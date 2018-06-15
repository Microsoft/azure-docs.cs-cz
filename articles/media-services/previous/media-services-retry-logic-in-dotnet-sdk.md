---
title: Opakujte logiky sady Media Services SDK pro .NET | Microsoft Docs
description: Téma poskytuje přehled logika opakovaných pokusů v sady Media Services SDK pro .NET.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 34125712c59938b3a74e7cdc150f3f16b694b92f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790423"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Opakujte logiky sady Media Services SDK pro .NET
Při práci se službou Microsoft Azure, může dojít přechodných. Pokud dojde k přechodná chyba, ve většině případů po několika pokusech operace proběhla úspěšně. Media Services SDK pro .NET implementuje logiku opakovaných pokusů pro zpracování přechodné chyby související s výjimek a chyb, které jsou způsobeny webových požadavků, provádění dotazů, ukládání změn a operace úložiště.  Ve výchozím nastavení spustí sadu Media Services SDK pro .NET do čtyř opakovaných vyhledávání před znovu způsobující výjimku do vaší aplikace. Kód ve vaší aplikaci pak musí správně zpracovat výjimku.  

 Toto je stručný platí webovému požadavku, úložiště, dotazů a SaveChanges zásad:  

* Zásada úložiště se používá pro operace úložiště objektů blob (nahrávání nebo stahování souborů asset).  
* Zásada webovému požadavku se používá pro obecné webových požadavků (například pro získání tokenu ověřování a řešení koncový bod clusteru uživatele).  
* Zásada dotazu se používá k dotazování entity z REST (například mediaContext.Assets.Where(...)).  
* Zásady SaveChanges slouží k provádění všechno, co změny dat v rámci služby (například vytváření entity aktualizaci entity, volání funkce služby pro operace).  
  
  Toto téma uvádí typy výjimek a chybové kódy, které jsou zpracovávány sady Media Services SDK pro .NET logika opakovaných pokusů.  

## <a name="exception-types"></a>Typy výjimek
Následující tabulka popisuje výjimky, které zpracovává sadu Media Services SDK pro .NET nebo nezpracovává pro některé operace, které může způsobit přechodné chyby.  

| Výjimka | Webové žádosti | Úložiště | Dotaz | SaveChanges |
| --- | --- | --- | --- | --- |
| Výjimku WebException<br/>Další informace najdete v tématu [výjimku WebException stavové kódy](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) části. |Ano |Ano |Ano |Ano |
| DataServiceClientException<br/> Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| DataServiceQueryException<br/> Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| DataServiceRequestException<br/> Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ne |Ano |Ano |Ano |
| DataServiceTransportException |Ne |Ne |Ano |Ano |
| TimeoutException |Ano |Ano |Ano |Ne |
| SocketException |Ano |Ano |Ano |Ano |
| StorageException |Ne |Ano |Ne |Ne |
| Výjimka vstupu/výstupu |Ne |Ano |Ne |Ne |

### <a name="WebExceptionStatus"></a> Výjimku WebException stavové kódy
Následující tabulka uvádí, pro které kódy chyb výjimku WebException je implementováno logika opakovaných pokusů. [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) výčet definuje stavové kódy.  

| Status | Webové žádosti | Úložiště | Dotaz | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ano |Ano |Ano |Ano |
| NameResolutionFailure |Ano |Ano |Ano |Ano |
| ProxyNameResolutionFailure |Ano |Ano |Ano |Ano |
| SendFailure |Ano |Ano |Ano |Ano |
| PipelineFailure |Ano |Ano |Ano |Ne |
| ConnectionClosed |Ano |Ano |Ano |Ne |
| KeepAliveFailure |Ano |Ano |Ano |Ne |
| Neznámé chyby |Ano |Ano |Ano |Ne |
| ReceiveFailure |Ano |Ano |Ano |Ne |
| RequestCanceled |Ano |Ano |Ano |Ne |
| Vypršení časového limitu |Ano |Ano |Ano |Ne |
| Požadavku <br/>Opakování v požadavku je řízena zpracování kód stavu HTTP. Další informace najdete v tématu [stavové kódy HTTP Chyba](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ano |Ano |Ano |Ano |

### <a name="HTTPStatusCode"></a> Stavové kódy chyb HTTP
Když operace dotazů a SaveChanges throw DataServiceClientException, DataServiceQueryException nebo DataServiceQueryException, se ve vlastnosti StatusCode vrátí stavový kód chyby HTTP.  Následující tabulka uvádí, pro které kódy chyb je implementováno logika opakovaných pokusů.  

| Status | Webové žádosti | Úložiště | Dotaz | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Ne |Ano |Ne |Ne |
| 403 |Ne |Ano<br/>Zpracování opakování s delší čeká. |Ne |Ne |
| 408 |Ano |Ano |Ano |Ano |
| 429 |Ano |Ano |Ano |Ano |
| 500 |Ano |Ano |Ano |Ne |
| 502 |Ano |Ano |Ano |Ne |
| 503 |Ano |Ano |Ano |Ano |
| 504 |Ano |Ano |Ano |Ne |

Pokud se chcete podívat na skutečném provádění sady Media Services SDK pro .NET logika opakovaných pokusů, najdete v části [azure-sdk pro media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

