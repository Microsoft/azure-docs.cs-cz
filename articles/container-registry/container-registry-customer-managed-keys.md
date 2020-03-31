---
title: Šifrování v klidovém stavu pomocí klíčů spravovaných zákazníkem
description: Informace o šifrování po zbytek registru kontejnerů Azure a o šifrování registru pomocí klíče spravovaného zákazníkem uloženého v úložišti klíčů Azure
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498947"
---
# <a name="encryption-using-customer-managed-keys"></a>Šifrování pomocí klíčů spravovaných zákazníkem

Když ukládáte ibi a další artefakty v registru kontejnerů Azure, Azure automaticky zašifruje obsah registru v klidovém stavu pomocí [klíčů spravovaných službou](../security/fundamentals/encryption-atrest.md#data-encryption-models). Výchozí šifrování můžete doplnit o další vrstvu šifrování pomocí klíče, který vytvoříte a spravujete v azure key vaultu. Tento článek vás provede kroky pomocí Azure CLI a portálu Azure.

Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem je podporováno prostřednictvím integrace s [trezorem klíčů Azure](../key-vault/key-vault-overview.md). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault je API pro generování šifrovacích klíčů. Pomocí služby Azure Key Vault můžete také auditovat využití klíčů.

Tato funkce je k dispozici ve vrstvě služby registru **kontejneru Premium.** Informace o vrstvách a limitech služby registru naleznete v tématu [Azure Container Registry SKU](container-registry-skus.md).

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí určitá [omezení.](#preview-limitations) Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.
>
   
## <a name="preview-limitations"></a>Omezení náhledu 

* Tuto funkci lze aktuálně povolit pouze při vytváření registru.
* Po povolení klíče spravovaného zákazníkem v registru jej nelze zakázat.
* [Vztah důvěryhodnosti obsahu](container-registry-content-trust.md) není aktuálně podporován v registru zašifrovaném klíčem spravovaným zákazníkem.
* V registru zašifrovaném klíčem spravovaným zákazníkem jsou protokoly spuštění [úloh ACR](container-registry-tasks-overview.md) aktuálně uchovávány pouze 24 hodin. Pokud potřebujete uchovávat protokoly po delší dobu, naleznete v pokynech k [exportu a ukládání protokolů spuštění úloh](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Požadavky

Chcete-li použít kroky azure cli v tomto článku, budete potřebovat Azure CLI verze 2.2.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Povolení klíče spravovaného zákazníkem – cli

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V případě potřeby spusťte příkaz [az group create][az-group-create] a vytvořte skupinu prostředků pro vytvoření trezoru klíčů, registru kontejnerů a dalších požadovaných prostředků.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte uživatelem přiřazenou [spravovanou identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) pomocí příkazu [az identity create.][az-identity-create] Tato identita bude použita registrem pro přístup ke službě Trezor klíčů.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Ve výstupu příkazu poznamenejte `id` si `principalId`následující hodnoty: a . Tyto hodnoty potřebujete v pozdějších krocích ke konfiguraci přístupu registru k trezoru klíčů.

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

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Vytvořte trezor klíčů s [vytvořením trezoru az keyvault][az-keyvault-create] pro uložení klíče spravovaného zákazníkem pro šifrování registru. 

Chcete-li zabránit ztrátě dat způsobené náhodným odstraněním klíčů nebo trezoru klíčů, je nutné povolit následující nastavení: **Ochrana proti odstranění a** **odstranění**pomocí programu Soft Delete . Následující příklad obsahuje parametry pro tato nastavení: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Přidání zásad přístupu k trezoru klíčů

Nakonfigurujte zásadu pro trezor klíčů tak, aby k němu mohla přistupovat identita. V následujícím příkazu [az keyvault set-policy][az-keyvault-set-policy] předáte ID jistiny spravované identity, kterou jste vytvořili, která byla dříve uložena v proměnné prostředí. Nastavte oprávnění klíče k **získání**, **unwrapKey**a **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Vytvořit klíč a získat ID klíče

Spusťte příkaz [az keyvault vytvořit][az-keyvault-key-create] příkaz pro vytvoření klíče v trezoru klíčů.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Ve výstupu příkazu poznamenejte si ID klíče . `kid` Toto ID použijete v dalším kroku:

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
Pro pohodlí uložte tuto hodnotu do proměnné prostředí:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Vytvoření registru s klíčem spravovaným zákazníkem

Spusťte příkaz [az acr create][az-acr-create] a vytvořte registr a povolte klíč spravovaný zákazníkem. Předajte ID objektu zabezpečení spravované identity a ID klíče uložené dříve v proměnných prostředí:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Zobrazit stav šifrování

Chcete-li zobrazit, zda je povoleno šifrování registru pomocí klíče spravovaného zákazníkem, spusťte příkaz [az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Povolit klíč spravovaný zákazníkem – portál

### <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Vytvořte uživatelem přiřazenou [spravovanou identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) na webu Azure Portal. Postup najdete [v tématu Vytvoření identity přiřazené uživateli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

**Poznamenejte** si název prostředku spravované identity. Tento název potřebujete v pozdějších krocích.

![Vytvoření spravované identity přiřazené uživateli na webu Azure Portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Postup vytvoření trezoru klíčů najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z Azure Key Vault pomocí portálu Azure](../key-vault/quick-create-portal.md).

Při vytváření trezoru klíčů pro klíč spravovaný zákazníkem musíte na kartě **Základy** povolit následující nastavení ochrany: **Ochrana proti odstranění a** **vymazání**. Tato nastavení pomáhají zabránit ztrátě dat způsobené náhodným odstraněním klíče nebo trezoru klíčů.

![Vytvoření trezoru klíčů na webu Azure Portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Přidání zásad přístupu k trezoru klíčů

Nakonfigurujte zásadu pro trezor klíčů tak, aby k němu mohla přistupovat identita.

1. Přejděte do trezoru klíčů.
1. Vyberte **možnost Nastavení** > **Zásady přístupu > +Přidat zásady přístupu**.
1. Vyberte **Klíčová oprávnění**a vyberte **Získat**, **Rozbalit klíč**a **Zalamovat klíč**.
1. Vyberte **Vybrat objekt zabezpečení** a vyberte název prostředku spravované identity přiřazené uživateli.  
1. Vyberte **Přidat**, pak vyberte **Uložit**.

![Vytvořit zásady přístupu k trezoru klíčů](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Vytvořit klíč

1. Přejděte do trezoru klíčů.
1. Vyberte **položku Nastavení** > **kláves**.
1. Vyberte **+Generovat/Importovat** a zadejte jedinečný název klíče.
1. Přijměte zbývající výchozí hodnoty a vyberte **Vytvořit**.
1. Po vytvoření vyberte klíč a poznamenejte si aktuální verzi klíče.

### <a name="create-azure-container-registry"></a>Vytvoření registru kontejneru Azure

1. Vyberte **možnost Vytvořit** > **registr kontejnerů****prostředků** > .
1. Na kartě **Základy** vyberte nebo vytvořte skupinu prostředků a zadejte název registru. Ve **skladové jednotce**vyberte **možnost Premium**.
1. Na kartě **Šifrování** vyberte v **klíči spravovaném zákazníkem** **možnost Povoleno**.
1. V **části Identita**vyberte spravovanou identitu, kterou jste vytvořili.
1. V **pouzdvzrovacím klíči**vyberte **vybrat z trezoru klíčů**.
1. V okně **Vybrat klíč z trezoru klíčů Azure** vyberte trezor klíčů, klíč a verzi, kterou jste vytvořili v předchozí části.
1. Na kartě **Šifrování** vyberte **Zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit,** chcete-li nasadit instanci registru.

![Vytvoření registru kontejnerů na webu Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Chcete-li zobrazit stav šifrování registru na portálu, přejděte do registru. V části **Nastavení**vyberte **Šifrování (Náhled).**

## <a name="enable-customer-managed-key---template"></a>Povolit klíč spravovaný zákazníkem – šablona

Šablonu Správce prostředků můžete také použít k vytvoření registru a povolení šifrování pomocí klíče spravovaného zákazníkem. 

Následující šablona vytvoří nový registr kontejneru a uživatelem přiřazenou spravovanou identitu. Zkopírujte následující obsah do nového souboru a `CMKtemplate.json`uložte jej pomocí názvu souboru, například .

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

Podle pokynů v předchozích částech vytvořte následující zdroje:

* Trezor klíčů, označený názvem
* Klíč trezoru klíčů, identifikovaný ID klíče

Spusťte následující příkaz [pro vytvoření nasazení skupiny az][az-group-deployment-create] a vytvořte registr pomocí předchozího souboru šablony. Tam, kde je uvedeno, zadejte nový název registru a název spravované identity, stejně jako název trezoru klíčů a ID klíče, které jste vytvořili. 

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

Chcete-li zobrazit stav šifrování registru, spusťte příkaz [az acr encryption show-status][az-acr-encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Použití registru

Po povolení registru k šifrování dat pomocí klíče spravovaného zákazníkem můžete provádět stejné operace registru, které provádíte v registru, který není šifrován pomocí klíče spravovaného zákazníkem. Můžete například ověřit pomocí registru a nabízených iimages Dockeru. Viz příkladpříkazů v [tématu Push and pull a image](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Otočit klávesu

Klíč spravovaný zákazníkem můžete v trezoru klíčů Azure v souladu s vašimi zásadami dodržování předpisů otočit. Vytvořte nový klíč a aktualizujte registr tak, aby šifrovali data pomocí nového klíče. Tyto kroky můžete provést pomocí příkazového příkazu k onomu Azure nebo na portálu.

Spusťte například příkaz [az keyvault create][az-keyvault-key-create] a vytvořte nový klíč:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Potom spusťte příkaz [az acr šifrování rotate-key][az-acr-encryption-rotate-key] a předajte nové ID klíče a hlavní ID spravované identity, kterou jste dříve nakonfigurovali:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Odvolat klíč

Odvolat šifrovací klíč spravovaný zákazníkem změnou zásad přístupu v trezoru klíčů nebo odstraněním klíče. Pomocí příkazu [delete-policy pomocí příkazu az keyvault změňte][az-keyvault-delete-policy] zásady přístupu spravované identity používané registrem. Například:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Zrušení klíče účinně blokuje přístup ke všem datům registru, protože registr nemá přístup k šifrovacímu klíči. Pokud je povolen přístup ke klíči nebo je odstraněný klíč obnoven, registr jej vybere, abyste měli opět přístup k zašifrovaným datům registru.

## <a name="next-steps"></a>Další kroky

* Další informace o [šifrování v klidovém stavu v Azure](../security/fundamentals/encryption-atrest.md).
* Přečtěte si další informace o zásadách přístupu a [o zabezpečení přístupu k trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md).
* Pokud chcete poskytnout zpětnou vazbu ke klíčům spravovaným zákazníkem pro Azure Container Registry, navštivte [web ACR GitHub](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
