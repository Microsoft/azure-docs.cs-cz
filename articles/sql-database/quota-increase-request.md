---
title: Žádost o zvýšení kvóty
description: Tato stránka popisuje, jak vytvořit žádost o podporu pro zvýšení kvót pro Azure SQL Database jednotlivé databáze, servery a spravované instance.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586151"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Zvýšení kvóty požadavků pro Azure SQL Database

Tento článek vysvětluje, jak požádat o zvýšení kvóty pro Azure SQL Database pro jednotlivé databáze, servery a spravované instance. Také vysvětluje, jak povolit přístup k předplatnému do oblasti.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Vytvoření nové žádosti o podporu

Pomocí následujících kroků vytvořte nový požadavek na podporu z portálu Azure pro SQL Database.

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Nápověda + podpora**.

   ![Odkaz Nápověda + podpora](./media/quota-increase-request/help-plus-support.png)

1. V **nápovědě + podpoře**vyberte Nový **požadavek na podporu**.

    ![Vytvoření nové žádosti o podporu](./media/quota-increase-request/new-support-request.png)

1. V **případě typu Problém**vyberte možnost Omezení služeb a předplatného **(kvóty).**

   ![Výběr typu problému](./media/quota-increase-request/select-quota-issue-type.png)

1. V **části Předplatné**vyberte předplatné, jehož kvótu chcete zvýšit.

   ![Výběr předplatného pro zvýšenou kvótu](./media/quota-increase-request/select-subscription-support-request.png)

1. V **případě typu Kvóta**vyberte jeden z následujících typů kvót:

   - **SQL Database** pro kvóty jednoho a elastického fondu.
   - **Instance spravované databáze SQL Database** pro spravované instance.

   Pak vyberte **Další: Řešení >>**.

   ![Výběr typu kvóty](./media/quota-increase-request/select-quota-type.png)

1. V okně **Podrobnosti** vyberte **Zadat podrobnosti** a zadejte další informace.

   ![Odkaz "Poskytnout podrobnosti"](./media/quota-increase-request/provide-details-link.png)

Kliknutím **na tlačítko Poskytnout podrobnosti** se zobrazí okno **Podrobnosti o kvótě,** které umožňuje přidat další informace. Následující části popisují různé možnosti pro typy kvót **spravovaných instancí** **databáze SQL** a SQL Database.

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Typy kvót sql database

Následující části popisují tři možnosti zvýšení kvóty pro typy kvót **sql database:**

- Jednotky transakcí databáze (DTU) na server
- Servery na předplatné
- Povolení přístupu předplatného do oblasti

### <a name="database-transaction-units-dtus-per-server"></a>Jednotky transakcí databáze (DTU) na server

Pomocí následujících kroků můžete požádat o zvýšení jednotky DTU na server.

1. Vyberte **jednotky transakcí databáze (DTU) podle typu kvóty serveru.**

1. V seznamu **Zdroje** vyberte zdroj, na který chcete cílit.

1. Do pole **Nová kvóta** zadejte nový limit DTU, který požadujete.

   ![Podrobnosti o kvótě DTU](./media/quota-increase-request/quota-details-dtus.png)

Další informace naleznete v [tématu Omezení prostředků pro jednotlivé databáze pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-single-databases.md) a [omezení prostředků pro elastické fondy pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servery na předplatné

Pomocí následujících kroků můžete požádat o zvýšení počtu serverů na odběr.

1. Vyberte typ **kvóty Servery podle předplatného.**

1. V seznamu **Umístění** vyberte oblast Azure, kterou chcete použít. Kvóta je na předplatné v každé oblasti.

1. Do pole **Nová kvóta** zadejte požadavek na maximální počet serverů v této oblasti.

   ![Podrobnosti o kvótě serverů](./media/quota-increase-request/quota-details-servers.png)

Další informace naleznete v [tématu omezení prostředků databáze SQL a zásadsprávné hospo-](sql-database-resource-limits-database-server.md)

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Povolení přístupu předplatného do oblasti

Některé typy nabídek nejsou k dispozici ve všech oblastech. Může se zobrazit chyba, například následující:

`This location is not available for subscription`

Pokud vaše předplatné potřebuje přístup v určité oblasti, použijte možnost **Žádost o další kvótu** a požádejte o přístup. V žádosti zadejte nabídku a podrobnosti skladové položky, které chcete povolit pro oblast. Informace o možnostech nabídky a skladové položky najdete v článku [Ceny databáze Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Další podrobnosti o kvótách](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Typ kvóty spravované instance

Pro typ kvóty **spravované instance serveru SQL Server** použijte následující kroky:

1. V seznamu **Oblast** vyberte oblast Azure, na kterou chcete cílit.

1. Zadejte nová omezení, která požadujete pro **podsíť** a **virtuální jádro**.

   ![Podrobnosti o kvótě spravované instance](./media/quota-increase-request/quota-details-managed-instance.png)

Další informace naleznete v [tématu Přehled omezení prostředků spravovaných instancí Azure SQL Database](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Odešlete svůj požadavek

Posledním krokem je vyplnění zbývajících podrobností o požadavku na kvótu databáze SQL. Pak vyberte **Další: Kontrola + vytvoření>>** a po kontrole podrobností žádosti klikněte na **Vytvořit** a odešlete žádost.

## <a name="next-steps"></a>Další kroky

Po odeslání žádosti bude žádost zkontrolována. Budete kontaktováni s odpovědí na základě informací, které jste poskytli ve formuláři.

Další informace o dalších omezeních Azure najdete v [tématu Limity předplatného azure a služeb, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
