---
title: Pochopení Azure Reserved VM Instances slevy | Microsoft Docs
description: Přečtěte si, jak se sleva na rezervované instance virtuálního počítače Azure používá pro běžící virtuální počítače.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849990"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Jak se na virtuální počítače použije sleva na rezervaci Azure

Po zakoupení rezervované instance virtuálního počítače Azure se sleva rezervace automaticky použije na virtuální počítače, které odpovídají atributům a množstvím rezervace. Rezervace pokrývá výpočetní náklady vašich virtuálních počítačů.

Sleva rezervace se vztahuje na základní virtuální počítače, které zakoupíte z Azure Marketplace.

SQL Database vyhrazenou kapacitu najdete v tématu [vysvětlení slevy na rezervované instance Azure](billing-understand-reservation-charges.md).

Následující tabulka ukazuje náklady na virtuální počítač po zakoupení rezervované instance virtuálního počítače. Ve všech případech se vám budou účtovat poplatky za úložiště a síť za normálních sazeb.

| Typ virtuálního počítače  | Poplatky s rezervovanou instancí virtuálního počítače |
|-----------------------|--------------------------------------------|
|Virtuální počítače se systémem Linux bez dalšího softwaru | Rezervace pokrývá náklady na infrastrukturu virtuálních počítačů.|
|Virtuální počítače se systémem Linux s poplatky za software (například Red Hat) | Tato rezervace pokrývá náklady na infrastrukturu. Účtuje se vám další software.|
|Virtuální počítače s Windows bez dalšího softwaru |Tato rezervace pokrývá náklady na infrastrukturu. Účtuje se vám váš software pro Windows.|
|Virtuální počítače s Windows a další software (třeba SQL Server) | Tato rezervace pokrývá náklady na infrastrukturu. Účtují se vám poplatky za software Windows a další software.|
|Virtuální počítače s Windows s [zvýhodněné hybridní využití Azure](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Tato rezervace pokrývá náklady na infrastrukturu. Náklady na software systému Windows jsou pokryté Zvýhodněné hybridní využití Azure. Veškerý další software se účtuje samostatně.|

## <a name="how-reservation-discount-is-applied"></a>Jak se používá sleva rezervace

Slevou rezervace je "*použití-IT-nebo-ztráta-IT*". Pokud tedy za každou hodinu nesouhlasíte s prostředky, ztratíte množství rezervace za tuto hodinu. Nevyužité rezervované hodiny nemůžete přenést.

Po vypnutí prostředku se sleva rezervace automaticky použije na jiný shodný prostředek v zadaném oboru. Pokud v zadaném oboru nebyly nalezeny žádné vyhovující prostředky, budou rezervované hodiny *ztraceny*.

## <a name="reservation-discount-for-non-windows-vms"></a>Zlevněná sleva pro virtuální počítače s jiným systémem než Windows

 Sleva za rezervované instance Azure se aplikuje na každou hodinu na spuštění instancí virtuálních počítačů. Rezervace, které jste zakoupili, odpovídají využití vygenerovaného běžícími virtuálními počítači za účelem uplatnění slevy rezervace. U virtuálních počítačů, které nemusí běžet celou hodinu, se rezervace vyplní jinými virtuálními počítači, které nepoužívají rezervaci, včetně souběžně spuštěných virtuálních počítačů. Na konci hodiny je aplikace rezervace pro virtuální počítače v dané hodiny uzamčena. V případě, že se virtuální počítač nespustí po hodinu nebo souběžných virtuálních počítačích v průběhu hodiny, neplní se za tuto hodinu tato rezervace. Následující graf znázorňuje použití rezervace k fakturovatelnámu využití virtuálních počítačů. Ilustrace je založena na jednom nákupu rezervace a dvou vyhovujících instancích virtuálních počítačů.

![Snímek obrazovky s jednou aplikovanou rezervací a dvěma vyhovujícími instancemi virtuálních počítačů](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Veškeré využití nad řádkem rezervace se účtuje podle standardních tarifů průběžných plateb. Za řádek rezervací se vám neúčtují žádné použití, protože už je zaplacená jako součást nákupu rezervací.
2. Ve hodinu 1 je instance 1 spouštěna 0,75 hodin a instance 2 se spouští po dobu 0,5 hodin. Celkové využití pro hodinu 1 je 1,25 hodin. Za zbývající hodiny 0,25 se účtují sazby za průběžné platby.
3. Pro hodinu 2 a hodinu 3 jsou obě instance spuštěné 1 hodina. Na jednu instanci se vztahuje rezervace a druhá se účtuje podle tarifů průběžných plateb.
4. Ve hodinu 4 je instance 1 spouštěna 0,5 hodin a instance 2 se spustí po dobu 1 hodiny. Instance 1 je plně pokrytá rezervací a 0,5 hodin instance 2 je pokrytá. Za zbývající 0,5 hodiny se vám bude účtovat sazba průběžných plateb.

Informace o používání Azure Reservations v sestavách využití fakturace najdete v tématu [vysvětlení použití rezervací](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Sleva rezervované pro virtuální počítače s Windows

Pokud používáte instance virtuálních počítačů s Windows, rezervace se aplikuje na pokrytí nákladů na infrastrukturu. Použití rezervace na náklady na infrastrukturu virtuálních počítačů pro virtuální počítače s Windows je stejné jako u virtuálních počítačů s jiným systémem než Windows. Poplatky za software systému Windows se účtují samostatně na základě vCPU. Viz [náklady na software systému Windows s rezervacemi](billing-reserved-Instance-windows-software-costs.md). Náklady na licencování Windows můžete pokrýt pomocí [zvýhodněné hybridní využití Azure pro Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Sleva se může vztahovat na různé velikosti.

Pokud si koupíte rezervovanou instanci virtuálního počítače, vyberete-li možnost **optimalizováno pro**: **flexibilita velikosti instance**, bude pokrytí slevy ZÁVISET na velikosti virtuálního počítače, kterou vyberete. Tato rezervace se může vztahovat na velikosti virtuálních počítačů ve stejné skupině řady. Další informace najdete v tématu [flexibilita velikosti virtuálních počítačů pomocí rezervovaných instancí virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Sleva se vztahuje jenom na spárování.

Sleva za rezervaci se vztahuje jenom na využití virtuálních počítačů, `ServiceType` kde `AdditionalInfo` hodnota odpovídá zadanému rezervovanému umístění. Aplikace slev pro rezervaci ignoruje měřič použitý pro virtuální počítače a jenom `ServiceType`vyhodnocuje. Zjistěte, pro který typ služby jste si virtuální počítač zakoupili. Můžete si vyměňovat rezervované úložiště nevyužívající službu Premium Storage pro rezervaci služby Premium Storage nebo opačným způsobem.

## <a name="services-that-get-vm-reservation-discounts"></a>Služby, které získávají slevy na rezervaci virtuálních počítačů

Rezervace virtuálních počítačů se můžou vztahovat na využití virtuálních počítačů vysílaná z několika služeb – ne jenom pro nasazení virtuálních počítačů. Prostředky, které získávají slevy na rezervované instance, se mění v závislosti na nastavení flexibility velikosti instance.

### <a name="instance-size-flexibility-setting"></a>Nastavení flexibility velikosti instance

Nastavení flexibility velikosti instance Určuje, které služby získají slevy za rezervované instance.

Bez ohledu na to, jestli je nastavení zapnuté nebo vypnuté, se slevy za rezervované automaticky  vztahují na `Microsoft.Compute`všechny vyhovující využití virtuálních počítačů, když je ConsumedService. Ověřte proto data o využití pro hodnotu *ConsumedService* . Možné příklady:

- Virtuální počítače
- Škálovací sady virtuálních počítačů
- Služba kontejneru
- Nasazení Azure Batch (v režimu předplatných uživatelů)
- Azure Kubernetes Service (AKS)
- Service Fabric

Pokud je nastavení zapnuté, slevy rezervace se automaticky použijí na vyhovující využití virtuálních počítačů, pokud je *ConsumedService* některou z následujících položek:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Zkontrolujte hodnotu *ConsumedService* v datech o využití a určete, jestli je využití opravňující pro slevy za rezervované slevy.

Další informace o flexibilitě velikosti instancí najdete v tématu [flexibilita velikosti virtuálních počítačů pomocí rezervovaných instancí virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o Azure Reservations najdete v následujících článcích:

- [Co jsou rezervace pro Azure?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací pro Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení použití rezervací pro odběry CSP](/partner-center/azure-reservations)
- [Náklady na software systému Windows, které nejsou součástí rezervací](billing-reserved-instance-windows-software-costs.md)
