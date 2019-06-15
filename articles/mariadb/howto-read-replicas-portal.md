---
title: Vytváření a správa repliky pro čtení ve službě Azure Database pro MariaDB
description: Tento článek popisuje, jak nastavit a spravovat repliky pro čtení ve službě Azure Database pro MariaDB pomocí portálu
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: eb228138118512c5c64574212910c5f16885ee94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079025"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Jak vytvořit a spravovat čtení replik ve službě Azure Database pro MariaDB pomocí webu Azure portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database pro MariaDB service pomocí webu Azure portal.

> [!IMPORTANT]
> Stejné oblasti čtení replik je aktuálně ve verzi public preview.

## <a name="prerequisites"></a>Požadavky

- [MariaDB serveru Azure Database for](quickstart-create-mariadb-server-database-using-azure-portal.md) , který se použije jako hlavní server.

> [!IMPORTANT]
> Funkce repliky pro čtení je pouze k dispozici pro službu Azure Database pro MariaDB servery v obecné účely nebo k paměťově optimalizovaným cenové úrovně. Ujistěte se, že se že hlavní server je v jednom z těchto cenové úrovně.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení můžete vytvořit pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte existující Azure Database pro MariaDB server, který chcete použít jako hlavní. Tato akce otevře **přehled** stránky.

3. Vyberte **replikace** v nabídce v části **nastavení**.

4. Vyberte **přidání repliky**.

   ![Azure Database pro MariaDB – replikace](./media/howto-read-replica-portal/add-replica.png)

5. Zadejte název pro server repliky.

    ![Azure Database pro MariaDB – název repliky](./media/howto-read-replica-portal/replica-name.png)

6. Vyberte **OK** pro potvrzení vytvoření repliky.

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru na hlavní server. Konfigurace serveru repliky můžete po jejím vytvoření změnit. Doporučuje se, že konfigurace serveru repliky by udržováno na hodnoty roven nebo větší než hlavní Ujistěte se, že je replika schopné udržovat tempo s hlavní.

Po vytvoření serveru repliky, ho můžete prohlížet **replikace** okno.

   ![Azure Database pro MariaDB – seznam replik](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky

> [!IMPORTANT]
> Zastavuje se replikace na server je nevratná operace. Jakmile se zastaví replikace mezi hlavní a repliky, nejde vrátit. Server repliky pak stane samostatným serverem a nyní podporuje čtení a zápisu. Tento server nelze je převést na repliku znovu.

Pokud chcete zastavit replikaci mezi hlavní a serverem repliky na webu Azure Portal, postupujte následovně:

1. Na webu Azure Portal vyberte hlavní Azure Database pro MariaDB server. 

2. Vyberte **replikace** v nabídce v části **nastavení**.

3. Vyberte, kterou chcete zastavit replikaci pro server repliky.

   ![Azure Database pro MariaDB – výběr serveru zastavení replikace](./media/howto-read-replica-portal/stop-replication-select.png)

4. Vyberte **zastavení replikace**.

   ![Azure Database pro MariaDB - zastavení replikace](./media/howto-read-replica-portal/stop-replication.png)

5. Potvrďte chcete zastavit replikaci kliknutím **OK**.

   ![Azure Database pro MariaDB - potvrdit zastavení replikace](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Pokud chcete odstranit server repliky pro čtení z portálu Azure portal, postupujte následovně:

1. Na webu Azure Portal vyberte hlavní Azure Database pro MariaDB server.

2. Vyberte **replikace** v nabídce v části **nastavení**.

3. Vyberte server repliky, který chcete odstranit.

   ![Azure Database pro MariaDB - serveru vyberte odstranění repliky](./media/howto-read-replica-portal/delete-replica-select.png)

4. Vyberte **odstranit repliku**

   ![Azure Database pro MariaDB – odstranit repliku](./media/howto-read-replica-portal/delete-replica.png)

5. Zadejte název repliky a klikněte na tlačítko **odstranit** potvrďte odstranění repliky.  

   ![Azure Database pro MariaDB – potvrďte odstranění repliky](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Odstranit hlavního serveru

> [!IMPORTANT]
> Odstraňuje se hlavní server zastaví se replikace na všechny servery repliky a odstraní hlavní samotný server. Servery repliky se samostatnými servery, které nyní podporují čtení a zápisu.

Pokud chcete odstranit hlavním serverem na webu Azure Portal, postupujte následovně:

1. Na webu Azure Portal vyberte hlavní Azure Database pro MariaDB server.

2. Z **přehled**vyberte **odstranit**.

   ![Azure Database pro MariaDB - Delete master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Název hlavního serveru a klikněte na **odstranit** potvrďte odstranění hlavního serveru.  

   ![Azure Database pro MariaDB - Delete master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorování replikace

1. V [webu Azure portal](https://portal.azure.com/), vyberte repliku – Azure Database pro MariaDB server, kterou chcete monitorovat.

2. V části **monitorování** části bočního panelu, vyberte **metriky**:

3. Vyberte **zpoždění replikace během několika sekund** z rozevíracího seznamu dostupných metrik.

   ![Vyberte možnost replikace prodleva](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Vyberte časový rozsah, který chcete zobrazit. Na následujícím obrázku vybere rozsah času 30 minut.

   ![Vyberte časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Zobrazení je zpoždění replikace pro vybraný časový rozsah. Následující obrázek zobrazuje posledních 30 minut pro velké zatížení.

   ![Vyberte časový rozsah](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Další postup

- Další informace o [čtení replik](concepts-read-replicas.md)