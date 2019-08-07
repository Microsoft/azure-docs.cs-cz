---
title: Správa Azure Reservations
description: Přečtěte si, jak můžete Azure Reservations spravovat.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840039"
---
# <a name="manage-reservations-for-azure-resources"></a>Správa rezervací pro prostředky Azure

Po zakoupení rezervace Azure možná budete muset uplatnit rezervaci na jiné předplatné, změnit, kdo může tuto rezervaci spravovat, nebo změnit rozsah rezervace. Rezervaci můžete také rozdělit do dvou rezervací a použít tak některé z instancí, které jste koupili v jiném předplatném.

Pokud jste si koupili Azure Reserved Virtual Machine Instances, můžete změnit nastavení optimalizace pro rezervaci. Sleva rezervace se může vztahovat na virtuální počítače ve stejné sérii nebo můžete rezervovat kapacitu datového centra pro konkrétní velikost virtuálního počítače. A, měli byste se pokusit optimalizovat rezervace, aby byly plně využívány.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Objednávka rezervace a rezervace

Při nákupu rezervace se vytvoří dva objekty: **Objednávka rezervace** a **rezervace**.

V době nákupu má objednávka rezervace v rámci ní jednu rezervaci. Akce jako rozdělení, sloučení, částečná refundace nebo vytvoření nových rezervací v rámci **objednávky rezervace**.

Chcete-li zobrazit objednávku rezervace, přejděte na **rezervace** > vyberte rezervaci a pak klikněte na **ID objednávky rezervace**.

