---
title: Přepínání mezi zobrazení a úprava režimu pro sestavy v Power BI prostoru kolekcích | Microsoft Docs
description: Zjistěte, jak přepínat mezi zobrazení a úprava režim pro sestavy v rámci kolekce pracovních prostorů Power BI.
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
ms.openlocfilehash: 23a8c4f0dd626a623df56de9546258a23d549d1a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Přepínání mezi zobrazení a úprava režimu pro sestavy v Power BI prostoru kolekce

Zjistěte, jak přepínat mezi zobrazení a úprava režim pro sestavy v rámci kolekce pracovních prostorů Power BI.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Vytváření token přístupu

Budete muset vytvořit přístupový token, který vám dává možnost jak zobrazit a upravit sestavu. Pokud chcete upravit a uložit sestavu, musíte **Report.ReadWrite** token oprávnění. Další informace najdete v tématu [Authenticating a autorizaci v Power BI prostoru kolekce](app-token-flow.md).

> [!NOTE]
> To umožňuje upravit a uložit změny do existující sestavy. Pokud také chcete funkci podporu **uložit jako**, budete muset zadat další oprávnění. Další informace najdete v tématu [obory](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Vložení konfigurace

Budete muset zadat oprávnění a viewMode, chcete-li zobrazit uložení tlačítko v režimu úprav. Další informace najdete v tématu [vložení podrobnosti konfigurace](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
```

To znamená pro vložení sestavy v režimu zobrazení na základě **viewMode** Probíhá nastavení objektu na **modelů. ViewMode.View**.

## <a name="view-mode"></a>Režim zobrazení

Chcete-li přepnout do režimu zobrazení, pokud jste v režimu úprav, můžete použít následující JavaScript.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Režim úprav

Pokud jste v zobrazení režimu, můžete použít následující JavaScript přepnout do režimu úprav.

```
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
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Úložiště Git PowerBI CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Úložiště Git PowerBI uzlu](https://github.com/Microsoft/PowerBI-Node)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
