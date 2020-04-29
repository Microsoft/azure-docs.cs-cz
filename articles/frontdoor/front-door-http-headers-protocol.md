---
title: Podpora protokolů hlaviček protokolu HTTP v front-dveřích Azure | Microsoft Docs
description: Tento článek popisuje protokoly protokolu HTTP, které front-Dvířk podporuje.
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
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471672"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Podpora protokolů hlaviček protokolu HTTP v front-dveřích Azure
Tento článek popisuje protokol, který přední dveře podporuje s částmi cesty volání (viz obrázek). V následujících částech najdete další informace o hlavičkách HTTP podporovaných předními dvířky.

![Protokol hlaviček HTTP služby Azure front-dveří][1]

>[!IMPORTANT]
>Přední dveře nepotvrzují žádné hlavičky HTTP, které tady nejsou popsané.

## <a name="client-to-front-door"></a>Klient do front-dveří
Přední dvířka přijímají většinu hlaviček z příchozího požadavku bez změny. Některá rezervovaná záhlaví jsou při odeslání odebrána z příchozího požadavku, včetně hlaviček s předponou X-FD-*.

## <a name="front-door-to-backend"></a>Přední dveře do back-endu

Přední dvířka obsahují záhlaví z příchozího požadavku, pokud nejsou odebrána z důvodu omezení. Přední dvířka také přidávají následující hlavičky:

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| Vedení |  Přes: 1,1 Azure </br> Přední dvířka přidávají verzi HTTP klienta následovanou *Azure* jako hodnotu pro hlavičku Via. Tato hlavička označuje verzi protokolu HTTP klienta a tato přední dvířka byla zprostředkujícím příjemcem pro požadavek mezi klientem a back-endu.  |
| X – Azure – IP adresa klienta | X-Azure-IP adresa klienta: 127.0.0.1 </br> Představuje IP adresu klienta přidruženou k zpracovávané žádosti. Například žádost přicházející z proxy serveru může přidat hlavičku X-pro, která označuje IP adresu původního volajícího. |
| X – Azure – SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Představuje IP adresu soketu přidruženou k připojení TCP, ze kterého pochází aktuální žádost. IP adresa klienta žádosti se nemusí shodovat s IP adresou soketu, protože může být libovolně přepsána uživatelem.|
| X – Azure – ref |  X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Jedinečný řetězec odkazu, který identifikuje požadavek obsluhující předními dveřmi. Slouží k vyhledávání protokolů přístupu a kritických pro řešení potíží.|
| X – Azure – RequestChain |  X-Azure-RequestChain: směrování = 1 </br> Záhlaví, které používá přední dvířka k detekci smyček požadavků a uživatel by na něj neměli mít závislost. |
| X-předané – pro | Předané X-pro: 127.0.0.1 </br> Pole hlavička protokolu HTTP X-XFF () často identifikuje původní IP adresu klienta připojujícího se k webovému serveru prostřednictvím proxy serveru HTTP nebo nástroje pro vyrovnávání zatížení. Pokud existuje existující hlavička XFF, pak přední vrátka připojí k ní IP adresu soketu klienta nebo přidá hlavičku XFF s IP adresou soketu klienta. |
| X-předávaný-Host | X-předaný-Host: contoso.azurefd.net </br> Pole hlavička protokolu HTTP předávaného serverem X je společná metoda používaná k identifikaci původního hostitele požadovaného klientem v hlavičce požadavku HTTP hostitele. Důvodem je to, že se název hostitele z front-endu může lišit pro back-end Server, který požadavek zpracovává. |
| X-předáno – proto | X-předané – proto: http </br> Pole hlavičky HTTP s přesměrováním se často používá k identifikaci původního protokolu požadavku HTTP, protože přední dveře na základě konfigurace můžou komunikovat s back-endu pomocí protokolu HTTPS. To platí i v případě, že požadavek na reverzní proxy je HTTP. |
| X-FD – HealthProbe | K identifikaci sondy stavu z předních dveří se používá pole hlaviček protokolu HTTP X-FD-HealthProbe. Pokud je tato hlavička nastavená na 1, je žádost sonda stavu. V případě, že chcete získat striktní přístup z konkrétních front a polí s hlavičkou předávaného přes X-host, můžete použít. |

## <a name="front-door-to-client"></a>Přední dveře klienta

Všechny hlavičky odeslané do front-endu z back-endu jsou také předány klientovi. Níže jsou hlavičky odeslané z front-dveří klientům.

| Hlavička  | Příklad |
| ------------- | ------------- |
| X – Azure – ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Toto je jedinečný referenční řetězec, který identifikuje požadavek poskytovaný předními dveřmi. To je důležité pro řešení potíží, které se používá k vyhledávání protokolů přístupu.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření služby Front Door](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
