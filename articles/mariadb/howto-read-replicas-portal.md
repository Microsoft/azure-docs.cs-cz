---
title: Spravovat repliky čtení-Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak nastavit a spravovat repliky pro čtení v Azure Database for MariaDB pomocí portálu.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 3ca6ef3c368a5f578cc90fae3923caa89f3b076a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665000"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky pro čtení v Azure Database for MariaDB pomocí Azure Portal

V tomto článku se naučíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database for MariaDB pomocí Azure Portal.

## <a name="prerequisites"></a>Předpoklady

- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) , který se bude používat jako zdrojový server.

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MariaDB servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

> [!IMPORTANT]
> Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

Server repliky pro čtení se dá vytvořit pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte existující server Azure Database for MariaDB, který chcete použít jako hlavní server. Tato akce otevře stránku s **přehledem** .

3. V nabídce v části **Nastavení** vyberte **replikace** .

4. Vyberte **Přidat repliku**.

   ![Azure Database for MariaDB – replikace](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název serveru repliky.

    ![Azure Database for MariaDB – název repliky](./media/howto-read-replica-portal/replica-name.png)

6. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako na zdrojovém serveru.

    ![Azure Database for MariaDB – umístění repliky](./media/howto-read-replica-portal/replica-location.png)

7. Vyberte **OK** a potvrďte tak vytvoření repliky.

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako hlavní. Konfiguraci serveru repliky je možné po vytvoření změnit. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná s hlavní hodnotou.

Po vytvoření serveru repliky ho můžete zobrazit z okna **replikace** .

   ![Repliky seznamu Azure Database for MariaDB](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky

> [!IMPORTANT]
> Zastavení replikace na serveru je nevratné. Po zastavení replikace mezi zdrojem a replikou je nelze vrátit zpět. Server repliky se pak stal samostatným serverem a teď podporuje čtení i zápis. Tento server nelze znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi zdrojem a serverem repliky ze Azure Portal, použijte následující postup:

1. V Azure Portal vyberte svůj zdrojový Azure Database for MariaDB Server. 

2. V nabídce v části **Nastavení** vyberte **replikace** .

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   ![Azure Database for MariaDB – zastavení replikace vybrat server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **zastavit replikaci**.

   ![Azure Database for MariaDB – zastavení replikace](./media/howto-read-replica-portal/stop-replication.png)

5. Kliknutím na **OK** potvrďte, že chcete replikaci zastavit.

   ![Azure Database for MariaDB – zastavení replikace potvrzení](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

K odstranění serveru repliky pro čtení z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj zdrojový Azure Database for MariaDB Server.

2. V nabídce v části **Nastavení** vyberte **replikace** .

3. Vyberte server repliky, který chcete odstranit.

   ![Azure Database for MariaDB – odstranění repliky vybrat server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vyberte **Odstranit repliku** .

   ![Azure Database for MariaDB – odstranění repliky](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a kliknutím na **Odstranit** potvrďte odstranění repliky.  

   ![Azure Database for MariaDB – odstranění repliky potvrzení](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Odstranění zdrojového serveru

> [!IMPORTANT]
> Odstraněním zdrojového serveru se zastaví replikace na všechny servery replik a odstraní se samotný zdrojový server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Pokud chcete odstranit zdrojový server z Azure Portal, použijte následující postup:

1. V Azure Portal vyberte svůj zdrojový Azure Database for MariaDB Server.

2. V **přehledu** vyberte **Odstranit**.

   ![Azure Database for MariaDB – odstranění hlavní větve](./media/howto-read-replica-portal/delete-master-overview.png)

3. Zadejte název zdrojového serveru a kliknutím na **Odstranit** potvrďte odstranění zdrojového serveru.  

   ![Azure Database for MariaDB-odstranit hlavní potvrzení](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. V [Azure Portal](https://portal.azure.com/)vyberte Azure Database for MariaDB server repliky, který chcete monitorovat.

2. V části **monitorování** v postranním panelu vyberte **metriky**:

3. V rozevíracím seznamu dostupných metrik vyberte **prodlevu replikace v sekundách** .

   ![Vybrat prodlevu replikace](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Následující obrázek vybere časový rozsah 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazí prodlevu replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut pro velkou úlohu.

   ![Vyberte časový rozsah 30 minut.](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [replikách pro čtení](concepts-read-replicas.md)
