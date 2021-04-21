---
title: Šifrování registru pomocí klíče spravovaného zákazníkem
description: Přečtěte si o šifrování v klidovém prostředí služby Azure Container registry a o tom, jak zašifrovat registr Premium pomocí klíče spravovaného zákazníkem, který je uložený v Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: 9ec32e32d187a3db07f023c78efbd301ef578cbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817030"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Šifrování registru pomocí klíče spravovaného zákazníkem

Když ukládáte image a jiné artefakty do služby Azure Container Registry, Azure automaticky zašifruje obsah registru v klidovém formátu pomocí [klíčů spravovaných službou](../security/fundamentals/encryption-models.md). Výchozí šifrování můžete doplnit další vrstvou šifrování pomocí klíče, který vytvoříte a spravujete v Azure Key Vault (klíč spravovaný zákazníkem). Tento článek vás provede jednotlivými kroky při použití rozhraní příkazového řádku Azure CLI, Azure Portal nebo šablony Správce prostředků.

Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem je podporováno prostřednictvím integrace s [Azure Key Vault](../key-vault/general/overview.md): 

* Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. 
* Pomocí Azure Key Vault můžete také auditovat použití klíče.
* Azure Container Registry podporuje automatické otočení šifrovacích klíčů registru, když je v Azure Key Vault k dispozici nová verze klíče. Šifrovací klíče registru můžete také ručně otočit.

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).


## <a name="things-to-know"></a>Co je potřeba vědět

