---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919059"
---
Tato část popisuje pokyny a požadavky pro přidělování adresních prostorů klienta, kde vybraná jednotka služby Virtual WAN typu Point-to-Site VPN Gateway škálování je větší než nebo rovna 40.

### <a name="background"></a>Pozadí

Brány VPN typu Point-to-site ve virtuálním centru WAN jsou nasazeny s několika instancemi. Každá instance brány VPN typu Point-to-site může podporovat až 10 000 současných uživatelských připojení typu Point-to-site. V důsledku toho musí virtuální síť WAN nasazovat 40 větší kapacitu, což vyžaduje minimální počet fondů adres přidělených pro různé jednotky škálování.

Pokud se například vybere jednotka škálování 100, nasadí se 5 instancí pro VPN Gateway Point-to-site ve virtuálním centru. Toto nasazení může podporovat 50 000 souběžných připojení a **minimálně** 5 jedinečných fondů adres.

**Dostupné jednotky škálování**

| Jednotka škálování | Maximální počet podporovaných klientů | Minimální počet fondů adres |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Určení fondů adres

Níže jsou uvedeny pokyny pro výběr fondů adres. Všimněte si, že přiřazování fondů adres typu Point-to-Site VPN provádí automaticky virtuální síť WAN.

1. Jedna instance brány umožňuje maximálně 10 000 souběžných připojení. Každý fond adres by měl obsahovat aspoň 10 000 jedinečných IP adres RFC1918.
1. Několik rozsahů fondů adres se automaticky sloučí a přiřadí k **jedné** instanci brány. Tento proces se provádí kruhové dotazování pro všechny instance brány, které mají méně než 10 000 IP adres. Například fond s 5 000 adresami se dá v rámci virtuální sítě WAN kombinovat automaticky s jiným fondem, který má 8 000 adres a je přiřazený k jedné instanci brány.
1. Jeden fond adres je přiřazený jenom k jedné instanci brány pomocí virtuální sítě WAN.
1. Fondy adres musí být jedinečné. Mezi fondy adres se nemůže překrývat.

> [!NOTE] 
> Pokud je fond adres přidružený k instanci brány, která prochází údržbou, fond adres nelze znovu přiřadit jiné instanci.

### <a name="example"></a>Příklad 

Následující příklad popisuje situaci, kdy 60 jednotek škálování podporuje až 30 000 připojení, ale vyhrazené fondy adres mají méně než 30 000 souběžných připojení.

Celkový počet souběžných připojení podporovaných v této instalaci je 28 192. První instance brány podporuje 10 000 adres, druhá instance 8 192 připojení a třetí instance také podporuje adresy 10 000.

| Číslo fondu adres | Fond adres | Podporovaná připojení |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

**Doporučení #1: Ujistěte se, že #2 fondu adres má minimálně 10 000 různých IP adres. (příklad: 10.13.0.0/15)**

**#2 doporučení: přidejte ještě jeden další fond adres. (příklad: fond adres #4 10.15.0.0/21 s adresou 2048). Fondy adres 2 a 4 budou automaticky kombinovány a umožní této instanci brány podporovat souběžná připojení 10 000.**
