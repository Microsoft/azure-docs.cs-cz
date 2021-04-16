---
title: Co je Dedicated HSM? – Vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Přečtěte si, jak je vyhrazený modul HARDWAROVÉho zabezpečení Azure služba Azure, která poskytuje úložiště kryptografických klíčů v Azure.
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
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 418c8f0844bf2336ce0d4a681071f237d81877ca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505713"
---
# <a name="what-is-azure-dedicated-hsm"></a>Co je Azure Dedicated HSM?

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je služba Azure, která poskytuje úložiště kryptografických klíčů v Azure. Vyhrazený modul HSM splňuje nejpřísnější požadavky na zabezpečení. Je to ideální řešení pro zákazníky, kteří vyžadují zařízení se standardem FIPS 140-2 úrovně 3 a kompletní a exkluzivní kontrolu nad zařízením HSM. 

 Zařízení HSM se nasazují globálně napříč několika oblastmi Azure. Dají se snadno zřídit jako pár zařízení a mají nakonfigurovanou vysokou dostupnost. Zařízení HSM je taky možné zřídit v různých oblastech, abyste se ujistili o převzetí služeb při selhání na místní úrovni. Společnost Microsoft poskytuje vyhrazenou službu HSM pomocí [Thales zařízení A790 Luna 7 modelu HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . Toto zařízení nabízí nejvyšší úrovně možností pro výkon a integraci s kryptografickým rozhraním. 

Po zřízení se zařízení HSM připojí přímo k virtuální síti zákazníka. K nim mají taky při konfiguraci připojení typu Point-to-site nebo VPN typu Site-to-site k dispozici místní nástroje pro správu aplikací a aplikací. Zákazníci dostanou software a dokumentaci ke konfiguraci a správě zařízení HSM na [portálu zákaznických služeb Thales](https://supportportal.thalesgroup.com/csm).

## <a name="why-use-azure-dedicated-hsm"></a>Proč používat vyhrazený modul HSM Azure?

### <a name="fips-140-2-level-3-compliance"></a>Dodržování předpisů FIPS 140-2 úrovně 3

Mnoho organizací má přísné oborové předpisy, které určují, že kryptografické klíče musí být uložené ve [standardu FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) ověřený HSM. Vyhrazený modul HARDWAROVÉho zabezpečení Azure a nová nabídka pro jednoho tenanta, [Azure Key Vault spravovaný HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm), můžou zákazníkům z různých průmyslových segmentů, jako jsou finanční služby, státní úřady a další, plnit požadavky standardu FIPS 140-2 úrovně 3. Zatímco služba víceklientské [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) Microsoftu v současnosti používá HSM ověřený na úrovni FIPS 140-2 úrovně 2. 

### <a name="single-tenant-devices"></a>Zařízení s jedním klientem

Mnohé z našich zákazníků mají požadavek na jedinou tenantů šifrovacího zařízení úložiště. Vyhrazená služba HSM v Azure jim umožňuje zřídit fyzické zařízení z některého z globálně distribuovaných datových center od Microsoftu. Po zřízení pro zákazníka může k zařízení přistupovat jenom tento zákazník.

### <a name="full-administrative-control"></a>Úplný ovládací prvek pro správu

Mnoho zákazníků vyžaduje pro účely správy úplnou administrativní kontrolu a výhradní přístup k jejich zařízení. Po zřízení zařízení má k zařízení přístup na úrovni správce nebo na úrovni aplikace.

 Společnost Microsoft nemá žádný administrativní řídicí prvek, když ho zákazník poprvé přistupuje k zařízení, kdy zákazník změní heslo. Od tohoto okamžiku je zákazník skutečným jedním klientem s úplným řízením správy a možností správy aplikací. Microsoft udržuje přístup na úrovni monitoru (ne roli správce) pro telemetrii prostřednictvím připojení sériového portu. Tento přístup pokrývá monitorování hardwaru, jako je teplota, stav napájení a stav ventilátoru. 
 
 Zákazník je zdarma, aby bylo toto monitorování potřeba zakázat. Pokud je však zakáže, nebudou dostávat aktivní výstrahy týkající se stavu od společnosti Microsoft.

### <a name="high-performance"></a>Vysoký výkon

Zařízení Thales se pro tuto službu vybralo z nejrůznějších důvodů. Nabízí širokou škálu podpory kryptografických algoritmů, řadu podporovaných operačních systémů a širokou podporu rozhraní API. Konkrétní model, který je nasazený, nabízí špičkový výkon s 10 000 operacemi za sekundu pro šifrování RSA-2048. Podporuje 10 oddílů, které lze použít pro jedinečné instance aplikace. Toto zařízení má nízkou latenci, vysokou kapacitu a vysokou propustnost zařízení.

### <a name="unique-cloud-based-offering"></a>Jedinečná cloudová nabídka

Společnost Microsoft uznala specifickou potřebu jedinečné sady zákazníků. Je to jediný poskytovatel cloudu, který nabízí novým zákazníkům vyhrazenou službu HSM, která je ověřená na úrovni FIPS 140-2 úrovně 3, a nabízí tento rozsah integrace cloudových a místních aplikací.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Platí vám pro vás vyhrazené hardwarové zabezpečení Azure?

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je specializovaná služba, která řeší jedinečné požadavky na konkrétní typ rozsáhlé organizace. V důsledku toho se očekává, že hromadné zákazníky Azure nebudou odpovídat profilu použití této služby. Mnohé z nich vyhledají, aby služba Azure Key Vault byla lépe vhodná a nákladově efektivní. Abychom vám pomohli rozhodnout, jestli vyhovuje vašim požadavkům, zjistili jsme následující kritéria.

### <a name="best-fit"></a>Nejlépe přizpůsobit

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je nejvhodnější pro scénáře "zvednutí a posunutí", které vyžadují přímý a výhradní přístup k zařízením HSM. Mezi příklady patří:

- Migrace aplikací z místního prostředí do Azure Virtual Machines
- Migrace aplikací z Amazon AWS EC2 na virtuální počítače, které používají službu AWS Cloud HSM Classic (Amazon nenabízí tuto službu novým zákazníkům)
- Spuštění softwaru se zúženým zabalením, jako je třeba Apache/ngnix SSL Offload, Oracle TDE a ASSD v Azure Virtual Machines 

### <a name="not-a-fit"></a>Nevejde se

Vyhrazený modul HARDWAROVÉho zabezpečení Azure není vhodný pro následující typy scénářů: cloudové služby Microsoftu, které podporují šifrování pomocí klíčů spravovaných zákazníkem (například Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database a klíč zákazníka pro Office 365), které nejsou integrované s vyhrazeným modulem HARDWAROVÉho zabezpečení Azure.

### <a name="it-depends"></a>To záleží...

To, jestli bude vyhrazený modul HSM pro Azure fungovat, závisí na potenciálně složité kombinaci požadavků a ohrožení zabezpečení, které můžete nebo nemůžete dělat. Příkladem je požadavek FIPS 140-2 úrovně 3. Tento požadavek je běžný a vyhrazený modul HSM Azure a nová nabídka pro jednoho tenanta, [Azure Key Vault spravovaný modul hardwarového](https://docs.microsoft.com/azure/key-vault/managed-hsm) zabezpečení (HSM), jsou aktuálně jedinou možností pro jejich setkání. Pokud tyto požadavky nejsou relevantní, je často to volba mezi Azure Key Vault a vyhrazeným modulem HSM v Azure. Před provedením rozhodnutí vyhodnoťte vaše požadavky.

Mezi situace, ve kterých budete muset zvážit tyto možnosti, patří: 

- Nový kód spuštěný ve virtuálním počítači Azure zákazníka
- SQL Server TDE na virtuálním počítači Azure
- Azure Storage šifrování na straně klienta
- SQL Server a databáze Azure SQL Always Encrypted

## <a name="next-steps"></a>Další kroky

Toto je vysoce specializovaná služba. Proto doporučujeme plně porozumět klíčovým koncepcím v této sadě dokumentace, včetně cen, podpory a smluv o úrovni služeb. 

[Příručky Thales Integration](https://cpl.thalesgroup.com/partners/overview) vám pomůžou usnadnit zřizování HSM do stávajícího virtuálního síťového prostředí. K dispozici jsou také postupy, které vám pomohou určit, jak nastavit architekturu nasazení.

* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)
