---
title: Uložení sestavy v Power BI prostoru kolekce | Microsoft Docs
description: Informace o ukládání sestavy v rámci kolekce pracovních prostorů Power BI. To vyžaduje příslušná oprávnění, aby bylo možné správně fungovat.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c5512584531c9f5c8a13e9a50161eb6b5a1f8a7b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Uložení sestavy v Power BI prostoru kolekce

Informace o ukládání sestavy v rámci kolekce pracovních prostorů Power BI. Ukládání sestav vyžaduje příslušná oprávnění, aby bylo možné správně fungovat.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

V rámci Power BI prostoru kolekcí můžete upravit existující sestavy a uložíte. Můžete také vytvořit novou sestavu a uložit jako novou sestavu k jeho vytvoření.

Chcete-li uložit sestavu, musíte nejprve vytvořit token pro konkrétní sestavu s správné rozsahy:

* Chcete-li povolit ukládání Report.ReadWrite obor je požadován
* Chcete-li uložit jako, jsou požadovány Report.Read a Workspace.Report.Copy oborů
* Chcete-li uložit a uložit jako, jsou požadovány Report.ReadWrite a Workspace.Report.Copy

V uvedeném pořadí, aby bylo možné povolit právo uložit nebo uložit jako tlačítka v nabídce soubor potřebujete poskytovat správné oprávnění v konfiguraci vložení při vložení sestavy:

* modely. Permissions.ReadWrite
* modely. Permissions.Copy
* modely. Permissions.All

> [!NOTE]
> Přístupový token musí také odpovídající obory. Další informace najdete v tématu [obory](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Vložení sestavy v režimu úprav

Řekněme, že chcete v režimu úprav uvnitř vaší aplikace, Uděláte to tak, aby předat vlastnosti oprávnění v konfiguraci vložení a volání powerbi.embed() vložení sestavy. Zadejte oprávnění a viewMode, aby bylo možné zobrazit uložení a uložit jako tlačítka v režimu úprav. Další informace najdete v tématu [vložení podrobnosti konfigurace](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Například v jazyce JavaScript:

```
   <div id="reportContainer"></div>

    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
```

Sestavy je vložen do vaší aplikace v režimu úprav.

## <a name="save-report"></a>Uloží sestavu

Po vložení sestavy v režimu úprav s správné token a oprávnění, můžete uložit sestavu v nabídce Soubor nebo z javascript:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Uložit jako

```
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Až poté, co *uložit jako* je vytvoření nové sestavy. Po uložení se na plátno stále zobrazuje staré sestavu v režimu úprav a není novou sestavu. Vložení nové sestavy, který byl vytvořen. Vložení nové sestavy vyžaduje nový přístupový token, jako jsou vytvořené na sestavu.

Pak bude nutné načíst novou sestavu po *uložit jako*. Načítání nová sestava je podobná vložení žádnou sestavu.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Vložení sestavy](embed-report.md)  
[Vytvoření nové sestavy z datové sady](create-report-from-dataset.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)

