---
title: Předplatíte softwarové plány - rezervace Azure | Dokumentace Microsoftu
description: Zjistěte, jak si Předplatíte pro softwarové plány ušetříte peníze, přes váš platili s průběžnými platbami.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771239"
---
# <a name="prepay-for-azure-software-plans"></a>Předplacení softwarových plánů Azure

Když si Předplatíte využití softwaru SUSE a Red Hat v Azure, můžete ušetřit peníze za vaše platili s průběžnými platbami. Tyto slevy uplatňují měřiče SUSE a Red Hat a ne na využívání virtuálních počítačů. Můžete si koupit rezervace pro virtuální počítače si zajistíte další úspory samostatně.

Můžete si koupit plány softwaru SUSE a Red Hat na webu Azure Portal. Koupit plán:

- Musíte mít roli vlastníka pro alespoň jeden Enterprise nebo předplatné s průběžnými platbami.
- Předplatné Enterprise **přidat Reserved Instances** musí být povolena možnost v [portálu EA](https://ea.azure.com/). Pokud je zakázáno, musíte být správce EA pro předplatné.
- Do programu Cloud Solution Provider (CSP) můžete koupit správce agentů nebo obchodní zástupci softwarové plány.

## <a name="buy-a-software-plan"></a>Koupit plán softwaru

1. Přihlášení k webu Azure portal a přejděte na [rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klikněte na tlačítko **přidat** a pak vyberte plán softwaru, který chcete koupit.
Vyplňte požadovaná pole. Všechny virtuální počítač s operačním systémem SUSE Linuxem nebo virtuální počítač RedHat, který odpovídá atributy si zakoupit získá slevu. Skutečný počet nasazení, která získat slevy závisí na rozsah a množství vybranou.
3. Vyberte předplatné. Používá se k úhradě plánu.
Předplatné se účtuje pořizovací náklady pro rezervaci. Tento typ předplatného musí být smlouvu Enterprise Agreement (nabízejí čísla: MS-AZR-0017P nebo MS-AZR - 0148 P) nebo s průběžnými platbami (nabízejí čísla: MS-AZR-0003P nebo MS-AZR-0023P).
    - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
    - Pro předplatné s průběžnými platbami účtují se poplatky na způsob platby předplatné kreditní kartou nebo fakturou.
4. Vyberte obor. Rozsah může zahrnovat jeden nebo více odběrů (sdíleném oboru) jedno předplatné.
    - Jedno předplatné – slevy plánu se využije na odpovídající použití v rámci předplatného.
    - Na úrovni Shared – sleva za plán se využije na odpovídající instance v libovolné předplatné, v účtování kontextu. Kontext fakturace pro podnikové zákazníky, je registrace a obsahuje všechna předplatná v registraci. Pro zákazníky s průběžnými platbami je kontext fakturace Všechna předplatná s průběžnými platbami, vytváří správce účtu.
5. Vyberte produkt, který má zvolte velikost virtuálního počítače a typ obrázku. Sleva se týká pouze vybrané velikosti virtuálního počítače.
6. Vyberte podmínku, jeden rok nebo tři roky.
7. Zvolte množství, což je počet záloh instancí virtuálních počítačů, které můžete získat slevu fakturace.
8. Přidání produktu do nákupního košíku, kontrolu a nákup.

Sleva za rezervaci se automaticky využije na měření softwaru, který platíte předem za. Poplatky za výpočetní kapacitu virtuálního počítače se nevztahuje plán. Rezervací virtuálních počítačů můžete zakoupit zvlášť.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Sleva za vztahuje na různé velikosti virtuálních počítačů SUSE

Stejně jako rezervované instance virtuálních počítačů dvanáctiměsíčními plány operačním systémem SUSE Linux velikost flexibilita instancí. Slevy platí i v případě, že nasazujete virtuální počítač, který je jinou velikost z operačního systému SUSE plán, který jste koupili. Další informace najdete v tématu [pochopit, jak se software plán sleva](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>RedHat plán slevy

Plány jsou dostupné jenom pro virtuální počítače s Red Hat Enterprise Linux. Sleva se nevztahuje na virtuální počítače s Red Hat Enterprise Linuxem SAP HANA a Red Hat Enterprise SAP Business Apps virtuální počítače s Linuxem.

Slevy RedHat plán platí pouze pro velikost virtuálního počítače, který vyberete při nákupu. RHEL plány nelze refundovat ani vyměňují po zakoupení.


## <a name="cancellation-and-exchanges-not-allowed"></a>Zrušení a výměny není povoleno

Nelze zrušit nebo exchange SUSE nebo Red Hat plán, který jste koupili. Zkontrolujte své využití, abyste se ujistili, že kupujete správný plán. Vám pomůžou identifikovat, co chcete koupit [pochopit, jak se software plán sleva](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](../../billing/billing-manage-reserved-vm-instance.md).

Další informace naleznete v následujících článcích:

- [Co jsou Azure rezervace?](../../billing/billing-save-compute-costs-reservations.md)
- [Správa rezervací v Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Vysvětlení, používání sleva za rezervaci SUSE](../../billing/billing-understand-suse-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](../../billing/billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)
