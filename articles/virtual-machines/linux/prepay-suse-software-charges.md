---
title: Platba za plán softwaru – Azure Reservations
description: Přečtěte si, jak můžete předem zaplatit softwarové plány a ušetřit tak peníze nad náklady na průběžné platby.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: 5a4f7de0cacb17087e346123f53dad45ae1de357
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602621"
---
# <a name="prepay-for-azure-software-plans"></a>Předplacení softwarových plánů Azure

Když platíte za vaše SUSE a RedHat software v Azure, můžete ušetřit peníze nad náklady na průběžné platby. Slevy se vztahují jenom na SUSE a RedHat měřiče a ne na využití virtuálních počítačů. Rezervace virtuálních počítačů můžete koupit samostatně pro další úspory.

V Azure Portal můžete koupit softwarové plány SUSE a RedHat. Postup při nákupu plánu:

- Pro alespoň jeden podnik nebo individuální předplatné s cenami průběžných plateb musíte mít roli vlastníka.
- U předplatných se smlouvou Enterprise musí být na portálu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Pokud je nastavení zakázané, musíte být správce EA pro předplatné.
- Pro program CSP (Cloud Solution Provider) můžou agenti správce nebo prodejní agenti koupit softwarové plány.

## <a name="buy-a-software-plan"></a>Koupit plán softwaru

1. Přihlaste se k Azure Portal a pokračujte na [rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klikněte na **Přidat** a pak vyberte plán softwaru, který chcete koupit.
Vyplňte požadovaná pole. Slevový virtuální počítač se systémem SUSE nebo RedHat, který odpovídá atributům, co kupujete. Skutečný počet nasazení, která získají slevu, závisí na zvoleném rozsahu a množství.
3. Vyberte předplatné. Používá se k platbě za plán.
Platební metodou předplatného se účtují náklady na front-end pro rezervaci. Typ předplatného musí být smlouva Enterprise (počet nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo individuální smlouva s cenami průběžných plateb (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P).
    - U podnikového předplatného se poplatky odečtou od předplatného Azure v rámci předplatného (dříve nazývaného peněžního závazku), nebo se účtují jako nadlimitní využití.
    - U jednotlivých předplatných s průběžnými platbami se poplatky účtují na platební kartu nebo platební metodu předplatného.
4. Vyberte rozsah. Obor může zahrnovat jedno nebo víc předplatných (sdílený rozsah).
    - Jedno předplatné – sleva plánu se používá pro porovnání využití v rámci předplatného.
    - Shared – sleva plánu se použije pro porovnání instancí v jakémkoli předplatném v rámci vašeho fakturačního kontextu. Pro podnikové zákazníky je účetním kontextem registrace a zahrnuje všechna předplatná v registraci. Pro jednotlivé tarify, které jsou zákazníky s průběžnými platbami podle aktuálního využití, jsou fakturačním účtem všechny jednotlivé plány s předplatnými s průběžnými platbami, které vytvořil správce účtu.
    - Jedna skupina prostředků – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.
5. Vyberte produkt a zvolte velikost virtuálního počítače a typ obrázku. Tato sleva se vztahuje jenom na vybranou velikost virtuálního počítače.
6. Vyberte jeden nebo tři roky.
7. Vyberte množství, což je počet předplacených instancí virtuálních počítačů, které můžou získat fakturační slevu.
8. Přidejte produkt na košík, Projděte si ho a kupte si ho.

Sleva rezervace se automaticky použije na měřič softwaru, pro který platíte předem. Plán nepokrývá poplatky za výpočetní prostředky virtuálních počítačů. Rezervace virtuálních počítačů si můžete koupit samostatně.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Sleva se vztahuje na různé velikosti virtuálních počítačů SUSE.

Podobně jako rezervované instance virtuálních počítačů nabízejí plány SUSE Linux flexibilitu velikosti instancí. Vaše sleva platí i v případě, že nasazujete virtuální počítač, který má jinou velikost než zakoupený plán SUSE. Další informace najdete v tématu [Vysvětlení způsobu použití slevy softwarového plánu](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Sleva plánu RedHat

Plány jsou k dispozici pouze pro Red Hat Enterprise Linux virtuálních počítačů. Tato sleva se nevztahuje na virtuální počítače RedHat Enterprise SAP HANA Linux nebo RedHat Enterprise Linux SAP Business Apps.

Slevy plánu RedHat se vztahují jenom na velikost virtuálního počítače, kterou jste vybrali v době nákupu. Plány RHEL se po nákupu nedají znovu vydávat ani vyměňovat.


## <a name="cancellation-and-exchanges-not-allowed"></a>Zrušení a výměny nejsou povolené.

Nemůžete zrušit nebo vyměnit plán SUSE nebo RedHat, který jste si koupili. Zkontrolujte své využití, abyste se ujistili, že kupujete správný plán. Nápovědu k určení toho, co koupit, najdete v tématu [Vysvětlení způsobu použití slevy softwarového plánu](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat rezervaci, najdete v tématu [Správa rezervací Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Další informace najdete v těchto článcích:

- [Co jsou rezervace Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Správa rezervací v Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Vysvětlení způsobu použití slevy za rezervaci SUSE](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
