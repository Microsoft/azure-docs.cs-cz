---
title: Zobrazení rezervací prostředků Azure | Microsoft Docs
description: Zjistěte, jak na webu Azure Portal zobrazit rezervace Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199241"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Zobrazení rezervací Azure na webu Azure Portal

V závislosti na typu a oprávněních vašeho předplatného existuje několik způsobů, jak zobrazit rezervace v Azure.

## <a name="view-purchased-reservations"></a>Zobrazení zakoupených rezervací

Když zakoupíte rezervaci, ve výchozím nastavení ji můžete zobrazit vy a správce účtu. Vy a správce účtu automaticky získáte roli Vlastník objednávky rezervace a samotné rezervace. Pokud chcete umožnit ostatním uživatelům zobrazit rezervaci, musíte je přidat jako **vlastníka** nebo **čtenáře** objednávky rezervace nebo samotné rezervace.

Další informace najdete v tématu [Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Pokud chcete zobrazit rezervaci jako vlastník nebo čtenář, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte **Rezervace**.
    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/view-reservations/portal-reservation-search.png)  
3. V seznamu se zobrazí všechny rezervace, u kterých máte roli Vlastník nebo Čtenář. U každé rezervace se zobrazí poslední známé procento využití.  
    ![Příklad ukazující seznam rezervací](./media/view-reservations/view-reservations.png)
4. Po výběru rezervace se zobrazí trend využití za posledních pět dnů.  
    ![Příklad ukazující trend využití rezervace](./media/view-reservations/reservation-utilization.png)
5. Informace o [využití rezervací](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) můžete získat také pomocí rozhraní API Podrobnosti o rezervované instanci a [balíčku obsahu Power BI pro Microsoft Azure Consumption Insights](/power-bi/service-connect-to-azure-consumption-insights).

Pokud potřebujete změnit rozsah rezervace, rozdělit rezervaci nebo změnit, kdo může rezervaci spravovat, přečtěte si téma [Správa rezervací Azure](manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Zobrazení transakcí rezervací u smluv Enterprise

 Pokud máte smlouvu Enterprise vedenou partnerem, můžete rezervace zobrazit na portálu EA Portal v části **Sestavy**. V případě ostatních smluv Enterprise můžete rezervace zobrazit na portálu EA Portal i na webu Azure Portal. Pokud chcete zobrazit transakce rezervací, musíte být správcem EA.

Pokud chcete zobrazit transakce rezervací na webu Azure Portal, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/view-reservations/portal-cm-billing-search.png)

1. Vyberte **Transakce rezervací**.
1. Pokud chcete filtrovat výsledky, vyberte **Časový rozsah**, **Typ** nebo **Popis**.
1. Vyberte **Použít**.

    ![Snímek obrazovky ukazující výsledky transakcí rezervací](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Informace o získání dat pomocí rozhraní API najdete v tématu [Získání informací o poplatcích za rezervované instance pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace v Azure?](save-compute-costs-reservations.md)
- [Správa rezervací v Azure](manage-reserved-vm-instance.md)

Zakoupení plánu služby:

- [Předplacení rezervované kapacity služby Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Zakoupení plánu softwaru:

- [Předplacení plánů softwaru Red Hat z rezervací Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Vysvětlení využití:

- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
