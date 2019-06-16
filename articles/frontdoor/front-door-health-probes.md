---
title: Služba Azure branou – monitorování stavu back-endu | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit, jak služba Azure branou monitoruje stav vaší back-EndY
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
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736577"
---
# <a name="health-probes"></a>Sondy stavu

Aby bylo možné určit stav každého back-endu, každé prostředí branou pravidelně odesílá syntetické požadavku HTTP/HTTPS pro všechny vaše nakonfigurované back-EndY. Služba Front Door pak s využitím odpovědí z těchto sond určí nejlepší back-endy, do kterých má směrovat skutečné požadavky klientů.


## <a name="supported-protocols"></a>Podporované protokoly

Přední dveře podporuje odesílání sondy přes protokol HTTP nebo HTTPS. Tyto sondy se odesílají přes stejné porty TCP, jaké jsou nakonfigurované pro směrování požadavků klientů. Toto chování není možné přepsat.

## <a name="health-probe-responses"></a>Odpovědím sondy stavu

| Odezvy  | Popis | 
| ------------- | ------------- |
| Určení stavu  |  Stavový kód 200 OK kód označuje, že back-end je v pořádku. Všechno ostatní je považován za neúspěšný. Pokud z nějakého důvodu (včetně selhání sítě) není platné odpovědi HTTP přijaté pro test, test paměti se počítá jako selhání.|
| Měření latence  | Latence je skutečný čas měřená od okamžiku, kdy bezprostředně před pošleme na chvíli, kdy jsme přijetí posledního bajtu odpovědi na požadavek testu. Používáme nové připojení protokolu TCP pro každý požadavek, tak toto měření není tendenční směrem k back-EndY s existující teplé připojení.  |

## <a name="how-front-door-determines-backend-health"></a>Určuje, jak přední dveře stav back-endu

Služba Azure branou používá stejné tří kroků níže přes všechny algoritmy k určení stavu.

1. Vyloučení zakázané back-EndY.

2. Vylučte back-endů, které mají chyby sond stavu:
    * Tento výběr se provádí pomocí poslední _n_ odpovědím sondy stavu. Pokud se alespoň _x_ jsou v pořádku, back-endu se považuje za v pořádku.

    * _n_ je nakonfigurovaný tak, že změníte hodnotu SampleSize vlastnost v nastavení služby Vyrovnávání zatížení.

    * _x_ je nakonfigurovaný tak, že změníte vlastnost SuccessfulSamplesRequired v nastavení služby Vyrovnávání zatížení.

3. Mimo sadu v dobrém stavu back-endů v back-endový fond branou kromě opatření a udržuje latence (času jejich návratu) pro každý back-end.


## <a name="complete-health-probe-failure"></a>Chyba sběru dat stavu dokončeno

Pokud selžou i pro každý back-endu do back-endový fond sond stavu, branou bere v úvahu všechny back-endů v pořádku a směruje provoz do kruhové dotazování na distribuci napříč jimi.

Jakmile jakéhokoli back-endu se vrátí do stavu v pořádku, branou, bude pokračovat normální algoritmus Vyrovnávání zatížení.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
