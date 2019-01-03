---
title: Doprovodné materiály k nasazování aplikací pomocí šablon - službě Azure App Service | Dokumentace Microsoftu
description: Doporučení pro vytváření šablon Azure Resource Manageru pro nasazování webových aplikací.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 8c3eac208a4e1b0fd55e56956035b3fb5a23fc00
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730921"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Doprovodné materiály k nasazování webových aplikací pomocí šablon Azure Resource Manageru

Tento článek obsahuje doporučení pro vytváření šablon Azure Resource Manageru pro nasazení řešení služby Azure App Service. Tato doporučení vám může pomoct vyhnout běžné problémy.

## <a name="define-dependencies"></a>Definování závislostí

Definování závislostí u webových aplikací nutné znalosti toho, jak komunikovat prostředky v rámci webové aplikace. Pokud zadáte nesprávné pořadí závislostí, může způsobit chyby nasazení nebo vytvořte časování, nasazení se zablokuje.

> [!WARNING]
> Pokud zahrnete MSDeploy rozšíření webu ve vaší šabloně, je nutné nastavit všechny konfigurace prostředky jako závislý na prostředku MSDeploy. Změny konfigurace způsobit, že server restartovat asynchronně. Tím, že prostředky konfigurace závisí na MSDeploy, zajistíte tím, že dokončení MSDeploy před restartování webu. Bez těchto závislostí může restartovat web během procesu nasazení MSDeploy. Ukázková šablona, naleznete v tématu [WordPress šablony se webové nasazení závislostí](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Následující obrázek ukazuje pak pořadí závislostí pro různé prostředky App Service:

![Závislosti webové aplikace](media/web-sites-rm-template-guidance/web-dependencies.png)

Nasazení prostředků v následujícím pořadí:

**Vrstvy 1**
* Plán služby App Service.
* Všechny ostatní související prostředky, jako jsou databáze nebo účty úložiště.

**Úroveň 2**
* Webové aplikace – závisí na plán služby App Service.
* Azure Application Insights instanci, která cílí na serverové farmě--závisí na plán služby App Service.

**Úroveň 3**
* Správa – zdrojového kódu, závisí na webovou aplikaci.
* Rozšíření webu MSDeploy – závisí na webovou aplikaci.
* Instance Application Insights, který cílí na serverové farmě – závisí na webovou aplikaci.

**Vrstva 4**
* Certifikát App Service – závisí na správy zdrojového kódu nebo MSDeploy, pokud je k dispozici. V opačném případě závisí na webovou aplikaci.
* Nastavení konfigurace (připojovací řetězce, hodnoty web.config, nastavení aplikace) – závisí na správy zdrojového kódu nebo MSDeploy, pokud je k dispozici. V opačném případě závisí na webovou aplikaci.

**Úroveň 5**
* Název vazby – hostování závisí na certifikátu, pokud jsou k dispozici. V opačném případě závisí na vyšší úrovni prostředků.
* Rozšíření – lokality závisí na nastavení konfigurace, pokud jsou k dispozici. V opačném případě závisí na vyšší úrovni prostředků.

Obvykle vaše řešení obsahuje jenom některé z těchto prostředků a úrovně. Pro chybějící úrovně namapujte na další vyšší úroveň nižší prostředky.

Následující příklad ukazuje část šablony. Hodnota konfigurace řetězec připojení závisí na rozšíření MSDeploy. Rozšíření MSDeploy závisí na webovou aplikaci a databázi. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Připraveno ke spuštění ukázky, která používá výše uvedený kód, naleznete v tématu [šablony: Vytvoření jednoduché webové aplikace Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Informace o chybách MSDeploy

Pokud vaše šablony Resource Manageru používá MSDeploy, může být obtížné pochopit, chybové zprávy pro nasazení. Pokud chcete získat další informace po selhání nasazení, vyzkoušejte následující kroky:

1. Přejděte na web [konzola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Přejděte do složky na D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Hledání souborů appManagerStatus.xml a appManagerLog.xml. První soubor zaznamená stav. Druhý soubor zaznamenává informace o této chybě. Pokud není jasné, chyba, můžete zahrnout při se s žádostí o pomoc na fóru.

## <a name="choose-a-unique-web-app-name"></a>Zvolte jedinečný název aplikace

Musí být globálně jedinečný název pro vaši webovou aplikaci. Můžete použít zásady vytváření názvů, která by mohla být jedinečný, nebo můžete použít [uniqueString funkce](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) pomáhat s generováním jedinečný název.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Nasaďte certifikát webové aplikace ze služby Key Vault

Pokud obsahuje šablony [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) prostředek pro vytvoření vazby SSL a certifikát je uložen ve službě Key Vault, je nutné identity služby App Service můžete přístup k certifikátu.

V globální Azure objektu služby App Service má ID **abfa0a7c-a6b6-4736-8310-5855508787cd**. Pokud chcete udělit přístup do služby Key Vault, instančního objektu služby App Service, použijte:

```azurepowershell-interactive
Set-AzureRmKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Ve službě Azure Government, objekt služby App Service má ID **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Pomocí tohoto ID v předchozím příkladu.

Ve službě Key Vault vyberte **certifikáty** a **vygenerovat/importovat** na kterou certifikát nahrajete.

![Importovat certifikát](media/web-sites-rm-template-guidance/import-certificate.png)

V šabloně, zadejte název certifikátu pro `keyVaultSecretName`.

Ukázková šablona, naleznete v tématu [nasaďte certifikát webové aplikace ze služby Key Vault tajný klíč a použít ho k vytvoření vazby SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Další postup

* Kurz k nasazování webových aplikací s využitím šablony najdete v tématu [zřizování a nasazování mikroslužeb v Azure předvídatelně](deploy-complex-application-predictably.md).