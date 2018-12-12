---
title: Co je vyhrazené HSM? – Vyhrazené modulu hardwarového zabezpečení azure | Dokumentace Microsoftu
description: Přehled vyhrazených modulu hardwarového zabezpečení Azure poskytuje možnosti úložiště klíčů v Azure, která splňují standard FIPS 140-2 Level 3 certifikační
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080074"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co je služba Rezervované HSM Azure?

Azure vyhrazené modul hardwarového zabezpečení je služba Azure, která poskytuje úložiště kryptografických klíčů v Azure. Vyhrazené HSM splňuje nejpřísnějšími požadavky na zabezpečení. Je to ideální řešení pro zákazníky, kteří by podle standardu FIPS 140-2 Level 3 ověřit zařízení a dokončení a výhradní kontrolu nad zařízení HSM. Zařízení HSM globálně nasazených napříč několika oblastmi Azure a lze snadno Zřizuje se jako pár zařízení a nakonfigurovat na vysokou dostupnost. Moduly hardwarového zabezpečení může také zřídí napříč oblastmi, aby zajistil proti regionální úrovni převzetí služeb při selhání. Microsoft zaujal pomocí vyhrazené HSM služby [SafeNet Luna sítě HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) ze Gemalto. Toto zařízení přináší nejvyšší úroveň výkonu a možností kryptografických integrace. Při zřizování, moduly hardwarového zabezpečení jsou připojené přímo k virtuální síti zákazníka a může také přístupný pomocí nástroje pro aplikace a správu místních konfigurací připojení k síti VPN point-to-site a site-to-site. Zákazníci se získat software a dokumentace ke konfiguraci a správě zařízení HSM z portálu společnosti Gemalto podpory.

## <a name="why-use-azure-dedicated-hsm"></a>Proč používat Azure vyhrazených HSM?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 Level 3 dodržování předpisů

Řada organizací má přísnější oborové předpisy, které vyžadují kryptografického klíče úložiště splňuje [FIPS 140-2 úrovně 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) požadavky. Služba Azure Key Vault Microsoftu pro více tenantů aktuálně poskytuje pouze podle standardu FIPS 140-2 úrovně 2 certifikační. Azure Dedicated HSM splňuje skutečné potřebu oboru finančních služeb, státních úřadů a další, kteří musí splňovat FIPS 140-2 Level 3 požadavky.

### <a name="single-tenant-devices"></a>Zařízení jednoho Tenanta

Řada našich zákazníků má požadavek pro jeden tenantů kryptografických úložné zařízení. Služba Dedicated modulu hardwarového zabezpečení Azure vám umožní zřizování fyzického zařízení z jedné z globálně distribuovaných datových centrech společnosti Microsoft. Po zřízení pro zákazníka, bude mít přístup k zařízení pouze tohoto zákazníka.

### <a name="full-administrative-control"></a>Úplné řízení pro správu

Mnoho zákazníků a také zařízení jednoho tenanta, vyžadují úplné řízení pro správu a mít přístup pro účely správy. Po zřízení pouze tento zákazník má libovolný správce nebo aplikace úroveň přístupu k zařízení. Microsoft bude nemají žádnou administrativní kontrolu po první přístup zákazníka, který vyžaduje změnu změně hesla. Od tohoto okamžiku se zákazník true jednoho tenanta s úplné řízení pro správu a možnosti správy aplikací. Microsoft udržovat monitorování přístupu na úrovni (ne roli správce) pro telemetrii přes sériový port připojení pokrývající monitorování hardwaru, například teploty, stav power dodávek a ventilátor stavu. Zákazník je bezplatné, zakažte tuto Pokud potřeby, ale nebude pak dostávat proaktivní zdravotnický upozornění od Microsoftu.

### <a name="high-performance"></a>High Performance

Zařízení Gemalto byla vybrána pro tuto službu z důvodu jeho širokou škálu podporu kryptografických algoritmů, různé operační systémy podporované a širokou podporu rozhraní API. Konkrétní modelu nasazení nabízí špičkový výkon s 10 000 operací za sekundu pro RSA 2048. Podporuje 10 oddíly, které lze použít pro instance jedinečných aplikací. Toto je nízkou latenci, vysokou kapacitu a vysokou propustnost zařízení.

### <a name="unique-cloud-based-offering"></a>Jedinečná Cloudová nabídka

Microsoft rozpoznán nějaký zvláštní nebo konkrétní mezi jedinečnou sadu zákazníků a je jediným poskytovatelem cloudu, která nabízí nové zákazníky o vyhrazenou službu HSM, který je podle standardu FIPS 140-2 Level 3 ověří a nabízí natolik cloudových a místních aplikací integrace.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Vyhrazené modulu hardwarového zabezpečení Azure je pro vás nejvhodnější?

Azure vyhrazené modulu hardwarového zabezpečení je specializovaná služba adresování jedinečné požadavky mezi konkrétního typu organizace ve velkém měřítku. V důsledku toho se očekává, že hromadná zákazníkům Azure nebudou hodit profilu použijte pro tuto službu. Mnoho najdete službu Azure Key Vault bude vhodnější a finančně výhodnější. Které vám pomohou rozhodnout vyhovovat vašim požadavkům jsme identifikovali následující kritéria.

### <a name="best-fit"></a>Přizpůsobit

Nejvhodnější pro scénáře "lift and shift", které vyžadují přímé a výhradní přístup k zařízením hardwarového zabezpečení. Příklady obsahují:

- Migrace aplikací z místního Azure Virtual Machines
- Migrace aplikací z Amazon EC2 služby AWS na Azure Virtual Machines, která používá službu AWS cloudu HSM Classic (Amazon není nabízející tuto službu pro nové zákazníky)
- Pečlivě zabaleny software spuštěný ve službě Azure Virtual Machines jako je přesměrování zpracování SSL Apache/Ngnix transparentní šifrování dat Oracle a ADCS

### <a name="not-a-fit"></a>Není vhodný

Cloudovým službám Microsoftu, která podporují šifrování pomocí klíčů spravovaných zákazníkem (jako je Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL, klíč zákazníka Office 365) nejsou součástí vyhrazené modulu hardwarového zabezpečení Azure.

### <a name="it-depends"></a>To záleží na okolnostech

Mnoho scénářů bude záviset na potenciální složitá struktura požadavků a co ohrožení může nebo nemůže být provedena. Příkladem je požadavek FIPS 140-2 Level 3, které je vyžadováno často a proto v současné době je vyhrazené HSM pouze možnosti.  Pokud tyto požadavky mandátem nejsou relevantní, pak často by bylo rozhodování mezi Azure Key Vault a vyhrazené HSM založené na posouzení kombinaci požadavky. Příklady obsahují:

- Nový kód spuštěný ve virtuálním počítači zákazníka Azure
- SQL Server transparentní šifrování dat ve virtuálním počítači Azure
- Šifrování na straně klienta služby Azure Storage
- SQL Server a Azure SQL Database s funkcí Always Encrypted

## <a name="next-steps"></a>Další kroky

Vzhledem k tomu vysoce specializované povaha této služby doporučujeme, že některé z klíčových konceptů v této sadě dokumentace jsou plně pochopitelné, ceny, podpory a smlouvy o úrovni služeb jsou plně porozuměl jsem jim a poté je kurz k dispozici pro usnadnění zřizování modulů HSM do existujícího prostředí virtuální sítě. [Gemalto integračních příručkách](https://safenet.gemalto.com/partners/microsoft/) a návody pro rozhodování o tom, architektura nasazení jsou také skvělým zdrojem.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
