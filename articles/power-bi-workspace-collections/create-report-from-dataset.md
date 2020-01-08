---
title: Vytvoření sestavy z datové sady Power BI kolekce pracovních prostorů
description: Sestavy kolekce pracovních prostorů Power BI se teď dají vytvořit z datové sady ve vaší vlastní aplikaci.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bcc6044d0f0f5270f81a619e4d1ad71ea35cc170
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427061"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Vytvoření nové sestavy z datové sady v Power BI kolekcích pracovních prostorů

Sestavy kolekce pracovních prostorů Power BI se teď dají vytvořit z datové sady ve vaší vlastní aplikaci.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Metoda ověřování je podobná jako vložení sestavy. Je založen na tokenech přístupu, které jsou specifické pro datovou sadu. Tokeny používané pro PowerBI.com jsou vydávány Azure Active Directory (AAD). Tokeny kolekce pracovního prostoru Power BI vydávají vaše vlastní aplikace.

Při vytváření vložené sestavy jsou vydané tokeny pro konkrétní datovou sadu. Tokeny by měly být přidruženy k adrese URL pro vložení na stejném elementu, abyste měli jistotu, že každý má jedinečný token. Aby bylo možné vytvořit vloženou sestavu, musí být v přístupovém tokenu zadaná *datová sada. čtení a pracovní prostor. Report. Create* .

## <a name="create-access-token-needed-to-create-new-report"></a>Vytvoření přístupového tokenu potřebného k vytvoření nové sestavy

Power BI kolekce pracovních prostorů používají token pro vložení, což jsou webové tokeny JSON podepsané pomocí HMAC. Tokeny se podepisují pomocí přístupového klíče z kolekce pracovních prostorů Power BI. Vložené tokeny ve výchozím nastavení slouží k poskytnutí přístupu k sestavě, která má být vložena do aplikace, jen pro čtení. Tokeny pro vložení jsou vydávány pro konkrétní sestavu a měly by být přidruženy k adrese URL pro vložení.

Na serveru by se měly vytvořit přístupové tokeny, které se používají k podpisu a šifrování tokenů. Informace o tom, jak vytvořit přístupový token, najdete v tématu [ověřování a autorizace pomocí Power BIch kolekcí pracovních prostorů](app-token-flow.md). Můžete také zkontrolovat metodu [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) . Tady je příklad toho, co by vypadalo jako použití sady .NET SDK pro Power BI.

V tomto příkladu máme ID datové sady, na které chceme vytvořit novou sestavu. Také je potřeba přidat obory pro *datovou sadu. číst a v pracovním prostoru. Report. Create*.

*Třída PowerBIToken* vyžaduje instalaci [balíčku Power BI Core NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalace balíčku NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#znakovou**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Vytvořit novou prázdnou sestavu

Aby bylo možné vytvořit novou sestavu, měla by být poskytnuta konfigurace Create. To by mělo zahrnovat přístupový token, embedURL a datasetID, pro který chcete vytvořit sestavu. K tomu je potřeba nainstalovat balíček NuGet [Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl bude pouze https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> K otestování funkcí můžete použít [ukázku pro vložení sestavy JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/) . Obsahuje také příklady kódu pro různé operace, které jsou k dispozici.

**Instalace balíčku NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScriptový kód**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Volání *PowerBI. createReport ()* způsobí, že v rámci elementu *div* se zobrazí prázdné plátno v režimu úprav.

![Nová prázdná sestava](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Uložit nové sestavy

Sestava není vytvořena, dokud nebudete volat operaci **Uložit jako** . To lze provést z nabídky soubor nebo z JavaScriptu.

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
> Nová sestava se vytvoří až po volání metody **Save as (Uložit jako** ). Po uložení bude plátno stále zobrazovat datovou sadu v režimu úprav, nikoli v sestavě. Novou sestavu potřebujete znovu načíst, jako byste nějakou jinou sestavu.

![Nabídka soubor-uložit jako](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Načíst novou sestavu

Aby bylo možné pracovat s novou sestavou, je nutné ji vložit stejným způsobem, jako aplikace vloží běžnou sestavu, což znamená, že nový token musí být vydán konkrétně pro novou sestavu a následně volat metodu embed.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatizace ukládání a načítání nové sestavy pomocí události "uloženo"

K automatizaci procesu "Uložit jako" a následnému načtení nové sestavy můžete použít událost "uloženo". Tato událost se aktivuje, když se dokončí operace Uložit, a vrátí objekt JSON obsahující nový reportId, název sestavy, starou reportId (pokud tam existoval) a pokud byla operace saveAs nebo uložena.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

K automatizaci procesu můžete naslouchat na události "uloženo", vzít nové reportId, vytvořit nový token a vložit do něj novou sestavu.

```html
<div id="reportContainer"></div>
<script>
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
</script>
```

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Ukládání sestav](save-reports.md)  
[Vložení sestavy](embed-report.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI základní balíček NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Balíček Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Chcete se na něco zeptat? [Vyzkoušejte komunitu Power BI](https://community.powerbi.com/)
