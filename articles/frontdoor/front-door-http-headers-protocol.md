---
title: Podpora protokolů hlaviček protokolu HTTP v front-dveřích Azure | Microsoft Docs
description: Tento článek popisuje protokoly protokolu HTTP, které front-Dvířk podporuje.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785752"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Podpora protokolů hlaviček protokolu HTTP v front-dveřích Azure
Tento článek popisuje protokol, který přední dveře podporuje s částmi cesty volání (viz obrázek). V následujících částech najdete další informace o hlavičkách HTTP podporovaných předními dvířky.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protokol hlaviček HTTP služby Azure front-dveří":::

>[!IMPORTANT]
>Přední dveře nepotvrzují žádné hlavičky HTTP, které tady nejsou popsané.

## <a name="client-to-front-door"></a>Klient do front-dveří
Přední dvířka přijímají většinu hlaviček pro příchozí požadavek beze změny. Některá rezervovaná záhlaví jsou při odeslání odebrána z příchozího požadavku, včetně hlaviček s předponou X-FD-*.

## <a name="front-door-to-backend"></a>Přední dveře do back-endu

Pokud nejsou odebrány z důvodu omezení, obsahují přední dvířka hlavičky pro příchozí požadavek. Přední dvířka také přidávají následující hlavičky:

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| Vedení |  *Přes: 1,1 Azure* </br> Přední dvířka přidávají verzi HTTP klienta následovanou *Azure* jako hodnotu pro hlavičku Via. Tato hlavička označuje verzi protokolu HTTP klienta a tato přední dvířka byla zprostředkujícím příjemcem pro požadavek mezi klientem a back-endu.  |
| X – Azure – IP adresa klienta | *X-Azure-IP adresa klienta: 127.0.0.1* </br> Představuje IP adresu klienta přidruženou k zpracovávané žádosti. Například žádost přicházející z proxy serveru může přidat hlavičku X-pro, která označuje IP adresu původního volajícího. |
| X – Azure – SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Představuje IP adresu soketu přidruženou k připojení TCP, ze kterého pochází aktuální žádost. IP adresa klienta žádosti se nemusí shodovat s IP adresou soketu, protože IP adresa klienta může být libovolně přepsána uživatelem.|
| X – Azure – ref | *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jedinečný řetězec odkazu, který identifikuje požadavek obsluhující předními dveřmi. Slouží k vyhledávání protokolů přístupu a kritických pro řešení potíží.|
| X – Azure – RequestChain | *X-Azure-RequestChain: směrování = 1* </br> Záhlaví, které používá přední dvířka k detekci smyček požadavků a uživatel by na něj neměli mít závislost. |
| X – Azure – FDID | *X-Azure – FDID: 55ce4ed1-4B06-4bf1-B40E-4638452104da* <br/> Řetězec odkazu, který identifikuje požadavek z konkrétního prostředku front-dveří. Hodnotu lze zobrazit v Azure Portal nebo načíst pomocí rozhraní API pro správu. V kombinaci s ACL protokolu IP můžete použít tuto hlavičku k uzamknutí koncového bodu tak, aby přijímal pouze požadavky z konkrétního prostředku front-dveří. [Další podrobnosti](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) najdete v nejčastějších dotazech. |
| X-předané – pro | *Předané X-pro: 127.0.0.1* </br> Pole hlavička protokolu HTTP X-XFF () často identifikuje původní IP adresu klienta připojujícího se k webovému serveru prostřednictvím proxy serveru HTTP nebo nástroje pro vyrovnávání zatížení. Pokud existuje existující hlavička XFF, pak přední vrátka připojí k ní IP adresu soketu klienta nebo přidá hlavičku XFF s IP adresou soketu klienta. |
| X-předávaný-Host | *X-předaný-Host: contoso.azurefd.net* </br> Pole hlavička protokolu HTTP předávaného serverem X je společná metoda používaná k identifikaci původního hostitele požadovaného klientem v hlavičce požadavku HTTP hostitele. Důvodem je to, že se název hostitele z front-endu může lišit pro back-end Server, který požadavek zpracovává. |
| X-předáno – proto | *X-předané – proto: http* </br> Pole záhlaví HTTP s přesměrováním za běhu se často používá k identifikaci původního protokolu požadavku HTTP. Přední dveře založené na konfiguraci můžou komunikovat s back-endu pomocí protokolu HTTPS. To platí i v případě, že požadavek na reverzní proxy je HTTP. |
| X-FD – HealthProbe | K identifikaci sondy stavu z předních dveří se používá pole hlaviček protokolu HTTP X-FD-HealthProbe. Pokud je tato hlavička nastavená na 1, je žádost sonda stavu. V případě, že chcete získat striktní přístup z konkrétních front a polí s hlavičkou předávaného přes X-host, můžete použít. |
| X – Azure – FDID | *X-Azure-FDID hlavička: 437c82cd-360A-4a54-94c3-5ff707647783* </br> Toto pole obsahuje frontdoorID, které se dá použít k identifikaci, ze kterého front-dveří je příchozí požadavek. Toto pole vyplní služba front-dveří. | 

## <a name="front-door-to-client"></a>Přední dveře klienta

Všechny hlavičky odeslané do front-endu z back-endu jsou také předány klientovi. Níže jsou hlavičky odeslané z front-dveří klientům.

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| X – Azure – ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Toto je jedinečný referenční řetězec, který identifikuje požadavek poskytovaný předními dveřmi, což je důležité pro řešení potíží, které se používá k vyhledávání protokolů přístupu.|
| X – mezipaměť | *X-mezipaměť: TCP_HIT* </br> Tato hlavička popisuje stav mezipaměti požadavku, který umožňuje určit, jestli se má obsah odpovědi dodávat z mezipaměti frontových dveří. |

Aby bylo možné povolit následující volitelné hlavičky odpovědí, je třeba odeslat hlavičku žádosti X-Azure-DebugInfo: 1.

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| X – Azure – OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Tato hlavička obsahuje stavový kód HTTP vrácený back-end. Pomocí této hlavičky můžete identifikovat stavový kód HTTP vrácený aplikací spuštěnou v back-endu, aniž byste museli procházet protokoly back-endu. Tento stavový kód se může lišit od stavového kódu HTTP v odpovědi odeslané klientovi přes dvířka. Tato hlavička vám umožní určit, jestli se back-end nepoužívá, nebo pokud se jedná o problém s frontou dvířek. |
| X – Azure – InternalError | Tato hlavička bude obsahovat kód chyby, ke kterému dojdou přední dveře při zpracování žádosti. Tato chyba označuje, že problém je interní pro službu nebo infrastrukturu front-dveří. Nahlaste problém pro podporu.  |
| X – Azure – ExternalError | *X-Azure-ExternalError: 0x830c1011, certifikační autorita není známá.* </br> V této hlavičce se zobrazuje kód chyby, který jsou předními servery při vytváření připojení k back-end serveru pro zpracování žádosti. Tato hlavička pomůže identifikovat problémy v souvislosti s připojením mezi předními a back-endové aplikací. Tato hlavička bude obsahovat podrobnou chybovou zprávu, která vám pomůže identifikovat problémy s připojením k back-endu (například překlad DNS, neplatný certifikát atd.). |

## <a name="next-steps"></a>Další kroky

- [Vytvoření služby Front Door](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)
