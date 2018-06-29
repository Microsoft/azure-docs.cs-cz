---
title: Pochopení slevách Azure vyhrazenou instancí | Microsoft Docs
description: Zjistěte, jak je vyhrazená Instance virtuálního počítače Azure slevu použito pro běžící virtuální počítače.
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: d1229a49a5bb3bf3198c91a748ed37b7a626c506
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063782"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Pochopit, jak se použije slevu vyhrazenou instanci
Po nákupu Azure vyhrazenou instanci virtuálního počítače slevu vyhrazenou instanci automaticky použita pro virtuální počítače odpovídající atributy a objemu vyhrazenou instanci. Vyhrazená instance obsahuje náklady na infrastrukturu virtuálních počítačů. Následující tabulka znázorňuje náklady pro virtuální počítač po zakoupení vyhrazenou instanci. Ve všech případech musíte platit za úložiště a sítě normální tempem.

| Typ virtuálního počítače  | Poplatky za s vyhrazenou instancí |    
|-----------------------|--------------------------------------------|
|Virtuální počítače s Linuxem bez další software | Vyhrazená instance obsahuje náklady na infrastrukturu virtuálních počítačů.|
|Virtuální počítače s Linuxem pomocí softwaru poplatky (například Red Hat) | Vyhrazená instance obsahuje náklady na infrastrukturu. Budou se vám účtovat další software.|
|Virtuální počítače Windows bez další software |Vyhrazená instance obsahuje náklady na infrastrukturu. Budou se vám účtovat pro software systému Windows.|
|Virtuální počítače Windows s další software (například SQL server) | Vyhrazená instance obsahuje náklady na infrastrukturu. Budou se účtovat softwaru Windows a další software.|
|Virtuální počítače Windows se [Azure hybridní výhody](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Vyhrazená instance obsahuje náklady na infrastrukturu. Náklady na software Windows jsou předmětem Benefit hybridní Azure. Žádný další software je účtována samostatně.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Aplikace vyhrazenou instanci slevy na Windows VMs
 Diskontní vyhrazenou instanci Azure se použije pro spuštěné instance virtuálních počítačů na hodinu. Vyhrazená instance, které jste zakoupili budou odpovídat využití vysílaných virtuální počítače běžící použít vyhrazenou instanci slevy. Pro virtuální počítače, které se nemusí spustit úplné hodinu bude na vyhrazené instanci vyplněno z jiných virtuálních počítačů bez použití vyhrazené instanci, včetně současně spuštěných virtuálních počítačů. Na konci hodinu je uzamčené vyhrazenou instanci aplikace pro virtuální počítače za hodinu. V případě, že virtuální počítač nespustí jednu hodinu nebo souběžných virtuálních počítačů v rámci hodiny nevyplní hodinu vyhrazenou instanci, je vyhrazená instance je nedostatečně využité pro tento hodinu. Následující graf ukazuje o vyhrazenou instanci aplikace pro fakturovatelné využití virtuálních počítačů. Na obrázku je založena na jednu vyhrazenou instanci nákup a dvě odpovídající instance virtuálních počítačů.

![Snímek obrazovky jeden použít vyhrazenou instanci a dvě odpovídající instance virtuálních počítačů](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Získá všechny využití, které je nad řádkem vyhrazenou instanci účtovat tempem regulární průběžnými platbami. Si nejste zdarma pro všechny využití níže v řádku vyhrazená instance, protože byl již placené jako součást vyhrazenou instanci nákupu.
2.  Za hodinu 1 0,75 hodin je spuštěna instance 1 a instance 2 spustí 0,5 hodin. Celkové využití hodinu 1 je 1,25 hodin. Budou se účtovat průběžnými platbami sazby za zbývající 0,25 hodin.
3.  Hodina 2 a 3 hodiny obě instance spustili jednu hodinu. Jedna instance je předmětem vyhrazenou instanci a druhý je účtován průběžnými platbami tempem.
4.  4 hodiny je spuštěna instance 1 0,5 hodin a spuštěna instance 2 hodiny. Instance 1 je podrobně popsané vyhrazená instance a věnuje se 0,5 hodin instance 2. Pro zbývající hodiny 0,5 jste účtovat průběžnými platbami rychlost.

Pochopení a zobrazit aplikace vaše Azure vyhrazená instance v fakturace sestavy využití najdete v tématu [pochopit vyhrazenou instanci využití](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Aplikace slevy vyhrazená instance virtuálních počítačů Windows
Když jsou spuštěné instance virtuálního počítače s Windows, je použita vyhrazenou instanci tak, aby pokrývalo náklady na infrastrukturu. Vyhrazená instance aplikace na náklady na infrastrukturu virtuálních počítačů pro virtuální počítače Windows je stejný jako u Windows VMs. Budou se vám účtovat odděleně pro Windows softwaru na základě za virtuální procesory. V tématu [náklady na software Windows s vyhrazenou instancí](https://go.microsoft.com/fwlink/?linkid=862756). Může zahrnovat váš Windows s [Azure hybridní výhody pro Windows Server] náklady na licencování (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí, naleznete v následujících článcích:

- [Jaké jsou vyhrazená instance virtuálních počítačů Azure?](billing-save-compute-costs-reservations.md)
- [Předem pro virtuální počítače s instancemi Azure vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Spravovat vyhrazená instance v Azure](billing-manage-reserved-vm-instance.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
- [Pochopení využití vyhrazenou instanci odběrů zprostředkovatele kryptografických služeb](https://docs.microsoft.com/partner-center/azure-reservations)
- [Náklady na software Windows není součástí vyhrazené instance](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
