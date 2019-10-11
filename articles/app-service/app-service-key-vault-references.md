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
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 49bf7984efe74edd2a19909509e0c6b9564fc2e9
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274427"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Použití Key Vault odkazů pro App Service a Azure Functions

> [!NOTE] 
> Odkazy na Key Vault nejsou aktuálně k dispozici v plánech spotřeby Linux.

V tomto tématu se dozvíte, jak pracovat s tajnými kódy z Azure Key Vault v App Service nebo Azure Functions aplikace bez nutnosti jakýchkoli změn kódu. [Azure Key Vault](../key-vault/key-vault-overview.md) je služba, která poskytuje centralizovanou správu tajných kódů s úplnou kontrolou zásad přístupu a historie auditu.

## <a name="granting-your-app-access-to-key-vault"></a>Udělení přístupu aplikace k Key Vault

Aby bylo možné číst tajné kódy z Key Vault, je nutné vytvořit trezor a udělit aplikaci oprávnění k přístupu.

1. Vytvořte Trezor klíčů pomocí [Key Vault rychlý Start](../key-vault/quick-create-cli.md).

1. Vytvořte [spravovanou identitu přiřazenou systémem](overview-managed-identity.md) pro vaši aplikaci.

   > [!NOTE] 
   > Odkazy na Key Vault aktuálně podporují pouze spravované identity přiřazené systémem. Uživatelsky přiřazené identity nelze použít.

1. Vytvořte [zásadu přístupu v Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) pro identitu aplikace, kterou jste vytvořili dříve. Povolit pro tuto zásadu oprávnění tajného klíče "získat". Nekonfigurujte nastavení "autorizované aplikace" nebo `applicationId`, protože to není kompatibilní se spravovanou identitou.

    > [!NOTE]
    > Odkazy na Key Vault nejsou v současné době schopné přeložit tajné klíče uložené v trezoru klíčů s [omezeními sítě](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Referenční syntaxe

Odkaz na Key Vault má formát `@Microsoft.KeyVault({referenceString})`, kde `{referenceString}` se nahrazuje jednou z následujících možností:

> [!div class="mx-tdBreakAll"]
> | Řetězec odkazu                                                            | Popis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_SecretUri_                                                       | **SecretUri** by měl být úplný identifikátor URI datové roviny tajného klíče v Key Vault, včetně verze, například https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931.  |
> | Trezor =_trezor_; Tajné heslo =_tajné heslo_ Verzetajnéhoklíče =_verzetajnéhoklíče_ | Název **trezoru** by měl být název vašeho prostředku Key Vault. Název **tajného** kódu by měl být název cílového tajného klíče. **Verzetajnéhoklíče** by měla být verze tajného klíče, který se má použít. |

> [!NOTE] 
> Aktuálně jsou požadovány verze. Při střídání tajných kódů bude nutné aktualizovat verzi v konfiguraci aplikace.

Například kompletní odkaz by vypadal jako následující:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Jinou

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Nastavení zdrojové aplikace z Key Vault

Odkazy na Key Vault lze použít jako hodnoty pro [nastavení aplikace](configure-common.md#configure-app-settings), což vám umožní zachovat tajné klíče v Key Vault namísto Konfigurace lokality. Nastavení aplikace jsou bezpečně šifrovaná v klidovém stavu, ale pokud potřebujete možnosti správy tajných klíčů, měli byste přejít do Key Vault.

Chcete-li použít odkaz Key Vault pro nastavení aplikace, nastavte odkaz jako hodnotu nastavení. Vaše aplikace může na tajný klíč odkazovat jako na klíč jako normální. Nevyžadují se žádné změny kódu.

> [!TIP]
> Většina nastavení aplikací pomocí Key Vault odkazů by měla být označená jako nastavení slotu, protože pro každé prostředí byste měli mít oddělené trezory.

### <a name="azure-resource-manager-deployment"></a>Nasazení Azure Resource Manager

Při automatizaci nasazení prostředků prostřednictvím šablon Azure Resource Manager může být nutné pořadí závislostí v určitém pořadí, aby tato funkce fungovala. Všimněte si, že budete muset definovat nastavení aplikace jako vlastní prostředek, místo použití vlastnosti `siteConfig` v definici webu. Je to proto, že lokalita musí být definovaná jako první, aby se k ní vytvořila identita přiřazená systémem a mohla by se používat v zásadách přístupu.

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
> V tomto příkladu je nasazení správy zdrojů závislé na nastavení aplikace. To je obvykle nebezpečné chování, protože se aktualizace nastavení aplikace chová asynchronně. Protože však obsahujeme nastavení aplikace `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, aktualizace je synchronní. To znamená, že nasazení správy zdrojů bude zahájeno až po úplné aktualizaci nastavení aplikace.

## <a name="troubleshooting-key-vault-references"></a>Řešení potíží s Key Vaultmi odkazy

Pokud odkaz není správně přeložen, použije se místo toho referenční hodnota. To znamená, že pro nastavení aplikace by se vytvořila proměnná prostředí, jejíž hodnota má syntaxi `@Microsoft.KeyVault(...)`. To může způsobit, že aplikace vyvolá chyby, protože očekávala tajný kód určité struktury.

Nejčastěji to je způsobeno neznámým nastavením [zásad přístupu Key Vault](#granting-your-app-access-to-key-vault). Může to ale také být způsobeno tím, že už existující tajný kód nebo Chyba syntaxe samotného odkazu.

Pokud je syntaxe správná, můžete zobrazit další příčiny chyby tak, že zkontrolujete aktuální stav řešení na portálu. Přejděte do nastavení aplikace a pro příslušný odkaz vyberte Upravit. Pod konfigurací nastavení byste měli vidět informace o stavu, včetně všech chyb. Neexistence těchto informací implikuje neplatnou syntaxi reference.

K získání dalších informací můžete použít také jeden z vestavěných detektorů.

### <a name="using-the-detector-for-app-service"></a>Použití detektoru pro App Service

1. Na portálu přejděte do aplikace.
2. Vyberte možnost **diagnostikovat a vyřešit problémy**.
3. Zvolte možnost **dostupnost a výkon** a vyberte možnost **Webová aplikace dolů.**
4. Najděte **Key Vault Diagnostika nastavení aplikace** a klikněte na **Další informace**.


### <a name="using-the-detector-for-azure-functions"></a>Použití detektoru pro Azure Functions

1. Na portálu přejděte do aplikace.
2. Přejděte k **funkcím platformy.**
3. Vyberte možnost **diagnostikovat a vyřešit problémy**.
4. Zvolte možnost **dostupnost a výkon** a vyberte **funkce aplikace dolů nebo hlášení chyb.**
5. Klikněte na **Key Vault Diagnostika nastavení aplikace.**
