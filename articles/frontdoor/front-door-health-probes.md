---
title: Služba front-endu Azure – sledování stavu pro back-end | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak služba Azure front-endu monitoruje stav back-endu.
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
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742397"
---
# <a name="health-probes"></a>Sondy stavu

Aby bylo možné určit stav každého back-endu, každé prostředí front-endu pravidelně odesílá do každé nakonfigurované back-endy syntetickou žádost HTTP/HTTPS. Služba Front Door pak s využitím odpovědí z těchto sond určí nejlepší back-endy, do kterých má směrovat skutečné požadavky klientů. Všimněte si, že vzhledem k tomu, že přední dvířka mají globálně mnoho hraničních prostředí, můžou požadavky na test sondy stavu na back-endy být vysoké, protože více než jedna žádost za sekundu závisí na konfiguraci četnosti sond stavu. 



## <a name="supported-protocols"></a>Podporované protokoly

Přední dvířka podporují odesílání sond přes protokoly HTTP nebo HTTPS. Tyto sondy se odesílají přes stejné porty TCP, jaké jsou nakonfigurované pro směrování požadavků klientů. Toto chování není možné přepsat.

## <a name="health-probe-responses"></a>Odezvy sond stavu

| Odpovědi  | Popis | 
| ------------- | ------------- |
| Určování stavu  |  200 OK stavový kód označuje, že back-end je v pořádku. Všechno ostatní je považováno za selhání. Pokud z nějakého důvodu (včetně selhání sítě) neobdrží platná odpověď HTTP, bude test paměti počítán jako selhání.|
| Latence měření  | Latence je čas na zeď měřený od okamžiku těsně před odesláním žádosti o test do okamžiku, kdy obdržíte poslední bajt odpovědi. Pro každý požadavek používáme nové připojení TCP, takže toto měření není posunuté směrem k back-endu se stávajícími teplými připojeními.  |

## <a name="how-front-door-determines-backend-health"></a>Jak přední dveře určují stav back-endu

Služba front-dveří pro Azure používá ke zjištění stavu stejný postup tří kroků v rámci všech algoritmů.

1. Vyloučit zakázané back-endy

2. Vyloučit back-endy s chybami sond stavu:
    * Tento výběr se provádí zobrazením posledních _n_ odpovědí na test stavu. Pokud je v pořádku alespoň _x_ , back-end je považován za dobrý.

    * _n_ se konfiguruje změnou vlastnosti SampleSize v nastavení vyrovnávání zatížení.

    * _x_ se konfiguruje změnou vlastnosti SuccessfulSamplesRequired v nastavení vyrovnávání zatížení.

3. Ze sady bezproblémového back-endu ve fondu back-endu navíc přední dveře měří a udržuje latenci (dobu odezvy) pro každý back-end.


## <a name="complete-health-probe-failure"></a>Dokončit selhání sondy stavu

Pokud sondy stavu selžou u každého back-endu v back-endu fondu, pak přední dveře považují všechny back-endy v pořádku a směruje provoz v kruhovém dotazování napříč všemi.

Jakmile se kterýkoli back-end vrátí do stavu v pořádku, pak přední dveře obnoví normální algoritmus vyrovnávání zatížení.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
