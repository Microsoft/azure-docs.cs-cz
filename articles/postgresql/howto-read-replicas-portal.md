---
title: Správa replik čtení – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak spravovat čtení replik Azure Database pro PostgreSQL – jeden server z webu Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768949"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Vytváření a správa replik čtení v Azure Database for PostgreSQL – jeden server z webu Azure Portal

V tomto článku se dozvíte, jak vytvořit a spravovat repliky čtení v Azure Database for PostgreSQL z webu Azure Portal. Další informace o replikách pro čtení naleznete v [přehledu](concepts-read-replicas.md).


## <a name="prerequisites"></a>Požadavky
[Databáze Azure pro PostgreSQL server](quickstart-create-server-database-portal.md) má být hlavní server.

## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky musí být použity k přípravě hlavního serveru v úrovních pro obecné účely nebo optimalizované pro paměť. Hlavní server je připraven pro replikaci nastavením parametru azure.replication_support. Při změně parametru replikace je nutné restartovat server, aby se změna projevila. Na webu Azure Portal jsou tyto dva kroky zapouzdřeny jedním tlačítkem **Enable Replication Support**.

1. Na webu Azure Portal vyberte existující Azure Database for PostgreSQL server, který se použije jako hlavní server.

2. Na postranním panelu serveru vyberte v části **NASTAVENÍ** **položku Replikace**.

> [!NOTE] 
> Pokud se zobrazí **možnost Zakázat podporu replikace** šedě, nastavení replikace jsou již nastavena na serveru ve výchozím nastavení. Můžete přeskočit následující kroky a přejít k vytvoření repliky pro čtení. 