![Příklad podrobností objednávky rezervace znázorňující ID objednávky rezervace ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Rezervace dědí oprávnění ze své objednávky rezervace.

## <a name="change-the-reservation-scope"></a>Změna oboru rezervace

 Vaše sleva za rezervaci se vztahuje na virtuální počítače, databáze SQL, Azure Cosmos DB nebo jiné prostředky, které odpovídají vaší rezervaci a které se spouštějí v oboru rezervací. Fakturační kontext je závislý na předplatném, které jste použili k nákupu rezervace.

Aktualizace oboru rezervace:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny** > **rezervace**služeb.
3. Vyberte rezervaci.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změňte obor.

Pokud změníte možnost z Shared to Single Scope, můžete vybrat jenom odběry, u kterých jste vlastníkem. Je možné vybrat pouze předplatná ve stejném kontextu fakturace jako rezervace.

Obor se vztahuje jenom na jednotlivé předplatné s tarify průběžných plateb (nabízí MS-AZR-0003P nebo MS-AZR-0023P), Enterprise nabízí MS-AZR-0017P nebo MS-AZR-0148P nebo cloudové typy předplatného.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci

Správu rezervace můžete delegovat přidáním osob do rolí na základě objednávky rezervace nebo rezervace. Ve výchozím nastavení má osoba, která uvádí objednávku rezervace, a správce účtu roli vlastníka na objednávce rezervace a rezervaci.

Přístup k objednávkám rezervací a rezervacím můžete spravovat nezávisle na předplatných, která dostanou slevu za rezervaci. Když někomu udělíte oprávnění ke správě objednávky rezervace nebo rezervace, neudělí jim oprávnění ke správě předplatného. Podobně platí, že pokud někomu udělíte oprávnění ke správě předplatného v oboru rezervace, neudělí jim práva ke správě objednávky rezervace nebo rezervace.

Aby bylo možné provést výměnu nebo refundaci, musí mít uživatel přístup k objednávce rezervace. Při udělování oprávnění pro uživatele je nejvhodnější udělit oprávnění k objednávce rezervace, nikoli k rezervaci.


Delegování správy přístupu pro rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte možnost **všechny služby** > **rezervace** pro výpis rezervací, ke kterým máte přístup.
3. Vyberte rezervaci, které chcete delegovat přístup jiným uživatelům.
4. Vyberte **řízení přístupu (IAM)** .
5. Vyberte **Přidat** > **vlastníka**role > přiřazení role. Pokud chcete udělit omezený přístup, vyberte jinou roli.
6. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
7. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Rozdělení jedné rezervace do dvou rezervací

 Po zakoupení více než jedné instance prostředku v rámci rezervace můžete chtít přiřadit instance v rámci této rezervace různým předplatným. Ve výchozím nastavení mají všechny instance jeden obor – buď jedno předplatné, nebo sdílené. Například jste vykoupili 10 instancí rezervace a určili obor, který má být předplatné A. Teď můžete chtít změnit rozsah sedmi rezervací na předplatné a a zbývající tři na předplatné B. rozdělení rezervace vám umožní distribuovat instance pro detailní správu rozsahu. Přidělení k předplatným můžete zjednodušit tak, že vyberete sdílený rozsah. Pro účely správy nákladů nebo rozpočtování ale můžete přidělit množství konkrétním předplatným.

 Rezervaci můžete rozdělit do dvou rezervací i přes PowerShell, CLI nebo prostřednictvím rozhraní API.

### <a name="split-a-reservation-by-using-powershell"></a>Rozdělení rezervace pomocí PowerShellu

1. ID objednávky rezervace získáte spuštěním následujícího příkazu:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Získat podrobnosti o rezervaci:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rozdělit rezervaci na dvě a distribuovat instance:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Rozsah můžete aktualizovat spuštěním následujícího příkazu:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervace zrušení, výměna nebo refundace

Rezervace můžete zrušit, vyměnit nebo vrátit refundaci s určitými omezeními. Další informace najdete v tématu věnovaném [výměnám samoobslužných služeb a refundacím pro Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Změnit nastavení optimalizace pro rezervované instance virtuálních počítačů

 Když si koupíte rezervovanou instanci virtuálního počítače, zvolíte možnost flexibilita velikosti instance nebo Priorita kapacity. Flexibilita velikosti instance aplikuje slevu rezervace na jiné virtuální počítače ve stejné [skupině velikostí virtuálních počítačů](https://aka.ms/RIVMGroups). Priorita kapacity má prioritu kapacity datového centra pro vaše nasazení. Tato možnost nabízí další důvěru ve vaší schopnosti spouštět instance virtuálních počítačů, když je potřebujete.

Ve výchozím nastavení platí, že když je rozsah rezervace sdílen, je flexibilita velikosti instance zapnuta. Kapacita datového centra není pro nasazení virtuálních počítačů stanovena na prioritu.

V případě rezervací, kde je obor jeden, můžete optimalizovat rezervaci pro prioritu kapacity místo flexibility velikosti instancí virtuálních počítačů.

Aktualizace nastavení optimalizace pro rezervaci:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny** > **rezervace**služeb.
3. Vyberte rezervaci.
4. Vyberte **Nastavení** > **Konfigurace**.
5. Změňte nastavení **optimalizovat pro** .

## <a name="optimize-reservation-use"></a>Optimalizace použití rezervací

Úspora rezervace Azure je výsledkem jenom z udržitelného využívání prostředků. Když provedete nákup rezervací, platíte předem náklady za to, co je v podstatě 100% možného využití prostředků po dobu jednoho nebo tří let. Zkuste maximalizovat svou rezervaci, abyste získali co nejvíc možností využití a úspory. Následující části vysvětlují, jak sledovat rezervaci a optimalizovat jejich použití.

### <a name="view-reservation-use-in-the-azure-portal"></a>Zobrazení použití rezervací v Azure Portal

Jedním ze způsobů zobrazení využití rezervace je Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **všechny** > [**rezervace**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) služeb a poznamenejte si **využití (%)** pro rezervaci.  
  ![Obrázek znázorňující seznam rezervací](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Vyberte rezervaci.
4. Podívejte se na rezervaci použít trend v průběhu času.  
  ![Obrázek znázorňující použití rezervací ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Zobrazení použití rezervací s rozhraním API

Pokud jste zákazníkem smlouva Enterprise (EA), můžete programově zobrazit, jak se ve vaší organizaci používají rezervace. Nevyužité rezervace se zobrazí prostřednictvím dat o využití. Když si prohlédnete poplatky za rezervované údaje, pamatujte na to, že data jsou rozdělená mezi skutečné náklady a náklady na amortizaci. Skutečné náklady poskytují data pro sjednocení měsíčních faktur. Má také podrobnosti o nákupu rezervací a o rezervacích aplikace. Náklady na amortizaci se podobají skutečným nákladům s tím rozdílem, že platná cena za využití rezervace je poměrná. Nevyužité hodiny rezervace se zobrazují v údajích o nákladech na amortizaci. Další informace o datech využití pro zákazníky se smlouvou EA najdete v tématu [získání smlouva Enterprisech nákladů a využití rezervací](billing-understand-reserved-instance-usage-ea.md).

Pro jiné typy předplatného použijte [Souhrn rezervací rozhraní API – seznam podle pořadí rezervací a rezervací](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>Optimalizace rezervace

Pokud zjistíte, že se nepoužívají rezervace vaší organizace:

- Ujistěte se, že virtuální počítače, které vaše organizace vytváří, odpovídají velikosti virtuálního počítače, která je pro rezervaci.
- Ujistěte se, že je zapnutá flexibilita velikosti instance. Další informace najdete v tématu [Správa rezervací – Změna nastavení optimalizace pro rezervované instance virtuálních počítačů](#change-optimize-setting-for-reserved-vm-instances).
- Změňte rozsah rezervace na Shared tak , aby se v podstatě platilo. Další informace najdete v tématu [Změna rozsahu rezervace](#change-the-reservation-scope).
- Zvažte vyměňujení nevyužitého množství. Další informace najdete v tématu [zrušení a výměny](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o Azure Reservations najdete v následujících článcích:

- [Co jsou rezervace pro Azure?](billing-save-compute-costs-reservations.md)

Koupit plán služby:
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Platba za Azure Cosmos DB prostředky s využitím Azure Cosmos DB rezervované kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)

Koupit plán softwaru:
- [Platba za plán softwaru Red Hat z Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Principy slev a využití:
- [Vysvětlení způsobu použití slevy rezervace virtuálních počítačů](billing-understand-vm-reservation-charges.md)
- [Princip použití slevy Red Hat Enterprise Linux software Plan](../billing/billing-understand-rhel-reservation-charges.md)
- [Princip použití zlevněného plánu Enterprise software SUSE Linux](../billing/billing-understand-suse-reservation-charges.md)
- [Informace o tom, jak se používají jiné slevy rezervace](billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software systému Windows, které nejsou součástí rezervací](billing-reserved-instance-windows-software-costs.md)
