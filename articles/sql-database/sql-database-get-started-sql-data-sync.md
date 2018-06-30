---
title: Nastavit synchronizaci dat SQL Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nastavit synchronizaci dat SQL Azure
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: xiwu
ms.reviewer: douglasl
ms.openlocfilehash: 7cb7ce0ea81cf99c2ee14a6f209864894563d237
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111775"
---
# <a name="set-up-sql-data-sync"></a>Nastavit synchronizaci dat SQL
V tomto kurzu zjistěte, jak nastavit synchronizaci dat SQL Azure tak, že vytvoříte skupinu hybridních synchronizace, která obsahuje instance Azure SQL Database a SQL Server. Do nové skupiny synchronizace plně konfigurována a synchronizuje podle plánu, který nastavíte.

Tento kurz předpokládá, že máte alespoň zkušenosti s SQL Database a SQL Server. 

Přehled synchronizaci dat SQL najdete v tématu [synchronizaci dat mezi několika databází cloudu a místně s synchronizaci dat SQL Azure](sql-database-sync-data.md).

Pro dokončení příklady prostředí PowerShell, které ukazují, jak nakonfigurovat synchronizaci dat SQL, najdete v následujících článcích:
-   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Krok 1 – Vytvoření skupiny synchronizace

### <a name="locate-the-data-sync-settings"></a>Najít nastavení synchronizace dat

1.  V prohlížeči přejděte na portál Azure.