3. Vyberte **povolit podporu replikace**. 

   ![Povolení podpory replikace](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Potvrďte, že chcete povolit podporu replikace. Tato operace restartuje hlavní server. 

   ![Potvrdit podporu povolit replikaci](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Po dokončení operace obdržíte dvě oznámení na webu Azure Portal. Existuje jedno oznámení pro aktualizaci parametru serveru. Existuje další oznámení pro restartování serveru, které následuje okamžitě.

   ![Oznámení o úspěchu - povolit](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Aktualizujte stránku portálu Azure a aktualizujte panel nástrojů Replikace. Nyní můžete vytvořit repliky pro čtení pro tento server.

   ![Aktualizovaný panel nástrojů](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Povolení podpory replikace je jednorázová operace na hlavní server. Pro vaše pohodlí je k dispozici tlačítko **Zakázat podporu replikace.** Nedoporučujeme zakázat podporu replikace, pokud si nejste jisti, že nikdy nevytvoříte repliku na tomto hlavním serveru. Podporu replikace nelze zakázat, pokud hlavní server obsahuje existující repliky.


## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Chcete-li vytvořit repliku pro čtení, postupujte takto:

1. Vyberte existující Azure Database pro PostgreSQL server, který chcete použít jako hlavní server. 

2. Na postranním panelu serveru vyberte v části **NASTAVENÍ** **položku Replikace**.

3. Vyberte **Přidat repliku**.

   ![Přidání repliky](./media/howto-read-replicas-portal/add-replica.png)

4. Zadejte název repliky pro čtení. 

    ![Pojmenování repliky](./media/howto-read-replicas-portal/name-replica.png)

5. Vyberte umístění repliky. Výchozí umístění je stejné jako umístění hlavního serveru.

    ![Výběr umístění](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

6. Výběrem **možnosti OK** potvrďte vytvoření repliky.

Replika je vytvořena pomocí stejného nastavení výpočetních prostředků a úložiště jako předloha. Po vytvoření repliky lze změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a záložní retenční období. Cenovou úroveň lze také změnit nezávisle, s výjimkou úrovně Basic nebo z ní.

> [!IMPORTANT]
> Před aktualizací nastavení hlavního serveru na novou hodnotu aktualizujte nastavení repliky na stejnou nebo vyšší hodnotu. Tato akce pomáhá replika držet krok s všechny změny provedené v předloze.

Po vytvoření repliky pro čtení ji lze zobrazit z okna **Replikace:**

![Zobrazení nové repliky v okně Replikace](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zastavení replikace
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit.

> [!IMPORTANT]
> Po zastavení replikace na hlavní server a repliku pro čtení ji nelze vrátit zpět. Replika pro čtení se stane samostatným serverem, který podporuje čtení i zápisy. Samostatný server nelze znovu vytvořit v replice.

Pokud chcete zastavit replikaci mezi hlavním serverem a replikou pro čtení z webu Azure Portal, postupujte takto:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro PostgreSQL server.

2. V nabídce server vyberte v části **NASTAVENÍ**položku **Replikace**.

3. Vyberte replika serveru, pro který chcete zastavit replikaci.

   ![Vyberte repliku](./media/howto-read-replicas-portal/select-replica.png)
 
4. Vyberte **možnost Zastavit replikaci**.

   ![Vybrat zastavení replikace](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Chcete-li replikaci zastavit, vyberte **možnost OK.**

   ![Potvrdit zastavení replikace](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Odstranění hlavního serveru
Chcete-li odstranit hlavní server, použijte stejné kroky jako k odstranění samostatné databáze Azure pro server PostgreSQL. 

> [!IMPORTANT]
> Při odstranění hlavního serveru je zastavena replikace všech replik čtení. Čtení repliky stát samostatné servery, které nyní podporují čtení i zápisy.

Pokud chcete odstranit server z webu Azure Portal, postupujte takto:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro PostgreSQL server.

2. Otevřete stránku **Přehled** serveru. Vyberte **Odstranit**.

   ![Na stránce Přehled serveru vyberte odstranění hlavního serveru.](./media/howto-read-replicas-portal/delete-server.png)
 
3. Zadejte název hlavního serveru, který chcete odstranit. Výběrem **možnosti Odstranit** potvrďte odstranění hlavního serveru.

   ![Potvrzení odstranění hlavního serveru](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Odstranění repliky
Repliku pro čtení můžete odstranit podobně jako odstranění hlavního serveru.

- Na webu Azure Portal otevřete stránku **Přehled** pro repliku pro čtení. Vyberte **Odstranit**.

   ![Na stránce Přehled repliky vyberte, chcete-li repliku odstranit.](./media/howto-read-replicas-portal/delete-replica.png)
 
Repliku pro čtení můžete také odstranit z okna **Replikace** takto:

1. Na webu Azure Portal vyberte hlavní databázi Azure pro PostgreSQL server.

2. V nabídce server vyberte v části **NASTAVENÍ**položku **Replikace**.

3. Vyberte repliku pro čtení, kterou chcete odstranit.

   ![Vyberte repliku, kterou chcete odstranit.](./media/howto-read-replicas-portal/select-replica.png)
 
4. Vyberte **odstranit repliku**.

   ![Vybrat odstranit repliku](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Zadejte název repliky, kterou chcete odstranit. Výběrem **možnosti Odstranit** potvrďte odstranění repliky.

   ![Potvrdit odstranění repliky te](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Sledování repliky
Dvě metriky jsou k dispozici pro sledování replik čtení.

### <a name="max-lag-across-replicas-metric"></a>Metrika Maximální prodleva mezi replikami
Metrika **Max Lag Across Replicas** zobrazuje zpoždění v bajtů mezi hlavním serverem a nejvíce zaostávající replikou. 

1.  Na webu Azure Portal vyberte hlavní databázi Azure pro PostgreSQL server.

2.  Vyberte **Metriky**. V okně **Metriky** vyberte **možnost Maximální prodleva mezi replikami**.

    ![Sledování maximálního zpoždění mezi replikami](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Pro **agregaci**vyberte **možnost Max**.


### <a name="replica-lag-metric"></a>Metrika Zpoždění repliky
Metrika **Zpoždění replik** zobrazuje čas od poslední přehrané transakce v replice. Pokud v hlavním vzoru nedochází k žádným transakcím, metrika odráží tuto časovou prodlevu.

1. Na webu Azure Portal vyberte repliku čtení Azure Database for PostgreSQL.

2. Vyberte **Metriky**. V okně **Metriky** vyberte **prodleva repliky**.

   ![Sledování zpoždění repliky](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Pro **agregaci**vyberte **možnost Max**. 
 
## <a name="next-steps"></a>Další kroky
* Další informace o [čtení replik v Azure Database for PostgreSQL](concepts-read-replicas.md).
* Zjistěte, jak [vytvářet a spravovat repliky čtení v rozhraní API Azure a rozhraní REST API](howto-read-replicas-cli.md).