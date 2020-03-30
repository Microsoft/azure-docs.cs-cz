---
title: Co je Dedicated HSM? - Azure Vyhrazené HSM | Dokumenty společnosti Microsoft
description: Přehled vyhrazeného modulu hardwarového zabezpečení Azure poskytuje funkce úložiště klíčů v rámci Azure, které splňují certifikaci FIPS 140-2 Level 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: dd5ce117645ef2b368bbf8f0e441770d6e746b5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "70881071"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co je Azure Dedicated HSM?

Azure Dedicated HSM je služba Azure, která poskytuje úložiště kryptografických klíčů v Azure. Vyhrazený modul hardwarového zabezpečení splňuje nejpřísnější požadavky na zabezpečení. Je to ideální řešení pro zákazníky, kteří vyžadují zařízení fips 140-2 level 3 ověřená a kompletní a výhradní ovládání zařízení HSM. 

 Zařízení hsm se nasazují globálně v několika oblastech Azure. Lze je snadno zřídit jako dvojici zařízení a nakonfigurovat pro vysokou dostupnost. Zařízení hsm lze také zřídit napříč oblastmi, aby bylo zajištěno proti převzetí služeb při selhání na regionální úrovni. Společnost Microsoft poskytuje vyhrazenou službu hardwarového zabezpečení pomocí zařízení [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) od společnosti Gemalto. Toto zařízení nabízí nejvyšší úrovně výkonu a možnosti kryptografické integrace. 

Po jejich zřízení jsou zařízení s ním, připojení k připojení přímo k virtuální síti zákazníka. K nim lze přistupovat také pomocí místních aplikací a nástrojů pro správu při konfiguraci připojení VPN z bodu k webu nebo k webu. Zákazníci získají software a dokumentaci pro konfiguraci a správu zařízení hsm z portálu podpory společnosti Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Proč používat azure dedicated hsm?

### <a name="fips-140-2-level-3-compliance"></a>Shody fips 140-2 Level-3

