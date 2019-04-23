---
title: Spravovat další repliky pro službu Azure Database for PostgreSQL na webu Azure Portal
description: Další informace o správě – Azure Database for PostgreSQL čtení replik na webu Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: bf1fb1c1343173949ecb6348284cb537282b277b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787659"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Vytvořit a spravovat repliky pro čtení z webu Azure portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database for PostgreSQL na webu Azure Portal. Další informace o čtení replik, najdete v článku [přehled](concepts-read-replicas.md).


## <a name="prerequisites"></a>Požadavky
[– Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) na hlavní server.

## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky musí použije k přípravě na úrovni obecné účely nebo k paměťově optimalizovaným hlavní server. Hlavní server připravený pro replikaci tak, že nastavíte parametr azure.replication_support. Při změně parametru replikace, je nutné tato změna se projeví restartovat server. Na webu Azure Portal, jsou tyto dva kroky zapouzdřena objektem na jediné tlačítko **povolit podporu replikace**.

1. Na webu Azure Portal vyberte existující server Azure Database for PostgreSQL pro použití jako hlavní.

2. Na bočním panelu serveru v části **nastavení**vyberte **replikace**.

3. Vyberte **povolit podporu replikace**. 

   ![Povolit podporu replikace](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Potvrďte, že chcete povolit podporu replikace. Tato operace restartuje hlavního serveru. 

   ![Potvrďte podporu povolení replikace](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Zobrazí se dvě oznámení portálu Azure portal a po dokončení operace. Existuje jedno oznámení pro aktualizace parametrů serveru. Existuje další oznámení o restartování serveru, který následuje ihned.

   ![Oznámení o úspěchu – povolit](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Aktualizace stránky Azure portal k aktualizaci nástrojů replikace. Nyní můžete vytvořit další repliky pro tento server.

   ![Aktualizace nástrojů](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Povolení replikace podpory se o jednorázovou operaci jeden hlavní server. A **zakázat podporu replikace** tlačítko je k dispozici pro vaše pohodlí. Nedoporučujeme zakazuje podporu replikace, pokud si nejste jisti, že se nikdy vytvoření repliky na tomto hlavního serveru. Nelze zakázat podporu replikace, pokud hlavní server má existující repliky.


## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Vytvoření repliky pro čtení, postupujte podle těchto kroků:

1. Vyberte existující server Azure Database for PostgreSQL pro použití jako hlavní server. 

2. Na bočním panelu serveru v části **nastavení**vyberte **replikace**.

3. Vyberte **přidání repliky**.

   ![Přidejte repliku](./media/howto-read-replicas-portal/add-replica.png)

4. Zadejte název repliky pro čtení. Vyberte **OK** pro potvrzení vytvoření repliky.

   ![Název repliky](./media/howto-read-replicas-portal/name-replica.png) 

Replika je vytvořen pomocí stejné konfigurace serveru na hlavní server. Po vytvoření repliky několik nastavení lze změnit nezávisle z hlavního serveru: výpočetní generace, virtuální jádra, úložiště a dobu uchování zálohování. Cenovou úroveň můžete změnit také nezávisle na sobě, s výjimkou do nebo z úrovně Basic.

> [!IMPORTANT]
> Před hlavním serverem služby konfigurace se aktualizuje na nové hodnoty, aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika je dokáže dodat se změnami provedenými na hlavní server.

Po vytvoření repliky pro čtení, můžete zobrazit v **replikace** okno:

![Zobrazit novou repliku v okně replikace](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zastavit replikaci
Je-li zastavit replikace mezi serverem a hlavním serverem repliky pro čtení.

> [!IMPORTANT]
> Po zastavení replikace do hlavního serveru a repliky pro čtení nejde vrátit. Čtení replika přestane být samostatný server, který podporuje operace čtení i zápisu. Samostatný server nelze je převést na repliku znovu.

Pokud chcete zastavit replikaci mezi hlavní server a další repliky na webu Azure Portal, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2. V nabídce server v části **nastavení**vyberte **replikace**.

3. Vyberte server repliky, pro kterou chcete zastavit replikaci.

   ![Vyberte repliku](./media/howto-read-replicas-portal/select-replica.png)
 
4. Vyberte **zastavení replikace**.

   ![Vyberte zastavení replikace](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Vyberte **OK** na zastavení replikace.

   ![Potvrdit zastavení replikace](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Odstranit hlavního serveru
Pokud chcete odstranit hlavní server, použijte stejný postup jako samostatné databáze Azure pro PostgreSQL server odstranit. 

> [!IMPORTANT]
> Když odstraníte hlavní server, se zastaví replikace na všechny repliky pro čtení. Čtení replik se stanou samostatné servery, které nyní podporují čtení a zápisu.

Postup odstranění serveru z portálu Azure portal, postupujte podle těchto kroků:

1. Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2. Otevřít **přehled** stránce serveru. Vyberte **Odstranit**.

   ![Na stránce Přehled serveru vyberte odstranění hlavního serveru](./media/howto-read-replicas-portal/delete-server.png)
 
3. Zadejte název serveru hlavní odstranit. Vyberte **odstranit** potvrďte odstranění hlavního serveru.

   ![Potvrdit odstranění hlavního serveru](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Odstranit repliku
Můžete odstranit repliku čtení podobný jak odstranit hlavního serveru.

- Na webu Azure Portal, otevřete **přehled** stránky pro repliky pro čtení. Vyberte **Odstranit**.

   ![Na stránce Přehled repliky vyberte možnost odstranění repliky](./media/howto-read-replicas-portal/delete-replica.png)
 
Můžete také odstranit repliku čtení ze **replikace** okna pomocí následujících kroků:

1. Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2. V nabídce server v části **nastavení**vyberte **replikace**.

3. Vyberte další repliky odstranit.

   ![Vyberte Odstranit repliku](./media/howto-read-replicas-portal/select-replica.png)
 
4. Vyberte **odstranit repliku**.

   ![Výběr možnosti Odstranit repliku](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Zadejte název repliky odstranit. Vyberte **odstranit** potvrďte odstranění repliky.

   ![Potvrdit odstranění repliky te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorování replik
Dvě metriky jsou k dispozici pro sledování replik pro čtení.

### <a name="max-lag-across-replicas-metric"></a>Maximální prodleva mezi repliky metrika
**Maximální prodleva mezi repliky** metrika zobrazuje je zpoždění v bajtech mezi hlavní server a většina obložení repliky. 

1.  Na webu Azure Portal vyberte hlavní server Azure Database for PostgreSQL.

2.  Vyberte **Metriky**. V **metriky** okně **maximální prodleva mezi repliky**.

    ![Monitorování je maximální zpoždění mezi repliky](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Pro vaše **agregace**vyberte **maximální**.


### <a name="replica-lag-metric"></a>Metrika prodleva repliky
**Repliky prodleva** metrika zobrazuje čas, protože poslední přehrály transakce v replice. Pokud neexistují žádné transakce, ke kterým dochází v hlavní, metriku odráží tento časový interval.

1. Na webu Azure Portal vyberte Azure Database for PostgreSQL, přečtěte si repliky.

2. Vyberte **Metriky**. V **metriky** okně **repliky prodleva**.

   ![Monitorování replik prodleva](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Pro vaše **agregace**vyberte **maximální**. 
 
## <a name="next-steps"></a>Další postup
Další informace o [čtení replik ve službě Azure Database for PostgreSQL](concepts-read-replicas.md).