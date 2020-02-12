---
title: Load Balancer resetování protokolu TCP při nečinnosti v Azure
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak Azure Load Balancer s obousměrnými pakety TCP RST při nečinnosti.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: eac7dc3b7188131685ef630c0dc01d248e1d6a6a
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134785"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Load Balancer s resetováním protokolu TCP po nečinnosti

Pomocí [Standard Load Balancer](load-balancer-standard-overview.md) můžete pro své scénáře vytvořit předvídatelné chování aplikace tím, že pro dané pravidlo povolíte resetování protokolu TCP pro nečinnost. Výchozím chováním Load Balancer je nejenom tiché vyřazení toků při dosažení časového limitu nečinnosti toku.  Povolení této funkce způsobí, že Load Balancer odesílat obousměrné resety TCP (TCP RST Packet) na časový limit nečinnosti.  Tím se informují koncové body vaší aplikace, ke kterým vypršel časový limit připojení a které už nejsou použitelné.  Koncové body můžou v případě potřeby okamžitě vytvořit nové připojení.

![Resetování protokolu TCP Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Toto výchozí chování můžete změnit a povolit odesílání resetů TCP na časový limit nečinnosti u příchozích pravidel NAT, pravidel vyrovnávání zatížení a [odchozích pravidel](https://aka.ms/lboutboundrules).  Pokud je povoleno podle pravidla, Load Balancer odešle obousměrné resetování TCP (pakety TCP RST) do koncových bodů klienta i serveru v době nečinnosti u všech vyhovujících toků.

Koncové body, které obdrží pakety TCP RST, okamžitě zavřou příslušný soket. To poskytuje okamžité oznámení koncovým bodům, k nimž došlo k vydání připojení, a veškerá budoucí komunikace se stejným připojením TCP selže.  Aplikace mohou vyprázdnit připojení v případě, že soket zavře a znovu vytvoří připojení podle potřeby, aniž by čekali na vypršení časového limitu připojení TCP.

V mnoha scénářích to může snížit nutnost odesílání nečinných dat toku TCP (nebo aplikační vrstvy), aby se obnovil časový limit nečinnosti. 

Pokud vaše doby nečinnosti překračují hodnoty povolené konfigurací nebo pokud vaše aplikace zobrazuje nežádoucí chování s povolenými obnovenými omezeními protokolu TCP, může být stále nutné použít kontroly stavu protokolu TCP (nebo neaktivních zobrazení vrstvy aplikace) a monitorovat tak živý výkon připojení TCP.  V případě, že se připojení proxy serverem nachází v cestě, může to být také užitečné i v případě, že se připojení využije, zejména v případě nečinnosti aplikační vrstvy.  

Pečlivě Projděte celý scénář od konce až do konce, abyste se rozhodli, jestli vám doporučujeme povolit resetování protokolu TCP, upravit časový limit nečinnosti a případně provést další kroky, abyste zajistili, že se chování aplikace požaduje.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Povolení resetování protokolu TCP při nečinnosti

Pomocí rozhraní API verze 2018-07-01 můžete povolit odesílání obousměrných resetů TCP na časový limit nečinnosti u jednotlivých pravidel:

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

## <a name="regions"></a>Dostupnost oblasti

K dispozici ve všech oblastech.

## <a name="limitations"></a>Omezení

- TCP RST se posílá pouze během připojení TCP v navázaném stavu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Standard Load Balancer](load-balancer-standard-overview.md).
- Přečtěte si o [odchozích pravidlech](load-balancer-outbound-rules-overview.md).
