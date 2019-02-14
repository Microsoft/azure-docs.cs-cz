---
title: Vložení sestavy do kolekce pracovních prostorů Power BI Azure | Dokumentace Microsoftu
description: Zjistěte, jak pro vložení sestavy, která je v kolekcích pracovních prostorů Power BI do vaší aplikace.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 7a83a56a63fbd8a74e6a7e6d6f5158e3b5a8c2a4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232725"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Vložení sestavy do kolekce pracovních prostorů Power BI

Zjistěte, jak pro vložení sestavy, která je v kolekcích pracovních prostorů Power BI do vaší aplikace.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Podíváme se na tom, jak ve skutečnosti vložení sestavy do vaší aplikace. To předpokládá, že už máte sestavu, která existuje v rámci pracovního prostoru ve vaší kolekci pracovních prostorů. Pokud tento krok jste zatím neudělali, přečtěte si téma [začít pracovat s kolekcí pracovních prostorů Power BI](get-started.md).

.NET (C#) nebo sady Node.js SDK, spolu s jazyka JavaScript, můžete použít k snadnému vytváření vaší aplikace pomocí kolekcí pracovních prostorů Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Použití rozhraní REST API pomocí přístupových klíčů

Aby bylo možné volat rozhraní REST API, můžete předat přístupový klíč, který můžete získat z webu Azure portal pro daný pracovní prostor kolekce. Další informace najdete v tématu [začít pracovat s kolekcí pracovních prostorů Power BI](get-started.md).

## <a name="get-a-report-id"></a>Získat ID sestavy

Každý přístupový token je založena na sestavě. Je potřeba získat id dané sestavy pro sestavu, kterou chcete vložit. To můžete udělat na základě volání [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) rozhraní REST API. Vrátí id sestavy a adresu url vložení. To můžete udělat pomocí Power BI .NET SDK nebo přímé volání rozhraní REST API.

### <a name="using-the-power-bi-net-sdk"></a>Pomocí Power BI .NET SDK

Při použití sady .NET SDK, je potřeba vytvořit token přihlašovacích údajů, který je založen na přístupový klíč, který jste získali z portálu Azure portal. To je nutné nainstalovat [balíček NuGet rozhraní API Power BI](https://www.nuget.org/profiles/powerbi).

**Instalace balíčku NuGet**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**Kód jazyka C#**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Přímé volání rozhraní REST API

```csharp
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Vytvoření přístupového tokenu

Použití Power BI kolekce pracovních prostorů vložené tokeny, které jsou HMAC podepsané webových tokenů JSON. Tokeny jsou podepsány pomocí přístupového klíče z vaší kolekce pracovních prostorů Power BI. Vložené tokeny, ve výchozím nastavení, se používají k zajištění přístup jen pro čtení pro sestavy k vložení do aplikace. Vložit tokeny budou vydány pro konkrétní sestavu a by měly být přidružené adrese URL vložení.

Přístupové tokeny by být vytvořeny na serveru, které přístupové klíče se používají k přihlašování a šifrování tokenů. Informace o tom, jak vytvořit token přístupu najdete v tématu [ověřování a autorizace pomocí kolekcí pracovních prostorů Power BI](app-token-flow.md). Můžete také zkontrolovat [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metody. Tady je příklad co to může vypadat třeba pomocí sady .NET SDK pro Power BI.

Můžete použít ID sestavy, které jste získali dříve. Jakmile se vytvoří token pro vložení, pak použijete přístupový klíč k vygenerování tokenu, který můžete použít z pohledu jazyka javascript. *PowerBIToken třídy* je nutné nainstalovat [Power BI základní NuGut balíček](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalace balíčku NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**Kód jazyka C#**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Přidání obory oprávnění pro tokeny pro vložení

Při použití tokenů pro vložení, můžete omezit využití prostředků, které poskytuje přístup k. Z tohoto důvodu můžete vygenerovat token s s vymezeným oborem oprávnění. Další informace najdete v tématu [obory](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Vložení pomocí JavaScriptu

Jakmile máte přístupový token a ID sestavy, jsme vložení sestavy pomocí JavaScriptu. To vyžaduje instalaci balíčku NuGet [balíček Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Stačí embedUrl bude https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Můžete použít [ukázka vložení sestavy JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) k testování funkcí. Poskytuje příklady kódu pro různé operace, které jsou k dispozici.

**Instalace balíčku NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Kód jazyka JavaScript**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>Nastavit velikost prvků embedded

Sestava bude automaticky vložen v závislosti na velikosti svého kontejneru. Pokud chcete přepsat výchozí velikost vloženou položku, jednoduše přidejte třídy atributu nebo vložené styly CSS pro šířku a výšku.

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript balíčku](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI NuGet rozhraní API balíčku](https://www.nuget.org/profiles/powerbi)
[Power BI základní NuGut balíček](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI-CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)  
[Power BI uzlu úložiště Git](https://github.com/Microsoft/PowerBI-Node)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
