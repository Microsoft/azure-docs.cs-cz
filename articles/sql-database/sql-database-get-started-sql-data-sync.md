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
ms.date: 01/08/2018
ms.openlocfilehash: eb788c07ed9424ae6d5beed5be11c71a11c62265
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304274"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Kurz: Nastavení synchronizace dat SQL mezi Azure SQL Database a SQL Server v místním

V tomto kurzu se dozvíte, jak nastavit synchronizaci dat SQL Azure tak, že vytvoříte skupinu synchronizace, která obsahuje instance Azure SQL Database a SQL Server. Skupina synchronizace je nakonfigurované vlastní a synchronizuje na nastaveného plánu.

Kurz předpokládá, že máte alespoň nějaké zkušenosti s SQL Database a SQL Server.

Přehled synchronizace dat SQL najdete v tématu [synchronizace dat mezi cloudu a místními databázemi pomocí synchronizace dat SQL Azure](sql-database-sync-data.md).

Příklady prostředí PowerShell na tom, jak nakonfigurovat synchronizaci dat SQL najdete v tématu [synchronizace mezi databázemi Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md) nebo [službě Azure SQL Database a místní databáze SQL serveru](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Azure SQL Data synchronizace provádí vložení změn **není** v tuto chvíli podporován Azure SQL Database Managed Instance.

## <a name="create-sync-group"></a>Vytvořit skupinu synchronizace

1. V prohlížeči přejděte na web Azure Portal. Vyhledejte vaše databáze SQL v řídicím panelu nebo, vyberte **databází SQL** ikonu na panelu nástrojů a na **databází SQL** stránky, vyberte databázi, kterou chcete použít jako databázi centra pro synchronizaci dat.

    > [!NOTE]
    > Centrum databáze synchronizace topologie centrální koncový bod, ve které skupině synchronizace je více koncových bodů databáze. Všechny ostatní členské databáze s koncovými body ve skupině synchronizace synchronizovat s databází centra.

1. Na **SQL database** stránka pro vybrané databáze, vyberte **synchronizovat s jinými databázemi**.

    ![Synchronizovat do jiných možností databáze](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. Na **synchronizovat s jinými databázemi** stránce **nová skupina synchronizace**. **Nová skupina synchronizace** otevře se stránka **vytvořit skupinu synchronizace (krok 1)** zvýrazněné.

  ![Krok 1 nastavení](media/sql-database-get-started-sql-data-sync/stepone.png)

  Na **vytvořit skupinu synchronizace dat** stránce, změnit následující nastavení:

  | Nastavení                        | Popis |
  | ------------------------------ | ------------------------------------------------- |
  | **Název skupiny synchronizace** | Zadejte název nové skupiny synchronizace. Tento název se liší od názvu samotná databáze. |
  | **Databáze metadat synchronizace** | Zvolte možnost pro vytvoření databáze (doporučeno) nebo použít stávající databázi.<br/><br/>Pokud se rozhodnete **novou databázi**vyberte **vytvořit novou databázi.** Pak na **SQL Database** stránce, název a nakonfigurujte novou databázi a vyberte **OK**.<br/><br/>Pokud se rozhodnete **použít stávající databázi**, vyberte databázi ze seznamu. |
  | **Automatic Sync** | Vyberte **na** nebo **vypnout**.<br/><br/>Pokud se rozhodnete **na**, zadejte číslo a vyberte **sekund**, **minut**, **hodin**, nebo **dnů** v **Frekvence synchronizace** oddílu. |
  | **Řešení konfliktů** | Vyberte **zvolí se Centrum** nebo **zvolí se člen**.<br/><br/>**Zvolí se Centrum** znamená, že když dojde ke konfliktu, data v databázi centra přepíší konfliktní data v databázi člena.<br/><br/>**Zvolí se člen** znamená, že když dojde ke konfliktu, data v databázi člena přepíší konfliktní data v databázi centra. |

  > [!NOTE]
  > Společnost Microsoft doporučuje vytvořit novou, prázdnou databázi pro použití jako **databáze synchronizace metadat**. Synchronizace dat vytvoří tabulky v této databázi a spouští Časté úlohy. Tato databáze je sdílen jako **databáze synchronizace metadat** pro všechny skupiny synchronizace ve vybrané oblasti a nelze změnit databázi nebo jeho název bez odebrání všech skupin synchronizace a agenti synchronizace v oblasti.

  Vyberte **OK** a počkat na skupině synchronizace k vytvoření a nasazení.

## <a name="add-sync-members"></a>Přidat členy synchronizace

Jakmile se nová skupina synchronizace vytvoří a nasadí, **přidat členy synchronizace (krok 2)** je na obrazovce zvýrazněna **nová skupina synchronizace** stránky.

V **databáze centra** části, zadejte existující přihlašovací údaje pro server SQL Database, na kterém se nachází databáze centra. Nezadávejte *nové* přihlašovacích údajů v této části.

![Krok 2 nastavení](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Chcete-li přidat službu Azure SQL Database

V **členské databáze** části, volitelně přidat službu Azure SQL Database do skupiny synchronizace tak, že vyberete **přidat databázi Azure**. **Konfigurace služby Azure Database** otevře se stránka.

  ![Krok 2: Konfigurace databáze](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na **konfigurace služby Azure Database** stránce, změnit následující nastavení:

  | Nastavení                       | Popis |
  | ----------------------------- | ------------------------------------------------- |
  | **Název člena synchronizace** | Zadejte název nového člena synchronizace. Tento název se liší od samotný název databáze. |
  | **Předplatné** | Vyberte k přidruženému předplatnému Azure pro účely fakturace. |
  | **Azure SQL Server** | Vyberte existující databázi serveru SQL. |
  | **Azure SQL Database** | Vyberte existující databázi SQL. |
  | **Směry synchronizace** | Vyberte **obousměrná synchronizace**, **k centru**, nebo **z centra**. |
  | **Uživatelské jméno** a **heslo** | Zadejte existující přihlašovací údaje pro server SQL Database, na kterém je umístěna databáze člena. Nezadávejte *nové* přihlašovacích údajů v této části. |

  Vyberte **OK** a počkat na nového člena synchronizace k vytvoření a nasazení.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Chcete-li přidat k místní databázi SQL serveru

V **členské databáze** části, volitelně přidat k místním SQL serveru do skupiny synchronizace tak, že vyberete **přidat databázi On-Premises**. **Nakonfigurovat místní** stránce otevře, kde můžete provádět následující akce:

1. Vyberte **zvolit bránu agenta synchronizace**. **Vybrat agenta synchronizace** otevře se stránka.

  ![Vytvoření agenta synchronizace](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na **zvolte agenta synchronizace** stránky, vyberte, jestli chcete existujícího agenta použít nebo vytvořit agenta.

  Pokud se rozhodnete **agentů existující**, vyberte existujícího agenta ze seznamu.

  Pokud se rozhodnete **vytvořit nového agenta**, proveďte následující akce:

    1. Stáhnout agenta synchronizace dat z uvedený odkaz a nainstalujte ho na počítači, kde se nachází SQL Server. Můžete si také stáhnout agenta přímo z [agenta synchronizace dat SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Budete muset otevřít odchozí port 1433 protokolu TCP v bráně firewall umožňuje komunikaci se serverem pro agenta klienta.

    1. Zadejte název pro agenta.

    1. Vyberte **vytvořit a generovat klíč** a zkopírujte klíč agenta do schránky.

    1. Vyberte **OK** zavřete **vybrat agenta synchronizace** stránky.

1. Na počítači systému SQL Server vyhledejte a spuštění agenta synchronizace klienta aplikace.

  ![Data synchronizovat klientskou aplikaci pro agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. V aplikaci agenta synchronizace, vyberte **odešlete klíč agenta**. **Konfigurace databáze synchronizace metadat** zobrazí se dialogové okno.

    1. V **konfigurace databáze synchronizace metadat** dialogové okno, vložte klíč agenta zkopírovali z portálu Azure portal. Také zadejte existující přihlašovací údaje pro server Azure SQL Database, na kterém je umístěna databáze metadat. (Pokud jste vytvořili databázi metadat, tato databáze je na stejném serveru jako centrální databáze.) Vyberte **OK** a počkejte na dokončení konfigurace.

        ![Zadejte přihlašovací údaje pro klíč a server agenta](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Pokud se zobrazí chyba brány firewall, vytvořte pravidlo brány firewall v Azure a umožňuje příchozí provoz z počítače systému SQL Server. Toto pravidlo můžete vytvořit ručně na portálu nebo v SQL Server Management Studio (SSMS). V aplikaci SSMS připojení k databázi centra na Azure tak, že zadáte jeho název, jako < hub_database_name >. database.windows.net.

    1. Vyberte **zaregistrovat** k registraci do databáze SQL serveru s agentem. **Konfigurace systému SQL Server** zobrazí se dialogové okno.

        ![Přidat a nakonfigurovat databázi SQL serveru](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. V **konfigurace systému SQL Server** dialogovém okně Zvolit připojení pomocí ověřování systému SQL Server nebo ověřování Windows. Pokud zvolíte ověřování serveru SQL Server, zadejte existující přihlašovací údaje. Zadejte název SQL serveru a název databáze, kterou chcete synchronizovat a vyberte **Test připojení** na vaše nastavení testu. Potom vyberte **Uložit** a se registrovaných databáze zobrazí v seznamu.

        ![Databáze systému SQL Server je teď zaregistrovaný.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zavřít klienta synchronizovat agenta aplikace.

1. Na portálu na **nakonfigurovat místní** stránce **vyberte databázi**.

1. Na **vybrat databázi** stránku, **název člena synchronizace** pole, zadejte název nového člena synchronizace. Tento název se liší od názvu samotná databáze. Vyberte databázi ze seznamu. V **směry synchronizace** pole, vyberte **obousměrná synchronizace**, **do centra**, nebo **z centra**.

    ![Vyberte v místní databázi](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Vyberte **OK** zavřete **vybrat databázi** stránky. Potom vyberte **OK** zavřete **nakonfigurovat místní** stránce a počkat na nového člena synchronizace k vytvoření a nasazení. Nakonec vyberte **OK** zavřete **vybrat členy synchronizace** stránky.

> [!NOTE]
> Pro připojení k místní agent a synchronizaci dat SQL, přidejte uživatelské jméno k roli *DataSync_Executor*. Synchronizace dat vytvoří této role v instanci systému SQL Server.

## <a name="configure-sync-group"></a>Konfigurovat skupinu synchronizace

Po vytvoření a nasazení novým členům skupiny synchronizace **konfigurovat skupinu synchronizace (krok 3)** je zvýrazněno **nová skupina synchronizace** stránky.

![Krok 3 nastavení](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na **tabulky** stránky, vyberte databázi ze seznamu členů skupiny synchronizace a vyberte **aktualizace schématu**.

1. V seznamu vyberte tabulky, které chcete synchronizovat. Standardně jsou vybrané všechny sloupce, proto zakázat zaškrtávací políčko u sloupce, které nechcete synchronizovat. Ujistěte se, že ponechte vybraný sloupec primárního klíče.

1. Vyberte **Uložit**.

1. Ve výchozím nastavení nejsou databáze synchronizovat, dokud ručně, nebo ručně spustit. Pokud chcete spustit ruční synchronizaci, přejděte k vaší databázi SQL na webu Azure Portal, vyberte **synchronizovat s jinými databázemi**a vyberte skupinu synchronizace. **Synchronizace dat** otevře se stránka. Vyberte **Synchronizovat**.

    ![Ruční synchronizace](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>Nejčastější dotazy

**Jak často můžete synchronizovat Data synchronizovat data?**

Minimální doba mezi synchronizacemi je pět minut.

**Synchronizaci dat SQL plně vytvářet tabulky?**

Pokud tabulky schématu synchronizace nebyly nalezeny v cílové databázi, vytvoří je synchronizace dat SQL s sloupce, které jste vybrali. Však to nemá za následek Plnohodnotná schéma z následujících důvodů:

- Pouze vybraných sloupců jsou vytvořeny v cílové tabulce. Není vybrané sloupce budou ignorovány.
- Jenom vybrané sloupce indexy jsou vytvořeny v cílové tabulce. U sloupců není vybrána tyto indexy jsou ignorovány.
- Indexy na sloupce typu XML nejsou vytvořeny.
- Kontrola omezení nejsou vytvořeny.
- Aktivační události zdrojových tabulkách nejsou vytvořeny.
- Zobrazení a uložených procedur nejsou vytvořeny.

Kvůli těmto omezením doporučujeme následující věci:

- Pro produkční prostředí vytvořte schéma Plnohodnotná sami.
- Při experimentování se službou, pomocí funkce automatického zřizování.

**Proč se zobrazují tabulky, které se nepovedlo vytvořit?**

Synchronizace dat vytváří další tabulky v databázi pro řešení change tracking. Tyto Neodstraňovat nebo synchronizace dat přestane fungovat.

**Jsou moje data témuž cíli po synchronizaci?**

Ne nutně. Skupina synchronizace se hvězdicové tři (A, B a C) trvat, kde synchronizace jsou Hub a centra a B a centra a C. Pokud ke změně databáze A *po* IOT Hub a synchronizace, který změnu není zapsána do databáze B nebo C až do další úlohy synchronizace.

**Jak získat změn schématu do skupiny synchronizace**

Ujistěte se a rozšířit všechny změny schématu ručně.

1. Do centra a pro všechny členy synchronizace ručně replikujte změny schématu.
1. Aktualizujte schéma synchronizace.

Pro přidání nové tabulky a sloupce:

Nové tabulky a sloupce nebudou mít vliv aktuální synchronizace a synchronizace dat je ignoruje, dokud se přidají do schématu synchronizace. Při přidávání nové databázové objekty, postupujte podle sekvence:

1. Přidáte nové tabulky nebo sloupce do centra a pro všechny členy synchronizace.
1. Přidáte nové tabulky nebo sloupce do schématu synchronizace.
1. Proces vkládání hodnot do nových tabulek a sloupců.

Pro změnu datového typu sloupce:

Když změníte datový typ existující sloupec, synchronizaci dat i nadále fungovat, dokud nové hodnoty podle původního datového typu definovaného ve schématu synchronizace. Například, pokud změníte typ ve zdrojové databázi z **int** k **bigint**, synchronizaci dat i nadále fungovat, dokud vložení příliš velká pro hodnoty **int** datového typu. Dokončení změny ručně replikovat změny schématu do centra a pro všechny členy synchronizace a potom aktualizovat schéma synchronizace.

**Jak můžete exportovat a importovat databáze se synchronizací dat?**

Po exportu databáze jako *.bacpac* soubor a naimportujte ho k vytvoření databáze, následujícím postupem používat synchronizaci dat v nové databázi:

1. Vyčištění objektů synchronizace dat a další tabulky na novou databázi s použitím [tento skript](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skript odstraní všechny požadované objekty synchronizace dat z databáze.
1. Znovu vytvořte skupinu synchronizace s novou databází. Pokud už nepotřebujete staré skupinu synchronizace, odstraňte ho.

**Kde najdu informace o klientský agent?**

Nejčastější dotazy týkající se agenta klienta najdete v tématu [nejčastější dotazy týkající se agenta](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Další postup

Blahopřejeme. Vytvořili jste skupinu synchronizace, která obsahuje instance SQL Database a databázi serveru SQL Server.

Další informace o Synchronizaci dat SQL:

- [Agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
- [Osvědčené postupy](sql-database-best-practices-data-sync.md) a [řešení potíží se synchronizací dat SQL Azure](sql-database-troubleshoot-data-sync.md)
- [Monitorování synchronizace dat SQL s využitím Log Analytics](sql-database-sync-monitor-oms.md)
- [Aktualizovat schéma synchronizace, pomocí příkazů jazyka Transact-SQL](sql-database-update-sync-schema.md) nebo [prostředí PowerShell](scripts/sql-database-sync-update-schema.md)

Další informace o službě SQL Database:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
