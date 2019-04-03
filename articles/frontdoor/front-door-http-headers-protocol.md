---
title: Protokol podporuje pro hlavičky protokolu HTTP branou služby Azure | Dokumentace Microsoftu
description: Tento článek popisuje záhlaví protokolů HTTP, které podporuje služba branou.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861838"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Podpora protokolů pro hlavičky protokolu HTTP branou služby Azure
Tento článek popisuje protokol, který podporuje branou služby s částí cesty volání (viz obrázek). Následující části obsahují další informace o podporované službou branou hlavičky protokolu HTTP.

![Azure hlavičky protokolu HTTP služby branou][1]

>[!IMPORTANT]
>Branou služby není certifikovat záhlaví HTTP, které tady nejsou uvedené.

## <a name="client-to-front-door-service"></a>Klient branou služby
Přední dveře služba přijímá beze změny, je většina hlaviček z příchozího požadavku. Některé vyhrazené hlavičky budou odebrány z příchozího požadavku v případě odeslaný, včetně záhlaví x - FD-* předponu.

## <a name="front-door-service-to-backend"></a>Služba přední dveře k back-endu

Branou služby zahrnuje záhlaví z příchozího požadavku, pokud je odebrán z důvodu omezení. Přední dveře také přidá následující hlavičky:

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| Přes |  Via: 1.1 azure </br> Verze klienta protokolu HTTP, za nímž následuje přidá branou *Azure* hodnotu hlavičky Via. Označuje verzi klienta protokolu HTTP a že branou byl zprostředkující příjemce pro žádost mezi klientem a back-endu.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Představuje IP adresu klienta, který je přidružený k požadavku právě zpracovává. Požadavek pocházející z proxy serveru může například přidat hlavičku X-předané-pro určení IP adresy původní volajícího. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Představuje socket IP adresu spojenou s připojením TCP, který aktuální požadavek pochází z. IP adresa požadavku klienta nemusí být rovna socket IP adresu, protože může být libovolně přepsat uživatelem.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou. Používá se pro přístup k protokolům vyhledávání a kritické pro řešení potíží.|
| X-Azure-RequestChain |  X-Azure-RequestChain: segmentů směrování = 1 </br> Záhlaví, že branou využívá ke zjištění smyčky žádosti a uživatelé neměla by mít závislost na něj. |
| X-Forwarded-For | X-předané pro: 127.0.0.1 </br> Pole hlavičky X-Forwarded-For (XFF) HTTP často identifikuje zdrojovou IP adresu klienta připojení k webovým serverem prostřednictvím HTTP proxy server nebo službu Vyrovnávání zatížení. Pokud je existující záhlaví XFF, branou připojí socket IP adresu klienta do ní nebo přidá hlavičku XFF socket IP adresu klienta. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Pole hlavičky HTTP X předané hostiteli se o běžnou metodu, která slouží k identifikaci původní hostitele vyžádaného klientem v hlavičce žádosti HTTP na hostitele. Je to proto, že název hostitele z branou se může lišit pro back-end serveru, který zpracovává požadavek. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Pole hlavičky HTTP X-Forwarded-Proto se často používá k identifikaci původní protokol požadavek HTTP, protože branou, založená na konfiguraci, může komunikovat s back-end pomocí protokolu HTTPS. To platí i v případě, že je požadavek na reverzní proxy server HTTP. |

## <a name="front-door-service-to-client"></a>Služba branou klienta

Záhlaví odeslaných branou z back-end jsou také předává do klienta. Níže jsou hlavičky posílané z přední dveře do klientů.

| Hlavička  | Příklad: |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Toto je odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou. To je zásadní pro řešení potíží, protože se používá pro přístup k protokolům vyhledávání.|

## <a name="next-steps"></a>Další postup

- [Vytvoření služby Front Door](quickstart-create-front-door.md)
- [Jak funguje branou](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png