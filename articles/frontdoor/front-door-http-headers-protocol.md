---
title: Podpora protokolu pro hlavičky HTTP v Azure Front Door | Dokumenty společnosti Microsoft
description: Tento článek popisuje protokoly záhlaví PROTOKOLU HTTP, které podporuje front door.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471672"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Podpora protokolu pro hlavičky HTTP v Azure Front Door
Tento článek popisuje protokol, který přední dveře podporují s částmi cesty volání (viz obrázek). Následující části poskytují další informace o hlavičkách PROTOKOLU HTTP podporovaných frontdoor.

![Protokol záhlaví http azure předních dveří][1]

>[!IMPORTANT]
>Přední dveře necertifikují žádné hlavičky HTTP, které zde nejsou zdokumentovány.

## <a name="client-to-front-door"></a>Klient k předním dveřím
Přední dveře přijímá většinu záhlaví z příchozí žádosti bez jejich úpravy. Některé vyhrazené hlavičky jsou odebrány z příchozí ho požadavku, pokud jsou odeslány, včetně záhlaví s předponou X-FD-*.

## <a name="front-door-to-backend"></a>Přední dveře k backendu

Přední dveře obsahuje záhlaví z příchozí žádosti, pokud není odebrána z důvodu omezení. Přední dveře také přidává následující záhlaví:

| Hlavička  | Příklad a popis |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door přidá verzi HTTP klienta následovanou *Azure* jako hodnotu hlavičky Via. Tato hlavička označuje verzi HTTP klienta a že front door byl zprostředkujícím příjemcem pro požadavek mezi klientem a back-endem.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Představuje ip adresu klienta přidruženou ke zpracování požadavku. Například požadavek přicházející z proxy může přidat x-forwarded-for záhlaví označující IP adresu původního volajícího. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Představuje adresu IP soketu přidruženou k připojení TCP, ze kterého pochází aktuální požadavek. IP adresa klienta požadavku se nemusí rovnat adrese IP jeho soketu, protože může být libovolně přepsána uživatelem.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTKANDM3YzgyY2QtMzYwYS00YTU0LTK0YzMtNWZMNzA3NjQ3Nzgzz </br> Jedinečný referenční řetězec, který identifikuje požadavek obsluhovaný front door. Používá se k vyhledávání přístupových protokolů a důležité pro řešení potíží.|
| Řetězec požadavků X-Azure- |  X-Azure-RequestChain: směrování=1 </br> Záhlaví, které používá front door ke zjišťování smyčky požadavků a uživatelé by neměli mít závislost na něm. |
| X-Forwarded-Pro | X-Forwarded-Pro: 127.0.0.1 </br> Pole hlavičky HTTP X-Forwarded-For (XFF) často identifikuje původní ADRESU IP klienta, který se připojuje k webovému serveru prostřednictvím serveru HTTP proxy nebo nástroj pro vyrovnávání zatížení. Pokud existuje existující hlavička XFF, pak front door připojí IP klientského soketu nebo přidá hlavičku XFF s IP klientským soketem. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Pole hlavičky HTTP s předsunutým hostitelem je běžná metoda používaná k identifikaci původního hostitele požadovaného klientem v hlavičce požadavku HTTP hostitele. Důvodem je, že název hostitele z front door se může lišit pro back-endový server zpracování požadavku. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Pole hlavičky HTTP X-Forwarded-Proto se často používá k identifikaci původního protokolu požadavku HTTP, protože frontdoor, na základě konfigurace, může komunikovat s back-endem pomocí protokolu HTTPS. To platí i v případě, že požadavek na reverzní proxy server je HTTP. |
| X-FD-HealthProbe | Pole hlavičky HTTP X-FD-HealthProbe se používá k identifikaci sondy stavu z předních dveří. Pokud tato hlavička nastavena na 1, požadavek je sonda stavu. Můžete použít, když chcete striktně přistupovat z konkrétních předních dveří s polem hlavičky X-Forwarded-Host. |

## <a name="front-door-to-client"></a>Přední dveře klientovi

Všechny hlavičky odeslané do front door z back-endu jsou také předány klientovi. Níže jsou záhlaví odeslaná z front door klientům.

| Hlavička  | Příklad |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTKANDM3YzgyY2QtMzYwYS00YTU0LTK0YzMtNWZMNzA3NjQ3Nzgzz* </br> Toto je jedinečný referenční řetězec, který identifikuje požadavek obsluhovaný front door. To je důležité pro řešení potíží, protože se používá k vyhledávání přístupových protokolů.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření služby Front Door](quickstart-create-front-door.md)
- [Jak fungují přední dveře](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
