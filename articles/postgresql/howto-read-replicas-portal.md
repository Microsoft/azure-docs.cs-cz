---
title: Spravovat repliky čtení-Azure Portal-Azure Database for PostgreSQL-Single server
description: Naučte se spravovat repliky pro čtení Azure Database for PostgreSQL – jeden server z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 623b9c1eccefe5d7e6027ddbed61c89720d98e9a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884480"
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

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

4. Restartujte server, aby se změna projevila, a to tak, že vyberete **Ano**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL-replikace – potvrzení restartování":::

5. Až se operace dokončí, zobrazí se vám dvě oznámení Azure Portal. Pro aktualizaci parametru serveru je k dispozici jedno oznámení. Pro okamžité restartování serveru je k dispozici jiné oznámení.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Oznámení o úspěšnosti":::

6. Aktualizujte stránku Azure Portal a aktualizujte tak panel nástrojů pro replikaci. Nyní můžete vytvořit repliky čtení pro tento server.
   

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
K vytvoření repliky pro čtení použijte následující postup:

1. Vyberte existující server Azure Database for PostgreSQL, který se má použít jako hlavní server. 

2. Na bočním panelu serveru v části **Nastavení**vyberte **replikace**.

3. Vyberte **Přidat repliku**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Přidání repliky":::

4. Zadejte název repliky pro čtení. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Pojmenování repliky":::

5. Vyberte umístění repliky. Výchozí umístění je stejné jako u hlavního serveru.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Vybrat umístění":::

   > [!NOTE]
   > Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

6. Kliknutím na **OK** potvrďte vytváření repliky.

Po vytvoření repliky pro čtení je možné ji zobrazit z okna **replikace** :

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Zobrazit novou repliku v okně replikace":::
 

> [!IMPORTANT]
> Přečtěte si [část s informacemi v tématu Přehled repliky čtení](concepts-read-replicas.md#considerations).
>
> Než bude nastavení hlavního serveru aktualizováno na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

## <a name="stop-replication"></a>Zastavení replikace
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit.

> [!IMPORTANT]
> Po zastavení replikace na hlavní server a repliku pro čtení ji nejde vrátit zpět. Replika čtení se stal samostatným serverem, který podporuje čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi hlavním serverem a replikou pro čtení z Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for PostgreSQL Server.

2. V nabídce Server v části **Nastavení**vyberte **replikace**.

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Vybrat repliku":::
 
4. Vyberte **zastavit replikaci**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Vyberte zastavit replikaci.":::
 
5. Vyberte **OK** a zastavte replikaci.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Potvrzení zastavení replikace":::
 

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru
Pokud chcete odstranit hlavní server, použijte stejný postup jako při odstraňování samostatného serveru Azure Database for PostgreSQL. 

> [!IMPORTANT]
> Při odstranění hlavního serveru se zastaví replikace do všech replik čtení. Repliky čtení se stanou samostatnými servery, které nyní podporují čtení i zápis.

Pokud chcete server z Azure Portal odstranit, postupujte takto:

1. V Azure Portal vyberte svůj hlavní Azure Database for PostgreSQL Server.

2. Otevřete stránku **Přehled** serveru. Vyberte **Odstranit**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Na stránce Přehled serveru vyberte možnost odstranění hlavního serveru.":::
 
3. Zadejte název hlavního serveru, který chcete odstranit. Vyberte **Odstranit** a potvrďte tak odstranění hlavního serveru.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Potvrďte odstranění hlavního serveru.":::
 

## <a name="delete-a-replica"></a>Odstranění repliky
Repliku pro čtení můžete odstranit podobně jako při odstraňování hlavního serveru.

- V Azure Portal otevřete stránku **Přehled** repliky pro čtení. Vyberte **Odstranit**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Na stránce Přehled repliky vyberte, chcete-li odstranit repliku.":::
 
Repliku pro čtení z okna **replikace** můžete také odstranit pomocí následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for PostgreSQL Server.

2. V nabídce Server v části **Nastavení**vyberte **replikace**.

3. Vyberte repliku pro čtení, kterou chcete odstranit.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Vyberte repliku, kterou chcete odstranit.":::
 
4. Vyberte **Odstranit repliku**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Vyberte Odstranit repliku.":::
 
5. Zadejte název repliky, která se má odstranit. Vyberte **Odstranit** a potvrďte odstranění repliky.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Potvrďte odstranění repliky te.":::
 

## <a name="monitor-a-replica"></a>Monitorování repliky
Ke sledování replik pro čtení jsou k dispozici dvě metriky.

### <a name="max-lag-across-replicas-metric"></a>Maximální prodleva napříč replikami
Metrika **maximální prodlevy napříč replikami** zobrazuje zpoždění v bajtech mezi hlavním serverem a nejvyšším zpožděním repliky. 

1.  V Azure Portal vyberte hlavní Azure Database for PostgreSQL Server.

2.  Vyberte **Metriky**. V okně **metriky** vyberte **maximální prodleva napříč replikami**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Monitorování maximální prodlevy mezi replikami":::
 
3.  Pro **agregaci**vyberte **Max**.


### <a name="replica-lag-metric"></a>Metrika prodlevy repliky
Metrika **prodlevy repliky** zobrazuje čas od poslední opakované transakce v replice. Pokud se ve vaší hlavní službě nevyskytují žádné transakce, metrika tuto časovou prodlevu odráží.

1. V Azure Portal vyberte Azure Database for PostgreSQL replice pro čtení.

2. Vyberte **Metriky**. V okně **metriky** vyberte **prodlevu repliky**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Sledování prodlevy repliky":::
 
3. Pro **agregaci**vyberte **Max**. 
 
## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure CLI a REST API](howto-read-replicas-cli.md).