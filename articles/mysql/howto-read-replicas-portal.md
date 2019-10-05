---
title: Vytvoření & Správa replik čtení (Azure Portal) – Azure Database for MySQL
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for MySQL pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a90e9cccf8b59dabbee8415818c0e819ba1b26c3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972879"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky pro čtení v Azure Database for MySQL pomocí Azure Portal

V tomto článku se naučíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database for MySQL pomocí Azure Portal.

## <a name="prerequisites"></a>Předpoklady

- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) , který se bude používat jako hlavní server.

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MySQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je hlavní server v jedné z těchto cenových úrovní.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení se dá vytvořit pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte existující server Azure Database for MySQL, který chcete použít jako hlavní server. Tato akce otevře stránku s **přehledem** .

3. V nabídce v části **Nastavení**vyberte **replikace** .

4. Vyberte **Přidat repliku**.

   ![Azure Database for MySQL – replikace](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název serveru repliky.

    ![Azure Database for MySQL – název repliky](./media/howto-read-replica-portal/replica-name.png)

6. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako u hlavního serveru.

    ![Azure Database for MySQL – umístění repliky](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

7. Vyberte **OK** a potvrďte tak vytvoření repliky.

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako hlavní. Konfiguraci serveru repliky je možné po vytvoření změnit. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než hlavní, aby bylo zajištěno, že je replika schopná s hlavní hodnotou.

Po vytvoření serveru repliky ho můžete zobrazit z okna **replikace** .

   ![Repliky seznamu Azure Database for MySQL](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky

> [!IMPORTANT]
> Zastavení replikace na serveru je nevratné. Po zastavení replikace mezi hlavním serverem a replikou nelze vrátit zpět. Server repliky se pak stal samostatným serverem a teď podporuje čtení i zápis. Tento server nelze znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi hlavním serverem a serverem repliky ze Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for MySQL server. 

2. V nabídce v části **Nastavení**vyberte **replikace** .

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   ![Azure Database for MySQL – zastavení replikace vybrat server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **zastavit replikaci**.

   ![Azure Database for MySQL – zastavení replikace](./media/howto-read-replica-portal/stop-replication.png)

5. Kliknutím na **OK**potvrďte, že chcete replikaci zastavit.

   ![Azure Database for MySQL – zastavení replikace potvrzení](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

K odstranění serveru repliky pro čtení z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj hlavní Azure Database for MySQL server.

2. V nabídce v části **Nastavení**vyberte **replikace** .

3. Vyberte server repliky, který chcete odstranit.

   ![Azure Database for MySQL – odstranění repliky vybrat server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vyberte **Odstranit repliku** .

   ![Azure Database for MySQL – odstranění repliky](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a kliknutím na **Odstranit** potvrďte odstranění repliky.  

   ![Azure Database for MySQL – odstranění repliky potvrzení](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru

> [!IMPORTANT]
> Odstraněním hlavního serveru se zastaví replikace na všechny servery replik a odstraní se samotný hlavní server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

K odstranění hlavního serveru z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj hlavní Azure Database for MySQL server.

2. V **přehledu**vyberte **Odstranit**.

   ![Azure Database for MySQL – odstranění hlavní větve](./media/howto-read-replica-portal/delete-master-overview.png)

3. Zadejte název hlavního serveru a kliknutím na **Odstranit** potvrďte odstranění hlavního serveru.  

   ![Azure Database for MySQL – odstranění hlavní větve](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. V [Azure Portal](https://portal.azure.com/)vyberte Azure Database for MySQL server repliky, který chcete monitorovat.

2. V části **monitorování** v postranním panelu vyberte **metriky**:

3. V rozevíracím seznamu dostupných metrik vyberte **prodlevu replikace v sekundách** .

   ![Vybrat prodlevu replikace](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Následující obrázek vybere časový rozsah 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazí prodlevu replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut.

   ![Vybrat časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [replikách pro čtení](concepts-read-replicas.md)