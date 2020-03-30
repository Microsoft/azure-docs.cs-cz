---
title: Zásady partnerského vztahu Microsoftu
titleSuffix: Azure
description: Zásady partnerského vztahu Microsoftu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775222"
---
# <a name="peering-policy"></a>Zásady peeringu
Obecné požadavky společnosti Microsoft z vaší sítě jsou vysvětleny v následujících částech. Ty platí pro přímé partnerský vztah a požadavky partnerského vztahu exchange.

## <a name="technical-requirements"></a>Technické požadavky

* Plně redundantní síť s dostatečnou kapacitou pro výměnu provozu bez přetížení.
* Peer bude mít veřejně směrovatelné číslo autonomního systému (ASN).
* IPv4 i IPv6 jsou podporovány a Microsoft očekává, že vytvořit relace obou typů v každém umístění partnerského vztahu.
* MD5 není podporován.
* **Podrobnosti o ASN:**
    * Microsoft spravuje AS8075 spolu s následujícími ASNs: AS8068, AS8069, AS12076. Úplný seznam ASN s partnerským vztahem AS8075 nazvěte AS-SET MICROSOFT.
    * Všechny strany peering s Microsoft souhlasí s tím, že nebude přijímat trasy z AS12076 (Express Route) za žádných okolností, a měl by odfiltrovat AS12076 na všechny vrstevníky.
* **Zásady směrování:**
    * Peer bude mít alespoň jeden veřejně směrovatelný /24.
    * Společnost Microsoft přepíše přijaté diskriminátory s více výstupy (MED).
    * Společnost Microsoft upřednostňuje příjem značek komunity Protokolu BGP od partnerů, které označují původ trasy.
    * Očekává se, že společnost Peer zaregistruje své trasy ve veřejné databázi IRR (Internet Routing Registry) za účelem filtrování a vynasnaží se udržovat tyto informace aktuální.
    * Doporučujeme, aby partneři nastavili maximální předponu 1000 (IPv4) a 100 (IPv6) tras v partnerských relacích se společností Microsoft.
    * Pokud není výslovně dohodnuto předem, očekává se, že partneři oznámí konzistentní trasy ve všech místech, kde se smicrosoft utnou.
    * Obecně platí, že peeringové relace s AS8075 budou inzerovat všechny trasy AS-MICROSOFT. Propojení AS8075 v Africe a Asii může být omezeno na trasy relevantní pro příslušný region.
    * Ani jedna ze stran nevytvoří statickou trasu, trasu poslední instance nebo jinak neodešle provoz druhé straně na trasu, která není oznámena prostřednictvím Protokolu BGP.
    * Od rovnocenných počítačů se očekává, že budou dodržovat průmyslové standardy [MANRS](https://www.manrs.org/) pro zabezpečení tras.

## <a name="operational-requirements"></a>Provozní požadavky
* Plně obsazené 24x7 Network Operations Center (NOC), které je schopné pomoci při řešení všech technických problémů a problémů s výkonem, porušení zabezpečení, útoků odmítnutí služby nebo jakéhokoli jiného zneužití pocházejícího z druhé strany nebo jejich zákazníků.
* Očekává se, že partneři budou mít kompletní a aktuální profil v [PeeringDB,](https://www.peeringdb.com) včetně e-mailu 24x7 NOC z firemní domény a telefonního čísla. Tyto informace používáme k ověření podrobností partnera, jako jsou informace o NOC, technické kontaktní informace a jejich přítomnost v peeringových zařízeních atd. Osobní účty Yahoo, Gmail a hotmail nejsou akceptovány.

## <a name="physical-connection-requirements"></a>Požadavky na fyzické připojení
* Umístění, kde se můžete spojit s Microsoftem pro přímý partnerský vztah nebo partnerský vztah Exchange, jsou uvedena v [PeeringDB](https://www.peeringdb.com/net/694)
* **Partnerský vztah výměny:**
    * Propojení musí být přes jednorežimové vlákno pomocí příslušné optiky 10Gbps.
    * Očekává se, že partneři upgradují své porty, když využití ve špičce přesáhne 50 %.
* **Přímý partnerský vztah:**
    * Propojení musí být přes jednorežimové vlákno pomocí příslušné optiky 10Gbps nebo 100Gbps.
    * Společnost Microsoft vytvoří přímý partnerský vztah pouze s poskytovateli služeb Internetu nebo poskytovateli síťových služeb.
    * Od partnerů se očekává, že budou upgradovat své porty, když využití ve špičce přesáhne 50 % a zachová vajímec v každém metru, a to buď na jednom místě, nebo na několika místech v metru.
    * Každý přímý partnerský vztah se skládá ze dvou připojení ke dvěma hraničním směrovačům Microsoft u směrovačů peeru umístěných v hraničnísíti partnerské strany. Společnost Microsoft vyžaduje dvě relace protokolu BGP napříč těmito připojeními. Partner se může rozhodnout, že na jejich konci nenasadí redundantní zařízení.

## <a name="traffic-requirements"></a>Dopravní požadavky
* Peery přes partnerský vztah exchange musí mít minimálně 200 Mb provozu a méně než 2 Gb.  Pro provoz přesahující 2Gb přímý partnerský vztah by měl být přezkoumán.
* U přímého partnerského vztahu musí provoz z vaší sítě do společnosti Microsoft splňovat pod minimální mzda.

    | Geografická oblast                      | Minimální návštěvnost společnosti Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mb/s                       |
    | APAC (kromě Indie)      |   2 Gb/s                       |
    | APAC (pouze Indie)        | 500 Mb/s                       |
    | Evropa                   |   2 Gb/s                       |
    | LATAM                    |   2 Gb/s                       |
    | Střední východ              | 500 Mb/s                       |
    | Není k dispozici                       |   2 Gb/s                       |

* **Rozmanitosti:**
    * V NA, Evropě, APAC a LATAM, propojte alespoň ve třech geograficky rozmanitých místech, pokud je to možné, a zachovat různorodou kapacitu, aby provoz k převzetí služeb při selhání v rámci každého metra.
    * V Africe, na Středním východě a v Indii se propojte v co největším počtu různých míst. Musí udržovat dostatečnou různorodou kapacitu, aby se zajistilo, že provoz zůstane v regionu.

## <a name="next-steps"></a>Další kroky

* Další informace o postupech nastavení přímého partnerského vztahu u Microsoftu postupujte [podle návodu k přímému partnerského vztahu](walkthrough-direct-all.md).
* Další informace o postupech nastavení partnerského vztahu exchange u Microsoftu postupujte podle [návodu k partnerské mušli.](walkthrough-exchange-all.md)