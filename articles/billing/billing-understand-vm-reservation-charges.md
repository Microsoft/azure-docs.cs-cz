---
title: Vysvětlení slevách na využití platformy Azure Reserved VM Instances | Dokumentace Microsoftu
description: Zjistěte, jak se uplatňuje sleva ve výši rezervované Instance virtuálního počítače Azure ke spouštění virtuálních počítačů.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: 3e1280abfabadba36303ba7a42f9184507c5a592
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904501"
---
# <a name="understand-how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Vysvětlení použití slevy na rezervaci Azure pro virtuální počítače

Po nákupu rezervované Instance virtuálního počítače Azure sleva za rezervaci se automaticky využije na virtuální počítače, které odpovídají atributy a množství rezervace. Rezervace pokrývá náklady na výpočetní prostředky virtuálních počítačů.

SQL Database vyhrazené kapacity najdete v části [slevy pochopit Azure Reserved Instances](billing-understand-reservation-charges.md).

Následující tabulka ukazuje náklady na virtuální počítač po nákupu rezervované Instance virtuálního počítače. Ve všech případech se vám budou účtovány za úložiště a sítě za běžné sazby.

| Typ virtuálního počítače  | Poplatky se rezervované Instance virtuálního počítače |
|-----------------------|--------------------------------------------|
|Virtuální počítače s Linuxem bez další software | Rezervace pokrývá náklady na infrastrukturu virtuálních počítačů.|
|Virtuální počítače s Linuxem s poplatky za software (například Red Hat) | Rezervace pokrývá náklady na infrastrukturu. Vám budou účtovány další software.|
|Virtuální počítače s Windows bez další software |Rezervace pokrývá náklady na infrastrukturu. Za Windows software.|
|Virtuální počítače s Windows s další software (například SQL server) | Rezervace pokrývá náklady na infrastrukturu. Vám budou účtovány softwaru Windows a další software.|
|Virtuální počítače s Windows s [zvýhodněné hybridní využití Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Rezervace pokrývá náklady na infrastrukturu. Náklady na software Windows se vztahují na program Azure Hybrid Benefit. Další software, se účtuje zvlášť.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Aplikace sleva za rezervaci na Windows VMs

 Slevy na rezervaci Azure se použije ke spuštění instancí virtuálních počítačů po hodinách. Rezervace, které jste zakoupili budou odpovídat využití, protože ho vygeneroval spuštěných virtuálních počítačů použít sleva za rezervaci. Pro virtuální počítače, které se možná nespustí celou hodinu bude vyplněno rezervace z jiných virtuálních počítačů bez použití rezervaci, včetně souběžně běžících virtuálních počítačů. Na konec hodiny je uzamčen aplikace rezervované pro virtuální počítače za hodinu. V případě virtuální počítač nespustí je jedna hodina nebo souběžných virtuálních počítačů během hodiny nevyplní hodinu rezervace, rezervaci je historického pro určitou hodinu. Následující graf znázorňuje použití rezervace fakturovatelné využívání virtuálního počítače. Na obrázku je založen na nákup rezervace jeden a dvě odpovídající instance virtuálních počítačů.

![Snímek obrazovky jedna použité rezervace a dvě odpovídající instance virtuálních počítačů](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Veškeré využití nad řádkem rezervace získá účtovat podle tarifů obvyklé průběžné sazby. Nejste se poplatky za jakékoliv využití pod řádkem rezervace, protože byl již placené při nákupu rezervace.
2. Za 1 hodinu je spuštěna instance 1 0,75 hodin a spuštění instance 2 0,5 hodiny. Celkové využití za hodinu 1 je 1,25 hodin. Zbývající hodiny 0,25 vám budeme účtovat tarify průběžných plateb.
3. Pro hodinu 2 a 3 hodiny obě instance spustila za 1 hodinu. Jedna instance se bude vztahovat rezervaci a druhý se účtují sazbami průběžných plateb.
4. 4 hodiny je spuštěna instance 1 0,5 hodiny a spuštění instance 2 za 1 hodinu. Instance 1 je plně pokryta rezervace a které pokrývá 0,5 hodiny instance 2. Kurz s průběžnými platbami vám budeme účtovat zbývající 0,5 hodiny.

K pochopení a zobrazení aplikace vaše rezervace Azure na fakturaci využití sestav, naleznete v tématu [porozumět používání rezervace](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Aplikace sleva za rezervaci k virtuálním počítačům s Windows

Pokud máte spuštěnou instancí virtuálních počítačů Windows, se má rezervace použít na zahrnují náklady na infrastrukturu. Aplikace rezervace náklady na infrastrukturu virtuálních počítačů pro virtuální počítače s Windows je stejný jako u Windows VMs. Vám budeme účtovat samostatně softwaru Windows na základě na virtuální procesor. Zobrazit [náklady na software Windows s rezervace](https://go.microsoft.com/fwlink/?linkid=862756). Může zahrnovat vaše Windows licenční náklady na s [programu Azure Hybrid Benefit pro Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Sleva za vztahuje na různých velikostí s flexibilitou velikost instance

Při nákupu rezervované Instance virtuálního počítače, pokud vyberete **optimalizovaná pro**: **instance velikost flexibilitu**, pokrytí slevy závisí na velikosti virtuálního počítače, vyberete. Rezervaci můžete použít velikosti virtuálních počítačů (VM) ve stejné skupině velikost series. Další informace najdete v tématu [flexibilitu velikost virtuálního počítače s rezervovanými instancemi virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
