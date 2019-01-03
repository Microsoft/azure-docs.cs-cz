---
title: 'Azure portal: Geografická replikace SQL Database | Dokumentace Microsoftu'
description: Konfigurace geografické replikace pro jednu, nebo součástí fondu databáze ve službě Azure SQL Database pomocí webu Azure portal a zahájit převzetí služeb při selhání
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: c25dd911b5648cdf4b64053ef26a7b70c9156074
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599752"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurace aktivní geografické replikace pro Azure SQL Database webu Azure portal a zahájit převzetí služeb při selhání

Tento článek ukazuje, jak nakonfigurovat [aktivní geografické replikace pro jednu databázi, databázi ve fondu] na logické server(sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) ve službě SQL Database pomocí [Webu azure portal](http://portal.azure.com) a k zahájení převzetí služeb při selhání.

Informace o skupinách automatické převzetí služeb při selhání s databázemi ve fondu a jeden, naleznete v tématu [osvědčené postupy používání skupin převzetí služeb při selhání s databázemi ve fondu a jeden](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Informace o skupinách automatické převzetí služeb při selhání s spravované instance (preview) najdete v tématu [osvědčené postupy používání skupin převzetí služeb při selhání s databázemi ve fondu a jeden](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat aktivní geografickou replikaci s využitím webu Azure portal, potřebujete následující prostředek:

* Azure SQL database: Primární databáze, který chcete replikovat do jiné geografické oblasti.

> [!Note]
Aktivní geografickou replikaci musí být mezi databázemi ve stejném předplatném.

## <a name="add-a-secondary-database"></a>Přidání sekundární databáze

Následující kroky vytvoření nové sekundární databáze v partnerství geografické replikace.  

Přidání sekundární databáze, musí být vlastníkem předplatného nebo spoluvlastník.

Sekundární databáze má stejný název jako primární databáze a ve výchozím nastavení, má stejnou službu vrstvy a vypočítat velikost. Sekundární databáze může být izolované databáze nebo databáze v elastickém fondu. Další informace najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
Jakmile sekundární se vytvoří a nasadí, zahájení dat replikace z primární databáze nové sekundární databáze.

> [!NOTE]
> Pokud partnerská databáze již existuje (například v důsledku ukončení předchozí relaci geografické replikace) příkaz se nezdaří.

1. V [webu Azure portal](http://portal.azure.com), přejděte do databáze, kterou chcete nastavit pro geografickou replikaci.
2. Na stránce služby SQL database, vyberte **geografickou replikaci**a pak vyberte oblasti se má vytvořit sekundární databáze. Můžete vybrat libovolnou oblast jiné než oblasti, který je hostitelem primární databáze, ale doporučujeme, abyste [spárované oblasti](../best-practices-availability-paired-regions.md).

    ![Konfigurace geografické replikace](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Vybrat nebo nakonfigurovat server a cenovou úroveň pro sekundární databázi.

    ![Konfigurovat sekundární](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Volitelně můžete přidat sekundární databáze do elastického fondu. Chcete-li vytvořit sekundární databáze ve fondu, klikněte na tlačítko **elastického fondu** a vyberte fond na cílovém serveru. Fond už musí existovat v cílovém serveru. Tento pracovní postup nelze vytvořit fond.
5. Klikněte na tlačítko **vytvořit** přidat sekundární.
6. Sekundární databáze se vytvoří a spustí proces osazení.

    ![Konfigurovat sekundární](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po dokončení procesu osazení se sekundární databáze zobrazí jeho stav.

    ![Synchronizace replik indexů dokončení](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Zahájit převzetí služeb při selhání

Sekundární databáze můžete přepnout na primární.  

1. V [webu Azure portal](http://portal.azure.com), přejděte na primární databázi v partnerství geografickou replikaci.
2. V okně databáze SQL vyberte **všechna nastavení** > **geografickou replikaci**.
3. V **sekundárních replik** vyberte databáze, kterou chcete nové primární a klikněte na tlačítko **převzetí služeb při selhání**.

    ![Převzetí služeb při selhání](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Klikněte na tlačítko **Ano** zahájíte převzetí služeb při selhání.

Příkaz okamžitě přepne sekundární databázi do primární role.

Je na krátkou dobu, během které jsou obě databáze není k dispozici (v řádu 0 až 25 sekund), zatímco jsou přepnuté role. Pokud primární databáze má více sekundární databází, tento příkaz automaticky změní konfiguraci sekundární databáze pro připojení k nové primární. Celá operace by měla trvat necelou minutu dokončit za normálních okolností.

> [!NOTE]
> Tento příkaz je určená pro rychlé obnovení databáze i v případě výpadku. Převzetí služeb při selhání se aktivuje bez synchronizace dat (Vynucené převzetí služeb při selhání).  Pokud je primární online a potvrzování transakcí při vydání příkazu se ztrátě dat může dojít.

## <a name="remove-secondary-database"></a>Odebrání sekundární databáze

Tato operace trvale ukončí replikaci do sekundární databáze a roli sekundární databáze se změní na standardní databázi pro čtení i zápis. Pokud nefunguje připojení k sekundární databázi, příkazu úspěšné, ale sekundární fakturuje se nestane čtení a zápis až po připojení se obnoví.  

1. V [webu Azure portal](http://portal.azure.com), přejděte na primární databázi v partnerství geografickou replikaci.
2. Na stránce služby SQL database, vyberte **geografickou replikaci**.
3. V **sekundárních replik** vyberte databáze, kterou chcete odebrat z partnerství geografickou replikaci.
4. Klikněte na tlačítko **zastavení replikace**.

    ![Odeberte sekundární](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Otevře se okno s potvrzením. Klikněte na tlačítko **Ano** odebrání partnerství geografickou replikaci databáze. (Nastavit do databáze pro čtení a zápis není součástí žádné replikační.)

## <a name="next-steps"></a>Další postup

* Další informace o aktivní geografickou replikaci, najdete v článku [aktivní geografickou replikaci](sql-database-active-geo-replication.md).
* Další informace o skupinách automatické převzetí služeb při selhání najdete v tématu [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md)
* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
