---
title: Správa repliky pro čtení na webu Azure portal pro Azure Database for PostgreSQL
description: Tento článek popisuje správu – Azure Database for PostgreSQL čtení replik na webu Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: bf6e9947c21e5b07b2adc99de585c77444447c04
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902651"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Jak vytvořit a spravovat čtení replik na webu Azure Portal

> [!IMPORTANT]
> Funkce repliky pro čtení je ve verzi Public Preview.


V tomto článku se dozvíte, jak vytvořit a spravovat repliky pro čtení v služba Azure Database for PostgreSQL pomocí webu Azure portal. Další informace o replik pro čtení [, přečtěte si dokumentaci koncepty](concepts-read-replicas.md).

## <a name="prerequisites"></a>Požadavky
- [– Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md) , který bude hlavní server.

## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tento krok hlavní přípravy se týká pouze servery pro obecné účely a optimalizovaný pro paměť.

**Azure.replication_support** parametr musí být nastaven do REPLIKY na hlavní server. Změna tento parametr se vyžaduje restartování serveru se projeví.

1. Na webu Azure Portal vyberte existující server Azure Database for PostgreSQL, který chcete použít jako hlavní.

2. Vyberte **parametry serveru** z nabídky na levé straně.

3. Vyhledejte **azure.replication_support**.

   ![Azure Database for PostgreSQL – azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Nastavte **azure.replication_support** do REPLIKY. **Uložit** změny.

   ![Azure Database for PostgreSQL – REPLIKY a uložit](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Po uložení se dokončí, zobrazí se oznámení.

   ![Azure Database for PostgreSQL – uložení oznámení](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Restartujte server na použití změny po uložení. Zobrazit [v dokumentaci k restartování](howto-restart-server-portal.md) informace o restartování serveru.


## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Repliky pro čtení můžete vytvořit pomocí následujících kroků:
1.  Vyberte existující server Azure Database for PostgreSQL, který chcete použít jako hlavní. 

2.  Vyberte v nabídce v části nastavení replikace.

   Pokud jste nenastavili **azure.replication_support** k REPLICE na obecné účely nebo k paměťově optimalizovaným hlavní a restartuje server, zobrazí se zpráva s pokyny k tomu. Než budete pokračovat s vytvořením Uděláte to tak.

3.  Výběr možnosti Přidat repliky.

   ![Azure Database for PostgreSQL – přidání repliky](./media/howto-read-replicas-portal/add-replica.png)

4.  Zadejte název pro server repliky a klepněte na tlačítko OK pro potvrzení vytvoření repliky.

   ![Azure Database for PostgreSQL – název repliky](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru na hlavní server. Poté, co byla vytvořena replika, cenová úroveň (s výjimkou do a z Basic), výpočetní generaci, virtuální jádra, úložiště a období uchování zálohy lze změnit nezávisle z hlavního serveru.

> [!IMPORTANT]
> Předtím, než se konfigurace serveru hlavní server je aktualizovat na nové hodnoty, tyto repliky konfigurace se musí aktualizovat na stejné nebo vyšší hodnoty. Pokus jinak dojde k chybě. Tím se zajistí, že tyto repliky jsou schopné udržovat tempo se změnami provedenými na hlavní server. 


Po vytvoření serveru repliky, lze zobrazit v okně replikace.

![Azure Database for PostgreSQL – novou repliku](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Zastavit replikaci

> [!IMPORTANT]
> Zastavuje se replikace na server je nevratná operace. Jakmile se zastaví replikace mezi hlavní a repliky, nejde vrátit. Server repliky pak stane samostatným serverem a nyní podporuje čtení a zápisu. Tento server nelze je převést na repliku znovu.

Pokud chcete zastavit replikaci mezi hlavní a repliky na webu Azure Portal, postupujte následovně:
1.  Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2.  Vyberte v nabídce v části nastavení replikace.

3.  Vyberte, kterou chcete zastavit replikaci pro server repliky.

   ![Azure Database for PostgreSQL – výběr repliky](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Vyberte zastavení replikace.

   ![Azure Database for PostgreSQL – výběr zastavení replikace](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Potvrďte, že chcete zastavit replikaci kliknutím na OK.

   ![Azure Database for PostgreSQL – potvrdit zastavení replikace](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>Odstranit předlohy

> [!IMPORTANT]
> Odstraňuje se hlavní server, zastaví se replikace na všechny servery repliky. Servery repliky se samostatnými servery, které nyní podporují čtení a zápisu.
Odstraňuje se hlavní postupuje stejně jako u samostatné databáze Azure pro PostgreSQL server. Postup odstranění serveru z portálu Azure portal, postupujte takto:

1.  Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2.  Přehled vyberte odstranit.

   ![Azure Database for PostgreSQL – odstranění serveru](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Zadejte název hlavního serveru a vyberte Odstranit pro potvrzení odstranění hlavního serveru.

   ![Azure Database for PostgreSQL – potvrzení odstranění](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Odstranit repliku
K odstranění repliky pro čtení, stejně jako u odstranění hlavního serveru výše postupujte podle stejných kroků. Nejprve otevřete stránku přehled repliky a vyberte položku odstranit.

   ![Azure Database for PostgreSQL – odstranění repliky](./media/howto-read-replicas-portal/delete-replica.png)
 
Alternativně můžete odstranit z okna replikace.
1.  Na webu Azure Portal vyberte váš hlavní server Azure Database for PostgreSQL.

2.  Vyberte v nabídce v části nastavení replikace.

3.  Vyberte server repliky, který chcete odstranit. 

   ![Azure Database for PostgreSQL – výběr repliky](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Vyberte možnost odstranění repliky.

   ![Azure Database for PostgreSQL – výběr možnosti Odstranit repliku](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Zadejte název repliky a vyberte Odstranit pro potvrzení odstranění repliky.

   ![Azure Database for PostgreSQL – potvrďte odstranění repliky](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorování replik
### <a name="max-lag-across-replicas"></a>Maximální prodleva mezi repliky
**Maximální prodleva mezi repliky** metrika zobrazuje prodleva mezi hlavní a nejvíce obložení repliky. 

1.  Na webu Azure Portal, vyberte **hlavní** – Azure Database for PostgreSQL server.

2.  Vybrané metrice. V okně metrik vyberte **maximální prodleva mezi repliky**.

    ![Azure Database for PostgreSQL – monitorování maximální prodleva mezi repliky](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Vyberte **maximální** jako vaše agregace. 

### <a name="replica-lag"></a>Prodleva repliky
**Repliky prodleva** metrika zobrazuje čas, protože poslední přehrály transakce v této replice. Pokud neexistují žádné transakce, ke kterým dochází v hlavní, metriku odráží tento časový interval.

1.  Na webu Azure Portal, vyberte **repliky** – Azure Database for PostgreSQL server.

2.  Vybrané metrice. V okně metrik vyberte **repliky prodleva**.

   ![Azure Database for PostgreSQL – monitorování replik prodleva](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Vyberte **maximální** jako vaše agregace. 
 
## <a name="next-steps"></a>Další postup
- Další informace o [čtení replik ve službě Azure Database for PostgreSQL](concepts-read-replicas.md).