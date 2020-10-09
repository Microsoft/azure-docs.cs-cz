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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775222"
---
# <a name="peering-policy"></a>Zásady peeringu
Obecné požadavky společnosti Microsoft z vaší sítě jsou vysvětleny v následujících částech. Platí to pro požadavky přímých partnerských vztahů i na partnerské vztahy mezi servery Exchange.

## <a name="technical-requirements"></a>Technické požadavky

* Plně redundantní síť s dostatečnou kapacitou pro výměnu provozu bez zahlcení.
* Partner bude mít veřejně směrovatelné číslo autonomního systému (ASN).
* Jsou podporovány adresy IPv4 i IPv6 a společnost Microsoft očekává, že v každém umístění partnerského vztahu naváže relace obou typů.
* Algoritmus MD5 není podporován.
* **Podrobnosti o ASN:**
    * Společnost Microsoft spravuje AS8075 spolu s následujícími čísla ASN: AS8068, AS8069, AS12076. Úplný seznam čísla ASN s partnerským vztahem AS8075, najdete v části nastavení MICROSOFT.
    * Všechny strany, které se zavazují s Microsoftem, souhlasí s tím, že v žádném případě nepřijímají trasy od AS12076 (expresní trasa) a odfiltrují AS12076 na všech partnerských skupinách.
* **Zásady směrování:**
    * Partner bude mít aspoň jednu veřejně směrovatelný/24.
    * Společnost Microsoft přepíše přijaté Diskriminátory s vícenásobným ukončením (MED).
    * Společnost Microsoft upřednostňuje, aby od partnerských uzlů přijímala značky komunity protokolu BGP, aby označovala původ trasy.
    * U partnerského zařízení se očekává, že budou registrovat své trasy v databázi pro veřejnou IP adresu registru (IRR) pro účely filtrování a budou mít v dobré víře úsilí, aby tyto informace zůstaly v aktuálním stavu.
    * Doporučujeme, aby partnerské uzly nastavily v relacích partnerských vztahů s Microsoftem maximální předponu 1000 (IPv4) a 100 (IPv6).
    * V případě, že se výslovně nedohodli na předem, partneři budou oznamovat konzistentní trasy ve všech umístěních, kde jsou v partnerském vztahu s Microsoftem.
    * Obecně platí, že relace partnerských vztahů s AS8075 budou inzerovat všechny trasy AS-MICROSOFT. AS8075 vzájemná propojení v Africe a Asie můžou být omezené na trasy relevantní pro příslušnou oblast.
    * Žádná ze smluvních stran nevytvoří statickou trasu, trasu posledního způsobu nebo jinému odeslání provozu na trase, která není ohlášena prostřednictvím protokolu BGP.
    * Očekává se, že partneři vyhovují oborovým standardům [MANRS](https://www.manrs.org/) pro zabezpečení tras.

## <a name="operational-requirements"></a>Provozní požadavky
* Plně zaměstnanci nepřetržitého síťového provozního centra (NOC), který je schopný pomáhat při řešení všech problémů spojených s technickými a výkonem, narušení zabezpečení, útoků na útok DoS (Denial of Service) nebo jakékoli jiné zneužití pocházející v rámci partnera nebo jejich zákazníků.
* U partnerských vztahů se očekává, že budou mít úplný a aktuální profil pro [PeeringDB](https://www.peeringdb.com) , včetně nepřetržitého noc e-mailu z podnikové domény a telefonního čísla. Tyto informace používáme k ověření podrobností partnerského vztahu, jako jsou NOC informace, technické kontaktní informace a jejich přítomnost v zařízeních partnerských vztahů atd. Osobní účty Yahoo, Gmail a Hotmail se nepřijímají.

## <a name="physical-connection-requirements"></a>Požadavky na fyzické připojení
* Umístění, kde se můžete připojit pomocí Microsoftu pro přímý partnerský vztah nebo pro partnerský vztah Exchange, najdete v části [PeeringDB](https://www.peeringdb.com/net/694) .
* **Partnerský vztah Exchange:**
    * Propojení musí být v rámci jednoho režimu vlákna pomocí příslušných peering –ch vláken.
    * U partnerů se očekává, že upgradují své porty, pokud využití ve špičce překračuje 50%.
* **Přímý partnerský vztah:**
    * Propojení musí být přes vlákno v jednom režimu s použitím příslušných peering – nebo 100Gbpsch vláken.
    * Microsoft bude navazovat jenom přímé partnerské vztahy se poskytovateli internetových služeb nebo poskytovatele síťových služeb.
    * U partnerů se očekává, že upgradují své porty, pokud špičkové využití překračuje 50% a udržují v každé jednosměrné metrice různou kapacitu, a to buď v jednom umístění, nebo napříč několika místy ve Metro.
    * Každý přímý partnerský vztah se skládá ze dvou připojení ke dvěma hraničním směrovačům Microsoft Edge ze směrovačů partnerského vztahu umístěných v hraničních zařízeních. Microsoft během těchto připojení vyžaduje duální relace protokolu BGP. Partnerský uzel se na konci může rozhodnout nenasadit redundantní zařízení.

## <a name="traffic-requirements"></a>Požadavky na provoz
* Partnerské vztahy přes Exchange peering musí mít minimálně 200 MB provoz a méně než 2 GB.  V případě provozu překračujícího partnerský vztah 2 GB přímé sítě by se mělo zkontrolovat.
* V případě přímých partnerských vztahů musí provoz z vaší sítě do Microsoftu splňovat nižší minimální požadavky.

    | Geografická oblast                      | Minimální provoz do Microsoftu   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mb/s                       |
    | APAC (s výjimkou Indie)      |   2 Gb/s                       |
    | APAC (jenom Indie)        | 500 Mb/s                       |
    | Evropa                   |   2 Gb/s                       |
    | LATAM                    |   2 Gb/s                       |
    | Střední východ              | 500 Mb/s                       |
    | Není k dispozici                       |   2 Gb/s                       |

* **Rozmanitost**
    * V oblastech NEDEF, Evropa, APAC a LATAM, propojení v alespoň třech geograficky rozmístěných umístěních, pokud je to možné, a udržují různou kapacitu, která umožňuje převzetí služeb při selhání v každé Metro.
    * V Africe, Střední východ a Indie, propojení ve stejném počtu různých umístění. Musí udržovat dostatečně různorodou kapacitu, aby se zajistilo, že provoz zůstane v oblasti.

## <a name="next-steps"></a>Další kroky

* Další informace o krocích pro nastavení přímých partnerských vztahů s Microsoftem najdete v postupu [přímého partnerského vztahu](walkthrough-direct-all.md).
* Pokud se chcete dozvědět o krocích pro nastavení partnerského vztahu serveru Exchange s Microsoftem, postupujte podle pokynů pro [partnerský vztah Exchange](walkthrough-exchange-all.md).