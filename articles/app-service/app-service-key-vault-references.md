---
title: Odkazy na služby Key Vault ve službě App Service a Azure Functions | Dokumentace Microsoftu
description: Koncepční Průvodce odkaz a instalační program pro odkazy na služby Azure Key Vault ve službě Azure App Service a Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.openlocfilehash: 6f7a05638e9893c989276c61355a301e4a67a6ed
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52449922"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Použití služby Key Vault odkazy pro App Service a Azure Functions (preview)

> [!NOTE] 
> Odkazy služby Key Vault je aktuálně ve verzi preview.

V tomto tématu se dozvíte, jak pracovat s tajnými kódy ze služby Azure Key Vault ve vaší aplikaci služby App Service nebo Azure Functions bez nutnosti změny kódu. [Služba Azure Key Vault](../key-vault/key-vault-overview.md) je služba, která poskytuje správu centralizované tajné kódy s úplnou kontrolou nad historie zásad a auditování přístupu.

## <a name="granting-your-app-access-to-key-vault"></a>Uděluje přístup k vaší aplikaci do služby Key Vault

Za účelem čtení tajných kódů z trezoru klíčů, budete muset vytvořit trezor vytvoří a udělili oprávnění aplikace k němu přistupovat.

1. Vytvoření trezoru klíčů pomocí následujících [rychlý start služby Key Vault](../key-vault/quick-create-cli.md).

1. Vytvoření [systém přiřadil se identita spravované](app-service-managed-service-identity.md) pro vaši aplikaci.

   > [!NOTE] 
   > Key Vault odkazuje na aktuálně jenom podporu systém přiřadil spravovaných identit. Uživatelsky přiřazené identity nelze použít.

1. Vytvoření [zásady ve službě Key Vault přístupu](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) pro identitu aplikací, které jste vytvořili dříve. Povolte oprávnění tajného kódu "Get" na tuto zásadu.

## <a name="reference-syntax"></a>Odkaz syntaxe

Referenční dokumentace služby Key Vault je ve formátu `@Microsoft.KeyVault({referenceString})`, kde `{referenceString}` nahrazuje jeden z následujících možností:

> [!div class="mx-tdBreakAll"]
> | Řetězec odkazu                                                            | Popis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_secretUri_                                                       | **SecretUri** by měla být úplná rovina dat identifikátor URI tajného klíče ve službě Key Vault, včetně verze, například https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName =_vaultName_; SecretName =_secretName_; SecretVersion =_secretVersion_ | **VaultName** by měl název prostředku služby Key Vault. **SecretName** by měl být název cílového tajného kódu. **SecretVersion** by měla mít verzi tajného klíče používat. |

> [!NOTE] 
> V aktuální verzi preview verze jsou povinné. Při obměně tajné kódy, je potřeba aktualizovat verzi konfigurace vaší aplikace.

Úplný popis by například vypadat nějak takto:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Další možností:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Nastavení zdroje aplikace z trezoru klíčů

Odkazy na služby Key Vault můžete použít jako hodnoty pro [nastavení aplikace](web-sites-configure.md#app-settings), díky tomu můžete zachovat tajné kódy ve službě Key Vault místo konfiguraci webu. Nastavení aplikace jsou bezpečně v klidovém stavu zašifrovaná, ale pokud potřebujete možnosti správy tajných kódů, by měly patřit do služby Key Vault.

Použití služby Key Vault odkaz pro nastavení aplikace, nastavte odkaz jako hodnotu daného nastavení. Vaše aplikace může odkazovat na tajný kód prostřednictvím jeho klíč jako za normálních okolností. Nejsou potřeba žádné změny kódu.

> [!TIP]
> Většina nastavení aplikace pomocí služby Key Vault odkazy musí být označené jako nastavení slotu, jako byste měli mít samostatné úložišť pro každé prostředí.

### <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Při automatizaci nasazení prostředků pomocí šablon Azure Resource Manageru, budete muset pořadí závislostí v určitém pořadí, aby tato funkce fungovat. Poznámky, budete muset definovat nastavení aplikace jako vlastní prostředek, místo použití `siteConfig` vlastnost v definici lokality. Je to proto, že web je potřeba nejdřív definovat tak, aby systém přiřadil identity se vytvoří s ním a je možné v zásadách přístupu.

Příklad psuedo – šablona pro aplikaci function app může vypadat nějak takto:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> V tomto příkladu nasazení zdrojový ovládací prvek závisí na nastavení aplikace. Toto je obvykle nebezpečné chování, jak aktualizovat nastavení aplikace chová asynchronně. Ale vzhledem k tomu, že jsme zahrnuli `WEBSITE_ENABLE_SYNC_UPDATE_SITE` aplikace nastavení, aktualizace je synchronní. To znamená, že nasazení ovládacího prvku zdroje budou pouze obnoveny, jakmile úplně aktualizovala nastavení aplikace.
