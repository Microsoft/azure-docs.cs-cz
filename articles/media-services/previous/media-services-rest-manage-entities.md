---
title: Správa subjektů mediálních služeb s REST | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak spravovat entity mediálních služeb pomocí rozhraní REST API.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a03bc24b689df342be40536c26149a7611fc5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283313"
---
# <a name="managing-media-services-entities-with-rest"></a>Správa entit Mediálních služeb pomocí REST  

> [!div class="op_single_selector"]
> * [Odpočinku](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services je služba založená na rest založené na OData v3. Entity můžete přidávat, dotazovat, aktualizovat a odstraňovat stejným způsobem jako u jakékoli jiné služby OData. Výjimky budou v příslušných případech vyvolány. Další informace o OData naleznete v [dokumentaci k protokolu Open Data Protocol](https://www.odata.org/documentation/).

Toto téma ukazuje, jak spravovat entity Azure Media Services s REST.

>[!NOTE]
> Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Dubna 2017 bude například automaticky odstraněn veškerý záznam úlohy ve vašem účtu starší než 31. Pokud potřebujete archivovat informace o úloze/úloze, můžete použít kód popsaný v tomto tématu.

## <a name="considerations"></a>Požadavky  

Při přístupu k entitám ve službě Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní AMS API, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Přidání entit
Každá entita ve službě Media Services je přidána do sady entit, například Prostředky, prostřednictvím požadavku POST HTTP.

Následující příklad ukazuje, jak vytvořit AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Dotazování entit
Dotazování a výpis entity je jednoduché a zahrnuje pouze požadavek GET HTTP a volitelné operace OData.
Následující příklad načte seznam všech entit MediaProcessor.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Můžete také načíst určitou entitu nebo všechny sady entit přidružené k určité entitě, například v následujících příkladech:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Následující příklad vrátí pouze State vlastnost všechny Jobs.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Následující příklad vrátí všechny jobtemplates s názvem "SampleTemplate."

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> Operace $expand není podporována v Media Services, stejně jako nepodporované linq metody popsané v LINQ Aspekty (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Výčet prostřednictvím velkých kolekcí entit
Při dotazování entit je limit 1000 entit vrácených najednou, protože veřejné REST v2 omezuje výsledky dotazu na 1000 výsledků. Pomocí **přeskočit** a **nahoře** výčet prostřednictvím velké kolekce entit. 

Následující příklad ukazuje, jak použít **přeskočit** a **nahoru** přeskočit první 2000 úloh a získat dalších 1000 úloh.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Aktualizace entit
V závislosti na typu entity a stavu, ve které se nachází, můžete aktualizovat vlastnosti této entity prostřednictvím požadavků HTTP PATCH, PUT nebo MERGE. Další informace o těchto operacích naleznete v tématu [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Následující příklad kódu ukazuje, jak aktualizovat vlastnost Name na entitě Asset.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Odstranění entit
Entity lze odstranit ve službě Media Services pomocí požadavku DELETE HTTP. V závislosti na entitě může být důležité pořadí, ve kterém odstraníte entity. Například entity, jako je majetek, vyžadují, abyste před odstraněním majetku odvolali (nebo odstranili) všechny lokátory, které odkazují na konkrétní majetek.

Následující příklad ukazuje, jak odstranit Lokátor, který byl použit k nahrání souboru do úložiště objektů blob.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

