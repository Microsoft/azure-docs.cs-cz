---
title: Vysvětlení slevách na využití platformy Azure Reserved VM Instances | Dokumentace Microsoftu
description: Zjistěte, jak se uplatňuje sleva ve výši rezervované Instance virtuálního počítače Azure ke spouštění virtuálních počítačů.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370066"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Použití slevy na rezervaci Azure pro virtuální počítače

Po nákupu rezervované Instance virtuálního počítače Azure sleva za rezervaci se automaticky využije na virtuální počítače, které odpovídají atributy a množství rezervace. Rezervace pokrývá náklady na výpočetní prostředky virtuálních počítačů.

Sleva za rezervaci se vztahuje na základní virtuální počítače, které jste zakoupili z Azure Marketplace.

SQL Database vyhrazené kapacity najdete v části [slevy pochopit Azure Reserved Instances](billing-understand-reservation-charges.md).

Následující tabulka ukazuje náklady na virtuální počítač po nákupu rezervované Instance virtuálního počítače. Ve všech případech se vám budou účtovány za úložiště a sítě za běžné sazby.

| Typ virtuálního počítače  | Poplatky se rezervované Instance virtuálního počítače |
|-----------------------|--------------------------------------------|
|Virtuální počítače s Linuxem bez další software | Rezervace pokrývá náklady na infrastrukturu virtuálních počítačů.|
|Virtuální počítače s Linuxem s poplatky za software (například Red Hat) | Rezervace pokrývá náklady na infrastrukturu. Vám budou účtovány další software.|
|Virtuální počítače s Windows bez další software |Rezervace pokrývá náklady na infrastrukturu. Za Windows software.|
|Virtuální počítače s Windows s další software (například SQL server) | Rezervace pokrývá náklady na infrastrukturu. Vám budou účtovány softwaru Windows a další software.|
|Virtuální počítače s Windows s [zvýhodněné hybridní využití Azure](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | Rezervace pokrývá náklady na infrastrukturu. Náklady na software Windows se vztahují na program Azure Hybrid Benefit. Další software, se účtuje zvlášť.|

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva ve výši rezervace

Sleva za rezervaci se "*použití – it nebo ztratit – it*". Ano Pokud nemáte odpovídající prostředky pro hodinu, potom ztratíte množství rezervací pro určitou hodinu. Nelze provést vpřed nevyužité vyhrazené hodin.

Při vypínání prostředek sleva za rezervaci automaticky použije další odpovídající prostředek v zadaném oboru. Pokud systém nenašel žádné odpovídající prostředky v zadaném oboru, pak jsou vyhrazené hodin *ztráty*.

## <a name="reservation-discount-for-non-windows-vms"></a>Sleva za rezervaci pro Windows VMs

 Slevy na rezervaci Azure se použije ke spuštění instancí virtuálních počítačů po hodinách. Rezervace, které jste zakoupili budou odpovídat využití, protože ho vygeneroval spuštěných virtuálních počítačů použít sleva za rezervaci. Pro virtuální počítače, které se možná nespustí celou hodinu bude vyplněno rezervace z jiných virtuálních počítačů bez použití rezervaci, včetně souběžně běžících virtuálních počítačů. Na konec hodiny je uzamčen aplikace rezervované pro virtuální počítače za hodinu. V případě virtuální počítač nespustí je jedna hodina nebo souběžných virtuálních počítačů během hodiny nevyplní hodinu rezervace, rezervaci je historického pro určitou hodinu. Následující graf znázorňuje použití rezervace fakturovatelné využívání virtuálního počítače. Na obrázku je založen na nákup rezervace jeden a dvě odpovídající instance virtuálních počítačů.

![Snímek obrazovky jedna použité rezervace a dvě odpovídající instance virtuálních počítačů](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Veškeré využití nad řádkem rezervace získá účtovat podle tarifů obvyklé průběžné sazby. Nejste se poplatky za jakékoliv využití pod řádkem rezervace, protože byl již placené při nákupu rezervace.
2. Za 1 hodinu je spuštěna instance 1 0,75 hodin a spuštění instance 2 0,5 hodiny. Celkové využití za hodinu 1 je 1,25 hodin. Zbývající hodiny 0,25 vám budeme účtovat tarify průběžných plateb.
3. Pro hodinu 2 a 3 hodiny obě instance spustila za 1 hodinu. Jedna instance se bude vztahovat rezervaci a druhý se účtují sazbami průběžných plateb.
4. 4 hodiny je spuštěna instance 1 0,5 hodiny a spuštění instance 2 za 1 hodinu. Instance 1 je plně pokryta rezervace a které pokrývá 0,5 hodiny instance 2. Kurz s průběžnými platbami vám budeme účtovat zbývající 0,5 hodiny.

K pochopení a zobrazení aplikace vaše rezervace Azure na fakturaci využití sestav, naleznete v tématu [porozumět používání rezervace](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Sleva za rezervaci pro virtuální počítače s Windows

Pokud máte spuštěnou instancí virtuálních počítačů Windows, se má rezervace použít na zahrnují náklady na infrastrukturu. Aplikace rezervace náklady na infrastrukturu virtuálních počítačů pro virtuální počítače s Windows je stejný jako u Windows VMs. Vám budeme účtovat samostatně softwaru Windows na základě na virtuální procesor. Zobrazit [náklady na software Windows s rezervace](billing-reserved-Instance-windows-software-costs.md). Může zahrnovat vaše Windows licenční náklady na s [programu Azure Hybrid Benefit pro Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Slevy můžete použít pro různé velikosti

Při nákupu rezervované Instance virtuálního počítače, pokud vyberete **optimalizovaná pro**: **instance velikost flexibilitu**, pokrytí slevy závisí na velikosti virtuálního počítače, vyberete. Rezervaci můžete použít velikosti virtuálních počítačů (VM) ve stejné skupině velikost series. Další informace najdete v tématu [flexibilitu velikost virtuálního počítače s rezervovanými instancemi virtuálních počítačů](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Sleva na odpovídající ServiceType pouze

Sleva za rezervaci se vztahuje pouze na využití virtuálních počítačů ve kterém `ServiceType` hodnota v `AdditionalInfo` odpovídá rezervace, které jste si koupili. Slev za rezervaci ignoruje měřiče, které používají pro virtuální počítače a pouze vyhodnocuje `ServiceType`. Vědět, zadejte, že jste si koupili virtuálního počítače pro službu. Vyměňujete-úrovně premium storage rezervaci virtuálních počítačů pro rezervaci úložiště úrovně premium, nebo obráceně.

## <a name="classic-vms-and-cloud-services"></a>Klasické virtuální počítače a cloudové služby

Instance virtuálních počítačů vyhrazené automaticky použít pro oba klasické virtuální počítače a cloudové služby, když je povolená velikost flexibilita instancí. Pro cloudové služby sleva za rezervaci platí jenom pro výpočetní náklady. Když sleva za rezervaci se použije ke cloudovým službám, poplatky za využívání jsou rozděleny do poplatky za výpočty (Linux měřiče) a cloudových služeb poplatky (cloud services management měřidla). Další informace najdete v tématu [jak sleva za rezervaci se vztahuje ke Cloudovým službám](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou rezervace pro Azure?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací pro Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](/partner-center/azure-reservations)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
