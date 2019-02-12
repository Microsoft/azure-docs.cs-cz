---
title: Správa entit Media Services pomocí REST | Dokumentace Microsoftu
description: Další informace o správě entit Media Services pomocí rozhraní REST API.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 0f500ec776d90755d6738af80c34866105d354f5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999398"
---
# <a name="managing-media-services-entities-with-rest"></a>Správa entit Media Services pomocí REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services je založená na protokolu OData v3 služba založená na protokolu REST. Můžete přidat, dotazy, aktualizace a odstranění entit prakticky stejně jako jakékoli jiné služby OData. Výjimky zavolá navýšení kapacity v případě potřeby. Další informace o protokolu OData najdete v tématu [Open Data Protocol dokumentaci](http://www.odata.org/documentation/).

Toto téma ukazuje, jak spravovat entity Azure Media Services pomocí REST.

>[!NOTE]
> Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Například na 1. dubna 2017 se libovolný záznam úlohy ve vašem účtu, který je starší než 31. prosince 2016, se automaticky odstraní. Pokud potřebujete informace o úlohách/úkolech archivovat, můžete použít kód popsaný v tomto tématu.

## <a name="considerations"></a>Požadavky  

Při přístupu k entity ve službě Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API pro AMS, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Přidání entit
Každá entita ve službě Media Services je přidána do sady entit, jako je například prostředky, prostřednictvím požadavku POST HTTP.

Následující příklad ukazuje, jak vytvořit AccessPolicy.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Dotazování entit
Dotazování a výpis entit je jednoduché a zahrnuje pouze žádosti GET HTTP a volitelné operace OData.
Následující příklad načte seznam všech MediaProcessor entit.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Můžete také načíst konkrétní entity nebo všechny sady entit, které jsou spojené s konkrétní entitu, jako v následujících příkladech:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Následující příklad vrátí pouze vlastnost stav všech úloh.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Následující příklad vrátí všechny JobTemplates s názvem "SampleTemplate."

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> $Expand operace není podporována v Media Services, jakož i nepodporované metody LINQ podle aspekty LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Výčet prostřednictvím rozsáhlých kolekcí entit
Při dotazování entit, platí limit 1 000 entit najednou vrátit, protože veřejné v2 REST omezuje výsledky dotazu na 1000 výsledky. Použití **přeskočit** a **horní** výčet prostřednictvím velkou kolekci entit. 

Následující příklad ukazuje, jak používat **přeskočit** a **horní** přeskočte prvních 2000 úlohy a další 1 000 úloh.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Aktualizace entit
V závislosti na typu entity a, který je ve stavu můžete aktualizovat vlastnosti na dané entitě prostřednictvím opravu, PUT nebo sloučit HTTP žádosti. Další informace týkající se těchto operací najdete v tématu [opravy/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Následující příklad kódu ukazuje, jak aktualizovat vlastnost název na prostředek entity.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Odstranění entit
Entity můžete odstranit ve službě Media Services pomocí požadavku DELETE HTTP. V závislosti na entitu může být důležité pořadí, ve kterém můžete odstranit entity. Například entity, jako je například prostředky vyžadují, aby odvolání (nebo odstranit) všechny lokátory, které odkazují na tuto příslušného prostředku před odstraněním Assetu.

Následující příklad ukazuje, jak odstranit Lokátor, která byla použita k nahrání souboru do úložiště objektů blob.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

