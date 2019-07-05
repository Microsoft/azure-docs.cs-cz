---
title: Zobrazit rezervace pro prostředky Azure | Dokumentace Microsoftu
description: Zjistěte, jak zobrazení Azure rezervací na webu Azure Portal.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 21bf96866c14615009a17279ff2fdd04bf4116ad
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490272"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Zobrazení Azure rezervací na webu Azure Portal

V závislosti na typu předplatného a oprávnění existuje několik způsobů, jak zobrazit rezervace pro Azure.

## <a name="view-purchased-reservations"></a>Zobrazit zakoupená rezervace

Ve výchozím nastavení, při nákupu rezervace, je správce účtu může zobrazit a rezervace. Vy a správce účtu automaticky získáte roli vlastníka na objednávka rezervace a rezervace. Povolit ostatním uživatelům zobrazit rezervace, je musíte přidat jako **vlastníka** nebo **čtečky** v objednávce rezervace nebo rezervace.

Další informace najdete v tématu [přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Chcete-li zobrazit rezervace jako vlastník nebo čtečka otisku prstů,

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte **rezervace**.
    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-view-reservation/portal-reservation-search.png)  
3. Tento seznam obsahuje všechny rezervace, kde máte roli vlastníka nebo Čtenář. Každá položka zobrazuje procento poslední známé využití.  
    ![Příklad zobrazující seznam rezervací](./media/billing-view-reservation/view-reservations.png)
4. Vyberte rezervaci a zobrazit trend využití za posledních pět dní.  
    ![Příklad zobrazující rezervace využití trend](./media/billing-view-reservation/reservation-utilization.png)
5. Můžete získat také [rezervace využití](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) pomocí rozhraní API využití rezervovaných instancí a s [Microsoft Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

Pokud potřebujete změnit obor rezervace, rezervaci dělené tunelové propojení, nebo změnit, kdo může spravovat rezervaci, naleznete v tématu [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Zobrazit transakce rezervace pro podnikové registrace

 Pokud máte partnera vedla podnikového zápisu, zobrazit rezervace tak, že přejdete do **sestavy** přes portál EA. Pro jiné podnikové registrace můžete zobrazit rezervace na portálu EA a na webu Azure Portal. Musíte být správce EA, aby zobrazení rezervace transakcí.

Chcete-li zobrazit transakce rezervace na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Vyberte **rezervace transakce**.
1. Chcete-li filtrovat výsledky, vyberte **Timespan**, **typ**, nebo **popis**.
1. Vyberte **Použít**.

    ![Snímek obrazovky zobrazující rezervace výsledky transakce](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

K získání dat pomocí rozhraní API, najdete v článku [poplatky za transakce získat rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Další postup

Další informace o Azure rezervace, naleznete v následujících článcích:

- [Co jsou rezervace pro Azure?](billing-save-compute-costs-reservations.md)
- [Správa rezervací pro Azure](billing-manage-reserved-vm-instance.md)

Koupit plán služby:

- [Předem služby Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Koupit plán softwaru:

- [Předplatíte Red Hat softwarové plány z Azure rezervací](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

Vysvětlení využití:

- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
