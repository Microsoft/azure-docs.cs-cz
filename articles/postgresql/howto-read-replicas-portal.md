---
title: Spravovat repliky čtení-Azure Portal-Azure Database for PostgreSQL-Single server
description: Naučte se spravovat repliky pro čtení Azure Database for PostgreSQL – jeden server z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76768949"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Vytváření a Správa replik pro čtení v serveru Azure Database for PostgreSQL-Single z Azure Portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení v Azure Database for PostgreSQL z Azure Portal. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).


## <a name="prerequisites"></a>Požadavky
[Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) , který bude hlavním serverem.

## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky je nutné použít k přípravě hlavního serveru v Pro obecné účely nebo paměťově optimalizovaných úrovních. Hlavní server je připravený k replikaci nastavením parametru Azure. replication_support. Když se změní parametr replikace, vyžaduje se restartování serveru, aby se změna projevila. V Azure Portal jsou tyto dva kroky zapouzdřené jediným tlačítkem, **Povolit podporu replikace**.

1. V Azure Portal vyberte existující server Azure Database for PostgreSQL, který chcete použít jako hlavní server.

2. Na bočním panelu serveru v části **Nastavení**vyberte **replikace**.

> [!NOTE] 
> Pokud vidíte možnost **zakázat podporu replikace** šedě, nastavení replikace jsou již ve výchozím nastavení nastavena na vašem serveru. Můžete přeskočit následující kroky a přejít k části Vytvoření repliky pro čtení. 

3. Vyberte **Povolit podporu replikace**. 

   ![Povolit podporu replikace](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Potvrďte, že chcete povolit podporu replikace. Tato operace restartuje hlavní server. 

   ![Potvrďte možnost povolit podporu replikace](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Až se operace dokončí, zobrazí se vám dvě oznámení Azure Portal. Pro aktualizaci parametru serveru je k dispozici jedno oznámení. Pro okamžité restartování serveru je k dispozici jiné oznámení.

   ![Oznámení o úspěchu – povolit](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Aktualizujte stránku Azure Portal a aktualizujte tak panel nástrojů pro replikaci. Nyní můžete vytvořit repliky čtení pro tento server.

   ![Aktualizovaný panel nástrojů](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Povolení podpory replikace je jednorázová operace na hlavní server. Pro usnadnění je k dispozici tlačítko **zakázat podporu replikace** . Nedoporučujeme zakázat podporu replikace, pokud jste si jistí, že na tomto hlavním serveru nikdy nevytvoříte repliku. Podporu replikace nemůžete zakázat, pokud má hlavní server repliky existující.


## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
K vytvoření repliky pro čtení použijte následující postup:

1. Vyberte existující server Azure Database for PostgreSQL, který se má použít jako hlavní server. 

2. Na bočním panelu serveru v části **Nastavení**vyberte **replikace**.

3. Vyberte **Přidat repliku**.

   ![Přidání repliky](./media/howto-read-replicas-portal/add-replica.png)

4. Zadejte název repliky pro čtení. 

    ![Pojmenování repliky](./media/howto-read-replicas-portal/name-replica.png)

5. Vyberte umístění repliky. Výchozí umístění je stejné jako u hlavního serveru.

    ![Výběr umístění](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

6. Kliknutím na **OK** potvrďte vytváření repliky.

Replika se vytvoří pomocí stejného nastavení výpočtů a úložiště jako hlavní. Po vytvoření repliky se dá několik nastavení měnit nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Než bude nastavení hlavního serveru aktualizováno na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

Po vytvoření repliky pro čtení je možné ji zobrazit z okna **replikace** :

![Zobrazit novou repliku v okně replikace](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zastavení replikace
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit.

> [!IMPORTANT]
> Po zastavení replikace na hlavní server a repliku pro čtení ji nejde vrátit zpět. Replika čtení se stal samostatným serverem, který podporuje čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi hlavním serverem a replikou pro čtení z Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for PostgreSQL Server.

2. V nabídce Server v části **Nastavení**vyberte **replikace**.

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   ![Vybrat repliku](./media/howto-read-replicas-portal/select-replica.png)
 
4. Vyberte **zastavit replikaci**.

   ![Vyberte zastavit replikaci.](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Vyberte **OK** a zastavte replikaci.

   ![Potvrzení zastavení replikace](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru
Pokud chcete odstranit hlavní server, použijte stejný postup jako při odstraňování samostatného serveru Azure Database for PostgreSQL. 

> [!IMPORTANT]
> Při odstranění hlavního serveru se zastaví replikace do všech replik čtení. Repliky čtení se stanou samostatnými servery, které nyní podporují čtení i zápis.

Pokud chcete server z Azure Portal odstranit, postupujte takto:

1. V Azure Portal vyberte svůj hlavní Azure Database for PostgreSQL Server.

2. Otevřete stránku **Přehled** serveru. Vyberte **Odstranit**.

   ![Na stránce Přehled serveru vyberte možnost odstranění hlavního serveru.](./media/howto-read-replicas-portal/delete-server.png)
 
3. Zadejte název hlavního serveru, který chcete odstranit. Vyberte **Odstranit** a potvrďte tak odstranění hlavního serveru.

   ![Potvrďte odstranění hlavního serveru.](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Odstranění repliky
Repliku pro čtení můžete odstranit podobně jako při odstraňování hlavního serveru.

- V Azure Portal otevřete stránku **Přehled** repliky pro čtení. Vyberte **Odstranit**.

   ![Na stránce Přehled repliky vyberte, chcete-li odstranit repliku.](./media/howto-read-replicas-portal/delete-replica.png)
 
Repliku pro čtení z okna **replikace** můžete také odstranit pomocí následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for PostgreSQL Server.

2. V nabídce Server v části **Nastavení**vyberte **replikace**.

3. Vyberte repliku pro čtení, kterou chcete odstranit.

   ![Vyberte repliku, kterou chcete odstranit.](./media/howto-read-replicas-portal/select-replica.png)
 
4. Vyberte **Odstranit repliku**.

   ![Vyberte Odstranit repliku.](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Zadejte název repliky, která se má odstranit. Vyberte **Odstranit** a potvrďte odstranění repliky.

   ![Potvrďte odstranění repliky te.](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorování repliky
Ke sledování replik pro čtení jsou k dispozici dvě metriky.

### <a name="max-lag-across-replicas-metric"></a>Maximální prodleva napříč replikami
Metrika **maximální prodlevy napříč replikami** zobrazuje zpoždění v bajtech mezi hlavním serverem a nejvyšším zpožděním repliky. 

1.  V Azure Portal vyberte hlavní Azure Database for PostgreSQL Server.

2.  Vyberte **Metriky**. V okně **metriky** vyberte **maximální prodleva napříč replikami**.

    ![Monitorování maximální prodlevy mezi replikami](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Pro **agregaci**vyberte **Max**.


### <a name="replica-lag-metric"></a>Metrika prodlevy repliky
Metrika **prodlevy repliky** zobrazuje čas od poslední opakované transakce v replice. Pokud se ve vaší hlavní službě nevyskytují žádné transakce, metrika tuto časovou prodlevu odráží.

1. V Azure Portal vyberte Azure Database for PostgreSQL replice pro čtení.

2. Vyberte **Metriky**. V okně **metriky** vyberte **prodlevu repliky**.

   ![Sledování prodlevy repliky](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Pro **agregaci**vyberte **Max**. 
 
## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure CLI a REST API](howto-read-replicas-cli.md).