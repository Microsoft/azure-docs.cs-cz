---
title: Zobrazit rezervace pro prostředky Azure | Dokumentace Microsoftu
description: Zjistěte, jak zobrazení Azure rezervací na webu Azure Portal.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: c7522076987aacacc6fde6a0c9d2fa867a3f14aa
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314036"
---
# <a name="view-reservations-for-azure-in-the-azure-portal"></a>Zobrazit rezervace pro Azure na webu Azure Portal

V závislosti na typu předplatného a oprávnění existuje několik způsobů, jak zobrazit rezervace pro prostředky Azure.

## <a name="view-reservations-as-owner-or-reader"></a>Zobrazit rezervace jako vlastník nebo čtenáře

Ve výchozím nastavení, při nákupu rezervace, je správce účtu může zobrazit a rezervace. Vy a správce účtu automaticky získáte roli vlastníka na rezervaci. Povolit ostatním uživatelům zobrazit rezervace, je musíte přidat jako **vlastníka** nebo **čtečky** na rezervaci. Další informace najdete v tématu [přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Chcete-li zobrazit rezervace jako vlastník nebo čtečka otisku prstů,

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Hledat na **rezervace**.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-view-reservation/portal-reservation-search.png)

1. Zobrazí seznam rezervací, kde máte roli vlastníka nebo Čtenář.

Pokud potřebujete změnit obor rezervace, rezervaci dělené tunelové propojení, nebo změnit, kdo může spravovat rezervaci, naleznete v tématu [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Zobrazit transakce rezervace pro podnikové registrace

 Pokud máte partnera vedla podnikového zápisu, zobrazit rezervace tak, že přejdete do **sestavy** přes portál EA. Pro jiné podnikové registrace můžete zobrazit rezervace na portálu EA a na webu Azure Portal. Musíte být správce EA, aby zobrazení rezervace transakcí.

Chcete-li zobrazit transakce rezervace na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Vyberte **rezervace transakce**.
1. Chcete-li filtrovat výsledky, vyberte **Timespan**, **typ**, nebo **popis**.
1. Vyberte **Použít**.

    ![Snímek obrazovky zobrazující rezervace výsledky transakce](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

K získání dat pomocí rozhraní API, najdete v článku [poplatky za transakce získat rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Další postup

Další informace o Azure rezervace, naleznete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předem služby Cosmos DB vyhrazené kapacity](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervace pro předplatná CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
