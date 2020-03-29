---
title: Správa replik čtení – portál Azure – databáze Azure pro MySQL
description: Zjistěte, jak nastavit a spravovat repliky čtení v Azure Database for MySQL pomocí portálu Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b7226e5ae8c468339e02dbe87e279266e4609da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063479"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky čtení v Azure Database for MySQL pomocí portálu Azure

V tomto článku se dozvíte, jak vytvořit a spravovat repliky čtení ve službě Azure Database for MySQL pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

- [Databáze Azure pro mysql server,](quickstart-create-mysql-server-database-using-azure-portal.md) který se bude používat jako hlavní server.

> [!IMPORTANT]
> Funkce repliky pro čtení je k dispozici jenom pro Azure Database pro servery MySQL v cenových úrovních s obecnou dostupností nebo optimalizací pro paměť. Ujistěte se, že hlavní server je v jedné z těchto cenových úrovní.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server replik pro čtení lze vytvořit pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vyberte existující Azure Database for MySQL server, který chcete použít jako hlavní server. Tato akce otevře stránku **Přehled.**

3. V nabídce v části **NASTAVENÍ**vyberte **Možnost Replikace** .

4. Vyberte **Přidat repliku**.

   ![Databáze Azure pro MySQL – replikace](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název serveru repliky.

    ![Databáze Azure pro MySQL – název repliky](./media/howto-read-replica-portal/replica-name.png)

6. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako umístění hlavního serveru.

    ![Databáze Azure pro MySQL – umístění repliky](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

7. Chcete-li potvrdit vytvoření repliky, vyberte **možnost OK.**

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru jako předloha. Konfiguraci serveru repliklze změnit po jeho vytvoření. Doporučuje se, aby konfigurace serveru repliky byla udržována na stejných nebo vyšších hodnotách než hlavní server, aby byla replika schopna držet krok s předlohou.

Jakmile je replika server byl vytvořen, lze zobrazit z **replikace.**

   ![Databáze Azure pro MySQL – seznam replik](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na replikační server

> [!IMPORTANT]
> Zastavení replikace na server je nevratné. Jakmile je replikace zastavena mezi předlohou a replikou, nelze ji vrátit zpět. Replika server se pak stane samostatný server a nyní podporuje čtení i zápisy. Tento server nelze znovu přeměnit na repliku.

Chcete-li zastavit replikaci mezi hlavním serverem a replikami serveru z webu Azure Portal, použijte následující kroky:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro server MySQL. 

2. V nabídce v části **NASTAVENÍ**vyberte **Možnost Replikace** .

3. Vyberte replikovací server, pro který chcete replikaci zastavit.

   ![Databáze Azure pro MySQL – zastavení výběru serveru replikace](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **možnost Zastavit replikaci**.

   ![Databáze Azure pro MySQL – zastavení replikace](./media/howto-read-replica-portal/stop-replication.png)

5. Potvrďte, že chcete zastavit replikaci klepnutím na **tlačítko OK**.

   ![Azure Database for MySQL – potvrzení replikace](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění repliky serveru

Chcete-li odstranit server replik pro čtení z webu Azure Portal, použijte následující kroky:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro server MySQL.

2. V nabídce v části **NASTAVENÍ**vyberte **Možnost Replikace** .

3. Vyberte server replik, který chcete odstranit.

   ![Databáze Azure pro MySQL – odstranění vybraného serveru repliky](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vybrat **odstranit repliku**

   ![Databáze Azure pro MySQL – odstranění repliky](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a klepnutím na **tlačítko Odstranit** potvrďte odstranění repliky.  

   ![Databáze Azure pro MySQL – odstranění potvrzení repliky](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru

> [!IMPORTANT]
> Odstraněním hlavního serveru se zastaví replikace na všechny servery replik a odstraní se samotný hlavní server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Chcete-li odstranit hlavní server z webu Azure Portal, použijte následující kroky:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro server MySQL.

2. V části **Přehled**vyberte **odstranit**.

   ![Databáze Azure pro MySQL – hlavní odstranění](./media/howto-read-replica-portal/delete-master-overview.png)

3. Zadejte název hlavního serveru a klepnutím na **tlačítko Odstranit** potvrďte odstranění hlavního serveru.  

   ![Databáze Azure pro MySQL – hlavní odstranění](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte repliku Azure Database for MySQL server, který chcete monitorovat.

2. V části **Monitorování** na postranním panelu vyberte **Metriky**:

3. V rozevíracím seznamu dostupných metrik **vyberte zpoždění replikace během několika sekund.**

   ![Vybrat zpoždění replikace](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Na obrázku níže se vybere časový rozsah 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazení zpoždění replikace pro vybraný časový rozsah. Na obrázku níže se zobrazí posledních 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [čtení replik](concepts-read-replicas.md)