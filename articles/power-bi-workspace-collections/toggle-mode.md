---
title: Přepínání mezi zobrazením a upravit režimu pro sestavy v kolekcích pracovních prostorů Power BI | Dokumentace Microsoftu
description: Informace o přepínání mezi zobrazením a upravit režimu pro sestavy v rámci kolekce pracovních prostorů Power BI.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 892ea129a57e77c59f37b305b96b42aa85dba32b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232703"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Přepínání mezi zobrazením a upravit režimu pro sestavy v kolekcích pracovních prostorů Power BI

Informace o přepínání mezi zobrazením a upravit režimu pro sestavy v rámci kolekce pracovních prostorů Power BI.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Vytvoření přístupového tokenu

Je potřeba vytvořit přístupový token, který vám dává možnost, jak zobrazit a upravit sestavu. Pokud chcete upravit a uložit sestavu, musíte **Report.ReadWrite** token oprávnění. Další informace najdete v tématu [ověřování a autorizace v kolekcích pracovních prostorů Power BI](app-token-flow.md).

> [!NOTE]
> Můžete upravit a uložit změny do stávající sestavy. Pokud chcete také funkci podporu **uložit jako**, budete muset zadat další oprávnění. Další informace najdete v tématu [obory](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Konfigurace pro vložení

Je třeba zadat oprávnění a viewMode, chcete-li zobrazit uložení tlačítko v režimu úprav. Další informace najdete v tématu [podrobnosti o konfiguraci pro vložení](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
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

Tím je oznámeno vložení sestavy do režimu zobrazení na základě **viewMode** nastavena na **modely. ViewMode.View**.

## <a name="view-mode"></a>Režim zobrazení

Chcete-li přepnout do režimu zobrazení, pokud jste v režimu úprav můžete použít následující jazyka JavaScript.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Režim úprav

Pokud jste v zobrazení režimu, můžete použít následující JavaScript přepnout do režimu úprav.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Vložení sestavy](embed-report.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[Power BI uzlu úložiště Git](https://github.com/Microsoft/PowerBI-Node)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
