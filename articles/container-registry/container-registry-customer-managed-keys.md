---
title: Šifrování v klidovém případě pomocí klíče spravovaného zákazníkem
description: Přečtěte si o šifrování v klidovém prostředí služby Azure Container registry a o tom, jak zašifrovat registr Premium pomocí klíče spravovaného zákazníkem, který je uložený v Azure Key Vault
ms.topic: article
ms.date: 08/26/2020
ms.custom: ''
ms.openlocfilehash: 0e1810c8e3da334570dd1c4d6adb500e2cfa95e3
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487228"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Šifrování registru pomocí klíče spravovaného zákazníkem

Když ukládáte image a jiné artefakty do služby Azure Container Registry, Azure automaticky zašifruje obsah registru v klidovém formátu pomocí [klíčů spravovaných službou](../security/fundamentals/encryption-models.md). Výchozí šifrování můžete doplnit další vrstvou šifrování pomocí klíče, který vytvoříte a spravujete v Azure Key Vault. Tento článek vás provede jednotlivými kroky použití Azure CLI a Azure Portal.

Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem je podporováno prostřednictvím integrace s [Azure Key Vault](../key-vault/general/overview.md). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Pomocí Azure Key Vault můžete také auditovat použití klíče.

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).


## <a name="things-to-know"></a>Co je potřeba vědět

