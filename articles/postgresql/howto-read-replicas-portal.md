---
title: Spravovat další repliky pro službu Azure Database for PostgreSQL na webu Azure Portal
description: Další informace o správě – Azure Database for PostgreSQL čtení replik na webu Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b34b103d3b710b90fd7b396f2c8d0e7adc27aaca
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454663"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Vytvořit a spravovat repliky pro čtení z webu Azure portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení ve službě Azure Database for PostgreSQL na webu Azure Portal. Další informace o čtení replik, najdete v článku [přehled](concepts-read-replicas.md).

> [!IMPORTANT]
> Funkce repliky pro čtení je ve verzi public preview.

## <a name="prerequisites"></a>Požadavky
[– Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) na hlavní server.

## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky musí použije k přípravě na úrovni obecné účely nebo k paměťově optimalizovaným hlavní server.

`azure.replication_support` Parametr musí být nastaven na **REPLIKY** na hlavní server. Pokud tento parametr změníte, je nutné tato změna se projeví restartovat server.

1. Na webu Azure Portal vyberte existující server Azure Database for PostgreSQL pro použití jako hlavní.

2. V nabídce vlevo vyberte **parametry serveru**.

3. Hledat `azure.replication_support` parametru.

   ![Vyhledejte parametr azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Nastavte `azure.replication_support` pro parametr **REPLIKY**. Vyberte **Uložit** chcete zachovat změny.

   ![Nastavte parametr na REPLIKU a uložte provedené změny](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Po uložení změn se zobrazí oznámení:

   ![Uložit oznámení](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Restartujte server změny. Zjistěte, jak restartování serveru, najdete v článku [restartování serveru Azure Database for PostgreSQL](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Vytvoření repliky pro čtení, postupujte podle těchto kroků:

1.  Vyberte existující server Azure Database for PostgreSQL pro použití jako hlavní server. 

2.  V nabídce server v části **nastavení**vyberte **replikace**.

   Pokud jste nenastavili `azure.replication_support` parametr **REPLIKY** na obecné účely nebo k paměťově optimalizovaným hlavního serveru a restartujte server, dostanete oznámení. Před vytvořením repliky proveďte tyto kroky.

3.  Vyberte **přidání repliky**.

   ![Přidejte repliku](./media/howto-read-replicas-portal/add-replica.png)

4.  Zadejte název repliky pro čtení. Vyberte **OK** pro potvrzení vytvoření repliky.

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

1.  Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2.  V nabídce server v části **nastavení**vyberte **replikace**.

3.  Vyberte server repliky, pro kterou chcete zastavit replikaci.

   ![Vyberte repliku](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Vyberte **zastavení replikace**.

   ![Vyberte zastavení replikace](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Vyberte **OK** na zastavení replikace.

   ![Potvrdit zastavení replikace](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Odstranit hlavního serveru
Pokud chcete odstranit hlavní server, použijte stejný postup jako samostatné databáze Azure pro PostgreSQL server odstranit. 

> [!IMPORTANT]
> Když odstraníte hlavní server, se zastaví replikace na všechny repliky pro čtení. Čtení replik se stanou samostatné servery, které nyní podporují čtení a zápisu.

Postup odstranění serveru z portálu Azure portal, postupujte podle těchto kroků:

1.  Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2.  Otevřít **přehled** stránce serveru. Vyberte **Odstranit**.

   ![Na stránce Přehled serveru vyberte odstranění hlavního serveru](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Zadejte název serveru hlavní odstranit. Vyberte **odstranit** potvrďte odstranění hlavního serveru.

   ![Potvrdit odstranění hlavního serveru](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Odstranit repliku
Můžete odstranit repliku čtení podobný jak odstranit hlavního serveru.

- Na webu Azure Portal, otevřete **přehled** stránky pro repliky pro čtení. Vyberte **Odstranit**.

   ![Na stránce Přehled repliky vyberte možnost odstranění repliky](./media/howto-read-replicas-portal/delete-replica.png)
 
Můžete také odstranit repliku čtení ze **replikace** okna pomocí následujících kroků:

1.  Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2.  V nabídce server v části **nastavení**vyberte **replikace**.

3.  Vyberte další repliky odstranit.

   ![Vyberte Odstranit repliku](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Vyberte **odstranit repliku**.

   ![Výběr možnosti Odstranit repliku](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Zadejte název repliky odstranit. Vyberte **odstranit** potvrďte odstranění repliky.

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

1.  Na webu Azure Portal vyberte Azure Database for PostgreSQL, přečtěte si repliky.

2.  Vyberte **Metriky**. V **metriky** okně **repliky prodleva**.

   ![Monitorování replik prodleva](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Pro vaše **agregace**vyberte **maximální**. 
 
## <a name="next-steps"></a>Další postup
Další informace o [čtení replik ve službě Azure Database for PostgreSQL](concepts-read-replicas.md).