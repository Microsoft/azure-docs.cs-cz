---
title: Vytvoření a správa repliky pro čtení ve službě Azure Database for MySQL
description: Tento článek popisuje, jak nastavit a spravovat repliky pro čtení ve službě Azure Database for MySQL pomocí portálu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8e622a11c489618cf66e9cdddf369309e7188645
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548013"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Jak vytvořit a spravovat čtení replik ve službě Azure Database for MySQL pomocí webu Azure portal

V tomto článku se dozvíte, jak vytvořit a spravovat čtení replik v rámci stejné oblasti Azure jako hlavní v služba Azure Database for MySQL pomocí webu Azure portal. Tato funkce je aktuálně ve verzi public preview.

## <a name="prerequisites"></a>Požadavky

- [– Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md) , který se použije jako hlavní server.

> [!IMPORTANT]
> Čtení replik funkce dostupná jenom pro službu Azure Database pro servery MySQL v obecné účely nebo k paměťově optimalizovaným cenové úrovně. Ujistěte se, že se že hlavní server je v jednom z těchto cenové úrovně.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení můžete vytvořit pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte existující server Azure Database for MySQL, kterou chcete použít jako hlavní. Tato akce otevře **přehled** stránky.

3. Vyberte **replikace** v nabídce v části **nastavení**.

4. Vyberte **přidání repliky**.

   ![Azure Database for MySQL – replikace ](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název pro server repliky a klikněte na tlačítko **OK** pro potvrzení vytvoření repliky.

   ![Azure Database for MySQL – vytvoření repliky ](./media/howto-read-replica-portal/create-replica.png)

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru na hlavní server. Konfigurace serveru repliky můžete po jejím vytvoření změnit. Doporučuje se, že konfigurace serveru repliky by udržováno na hodnoty roven nebo větší než hlavní Ujistěte se, že je replika schopné udržovat tempo s hlavní.

Po vytvoření serveru repliky, ho můžete prohlížet **replikace** okno.

   ![Azure Database for MySQL – seznam replik ](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky

> [!IMPORTANT]
> Zastavuje se replikace na server je nevratná operace. Jakmile se zastaví replikace mezi hlavní a repliky, nejde vrátit. Server repliky pak stane samostatným serverem a nyní podporuje čtení a zápisu. Tento server nelze je převést na repliku znovu.

Pokud chcete zastavit replikaci mezi hlavní a serverem repliky na webu Azure Portal, postupujte následovně:

1. Na webu Azure Portal vyberte váš hlavní server Azure Database for MySQL. 

2. Vyberte **replikace** v nabídce v části **nastavení**.

3. Vyberte, kterou chcete zastavit replikaci pro server repliky.

   ![Azure Database for MySQL – výběr serveru zastavení replikace ](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **zastavení replikace**.

   ![Azure Database for MySQL – zastavení replikace ](./media/howto-read-replica-portal/stop-replication.png)

5. Potvrďte chcete zastavit replikaci kliknutím **OK**.

   ![Azure Database for MySQL – potvrdit zastavení replikace ](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Pokud chcete odstranit server repliky pro čtení z portálu Azure portal, postupujte následovně:

1. Na webu Azure Portal vyberte váš hlavní server Azure Database for MySQL.

2. Vyberte **replikace** v nabídce v části **nastavení**.

3. Vyberte server repliky, který chcete odstranit.

   ![Azure Database for MySQL – odstranění repliky vyberte serveru ](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vyberte **odstranit repliku**

   ![Azure Database for MySQL – odstranění repliky ](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a klikněte na tlačítko **odstranit** potvrďte odstranění repliky.  

   ![Azure Database for MySQL – odstranění repliky potvrzení ](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Odstranit hlavního serveru

> [!IMPORTANT]
> Odstraňuje se hlavní server zastaví se replikace na všechny servery repliky a odstraní hlavní samotný server. Servery repliky se samostatnými servery, které nyní podporují čtení a zápisu.

Pokud chcete odstranit hlavním serverem na webu Azure Portal, postupujte následovně:

1. Na webu Azure Portal vyberte váš hlavní server Azure Database for MySQL.

2. Z **přehled**vyberte **odstranit**.

   ![Azure Database for MySQL – odstranění master ](./media/howto-read-replica-portal/delete-master-overview.png)

3. Název hlavního serveru a klikněte na **odstranit** potvrďte odstranění hlavního serveru.  

   ![Azure Database for MySQL – odstranění master ](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. V [webu Azure portal](https://portal.azure.com/), vyberte repliku databáze Azure pro server MySQL, kterou chcete monitorovat.

2. V části **monitorování** části bočního panelu, vyberte **metriky**:

3. Vyberte **zpoždění replikace během několika sekund** z rozevíracího seznamu dostupných metrik. 

   ![Vyberte možnost replikace prodleva ](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Na následujícím obrázku vybere rozsah času 30 minut.

   ![Vyberte časový rozsah ](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazení je zpoždění replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut.

   ![Vyberte časový rozsah ](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další postup

- Další informace o [čtení replik](concepts-read-replicas.md)