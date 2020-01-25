---
title: Podpora protokolů hlaviček protokolu HTTP ve službě Azure front-dveří | Microsoft Docs
description: Tento článek popisuje protokoly protokolu HTTP, které služba front-dveří podporuje.
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
ms.openlocfilehash: 7c77527b7300c1149e96c94a4dbe122da226ac6d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720431"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Podpora protokolů hlaviček protokolu HTTP ve službě Azure front-dveří
Tento článek popisuje protokol, který služba front-dveří podporuje s částmi cesty volání (viz obrázek). V následujících částech najdete další informace o hlavičkách HTTP, které podporuje služba front-dveří.

![Protokol hlaviček HTTP služby Azure front-dveří][1]

>[!IMPORTANT]
>Služba front-dveří neosvědčuje žádné hlavičky HTTP, které tady nejsou popsané.

## <a name="client-to-front-door-service"></a>Klient ke službě front-dveří
Služba front-dveří přijímá většinu hlaviček od příchozího požadavku beze změny. Některá rezervovaná záhlaví jsou při odeslání odebrána z příchozího požadavku, včetně hlaviček s předponou X-FD-*.

## <a name="front-door-service-to-backend"></a>Služba front-endu do back-endu

Služba front-dveří obsahuje hlavičky z příchozího požadavku, pokud se neodebere z důvodu omezení. Přední dvířka také přidávají následující hlavičky:

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| Via |  Přes: 1,1 Azure </br> Přední dvířka přidávají verzi HTTP klienta následovanou *Azure* jako hodnotu pro hlavičku Via. To znamená, že verze protokolu HTTP klienta a tato přední dvířka byla zprostředkujícím příjemcem pro požadavek mezi klientem a back-endu.  |
| X-Azure-ClientIP | X-Azure-IP adresa klienta: 127.0.0.1 </br> Představuje IP adresu klienta přidruženou k zpracovávané žádosti. Například žádost přicházející z proxy serveru může přidat hlavičku X-pro, která označuje IP adresu původního volajícího. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Představuje IP adresu soketu přidruženou k připojení TCP, ze kterého pochází aktuální žádost. IP adresa klienta žádosti se nemusí shodovat s IP adresou soketu, protože může být libovolně přepsána uživatelem.|
| X – Azure – ref |  X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Jedinečný řetězec odkazu, který identifikuje požadavek obsluhující předními dveřmi. Slouží k vyhledávání protokolů přístupu a kritických pro řešení potíží.|
| X-Azure-RequestChain |  X-Azure-RequestChain: směrování = 1 </br> Záhlaví, které používá přední dvířka k detekci smyček požadavků a uživatel by na něj neměli mít závislost. |
| X-předané – pro | Předané X-pro: 127.0.0.1 </br> Pole hlavička protokolu HTTP X-XFF () často identifikuje původní IP adresu klienta připojujícího se k webovému serveru prostřednictvím proxy serveru HTTP nebo nástroje pro vyrovnávání zatížení. Pokud existuje existující hlavička XFF, pak přední vrátka připojí k ní IP adresu soketu klienta nebo přidá hlavičku XFF s IP adresou soketu klienta. |
| X-Forwarded-Host | X-předaný-Host: contoso.azurefd.net </br> Pole hlavička protokolu HTTP předávaného serverem X je společná metoda používaná k identifikaci původního hostitele požadovaného klientem v hlavičce požadavku HTTP hostitele. Důvodem je to, že se název hostitele z front-endu může lišit pro back-end Server, který požadavek zpracovává. |
| X-Forwarded-Proto | X-předané – proto: http </br> Pole hlavičky HTTP s přesměrováním se často používá k identifikaci původního protokolu požadavku HTTP, protože přední dveře na základě konfigurace můžou komunikovat s back-endu pomocí protokolu HTTPS. To platí i v případě, že požadavek na reverzní proxy je HTTP. |
| X-FD – HealthProbe | K identifikaci sondy stavu z předních dveří se používá pole hlaviček protokolu HTTP X-FD-HealthProbe. Pokud je tato hlavička nastavená na 1, je žádost sonda stavu. V případě, že chcete získat striktní přístup z konkrétních front a polí s hlavičkou předávaného přes X-host, můžete použít. |

## <a name="front-door-service-to-client"></a>Služba front-dveří pro klienta

Všechny hlavičky odeslané do front-endu z back-endu jsou také předány klientovi. Níže jsou hlavičky odeslané z front-dveří klientům.

| Hlavička  | Příklad: |
| ------------- | ------------- |
| X – Azure – ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Toto je jedinečný referenční řetězec, který identifikuje požadavek poskytovaný předními dveřmi. To je důležité pro řešení potíží, které se používá k vyhledávání protokolů přístupu.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření front-dveří](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
