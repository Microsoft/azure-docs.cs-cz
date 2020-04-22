---
title: Platba předem za softwarové plány – rezervace Azure
description: Zjistěte, jak si můžete předplatit softwarové plány, abyste ušetřili peníze za vaše náklady na platby za vaše peníze.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: banders
ms.openlocfilehash: a527b75d376f05ab6190187b7a03d6da775055ab
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759283"
---
# <a name="prepay-for-azure-software-plans"></a>Předplacení softwarových plánů Azure

Když si předplatíte používání softwaru SUSE a RedHat v Azure, můžete ušetřit peníze za vaše náklady s průběžným platbou. Slevy se vztahují pouze na měřiče SUSE a RedHat a nikoli na využití virtuálního počítače. Rezervace pro virtuální počítače si můžete zakoupit samostatně pro další úspory.

Softwarové plány SUSE a RedHat si můžete zakoupit na webu Azure Portal. Zakoupení plánu:

- Musíte mít roli vlastníka alespoň pro jedno předplatné Enterprise nebo individuální předplatné s průběžnými platbami.
- U předplatných se smlouvou Enterprise musí být na portálu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Pokud je nastavení zakázané, musíte být správcem EA pro předplatné.
- V programu Zprostředkovatel cloudových řešení (CSP) mohou softwarová plánu zakoupit správci nebo prodejci.

## <a name="buy-a-software-plan"></a>Zakoupení softwarového tarifu

1. Přihlaste se na portál Azure a přejděte na [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klikněte na **Přidat** a vyberte softwarový plán, který chcete koupit.
Vyplňte požadovaná pole. Každý virtuální počítač SUSE Linux nebo virtuální počítač RedHat, který odpovídá atributům, co si koupíte, získá slevu. Skutečný počet nasazení, která získají slevu, závisí na vybraném oboru a množství.
3. Vyberte předplatné. Používá se k zaplacení plánu.
Za platbu předplatným se účtují předem náklady na rezervaci. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo individuální smlouva s průběžnými platbami (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P).
    - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
    - U individuálního předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo způsob platby faktury.
4. Vyberte rozsah. Obor může zahrnovat jedno předplatné nebo více předplatných (sdílený obor).
    - Jednotné předplatné – sleva plánu se použije na odpovídající využití v předplatném.
    - Sdílené – sleva plánu se použije na odpovídající instance v libovolném předplatném v kontextu fakturace. Pro podnikové zákazníky je fakturační kontext registrace a zahrnuje všechna předplatná v registraci. U individuálního plánu s průběžnými platbami jsou v kontextu fakturace všechny individuální plány s předplatnými s průběžnými platbami vytvořenými správcem účtu.
    - Skupina jednoho prostředku – Použije slevu rezervace pouze na odpovídající zdroje ve vybrané skupině prostředků.
5. Vyberte produkt, který chcete zvolit velikost virtuálního počítače a typ obrázku. Sleva se vztahuje pouze na vybranou velikost virtuálního počítače.
6. Vyberte jednoleté nebo tříleté období.
7. Zvolte množství, což je počet předplacených instancí virtuálních účtů, které můžou získat fakturační slevu.
8. Přidejte produkt do košíku, zkontrolujte a zakupte.

Sleva na rezervaci se automaticky použije na softwarový měřič, za který platíte předem. Výpočetní poplatky virtuálních počítači se plánne nevztahuje. Rezervace virtuálních mís můžete zakoupit samostatně.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Sleva se vztahuje na různé velikosti virtuálních montovek SUSE

Stejně jako rezervované instance virtuálních počítače nabízejí plány SUSE Linux flexibilitu velikosti instance. Vaše sleva platí i v případě, že nasadíte virtuální počítač, který má jinou velikost než zakoupený plán SUSE. Další informace naleznete v [tématu Understand How the software plan discount .](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)

## <a name="redhat-plan-discount"></a>Sleva na plán RedHat

Plány jsou k dispozici pouze pro virtuální počítače Red Hat Enterprise Linux. Sleva se nevztahuje na virtuální počítače RedHat Enterprise Linux SAP HANA nebo virtuální počítače RedHat Enterprise Linux SAP Business Apps.

Slevy na plán RedHat platí jenom pro velikost virtuálního počítače, kterou vyberete v době nákupu. Plány RHEL nelze po zakoupení refundovat ani vyměnit.


## <a name="cancellation-and-exchanges-not-allowed"></a>Zrušení a výměna není povolena

Zakoupený plán SUSE nebo RedHat nelze zrušit ani vyměnit. Zkontrolujte své využití, abyste se ujistili, že kupujete správný plán. Nápovědu k identifikaci toho, co koupit, [najdete v tématu Informace o tom, jak se používá sleva softwarového plánu](../../cost-management-billing/reservations/understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat rezervaci, najdete v tématu [Správa rezervací Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Další informace naleznete v následujících článcích:

- [Co jsou rezervace Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Správa rezervací v Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Pochopit, jak se vztahuje sleva rezervace SUSE](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