Mnoho organizací má přísné oborové předpisy, které určují, že úložiště kryptografických klíčů splňuje požadavky [FIPS 140-2 Level-3.](https://csrc.nist.gov/publications/detail/fips/140/2/final) Služba Azure Key Vault od Microsoftu pro více klientů v současné době poskytuje pouze certifikaci FIPS 140-2 Level-2. Azure Dedicated HSM splňuje skutečnou potřebu pro odvětví finančních služeb, vládní agentury a další, kteří musí splňovat požadavky FIPS 140-2 Level-3.

### <a name="single-tenant-devices"></a>Zařízení s jedním klientem

Mnoho našich zákazníků má požadavek na jedno nájemné kryptografického paměťového zařízení. Služba Azure Dedicated HSM jim umožňuje zřídit fyzické zařízení z jednoho z globálně distribuovaných datových center Microsoftu. Po jeho zřízení zákazníkovi k zařízení má přístup pouze tento zákazník.

### <a name="full-administrative-control"></a>Úplná administrativní kontrola

Mnoho zákazníků vyžaduje plnou kontrolu správy a výhradní přístup ke svému zařízení pro administrativní účely. Po zřízení zařízení má k zařízení přístup na úrovni správy nebo aplikace pouze zákazník.

 Společnost Microsoft nemá žádnou kontrolu nad správu poté, co zákazník přistupuje k zařízení poprvé, v tomto okamžiku zákazník změní heslo. Od tohoto okamžiku je zákazník skutečný jeden tenant s plnou správu řízení a správu aplikací schopnosti. Společnost Microsoft udržuje přístup na úrovni monitorování (nikoli roli správce) pro telemetrii prostřednictvím připojení sériového portu. Tento přístup zahrnuje hardwarové monitory, jako je teplota, stav napájení a zdraví ventilátoru. 
 
 Zákazník může toto monitorování zakázat. Pokud ji však zakáží, nebudou od společnosti Microsoft dostávat proaktivní výstrahy stavu.

### <a name="high-performance"></a>Vysoký výkon

Zařízení Gemalto bylo vybráno pro tuto službu z různých důvodů. Nabízí širokou škálu podpory kryptografických algoritmů, řadu podporovaných operačních systémů a širokou podporu rozhraní API. Konkrétní nasazený model nabízí vynikající výkon s 10 000 operacemi za sekundu pro RSA-2048. Podporuje 10 oddílů, které lze použít pro jedinečné instance aplikace. Toto zařízení je zařízení s nízkou latencí, vysokou kapacitou a vysokou propustností.

### <a name="unique-cloud-based-offering"></a>Jedinečná cloudová nabídka

Společnost Microsoft rozpoznala konkrétní potřebu jedinečné skupiny zákazníků. Je to jediný poskytovatel cloudu, který nabízí novým zákazníkům vyhrazenou službu modulu hardwarového zabezpečení, která je ověřena fips 140-2 Level 3 a nabízí takový rozsah integrace cloudových a místních aplikací.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Je vyhrazený modul hardwarového zabezpečení Azure pro vás to pravé?

Azure Dedicated HSM je specializovaná služba, která řeší jedinečné požadavky pro konkrétní typ rozsáhlé organizace. V důsledku toho se očekává, že převážná část zákazníků Azure nebude odpovídat profilu použití pro tuto službu. Mnoho z nich zjistí, že služba Azure Key Vault je vhodnější a nákladově efektivnější. Abychom vám pomohli rozhodnout, zda vyhovuje vašim požadavkům, jsme určili následující kritéria.

### <a name="best-fit"></a>Nejvhodnější

Azure Dedicated HSM je nejvhodnější pro scénáře "lift-and-shift", které vyžadují přímý a výhradní přístup k zařízením hardwarového zabezpečení. Příklady obsahují:

- Migrace aplikací z místního do virtuálních počítačů Azure
- Migrace aplikací z Amazon AWS EC2 na virtuální počítače, které používají službu AWS Cloud HSM Classic (Amazon tuto službu novým zákazníkům nenabízí)
- Spuštění softwaru zabaleného v režimu Smrštitelné, jako je snížení zátěže Apache/Ngnix SSL, Oracle TDE a ADCS ve virtuálních počítačích Azure 

### <a name="not-a-fit"></a>Není fit

Vyhrazený modul hardwarového zabezpečení Azure se nehodí pro následující typ scénáře: Cloudové služby Microsoftu, které podporují šifrování pomocí klíčů spravovaných zákazníky (jako je Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Databáze a klíč zákazníka pro Office 365), které nejsou integrované s azure dedicated hsm.

### <a name="it-depends"></a>To záleží...

To, jestli pro vás bude fungovat vyhrazený modul hardwarového zabezpečení Azure, závisí na potenciálně složité kombinaci požadavků a kompromisů, které můžete nebo nemůžete provést. Příkladem je požadavek FIPS 140-2 level 3. Tento požadavek je běžný a vyhrazený modul hardwarového zabezpečení je aktuálně jedinou možností pro jeho splnění. Pokud tyto nařízené požadavky nejsou relevantní, pak je často volba mezi Azure Key Vault a vyhrazené modul hardwarového zabezpečení. Před rozhodnutím vyhodnoťte své požadavky.

Situace, ve kterých budete muset zvážit své možnosti patří: 

- Nový kód spuštěný ve virtuálním počítači Azure zákazníka
- SQL Server TDE ve virtuálním počítači Azure
- Šifrování na straně klienta azure storage
- SQL Server a Azure SQL DB vždy šifrované

## <a name="next-steps"></a>Další kroky

Jedná se o vysoce specializovanou službu. Proto doporučujeme plně pochopit klíčové koncepty v této sadě dokumentace, včetně cen, podpory a smluv o úrovni služeb. 

[Průvodce integrací Gemalto](https://safenet.gemalto.com/partners/microsoft/) vám pomůže usnadnit zřizování objektových síťových zařízení do existujícího prostředí virtuální sítě. K dispozici jsou také návody, které vám pomohou určit, jak nastavit architekturu nasazení.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Síťové služby](networking.md)
* [Možnosti podpory](supportability.md)
* [Sledování](monitoring.md)
