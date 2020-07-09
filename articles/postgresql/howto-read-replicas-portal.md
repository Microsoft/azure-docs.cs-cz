---
title: Spravovat repliky čtení-Azure Portal-Azure Database for PostgreSQL-Single server
description: Naučte se spravovat repliky pro čtení Azure Database for PostgreSQL – jeden server z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 8e148a3dac8435a08c0f1735cd35d06c700e1e84
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106624"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Vytváření a Správa replik pro čtení v serveru Azure Database for PostgreSQL-Single z Azure Portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení v Azure Database for PostgreSQL z Azure Portal. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).


## <a name="prerequisites"></a>Požadavky
[Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) , který bude hlavním serverem.

## <a name="azure-replication-support"></a>Podpora replikace Azure

[Repliky čtení](concepts-read-replicas.md) a [logické dekódování](concepts-logical.md) závisí na protokolu Postgres Write předem log (WAL). Tyto dvě funkce vyžadují různé úrovně protokolování z Postgres. Logické dekódování potřebuje vyšší úroveň protokolování než repliky čtení.

Ke konfiguraci správné úrovně protokolování použijte parametr podpory replikace Azure. Podpora replikace Azure má tři možnosti nastavení:

* **Off** – vloží do Wal minimální informace. Toto nastavení není k dispozici na většině Azure Database for PostgreSQL serverů.  
* **Replika** – přesnější podrobnější informace než **vypnuto**. Toto je minimální úroveň protokolování potřebného pro fungování [replik pro čtení](concepts-read-replicas.md) . Toto nastavení je na většině serverů výchozí.
* **Logický** – další podrobnější informace než **replika** Toto je minimální úroveň protokolování pro logické dekódování, které funguje. V tomto nastavení fungují i repliky pro čtení.

Po změně tohoto parametru je nutné restartovat server. Interně tento parametr nastaví parametry Postgres `wal_level` , `max_replication_slots` a `max_wal_senders` .

## <a name="prepare-the-master-server"></a>Příprava hlavního serveru

1. V Azure Portal vyberte existující server Azure Database for PostgreSQL, který chcete použít jako hlavní server.

2. V nabídce serveru vyberte **replikace**. Pokud je podpora replikace Azure nastavená na aspoň **repliku**, můžete vytvořit repliky pro čtení. 

3. Pokud není podpora replikace Azure nastavená na aspoň **repliku**, nastavte ji. Vyberte **Uložit**.

   ![Azure Database for PostgreSQL – replikace – nastavit repliku a uložit](./media/howto-read-replicas-portal/set-replica-save.png)

4. Restartujte server, aby se změna projevila, a to tak, že vyberete **Ano**.

   ![Azure Database for PostgreSQL-replikace – potvrzení restartování](./media/howto-read-replicas-portal/confirm-restart.png)

5. Až se operace dokončí, zobrazí se vám dvě oznámení Azure Portal. Pro aktualizaci parametru serveru je k dispozici jedno oznámení. Pro okamžité restartování serveru je k dispozici jiné oznámení.

   ![Oznámení o úspěšnosti](./media/howto-read-replicas-portal/success-notifications.png)

6. Aktualizujte stránku Azure Portal a aktualizujte tak panel nástrojů pro replikaci. Nyní můžete vytvořit repliky čtení pro tento server.
   

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