* Klíč spravovaný zákazníkem teď můžete povolit jenom v případě, že vytváříte registr. Při povolování klíče nakonfigurujete spravovanou identitu *přiřazenou uživatelem* pro přístup k trezoru klíčů.
* Po povolení šifrování pomocí klíče spravovaného zákazníkem v registru nelze šifrování zakázat.  
* Azure Container Registry podporuje pouze klíče RSA nebo RSA-HSM. Klíče eliptické křivky nejsou aktuálně podporovány.
* V registru zašifrovaném pomocí klíče spravovaného zákazníkem v současné době není [vztah důvěryhodnosti obsahu](container-registry-content-trust.md) podporován.
* V registru zašifrovaném pomocí klíče spravovaného zákazníkem jsou v současné době uchovávány protokoly pro [úlohy ACR](container-registry-tasks-overview.md) jenom na 24 hodin. Pokud potřebujete uchovat protokoly po delší dobu, přečtěte si téma pokyny k [exportu a ukládání protokolů spuštění úloh](container-registry-tasks-logs.md#alternative-log-storage).


> [!IMPORTANT]
> Pokud máte v plánu uložit šifrovací klíč registru do existujícího trezoru klíčů Azure, který odepře veřejný přístup a povoluje jenom privátní koncový bod nebo vybrané virtuální sítě, je potřeba provést další kroky konfigurace. Viz [Rozšířený scénář: Key Vault firewall](#advanced-scenario-key-vault-firewall) v tomto článku.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatická nebo ruční aktualizace verzí klíčů

Důležitým aspektem zabezpečení registru zašifrovaného s klíčem spravovaným zákazníkem je postup, jak často aktualizujete (otočíte) šifrovací klíč. Vaše organizace může mít zásady dodržování předpisů, které vyžadují pravidelné aktualizace [verzí](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) klíčů uložených v Azure Key Vault při použití jako klíčů spravovaných zákazníkem. 

Když konfigurujete šifrování registru pomocí klíče spravovaného zákazníkem, máte dvě možnosti aktualizace verze klíče používaného pro šifrování:

* **Automaticky aktualizovat** klíčovou verzi – Chcete-li automaticky aktualizovat klíč spravovaný zákazníkem, když je v Azure Key Vault k dispozici nová verze, vynechejte verzi klíče, pokud povolíte šifrování registru pomocí klíče spravovaného zákazníkem. Pokud je registr zašifrovaný pomocí klíče bez verze, Azure Container Registry pravidelně kontroluje Trezor klíčů pro novou verzi klíče a aktualizuje klíč spravovaný zákazníkem během 1 hodiny. Azure Container Registry automaticky používá nejnovější verzi klíče.

* **Ruční aktualizace verze klíče** – pro použití konkrétní verze klíče pro šifrování registru zadejte tuto verzi klíče, pokud povolíte šifrování registru s klíčem spravovaným zákazníkem. Pokud je registr zašifrovaný pomocí konkrétní verze klíče, Azure Container Registry používá tuto verzi pro šifrování, dokud ručně neotočíte klíč spravovaný zákazníkem.

Podrobnosti najdete v části [Výběr ID klíče s nebo bez verze klíče](#choose-key-id-with-or-without-key-version) a [verze aktualizace klíče](#update-key-version)dále v tomto článku.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kroky Azure CLI v tomto článku, potřebujete Azure CLI verze 2.2.0 nebo novější, nebo Azure Cloud Shell. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Povolení klíčového řádku spravovaného zákazníkem

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V případě potřeby pomocí příkazu [AZ Group Create][az-group-create] vytvořte skupinu prostředků pro vytvoření trezoru klíčů, registru kontejnerů a dalších požadovaných prostředků.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Pomocí příkazu [AZ identity Create][az-identity-create] vytvořte uživatelsky přiřazenou [identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) . Tuto identitu bude používat váš registr pro přístup ke službě Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Ve výstupu příkazu si poznamenejte následující hodnoty: `id` a `principalId` . Tyto hodnoty budete potřebovat v pozdějších krocích, abyste nakonfigurovali přístup k registru k trezoru klíčů.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Pro usnadnění uložte tyto hodnoty do proměnných prostředí:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

Vytvoření trezoru klíčů pomocí [AZ Key trezor Create][az-keyvault-create] k uložení klíče spravovaného zákazníkem pro šifrování registru. 

Ve výchozím nastavení se nastavení **obnovitelného odstranění** automaticky povolí v novém trezoru klíčů. Aby nedocházelo ke ztrátě dat způsobenému odstraněním nechtěného klíče nebo trezoru klíčů, povolte také nastavení **ochrany vyprázdnění** .

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Pro použití v pozdějších krocích Získejte ID prostředku trezoru klíčů:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Povolit přístup k trezoru klíčů

Nakonfigurujte zásady pro Trezor klíčů, aby k němu měl přístup identita. V následujícím příkazu [AZ webtrezor set-Policy][az-keyvault-set-policy] předáte ID objektu zabezpečení spravované identity, kterou jste vytvořili, uloženou dříve v proměnné prostředí. Nastavte klíčová oprávnění na **Get**, **unwrapKey** a **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Případně můžete použít [Azure RBAC pro Key Vault](../key-vault/general/rbac-guide.md) k přiřazení oprávnění identitě pro přístup k trezoru klíčů. Například přiřazení role šifrování Key Vault šifrovací služby k identitě přiřaďte pomocí příkazu [AZ role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Vytvořit klíč a získat ID klíče

Spuštěním příkazu [AZ Key trezor Key Create][az-keyvault-key-create] vytvořte klíč v trezoru klíčů.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Ve výstupu příkazu si poznamenejte ID klíče, `kid` . Toto ID použijete v dalším kroku:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Zvolit ID klíče s verzí klíče nebo bez něj

Pro přehlednost uložte formát, který zvolíte pro ID klíče v proměnné prostředí $keyID. Můžete použít ID klíče s verzí nebo klíčem bez verze.

#### <a name="manual-key-rotation---key-id-with-version"></a>Ruční střídání klíčů – ID klíče s verzí

Při použití k zašifrování registru pomocí klíče spravovaného zákazníkem povolí tento klíč pouze ruční otočení klíče v Azure Container Registry.

Tento příklad ukládá `kid` vlastnost klíče:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatické střídání klíčů – vynechání verze kódu 

Když se použije k zašifrování registru pomocí klíče spravovaného zákazníkem, povolí tento klíč automatické střídání klíčů, když se v Azure Key Vault zjistí nová verze klíče.

Tento příklad odebere verzi z `kid` vlastnosti klíče:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Vytvoření registru s klíčem spravovaným zákazníkem

Spuštěním příkazu [AZ ACR Create][az-acr-create] vytvořte registr ve vrstvě služeb Premium a povolte klíč spravovaný zákazníkem. Předejte spravované ID identity a ID klíče uložené dříve v proměnných prostředí:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Zobrazit stav šifrování

Pokud chcete zjistit, jestli je povolené šifrování registru s klíčem spravovaným zákazníkem, spusťte příkaz [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

V závislosti na klíči, který se používá k zašifrování registru, je výstup podobný tomuto:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status&quot;: &quot;enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Povolit zákaznickou správu klíčů – portál

### <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Vytvořte uživatelem přiřazenou [identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) v Azure Portal. Postup najdete v tématu [vytvoření identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Název identity použijete v pozdějších krocích.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Vytvoření uživatelsky přiřazené identity v Azure Portal":::

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

Postup vytvoření trezoru klíčů najdete v tématu [rychlý Start: vytvoření trezoru klíčů pomocí Azure Portal](../key-vault/general/quick-create-portal.md).

Při vytváření trezoru klíčů pro klíč spravovaný zákazníkem můžete na kartě **základy** povolit nastavení **ochrany vyprázdnění** . Toto nastavení pomáhá zabránit ztrátě dat způsobené náhodným odstraněním klíčů nebo trezoru klíčů.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Vytvoření trezoru klíčů v Azure Portal":::

### <a name="enable-key-vault-access"></a>Povolit přístup k trezoru klíčů

Nakonfigurujte zásady pro Trezor klíčů, aby k němu měl přístup identita.

1. Přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **zásady přístupu > + přidat zásady přístupu**.
1. Vyberte **klíčová oprávnění** a vyberte **získat**, **Rozbalit klíč** a **zabalit klíč**.
1. V části **Vybrat objekt zabezpečení** vyberte název prostředku spravované identity přiřazené uživatelem.  
1. Vyberte **Přidat** a pak vybrat **Uložit**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Vytvoření zásad přístupu trezoru klíčů":::

Případně můžete použít [Azure RBAC pro Key Vault](../key-vault/general/rbac-guide.md) k přiřazení oprávnění identitě pro přístup k trezoru klíčů. Přiřaďte k identitě například roli šifrování šifrovací služby Key Vault.

1. Přejděte do svého trezoru klíčů.
1. Vyberte **řízení přístupu (IAM)**  >  **+ Přidat**  >  **přiřazení role přidat**.
1. V okně **Přidat přiřazení role** :
    1. Vyberte **Key Vault role uživatele šifrování šifrovací služby** . 
    1. Přiřaďte přístup k **spravované identitě přiřazené uživateli**.
    1. Vyberte název prostředku vaší spravované identity přiřazené uživatelem a vyberte **Uložit**.

### <a name="create-key-optional"></a>Vytvořit klíč (volitelné)

Volitelně můžete vytvořit klíč v trezoru klíčů, který se použije k zašifrování registru. Pokud chcete vybrat konkrétní verzi klíče jako klíč spravovaný zákazníkem, postupujte podle těchto kroků. 

1. Přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **klíče**.
1. Vyberte **+ Generovat/importovat** a zadejte jedinečný název pro klíč.
1. Potvrďte zbývající výchozí hodnoty a vyberte **vytvořit**.
1. Po vytvoření vyberte klíč a pak vyberte aktuální verzi. Zkopírujte **identifikátor klíče** pro verzi klíče.

### <a name="create-azure-container-registry"></a>Vytvoření registru kontejneru Azure

1. Vyberte **vytvořit**  >  **kontejnery** prostředků  >  **Container Registry**.
1. Na kartě **základy** vyberte nebo vytvořte skupinu prostředků a zadejte název registru. V položce **SKU** vyberte **Premium**.
1. Na kartě **šifrování** v poli **klíč spravovaný zákazníkem** vyberte **povoleno**.
1. V části **Identita** vyberte spravovanou identitu, kterou jste vytvořili.
1. V části **šifrování** vyberte jednu z následujících možností:
    * Vyberte **vybrat z Key Vault** a vyberte existující Trezor klíčů a klíč, nebo **vytvořte nový**. Vybraný klíč není bez verzí a umožňuje automatické střídání klíčů.
    * Vyberte **zadat identifikátor URI klíče** a přímo zadejte identifikátor klíče. Můžete poskytnout buď identifikátor URI klíče s verzí (pro klíč, který se musí otočit ručně) nebo identifikátor URI nepoužívaného klíče (což umožňuje automatické střídání klíčů). 
1. Na kartě **šifrování** vyberte **zkontrolovat + vytvořit**.
1. Vyberte **vytvořit** a nasaďte instanci registru.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Vytvoření šifrovaného registru v Azure Portal":::

Pokud chcete zobrazit stav šifrování vašeho registru na portálu, přejděte k registru. V části **Nastavení** vyberte  **šifrování**.

## <a name="enable-customer-managed-key---template"></a>Povolit zákaznickou šablonu pro správu klíčů

Můžete také použít šablonu Správce prostředků k vytvoření registru a povolení šifrování pomocí klíče spravovaného zákazníkem.

Následující šablona vytvoří nový registr kontejneru a uživatelem přiřazenou spravovanou identitu. Zkopírujte následující obsah do nového souboru a uložte ho pomocí názvu souboru, například `CMKtemplate.json` .

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Postupujte podle kroků v předchozích částech a vytvořte následující prostředky:

* Trezor klíčů identifikovaný názvem
* Klíč trezoru klíčů identifikovaný ID klíče

Spusťte následující příkaz [AZ Deployment Group Create][az-deployment-group-create] a vytvořte registr pomocí předchozího souboru šablony. Tam, kde je uvedeno, zadejte nový název registru a název spravované identity a také název trezoru klíčů a ID klíče, který jste vytvořili.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Zobrazit stav šifrování

Chcete-li zobrazit stav šifrování registru, spusťte příkaz [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Použití registru

Po povolení klíče spravovaného zákazníkem v registru můžete provádět stejné operace v registru, které jste provedli v registru, který není zašifrovaný pomocí klíče spravovaného zákazníkem. Můžete například provést ověření pomocí registru a imagí Docker. Podívejte se na téma ukázkové příkazy při [vložení a načtení obrázku](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Otočit klíč

Aktualizujte verzi klíče v Azure Key Vault, nebo vytvořte nový klíč a pak aktualizujte registr tak, aby šifroval data pomocí klíče. Tyto kroky můžete provést pomocí rozhraní příkazového řádku Azure nebo na portálu.

Při otáčení klíče obvykle zadáte stejnou identitu, která se používá při vytváření registru. Volitelně můžete nakonfigurovat novou identitu přiřazenou uživatelem pro přístup k klíči nebo povolit a zadat identitu přiřazenou systémem v registru.

> [!NOTE]
> Zajistěte, aby byl pro identitu, kterou konfigurujete pro přístup k klíči, nastaven požadovaný [přístup k trezoru klíčů](#enable-key-vault-access) .

### <a name="update-key-version"></a>Aktualizovat verzi klíče

Běžným scénářem je aktualizace verze klíče používaného jako klíč spravovaný zákazníkem. V závislosti na konfiguraci šifrování registru se klíč spravovaný zákazníkem v Azure Container Registry automaticky aktualizuje nebo se musí aktualizovat ručně.

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ Key trezor Key][az-keyvault-key] vytvořte nebo spravujte klíče trezoru klíčů. Pokud chcete vytvořit novou verzi klíče, spusťte příkaz [AZ klíč trezoru Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Další krok závisí na konfiguraci šifrování registru:

* Pokud je registr nakonfigurovaný k detekci aktualizací verze klíče, klíč spravovaný zákazníkem se automaticky aktualizuje během 1 hodiny.

* Pokud je registr nakonfigurovaný tak, aby vyžadoval ruční aktualizaci nové verze klíče, spusťte příkaz [AZ ACR Encryption otočit-Key][az-acr-encryption-rotate-key] , který předává nové ID klíče a identitu, kterou chcete nakonfigurovat:

Ruční aktualizace verze klíče spravovaného zákazníkem:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Když spustíte aplikaci `az acr encryption rotate-key` , můžete předat ID klíče verze nebo ID klíče bez verze. Pokud použijete ID klíče bez verze, registr se pak nakonfiguruje tak, aby automaticky zjišťoval aktualizace novějších verzí klíčů.

### <a name="portal"></a>Portál

Nastavení **šifrování** registru použijte k aktualizaci trezoru klíčů, klíče nebo nastavení identity používaného pro klíč spravovaný zákazníkem.

Pokud například chcete nakonfigurovat nový klíč:

1. Na portálu přejděte do svého registru.
1. V části **Nastavení** vyberte   >  **klíč pro změnu** šifrování.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Otočit klíč v Azure Portal":::
1. V části **šifrování** vyberte jednu z následujících možností:
    * Vyberte **vybrat z Key Vault** a vyberte existující Trezor klíčů a klíč, nebo **vytvořte nový**. Vybraný klíč není bez verzí a umožňuje automatické střídání klíčů.
    * Vyberte **zadat identifikátor URI klíče** a přímo zadejte identifikátor klíče. Můžete poskytnout buď identifikátor URI klíče s verzí (pro klíč, který se musí otočit ručně) nebo identifikátor URI nepoužívaného klíče (což umožňuje automatické střídání klíčů).
1. Dokončete výběr klíče a vyberte **Uložit**.

## <a name="revoke-key"></a>Odvolat klíč

Odvolejte šifrovací klíč spravovaný zákazníkem změnou zásad přístupu nebo oprávnění k trezoru klíčů nebo odstraněním klíče. Pomocí příkazu [AZ klíčů trezor Delete-Policy][az-keyvault-delete-policy] můžete například změnit zásady přístupu spravované identity používané v registru:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Odvolání klíče efektivně zablokuje přístup ke všem datům registru, protože registr nemá přístup k šifrovacímu klíči. Pokud je povolený přístup ke klíči nebo dojde k obnovení odstraněného klíče, váš registr vybere klíč, abyste mohli znovu přistupovat k šifrovaným datům registru.

## <a name="advanced-scenario-key-vault-firewall"></a>Pokročilý scénář: Key Vault firewall

Šifrovací klíč můžete chtít uložit pomocí existujícího trezoru klíčů Azure nakonfigurovaného s [Key Vault bránou firewall](../key-vault/general/network-security.md), která odepře veřejný přístup a povoluje jenom privátní koncový bod nebo vybrané virtuální sítě. 

V tomto scénáři nejdřív vytvořte novou uživatelem přiřazenou identitu, Trezor klíčů a registr kontejnerů šifrovaný pomocí klíče spravovaného zákazníkem pomocí [Azure CLI](#enable-customer-managed-key---cli), [portálu](#enable-customer-managed-key---portal)nebo [šablony](#enable-customer-managed-key---template). Podrobné pokyny najdete v předchozích částech tohoto článku.
   > [!NOTE]
   > Nový trezor klíčů je nasazený mimo bránu firewall. Jenom dočasně se používá k uložení klíče spravovaného zákazníkem.

Po vytvoření registru pokračujte podle následujících pokynů. Podrobnosti jsou uvedeny v následujících částech.

1. Povolte identitu přiřazenou systémem v registru.
1. Udělte identitám přiřazeným systémem oprávnění k přístupu k klíčům v trezoru klíčů, která je omezená pomocí brány Key Vault firewall.
1. Ujistěte se, že brána firewall pro Key Vault umožňuje obejít důvěryhodné služby. Služba Azure Container Registry v současné době může bránu firewall obejít jenom při použití její systémové identity spravované. 
1. Pokud chcete klíč spravovaný zákazníkem otočit, vyberte ho v trezoru klíčů, který je omezený pomocí brány Key Vault firewall.
1. Pokud už je nepotřebujete, můžete odstranit Trezor klíčů, který jste vytvořili mimo bránu firewall.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Krok 1 – povolení identity přiřazené systémem v registru

1. Na portálu přejděte do svého registru.
1. Vyberte **Nastavení**  >   **Identita**.
1. V části **přiřazený systém** nastavte **stav** na **zapnuto**. Vyberte **Uložit**.
1. Zkopírujte **ID objektu** identity.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Krok 2 – udělení přístupu identitám přiřazeným systému k vašemu trezoru klíčů

1. Na portálu přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **zásady přístupu > + přidat zásady přístupu**.
1. Vyberte **klíčová oprávnění** a vyberte **získat**, **Rozbalit klíč** a **zabalit klíč**.
1. Zvolte **možnost vybrat objekt zabezpečení** a vyhledejte ID objektu spravované identity přiřazené systémem nebo název registru.  
1. Vyberte **Přidat** a pak vybrat **Uložit**.

### <a name="step-3---enable-key-vault-bypass"></a>Krok 3 – povolení obcházení trezoru klíčů

Pro přístup k trezoru klíčů nakonfigurovanému pomocí brány Key Vault firewall musí Registry obejít bránu firewall. Ujistěte se, že je Trezor klíčů nakonfigurovaný tak, aby povoloval přístup libovolné [důvěryhodné služby](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry je jednou z důvěryhodných služeb.

1. Na portálu přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **sítě**.
1. Potvrďte, aktualizujte nebo přidejte nastavení virtuální sítě. Podrobný postup najdete v tématu [konfigurace Azure Key Vault bran firewall a virtuálních sítí](../key-vault/general/network-security.md).
1. V nástroji **umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall**. Vyberte **Ano**. 

### <a name="step-4---rotate-the-customer-managed-key"></a>Krok 4 – otočení klíče spravovaného zákazníkem

Po dokončení předchozích kroků otáčejte na klíč, který je uložený v trezoru klíčů za bránou firewall.

1. Na portálu přejděte do svého registru.
1. V části **Nastavení** vyberte   >  **klíč pro změnu** šifrování.
1. V položce **Identita** vyberte **přiřazeno systému**.
1. Vyberte **vybrat z Key Vault** a vyberte název trezoru klíčů, který je za bránou firewall.
1. Vyberte existující klíč nebo **vytvořte nový**. Vybraný klíč není bez verzí a umožňuje automatické střídání klíčů.
1. Dokončete výběr klíče a vyberte **Uložit**.

## <a name="troubleshoot"></a>Řešení potíží

### <a name="removing-managed-identity"></a>Odebírá se spravovaná identita.


Pokud se pokusíte odebrat spravovanou identitu přiřazenou uživatelem nebo systémem z registru, který se používá ke konfiguraci šifrování, může se zobrazit chybová zpráva podobná této:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Nebudete také moci změnit (otočit) šifrovací klíč. Postup řešení závisí na typu identity, která se používá k šifrování.

**Identita přiřazená uživatelem**

Pokud k tomuto problému dochází s uživatelem přiřazenou identitou, nejdřív znovu přiřaďte identitu pomocí identifikátoru GUID zobrazeného v chybové zprávě. Například:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Po změně klíče a přiřazení jiné identity můžete odebrat původní identitu přiřazenou uživatelem.

**Identita přiřazená systémem**

Pokud k tomuto problému dochází s identitou přiřazenou systémem, [Vytvořte si lístek podpory Azure](https://azure.microsoft.com/support/create-ticket/) , který vám poskytne pomoc k obnovení identity.


## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [šifrování v klidovém umístění v Azure](../security/fundamentals/encryption-atrest.md).
* Přečtěte si další informace o zásadách přístupu a o tom, jak [zabezpečit přístup k trezoru klíčů](../key-vault/general/security-features.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
