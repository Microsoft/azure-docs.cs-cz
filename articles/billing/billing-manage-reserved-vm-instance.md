---
title: Správa Azure rezervace | Dokumentace Microsoftu
description: Zjistěte, jak můžete změnit obor předplatného a správa přístupu pro Azure rezervace.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: 670aafeb8eb107fc5970b70bfa85d7469c343483
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722849"
---
# <a name="manage-reservations-for-azure-resources"></a>Správa rezervací pro prostředky Azure

Po nákupu rezervace Azure, budete muset použít rezervace do jiného předplatného, změnit, kdo může spravovat rezervace nebo změnit obor rezervace. Můžete také rozdělit rezervace do dvou rezervace použít některé z instancí, které jste si zakoupili do jiného předplatného.

Pokud jste si zakoupili rezervované instance virtuálních počítačů Azure, můžete změnit nastavení optimalizace pro rezervaci. Sleva za rezervaci lze použít pro virtuální počítače ve stejné řady nebo si můžete rezervovat kapacitu datacentra pro konkrétní velikosti virtuálního počítače.

## <a name="change-the-scope-for-a-reservation"></a>Změnit obor pro rezervaci.

 Vaše sleva za rezervaci se vztahuje na virtuální počítače, databáze SQL, Azure Cosmos DB nebo další prostředky, které odpovídají vaší rezervace a spustit v rámci oboru rezervace. Rozsah rezervace může být jedno předplatné nebo všechna předplatná v fakturační kontextu. Je-li nastavit obor na jedno předplatné, rezervace porovnáváno s prostředky ve vybraném předplatném. Je-li nastavit obor na sdílené Azure odpovídá rezervace k prostředkům, které běží ve všech předplatných v rámci kontextu fakturace. Kontext fakturace je závislá na předplatné použité k koupit rezervaci.

Chcete-li aktualizovat obor rezervace, rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace**.
3. Vyberte rezervace.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změna oboru. 

Pokud změníte z sdílí jeden obor, můžete vybrat pouze pokud jste vlastníkem předplatného. Jenom předplatná v rámci stejného kontextu fakturace jako rezervace, je možné vybrat.

Obor se vztahuje pouze na nabídku průběžných plateb MS-AZR - 0003P, nabídka Enterprise MS-AZR - 0017P nebo typy předplatného poskytovatele CSP. Smlouvy enterprise předplatná pro vývoj/testování nejsou nárok na získání sleva za rezervaci.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Přidání nebo změna uživatelů, kteří můžou spravovat rezervace

Přidáním uživatelů do role na rezervaci můžete delegovat správu rezervaci. Ve výchozím nastavení osoby, které zakoupíte rezervaci a správce účtu mít roli vlastníka na rezervaci.

Z předplatných, které získáte slevy na rezervaci můžete spravovat přístup k rezervace nezávisle na sobě. Pokud někomu poskytnete oprávnění ke správě rezervace, který je nedává práva ke správě předplatného. A pokud někomu poskytnete oprávnění ke správě předplatných v rámci oboru rezervaci, která nedává jejich práva ke správě rezervace.

Delegovat správu přístupu k rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** > **rezervace** do seznamu rezervace, které máte přístup.
3. Vyberte rezervace, které chcete delegovat přístup ostatním uživatelům.
4. Vyberte **řízení přístupu (IAM)**.
5. Vyberte **přidat přiřazení role** > **Role** > **vlastníka**. Nebo, pokud chcete udělit omezený přístup, vyberte jinou roli.
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Rozdělit jeden rezervace do dvou rezervace

 Po nákupu více než jednu instanci prostředků v rámci rezervace, můžete přiřadit instancí v rámci této rezervaci do různých předplatných. Všechny instance ve výchozím nastavení, mají jeden rozsah – buď jedno předplatné nebo sdílet. Například zakoupili 10 rezervované instance a zadaný obor na odběr A. Teď můžete změnit obor 7 rezervací na předplatné A a zbývajících 3 k předplatnému služby serveru B. rozdělení rezervace umožňuje distribuci instancí detailní oboru správy. Přidělení pro předplatná můžete zjednodušit zvolením sdíleném oboru. Ale pro účely náklady na správu nebo plánování rozpočtu, můžete přidělit množství na konkrétní předplatné.

 Můžete rozdělit rezervace do dvou rezervace ale Powershellu, rozhraní příkazového řádku, nebo prostřednictvím rozhraní API.

### <a name="split-a-reservation-by-using-powershell"></a>Rozdělení rezervaci pomocí Powershellu

1. Spuštěním následujícího příkazu Získejte ID objednávky rezervace:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Získejte podrobnosti o rezervaci:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rozdělit do dvou rezervace a distribuovat instancí:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Spuštěním následujícího příkazu můžete aktualizovat obor:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
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

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Předplatíte prostředky Azure Cosmos DB pomocí služby Azure Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vysvětlení, používání sleva za rezervaci virtuálních počítačů](billing-understand-vm-reservation-charges.md)
- [Vysvětlení, jak se použije slevu plán softwaru SUSE Linux Enterprise](../billing/billing-understand-suse-reservation-charges.md)
- [Vysvětlení, jak se používají jiné slevy na rezervaci](billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
