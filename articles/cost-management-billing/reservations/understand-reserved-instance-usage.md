---
title: Využití rezervací Azure pro jednotlivé předplatné
description: Zjistěte, jak číst informace o využití, abyste pochopili, jak se uplatňuje rezervace Azure pro vaše samostatné předplatné s průběžnými platbami.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 2b00c90abce915ae6fd645cbcce3536870609558
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447885"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Vysvětlení využití rezervací Azure u jednoho předplatného s průběžnými platbami

Při vyhodnocování vašeho využití rezervací použijte hodnotu ReservationId ze [stránky Rezervace](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) a soubor s informacemi o využití z [portálu účtů Azure](https://account.azure.com).

Pokud jste zákazník se smlouvou Enterprise, přečtěte si téma [Vysvětlení využití rezervací pro smlouvu Enterprise](understand-reserved-instance-usage-ea.md).

Tento článek vychází z předpokladu, že se rezervace uplatňuje na jedno předplatné. Pokud se rezervace použije na více než jedno předplatné, může se zvýhodněná rezervace rozdělit do více souborů CSV s informacemi o využití.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Využití rezervovaných instancí virtuálních počítačů

Pro potřeby následujících částí si představte, že používáte virtuální počítač Standard_DS1_v2 s Windows v oblasti USA – východ a informace o rezervované instanci virtuálního počítače vypadají takto:

| Pole | Hodnota |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Množství |1|
|Skladová jednotka (SKU) | Standard_DS1_v2|
|Oblast | eastus |

Rezervace pokrývá hardwarovou část virtuálního počítače, protože nasazený virtuální počítač vyhovuje atributům rezervace. Informace o tom, na který software Windows se rezervovaná instance virtuálního počítače nevztahuje, najdete v tématu [Náklady na software pro rezervované instance virtuálních počítačů Azure s Windows](reserved-instance-windows-software-costs.md).

### <a name="statement-section-of-csv-file-for-vms"></a>Oddíl výpisu v souboru CSV týkající se virtuálních počítačů

V této části souboru CSV najdete celkové využití týkající se vaší rezervace. V poli **Podkategorie měřiče** použijte filtr obsahující **"Reservation-"** . Měla by se zobrazit obrazovka podobná následujícímu snímku:

![Snímek obrazovky s filtrovanými podrobnostmi o využití rezervace a poplatcích](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Řádek **Rezervované základní virtuální počítače** obsahuje celkový počet hodin, na které se vztahuje rezervace. Na tomto řádku je částka 0,00 USD, protože náklady pokrývá rezervace. Řádek **Reservation-Windows Svr (1 Core)** se vztahuje na náklady na software pro Windows.

### <a name="daily-usage-section-of-csv-file"></a>Oddíl s denním využitím v souboru CSV

Filtrujte sloupec **Další informace** a zadejte své **ID rezervace**. Následující snímek obrazovky ukazuje pole související s rezervací.

![Snímek obrazovky s podrobnostmi o denním využití a poplatcích](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. Položka **ReservationId** v poli **Další informace** odpovídá rezervaci použité pro daný virtuální počítač.
2. **ConsumptionMeter** je ID měřiče pro daný virtuální počítač.
3. Řádek **Rezervované základní virtuální počítače** ve sloupci **Podkategorie měřiče** představuje částku 0 USD z oddílu s výpisem. Náklady na provoz tohoto virtuálního počítače jsou už zahrnuté v ceně rezervace.
4. **ID měřiče** je ID měřiče pro danou rezervaci. Náklady na tento měřič jsou 0 USD. Toto ID měřiče se zobrazí pro každý virtuální počítač, který má nárok na slevu za rezervaci.
5. Standard_DS1_v2 je virtuální počítač s jedním vCPU a je nasazený bez zvýhodněného hybridního využití Azure. To znamená, že tento měřič zaznamenává dodatečné poplatky za software pro Windows. Pokud chcete najít měřič odpovídající jednojádrovému virtuálnímu počítači řady D, přečtěte si téma [Náklady na software pro rezervované instance virtuálních počítačů Azure s Windows](reserved-instance-windows-software-costs.md). Pokud máte zvýhodněné hybridní využití Azure, tento dodatečný poplatek se vám neúčtuje.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Využití u rezervací SQL Database a Cosmos DB

Následující části vysvětlují sestavu využití na příkladu Azure SQL Database. Stejný postup můžete použít i k získání informací o využití pro Azure Cosmos DB.

Předpokládejme, že používáte SQL Database Gen 4 v oblasti USA – východ a informace o vaší rezervaci vypadají zhruba takto:

| Pole | Hodnota |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Množství |2|
|Produkt| SQL Database Gen 4 (2 jádra)|
|Oblast | eastus |

### <a name="statement-section-of-csv-file"></a>Oddíl výpisu v souboru CSV

Nastavte filtr podle názvu měřiče **Reserved Instance Usage** (Využití rezervovaných instancí) a vyberte požadovanou hodnotu ve sloupci **Kategorie měřiče** – Azure SQL Database nebo Azure Cosmos DB. Měla by se zobrazit obrazovka podobná následujícímu snímku:

![Snímek obrazovky s položkou Kategorie měřiče](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

Řádek **Reserved Instance Usage** (Využití rezervovaných instancí) obsahuje celkový počet hodin jádra, na který se vztahuje rezervace. Částka pro tento řádek činí 0 USD, protože náklady pokrývá rezervace.

### <a name="detail-section-of-csv-file"></a>Oddíl podrobností v souboru CSV

Filtrujte sloupec **Další informace** a zadejte své **ID rezervace**. Následující snímek obrazovky ukazuje pole související s rezervací rezervované kapacity služby SQL Database.

![Snímek obrazovky s podrobnými informacemi o souboru CSV pro rezervovanou kapacitu](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. Položka **ReservationId** v poli **Další informace** odpovídá rezervaci rezervované kapacity služby SQL Database, která se uplatňuje na tento prostředek služby SQL Database.
2. **ConsumptionMeter** je ID měřiče pro daný prostředek SQL Database.
3. **ID měřiče** je měřič rezervace. Náklady na tento měřič jsou 0 USD. Toto ID měřiče se nachází v souboru CSV pro všechny prostředky služby SQL Database, které mají nárok na slevu za rezervaci.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení způsobu uplatnění slevy za rezervaci](../manage/understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)
