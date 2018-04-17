---
title: Vložení sestavy v Azure Power BI prostoru kolekcích | Microsoft Docs
description: Postup vložení sestavy, který je v Power BI prostoru kolekce do vaší aplikace.
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
ms.openlocfilehash: b6fa46b1cf3a251d6116e7de6ef41a9e6d265c29
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Vložení sestavy v Power BI prostoru kolekce

Postup vložení sestavy, který je v Power BI prostoru kolekce do vaší aplikace.

> [!IMPORTANT]
> Kolekce Pracovních prostorů Power BI jsou zastaralé a dostupné do června 2018 nebo do data uvedeného ve vaší smlouvě. Doporučujeme naplánovat migraci do Power BI Embedded, předejdete tak výpadkům vaší aplikace. Informace o postupu migrace dat do Power BI Embedded najdete v tématu [Migrace obsahu kolekcí Pracovních prostorů Power BI do Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Podíváme se na tom, jak ve skutečnosti vložení sestavy do vaší aplikace. Toto předpokládá, že už máte sestavu, která existuje v rámci pracovního prostoru v kolekci pracovního prostoru. Pokud tento krok jste ještě neudělali, přečtěte si téma [Začínáme s Power BI prostoru kolekce](get-started.md).

.NET (C#) nebo Node.js SDK, společně s JavaScript, můžete snadno vytvářet aplikace s Power BI prostoru kolekce.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Pomocí rozhraní REST API pomocí přístupových kláves

Aby bylo možné volat rozhraní REST API, můžete předat přístupový klíč, který můžete získat z portálu Azure pro kolekci daného pracovního prostoru. Další informace najdete v tématu [Začínáme s Power BI prostoru kolekce](get-started.md).

## <a name="get-a-report-id"></a>Získání ID sestavy

Každý přístupový token je založena na sestavu. Musíte získat id dané sestavy pro sestavu, kterou chcete vložit. To lze provést na základě volání [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API. Tato možnost vrátí id sestavy a adresu url pro vložení. To lze provést pomocí .NET SDK služby Power BI nebo přímé volání rozhraní REST API.

### <a name="using-the-power-bi-net-sdk"></a>Pomocí .NET SDK služby Power BI

Když pomocí sady .NET SDK, musíte vytvořit token přihlašovacích údajů, který je založen na přístupový klíč, který můžete získat z portálu Azure. To je nutné nainstalovat [balíček NuGet pro rozhraní API Power BI](https://www.nuget.org/profiles/powerbi).

**Instalace balíčku NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Kód jazyka C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Přímé volání rozhraní REST API

```
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

## <a name="create-an-access-token"></a>Vytvořit token přístupu

Power BI prostoru kolekce použijte vložení tokeny, které jsou HMAC podepsané webových tokenů JSON. Přístupový klíč z vaší kolekce pracovních prostorů Power BI jsou podepsané tokeny. Vložení tokeny, ve výchozím nastavení, slouží k poskytování přístupu jen pro čtení k sestavě pro vložení do aplikace. Vložení tokeny jsou vydán pro konkrétní sestavy a by měly být přidružené adrese URL vložení.

Přístupové tokeny by se vytvořit na serveru, přístupové klíče se používají pro přihlášení nebo šifrování tokenů. Informace o tom, jak vytvořit token přístupu najdete v tématu [Authenticating a autorizaci s Power BI prostoru kolekce](app-token-flow.md). Můžete také zkontrolovat [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metoda. Tady je příklad co to může vypadat třeba pomocí sady .NET SDK pro Power BI.

Můžete použít ID sestavy, které jste získali dříve. Po vytvoření vkládací token pak použijete přístupový klíč pro vygenerování tokenu, který můžete použít z pohledu javascript. *PowerBIToken třída* je nutné nainstalovat [Power BI základní NuGut balíček](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalace balíčku NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Kód jazyka C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Přidání obory oprávnění pro vložení tokeny

Pokud používáte vložení tokeny, můžete omezit využití prostředků, které vám umožní získat přístup k. Z tohoto důvodu můžete vygenerovat token s vymezená oprávnění. Další informace najdete v tématu [oborů](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Vložení pomocí jazyka JavaScript

Až budete mít přístupový token a ID sestavy, jsme vložení sestavy pomocí jazyka JavaScript. To je nutné nainstalovat NuGet [Power BI JavaScript balíček](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Právě embedUrl bude https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Můžete použít [ukázka vložení sestavy JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) správnou funkci. Také nabízí příklady kódu pro různé operace, které jsou k dispozici.

**Instalace balíčku NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Kód jazyka JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Nastavení velikosti vložené prvky

Sestava bude automaticky vložený podle velikosti svého kontejneru. Pokud chcete přepsat výchozí velikost vložené položky, stačí přidáte třídu atributu nebo vložené stylů CSS pro šířku a výšku.

## <a name="see-also"></a>Další informace najdete v tématech

[Začínáme s ukázkou](get-started-sample.md)  
[Ověřování a autorizace v kolekcích Pracovních prostorů Power BI](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Vložená ukázka JavaScriptu](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript balíčku](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI rozhraní API NuGet balíček](https://www.nuget.org/profiles/powerbi)
[Power BI základní NuGut balíčku](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Úložiště Git PowerBI CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Úložiště Git PowerBI uzlu](https://github.com/Microsoft/PowerBI-Node)  

Chcete se ještě na něco zeptat? [Vyzkoušejte komunitu Power BI](http://community.powerbi.com/)
