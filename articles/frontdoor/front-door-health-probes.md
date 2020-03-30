---
title: Azure Front Door – monitorování stavu back-endu | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Azure Front Door monitoruje stav back-endů
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
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471570"
---
# <a name="health-probes"></a>Sondy stavu

Za účelem určení stavu a blízkosti každého back-endu z daného prostředí front door, každé prostředí front door pravidelně odesílá syntetický požadavek HTTP/HTTPS do každého z nakonfigurovaných back-endů. Služba Front Door pak s využitím odpovědí z těchto sond určí nejlepší back-endy, do kterých má směrovat skutečné požadavky klientů. 

> [!WARNING]
> Vzhledem k tomu, že přední dveře mají mnoho okrajových prostředí po celém světě, zdravotní sonda požaduje objem pro vaše back-endy může být poměrně vysoká - v rozmezí od 25 požadavků každou minutu až po 1200 požadavků za minutu, v závislosti na frekvenci nakonfigurované na frekvenci sondy stavu. S výchozí frekvence sondy 30 sekund, objem sondy na back-endu by měla být asi 200 požadavků za minutu.

## <a name="supported-protocols"></a>Podporované protokoly

Přední dveře podporují odesílání sond přes protokoly HTTP nebo HTTPS. Tyto sondy se odesílají přes stejné porty TCP, jaké jsou nakonfigurované pro směrování požadavků klientů. Toto chování není možné přepsat.

## <a name="supported-http-methods-for-health-probes"></a>Podporované metody HTTP pro sondy stavu

Přední dveře podporují následující metody HTTP pro odesílání sond stavu:

1. **Získat:** Metoda GET znamená, že načtení jakékoli informace (ve formě entity) je identifikován Request-URI.
2. **HLAVA:** Head Metoda je shodná s GET s tím rozdílem, že server nesmí vrátit text zprávy v odpovědi. U nových profilů předních dveří je ve výchozím nastavení metoda sondy nastavena jako HEAD.

> [!NOTE]
> Pro nižší zatížení a náklady na back-endy, Přední dveře doporučuje používat HEAD požadavky na zdravotní sondy.

## <a name="health-probe-responses"></a>Reakce sondy stavu

| Odezvy  | Popis | 
| ------------- | ------------- |
| Určení zdraví  |  Stavový kód 200 OK označuje, že back-end je v pořádku. Všechno ostatní je považováno za selhání. Pokud z nějakého důvodu (včetně selhání sítě) není přijata platná odpověď HTTP pro sondu, sonda se počítá jako selhání.|
| Měření latence  | Latence je čas nástěnných hodin měřený od okamžiku bezprostředně před odesláním požadavku na sondu do okamžiku, kdy obdržíme poslední bajt odpovědi. Pro každý požadavek používáme nové připojení TCP, takže toto měření není zkreslené vůči back-endům se stávajícími teplými připojeními.  |

## <a name="how-front-door-determines-backend-health"></a>Jak přední dveře určuje zdraví back-endu

Azure Front Door používá stejný proces ve třech krocích níže napříč všemi algoritmy k určení stavu.

1. Vyloučit zakázané back-endy.

2. Vyloučit back-endy, které mají chyby sond stavu:
    * Tento výběr se provádí při pohledu na poslední _n_ odpovědi sondy stavu. Pokud alespoň _x_ jsou zdravé, back-end je považován za zdravý.

    * _n_ je konfigurován změnou vlastnosti SampleSize v nastavení vyrovnávání zatížení.

    * _x_ je konfigurovánzměnou vlastnosti SuccessfulSamplesRequired v nastavení vyrovnávání zatížení.

3. Mimo sadu vyživované back-endy v back-endfondu, Front Door dodatečně měří a udržuje latence (doba odezvy) pro každý back-end.


## <a name="complete-health-probe-failure"></a>Úplné selhání sondy stavu

Pokud sondy stavu nezdaří pro každý back-end ve fondu back-endu, pak Front Door považuje všechny backendy v pořádku a směruje provoz v distribuci kruhového dotazování napříč všemi z nich.

Jakmile se jakýkoli back-end vrátí do stavu v pořádku, pak Front Door obnoví normální algoritmus vyrovnávání zatížení.

## <a name="disabling-health-probes"></a>Zakázání sond stavu

Pokud máte jeden back-end ve vašem back-endu fondu, můžete zakázat sondy stavu snížení zatížení back-endu aplikace. I v případě, že máte více back-endů v back-endovém fondu, ale pouze jeden z nich je ve stavu povoleno, můžete zakázat sondy stavu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
