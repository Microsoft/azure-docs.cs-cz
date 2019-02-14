---
title: Ukládání sestav v kolekcích pracovních prostorů Power BI | Dokumentace Microsoftu
description: Zjistěte, jak uložit sestavy v rámci kolekce pracovních prostorů Power BI. To vyžaduje správná oprávnění, aby bylo možné úspěšně fungovat.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: a66584aa1cd3f335111774ef1df37cddc630f69d
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233366"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Ukládání sestav v kolekcích pracovních prostorů Power BI

Zjistěte, jak uložit sestavy v rámci kolekce pracovních prostorů Power BI. Uložení sestavy vyžaduje správná oprávnění, aby bylo možné úspěšně fungovat.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

V rámci kolekce pracovních prostorů Power BI můžete upravit existující sestavy a jejich uložení. Můžete také vytvořit novou sestavu a uložit jako novou sestavu pro jeho vytvoření.

Pokud chcete uložit sestavu, musíte nejprve vytvořit token pro konkrétní sestavy se správné rozsahy:

* Umožňuje uložit Report.ReadWrite obor je požadován
* Chcete-li uložit jako, Report.Read a Workspace.Report.Copy obory se vyžadují.
* Chcete-li uložit informace a uložit jako, Report.ReadWrite a Workspace.Report.Copy jsou povinné.

V uvedeném pořadí, chcete-li povolit právo uložit nebo uložit jako tlačítka v nabídce Soubor je potřeba zadat správné oprávnění v konfiguraci vložení při vložení sestavy:

* modely. Permissions.ReadWrite
* modely. Permissions.Copy
* modely. Permissions.All

> [!NOTE]
> Přístupový token musí také příslušných oborech. Další informace najdete v tématu [obory](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Vložení sestavy do režimu úprav

Řekněme, že chcete vložit sestavu v režimu úprav ve svých aplikacích, aby tak, aby předal správné vlastnosti v konfiguraci vložení volání powerbi.embed(). Zadejte oprávnění a viewMode, aby bylo možné zobrazit uložení a uložit jako tlačítka v režimu úprav. Další informace najdete v tématu [podrobnosti o konfiguraci pro vložení](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Například v jazyce JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
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
    </script>
```

Sestavy se teď vloží do vaší aplikace v režimu úprav.

## <a name="save-report"></a>Uložit sestavu

Po vložení sestavy v režimu úprav s správný token a oprávnění, můžete uložit sestavu v nabídce Soubor nebo z jazyka javascript:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Uložit jako

```javascript
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Až poté, co *uložit jako* je vytvoření nové sestavy. Po uložení plátno stále zobrazuje původní sestavu v režimu úprav a ne novou sestavu. Vložte novou sestavu, která byla vytvořena. Vložení nové sestavy vyžaduje nový přístupový token při jejich vytvoření na sestavu.

Pak musíte načíst nové sestavy po *uložit jako*. Načítají se nová sestava je podobný vložení žádné sestavy.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Vložení sestavy](embed-report.md)  
[Vytvoření nové sestavy z datové sady](create-report-from-dataset.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)

