---
title: Pochopení aplikace slevu vyhrazenou instancí virtuálního počítače Azure | Microsoft Docs
description: Zjistěte, jak je vyhrazená Instance virtuálního počítače Azure slevu použito pro spuštěných virtuálních počítačů.
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
ms.date: 11/10/2017
ms.author: yashar
ms.openlocfilehash: ddf6e6a869fb2961db6b626878f9433fdc00a55f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače
Po nákupu vyhrazenou instanci virtuálního počítače slevu rezervace automaticky použita pro virtuální počítače odpovídající atributy a objemu rezervace. Rezervace obsahuje náklady na infrastrukturu virtuálních počítačů. Následující tabulka znázorňuje náklady pro virtuální počítač po zakoupení rezervace. Ve všech případech musíte platit za úložiště a sítě normální tempem.

| Typ virtuálního počítače  | Poplatky za spolu s rezervací |    
|-----------------------|--------------------------------------------|
|Virtuální počítače s Linuxem bez další software | Rezervace obsahuje náklady na infrastrukturu virtuálních počítačů.|
|Virtuální počítače s Linuxem pomocí softwaru poplatky (například Red Hat) | Rezervace obsahuje náklady na infrastrukturu. Budou se vám účtovat další software.|
|Virtuální počítače Windows bez další software |Rezervace obsahuje náklady na infrastrukturu. Budou se vám účtovat pro software systému Windows.|
|Virtuální počítače Windows s další software (například SQL server) | Rezervace obsahuje náklady na infrastrukturu. Budou se účtovat softwaru Windows a další software.|
|Virtuální počítače Windows se [Azure hybridní výhody](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Rezervace obsahuje náklady na infrastrukturu. Náklady na software Windows jsou předmětem Benefit hybridní Azure. Žádný další software je účtována samostatně.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Aplikace rezervace slevy na Windows VMs
 Rezervace slevu se použijí na spuštěné instance virtuálních počítačů na hodinu. Rezervace, které jste zakoupili budou odpovídat využití vysílaných virtuální počítače běžící aplikovat slevu rezervace. Pro virtuální počítače, které se nemusí spustit úplné hodinu bude rezervace vyplněno z jiných virtuálních počítačů bez použití rezervace, včetně současně spuštěných virtuálních počítačů. Na konci hodinu je uzamčené rezervace aplikace pro virtuální počítače za hodinu. V případě, že virtuální počítač nespustí jednu hodinu nebo souběžných virtuálních počítačů v rámci hodiny nevyplní hodinu rezervace, rezervace je nedostatečně využité pro tento hodinu. Následující graf ukazuje použití rezervace k fakturovatelný využití virtuálních počítačů. Na obrázku je založena na nákup jeden rezervace a dvě odpovídající instance virtuálních počítačů.

![Vyhrazená Instance virtuálního počítače aplikace](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Získá všechny využití, které je nad řádkem vyhrazenou instanci virtuálního počítače účtovat tempem regulární průběžnými platbami. Nejste se zdarma pro všechny využití pod čarou vyhrazenou instancí virtuálních počítačů, protože byl již placené jako součást rezervace nákupu.
2.  Za hodinu 1 0,75 hodin je spuštěna instance 1 a instance 2 spustí 0,5 hodin. Celkové využití hodinu 1 je 1,25 hodin. Budou se účtovat průběžnými platbami sazby za zbývající 0,25 hodin.
3.  Hodina 2 a 3 hodiny obě instance spustili jednu hodinu. Jedna instance je předmětem rezervace a druhý je účtován průběžnými platbami tempem.
4.  4 hodiny je spuštěna instance 1 0,5 hodin a spuštěna instance 2 hodiny. Instance 1 je plně předmětem rezervace a věnuje se 0,5 hodin instance 2. Pro zbývající hodiny 0,5 jste účtovat průběžnými platbami rychlost.

Pochopení a zobrazit aplikace vaší rezervace v fakturace sestavy využití najdete v tématu [pochopit vyhrazenou instanci virtuálního počítače využití](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Aplikace slevy rezervace virtuálních počítačů Windows
Když jsou spuštěné instance virtuálního počítače s Windows, je použita rezervace tak, aby pokrývalo náklady na infrastrukturu. Aplikace rezervace náklady na infrastrukturu virtuálních počítačů pro virtuální počítače Windows je stejný jako u Windows VMs. Budou se vám účtovat odděleně pro Windows softwaru na základě za virtuální procesory. V tématu [náklady na software Windows díky rezervacím](https://go.microsoft.com/fwlink/?linkid=862756). Může zahrnovat váš Windows s [Azure hybridní výhody pro Windows Server] náklady na licencování (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa instancí vyhrazený virtuální počítač](billing-manage-reserved-vm-instance.md)
- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače](billing-save-compute-costs-reservations.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Pochopení využití vyhrazenou instanci odběrů zprostředkovatele kryptografických služeb](https://docs.microsoft.com/partner-center/azure-reservations)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktovat podporu

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
