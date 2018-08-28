---
title: Vytvoření nové sestavy z datové sady v kolekcích pracovních prostorů Power BI | Dokumentace Microsoftu
description: Teď je možné vytvářet sestavy Power BI kolekce pracovních prostorů z datové sady ve své aplikaci.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: dbfe8c98dce1879a2e0956fafba32ab6e608bfdb
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046650"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Vytvoření nové sestavy z datové sady v kolekcích pracovních prostorů Power BI

Teď je možné vytvářet sestavy Power BI kolekce pracovních prostorů z datové sady ve své aplikaci.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Metoda ověřování je podobná vkládání sestav. Je založen na přístupové tokeny, které jsou specifické pro datovou sadu. Pomocí Azure Active Directory (AAD) se vydávají tokeny pro PowerBI.com. Ve své aplikaci se vydávají tokeny Power BI kolekce pracovních prostorů.

Při vytvoření vložené sestavy, jsou tokeny vydané pro konkrétní datové sady. Tokeny by měly být přidruženy s vloženou adresu URL u stejného elementu k zajištění, že každá má jedinečný token. Chcete-li vytvořit zprávu o vložených *Dataset.Read a Workspace.Report.Create* obory musí být uvedeny v přístupovém tokenu.

## <a name="create-access-token-needed-to-create-new-report"></a>Vytvoření přístupového tokenu, které jsou potřeba k vytvoření nové sestavy

Kolekce pracovních prostorů Power BI použít vložení tokenu, který je HMAC podepsaný webových tokenů JSON. Tokeny jsou podepsány pomocí přístupového klíče z vaší kolekce pracovních prostorů Power BI. Vložené tokeny, ve výchozím nastavení, se používají k zajištění přístup jen pro čtení pro sestavy k vložení do aplikace. Vložit tokeny budou vydány pro konkrétní sestavu a by měly být přidružené adrese URL vložení.

Přístupové tokeny by být vytvořeny na serveru, které přístupové klíče se používají k přihlašování a šifrování tokenů. Informace o tom, jak vytvořit token přístupu najdete v tématu [ověřování a autorizace pomocí kolekcí pracovních prostorů Power BI](app-token-flow.md). Můžete také zkontrolovat [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metody. Tady je příklad co to může vypadat třeba pomocí sady .NET SDK pro Power BI.

V tomto příkladu máme naše ID datové sady, který chcete vytvořit novou sestavu na. Musíme také přidat obory *Dataset.Read a Workspace.Report.Create*.

*PowerBIToken třídy* je nutné nainstalovat [Power BI základní NuGut balíček](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalace balíčku NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Kód jazyka C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Vytvoření nové prázdné sestavy

Chcete-li vytvořit novou sestavu, musí být zadána konfigurace vytvořit. Ty by měly zahrnovat přístupový token, embedURL a datasetID, který chcete vytvořit sestavu s. To vyžaduje instalaci balíčku nuget [balíček Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Stačí embedUrl bude https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Můžete použít [ukázka vložení sestavy JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) k testování funkcí. Poskytuje příklady kódu pro různé operace, které jsou k dispozici.

**Instalace balíčku NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Kód jazyka JavaScript**

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
```

Volání *powerbi.createReport()* díky prázdného plátna v režimu úprav se zobrazí v rámci *div* elementu.

![Nová prázdná sestava](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Uložení nové sestavy

Sestava není vytvořena až do okamžiku volání **uložit jako** operace. To lze provést v nabídce Soubor nebo z jazyka JavaScript.

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
> Vytvoření nové sestavy až po **uložit jako** je volána. Po uložení se na plátně stále zobrazit datovou sadu v režimu úprav a není sestava. Je nutné znovu načíst nové sestavy stejně, jako byste jiných sestavách.

![Uložit soubor jako nabídka –](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Načíst novou sestavu

Aby bylo možné pracovat s novou sestavu, kterou je potřeba ji vložit do aplikace vloží u běžné sestavy stejným způsobem, což znamená, nový token musí být vydaný speciálně pro novou sestavu a poté zavolejte metodu vložení.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizace uložit a načíst nové sestavy pomocí "uložené" události

Aby bylo možné automatizovat proces "uložení jako" a pak načítání novou sestavu, můžete využít službu "uložené" události. Tato událost se aktivuje při ukládání operace se dokončila a vrátí objekt Json obsahující nové ID sestavy, název sestavy, původní ID sestavy (pokud existuje) a pokud byla operace rezervaci nebo uložit.

```
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

K automatizaci procesu naslouchání "uložené" události, využít nové ID sestavy, vytvořit nový token a vložit novou sestavu s ním.

```
<div id="reportContainer"></div>
  
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
```

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Ukládání sestav](save-reports.md)  
[Vložení sestavy](embed-report.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI základní NuGut balíček](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript balíčku](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)