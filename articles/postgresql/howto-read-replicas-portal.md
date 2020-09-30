---
title: Spravovat repliky čtení-Azure Portal-Azure Database for PostgreSQL-Single server
description: Naučte se spravovat repliky pro čtení Azure Database for PostgreSQL – jeden server z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d0de485158477496419dc394aaf6bdd1d28e772a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535810"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Vytváření a Správa replik pro čtení v serveru Azure Database for PostgreSQL-Single z Azure Portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení v Azure Database for PostgreSQL z Azure Portal. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).


## <a name="prerequisites"></a>Předpoklady
[Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) , který bude primárním serverem.

## <a name="azure-replication-support"></a>Podpora replikace Azure

[Repliky čtení](concepts-read-replicas.md) a [logické dekódování](concepts-logical.md) závisí na protokolu Postgres Write předem log (WAL). Tyto dvě funkce vyžadují různé úrovně protokolování z Postgres. Logické dekódování potřebuje vyšší úroveň protokolování než repliky čtení.

Ke konfiguraci správné úrovně protokolování použijte parametr podpory replikace Azure. Podpora replikace Azure má tři možnosti nastavení:

* **Off** – vloží do Wal minimální informace. Toto nastavení není k dispozici na většině Azure Database for PostgreSQL serverů.  
* **Replika** – přesnější podrobnější informace než **vypnuto**. Toto je minimální úroveň protokolování potřebného pro fungování [replik pro čtení](concepts-read-replicas.md) . Toto nastavení je na většině serverů výchozí.
* **Logický** – další podrobnější informace než **replika** Toto je minimální úroveň protokolování pro logické dekódování, které funguje. V tomto nastavení fungují i repliky pro čtení.

Po změně tohoto parametru je nutné restartovat server. Interně tento parametr nastaví parametry Postgres `wal_level` , `max_replication_slots` a `max_wal_senders` .

## <a name="prepare-the-primary-server"></a>Příprava primárního serveru

1. V Azure Portal vyberte existující server Azure Database for PostgreSQL, který chcete použít jako hlavní server.

2. V nabídce serveru vyberte **replikace**. Pokud je podpora replikace Azure nastavená na aspoň **repliku**, můžete vytvořit repliky pro čtení. 

3. Pokud není podpora replikace Azure nastavená na aspoň **repliku**, nastavte ji. Vyberte **Uložit**.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

4. Restartujte server, aby se změna projevila, a to tak, že vyberete **Ano**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

5. Až se operace dokončí, zobrazí se vám dvě oznámení Azure Portal. Pro aktualizaci parametru serveru je k dispozici jedno oznámení. Pro okamžité restartování serveru je k dispozici jiné oznámení.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

6. Aktualizujte stránku Azure Portal a aktualizujte tak panel nástrojů pro replikaci. Nyní můžete vytvořit repliky čtení pro tento server.
   

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
K vytvoření repliky pro čtení použijte následující postup:

1. Vyberte existující server Azure Database for PostgreSQL, který se má použít jako primární server. 

2. Na bočním panelu serveru v části **Nastavení**vyberte **replikace**.

3. Vyberte **Přidat repliku**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

4. Zadejte název repliky pro čtení. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

5. Vyberte umístění repliky. Výchozí umístění je stejné jako na primárním serveru.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::

   > [!NOTE]
   > Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

6. Kliknutím na **OK** potvrďte vytváření repliky.

Po vytvoření repliky pro čtení je možné ji zobrazit z okna **replikace** :

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 

> [!IMPORTANT]
> Přečtěte si [část s informacemi v tématu Přehled repliky čtení](concepts-read-replicas.md#considerations).
>
> Než se nastavení primárního serveru aktualizuje na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

## <a name="stop-replication"></a>Zastavení replikace
Replikaci mezi primárním serverem a replikou pro čtení můžete zastavit.

> [!IMPORTANT]
> Po zastavení replikace na primární server a repliku pro čtení ji nejde vrátit zpátky. Replika čtení se stal samostatným serverem, který podporuje čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi primárním serverem a replikou pro čtení z Azure Portal, postupujte podle těchto kroků:

1. V Azure Portal vyberte svůj primární Azure Database for PostgreSQL Server.

2. V nabídce Server v části **Nastavení**vyberte **replikace**.

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
4. Vyberte **zastavit replikaci**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
5. Vyberte **OK** a zastavte replikaci.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 

## <a name="delete-a-primary-server"></a>Odstranění primárního serveru
Pokud chcete odstranit primární server, použijte stejný postup jako při odstraňování samostatného serveru Azure Database for PostgreSQL. 

> [!IMPORTANT]
> Při odstranění primárního serveru se zastaví replikace do všech replik pro čtení. Repliky čtení se stanou samostatnými servery, které nyní podporují čtení i zápis.

Pokud chcete server z Azure Portal odstranit, postupujte takto:

1. V Azure Portal vyberte svůj primární Azure Database for PostgreSQL Server.

2. Otevřete stránku **Přehled** serveru. Vyberte **Odstranit**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
3. Zadejte název primárního serveru, který chcete odstranit. Výběrem **Odstranit** potvrďte odstranění primárního serveru.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 

## <a name="delete-a-replica"></a>Odstranění repliky
Repliku pro čtení můžete odstranit podobně jako při odstraňování primárního serveru.

- V Azure Portal otevřete stránku **Přehled** repliky pro čtení. Vyberte **Odstranit**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
Repliku pro čtení z okna **replikace** můžete také odstranit pomocí následujících kroků:

1. V Azure Portal vyberte svůj primární Azure Database for PostgreSQL Server.

2. V nabídce Server v části **Nastavení**vyberte **replikace**.

3. Vyberte repliku pro čtení, kterou chcete odstranit.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
4. Vyberte **Odstranit repliku**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
5. Zadejte název repliky, která se má odstranit. Vyberte **Odstranit** a potvrďte odstranění repliky.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 

## <a name="monitor-a-replica"></a>Monitorování repliky
Ke sledování replik pro čtení jsou k dispozici dvě metriky.

### <a name="max-lag-across-replicas-metric"></a>Maximální prodleva napříč replikami
Metrika **maximální prodlevy napříč replikami** zobrazuje zpoždění v bajtech mezi primárním serverem a nejvyšším zpožděním repliky. 

1.  V Azure Portal vyberte primární server Azure Database for PostgreSQL.

2.  Vyberte **Metriky**. V okně **metriky** vyberte **maximální prodleva napříč replikami**.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
3.  Pro **agregaci**vyberte **Max**.


### <a name="replica-lag-metric"></a>Metrika prodlevy repliky
Metrika **prodlevy repliky** zobrazuje čas od poslední opakované transakce v replice. Pokud se ve vaší hlavní službě nevyskytují žádné transakce, metrika tuto časovou prodlevu odráží.

1. V Azure Portal vyberte Azure Database for PostgreSQL replice pro čtení.

2. Vyberte **Metriky**. V okně **metriky** vyberte **prodlevu repliky**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Azure Database for PostgreSQL – replikace – nastavit repliku a uložit":::
 
3. Pro **agregaci**vyberte **Max**. 
 
## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure CLI a REST API](howto-read-replicas-cli.md).