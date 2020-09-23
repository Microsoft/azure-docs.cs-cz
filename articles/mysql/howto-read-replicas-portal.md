---
title: Spravovat repliky čtení-Azure Portal-Azure Database for MySQL
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for MySQL pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: ad8fd20d744f7aaa113b4c46f8ca0f05a6cc6951
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902841"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky pro čtení v Azure Database for MySQL pomocí Azure Portal

V tomto článku se naučíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database for MySQL pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) , který se bude používat jako hlavní server.

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MySQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je hlavní server v jedné z těchto cenových úrovní.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

> [!IMPORTANT]
> Když vytvoříte repliku pro hlavní server, který nemá žádné existující repliky, hlavní počítač se nejprve restartuje a připraví se pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

Server repliky pro čtení se dá vytvořit pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte existující server Azure Database for MySQL, který chcete použít jako hlavní server. Tato akce otevře stránku s **přehledem** .

3. V nabídce v části **Nastavení**vyberte **replikace** .

4. Vyberte **Přidat repliku**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL – replikace":::

5. Zadejte název serveru repliky.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL – název repliky":::

6. Vyberte umístění serveru repliky. Výchozí umístění je stejné jako u hlavního serveru.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL – umístění repliky":::

   > [!NOTE]
   > Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

7. Vyberte **OK** a potvrďte tak vytvoření repliky.

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako hlavní. Konfiguraci serveru repliky je možné po vytvoření změnit. Server repliky je vždy vytvořen ve stejné skupině prostředků a v rámci stejného předplatného, jako má hlavní server. Pokud chcete vytvořit server repliky pro jinou skupinu prostředků nebo jiné předplatné, můžete [server repliky](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) po vytvoření přesunout. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než hlavní, aby bylo zajištěno, že je replika schopná s hlavní hodnotou.

Po vytvoření serveru repliky ho můžete zobrazit z okna **replikace** .

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Repliky seznamu Azure Database for MySQL":::

## <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky

> [!IMPORTANT]
> Zastavení replikace na serveru je nevratné. Po zastavení replikace mezi hlavním serverem a replikou nelze vrátit zpět. Server repliky se pak stal samostatným serverem a teď podporuje čtení i zápis. Tento server nelze znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi hlavním serverem a serverem repliky ze Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal vyberte svůj hlavní Azure Database for MySQL server. 

2. V nabídce v části **Nastavení**vyberte **replikace** .

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL – zastavení replikace vybrat server":::

4. Vyberte **zastavit replikaci**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL – zastavení replikace":::

5. Kliknutím na **OK**potvrďte, že chcete replikaci zastavit.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL – zastavení replikace potvrzení":::

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

K odstranění serveru repliky pro čtení z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj hlavní Azure Database for MySQL server.

2. V nabídce v části **Nastavení**vyberte **replikace** .

3. Vyberte server repliky, který chcete odstranit.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL – odstranění repliky vybrat server":::

4. Vyberte **Odstranit repliku** .

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL – odstranění repliky":::

5. Zadejte název repliky a kliknutím na **Odstranit** potvrďte odstranění repliky.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL – odstranění repliky potvrzení":::

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru

> [!IMPORTANT]
> Odstraněním hlavního serveru se zastaví replikace na všechny servery replik a odstraní se samotný hlavní server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

K odstranění hlavního serveru z Azure Portal použijte následující postup:

1. V Azure Portal vyberte svůj hlavní Azure Database for MySQL server.

2. V **přehledu**vyberte **Odstranit**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL – odstranění hlavní větve":::

3. Zadejte název hlavního serveru a kliknutím na **Odstranit** potvrďte odstranění hlavního serveru.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL – odstranění hlavní větve":::

## <a name="monitor-replication"></a>Monitorování replikace

1. V [Azure Portal](https://portal.azure.com/)vyberte Azure Database for MySQL server repliky, který chcete monitorovat.

2. V části **monitorování** v postranním panelu vyberte **metriky**:

3. V rozevíracím seznamu dostupných metrik vyberte **prodlevu replikace v sekundách** .

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Vybrat prodlevu replikace":::

4. Vyberte časový rozsah, který chcete zobrazit. Následující obrázek vybere časový rozsah 30 minut.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Vybrat časový rozsah":::

5. Zobrazí prodlevu replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Vybrat časový rozsah":::

## <a name="next-steps"></a>Další kroky

- Další informace o [replikách pro čtení](concepts-read-replicas.md)
