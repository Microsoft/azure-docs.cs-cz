---
title: Logika opakování v sadě Media Services SDK pro .NET | Microsoft Docs
description: Téma obsahuje přehled logiky opakování v sadě Media Services SDK pro .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: fe37c9f7727535a292b029c342aa0e85ce0a86ec
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644117"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika opakování v sadě Media Services SDK pro .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Při práci s Microsoft Azure Services může docházet k přechodným chybám. Pokud dojde k přechodným chybám, ve většině případů po několika opakovaných pokusech operace proběhne úspěšně. Sada Media Services SDK pro .NET implementuje logiku opakování, která zpracovává přechodné chyby spojené s výjimkami a chybami, které jsou způsobené webovými požadavky, spouští dotazy, ukládají změny a operace úložiště.  Ve výchozím nastavení spustí sada Media Services SDK pro .NET čtyři opakované pokusy před opětovnou vyvoláním výjimky do vaší aplikace. Kód v aplikaci musí následně zpracovat tuto výjimku správně.  

 Následuje stručný návod pro zásady webového požadavku, úložiště, dotazu a SaveChanges:  

* Zásady úložiště se používají pro operace BLOB Storage (nahrávání nebo stahování souborů assetů).  
* Zásada webového požadavku se používá pro obecné webové požadavky (například pro získání ověřovacího tokenu a k překladu koncového bodu clusteru uživatelů).  
* Zásada dotazu se používá pro dotazování entit z REST (například mediaContext. assets. Where (...)).  
* Zásada SaveChanges se používá pro cokoli, co mění data v rámci služby (například vytvořením entity, která aktualizuje entitu, volání funkce služby pro operaci).  
  
  Toto téma obsahuje seznam typů výjimek a kódů chyb, které jsou zpracovávány Media Services SDK pro logiku opakování rozhraní .NET.  

## <a name="exception-types"></a>Typy výjimek
Následující tabulka popisuje výjimky, které sada Media Services SDK pro .NET zpracovává nebo nezpracovává u některých operací, které mohou způsobit přechodné chyby.  

| Výjimka | Webový požadavek | Storage | Dotaz | Metody |
| --- | --- | --- | --- | --- |
| WebException<br/>Další informace najdete v části [stavové kódy WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) . |Yes |Yes |Yes |Yes |
| DataServiceClientException<br/> Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Yes |Yes |Yes |
| DataServiceQueryException<br/> Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Yes |Yes |Yes |
| DataServiceRequestException<br/> Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Yes |Yes |Yes |
| DataServiceTransportException |No |No |Yes |Yes |
| TimeoutException |Yes |Yes |Yes |No |
| SocketException |Yes |Yes |Yes |Yes |
| StorageException |No |Yes |No |No |
| IOException |No |Yes |No |No |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Stavové kódy WebException
Následující tabulka uvádí, pro které kódy chyb WebException je logika opakování implementovaná. Výčet [WebExceptionStatus rovným](/dotnet/api/system.net.webexceptionstatus) definuje stavové kódy.  

| Status | Webový požadavek | Storage | Dotaz | Metody |
| --- | --- | --- | --- | --- |
| ConnectFailure |Yes |Yes |Yes |Yes |
| NameResolutionFailure |Yes |Yes |Yes |Yes |
| ProxyNameResolutionFailure |Yes |Yes |Yes |Yes |
| SendFailure |Yes |Yes |Yes |Yes |
| PipelineFailure |Yes |Yes |Yes |No |
| ConnectionClosed |Yes |Yes |Yes |No |
| KeepAliveFailure |Yes |Yes |Yes |No |
| UnknownError |Yes |Yes |Yes |No |
| ReceiveFailure |Yes |Yes |Yes |No |
| RequestCanceled |Yes |Yes |Yes |No |
| Časový limit |Yes |Yes |Yes |No |
| ProtocolError <br/>Opakování na ProtocolError se řídí zpracováním kódu stavu HTTP. Další informace najdete v tématu [stavové kódy chyb protokolu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Yes |Yes |Yes |Yes |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Stavové kódy chyb HTTP
Když operace Query a SaveChanges vyvolají DataServiceClientException, DataServiceQueryException nebo DataServiceQueryException, vrátí se kód stavu chyby HTTP ve vlastnosti StatusCode.  Následující tabulka uvádí, které chybové kódy jsou implementací logiky opakování implementovány.  

| Status | Webový požadavek | Storage | Dotaz | Metody |
| --- | --- | --- | --- | --- |
| 401 |No |Yes |No |No |
| 403 |No |Yes<br/>Zpracování opakovaných pokusů s delší dobou čekání. |No |No |
| 408 |Yes |Yes |Yes |Yes |
| 429 |Yes |Yes |Yes |Yes |
| 500 |Yes |Yes |Yes |No |
| 502 |Yes |Yes |Yes |No |
| 503 |Yes |Yes |Yes |Yes |
| 504 |Yes |Yes |Yes |No |

Pokud se chcete podívat na skutečnou implementaci logiky opakování pro sadu Media Services SDK pro .NET, přečtěte si téma [Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
