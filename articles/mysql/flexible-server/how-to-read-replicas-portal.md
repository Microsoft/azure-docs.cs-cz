---
title: Spravovat repliky pro čtení-Azure Portal-Azure Database for MySQL-flexibilní Server
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for MySQL flexibilním serveru pomocí Azure Portal.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: cd2d3363b9c035987280eb27632c470c012b8bbb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795190"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Jak vytvořit a spravovat repliky pro čtení v Azure Database for MySQL flexibilním serveru pomocí Azure Portal

> [!IMPORTANT]
> Čtení replik v Azure Database for MySQL-flexibilní Server je ve verzi Preview.

V tomto článku se naučíte, jak vytvářet a spravovat repliky pro čtení v Azure Database for MySQL flexibilním serveru pomocí Azure Portal.

> [!Note]
> Replika není podporovaná na serveru s povolenou vysokou dostupností. 

## <a name="prerequisites"></a>Předpoklady

- Server [flexibilního serveru Azure Database for MySQL](quickstart-create-server-portal.md) , který se bude používat jako zdrojový server.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

> [!IMPORTANT]
> Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

Server repliky pro čtení se dá vytvořit pomocí následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Vyberte existující Azure Database for MySQL flexibilní Server, který chcete použít jako zdroj. Tato akce otevře stránku s **přehledem** .

3. V nabídce v části **Nastavení** vyberte **replikace** .

4. Vyberte **Přidat repliku** .

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL – replikace":::

5. Zadejte název serveru repliky.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL – replikace":::

6. Vyberte **OK** a potvrďte tak vytvoření repliky.

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako zdroj. Konfiguraci serveru repliky je možné po vytvoření změnit. Server repliky se vždycky vytvoří ve stejné skupině prostředků, stejné lokalitě a stejném předplatném jako zdrojový server. Pokud chcete vytvořit server repliky pro jinou skupinu prostředků nebo jiné předplatné, můžete [server repliky](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) po vytvoření přesunout. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná udržet se zdrojem.

Po vytvoření serveru repliky ho můžete zobrazit z okna **replikace** .

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky

> [!IMPORTANT]
> Zastavení replikace na serveru je nevratné. Po zastavení replikace mezi zdrojem a replikou je nelze vrátit zpět. Server repliky se pak stal samostatným serverem a teď podporuje čtení i zápis. Tento server nelze znovu vytvořit do repliky.

Pokud chcete zastavit replikaci mezi zdrojem a serverem repliky ze Azure Portal, použijte následující postup:

1. V Azure Portal vyberte zdroj Azure Database for MySQL flexibilní Server. 

2. V nabídce v části **Nastavení** vyberte **replikace** .

3. Vyberte server repliky, pro který chcete zastavit replikaci.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. Vyberte **zastavit replikaci** .

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Kliknutím na **OK** potvrďte, že chcete replikaci zastavit.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

K odstranění serveru repliky pro čtení z Azure Portal použijte následující postup:

1. V Azure Portal vyberte zdroj Azure Database for MySQL flexibilní Server.

2. V nabídce v části **Nastavení** vyberte **replikace** .

3. Vyberte server repliky, který chcete odstranit.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. Vyberte **Odstranit repliku** .

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL – replikace":::

5. Zadejte název repliky a kliknutím na **Odstranit** potvrďte odstranění repliky.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL – replikace":::

## <a name="delete-a-source-server"></a>Odstranění zdrojového serveru

> [!IMPORTANT]
> Odstraněním zdrojového serveru se zastaví replikace na všechny servery replik a odstraní se samotný zdrojový server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Pokud chcete odstranit zdrojový server z Azure Portal, použijte následující postup:

1. V Azure Portal vyberte zdroj Azure Database for MySQL flexibilní Server.

2. V **přehledu** vyberte **Odstranit** .

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Zadejte název zdrojového serveru a kliknutím na **Odstranit** potvrďte odstranění zdrojového serveru.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL – replikace":::

## <a name="monitor-replication"></a>Monitorování replikace

1. V [Azure Portal](https://portal.azure.com/)vyberte repliku Azure Database for MySQL flexibilní Server, který chcete monitorovat.

2. V části **monitorování** v postranním panelu vyberte **metriky** :

3. V rozevíracím seznamu dostupných metrik vyberte **prodlevu replikace v sekundách** .

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Vyberte časový rozsah, který chcete zobrazit. Následující obrázek vybere časový rozsah 30 minut.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Zobrazí prodlevu replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Azure Database for MySQL – replikace":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Další informace o [replikách pro čtení](concepts-read-replicas.md)
