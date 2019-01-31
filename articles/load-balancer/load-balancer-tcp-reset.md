---
title: Obnovení protokolu TCP nástroje pro vyrovnávání zatížení při nečinnosti v Azure
titlesuffix: Azure Load Balancer
description: Nástroj pro vyrovnávání zatížení s obousměrný TCP RVNÍ paketů na časový limit nečinnosti
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/29/2019
ms.author: kumud
ms.openlocfilehash: 18e4a7ae5010730054dd110828c63e8418b93f39
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296915"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Nástroj pro vyrovnávání zatížení s TCP Nulováním nečinnosti (Public Preview)

Můžete použít [Load balanceru úrovně Standard](load-balancer-standard-overview.md) vytvořit předvídatelnějšího chování aplikace pro vaše scénáře tím, že pro dané pravidlo umožňuje TCP resetovat při nečinnosti. Výchozí chování pro vyrovnávání zatížení je tiše síťový toky, když je dosaženo časového limitu nečinnosti toku.  Povolení této funkce způsobí, že nástroj pro vyrovnávání zatížení k odeslání obousměrné (TCP RVNÍ paket) může resetovat TCP na časový limit nečinnosti.  Koncových bodů vaší aplikace to bude informovat, že připojení vypršela platnost a již není použitelné.  Koncové body můžete okamžitě vytvořit nové připojení v případě potřeby.

![Obnovení protokolu TCP nástroje pro vyrovnávání zatížení](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Nástroj pro vyrovnávání zatížení se vynulují funkce časového limitu nečinnosti protokolu TCP je v tuto chvíli dostupná jako veřejná verze Preview a k dispozici v omezenou sadu [oblastech](#regions). Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Změnit toto výchozí chování a odesílat TCP resetuje na časový limit nečinnosti na pravidla příchozího překladu adres, Vyrovnávání zatížení a pravidla povolit a [odchozí pravidla](https://aka.ms/lboutboundrules).  Když povolena pro každé pravidlo nástroje pro vyrovnávání zatížení odešle obousměrné TCP Reset (TCP RVNÍ pakety) do koncových bodů klienta i serveru v době nečinnosti časový limit pro všechny toky odpovídající.

Příjem paketů TCP RVNÍ koncové body odpovídající soketu okamžitě ukončena. Tento postup umožňuje okamžité odeslání oznámení do koncových bodů, které vydání sady připojení došlo k chybě a všechny budoucí komunikaci na stejné připojení TCP se nezdaří.  Aplikace může odstranit připojení, když soket nezavře a znovu vytvořit připojení podle potřeby a bez čekání na připojení TCP se nakonec vypršení časového limitu.

Pro většinu scénářů to může snížit nutnost odesílání TCP (nebo aplikační vrstva) keepalive aktualizovat časový limit nečinnosti toku. 

Pokud vaše nečinnosti dob trvání překračují povolené konfigurací nebo vaše aplikace zobrazuje nežádoucí chování se resetuje TCP povolený, může stále muset použít keepalive TCP (nebo keepalive vrstvy aplikace) k monitorování aktivity připojení TCP.  Dále keepalive lze také budou fungovat i když je připojení směrovány přes proxy server někde v cestě, zejména keepalive vrstvy aplikace.  

Pečlivě zkontrolujte celý scénář koncového rozhodnout, jestli můžete využívat povolení TCP obnoví nastavení časového limitu nečinnosti, a pokud může být nutné další kroky k zajištění chování požadované aplikace.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Povoluje se na časový limit nečinnosti TCP Reset

Pomocí rozhraní API verze 2018-07-01, můžete povolit odesílání obousměrné TCP resetuje na časový limit nečinnosti na základě za pravidla:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> Regionální dostupnost

K dispozici ve všech oblastech.

## <a name="limitations"></a>Omezení

- Portál nelze použít ke konfiguraci nebo zobrazit TCP Reset.  Použijte šablony, rozhraní REST API, Az CLI 2.0 nebo prostředí PowerShell.

## <a name="next-steps"></a>Další postup

- Další informace o [Load balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o [odchozí pravidla](load-balancer-outbound-rules-overview.md).
