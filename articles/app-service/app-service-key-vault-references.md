---
title: Odkazy na Key Vault – Azure App Service | Microsoft Docs
description: Koncepční referenční příručka a Průvodce nastavením pro Azure Key Vault odkazy v Azure App Service a Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 30bd7c68ae1c88aba288b515d0ec32581f90b868
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088185"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Použití Key Vault odkazů pro App Service a Azure Functions (Preview)

> [!NOTE] 
> Odkazy na Key Vault jsou momentálně ve verzi Preview.

V tomto tématu se dozvíte, jak pracovat s tajnými kódy z Azure Key Vault v App Service nebo Azure Functions aplikace bez nutnosti jakýchkoli změn kódu. [Azure Key Vault](../key-vault/key-vault-overview.md) je služba, která poskytuje centralizovanou správu tajných kódů s úplnou kontrolou zásad přístupu a historie auditu.

## <a name="granting-your-app-access-to-key-vault"></a>Udělení přístupu aplikace k Key Vault

Aby bylo možné číst tajné kódy z Key Vault, je nutné vytvořit trezor a udělit aplikaci oprávnění k přístupu.

1. Vytvořte Trezor klíčů pomocí [Key Vault rychlý Start](../key-vault/quick-create-cli.md).

1. Vytvořte [spravovanou identitu přiřazenou systémem](overview-managed-identity.md) pro vaši aplikaci.

   > [!NOTE] 
   > Odkazy na Key Vault aktuálně podporují pouze spravované identity přiřazené systémem. Uživatelsky přiřazené identity nelze použít.

1. Vytvořte [zásadu přístupu v Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) pro identitu aplikace, kterou jste vytvořili dříve. Povolit pro tuto zásadu oprávnění tajného klíče "získat". Nekonfigurujte "autorizovanou aplikaci" ani `applicationId` nastavení, protože to není kompatibilní se spravovanou identitou.

    Udělení přístupu k identitě aplikace v trezoru klíčů je operace jednorázová a zůstane stejná pro všechna předplatná Azure. Můžete ji použít k nasazení libovolných certifikátů, kolik chcete. 

## <a name="reference-syntax"></a>Referenční syntaxe

Odkaz na Key Vault je ve formátu `@Microsoft.KeyVault({referenceString})`, kde `{referenceString}` je nahrazen jednou z následujících možností:

> [!div class="mx-tdBreakAll"]
> | Řetězec odkazu                                                            | Popis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** by měl být úplný identifikátor URI datové roviny tajného klíče v Key Vault, včetně verze, např. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | Název **trezoru** by měl být název vašeho prostředku Key Vault. Název **tajného** kódu by měl být název cílového tajného klíče. **Verzetajnéhoklíče** by měla být verze tajného klíče, který se má použít. |

> [!NOTE] 
> V aktuální verzi Preview jsou vyžadovány verze. Při střídání tajných kódů bude nutné aktualizovat verzi v konfiguraci aplikace.

Například kompletní odkaz by vypadal jako následující:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Další možností:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Nastavení zdrojové aplikace z Key Vault

Odkazy na Key Vault lze použít jako hodnoty pro [nastavení aplikace](configure-common.md#configure-app-settings), což vám umožní zachovat tajné klíče v Key Vault namísto Konfigurace lokality. Nastavení aplikace jsou bezpečně šifrovaná v klidovém stavu, ale pokud potřebujete možnosti správy tajných klíčů, měli byste přejít do Key Vault.

Chcete-li použít odkaz Key Vault pro nastavení aplikace, nastavte odkaz jako hodnotu nastavení. Vaše aplikace může na tajný klíč odkazovat jako na klíč jako normální. Nevyžadují se žádné změny kódu.

> [!TIP]
> Většina nastavení aplikací pomocí Key Vault odkazů by měla být označená jako nastavení slotu, protože pro každé prostředí byste měli mít oddělené trezory.

### <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Při automatizaci nasazení prostředků prostřednictvím šablon Azure Resource Manager může být nutné pořadí závislostí v určitém pořadí, aby tato funkce fungovala. Všimněte si, že budete muset definovat nastavení aplikace jako vlastní prostředek, a ne použít `siteConfig` vlastnost v definici webu. Je to proto, že lokalita musí být definovaná jako první, aby se k ní vytvořila identita přiřazená systémem a mohla by se používat v zásadách přístupu.

Příklad psuedo-Template pro aplikaci Function App může vypadat takto:

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
> V tomto příkladu je nasazení správy zdrojů závislé na nastavení aplikace. To je obvykle nebezpečné chování, protože se aktualizace nastavení aplikace chová asynchronně. Protože však jsme zahrnuli `WEBSITE_ENABLE_SYNC_UPDATE_SITE` nastavení aplikace, aktualizace je synchronní. To znamená, že nasazení správy zdrojů bude zahájeno až po úplné aktualizaci nastavení aplikace.
