---
title: Přepínání mezi zobrazením a režimem úprav pro sestavy
description: Přečtěte si, jak přepínat mezi zobrazením a režimem úprav sestav v rámci Power BI kolekce pracovních prostorů.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b2696560b5d5013fe337b51ec61cbfac9e512610
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357660"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Přepínání mezi zobrazením a režimem úprav pro sestavy v Power BI kolekce pracovních prostorů

Přečtěte si, jak přepínat mezi zobrazením a režimem úprav sestav v rámci Power BI kolekce pracovních prostorů.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Vytvoření přístupového tokenu

Musíte vytvořit přístupový token, který vám umožní zobrazit a upravit sestavu. Chcete-li sestavu upravit a uložit, budete potřebovat oprávnění k tokenu **sestavy.** Další informace najdete v tématu [ověřování a autorizace v Power BIch kolekcích pracovních prostorů](app-token-flow.md).

> [!NOTE]
> To umožňuje upravit a uložit změny existující sestavy. Pokud chcete také jako funkci podpory **Uložit jako**, je třeba dodat další oprávnění. Další informace najdete v tématu [obory](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Vložení konfigurace

Aby se v režimu úprav zobrazilo tlačítko Uložit, je potřeba, abyste zadali oprávnění a vlastnost ViewMode. Další informace najdete v tématu [vložení podrobností o konfiguraci](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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

To znamená, že se sestava vloží do režimu zobrazení na základě **vlastnost ViewMode** nastaveného na **modely. Vlastnost ViewMode. View**.

## <a name="view-mode"></a>Režim zobrazení

Můžete použít následující JavaScript pro přepnutí do režimu zobrazení, pokud jste v režimu úprav.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Režim úprav

Pokud jste v režimu zobrazení, můžete přepnout do režimu úprav pomocí následujícího JavaScriptu.

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
[PowerBI-CSharp úložiště Git](https://github.com/Microsoft/PowerBI-CSharp)  
[Úložiště Git uzlu PowerBI](https://github.com/Microsoft/PowerBI-Node)  

Chcete se na něco zeptat? [Vyzkoušejte komunitu Power BI](https://community.powerbi.com/)
