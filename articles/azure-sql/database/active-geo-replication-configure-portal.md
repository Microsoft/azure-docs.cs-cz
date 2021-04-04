---
title: 'Kurz: převzetí služeb při selhání geografické replikace & na portálu'
description: Nakonfigurujte geografickou replikaci pro databázi pomocí Azure Portal a spusťte převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673275"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Kurz: konfigurace aktivní geografické replikace a převzetí služeb při selhání v Azure Portal (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto článku se dozvíte, jak nakonfigurovat [aktivní geografickou replikaci pro Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) pomocí [Azure Portal](https://portal.azure.com) a zahájit převzetí služeb při selhání.

Osvědčené postupy při používání skupin s automatickým převzetím služeb při selhání najdete v tématu [osvědčené postupy pro Azure SQL Database](auto-failover-group-overview.md#best-practices-for-sql-database) a [osvědčené postupy pro SPRAVOVANOU instanci Azure SQL](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat aktivní geografickou replikaci pomocí Azure Portal, budete potřebovat následující prostředek:

* Databáze v Azure SQL Database: primární databáze, kterou chcete replikovat do jiné geografické oblasti.

> [!Note]
> Při použití Azure Portal můžete vytvořit pouze sekundární databázi v rámci stejného předplatného jako primární. Pokud je nutné, aby byla sekundární databáze v jiném předplatném, použijte příkaz [Create database REST API](/rest/api/sql/databases/createorupdate) nebo [ALTER DATABASE API jazyka Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Přidat sekundární databázi

V následujících krocích se vytvoří nová sekundární databáze v partnerství geografické replikace.  

Chcete-li přidat sekundární databázi, musíte být vlastníkem nebo spoluvlastníkem předplatného.

Sekundární databáze má stejný název jako primární databáze a má ve výchozím nastavení stejnou úroveň služby a výpočetní velikost. Sekundární databází může být jedna databáze nebo databáze ve fondu. Další informace najdete v tématu [nákupní model založený na DTU](service-tiers-dtu.md) a [nákupní model založený na Vcore](service-tiers-vcore.md).
Po vytvoření a osazení sekundární databáze se data začnou replikovat z primární databáze do nové sekundární databáze.

> [!NOTE]
> Pokud Partnerská databáze již existuje (například v důsledku ukončení předchozího vztahu geografické replikace), příkaz se nezdařil.

1. V [Azure Portal](https://portal.azure.com)přejděte k databázi, kterou chcete nastavit pro geografickou replikaci.
2. Na stránce SQL Database vyberte **geografickou replikaci** a potom vyberte oblast pro vytvoření sekundární databáze. Můžete vybrat libovolnou jinou oblast, než je oblast hostující primární databázi, ale doporučujeme použít [spárovánou oblast](../../best-practices-availability-paired-regions.md).

    ![Konfigurace geografické replikace](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Vyberte nebo nakonfigurujte server a cenovou úroveň pro sekundární databázi.

    ![vytvořit sekundární formulář](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Volitelně můžete do elastického fondu přidat sekundární databázi. Chcete-li vytvořit sekundární databázi ve fondu, klikněte na možnost **elastický fond** a vyberte fond na cílovém serveru. Fond již musí existovat na cílovém serveru. Tento pracovní postup nevytváří fond.
5. Kliknutím na **vytvořit** přidejte sekundárního.
6. Sekundární databáze je vytvořena a proces osazení začíná.

    ![sekundární mapa](./media/active-geo-replication-configure-portal/seeding0.png)
7. Po dokončení procesu osazení sekundární databáze zobrazí svůj stav.

    ![Osazení dokončeno](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Inicializace převzetí služeb při selhání

Sekundární databázi lze přepnout tak, aby se stala primární.  

1. V [Azure Portal](https://portal.azure.com)přejděte k primární databázi v rámci partnerství geografické replikace.
2. V okně SQL Database vyberte **všechna nastavení**  >  **geografické replikace**.
3. V seznamu **sekundárních** , vyberte databázi, která se má stát novou primární, a klikněte na **Vynucené převzetí služeb při selhání**.

    ![převzetí služeb při selhání](./media/active-geo-replication-configure-portal/secondaries.png)
4. Kliknutím na **Ano** zahájíte převzetí služeb při selhání.

Příkaz okamžitě přepne sekundární databázi do primární role. Tento proces normálně by měl být dokončen do 30 sec nebo méně.

Probíhá krátká doba, během které jsou obě databáze nedostupné (v pořadí 0 až 25 sekund) při přepínání rolí. Pokud má primární databáze více sekundárních databází, příkaz automaticky znovu nakonfiguruje ostatní sekundární sekundární připojení, aby se připojil k nové primární službě. Dokončení celé operace by za normálních okolností nemělo trvat déle než minutu.

> [!NOTE]
> Tento příkaz je určený pro rychlé obnovení databáze v případě výpadku. Vyvolá převzetí služeb při selhání bez synchronizace dat (vynucené převzetí služeb při selhání)  Pokud je primární je online a potvrzování transakcí, když je příkaz vydán, může dojít ke ztrátě dat.

## <a name="remove-secondary-database"></a>Odebrat sekundární databázi

Tato operace trvale ukončí replikaci do sekundární databáze a změní roli sekundární databáze na standardní databázi pro čtení a zápis. Pokud je připojení k sekundární databázi přerušeno, příkaz se zdaří, ale sekundární se nestane pro čtení a zápis až po obnovení připojení.  

1. V [Azure Portal](https://portal.azure.com)přejděte k primární databázi v rámci partnerství geografické replikace.
2. Na stránce databáze SQL vyberte **geografickou replikaci**.
3. V seznamu **sekundárních** umístění vyberte databázi, kterou chcete odebrat, ze partnerství geografické replikace.
4. Klikněte na **zastavit replikaci**.

    ![Odebrat sekundární](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Otevře se okno potvrzení. Kliknutím na **Ano** odeberete databázi ze partnerství geografické replikace. (Nastavte ji na databázi pro čtení i zápis, která není součástí žádné replikace.)

## <a name="next-steps"></a>Další kroky

* Další informace o aktivní geografické replikaci najdete v tématu [Aktivní geografická replikace](active-geo-replication-overview.md).
* Další informace o skupinách automatického převzetí služeb při selhání najdete v tématu [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md) .
* Přehled provozní kontinuity a scénáře najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).