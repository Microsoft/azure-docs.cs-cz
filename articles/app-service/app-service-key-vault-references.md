---
title: Použití odkazů trezoru klíčů
description: Zjistěte, jak nastavit Azure App Service a Azure Functions pro použití odkazů azure key vaultu. Zpřístupněte tajnýkódy trezoru klíčů kódu aplikace.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: dd0a03ea76d517486bb9bda6d9628fb529166dd8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453723"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Použití odkazů trezoru klíčů pro službu App Service a funkce Azure

Toto téma ukazuje, jak pracovat s tajnými klíči z Azure Key Vault ve vaší aplikaci App Service nebo Azure Functions bez nutnosti jakýchkoli změn kódu. [Azure Key Vault](../key-vault/general/overview.md) je služba, která poskytuje centralizovanou správu tajných klíčů s plnou kontrolou nad zásadami přístupu a historií auditu.

## <a name="granting-your-app-access-to-key-vault"></a>Udělení přístupu aplikace k trezoru klíčů

Abyste mohli číst tajné klíče z trezoru klíčů, musíte mít vytvořený trezor a udělit aplikaci oprávnění k přístupu k němu.

1. Vytvořte trezor klíčů podle [rychlého startu trezoru klíčů](../key-vault/secrets/quick-create-cli.md).

1. Vytvořte [systémem přiřazenou spravovanou identitu](overview-managed-identity.md) pro vaši aplikaci.

   > [!NOTE] 
   > Odkazy na úložiště klíčů aktuálně podporují pouze spravované identity přiřazené systémem. Uživatelem přiřazené identity nelze použít.

1. Vytvořte [zásadu přístupu v trezoru klíčů](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) pro identitu aplikace, kterou jste vytvořili dříve. Povolte oprávnění tajného klíče "Získat" pro tuto zásadu. Nekonfigurujte "autorizovanou aplikaci" nebo `applicationId` nastavení, protože to není kompatibilní se spravovanou identitou.

    > [!NOTE]
    > Odkazy trezoru klíčů nejsou v současné době schopny vyřešit tajné klíče uložené v trezoru klíčů s [omezeními sítě](../key-vault/general/overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Syntaxe odkazu

Odkaz trezoru klíčů je `@Microsoft.KeyVault({referenceString})`formuláře `{referenceString}` , kde je nahrazen jednou z následujících možností:

> [!div class="mx-tdBreakAll"]
> | Referenční řetězec                                                            | Popis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_tajemstvíUri_                                                       | **SecretUri** by měl být úplný identifikátor URI datové roviny tajného klíče v trezoru klíčů, včetně verze, např.https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName =_název__ SecretName =_secretName_; SecretVersion =_secretVersion_ | **Název vaultu** by měl název prostředku trezoru klíčů. **SecretName** by měl být název cílového tajného klíče. **SecretVersion** by měla být verze tajného klíče použít. |

Úplný odkaz s version bude vypadat takto:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Máte k dispozici i další možnosti:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Nastavení zdrojové aplikace z trezoru klíčů

Odkazy na úložiště klíčů lze použít jako hodnoty pro [nastavení aplikace](configure-common.md#configure-app-settings), což umožňuje zachovat tajné kódy v trezoru klíčů namísto konfigurace webu. Nastavení aplikace jsou bezpečně šifrována v klidovém stavu, ale pokud potřebujete možnosti správy tajných kódů, měla by přejít do trezoru klíčů.

Chcete-li použít odkaz trezoru klíčů pro nastavení aplikace, nastavte odkaz jako hodnotu nastavení. Vaše aplikace může odkazovat na tajný klíč prostřednictvím svého klíče jako obvykle. Nejsou vyžadovány žádné změny kódu.

> [!TIP]
> Většina nastavení aplikací pomocí odkazů trezoru klíčů by měla být označena jako nastavení patice, protože byste měli mít samostatné trezory pro každé prostředí.

### <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Při automatizaci nasazení prostředků prostřednictvím šablon Azure Resource Manager, budete muset sekvenovat závislosti v určitém pořadí, aby tato funkce fungovala. Všimněte si, že budete muset definovat nastavení aplikace jako `siteConfig` vlastní prostředek, spíše než pomocí vlastnosti v definici webu. Důvodem je, že lokalita musí být definována jako první tak, aby systémpřiřazená identita byla vytvořena s ní a může být použita v zásadách přístupu.

Příklad psuedo-template pro aplikaci funkce může vypadat takto:

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
> V tomto příkladu závisí nasazení správy zdrojového kódu na nastavení aplikace. Obvykle se jedná o nebezpečné chování, protože aktualizace nastavení aplikace se chová asynchronně. Protože jsme však `WEBSITE_ENABLE_SYNC_UPDATE_SITE` zahrnuli nastavení aplikace, je aktualizace synchronní. To znamená, že nasazení správy zdrojového kódu začne až po úplné aktualizaci nastavení aplikace.

## <a name="troubleshooting-key-vault-references"></a>Poradce při potížích s odkazy na trezor klíčů

Pokud odkaz není správně vyřešen, bude použita referenční hodnota. To znamená, že pro nastavení aplikace by byla `@Microsoft.KeyVault(...)` vytvořena proměnná prostředí, jejíž hodnota má syntaxi. To může způsobit, že aplikace vyvolá chyby, protože očekávala tajný klíč určité struktury.

Nejčastěji je to způsobeno chybnou konfigurací [zásad přístupu trezoru klíčů](#granting-your-app-access-to-key-vault). Může však být také způsobeno tajným klíčem, který již neexistuje, nebo syntaktickou chybou v samotném odkazu.

Pokud je syntaxe správná, můžete zobrazit další příčiny chyby kontrolou aktuálního stavu rozlišení na portálu. Přejděte do nastavení aplikace a vyberte možnost "Upravit" pro daný odkaz. Pod konfigurací nastavení byste měli vidět informace o stavu, včetně případných chyb. Absence z nich znamená, že syntaxe odkazu je neplatná.

K získání dalších informací můžete také použít jeden z vestavěných detektorů.

### <a name="using-the-detector-for-app-service"></a>Používání detektoru pro službu App Service

1. Na portálu přejděte do aplikace.
2. Vyberte **Diagnostikovat a řešit problémy**.
3. Zvolte **Dostupnost a výkon** a vyberte **Webovou aplikaci dolů.**
4. Vyhledejte **diagnostiku nastavení aplikace trezoru klíčů** a klepněte na tlačítko **Další informace**.


### <a name="using-the-detector-for-azure-functions"></a>Použití detektoru pro funkce Azure

1. Na portálu přejděte do aplikace.
2. Přejděte na **funkce platformy.**
3. Vyberte **Diagnostikovat a řešit problémy**.
4. Zvolte **Dostupnost a výkon** a vyberte aplikace Funkce dolů nebo hlášení **chyb.**
5. Klikněte na **Diagnostika nastavení aplikace trezoru klíčů.**
