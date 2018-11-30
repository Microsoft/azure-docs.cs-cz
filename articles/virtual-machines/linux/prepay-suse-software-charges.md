---
title: Plány operačním systémem SUSE Linux - koupit rezervace, Azure | Dokumentace Microsoftu
description: Zjistěte, jak můžete předem pro vaše použití operačního systému SUSE a ušetřit peníze za vaše platili s průběžnými platbami.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 44d23cf38eb1dca9ade94c5a8fd0ae495300be44
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582186"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Předplatíte plány softwaru SUSE z Azure rezervací

Předem pro vaše použití operačního systému SUSE a ušetřit peníze za vaše platili s průběžnými platbami. Tyto slevy uplatňují měřiče SUSE a ne na využívání virtuálních počítačů. Můžete si koupit rezervace pro virtuální počítače, samostatně a ušetřit ještě víc.

Můžete si koupit plány softwaru SUSE na webu Azure Portal. Koupit plán:

- Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise, musí být v povoleno nákup rezervace [portál EA](https://ea.azure.com).
- Programu Cloud Solution Provider (CSP) Správce agentů nebo obchodní zástupci zakoupit plány SUSE.

## <a name="buy-a-suse-software-plan"></a>Koupit plán softwaru SUSE

1. Přejděte na [rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) na webu Azure portal.
1. Vyberte **přidat** a vyberte operačním systémem SUSE Linux.
1. Vyplňte požadovaná pole. Žádné SUSE virtuálního počítače s Linuxem, který odpovídá atributy si zakoupit získá slevu. Skutečný počet nasazení, která získat slevy závisí na rozsah a množství vybranou.

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název tento nákup.|
    |Předplatné|Předplatné použité k úhradě tento plán. Způsob platby v rámci předplatného účtuje pořizovací náklady pro rezervaci. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P) nebo předplatné s průběžnými platbami (číslo nabídky: MS-AZR-0003P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|
    |Rozsah       |Rozsah může zahrnovat jeden nebo více odběrů (sdíleném oboru) jedno předplatné. Pokud vyberete: <ul><li>Jedno předplatné – slevy plán platí pro použití operačním systémem SUSE Linux v tomto předplatném. </li><li>Na úrovni Shared – slevy plán platí pro použití operačním systémem SUSE Linux v kterékoli předplatné v rámci vaší fakturační kontextu. Pro podnikové zákazníky sdílený obor je registraci a zahrnuje všechna předplatná (s výjimkou předplatná pro vývoj/testování) během registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Plán softwaru     |Vyberte plán, SUSE Linux. Pomoc s určením, jaký plán koupit, najdete v tématu [Vysvětlení způsobu uplatňování slev za rezervaci softwaru SUSE Linux Enterprise](../../billing/billing-understand-suse-reservation-charges.md).|
    |Velikost virtuálního počítače     |SUSE Linux ceny závisí na počtu virtuálních procesorů na virtuálním počítači. Vyberte možnost, která představuje počet virtuálních procesorů na virtuální počítače s operačním systémem SUSE Linuxem.|
    |Označení        |Jeden nebo tři roky.|
    |Množství    |Počet virtuálních počítačů, které si kupujete tohoto operačního systému SUSE Linux plánu. Počet spuštěných instancí operačním systémem SUSE Linux, které můžete získat slevu fakturace je množství.|
1. Vyberte **Koupit**.
1. Vyberte **zobrazit tuto rezervaci** chcete podívat na stav vašeho nákupu.

Sleva za rezervaci se automaticky využije na všechny spuštěné virtual machines se SUSE, které odpovídá rezervace. Sleva platí jenom pro měření SUSE. Poplatky za výpočetní virtuální počítač nejsou předmětem tohoto plánu.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Sleva za vztahuje na různé velikosti virtuálních počítačů s flexibilitou velikost instance

Stejně jako rezervované instance virtuálních počítačů dvanáctiměsíčními plány operačním systémem SUSE Linux velikost flexibilita instancí. To znamená, že slevy aplikuje, i když nasazujete virtuální počítač, který je jinou velikost z operačního systému SUSE plán, který jste koupili. Další informace najdete v tématu [pochopit, jak se sleva za rezervaci softwaru SUSE Linux Enterprise použije](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="cancellation-and-exchanges-not-allowed"></a>Zrušení a výměny není povoleno

Zakoupený plán SUSE nejde zrušit ani vyměnit. Zkontrolujte své využití, abyste se ujistili, že kupujete správný plán. Pomoc s určením, jaký plán koupit, najdete v tématu [Vysvětlení způsobu uplatňování slev za rezervaci softwaru SUSE Linux Enterprise](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="next-steps"></a>Další postup

Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](../../billing/billing-manage-reserved-vm-instance.md).

Další informace naleznete v následujících článcích:

- [Co jsou Azure rezervace?](../../billing/billing-save-compute-costs-reservations.md)
- [Správa rezervací v Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Vysvětlení, používání sleva za rezervaci SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../../billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).