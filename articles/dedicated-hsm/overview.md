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
ms.openlocfilehash: 503c6d124d6a67c39d9a88100e5ad35dc787fb7b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975589"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co je služba Rezervované HSM Azure?

Azure vyhrazené modul hardwarového zabezpečení je služba Azure, která poskytuje úložiště kryptografických klíčů v Azure. Vyhrazené HSM splňuje nejpřísnějšími požadavky na zabezpečení. Je to ideální řešení pro zákazníky, kteří požadují FIPS 140-2 úrovně 3 ověřit zařízení a dokončení a výhradní kontrolu nad zařízení HSM. 

 Zařízení HSM globálně nasazených napříč několika oblastmi Azure. Může být snadno Zřizuje se jako pár zařízení a nakonfigurovat na vysokou dostupnost. Zařízení HSM se také dá zřídit napříč oblastmi, aby zajistil proti místní úrovni převzetí služeb při selhání. Microsoft poskytuje vyhrazený modulu hardwarového zabezpečení služby pomocí [SafeNet Luna sítě HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) ze Gemalto. Toto zařízení přináší nejvyšší úroveň výkonu a možností kryptografických integrace. 

Jsou povolené, zařízení modulu hardwarového zabezpečení jsou připojené přímo k virtuální síti zákazníka. Jsou také dostupné pomocí nástroje pro místní aplikace a správu při konfiguraci připojení VPN typu point-to-site a site-to-site. Výsledkem je software a dokumentace ke konfiguraci a správě zařízení HSM z portálu společnosti Gemalto podpory.

## <a name="why-use-azure-dedicated-hsm"></a>Proč používat vyhrazené modulu hardwarového zabezpečení Azure?

### <a name="fips-140-2-level-3-compliance"></a>Kompatibilita se standardem FIPS 140-2 Level 3

Řada organizací má přísnější oborové předpisy tohoto diktování splňující ukládání kryptografických klíčů [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) požadavky. Služba Azure Key Vault Microsoftu pro více tenantů aktuálně poskytuje pouze podle standardu FIPS 140-2 úrovně 2 certifikační. Azure Dedicated HSM splňuje skutečné potřebu oboru finančních služeb, státní úřady USA a dalších, kteří musí splňovat FIPS 140-2 Level 3 požadavky.

### <a name="single-tenant-devices"></a>Zařízení s jedním tenantem

Řada našich zákazníků má požadavek pro jeden tenantů kryptografických úložné zařízení. Služba Dedicated modulu hardwarového zabezpečení Azure umožňuje jejich ke zřízení fyzických zařízení z jedné z globálně distribuovaných datových centrech společnosti Microsoft. Po zřízení pro zákazníka, můžou pouze daného zákazníka přístup k zařízení.

### <a name="full-administrative-control"></a>Úplné řízení pro správu

Mnozí zákazníci vyžadují úplné řízení pro správu a mít přístup k jejich zařízení pro účely správy. Po zřízení zařízení pouze zákazník má přístup pro správu nebo na úrovni aplikace do zařízení.

 Společnost Microsoft nemá žádné administrativní řízení, jakmile zákazník má přístup k zařízení poprvé, kdy zákazník změní heslo. Od tohoto okamžiku se zákazník true jednoho tenanta s úplné řízení pro správu a možnosti správy aplikací. Microsoft Udržovat přístup na úrovni monitorování (ne správce roli) pro telemetrii přes sériový port připojení. Tento přístup se věnuje monitorování hardwaru, například teploty, stav power dodávek a ventilátor stavu. 
 
 Zákazník je bezplatné zakázat toto monitorování potřeba. Ale pokud se zakážou, nebude se zobrazit upozornění proaktivní zdravotnický od Microsoftu.

### <a name="high-performance"></a>Vysoký výkon

