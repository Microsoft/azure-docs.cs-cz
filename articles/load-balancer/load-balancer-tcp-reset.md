---
title: Load Balancer resetování TCP a vypršení časového limitu nečinnosti v Azure
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak Azure Load Balancer s obousměrnými pakety TCP RST při nečinnosti.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/09/2019
ms.author: allensu
ms.openlocfilehash: f77dd21a2c017ee41f955fdf5e0848df190dec2a
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651271"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer resetování TCP a časový limit nečinnosti

Pomocí [Standard Load Balancer](load-balancer-standard-overview.md) můžete pro své scénáře vytvořit předvídatelné chování aplikace tím, že pro dané pravidlo povolíte resetování protokolu TCP pro nečinnost. Výchozím chováním Load Balancer je nejenom tiché vyřazení toků při dosažení časového limitu nečinnosti toku.  Povolení této funkce způsobí, že Load Balancer odesílat obousměrné resety TCP (TCP RST Packet) na časový limit nečinnosti.  Tím se informují koncové body vaší aplikace, ke kterým vypršel časový limit připojení a které už nejsou použitelné.  Koncové body můžou v případě potřeby okamžitě vytvořit nové připojení.

![Resetování protokolu TCP Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Resetování protokolu TCP

Toto výchozí chování můžete změnit a povolit odesílání resetů TCP na časový limit nečinnosti u příchozích pravidel NAT, pravidel vyrovnávání zatížení a [odchozích pravidel](https://aka.ms/lboutboundrules).  Pokud je povoleno podle pravidla, Load Balancer odešle obousměrné resetování TCP (pakety TCP RST) do koncových bodů klienta i serveru v době nečinnosti u všech vyhovujících toků.

Koncové body, které obdrží pakety TCP RST, okamžitě zavřou příslušný soket. To poskytuje okamžité oznámení koncovým bodům, k nimž došlo k vydání připojení, a veškerá budoucí komunikace se stejným připojením TCP selže.  Aplikace mohou vyprázdnit připojení v případě, že soket zavře a znovu vytvoří připojení podle potřeby, aniž by čekali na vypršení časového limitu připojení TCP.

V mnoha scénářích to může snížit nutnost odesílání nečinných dat toku TCP (nebo aplikační vrstvy), aby se obnovil časový limit nečinnosti. 

Pokud vaše doby nečinnosti překračují hodnoty povolené konfigurací nebo pokud vaše aplikace zobrazuje nežádoucí chování s povolenými obnovenými omezeními protokolu TCP, může být stále nutné použít kontroly stavu protokolu TCP (nebo neaktivních zobrazení vrstvy aplikace) a monitorovat tak živý výkon připojení TCP.  V případě, že se připojení proxy serverem nachází v cestě, může to být také užitečné i v případě, že se připojení využije, zejména v případě nečinnosti aplikační vrstvy.  

Pečlivě Projděte celý scénář od konce až do konce, abyste se rozhodli, jestli vám doporučujeme povolit resetování protokolu TCP, upravit časový limit nečinnosti a případně provést další kroky, abyste zajistili, že se chování aplikace požaduje.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurovatelný časový limit nečinnosti protokolu TCP

Azure Load Balancer má nastavení časového limitu nečinnosti na 4 minuty až 120 minut. Ve výchozím nastavení je nastaveno na 4 minuty. Pokud je období neaktivity delší než hodnota časového limitu, není nijak zaručeno, že relace TCP nebo HTTP mezi klientem a vaší cloudovou službou bude zachovaná.

Po zavření připojení může klientská aplikace zobrazit následující chybovou zprávu: "základní připojení bylo zavřeno: připojení, které bylo očekáváno jako aktivní, bylo zavřeno serverem."

Běžným postupem je používání udržování připojení TCP. Tento postup zachovává aktivní připojení po delší dobu. Další informace najdete v těchto [příkladech rozhraní .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Když je povolená možnost Keep-Alive, pakety se odešlou během období nečinnosti na připojení. Pakety Keep-Alive zajišťují, že hodnota časového limitu nečinnosti není dosažena a připojení je udržováno po dlouhou dobu.

Nastavení funguje jenom pro příchozí připojení. Aby nedošlo ke ztrátě připojení, nakonfigurujte udržování připojení TCP pomocí intervalu kratšího, než je nastavení časového limitu nečinnosti, nebo zvyšte hodnotu časového limitu nečinnosti. Pro podporu těchto scénářů byla přidána podpora konfigurovatelného časového limitu nečinnosti.

Udržování připojení TCP funguje ve scénářích, kdy životnost baterie není omezením. Nedoporučuje se pro mobilní aplikace. Při použití udržování připojení TCP v mobilní aplikaci můžete baterii zařízení rychleji vyprázdnit.


## <a name="limitations"></a>Omezení

- Resetování TCP se odesílá jenom během navázání připojení TCP.
- Pro interní nástroje pro vyrovnávání zatížení s nakonfigurovanými porty HA se neposílá TCP resetování.
- Časový limit nečinnosti protokolu TCP nemá vliv na pravidla vyrovnávání zatížení na protokolu UDP.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si o [odchozích pravidlech](load-balancer-outbound-rules-overview.md).
- [Konfigurace TCP RST pro časový limit nečinnosti](load-balancer-tcp-idle-timeout.md)
