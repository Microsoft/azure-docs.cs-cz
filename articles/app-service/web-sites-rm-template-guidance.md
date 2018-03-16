---
title: "Pokyny k nasazení webové aplikace Azure pomocí šablon | Microsoft Docs"
description: "Doporučení pro vytváření šablon Azure Resource Manager k nasazení webové aplikace."
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: dc816bb6e95d2800d79124dfac60b55e88eaa500
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Pokyny k nasazení webové aplikace s použitím šablon Azure Resource Manageru

Tento článek obsahuje doporučení pro vytváření šablon Azure Resource Manager k nasazení řešení služby Azure App Service. Tato doporučení vám může pomoct vyhnout běžné problémy.

## <a name="define-dependencies"></a>Definování závislostí

Definování závislostí u webových aplikací vyžaduje znalosti o interakci prostředků v rámci webové aplikace. Pokud zadáte závislosti v nesprávném pořadí, může způsobit chyby nasazení nebo vytvořit spor, který nasazení se zablokuje.

> [!WARNING]
> Pokud do šablony zahrnout MSDeploy rozšíření lokality, je nutné nastavit všechny prostředky, konfigurace jako závislý na prostředku MSDeploy. Změny konfigurace způsobit webu restartovat asynchronně. Tím, že prostředky konfigurace závisí na MSDeploy, zkontrolujte, že dokončení MSDeploy před restartování webu. Bez těchto závislostí může během procesu nasazení MSDeploy restartovat webu. Šablonu příklad najdete v části [WordPress šablonu s webovou nasazení závislost](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Následující obrázek ukazuje, pak pořadí závislostí pro různé prostředky služby App Service:

![Závislosti webové aplikace](media/web-sites-rm-template-guidance/web-dependencies.png)

Můžete nasadit prostředky v následujícím pořadí:

**Vrstvy 1**
* Plán služby App Service.
* Žádné další související prostředky, jako jsou databáze nebo účty úložiště.

**Vrstva 2**
* Webové aplikace – závisí na plán služby App Service.
* Azure Application Insights instance, která je cílena serverové farmě – závisí na plán služby App Service.

**Úroveň 3**
* Ovládací prvek – zdroje závisí na webové aplikace.
* Rozšíření lokality MSDeploy – závisí na webové aplikace.
* Statistika instanci aplikace na zacílený serverové farmy – závisí na webové aplikace.

**Úroveň 4**
* Certifikát služby App Service – závisí na zdrojového kódu nebo MSDeploy Pokud buď je k dispozici. Jinak závisí na webové aplikace.
* Nastavení konfigurace (připojovací řetězce, hodnoty souboru web.config, nastavení aplikace) – závisí na zdrojového kódu nebo MSDeploy, pokud se buď nachází. Jinak závisí na webové aplikace.

**Úroveň 5**
* Hostování název vazby – závisí na certifikátu, pokud je k dispozici. Jinak závisí na vyšší úrovni prostředku.
* Rozšíření – lokality závisí na nastavení konfigurace, pokud je k dispozici. Jinak závisí na vyšší úrovni prostředku.

Řešení obvykle obsahuje jenom některé z těchto zdrojů a vrstvy. Pro chybějící úrovně mapovat nižší prostředky na další vyšší úroveň.

Následující příklad ukazuje částí šablony. Hodnota konfigurace řetězec připojení závisí na MSDeploy rozšíření. Rozšíření MSDeploy závisí na webovou aplikaci a databázi.

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>Najít informace o chybách MSDeploy

Pokud vaše šablony Resource Manageru používá MSDeploy, může být obtížné zjistit, nasazení chybové zprávy. Chcete-li získat další informace po selhání nasazení, zkuste následující kroky:

1. Přejděte na web [Kudu konzoly](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Přejděte do složky v D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Hledání souborů appManagerStatus.xml a appManagerLog.xml. První soubor zaznamená stav. Druhý soubor zaznamená do protokolu informace o této chybě. Pokud chyba není jasné pro vás, můžete jej zahrnout při se žádostí o pomoc ve fóru.

## <a name="choose-a-unique-web-app-name"></a>Zvolte název jedinečný webové aplikace

Název webové aplikace musí být globálně jedinečný. Můžete použít zásady vytváření názvů, které by mohlo být jedinečný, nebo můžete použít [uniqueString funkce](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) vám pomůže při generování jedinečný název.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>Další postup

* Kurz týkající se nasazení webové aplikace pomocí šablony, najdete v části [zřídit a nasadit mikroslužeb předvídatelné v Azure](app-service-deploy-complex-application-predictably.md).