Zařízení Gemalto byla vybrána pro tuto službu z různých důvodů. Nabízí širokou škálu podporu kryptografických algoritmů, celou řadu podporovaných operačních systémů a širokou podporu rozhraní API. Konkrétní model, který je nasazený nabízí špičkový výkon s 10 000 operací za sekundu pro RSA 2048. Podporuje 10 oddíly, které lze použít pro instance jedinečných aplikací. Toto zařízení je nízkou latenci, vysokou kapacitu a vysokou propustnost zařízení.

### <a name="unique-cloud-based-offering"></a>Jedinečná Cloudová nabídka

Microsoft rozpoznán se specifickými požadavky jedinečnou sadu zákazníků. To je jediný poskytovatel cloudu, která nabízí nové zákazníky o vyhrazenou službu HSM, který je podle standardu FIPS 140-2 úroveň ověření 3 a nabízí natolik z cloudové a místní integrace aplikací.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Vyhrazené modulu hardwarového zabezpečení Azure je pro vás nejvhodnější?

Azure vyhrazené modul hardwarového zabezpečení je specializovaná služba, která řeší požadavky specifické pro konkrétní typ organizace ve velkém měřítku. V důsledku toho se očekává, že hromadná zákazníkům Azure nebudou hodit profilu použijte pro tuto službu. Mnoho se najít službu Azure Key Vault musí být vhodné a nákladově efektivní. Chcete-li při rozhodování, že pokud je vhodný pro vaše požadavky, jsme identifikovali následující kritéria.

### <a name="best-fit"></a>Co nejlépe vyhovovaly

Azure vyhrazené modul hardwarového zabezpečení je nejvhodnější pro scénáře "lift and shift", které vyžadují přímé a výhradní přístup k zařízením hardwarového zabezpečení. Příklady obsahují:

- Migrace aplikací z místního Azure Virtual Machines
- Migrace aplikací z Amazon EC2 služby AWS do virtuálních počítačů, které používají službu AWS cloudu HSM Classic (Amazon není nabízející tuto službu pro nové zákazníky)
- Pečlivě zabaleny spuštěný software, jako je přesměrování zpracování SSL Apache/Ngnix transparentní šifrování dat Oracle a ADCS ve službě Azure Virtual Machines 

### <a name="not-a-fit"></a>Není vhodný

Azure vyhrazené HSM není vhodný pro následující typ scénáře: Cloudových služeb společnosti Microsoft podpora šifrování pomocí klíčů spravovaných zákazníkem, (například služby Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database a klíč zákazníka pro Office 365), které nejsou integrované s Azure vyhrazených HSM.

### <a name="it-depends"></a>To záleží na okolnostech

Zda vyhrazené modulu hardwarového zabezpečení Azure splní všechna vaše očekávání závisí na potenciálně velmi složitý kombinaci požadavky a ohrožení, které lze nebo nelze provést. Příkladem je požadavek na FIPS 140-2 Level 3. Tento požadavek je běžné a vyhrazené modul hardwarového zabezpečení je aktuálně jedinou možností pro splnění. Pokud tyto požadavky mandátem nejsou relevantní, často je možností volby mezi Azure Key Vault a vyhrazené modulu hardwarového zabezpečení. Vyhodnoťte vaše požadavky na před rozhodování.

Situace, ve kterých je nutné zvážit dvě možnosti: 

- Nový kód spuštěný ve virtuálním počítači Azure zákazníka
- Transparentní šifrování dat serveru SQL na virtuálním počítači Azure
- Šifrování na straně klienta služby Azure Storage
- SQL Server a Azure SQL Database s funkcí Always Encrypted

## <a name="next-steps"></a>Další postup

Jedná se o vysoce specializované službu. Doporučujeme proto, že plně chápete klíčové koncepty v této sadě dokumentace, včetně cen, podpory a smlouvy o úrovni služeb. 

[Gemalto integračních příručkách](https://safenet.gemalto.com/partners/microsoft/) umožňují usnadňují zřizování HSM do existující virtuální prostředí sítě. Existují také pomáhá určit, jak nastavit nasazení architektury jsou návody.

* [Vysoká dostupnost](high-availability.md)
* [fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
