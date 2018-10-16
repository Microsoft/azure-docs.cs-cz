---
title: Nastavení synchronizace dat SQL Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nastavit synchronizaci dat SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 98d30d2987d42a2c4893e00c3ba2ea6acd471bef
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318805"
---
# <a name="set-up-sql-data-sync-to-sync-data-between-azure-sql-database-and-sql-server-on-premises"></a>Nastavení synchronizace dat SQL, synchronizaci dat mezi Azure SQL Database a SQL Server v místním

V tomto kurzu se dozvíte, jak nastavit synchronizaci dat SQL Azure tak, že vytvoříte hybridní skupině synchronizace, která obsahuje instance Azure SQL Database a SQL Server. Nová skupina synchronizace plně konfigurována a synchronizuje na nastaveného plánu.

V tomto kurzu se předpokládá, že máte alespoň nějaké zkušenosti s SQL Database a SQL Server.

Přehled Synchronizace dat SQL najdete v tématu [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md).

Kompletní příklady Powershellu ukazující konfiguraci synchronizace dat SQL najdete v následujících článcích:

- [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
- [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Krok 1: vytvořit skupinu synchronizace

### <a name="locate-the-data-sync-settings"></a>Vyhledejte nastavení synchronizace dat

1. V prohlížeči přejděte na web Azure Portal.

2. Na portálu vyhledejte vaše databáze SQL z řídicího panelu nebo na ikonu databáze SQL na panelu nástrojů.

    ![Seznam databází Azure SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3. Na **databází SQL** vyberte stávající databázi SQL, kterou chcete použít pro synchronizaci dat jako databáze centra. Otevře se stránka databáze SQL.

    Centrální databáze je centrální koncový bod topologie synchronizace, ve které skupině synchronizace je více koncových bodů databáze. Všechny ostatní databáze koncové body ve stejné skupiny – to znamená všechny členské databáze - sync synchronizace s databází centra.

4. Na stránce databáze SQL pro vybranou databázi, vyberte **synchronizovat s jinými databázemi**. Otevře se stránka Data Sync.

    ![Synchronizovat do jiných možností databáze](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Vytvořit novou skupinu synchronizace

1. Na stránce pro synchronizaci dat, vyberte **nová skupina synchronizace**. **Nová skupina synchronizace** s krokem 1, otevře se stránka **vytvořit skupinu synchronizace**, zvýrazněné. **Vytvořit skupinu synchronizace dat** otevře se stránka také.

2. Na **vytvořit skupinu synchronizace dat** stránce, proveďte následující akce:

   1. V **název skupiny synchronizace** pole, zadejte název nové skupiny synchronizace.

   2. V **databáze synchronizace metadat** zvolte, jestli chcete vytvořit novou databázi (doporučeno) nebo použít stávající databázi.

        > [!NOTE]
        > Společnost Microsoft doporučuje vytvořit novou, prázdnou databázi pro použití jako databáze synchronizace metadat. Synchronizace dat vytvoří tabulky v této databázi a spouští Časté úlohy. Tato databáze je automaticky sdíleny jako databáze synchronizace metadat pro všechny vaše skupiny synchronizace ve vybrané oblasti. Databáze synchronizace metadat nebo jeho název nelze změnit bez odebrání všech skupin synchronizace a agenti synchronizace v oblasti.

        Pokud jste zvolili **novou databázi**vyberte **vytvořit novou databázi.** **SQL Database** otevře se stránka. Na **SQL Database** stránce, pojmenovat a nakonfigurovat novou databázi. Pak vyberte **OK**.

        Pokud jste zvolili **použít stávající databázi**, vyberte databázi ze seznamu.

   3. V **Automatická synchronizace** nejprve vyberte **na** nebo **vypnout**.

        Pokud jste zvolili **na**v **frekvence synchronizace** , zadejte číslo a vyberte sekund, minut, hodin nebo dnů.

        ![Zadejte četnost synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

   4. V **řešení konfliktů** vyberte "Centra wins" nebo "Člen wins."

        "Centrum wins" znamená, že dojde ke konfliktu, data v databázi centra přepíší konfliktní data v databázi člena. "Člen wins" znamená, že dojde ke konfliktu, data v databázi člena přepíší konfliktní data v databázi centra.

       ![Zadejte způsob řešení konfliktů](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

   5. Vyberte **OK** a počkat na nová skupina synchronizace k vytvoření a nasazení.

## <a name="step-2---add-sync-members"></a>Krok 2: Přidat členy synchronizace

Poté, co se nová skupina synchronizace vytvoří a nasadí, krok 2 **přidat členy synchronizace**, je zvýrazněn **nová skupina synchronizace** stránky.

V **databáze centra** části, zadejte existující přihlašovací údaje pro server SQL Database, na kterém se nachází databáze centra. Nezadávejte *nové* přihlašovacích údajů v této části.

![Centrální databáze byl přidán do skupiny synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Přidat službu Azure SQL Database

V **členské databáze** části, volitelně přidat službu Azure SQL Database do skupiny synchronizace tak, že vyberete **přidat databázi Azure**. **Konfigurace služby Azure Database** otevře se stránka.

Na **konfigurace služby Azure Database** stránce, proveďte následující akce:

1. V **název člena synchronizace** pole, zadejte název nového člena synchronizace. Tento název se liší od názvu samotná databáze.

2. V **předplatné** vyberte k přidruženému předplatnému Azure pro účely fakturace.

3. V **Azure SQL Server** vyberte existující databázi serveru SQL.

4. V **Azure SQL Database** vyberte existující databázi SQL.

5. V **směry synchronizace** pole, vyberte obousměrná synchronizace, do centra nebo z centra.

    ![Přidání nového člena synchronizace databáze SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6. V **uživatelské jméno** a **heslo** pole, zadejte existující přihlašovací údaje pro server SQL Database, na kterém je umístěna databáze člena. Nezadávejte *nové* přihlašovacích údajů v této části.

7. Vyberte **OK** a počkat na nového člena synchronizace k vytvoření a nasazení.

    ![Byl přidán nový člen synchronizace databáze SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Přidat k místní databázi SQL serveru

V **členské databáze** části, volitelně přidat k místním SQL serveru do skupiny synchronizace tak, že vyberete **přidat databázi On-Premises**. **Nakonfigurovat místní** otevře se stránka.

Na **nakonfigurovat místní** stránce, proveďte následující akce:

1. Vyberte **zvolit bránu agenta synchronizace**. **Vybrat agenta synchronizace** otevře se stránka.

    ![Zvolit bránu agenta synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2. Na **zvolit bránu agenta synchronizace** zvolte, jestli chcete existujícího agenta použít nebo vytvořit nového agenta.

    Pokud jste zvolili **agentů existující**, vyberte existujícího agenta ze seznamu.

    Pokud jste zvolili **vytvořit nového agenta**, proveďte následující akce:

   1. Stáhnout klientský software agenta synchronizace z uvedený odkaz a nainstalujte ho na počítači, kde se nachází SQL Server.

        > [!IMPORTANT]
        > Budete muset otevřít odchozí port 1433 protokolu TCP v bráně firewall umožňuje komunikaci se serverem pro agenta klienta.

   2. Zadejte název pro agenta.

   3. Vyberte **vytvořit a generovat klíč**.

   4. Klíč agenta zkopírujte do schránky.

        ![Vytvoření nového agenta synchronizace.](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

   5. Vyberte **OK** zavřete **vybrat agenta synchronizace** stránky.

   6. Na počítači systému SQL Server vyhledejte a spuštění agenta synchronizace klienta aplikace.

        ![Data synchronizovat klientskou aplikaci pro agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

   7. V aplikaci agenta synchronizace, vyberte **odešlete klíč agenta**. **Konfigurace databáze synchronizace metadat** zobrazí se dialogové okno.

   8. V **konfigurace databáze synchronizace metadat** dialogové okno, vložte klíč agenta zkopírovali z portálu Azure portal. Také zadejte existující přihlašovací údaje pro server Azure SQL Database, na kterém je umístěna databáze metadat. (Pokud jste vytvořili novou databázi metadat, tato databáze je na stejném serveru jako centrální databáze.) Vyberte **OK** a počkejte na dokončení konfigurace.

        ![Zadejte přihlašovací údaje pro klíč a server agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Pokud se zobrazí chyba brány firewall v tomto okamžiku budete muset vytvořit pravidlo brány firewall v Azure a umožňuje příchozí provoz z počítače systému SQL Server. Toto pravidlo můžete vytvořit ručně na portálu, ale možná bude snazší ji vytvořit v SQL Server Management Studio (SSMS). V aplikaci SSMS pokuste se připojit k databázi centra na Azure. Zadejte jeho název, jako < hub_database_name >. database.windows.net. Pokud chcete nakonfigurovat pravidlo brány firewall na Azure, postupujte podle kroků v dialogovém okně. Pak se vraťte do agenta synchronizace klienta aplikace.

   9. V aplikaci agenta synchronizace klienta, klikněte na tlačítko **zaregistrovat** k registraci do databáze SQL serveru s agentem. **Konfigurace systému SQL Server** zobrazí se dialogové okno.

        ![Přidat a nakonfigurovat databázi SQL serveru](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

   10. V **konfigurace systému SQL Server** dialogového okna zvolte, jestli se má připojit pomocí ověřování serveru SQL Server nebo ověřování Windows. Pokud zvolíte ověřování serveru SQL Server, zadejte existující přihlašovací údaje. Zadejte název SQL serveru a název databáze, kterou chcete synchronizovat. Vyberte **Test připojení** na vaše nastavení testu. Potom vyberte **Uložit**. Registrované databáze zobrazí v seznamu.

        ![Databáze systému SQL Server je teď zaregistrovaný.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

   11. Teď můžete zavřít aplikaci agenta synchronizace klienta.

   12. Na portálu na **nakonfigurovat místní** stránce **vyberte databázi.** **Vybrat databázi** otevře se stránka.

   13. Na **vybrat databázi** stránku, **název člena synchronizace** pole, zadejte název nového člena synchronizace. Tento název se liší od názvu samotná databáze. Vyberte databázi ze seznamu. V **směry synchronizace** pole, vyberte obousměrná synchronizace, do centra nebo z centra.

        ![Vyberte v místní databázi](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

   14. Vyberte **OK** zavřete **vybrat databázi** stránky. Potom vyberte **OK** zavřete **nakonfigurovat místní** stránce a počkat na nového člena synchronizace k vytvoření a nasazení. Nakonec klikněte na tlačítko **OK** zavřete **vybrat členy synchronizace** stránky.

        ![V místní databázi do skupiny synchronizace](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3. Pro připojení k místní agent a synchronizaci dat SQL, přidejte uživatelské jméno k roli `DataSync_Executor`. Synchronizace dat vytvoří této role v instanci systému SQL Server.

## <a name="step-3---configure-sync-group"></a>Krok 3: Konfigurace skupiny synchronizace

Po vytvoření a nasazení v kroku 3, novým členům skupiny synchronizace **konfigurovat skupinu synchronizace**, je zvýrazněn **nová skupina synchronizace** stránky.

1. Na **tabulky** stránky, vyberte databázi ze seznamu členů skupiny synchronizace a potom vyberte **aktualizace schématu**.

2. Ze seznamu dostupných tabulek vyberte tabulky, které chcete synchronizovat.

    ![Vybrat tabulky, které se budou synchronizovat](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3. Ve výchozím nastavení jsou vybrány všechny sloupce v tabulce. Pokud nechcete, aby se synchronizovat všechny sloupce, zakažte na zaškrtávací políčko u sloupce, které nechcete synchronizovat. Ujistěte se, že ponechte vybraný sloupec primárního klíče.

    ![Vybrat pole, která se budou synchronizovat](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4. Nakonec vyberte **Uložit**.

## <a name="faq-about-setup-and-configuration"></a>Nejčastější dotazy týkající se instalace a konfigurace

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Jak často můžete synchronizovat Data synchronizovat data

Minimální frekvence je každých pět minut.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Synchronizaci dat SQL plně vytvoření a zřízení tabulky

Pokud se schéma tabulky synchronizace již v cílové databázi, vytvoří je synchronizace dat SQL pomocí sloupců, které jste vybrali. Ale toto chování nemá za následek schématu plnou věrností, z následujících důvodů:

- Pouze sloupce, které jste vybrali, se vytvoří v cílové tabulce. Pokud některé sloupce ve zdrojových tabulkách nejsou součástí skupiny synchronizace, tyto sloupce nejsou zřízené v cílových tabulek.
- Indexy jsou vytvořeny pouze pro vybrané sloupce. Pokud zdrojové tabulky indexu má sloupce, které nejsou součástí skupiny synchronizace, tyto indexy nejsou zřízené v cílových tabulek.
- Indexy na sloupce typu XML nejsou zřízené.
- ZKONTROLUJTE, že omezení není zřízený.
- Existující aktivačních událostí zdrojových tabulkách nejsou zřízené.
- Zobrazení a uložených procedur nejsou vytvořeny v cílové databázi.

Kvůli těmto omezením doporučujeme následující věci:

- Pro produkční prostředí zřídíte schématu Plnohodnotná sami.
- Vyzkoušení služby, funkci automatického zřizování synchronizace dat SQL funguje dobře.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Proč se zobrazují tabulky, které můžu vytvořit

Synchronizace dat vytvoří na straně tabulky v databázi pro řešení change tracking. Neodstraňujte ho nebo synchronizace dat přestane fungovat.

### <a name="is-my-data-convergent-after-a-sync"></a>Jsou moje data témuž cíli po synchronizaci

Ne nutně. Ve skupině synchronizace s rozbočovači a tři paprsky (A, B a C) synchronizace jsou Hub a centra a B a centra a C. Pokud ke změně databáze A *po* IOT Hub a synchronizace, který změnu není zapsána do databáze B nebo C databáze až do další úlohy synchronizace.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Jak získat změn schématu do skupiny synchronizace

Je nutné provést a šíří všechny změny schématu ručně.

1. Do centra a pro všechny členy synchronizace ručně replikujte změny schématu.
2. Aktualizujte schéma synchronizace.

**Přidání nové tabulky a sloupce**.

Nové tabulky a sloupce nebudou mít vliv aktuální synchronizace. Synchronizace dat ignoruje nové tabulky a sloupce, dokud je nepřidáte do schématu synchronizace. Když přidáte nové databázové objekty, toto je doporučené pořadí pro postupujte podle:

1. Přidáte nové tabulky nebo sloupce do centra a pro všechny členy synchronizace.
2. Přidáte nové tabulky nebo sloupce do schématu synchronizace.
3. Bylo zahájeno k vložení hodnoty do nové tabulky a sloupce.

**Změna datového typu sloupce**.

Když změníte datový typ existující sloupec, synchronizaci dat i nadále fungovat, dokud nové hodnoty podle původního datového typu definovaného ve schématu synchronizace. Například, pokud změníte typ ve zdrojové databázi z **int** k **bigint**, synchronizaci dat i nadále fungovat, dokud vložit hodnotu, která je příliš velká pro **int** datový typ . Dokončení změny, ručně replikovat změny schématu do centra a pro všechny členy synchronizace a potom aktualizovat schéma synchronizace.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Jak můžete exportovat a importovat databáze se synchronizací dat

Po exportu databáze jako `.bacpac` soubor a naimportujte ho k vytvoření nové databáze, musíte udělat následující dvě věci používat synchronizaci dat v nové databázi:

1. Vyčištění objektů synchronizace dat a na straně tabulek na **novou databázi** pomocí [tento skript](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Tento skript odstraní všechny požadované objekty synchronizace dat z databáze.
2. Znovu vytvořte skupinu synchronizace s novou databází. Pokud už nepotřebujete staré skupinu synchronizace, odstraňte ho.

## <a name="faq-about-the-client-agent"></a>Nejčastější dotazy týkající se agenta klienta

### <a name="why-do-i-need-a-client-agent"></a>Proč potřebuji agenta klienta

Služba synchronizace dat SQL komunikuje s databází systému SQL Server prostřednictvím agenta klienta. Tuto funkci zabezpečení znemožňuje přímé komunikaci s databází za bránou firewall. Při synchronizaci dat SQL service komunikuje s agentem, pomocí provádí šifrované připojení a token jedinečný nebo *klíč agenta*. Databáze systému SQL Server ověření agenta pomocí připojovací řetězec a agent klíč. Tento návrh poskytuje vysokou úroveň zabezpečení pro vaše data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Kolik instancí místní agent uživatelského rozhraní můžete spustit.

Lze spustit pouze jednu instanci uživatelského rozhraní.

### <a name="how-can-i-change-my-service-account"></a>Jak mohu změnit Můj účet

Po instalaci agenta klienta je jediný způsob, jak změnit účet služby odinstalujte ho a nainstalujte nového agenta klienta pomocí nového účtu služby.

### <a name="how-do-i-change-my-agent-key"></a>Jak mohu změnit můj klíč agenta

Klíč agenta lze použít pouze jednou agentem. Se nesmí znovu použít při odebrání pak ji znovu nainstalujte nového agenta ani mohou využívat více agentů. Pokud je potřeba vytvořit nový klíč pro existujícího agenta, musí být jisti, že stejný klíč je zaznamenán s agenta klienta a synchronizaci dat SQL service.

### <a name="how-do-i-retire-a-client-agent"></a>Jak se vyřadit Klientský agent

Okamžitě zrušení platnosti nebo vyřazení agenta, znovu vygenerovat své klíče na portálu, ale neodešlou v Uživatelském rozhraní služby agentů. Obnovuje se klíč zruší platnost předchozí klíč bez ohledu, pokud je odpovídající agent online nebo offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak můžu přesunout klientského agenta do jiného počítače

Pokud chcete spustit místní agent z jiného počítače, než je aktuálně v, proveďte následující akce:

1. Nainstalujte agenta na požadovaný počítač.
2. Připojte se k portálu pro synchronizaci dat SQL a znovu vygenerovat klíč agenta pro nového agenta.
3. Použijte uživatelské rozhraní nového agenta odeslat nový klíč agenta.
4. Počkejte, dokud agent klienta stáhne seznam místních databází, které jste zaregistrovali dříve.
5. Zadejte přihlašovací údaje databáze pro všechny databáze, které zobrazují jako nedostupné. Tyto databáze musí být dosažitelná z nového počítače, na kterém je nainstalovaný agent.

## <a name="next-steps"></a>Další postup

Blahopřejeme. Vytvořili jste skupinu synchronizace, která obsahuje instance SQL Database a databázi serveru SQL Server.

Další informace o Synchronizaci dat SQL:

- [Synchronizace dat mezi několika cloudovými a místními databázemi pomocí Synchronizace dat SQL Azure](sql-database-sync-data.md)
- [Osvědčené postupy pro Synchronizaci dat SQL Azure](sql-database-best-practices-data-sync.md)
- [Monitorování Synchronizace dat SQL Azure s využitím Log Analytics](sql-database-sync-monitor-oms.md)
- [Řešení potíží se Synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)

- Úplné příklady PowerShellu ukazující konfiguraci Synchronizace dat SQL:
  - [Synchronizace mezi několika databázemi SQL Azure pomocí PowerShellu](scripts/sql-database-sync-data-between-sql-databases.md)
  - [Použití PowerShellu k synchronizaci mezi službou Azure SQL Database a místní databází SQL Serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

- [Stažení dokumentace k rozhraní REST API Synchronizace dat SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Další informace o službě SQL Database:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