* Klíč spravovaný zákazníkem teď můžete povolit jenom v případě, že vytváříte registr. Při povolování klíče nakonfigurujete spravovanou identitu *přiřazenou uživatelem* pro přístup k trezoru klíčů.
* Po povolení šifrování pomocí klíče spravovaného zákazníkem v registru nelze šifrování zakázat.  
* V registru zašifrovaném pomocí klíče spravovaného zákazníkem v současné době není [vztah důvěryhodnosti obsahu](container-registry-content-trust.md) podporován.
* V registru zašifrovaném pomocí klíče spravovaného zákazníkem jsou v současné době uchovávány protokoly pro [úlohy ACR](container-registry-tasks-overview.md) jenom na 24 hodin. Pokud potřebujete uchovat protokoly po delší dobu, přečtěte si téma pokyny k [exportu a ukládání protokolů spuštění úloh](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Pokud je přístup k trezoru klíčů Azure omezený pomocí virtuální sítě s [bránou Key Vault firewall](../key-vault/general/network-security.md), je potřeba provést další kroky konfigurace. Po vytvoření registru a povolení klíče spravovaného zákazníkem nastavte přístup k tomuto klíči pomocí spravované identity *přiřazené systémem* v registru a nakonfigurujete registr tak, aby vynechal bránu Key Vault firewall. Postupujte podle kroků v tomto článku a povolte šifrování pomocí klíče spravovaného zákazníkem a pak se podívejte na pokyny pro [pokročilý scénář: Key Vault firewall](#advanced-scenario-key-vault-firewall) dále v tomto článku.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít kroky Azure CLI v tomto článku, potřebujete Azure CLI verze 2.2.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

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

Aby nedošlo ke ztrátě dat způsobené náhodným odstraněním klíčů nebo trezoru klíčů, je nutné povolit následující nastavení: **tichá odstranění** a **ochrana vyprázdnění**. Následující příklad obsahuje parametry pro tato nastavení:

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Přidat zásady přístupu trezoru klíčů

Nakonfigurujte zásady pro Trezor klíčů, aby k němu měl přístup identita. V následujícím příkazu [AZ webtrezor set-Policy][az-keyvault-set-policy] předáte ID objektu zabezpečení spravované identity, kterou jste vytvořili, uloženou dříve v proměnné prostředí. Nastavte klíčová oprávnění na **Get**, **unwrapKey**a **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
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
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Pro usnadnění uložte tuto hodnotu do proměnné prostředí:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Vytvoření registru s klíčem spravovaným zákazníkem

Spuštěním příkazu [AZ ACR Create][az-acr-create] vytvořte registr ve vrstvě služeb Premium a povolte klíč spravovaný zákazníkem. Předejte spravované ID objektu zabezpečení identity a ID klíče uložené dříve v proměnných prostředí:

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

Výstup se podobá tomuto:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Povolit zákaznickou správu klíčů – portál

### <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Vytvořte uživatelem přiřazenou [identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) v Azure Portal. Postup najdete v tématu [vytvoření identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Název identity použijete v pozdějších krocích.

![Vytvoření spravované identity přiřazené uživatelem v Azure Portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

Postup vytvoření trezoru klíčů najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../key-vault/secrets/quick-create-portal.md).

Při vytváření trezoru klíčů pro klíč spravovaný zákazníkem můžete na kartě **základy** povolit následující nastavení ochrany: **obnovitelné odstranění** a **vyprázdnit ochranu**. Tato nastavení pomůžou zabránit ztrátě dat způsobené náhodným odstraněním klíčů nebo trezoru klíčů.

![Vytvoření trezoru klíčů v Azure Portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Přidat zásady přístupu trezoru klíčů

Nakonfigurujte zásady pro Trezor klíčů, aby k němu měl přístup identita.

1. Přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **zásady přístupu > + přidat zásady přístupu**.
1. Vyberte **klíčová oprávnění**a vyberte **získat**, **Rozbalit klíč**a **zabalit klíč**.
1. Vyberte **Vybrat objekt zabezpečení** a vyberte název prostředku spravované identity přiřazené uživatelem.  
1. Vyberte **Přidat**a pak vybrat **Uložit**.

![Vytvoření zásad přístupu trezoru klíčů](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Vytvořit klíč

1. Přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **klíče**.
1. Vyberte **+ Generovat/importovat** a zadejte jedinečný název pro klíč.
1. Potvrďte zbývající výchozí hodnoty a vyberte **vytvořit**.
1. Po vytvoření vyberte klíč a poznamenejte si aktuální verzi klíče.

### <a name="create-azure-container-registry"></a>Vytvoření registru kontejneru Azure

1. Vyberte **vytvořit**  >  **kontejnery**prostředků  >  **Container Registry**.
1. Na kartě **základy** vyberte nebo vytvořte skupinu prostředků a zadejte název registru. V položce **SKU**vyberte **Premium**.
1. Na kartě **šifrování** v poli **klíč spravovaný zákazníkem**vyberte **povoleno**.
1. V části **Identita**vyberte spravovanou identitu, kterou jste vytvořili.
1. V případě **šifrování**vyberte **vybrat z Key Vault**.
1. V okně **Vybrat klíč z Azure Key Vault** vyberte Trezor klíčů, klíč a verzi, které jste vytvořili v předchozí části.
1. Na kartě **šifrování** vyberte **zkontrolovat + vytvořit**.
1. Vyberte **vytvořit** a nasaďte instanci registru.

![Vytvoření registru kontejneru v Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Pokud chcete zobrazit stav šifrování vašeho registru na portálu, přejděte k registru. V části **Nastavení**vyberte  **šifrování**.

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

Spuštěním následujícího příkazu [AZ Group Deployment Create][az-group-deployment-create] vytvořte registr pomocí předchozího souboru šablony. Tam, kde je uvedeno, zadejte nový název registru a název spravované identity a také název trezoru klíčů a ID klíče, který jste vytvořili.

```bash
az group deployment create \
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

V souladu se zásadami dodržování předpisů otočte klíč spravovaný zákazníkem, který se používá pro šifrování registru. Vytvořte nový klíč, nebo aktualizujte verzi klíče a pak aktualizujte registr tak, aby šifroval data pomocí klíče. Tyto kroky můžete provést pomocí rozhraní příkazového řádku Azure nebo na portálu.

Při otáčení klíče obvykle zadáte stejnou identitu, která se používá při vytváření registru. Volitelně můžete nakonfigurovat novou identitu přiřazenou uživatelem pro přístup k klíči nebo povolit a zadat identitu přiřazenou systémem v registru.

> [!NOTE]
> Zajistěte, aby byla pro identitu, kterou konfigurujete pro přístup k klíči, nastavena požadovaná [zásada přístupu trezoru klíčů](#add-key-vault-access-policy) .

### <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ Key trezor Key][az-keyvault-key] vytvořte nebo spravujte klíče trezoru klíčů. Pokud například chcete vytvořit novou verzi klíče nebo klíč, spusťte příkaz [AZ Key trezor Key Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name>
```

Pak spusťte příkaz [AZ ACR Encryption otočit-Key][az-acr-encryption-rotate-key] s předáním nového ID klíče a identity, kterou chcete nakonfigurovat:

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

### <a name="portal"></a>Portál

Pomocí nastavení **šifrování** v registru můžete aktualizovat klíčovou verzi, klíč, Trezor klíčů nebo nastavení identity, která se používají pro klíč spravovaný zákazníkem.

Chcete-li například vygenerovat a nakonfigurovat novou verzi klíče:

1. Na portálu přejděte do svého registru.
1. V části **Nastavení**vyberte **Encryption**  >  **klíč pro změnu**šifrování.
1. Vyberte **Vybrat klíč** .

    ![Otočit klíč v Azure Portal](./media/container-registry-customer-managed-keys/rotate-key.png)
1. V okně **Vybrat klíč z Azure Key Vault** vyberte Trezor klíčů a klíč, který jste nakonfigurovali dříve, a v části **verze**vyberte **vytvořit novou**.
1. V okně **vytvořit klíč** vyberte **Generovat**a pak **vytvořit**.
1. Dokončete výběr klíče a vyberte **Uložit**.

## <a name="revoke-key"></a>Odvolat klíč

Odvolejte šifrovací klíč spravovaný zákazníkem změnou zásad přístupu pro Trezor klíčů nebo odstraněním klíče. Pomocí příkazu [AZ klíčů trezor Delete-Policy][az-keyvault-delete-policy] můžete například změnit zásady přístupu spravované identity používané v registru:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Odvolání klíče efektivně zablokuje přístup ke všem datům registru, protože registr nemá přístup k šifrovacímu klíči. Pokud je povolený přístup ke klíči nebo dojde k obnovení odstraněného klíče, váš registr vybere klíč, abyste mohli znovu přistupovat k šifrovaným datům registru.

## <a name="advanced-scenario-key-vault-firewall"></a>Pokročilý scénář: Key Vault firewall

Pokud je váš Trezor klíčů Azure nasazený ve virtuální síti s bránou Key Vault firewall, proveďte následující další kroky po povolení šifrování klíče spravovaného zákazníkem v registru.

1. Konfigurace šifrování registru pro použití identity přiřazené systémem v registru
1. Povolení obejít Key Vault brány firewall v registru
1. Otočit klíč spravovaný zákazníkem

### <a name="configure-system-assigned-identity"></a>Konfigurace identity přiřazené systémem

Pro přístup k trezoru klíčů pro šifrovací klíče můžete nakonfigurovat spravovanou identitu přiřazenou systémem registru. Pokud si nejste obeznámeni s různými spravovanými identitami pro prostředky Azure, přečtěte si [Přehled](../active-directory/managed-identities-azure-resources/overview.md).

Povolení identity přiřazené systémem v registru na portálu:

1. Na portálu přejděte do svého registru.
1. Vyberte **Nastavení**  >   **Identita**.
1. V části **přiřazený systém**nastavte **stav** na **zapnuto**. Vyberte **Uložit**.
1. Zkopírujte **ID objektu** identity.

Pokud chcete identitě udělit přístup k vašemu trezoru klíčů:

1. Přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **zásady přístupu > + přidat zásady přístupu**.
1. Vyberte **klíčová oprávnění**a vyberte **získat**, **Rozbalit klíč**a **zabalit klíč**.
1. Vyberte **Vybrat objekt zabezpečení** a vyhledejte ID objektu spravované identity přiřazené systémem nebo název registru.  
1. Vyberte **Přidat**a pak vybrat **Uložit**.

Aktualizace nastavení šifrování registru pro použití identity:

1. Na portálu přejděte do svého registru.
1. V části **Nastavení**vyberte **Encryption**  >  **klíč pro změnu**šifrování.
1. V položce **Identita**vyberte **přiřazeno systému**a vyberte **Uložit**.

### <a name="enable-key-vault-bypass"></a>Povolit obcházení trezoru klíčů

Pro přístup k trezoru klíčů nakonfigurovanému pomocí brány Key Vault firewall musí Registry obejít bránu firewall. Nakonfigurujte Trezor klíčů tak, aby povoloval přístup libovolné [důvěryhodné služby](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry je jednou z důvěryhodných služeb.

1. Na portálu přejděte do svého trezoru klíčů.
1. Vyberte **Nastavení**  >  **sítě**.
1. Potvrďte, aktualizujte nebo přidejte nastavení virtuální sítě. Podrobný postup najdete v tématu [konfigurace Azure Key Vault bran firewall a virtuálních sítí](../key-vault/general/network-security.md).
1. V nástroji **umožňuje důvěryhodným službám Microsoftu obejít tuto bránu firewall**. Vyberte **Ano**. 

### <a name="rotate-the-customer-managed-key"></a>Otočit klíč spravovaný zákazníkem

Po dokončení předchozích kroků otočte klíč k novému klíči v trezoru klíčů za bránou firewall. Postup najdete v tématu [otočení klíče](#rotate-key) v tomto článku.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [šifrování v klidovém umístění v Azure](../security/fundamentals/encryption-atrest.md).
* Přečtěte si další informace o zásadách přístupu a o tom, jak [zabezpečit přístup k trezoru klíčů](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
