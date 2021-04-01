---
title: Požádat o zvýšení kvóty
description: Tato stránka popisuje, jak vytvořit žádost o podporu pro zvýšení kvót pro Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 27719663acfbdbcd7293defc4b746153359adb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251844"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Zvýšení kvóty požadavků pro Azure SQL Database a SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tento článek vysvětluje, jak požádat o zvýšení kvóty pro Azure SQL Database a Azure SQL Managed instance. Vysvětluje taky, jak povolit přístup k předplatnému pro určitou oblast a jak požádat o povolení konkrétního hardwaru v oblasti.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Vytvořit novou žádost o podporu

Pomocí následujícího postupu můžete vytvořit novou žádost o podporu z Azure Portal pro SQL Database.

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**.

   ![Odkaz Help + support](./media/quota-increase-request/help-plus-support.png)

1. V **okně pomoc a podpora** vyberte **Nová žádost o podporu**.

    ![Vytvořit novou žádost o podporu](./media/quota-increase-request/new-support-request.png)

1. Jako **typ problému** vyberte **omezení služby a předplatné (kvóty)**.

   ![Vyberte typ problému.](./media/quota-increase-request/select-quota-issue-type.png)

1. V poli **předplatné** vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Vyberte předplatné pro zvýšenou kvótu.](./media/quota-increase-request/select-subscription-support-request.png)

1. Jako **typ kvóty** vyberte jeden z následujících typů kvót:

   - **SQL Database** pro kvóty pro izolovanou databázi a elastický fond.
   - **SQL Database spravované instance** pro spravované instance.

   Pak vyberte **Další: řešení >>**.

   ![Vyberte typ kvóty.](./media/quota-increase-request/select-quota-type.png)

1. V okně **podrobností** vyberte **zadat podrobnosti** a zadejte další informace.

   ![Odkaz na zadání podrobností](./media/quota-increase-request/provide-details-link.png)

Kliknutím na **zadat podrobnosti** zobrazíte okno s **podrobnostmi o kvótě** , které vám umožní přidat další informace. Následující části popisují různé možnosti pro **SQL Database** a SQL Database typy kvót **spravované instance** .

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL Database typy kvót

Následující části popisují možnosti zvýšení kvóty pro **SQL Database** typy kvót:

- Jednotky transakcí databáze (DTU) na server
- Servery na předplatné
- Přístup k oblasti předplatných nebo konkrétního hardwaru

### <a name="database-transaction-units-dtus-per-server"></a>Jednotky transakcí databáze (DTU) na server

Pomocí následujících kroků můžete požádat o zvýšení DTU na server.

1. Vyberte typ kvóty **DTU (Database Transaction units) na server** .

1. V seznamu **prostředků** vyberte prostředek, který chcete cílit.

1. Do pole **Nová kvóta** zadejte nový limit DTU, který požadujete.

   ![Podrobnosti kvóty DTU](./media/quota-increase-request/quota-details-dtus.png)

Další informace najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](resource-limits-dtu-single-databases.md) a [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servery na předplatné

Pomocí následujících kroků můžete požádat o zvýšení počtu serverů na předplatné.

1. Vyberte typ kvóty **servery podle předplatného** .

1. V seznamu **umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je vázaná na předplatné v každé oblasti.

1. Do pole **Nová kvóta** zadejte žádost o maximální počet serverů v této oblasti.

   ![Podrobnosti o kvótě serverů](./media/quota-increase-request/quota-details-servers.png)

Další informace najdete v tématu [SQL Database omezení prostředků a zásady správného řízení prostředků](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Povolit přístup k předplatnému pro oblast

Některé typy nabídek nejsou k dispozici v každé oblasti. Může se zobrazit chyba, například následující:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Pokud vaše předplatné potřebuje přístup v konkrétní oblasti, vyberte možnost **přístup k oblasti** . V žádosti zadejte podrobnosti nabídky a skladové položky, které chcete pro oblast povolit. Pokud chcete prozkoumat možnosti nabídky a SKU, přečtěte si téma [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Vyberte typ kvóty **přístupu k oblasti** .

1. V seznamu **Vybrat umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je vázaná na předplatné v každé oblasti.

1. Zadejte **Model nákupu** a očekávané podrobnosti o **spotřebě** .

   ![Přístup k oblasti žádostí](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Žádost o povolení konkrétního hardwaru v oblasti

Pokud vydaná [hardwarová generace](service-tiers-vcore.md#hardware-generations) není ve vaší oblasti k dispozici (viz [dostupnost hardwaru](service-tiers-vcore.md#hardware-availability)), můžete si ji vyžádat pomocí následujících kroků.

1. Vyberte jiný typ kvóty **žádosti o kvótu** .

1. Do pole **Popis** uveďte svůj požadavek, včetně názvu generování hardwaru a názvu oblasti, ve které ho budete potřebovat.

   ![Požádat o hardware v nové oblasti](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Odeslání žádosti

Posledním krokem je vyplnit zbývající podrobnosti o žádosti o kvótu služby SQL Database. Pak vyberte **Další: Zkontrolovat a vytvořit >>** a po zkontrolování podrobností o žádosti kliknutím na **Vytvořit** žádost odešlete.

## <a name="next-steps"></a>Další kroky

Po odeslání vaši žádost zkontrolujeme. Budeme vás kontaktovat s odpovědí na základě informací, které jste uvedli ve formuláři.

Další informace o dalších omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
