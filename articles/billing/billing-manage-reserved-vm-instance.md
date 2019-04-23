---
title: Správa Azure rezervace | Dokumentace Microsoftu
description: Zjistěte, jak můžete změnit obor předplatného a správa přístupu pro Azure rezervace.
ms.service: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 9a5b200ffb9441b90875c7764786004ff5f1e8a1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994944"
---
# <a name="manage-reservations-for-azure-resources"></a>Správa rezervací pro prostředky Azure

Po nákupu rezervací pro Azure, budete muset použít rezervace do jiného předplatného, změnit, kdo může spravovat rezervace nebo změnit obor rezervace. Můžete také rozdělit rezervace do dvou rezervace použít některé z instancí, které jste si zakoupili do jiného předplatného.

Pokud jste si zakoupili rezervované instance virtuálních počítačů Azure, můžete změnit nastavení optimalizace pro rezervaci. Sleva za rezervaci lze použít pro virtuální počítače ve stejné řady nebo si můžete rezervovat kapacitu datacentra pro konkrétní velikosti virtuálního počítače.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Objednávka rezervace a rezervace

Když si koupíte rezervace, rezervaci, jsou vytvořeny dva objekty: **Objednávka rezervace** a **rezervace**.

Při nákupu objednávky rezervace má jednu rezervaci pod ním. Akce, jako je rozdělit, sloučení, částečné náhrada nebo exchange vytvářet nová rezervace pod **objednávka rezervace**.

Chcete-li zobrazit objednávky rezervace, přejděte na **rezervace** > vyberte rezervace a klikněte **ID objednávky rezervace**.

![Příklad zobrazující ID objednávky rezervace podrobnosti objednávky rezervace ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Rezervace dědí oprávnění z jeho objednávka rezervace.

## <a name="change-the-reservation-scope"></a>Změnit obor rezervace

 Vaše sleva za rezervaci se vztahuje na virtuální počítače, databáze SQL, Azure Cosmos DB nebo další prostředky, které odpovídají vaší rezervace a spustit v rámci rezervace. Kontext fakturace je závislá na předplatné použité k koupit rezervaci.

Chcete-li aktualizovat obor rezervace, rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervace.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změna oboru.

Pokud změníte z sdílí jeden obor, můžete vybrat pouze pokud jste vlastníkem předplatného. Je možné vybrat pouze předplatná ve stejném kontextu fakturace jako rezervace.

Obor se vztahuje pouze na nabídku průběžných plateb MS-AZR-0003P nebo MS-AZR-0023P, nabídku Enterprise MS-AZR-0017P nebo MS-AZR-0148P nebo předplatná typu CSP.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci

Přidáním uživatelů do role v objednávce rezervace nebo rezervace můžete delegovat správu rezervace. Ve výchozím nastavení osoby, která umístí objednávka rezervace a správce účtu mít roli vlastník v objednávce rezervace a rezervace.

Můžete spravovat přístup k objednávky rezervace a rezervace nezávisle z předplatných, které získávají sleva za rezervaci. Pokud někomu poskytnete oprávnění ke správě objednávka rezervace nebo rezervace, není jim dát oprávnění ke správě předplatného. Podobně pokud někomu poskytnete oprávnění ke správě předplatného v oboru rezervaci, ho nedává jejich práva ke správě objednávka rezervace nebo rezervace.

K provedení exchange nebo refundaci, uživatel musí mít přístup k objednávka rezervace. Když někdo udělí oprávnění, je vhodné udělit oprávnění k objednávka rezervace není rezervace.


Delegovat správu přístupu k rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace** do seznamu rezervace, které máte přístup.
3. Vyberte rezervace, které chcete delegovat přístup ostatním uživatelům.
4. Vyberte **Řízení přístupu (IAM)**.
5. Vyberte **přidat přiřazení role** > **Role** > **vlastníka**. Pokud chcete udělit omezený přístup, vyberte jinou roli.
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Rozdělit jeden rezervace do dvou rezervace

 Po nákupu více než jednu instanci prostředků v rámci rezervace, můžete přiřadit instancí v rámci této rezervaci do různých předplatných. Všechny instance ve výchozím nastavení, mají jeden rozsah – buď jedno předplatné nebo sdílet. Například zakoupili 10 rezervované instance a zadaný obor na odběr A. Teď můžete změnit obor 7 rezervací na předplatné A a zbývajících 3 k předplatnému služby serveru B. rozdělení rezervace umožňuje distribuci instancí detailní oboru správy. Přidělení pro předplatná můžete zjednodušit zvolením sdíleném oboru. Ale pro účely náklady na správu nebo plánování rozpočtu, můžete přidělit množství na konkrétní předplatné.

 Můžete rozdělit rezervace do dvou rezervace ale Powershellu, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API.

### <a name="split-a-reservation-by-using-powershell"></a>Rozdělení rezervaci pomocí Powershellu

1. Spuštěním následujícího příkazu Získejte ID objednávky rezervace:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Získejte podrobnosti o rezervaci:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rozdělit do dvou rezervace a distribuovat instancí:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Spuštěním následujícího příkazu můžete aktualizovat obor:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>Zrušení a výměny

V závislosti na typu rezervace budete moci zrušit nebo si vyměňovat rezervaci. Další informace najdete v tématu zrušení a výměna oddílů v následujících tématech:

- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Změna optimalizovat nastavení Reserved VM Instances

 Při nákupu rezervované Instance virtuálního počítače, zvolte velikost flexibilita instancí nebo Priorita kapacity. Flexibilita instancí velikost platí sleva za rezervaci pro ostatní virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Priorita kapacity upřednostňuje kapacitu datacentra pro vaše nasazení. Tato možnost nabízí máte ještě větší jistotu budete moct spustit instance virtuálních počítačů, když je potřebujete.

Ve výchozím nastavení Pokud obor rezervace sdílí, je velikost flexibilita instancí na. Není nastaveno jako prioritní kapacitu datového centra pro nasazení virtuálních počítačů.

Pro rezervace, kde je jeden obor můžete optimalizovat rezervace pro Priorita kapacity místo velikost flexibilita instancí virtuálních počítačů.

Aktualizace nastavení optimalizace pro rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervace.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změnit **optimalizovat pro** nastavení.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou rezervace pro Azure?](billing-save-compute-costs-reservations.md)

Koupit plán služby:
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Předplatíte prostředky Azure Cosmos DB pomocí služby Azure Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)

Koupit plán softwaru:
- [Předplatíte Red Hat softwarové plány z Azure rezervací](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Princip slevu a využití:
- [Vysvětlení, používání sleva za rezervaci virtuálních počítačů](billing-understand-vm-reservation-charges.md)
- [Vysvětlení, jak se použije slevu plán softwaru Red Hat Enterprise Linux](../billing/billing-understand-rhel-reservation-charges.md)
- [Vysvětlení, jak se použije slevu plán softwaru SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Vysvětlení, jak se používají jiné slevy na rezervaci](billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
