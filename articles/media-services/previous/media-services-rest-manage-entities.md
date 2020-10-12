---
title: Správa entit Media Services pomocí REST | Microsoft Docs
description: Tento článek ukazuje, jak spravovat Media Services entit pomocí REST API.
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
ms.openlocfilehash: 95124f101b1f14a70dabedd7d44077c5c1b6e99c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264228"
---
# <a name="managing-media-services-entities-with-rest"></a>Správa entit Media Services pomocí REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services je služba založená na REST postavená na OData V3. Entity můžete přidávat, dotazovat, aktualizovat a odstraňovat, a to podobně jako u jakékoli jiné služby OData. Výjimky budou vyvolány, pokud jsou k dispozici. Další informace o OData najdete v [dokumentaci k Open Data Protocol](https://www.odata.org/documentation/).

V tomto tématu se dozvíte, jak spravovat Azure Media Services entit pomocí REST.

>[!NOTE]
> Od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 90 dní. Spolu s ním se odstraní přidružené záznamy úkolů, a to i v případě, že celkový počet záznamů je nižší než maximální kvóta. Například od 1. dubna 2017 se automaticky odstraní libovolný záznam úlohy ve vašem účtu, který je starší než 31. prosince 2016. Pokud potřebujete archivovat informace o úloze nebo úloze, můžete použít kód popsaný v tomto tématu.

## <a name="considerations"></a>Důležité informace  

Při přístupu k entitám v Media Services musíte nastavit konkrétní pole a hodnoty hlaviček v požadavcích HTTP. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

Informace o tom, jak se připojit k rozhraní API AMS, najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Přidávání entit
Každá entita v Media Services je přidána do sady entit, jako je například Assety, prostřednictvím požadavku HTTP POST.

Následující příklad ukazuje, jak vytvořit AccessPolicy.

```console
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
```

## <a name="querying-entities"></a>Dotazování entit
Dotazování a výpis entit je jednoduché a zahrnuje pouze požadavek GET HTTP a volitelné operace OData.
Následující příklad načte seznam všech entit MediaProcessor.

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Můžete také načíst konkrétní entitu nebo všechny sady entit přidružené ke konkrétní entitě, například v následujících příkladech:

```console
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
```

Následující příklad vrátí pouze vlastnost State všech úloh.

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Následující příklad vrátí všechny JobTemplates s názvem "SampleTemplate".

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> Operace $expand není podporována v Media Services a také nepodporované metody LINQ popsané v tématu požadavky LINQ (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Vytváření výčtu velkých kolekcí entit
Při dotazování entit existuje limit 1000 entit vrácených v jednom okamžiku, protože veřejné REST v2 omezuje výsledky dotazu na 1000 výsledků. Pomocí **Skip** a **Top** můžete vytvořit výčet pomocí velké kolekce entit. 

Následující příklad ukazuje, jak pomocí funkce **Skip** a **top** přeskočit první 2000 úlohy a získat další 1000 úlohy.  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>Aktualizace entit
V závislosti na typu entity a stavu, ve kterém se nachází, můžete aktualizovat vlastnosti této entity prostřednictvím oprav, vložení nebo sloučení požadavků HTTP. Další informace o těchto operacích naleznete v tématu [patch/Put/Merge](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

Následující příklad kódu ukazuje, jak aktualizovat vlastnost Name v entitě Assetu.

```console
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
```

## <a name="deleting-entities"></a>Odstraňují se entity
Entity lze v Media Services odstranit pomocí požadavku HTTP DELETE. V závislosti na entitě může být pořadí odstranění entit důležité. Například entity, například prostředky, vyžadují, abyste zrušili (nebo odstranili) všechny Lokátory, které odkazují na daný prostředek před odstraněním Assetu.

Následující příklad ukazuje, jak odstranit lokátor, který jste použili k nahrání souboru do úložiště objektů BLOB.

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
