---
title: Požádat o zvýšení kvóty
description: Tato stránka popisuje, jak vytvořit žádost o podporu pro zvýšení kvót pro Azure SQL Database izolované databáze, servery a spravované instance.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: fb576b81adeec99e4080c744749097390d1add1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111095"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Zvýšení kvóty žádostí pro Azure SQL Database

Tento článek vysvětluje, jak požádat o zvýšení kvóty pro Azure SQL Database pro izolované databáze, servery a spravované instance. Vysvětluje také, jak povolit přístup k předplatnému v určité oblasti.

## <a id="newquota"></a>Vytvořit novou žádost o podporu

Pomocí následujícího postupu můžete vytvořit novou žádost o podporu z Azure Portal pro SQL Database.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + support](./media/quota-increase-request/help-plus-support.png)

1. V **okně pomoc a podpora**vyberte **Nová žádost o podporu**.

    ![Vytvořit novou žádost o podporu](./media/quota-increase-request/new-support-request.png)

1. Jako **typ problému**vyberte **omezení služby a předplatné (kvóty)** .

   ![Vyberte typ problému.](./media/quota-increase-request/select-quota-issue-type.png)

1. V poli **předplatné**vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné pro zvýšenou kvótu.](./media/quota-increase-request/select-subscription-support-request.png)

1. Jako **typ kvóty**vyberte jeden z následujících typů kvót:

   - **SQL Database** pro kvóty pro izolovanou databázi a elastický fond.
   - **SQL Database spravované instance** pro spravované instance.

   Pak vyberte **Další: řešení > >** .

   ![Vyberte typ kvóty.](./media/quota-increase-request/select-quota-type.png)

1. V okně **podrobností** vyberte **zadat podrobnosti** a zadejte další informace.

   ![Odkaz "poskytnout podrobnosti"](./media/quota-increase-request/provide-details-link.png)

Kliknutím na Zobrazit **Podrobnosti** zobrazíte okno **Podrobnosti o kvótě** , které vám umožní přidat další informace. Následující části popisují různé možnosti pro **SQL Database** a SQL Database typy kvót **spravované instance** .

## <a id="sqldbquota"></a>SQL Database typy kvót

Následující části popisují tři možnosti zvýšení kvóty pro **SQL Database** typy kvót:

- Jednotky transakcí databáze (DTU) na server
- Servery na předplatné
- Povolit přístup k předplatnému pro oblast

### <a name="database-transaction-units-dtus-per-server"></a>Jednotky transakcí databáze (DTU) na server

Pomocí následujících kroků můžete požádat o zvýšení DTU na server.

1. Vyberte typ kvóty **DTU (Database Transaction units) na server** .

1. V seznamu **prostředků** vyberte prostředek, který chcete cílit.

1. Do pole **Nová kvóta** zadejte nový limit DTU, který požadujete.

   ![Podrobnosti kvóty DTU](./media/quota-increase-request/quota-details-dtus.png)

Další informace najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-single-databases.md) a [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servery na předplatné

Pomocí následujících kroků můžete požádat o zvýšení počtu serverů na předplatné.

1. Vyberte typ kvóty **servery podle předplatného** .

1. V seznamu **umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je vázaná na předplatné v každé oblasti.

1. Do pole **Nová kvóta** zadejte žádost o maximální počet serverů v této oblasti.

   ![Podrobnosti o kvótě serverů](./media/quota-increase-request/quota-details-servers.png)

Další informace najdete v tématu [SQL Database omezení prostředků a zásady správného řízení prostředků](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a>Povolit přístup k předplatnému pro oblast

Některé typy nabídek nejsou k dispozici v každé oblasti. Může se zobrazit chyba, například následující:

`This location is not available for subscription`

Pokud vaše předplatné potřebuje přístup v konkrétní oblasti, požádejte prosím o přístup **jinou možnost žádosti o kvótu** . V žádosti zadejte podrobnosti nabídky a skladové položky, které chcete pro oblast povolit. Pokud chcete prozkoumat možnosti nabídky a SKU, přečtěte si téma [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Podrobnosti o jiné kvótě](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>Typ kvóty spravované instance

V poli Typ kvóty **spravované Instance SQL Server** použijte následující postup:

1. V seznamu **oblast** vyberte oblast Azure, do které chcete cílit.

1. Zadejte nové limity, které požadujete pro **podsíť** a **Vcore**.

   ![Podrobnosti kvóty spravované instance](./media/quota-increase-request/quota-details-managed-instance.png)

Další informace najdete v tématu [přehled Azure SQL Database omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Odeslat žádost

Posledním krokem je vyplnit zbývající podrobnosti žádosti o kvótu SQL Database. Pak vyberte **Další: zkontrolovat + vytvořit > >** a po kontrole podrobností žádosti klikněte na **vytvořit** , aby se žádost odeslala.

## <a name="next-steps"></a>Další kroky

Po odeslání žádosti se bude zkontrolovat. Na základě informací, které jste zadali ve formuláři, se vám bude kontaktovat odpověď.

Další informace o dalších omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
