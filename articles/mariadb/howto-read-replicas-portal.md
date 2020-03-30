---
title: Správa replik pro čtení – portál Azure – databáze Azure pro MariaDB
description: Tento článek popisuje, jak nastavit a spravovat repliky čtení v Azure Database pro MariaDB pomocí portálu
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5c28697b27e9cf910302b7379e1443f7e78e96b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530610"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky čtení v Azure Database for MariaDB pomocí portálu Azure

V tomto článku se dozvíte, jak vytvořit a spravovat repliky čtení ve službě Azure Database for MariaDB pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

- [Databáze Azure pro MariaDB server,](quickstart-create-mariadb-server-database-using-azure-portal.md) který se bude používat jako hlavní server.

> [!IMPORTANT]
> Funkce repliky pro čtení je k dispozici jenom pro Azure Database pro servery MariaDB v cenových úrovních s obecnou dostupností nebo optimalizací pro paměť. Ujistěte se, že hlavní server je v jedné z těchto cenových úrovní.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server replik pro čtení lze vytvořit pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vyberte existující Azure Database for MariaDB server, který chcete použít jako hlavní server. Tato akce otevře stránku **Přehled.**

3. V nabídce v části **NASTAVENÍ**vyberte **Možnost Replikace** .

4. Vyberte **Přidat repliku**.

   ![Databáze Azure pro MariaDB – replikace](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název serveru repliky.

    ![Databáze Azure pro MariaDB – název repliky](./media/howto-read-replica-portal/replica-name.png)

6. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako umístění hlavního serveru.

    ![Databáze Azure pro MariaDB – umístění repliky](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Replikace mezi oblastmi je ve verzi Preview. Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

7. Chcete-li potvrdit vytvoření repliky, vyberte **možnost OK.**

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru jako předloha. Konfiguraci serveru repliklze změnit po jeho vytvoření. Doporučuje se, aby konfigurace serveru repliky byla udržována na stejných nebo vyšších hodnotách než hlavní server, aby byla replika schopna držet krok s předlohou.

Jakmile je replika server byl vytvořen, lze zobrazit z **replikace.**

   ![Databáze Azure pro MariaDB – seznam replik](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na replikační server

> [!IMPORTANT]
> Zastavení replikace na server je nevratné. Jakmile je replikace zastavena mezi předlohou a replikou, nelze ji vrátit zpět. Replika server se pak stane samostatný server a nyní podporuje čtení i zápisy. Tento server nelze znovu přeměnit na repliku.

Chcete-li zastavit replikaci mezi hlavním serverem a replikami serveru z webu Azure Portal, použijte následující kroky:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro server MariaDB. 

2. V nabídce v části **NASTAVENÍ**vyberte **Možnost Replikace** .

3. Vyberte replikovací server, pro který chcete replikaci zastavit.

   ![Databáze Azure pro MariaDB – zastavení serveru pro výběr replikace](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **možnost Zastavit replikaci**.

   ![Databáze Azure pro MariaDB – zastavení replikace](./media/howto-read-replica-portal/stop-replication.png)

5. Potvrďte, že chcete zastavit replikaci klepnutím na **tlačítko OK**.

   ![Databáze Azure pro MariaDB – potvrzení replikace](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění repliky serveru

Chcete-li odstranit server replik pro čtení z webu Azure Portal, použijte následující kroky:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro server MariaDB.

2. V nabídce v části **NASTAVENÍ**vyberte **Možnost Replikace** .

3. Vyberte server replik, který chcete odstranit.

   ![Databáze Azure pro MariaDB – odstranění vybraného serveru repliky](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vybrat **odstranit repliku**

   ![Databáze Azure pro MariaDB – odstranění repliky](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a klepnutím na **tlačítko Odstranit** potvrďte odstranění repliky.  

   ![Databáze Azure pro MariaDB – odstranění potvrzení repliky](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru

> [!IMPORTANT]
> Odstraněním hlavního serveru se zastaví replikace na všechny servery replik a odstraní se samotný hlavní server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Chcete-li odstranit hlavní server z webu Azure Portal, použijte následující kroky:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro server MariaDB.

2. V části **Přehled**vyberte **odstranit**.

   ![Databáze Azure pro MariaDB – hlavní odstranění](./media/howto-read-replica-portal/delete-master-overview.png)

3. Zadejte název hlavního serveru a klepnutím na **tlačítko Odstranit** potvrďte odstranění hlavního serveru.  

   ![Databáze Azure pro MariaDB – hlavní odstranění](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte repliku databáze Azure pro server MariaDB, který chcete monitorovat.

2. V části **Monitorování** na postranním panelu vyberte **Metriky**:

3. V rozevíracím seznamu dostupných metrik **vyberte zpoždění replikace během několika sekund.**

   ![Vybrat zpoždění replikace](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Na obrázku níže se vybere časový rozsah 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazení zpoždění replikace pro vybraný časový rozsah. Na obrázku níže se zobrazí posledních 30 minut pro velké zatížení.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [čtení replik](concepts-read-replicas.md)