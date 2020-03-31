---
title: Obnovení tcp v yidle v Azure při nečinnosti zařízení TCP
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte o Azure Load Balancer s obousměrné TCP RST pakety na časový limit nečinnosti.
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
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294407"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Vyrovnávání zatížení s obnovením protokolu TCP při nečinnosti

[Pomocí standardního vyvažovače zatížení](load-balancer-standard-overview.md) můžete vytvořit předvídatelnější chování aplikace pro vaše scénáře povolením tcp reset na nečinnosti pro dané pravidlo. Výchozí chování vykladače zatížení je tiše přetažení toků při dosažení časového limitu nečinnosti toku.  Povolení této funkce způsobí, že vykladač zatížení odešle obousměrné resetování Protokolu TCP (paket TCP RST) v časovém limitu nečinnosti.  To bude informovat koncové body aplikace, že vypršel časový limit připojení a již není použitelný.  Koncové body můžete okamžitě navázat nové připojení v případě potřeby.

![Obnovení tcp vykladače zatížení](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Změníte toto výchozí chování a povolíte odesílání protokolů TCP Resets při nečinnosti u příchozích pravidel naváděcí paměti NAT, pravidel vyrovnávání zatížení a [odchozích pravidel](https://aka.ms/lboutboundrules).  Pokud je možnost pro každou pravidlo, systém vyrovnávání zatížení odešle obousměrné protokol TCP Reset (pakety TCP RST) do koncových bodů klienta i serveru v době časového limitu nečinnosti pro všechny odpovídající toky.

Koncové body přijímající pakety TCP RST okamžitě uzavřou odpovídající soket. To poskytuje okamžité oznámení koncovým bodům, že došlo k uvolnění připojení a jakákoli budoucí komunikace na stejném připojení TCP se nezdaří.  Aplikace můžete vymazat připojení při uzavření soketu a obnovit připojení podle potřeby bez čekání na připojení TCP nakonec časový limit.

Pro mnoho scénářů to může snížit potřebu odesílat TCP (nebo aplikační vrstvy) keepalives aktualizovat časový limit nečinnosti toku. 

Pokud vaše doby trvání nečinnosti překročí dobu platnosti povolenou konfigurací nebo aplikace zobrazí nežádoucí chování s povolenými resety Protokolu TCP, bude stále nutné použít keepalives protokolu TCP (nebo udržovat y vrstvy aplikace) ke sledování živosti připojení TCP.  Dále keepalives může také zůstat užitečné pro když je připojení proxied někde v cestě, zejména aplikační vrstvy keepalives.  

Pečlivě zkontrolujte celý scénář začátku do konce a rozhodněte se, zda máte prospěch z povolení obnovení protokolu TCP, úpravy časového limitu nečinnosti a pokud mohou být vyžadovány další kroky k zajištění požadovaného chování aplikace.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Povolení protokolu TCP Reset při časovém limitu nečinnosti

Pomocí rozhraní API verze 2018-07-01 můžete povolit odesílání obousměrných resetování Protokolu TCP při časovém limitu pro nečinnosti na základě pravidla:

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

## <a name="region-availability"></a><a name="regions"></a>Dostupnost regionu

K dispozici ve všech oblastech.

## <a name="limitations"></a>Omezení

- Protokol TCP RST odeslaný pouze během připojení TCP ve stavu ESTABLISHED.

## <a name="next-steps"></a>Další kroky

- Další informace o [standardním vyvykladaču zatížení](load-balancer-standard-overview.md).
- Informace o [odchozích pravidlech](load-balancer-outbound-rules-overview.md).
- [Konfigurace protokolu TCP RST při nečinnosti](load-balancer-tcp-idle-timeout.md)