2.  Na portálu vyhledejte vaší databáze SQL z řídicího panelu nebo na ikonu databází SQL na panelu nástrojů.

    ![Seznam databází Azure SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Na **databází SQL** vyberte existující databázi SQL, který chcete použít jako databázi rozbočovače pro synchronizaci dat. Otevře se stránka databáze SQL.

    Databáze centra je centrální koncový bod synchronizace topologii, ve kterém má skupiny synchronizace víc koncových bodů databáze. Všechny ostatní databáze koncové body ve stejné skupiny – to znamená, všechny členské databáze - sync synchronizace s databází rozbočovače.

4.  Na stránce databáze SQL pro vybranou databázi, vyberte **synchronizace do jiné databáze**. Otevře se stránka synchronizaci dat.

    ![Synchronizaci další možnost databáze](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Vytvořit novou skupinu synchronizace

1.  Na stránce synchronizace dat vyberte **nové synchronizace skupiny**. **Nové synchronizace skupiny** otevře se stránka s krokem 1 **vytvořit skupinu synchronizace**, zvýrazněné. **Vytvořit skupinu synchronizace dat** otevře se stránka také.

2.  Na **vytvořit skupinu synchronizace dat** proveďte následující akce:

    1.  V **název skupiny synchronizace** pole, zadejte název nové skupiny synchronizace.

    2.  V **Metadata databáze Sync** zvolte, zda chcete vytvořit novou databázi (doporučeno) nebo použijte existující databázi.

        > [!NOTE]
        > Společnost Microsoft doporučuje, že můžete vytvořit nový, prázdný databázi chcete použít jako databázi synchronizace metadat. Synchronizaci dat vytváří tabulky v této databázi a spustí Časté úlohy. Tato databáze je automaticky sdílen jako databázi Metadata synchronizace pro všechny skupiny synchronizace ve vybrané oblasti. Metadata databáze Sync nebo jeho název nelze změnit bez vyřadíte.

        Pokud jste zvolili **novou databázi**, vyberte **vytvořit novou databázi.** **SQL Database** otevře se stránka. Na **SQL Database** stránky, název a nakonfigurujte novou databázi. Pak vyberte **OK**.

        Pokud jste zvolili **použít existující databázi**, vyberte databázi ze seznamu.

    3.  V **automatické synchronizace** část, vyberte nejdřív **na** nebo **vypnout**.

        Pokud jste zvolili **na**v **frekvence synchronizace** , zadejte číslo a vyberte sekund, minut, hodin nebo dnů.

        ![Zadejte četnost synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  V **řešení konfliktů** vyberte "Rozbočovače wins" nebo "Člen wins."

        "Rozbočovače wins" znamená, že pokud dojde ke konfliktu, data v databázi rozbočovače přepíše konfliktní data v databázi člena. "Člen wins" znamená, že pokud dojde ke konfliktu, data v databázi člena přepíše konfliktní data v databázi rozbočovače. 

        ![Zadejte způsob řešení konfliktů](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Vyberte **OK** a počkejte na novou skupinu synchronizace vytvoření a nasazení.

## <a name="step-2---add-sync-members"></a>Krok 2 – přidání členů synchronizace

Po nové synchronizace skupiny se vytvoří a nasadí, krok 2, **přidat členy synchronizace**, je označený na **nové synchronizace skupiny** stránky.

V **rozbočovače databáze** zadejte existující přihlašovací údaje pro databázi SQL server, na kterém je umístěna databáze rozbočovače. Nezadávejte *nové* přihlašovacích údajů v této části.

![Rozbočovače databáze byl přidán do skupiny synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Přidat k Azure SQL Database

V **databázi člena** část, volitelně přidejte databáze SQL Azure do skupiny synchronizace výběrem **přidat databázi Azure**. **Konfigurace databáze Azure** otevře se stránka.

Na **konfigurace databáze Azure** proveďte následující akce:

1.  V **název člena synchronizace** pole, zadejte název nového člena synchronizace. Tento název se liší od názvu samotná databáze.

2.  V **předplatné** pole, vyberte přidružené předplatné Azure pro účely fakturace.

3.  V **Azure SQL Server** pole, vyberte existující databázový server SQL.

4.  V **Azure SQL Database** pole, vyberte existující databázi SQL.

5.  V **synchronizace pokynů** pole, vyberte obousměrné synchronizace, do rozbočovače nebo z rozbočovače.

    ![Přidání nového člena synchronizace databáze SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  V **uživatelské jméno** a **heslo** pole, zadejte existující přihlašovací údaje pro databázi SQL server, na kterém je umístěna databáze člena. Nezadávejte *nové* přihlašovacích údajů v této části.

7.  Vyberte **OK** a počkejte na vytvoření a nasazení nového člena synchronizace.

    ![Byl přidán nový člen synchronizace databáze SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Přidat místní databázi systému SQL Server

V **databázi člena** část, volitelně přidat místní SQL Server do skupiny synchronizace výběrem **přidat místní databázi**. **Nakonfigurovat místní** otevře se stránka.

Na **nakonfigurovat místní** proveďte následující akce:

1.  Vyberte **zvolte bránu agenta synchronizace**. **Vyberte agenta synchronizace** otevře se stránka.

    ![Zvolte bránu agenta synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Na **zvolte bránu agenta synchronizace** vyberte, zda chcete použít existující agenta nebo vytvořit nový agent.

    Pokud jste zvolili **existující agenty**, vyberte existující agenta ze seznamu.

    Pokud jste zvolili **vytvořit nový agent**, provádět následující akce:

    1.  Stáhněte si klientský software agenta synchronizace z uvedeného odkazu a nainstalujte ji na počítači, kde je umístěný SQL Server.
 
        > [!IMPORTANT]
        > Budete muset otevřít odchozí port TCP 1433 v bráně firewall umožníte komunikaci se serverem pro agenta klienta.


    2.  Zadejte název pro agenta.

    3.  Vyberte **vytvořit a vygenerovat klíč**.

    4.  Klíč agenta zkopírujte do schránky.
        
        ![Vytvoření nového agenta synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Vyberte **OK** zavřete **vyberte agenta synchronizace** stránky.

    6.  Na počítači serveru SQL najít a spuštění aplikace synchronizace agenta klienta.

        ![Data synchronizovat klientskou aplikaci pro agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  V aplikaci agenta synchronizace, vyberte **odeslání klíč agenta**. **Konfigurace databáze synchronizace metadat** otevře se dialogové okno.

    8.  V **konfigurace databáze synchronizace metadat** dialogové okno, vložte klíč agenta zkopírovaných z portálu Azure. Zadejte také existující přihlašovací údaje pro server Azure SQL Database, na kterém je umístěna databáze metadat. (Pokud jste vytvořili novou databázi metadat, na stejném serveru jako databázi centra je tato databáze.) Vyberte **OK** a počkejte na dokončení konfigurace.

        ![Zadejte přihlašovací údaje pro klíč a server agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Pokud dojde k chybě brány firewall v tomto okamžiku, budete muset vytvořit pravidlo brány firewall na platformě Azure, které chcete povolit příchozí přenosy z počítače systému SQL Server. Pravidlo můžete vytvořit ručně na portálu, ale může být snadněji vytvořit v serveru SQL Server Management Studio (SSMS). V aplikaci SSMS pokuste se připojit k databázi rozbočovače na Azure. Zadejte jeho název jako < hub_database_name >. database.windows.net. Pokud chcete konfigurovat pravidlo brány firewall Azure, postupujte podle kroků v dialogovém okně. Pak se vraťte do agenta synchronizace klienta aplikace.

    9.  V aplikaci agenta synchronizace klienta, klikněte na tlačítko **zaregistrovat** k registraci do databáze SQL serveru s agentem. **Konfigurace serveru SQL Server** otevře se dialogové okno.

        ![Přidejte a nakonfigurujte databázi systému SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. V **konfigurace serveru SQL Server** dialogovém okně vyberte, jestli se mají připojit pomocí ověřování systému SQL Server nebo ověřování systému Windows. Pokud jste vybrali ověřování SQL serveru, zadejte existující přihlašovací údaje. Zadejte název serveru SQL Server a název databáze, který chcete synchronizovat. Vyberte **testovací připojení** můžete zkontrolovat nastavení. Potom vyberte **Uložit**. V seznamu se zobrazí registrované databáze.

        ![Databáze systému SQL Server je teď zaregistrovaný.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Teď můžete zavřít aplikaci synchronizace agenta klienta.

    12. Na portálu na **nakonfigurovat místní** vyberte **vyberte databázi.** **Vybrat databázi** otevře se stránka.

    13. Na **vybrat databázi** stránky v **název člena synchronizace** pole, zadejte název nového člena synchronizace. Tento název se liší od názvu samotná databáze. Vyberte databázi ze seznamu. V **synchronizace pokynů** pole, vyberte obousměrné synchronizace, do rozbočovače nebo z rozbočovače.

        ![Vyberte v místní databázi](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Vyberte **OK** zavřete **vybrat databázi** stránky. Potom vyberte **OK** zavřete **nakonfigurovat místní** stránky a počkejte na vytvoření a nasazení nového člena synchronizace. Nakonec klikněte na **OK** zavřete **vyberte synchronizace členů** stránky.

        ![V místní databázi přidán do skupiny synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Pro připojení k synchronizaci dat SQL a místní agent, přidejte uživatelské jméno k roli `DataSync_Executor`. Synchronizaci dat vytvoří tuto roli na instanci systému SQL Server.

## <a name="step-3---configure-sync-group"></a>Krok 3: Konfigurace skupiny synchronizace

Po nové členy skupiny synchronizace se vytváří a nasazují, krok 3 **skupiny synchronizace konfigurace**, je označený na **nové synchronizace skupiny** stránky.

1.  Na **tabulky** , vyberte databázi ze seznamu členů skupiny synchronizace a potom vyberte **aktualizace schématu**.

2.  Ze seznamu dostupných tabulek vyberte tabulky, které chcete synchronizovat.

    ![Vybrat tabulky, které se budou synchronizovat](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Ve výchozím nastavení jsou vybrány všechny sloupce v tabulce. Pokud nechcete, aby k synchronizaci všech sloupců, zakažte políčko pro sloupce, které nechcete synchronizovat. Ujistěte se, že nechte vybraný sloupec primárního klíče.

    ![Vybrat pole, která se budou synchronizovat](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Nakonec vyberte **Uložit**.

## <a name="faq-about-setup-and-configuration"></a>Nejčastější dotazy týkající se instalace a konfigurace

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Jak často můžete synchronizaci dat synchronizovat data? 
Minimální frekvence se každých pět minut.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Synchronizaci dat SQL plně vytvořit a zřídit tabulky?

Pokud schéma tabulky synchronizace nejsou už vytvořené v cílové databázi, vytvoří jejich synchronizaci dat SQL s sloupce, které jste vybrali. Ale toto chování neměla za následek úplnou věrnosti schématu, z následujících důvodů:

-   Pouze sloupce, které jste vybrali jsou vytvořeny v cílové tabulce. Pokud některé sloupce ve zdrojové tabulky není součástí skupiny sync, tyto sloupce nejsou zřízené v cílových tabulkách.

-   Indexy jsou vytvářeny pouze pro vybrané sloupce. Pokud má index tabulky zdrojového sloupce, které nejsou součástí skupiny synchronizace, tyto indexy nejsou zřízené v cílových tabulkách.

-   Indexy na sloupce typu XML nejsou zřízené.

-   ZKONTROLUJTE, že omezení není zřízený.

-   Existující aktivační události na zdrojové tabulky nejsou zřízené.

-   Zobrazení a uložených procedur nejsou vytvořeny v cílové databázi.

Z důvodu tato omezení doporučujeme následující akce:
-   Pro produkční prostředí zřídit schéma úplné věrnosti sami.
-   Pro vyzkoušení služby, funguje dobře funkci Automatické zřizování synchronizaci dat SQL.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Proč se zobrazuje, tabulky, které I nevytvořila?  
Synchronizaci dat vytváří straně tabulky v databázi pro sledování změn. Neodstraňujte ho nebo synchronizaci dat přestane fungovat.

### <a name="is-my-data-convergent-after-a-sync"></a>Jsou má data témuž cíli po synchronizace?

Ne nutně. V skupiny synchronizace s rozbočovačem a tři koncových (A, B a C) synchronizace jsou rozbočovače do A, rozbočovače b a rozbočovače na C. Pokud dojde ke změně do databáze A *po* rozbočovače k synchronizaci, který změnu není zapsána do databáze B nebo databáze C až další úlohy synchronizace.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Jak získat změny schématu do skupiny synchronizace?

Budete muset provést a šířit všechny změny schématu ručně.
1. Replikujte změny schématu ručně k rozbočovači a všem členům synchronizace.
2. Aktualizace schématu synchronizace.

**Přidání nové tabulky a sloupce**. Aktuální synchronizace nebudete mít vliv na nové tabulky a sloupce. Synchronizaci dat ignoruje nové tabulky a sloupce, dokud je nepřidáte do schématu synchronizace. Když přidáte nové databázové objekty, toto je doporučené pořadí podle:
1. Přidáte nové tabulky nebo sloupce do centra a všem členům synchronizace.
2. Přidejte nové tabulky nebo sloupce schématu synchronizace.
3. Spusťte vložení hodnoty do nové tabulky a sloupce.

**Změna datového typu sloupce**. Když změníte existující sloupec datový typ, synchronizaci dat i nadále fungovat, dokud nové hodnoty podle původní datového typu definovaného ve schématu synchronizace. Například, pokud změníte typ ve zdrojové databázi z **int** k **bigint**, bude pořád fungovat, dokud vložte hodnotu, která je příliš velký pro synchronizaci dat **int** datový typ . K dokončení změn, ručně replikovat změny schématu do centra a všem členům synchronizace a potom aktualizovat schéma synchronizace.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Jak můžete exportovat a importovat databáze se synchronizací dat?
Po exportu databáze jako `.bacpac` souboru a importovat soubor k vytvoření nové databáze, budete muset provést následující dva kroky můžete používat synchronizaci dat v databázi nové:
1.  Vyčištění objektů synchronizaci dat a straně tabulek na **novou databázi** pomocí [tento skript](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Tento skript odstraní všechny požadované objekty synchronizaci dat z databáze.
2.  Znovu vytvořte skupiny synchronizace s novou databází. Pokud původní skupiny synchronizace již nepotřebujete, odstraňte jej.

## <a name="faq-about-the-client-agent"></a>Nejčastější dotazy o agenta klienta

### <a name="why-do-i-need-a-client-agent"></a>Proč musím Klientský agent?

Synchronizaci dat SQL služba komunikuje s databází systému SQL Server prostřednictvím agenta klienta. Tato funkce zabezpečení zabraňuje přímé komunikaci s databází za bránou firewall. Při synchronizaci dat SQL služby komunikuje s agentem, ho nemá, takže pomocí šifrované připojení a jedinečný token nebo *klíč agenta*. Databáze systému SQL Server ověření agenta pomocí připojovací řetězec a agent klíče. Tento návrh poskytuje vysokou úroveň zabezpečení pro vaše data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Kolik instancí místní agent uživatelského rozhraní lze spustit?

Lze spustit pouze jednu instanci uživatelského rozhraní.

### <a name="how-can-i-change-my-service-account"></a>Jak můžete změnit Můj účet služby?

Po instalaci agenta klienta, je jediným způsobem, jak změnit účet služby odinstalujte ji a instalaci nového agenta klienta pomocí nového účtu služby.

### <a name="how-do-i-change-my-agent-key"></a>Změna Moje klíč agenta

Klíčem agent může být agentem použit pouze jednou. Nemůže být znovu při odebrání pak znovu nainstalujte nového agenta, ani lze ji použít více agenty. Pokud potřebujete vytvořit nový klíč pro existujícího agenta, musí být jisti, že se stejným klíčem zaznamenává s klientským agentem a se službou synchronizaci dat SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Jak vyřazení z provozu Klientský agent?

Okamžitě zneplatní nebo vyřazení agenta, obnovit svůj klíč na portálu, ale neodešlou v uživatelském rozhraní agenta. Znovu vygenerovat klíč by způsobila neplatnost předchozí klíč ohledu, pokud odpovídající agenta online nebo offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak přesunout agenta klienta do jiného počítače

Pokud chcete spustit místní agent z jiného počítače, než je aktuálně v, proveďte následující akce:

1. Nainstalujte agenta na požadované počítače.

2. Přihlaste se k portálu pro synchronizaci dat SQL a znovu vygenerovat klíč pomocí agenta pro nového agenta.

3. Použijte uživatelské rozhraní nového agenta odeslat nový klíč agenta.

4. Počkejte, než agent klienta stáhne seznam místní databází, které byly dříve zaregistrovány.

5. Zadejte přihlašovací údaje databáze pro všechny databáze, které zobrazují jako nedostupný. Tyto databáze musí být dosažitelný z nového počítače, ve kterém je nainstalován agent.

## <a name="next-steps"></a>Další postup
Blahopřejeme. Vytvořili jste skupinu synchronizace, která obsahuje instance databáze SQL a databáze SQL serveru.

Další informace o Synchronizaci dat SQL:

-   [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md)
-   [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)
-   [Monitorování Synchronizace dat SQL Azure s využitím Log Analytics](sql-database-sync-monitor-oms.md)
-   [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

-   Úplné příklady PowerShellu ukazující konfiguraci Synchronizace dat SQL:
    -   [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Stažení dokumentace k rozhraní REST API Synchronizace dat SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Další informace o službě SQL Database:

-   [Přehled služby SQL Database](sql-database-technical-overview.md)
-   [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
