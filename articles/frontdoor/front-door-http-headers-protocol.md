---
title: Služba Azure branou – hlavičky protokolu HTTP podporují | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit podporovaných protokolů záhlaví HTTP pomocí přední dveře
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
ms.openlocfilehash: 40bfdfc3837da12f62864433508482a65def291c
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407313"
---
# <a name="azure-front-door-service---http-headers-support"></a>Služba Azure branou – podpora hlavičky protokolu HTTP
Tento dokument popisuje, jak protokol, který branou služby Azure podporuje s různými částmi volání cesty, jak je uvedeno v následujícím obrázku. Následující části poskytují lepší přehled o hlavičky protokolu HTTP, který podporuje branou.

![Azure hlavičky protokolu HTTP služby branou][1]

>[!WARNING]
>Služba Azure branou neposkytuje záruky v záhlaví HTTP, které tady nejsou uvedené.

## <a name="1-client-to-front-door"></a>1. Klient branou
Přední dveře přijímá většina hlaviček z příchozího požadavku (beze změny jejich), existují však některé vyhrazené hlavičky, které budou odebrány z příchozího požadavku, pokud jsou odesílány. To zahrnuje záhlaví s následující předpony:
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Přední dveře k back-endu

Přední dveře bude obsahovat záhlaví z příchozího požadavku, pokud byly odebrány z důvodu omezení uvedených výše. Přední dveře taky se přidá následující hlavičky:

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| Přes |  *Prostřednictvím: 1.1 azure* </br> Přední dveře přidá verze klienta protokolu HTTP, za nímž následuje "Azure" jako hodnota pro průchozí záhlaví. Je přidán k označení verze klienta protokolu HTTP a dveří Front Azure se zprostředkující příjemce pro žádost mezi klientem a back-endu.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Představuje "client" IP adresa přidružená k požadavek zpracovávaný. Požadavek pocházející z proxy serveru může například přidat hlavičku X-předané-pro určení IP adresy volajícího původní. |
| X-Azure-SocketIP | *X-Azure-SocketIP: 127.0.0.1* </br> Představuje adresu IP soketu spojenou s připojením TCP, pochází z aktuálního požadavku. Klient IP adresu požadavku nemusí být rovna Socket IP adresu, protože to může být libovolně přepsána koncový uživatel.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Toto je odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou. Je velmi důležité pro řešení potíží, protože se používá pro přístup k protokolům vyhledávání.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: hops=1* </br> Toto je hlavičku, která branou využívá ke zjištění smyčky žádosti a uživatelé neměla by mít závislost na něm. |
| X-Forwarded-For | *X-předané pro: 127.0.0.1* </br> Pole hlavičky X-Forwarded-For (XFF) protokolu HTTP se o běžnou metodu pro určení zdrojovou IP adresu klienta připojení k webovým serverem prostřednictvím HTTP proxy server nebo službu Vyrovnávání zatížení. Pokud došlo stávajícího XFF záhlaví, pak branou připojí socket IP adresu klienta do jinde přidá hlavičku XFF socket IP adresu klienta. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> Pole hlavičky HTTP X předané hostiteli se o běžnou metodu pro určení původního hostitele vyžádaného klientem v hlavičce žádosti HTTP na hostitele, protože název hostitele z branou se může lišit pro back-end serveru, který zpracovává požadavek. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Pole hlavičky HTTP X-Forwarded-Proto je běžnou metodu pro určení původní protokol požadavku HTTP, protože v závislosti na konfiguraci může branou komunikovat s back-end pomocí protokolu HTTPS, i když je požadavek na reverzní proxy server HTTP. |

## <a name="3-front-door-to-client"></a>3. Přední dveře do klienta

Následující části jsou hlavičky, která odesílají z přední dveře do klientů. Záhlaví odeslaných branou z back-end jsou předány také klienta.

| Hlavička  | Příklad: |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Toto je odkaz na jedinečný řetězec, který identifikuje požadavek obsluhuje branou. Je velmi důležité pro řešení potíží, protože se používá pro přístup k protokolům vyhledávání.|

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png