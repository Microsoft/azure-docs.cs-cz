---
title: Vytváření a Správa replik pro čtení v Azure Database for MariaDB
description: Tento článek popisuje, jak nastavit a spravovat repliky pro čtení v Azure Database for MariaDB pomocí portálu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 939097ccca844e505819f5aad405d6322941b526
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309166"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky pro čtení v Azure Database for MariaDB pomocí Azure Portal

V tomto článku se naučíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database for MariaDB pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) , který se bude používat jako hlavní server.

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MariaDB servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že se že hlavní server je v jednom z těchto cenové úrovně.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení se dá vytvořit pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte existující server Azure Database for MariaDB, který chcete použít jako hlavní server. Tato akce otevře **přehled** stránky.

3. V nabídce v části **Nastavení**vyberte **replikace** .

4. Vyberte **Přidat repliku**.

   ![Azure Database for MariaDB – replikace](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název serveru repliky.

    ![Azure Database for MariaDB – název repliky](./media/howto-read-replica-portal/replica-name.png)

6. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako u hlavního serveru.

    ![Azure Database for MariaDB – umístění repliky](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

7. Vyberte **OK** a potvrďte tak vytvoření repliky.

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru na hlavní server. Konfigurace serveru repliky můžete po jejím vytvoření změnit. Doporučuje se, že konfigurace serveru repliky by udržováno na hodnoty roven nebo větší než hlavní Ujistěte se, že je replika schopné udržovat tempo s hlavní.

Po vytvoření serveru repliky ho můžete zobrazit z okna **replikace** .

   ![Repliky seznamu Azure Database for MariaDB](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky

> [!IMPORTANT]
> Zastavuje se replikace na server je nevratná operace. Jakmile se zastaví replikace mezi hlavní a repliky, nejde vrátit. Server repliky pak stane samostatným serverem a nyní podporuje čtení a zápisu. Tento server nelze je převést na repliku znovu.

Pokud chcete zastavit replikaci mezi hlavním serverem a serverem repliky ze Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for MariaDB Server. 

2. V nabídce v části **Nastavení**vyberte **replikace** .

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   ![Azure Database for MariaDB – zastavení replikace vybrat server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **zastavit replikaci**.

   ![Azure Database for MariaDB – zastavení replikace](./media/howto-read-replica-portal/stop-replication.png)

5. Kliknutím na **OK**potvrďte, že chcete replikaci zastavit.

   ![Azure Database for MariaDB – zastavení replikace potvrzení](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

K odstranění serveru repliky pro čtení z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj hlavní Azure Database for MariaDB Server.

2. V nabídce v části **Nastavení**vyberte **replikace** .

3. Vyberte server repliky, který chcete odstranit.

   ![Azure Database for MariaDB – odstranění repliky vybrat server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vyberte **Odstranit repliku** .

   ![Azure Database for MariaDB – odstranění repliky](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a kliknutím na **Odstranit** potvrďte odstranění repliky.  

   ![Azure Database for MariaDB – odstranění repliky potvrzení](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Odstranit hlavního serveru

> [!IMPORTANT]
> Odstraňuje se hlavní server zastaví se replikace na všechny servery repliky a odstraní hlavní samotný server. Servery repliky se samostatnými servery, které nyní podporují čtení a zápisu.

K odstranění hlavního serveru z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj hlavní Azure Database for MariaDB Server.

2. V **přehledu**vyberte **Odstranit**.

   ![Azure Database for MariaDB – odstranění hlavní větve](./media/howto-read-replica-portal/delete-master-overview.png)

3. Zadejte název hlavního serveru a kliknutím na **Odstranit** potvrďte odstranění hlavního serveru.  

   ![Azure Database for MariaDB – odstranění hlavní větve](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. V [Azure Portal](https://portal.azure.com/)vyberte Azure Database for MariaDB server repliky, který chcete monitorovat.

2. V části **monitorování** v postranním panelu vyberte **metriky**:

3. V rozevíracím seznamu dostupných metrik vyberte **prodlevu replikace v sekundách** .

   ![Vybrat prodlevu replikace](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Následující obrázek vybere časový rozsah 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazí prodlevu replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut pro velkou úlohu.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další postup

- Další informace o [čtení replik](concepts-read-replicas.md)