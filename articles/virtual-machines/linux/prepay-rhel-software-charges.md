---
title: Plány Red Hat Enterprise Linux - koupit rezervace, Azure | Dokumentace Microsoftu
description: Zjistěte, jak můžete předem pro vaše použití Red Hat a ušetřit peníze za vaše platili s průběžnými platbami.
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
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652952"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Předem pro Red Hat Enterprise Linux plány z Azure rezervací

Předem pro vaše použití Red Hat a ušetřit peníze za vaše platili s průběžnými platbami. Tyto slevy uplatňují měřiče Red Hat a ne na využívání virtuálních počítačů. Můžete si koupit rezervace pro virtuální počítače, samostatně a ušetřit ještě víc.

Můžete si koupit plány softwaru Red Hat na webu Azure Portal. Koupit plán:

- Musí být v roli vlastník pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise **přidat Reserved Instances** musí být povolená v [portál EA](https://ea.azure.com). Nebo, pokud je toto nastavení zakázané, musíte být správce EA na předplatné.
- Do programu Cloud Solution Provider (CSP) můžete koupit správce agentů nebo obchodní zástupci plány Red Hat.

## <a name="buy-a-red-hat-software-plan"></a>Koupit plán softwaru Red Hat

1. Přejděte na [rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) na webu Azure portal.
1. Vyberte **přidat** a vyberte Red Hat Linux.
1. Vyplňte požadovaná pole. Žádné Red Hat virtuálního počítače s Linuxem, který odpovídá atributy si zakoupit získá slevu. Skutečný počet nasazení, která získat slevy závisí na rozsah a množství vybranou.

    | Pole      | Popis|
    |:------------|:--------------|
    |Název        |Název tento nákup.|
    |Předplatné|Předplatné použité k úhradě tento plán. Způsob platby v rámci předplatného účtuje pořizovací náklady pro rezervaci. Tento typ předplatného musí být smlouvu enterprise agreement (nabízejí čísla: MS-AZR-0017P nebo MS-AZR - 0148 P) nebo s průběžnými platbami (nabízejí čísla: MS-AZR-0003P nebo MS-AZR - 0023 P). V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití. V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.|
    |Rozsah       |Rozsah může zahrnovat jeden nebo více odběrů (sdíleném oboru) jedno předplatné. Pokud vyberete: <ul><li>Jedno předplatné – slevy plánu se použijí pro využití Red Hat Linux v tomto předplatném. </li><li>Na úrovni Shared – slevy plán platí pro použití Red Hat Linux v kterékoli předplatné v rámci vaší fakturační kontextu. Pro podnikové zákazníky sdílený obor je registrace a obsahuje všechna předplatná v rámci registrace. Pro zákazníky s průběžnými platbami sdílený obor je Všechna předplatná s průběžnými platbami, vytváří správce účtu.</li></ul>|
    |Plánování     |Vyberte plán, Red Hat Linux. Pomoc při určování, co chcete koupit najdete v článku pochopení, jak se sleva za rezervaci softwaru Red Hat Linux Enterprise použije.|
    |Velikost virtuálního počítače     |Ceny Red Hat Linux závisí na počtu virtuálních procesorů na virtuálním počítači. Vyberte možnost, která představuje počet virtuálních procesorů na virtuální počítače Red Hat Linux.|
    |Označení        |Jeden nebo tři roky.|
    |Množství    |Počet virtuálních počítačů, které si kupujete tohoto plánu Red Hat Linux. Počet spuštěných instancí Red Hat Linux, které můžete získat slevu fakturace je množství.|
1. Vyberte **Koupit**.
1. Vyberte **zobrazit tuto rezervaci** chcete podívat na stav vašeho nákupu.

Sleva za rezervaci se automaticky využije na všechny spuštěné virtuální počítače Red Hat, které odpovídá rezervace. Sleva se týká jenom měřiče Red Hat. Poplatky za výpočetní virtuální počítač nejsou předmětem tohoto plánu.

## <a name="discount-applies-to-different-vm-sizes"></a>Sleva za vztahuje na různé velikosti virtuálních počítačů

Jako rezervované instance virtuálních počítačů Red Hat Linux plány nabízejí flexibilitu velikost instance. To znamená, že slevy aplikuje, i když nasazujete virtuální počítač, který je jinou velikost z plánu Red Hat, které jste si zakoupili. Další informace najdete v tématu Principy použití sleva za rezervaci softwaru Red Hat Linux Enterprise.

## <a name="cancellation-and-exchanges-not-allowed"></a>Zrušení a výměny není povoleno

Nelze zrušit nebo exchange, které jste koupili plán Red Hat. Zkontrolujte své využití, abyste se ujistili, že kupujete správný plán. Pomoc při určování, co chcete koupit najdete v článku pochopení, jak se sleva za rezervaci softwaru Red Hat Linux Enterprise použije.

## <a name="next-steps"></a>Další postup

Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](../../billing/billing-manage-reserved-vm-instance.md).

Další informace naleznete v následujících článcích:

- [Co jsou Azure rezervace?](../../billing/billing-save-compute-costs-reservations.md)
- [Správa rezervací v Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Vysvětlení, jak je použito sleva za rezervaci Red Hat
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../../billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.