---
title: Zásady partnerského vztahu Microsoftu
titleSuffix: Azure
description: Zásady partnerského vztahu Microsoftu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592325"
---
# <a name="peering-policy"></a>Zásady peeringu
Microsoft udržuje zásadu selektivního partnerského vztahu navrženou tak, aby zajistila nejlepší možné prostředí pro zákazníky zajištěné oborovou normou a osvědčenými postupy, škálováním pro budoucí poptávku a strategického umísťování partnerských vztahů. V takovém případě společnost Microsoft vyhrazuje právo udělat výjimky zásad, jak je to nutné. Obecné požadavky společnosti Microsoft z vaší sítě jsou vysvětleny v následujících částech. Platí to pro požadavky přímých partnerských vztahů i na partnerské vztahy mezi servery Exchange. 

## <a name="technical-requirements"></a>Technické požadavky

* Plně redundantní síť s dostatečnou kapacitou pro výměnu provozu bez zahlcení.
* Partner bude mít veřejně směrovatelné číslo autonomního systému (ASN).
* Jsou podporovány adresy IPv4 i IPv6 a společnost Microsoft očekává, že v každém umístění partnerského vztahu naváže relace obou typů.
* Algoritmus MD5 není podporován.
* **Podrobnosti o ASN:**

    * Společnost Microsoft spravuje AS8075 spolu s následujícími čísla ASN: AS8068, AS8069, AS12076. Úplný seznam čísla ASN s partnerským vztahem AS8075, najdete v části nastavení MICROSOFT.
    * Všechny strany, které se zavazují s Microsoftem, souhlasí s tím, že v žádném případě nepřijímají trasy od AS12076 (Express Route) a odfiltrují AS12076 na všech partnerských skupinách.

* **Zásady směrování:**
    * Partner bude mít aspoň jednu veřejně směrovatelný/24.
    * Společnost Microsoft přepíše přijaté Diskriminátory s vícenásobným ukončením (MED).
    * Společnost Microsoft upřednostňuje, aby od partnerských uzlů přijímala značky komunity protokolu BGP, aby označovala původ trasy.
    * Pro relace partnerských vztahů s Microsoftem doporučujeme, aby partneři nastavili jako maximální předponu trasy 2000 (IPv4) a 500 (IPv6).
    * V případě, že se výslovně nedohodli na předem, partneři budou oznamovat konzistentní trasy ve všech umístěních, kde jsou v partnerském vztahu s Microsoftem.
    * Obecně platí, že relace partnerských vztahů s AS8075 budou inzerovat všechny trasy AS-MICROSOFT. Společnost Microsoft může oznámit některé konkrétní regionální oblasti.
    * Žádná ze smluvních stran nevytvoří statickou trasu, trasu posledního způsobu nebo jinému odeslání provozu na trase, která není ohlášena prostřednictvím protokolu BGP.
    * Pro účely filtrování jsou vyžadovány partnerské uzly pro registraci svých tras v databázi služby směrování v síti Internet Routing Registry (IRR) a tyto informace budou v aktuálním stavu.      
    * Partnerské vztahy budou vyhovovat MANRS oborovým standardům pro zabezpečení tras.  Společnost Microsoft si může vybrat: 1.) nevytvářet partnerské vztahy se společnostmi, které nemají podepsané a registrované trasy; 2.) pro odebrání neplatných tras RPKI; 3.) nepřijímejte trasy od navázaných partnerských uzlů, které nejsou registrovány a podepsány. 

## <a name="operational-requirements"></a>Provozní požadavky
* Plně zaměstnanci nepřetržitého síťového provozního centra (NOC), který je schopný pomáhat při řešení všech problémů spojených s technickými a výkonem, narušení zabezpečení, útoků na útok DoS (Denial of Service) nebo jakékoli jiné zneužití pocházející v rámci partnera nebo jejich zákazníků.
* U partnerských vztahů se očekává, že budou mít úplný a aktuální profil pro [PeeringDB](https://www.peeringdb.com) , včetně nepřetržitého noc e-mailu z podnikové domény a telefonního čísla. Tyto informace používáme k ověření podrobností partnerského vztahu, jako jsou NOC informace, technické kontaktní informace a jejich přítomnost v zařízeních partnerských vztahů atd. Osobní účty společnosti Yahoo, Gmail a Hotmail nejsou přijímány.

## <a name="physical-connection-requirements"></a>Požadavky na fyzické připojení
* Umístění, kde se můžete připojit pomocí Microsoftu pro přímý partnerský vztah nebo pro partnerský vztah Exchange, najdete v části [PeeringDB](https://www.peeringdb.com/net/694) .

* **Partnerský vztah Exchange:**
    * U partnerských vztahů se očekává, že bude mít minimálně 10 GB připojení k Exchangi.
    * U partnerů se očekává, že upgradují své porty, pokud využití ve špičce překračuje 50%.
    * Microsoft doporučuje partnerským uzlům udržovat různé možnosti připojení k Exchangi pro podporu scénářů převzetí služeb při selhání.

* **Přímý partnerský vztah:**
    * Propojení musí být prostřednictvím optického vlákna v jednom režimu s využitím optických vláken 100 GB/s.
    * Microsoft bude navazovat jenom přímé partnerské vztahy se poskytovateli internetových služeb nebo poskytovatele síťových služeb.
    * U partnerů se očekává, že upgradují své porty, pokud špičkové využití překračuje 50% a udržují v každé jednosměrné metrice různou kapacitu, a to buď v jednom umístění, nebo napříč několika místy ve Metro.
    * Každý přímý partnerský vztah se skládá ze dvou připojení ke dvěma hraničním směrovačům Microsoft Edge ze směrovačů partnerského vztahu umístěných v hraničních zařízeních. Microsoft během těchto připojení vyžaduje duální relace protokolu BGP. Partnerský uzel se na konci může rozhodnout nenasadit redundantní zařízení.


## <a name="traffic-requirements"></a>Požadavky na provoz

* Partnerské vztahy přes Exchange peering musí mít minimálně 500 MB provozu a méně než 2 GB. Pro přenos, který překračuje 2 GB přímého partnerského vztahu, by se mělo zřídit.
* Microsoft vyžaduje pro přímý partnerský vztah minimálně 2 GB. Každé vzájemně dohodnuté umístění partnerského vztahu musí podporovat převzetí služeb při selhání, které zajišťuje, aby partnerský vztah zůstal při převzetí služeb při selhání 

## <a name="next-steps"></a>Další kroky

* Další informace o krocích pro nastavení přímých partnerských vztahů s Microsoftem najdete v postupu [přímého partnerského vztahu](walkthrough-direct-all.md).
* Pokud se chcete dozvědět o krocích pro nastavení partnerského vztahu serveru Exchange s Microsoftem, postupujte podle pokynů pro [partnerský vztah Exchange](walkthrough-exchange-all.md).
