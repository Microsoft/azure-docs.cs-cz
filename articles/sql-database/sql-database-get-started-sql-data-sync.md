---
title: Nastavení synchronizace dat
description: Tento kurz ukazuje, jak nastavit Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268961"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Kurz: Nastavení synchronizace dat SQL mezi databází Azure SQL database a SQL Server místně

V tomto kurzu se dozvíte, jak nastavit Azure SQL Data Sync vytvořením skupiny synchronizace, která obsahuje azure SQL database a SQL Server instance. Skupina synchronizace je nakonfigurována na vlastní úrovni a synchronizuje se podle nastaveného plánu.

Kurz předpokládá, že máte alespoň některé předchozí zkušenosti s SQL Database a SQL Server.

Přehled synchronizace dat SQL najdete v [tématu Synchronizace dat v cloudu a místních databázích pomocí Azure SQL Data Sync](sql-database-sync-data.md).

Příklady PowerShellu o konfiguraci synchronizace dat SQL najdete v tématu [Jak synchronizovat mezi databázemi Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md) nebo databází Azure SQL a místní databází SQL [Serveru.](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Azure SQL Data Sync v tuto chvíli **nepodporuje** spravovanou instanci Azure SQL Database.

## <a name="create-sync-group"></a>Vytvořit skupinu synchronizace

1. Přejděte na [portál Azure](https://portal.azure.com) a najděte svou databázi SQL. Vyhledejte a vyberte **databáze SQL**.

    ![Hledání databází SQL, portál Microsoft Azure](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Vyberte databázi, kterou chcete použít jako centrální databázi pro synchronizaci dat.

    ![Výběr ze seznamu databází SQL, portál Microsoft Azure](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > Databáze centra je centrální koncový bod topologie synchronizace, ve kterém má skupina synchronizace více koncových bodů databáze. Všechny ostatní členské databáze s koncovými body ve skupině synchronizace, synchronizace s databází rozbočovače.

1. V nabídce **databáze SQL** pro vybranou databázi vyberte **Synchronizovat s jinými databázemi**.

    ![Synchronizace s jinými databázemi, databází SQL, portálem Microsoft Azure](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. Na stránce **Synchronizovat s jinými databázemi** vyberte **Nová skupina synchronizace**. Otevře se stránka **Nová skupina synchronizace** se zvýrazněnou **skupinou Vytvořit synchronizaci (krok 1).**

   ![Nastavení kroku 1](media/sql-database-get-started-sql-data-sync/stepone.png)

   Na stránce **Vytvořit skupinu synchronizace dat** změňte následující nastavení:

   | Nastavení                        | Popis |
   | ------------------------------ | ------------------------------------------------- |
   | **Název skupiny synchronizace** | Zadejte název nové skupiny synchronizace. Tento název se liší od názvu samotné databáze. |
   | **Synchronizovat databázi metadat** | Zvolte vytvoření databáze (doporučeno) nebo použití existující databáze.<br/><br/>Pokud zvolíte **Nová databáze**, vyberte **Vytvořit novou databázi.** Potom na stránce **databáze SQL** pojmenujte a nakonfigurujte novou databázi a vyberte **OK**.<br/><br/>Pokud zvolíte **Použít existující databázi**, vyberte databázi ze seznamu. |
   | **Automatická synchronizace** | Vyberte **Možnost Zapnuto** nebo **Vypnuto**.<br/><br/>Pokud zvolíte **Zapnuto**, zadejte číslo a v části **Frekvence synchronizace** vyberte **Položky Sekundy**, **Minuty**, **Hodiny**nebo **Dny.** |
   | **Konfliktů** | Vyberte **výhra v centru** nebo členské **vítězství**.<br/><br/>**Výhra rozbočovače** znamená, že když dojde ke konfliktům, data v databázi rozbočovače přepíší konfliktní data v databázi členů.<br/><br/>**Výhra člena** znamená, že když dojde ke konfliktům, data v databázi členů přepíší konfliktní data v databázi rozbočovače. |

   > [!NOTE]
   > Společnost Microsoft doporučuje vytvořit novou prázdnou databázi pro použití jako **databáze metadat synchronizace**. Synchronizace dat vytvoří tabulky v této databázi a spustí časté úlohy. Tato databáze je **sdílena** jako databáze metadat synchronizace pro všechny skupiny synchronizace ve vybrané oblasti a nelze změnit databázi nebo její název bez odebrání všech skupin synchronizace a agentů synchronizace v oblasti.

   Vyberte **OK** a počkejte na vytvoření a nasazení skupiny synchronizace.

## <a name="add-sync-members"></a>Přidání členů synchronizace

Po vytvoření a nasazení nové skupiny synchronizace se na stránce **Nová skupina synchronizace** zvýrazní **přidání členů synchronizace (krok 2).**

V části **Hub Database** zadejte existující pověření pro databázový server SQL, na kterém je umístěna databáze rozbočovače. Do této části nezadávejte *nová* pověření.

![Nastavení kroku 2](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Přidání databáze Azure SQL

V části **Databáze členů** volitelně přidejte do skupiny synchronizace databázi Azure SQL výběrem **možnosti Přidat databázi Azure SQL**. Otevře se stránka **Konfigurovat databázi Azure SQL.**

  ![Krok 2 – konfigurace databáze](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na stránce **Configure Azure SQL Database** změňte následující nastavení:

  | Nastavení                       | Popis |
  | ----------------------------- | ------------------------------------------------- |
  | **Synchronizovat název člena** | Zadejte název nového člena synchronizace. Tento název se liší od samotného názvu databáze. |
  | **Předplatné** | Vyberte přidružené předplatné Azure pro účely fakturace. |
  | **Azure SQL Server** | Vyberte existující databázový server SQL. |
  | **Azure SQL Database** | Vyberte existující databázi SQL. |
  | **Pokyny synchronizace** | Vyberte **Obousměrná synchronizace**, **Do centra**nebo **Z centra**. |
  | **Uživatelské jméno** a **heslo** | Zadejte existující pověření pro databázový server SQL, na kterém je umístěna databáze členů. Do této části nezadávejte *nová* pověření. |

  Vyberte **OK** a počkejte na vytvoření a nasazení nového člena synchronizace.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Přidání místní databáze serveru SQL Server

V části **Databáze členů** volitelně přidejte místní SQL Server do skupiny synchronizace výběrem **možnosti Přidat místní databázi**. Otevře se stránka **Konfigurovat místní,** kde můžete dělat následující akce:

1. Vyberte **možnost Zvolit bránu agenta synchronizace**. Otevře se stránka **Vybrat agenta synchronizace.**

   ![Vytvoření synchronizačního agenta](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na stránce **Zvolit agenta synchronizace** zvolte, zda chcete použít existujícího agenta nebo vytvořit agenta.

   Pokud zvolíte **Existující agenty**, vyberte existujícího agenta ze seznamu.

   Pokud zvolíte **Vytvořit nového agenta**, udělejte následující věci:

   1. Stáhněte agenta synchronizace dat z poskytnutého odkazu a nainstalujte jej do počítače, ve kterém je server SQL Server umístěn. Agenta můžete také stáhnout přímo z [agenta synchronizace dat SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Chcete-li agentovi klienta komunikovat se serverem, musíte otevřít výstupní port TCP 1433 v bráně firewall.

   1. Zadejte název agenta.

   1. Vyberte **Vytvořit a generovat klíč** a zkopírujte klíč agenta do schránky.

   1. Výběrem **možnosti OK** zavřete stránku **Vybrat agenta synchronizace.**

1. V počítači se serverem SQL Server vyhledejte a spusťte aplikaci Client Sync Agent.

   ![Aplikace klientského agenta synchronizace dat](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. V aplikaci agent synchronizace vyberte **Odeslat klíč agenta**. Otevře se dialogové okno **Synchronizovat konfiguraci databáze metadat.**

    1. V dialogovém okně **Synchronizovat konfiguraci databáze metadat** vložte klíč agenta zkopírovaný z portálu Azure. Zadejte také existující pověření pro server Azure SQL Database, na kterém je umístěna databáze metadat. (Pokud jste vytvořili databázi metadat, tato databáze je na stejném serveru jako databáze rozbočovače.) Vyberte **OK** a počkejte na dokončení konfigurace.

        ![Zadání pověření klíče agenta a serveru](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Pokud se zobrazí chyba brány firewall, vytvořte pravidlo brány firewall v Azure, aby byl povolen příchozí provoz z počítače SQL Server. Pravidlo můžete vytvořit ručně na portálu nebo v aplikaci SQL Server Management Studio (SSMS). V SSMS se připojte k databázi rozbočovače v Azure zadáním jejího názvu jako <hub_database_name>.database.windows.net.

    1. Chcete-li zaregistrovat databázi serveru SQL Server u agenta, **vyberte možnost Registrovat.** Otevře se dialogové okno **Konfigurace serveru SQL Server.**

        ![Přidání a konfigurace databáze serveru SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. V dialogovém okně **Konfigurace serveru SQL Server** zvolte připojení pomocí ověřování serveru SQL Server nebo ověřování systému Windows. Pokud zvolíte ověřování serveru SQL Server, zadejte existující pověření. Zadejte název serveru SQL server a název databáze, kterou chcete synchronizovat, a vyberte **testovat připojení** k testování nastavení. Pak vyberte **Uložit** a registrovaná databáze se zobrazí v seznamu.

        ![Databáze serveru SQL Server je nyní zaregistrována.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Zavřete aplikaci Agent synchronizace klienta.

1. Na portálu vyberte na stránce **Konfigurovat místní** **položku Vyberte databázi**.

1. On the **Select Database** page, in the **Sync Member Name** field, provide a name for the new sync member. Tento název se liší od názvu samotné databáze. Vyberte databázi ze seznamu. V poli **Synchronizovat trasy** vyberte **Obousměrná synchronizace**, **Do centra**nebo Z **centra**.

    ![Výběr místní databáze](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Chcete-li zavřít stránku **Vybrat databázi,** vyberte **ok.** Pak vyberte **OK,** **chcete-li** zavřít stránku Konfigurovat místní a počkejte, až bude vytvořen a nasazen nový člen synchronizace. Nakonec vyberte **OK,** chcete-li zavřít stránku **Vybrat členy synchronizace.**

> [!NOTE]
> Chcete-li se připojit k synchronizaci dat SQL a místnímu agentovi, přidejte své uživatelské jméno do *DataSync_Executor*role . Synchronizace dat vytvoří tuto roli v instanci serveru SQL Server.

## <a name="configure-sync-group"></a>Konfigurace skupiny synchronizace

Po vytvoření a nasazení nových členů skupiny synchronizace se na stránce **Nová skupina synchronizace** zvýrazní **konfigurace skupiny synchronizace (krok 3).**

![Nastavení kroku 3](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na stránce **Tabulky** vyberte databázi ze seznamu členů skupiny synchronizace a vyberte **Aktualizovat schéma**.

1. Ze seznamu vyberte tabulky, které chcete synchronizovat. Ve výchozím nastavení jsou vybrány všechny sloupce, proto zakažte políčko pro sloupce, které nechcete synchronizovat. Nezapomeňte ponechat vybraný sloupec primárního klíče.

1. Vyberte **Uložit**.

1. Ve výchozím nastavení nejsou databáze synchronizovány, dokud není naplánováno nebo ručně spuštěno. Pokud chcete spustit ruční synchronizaci, přejděte do databáze SQL na webu Azure Portal, vyberte **Synchronizovat s jinými databázemi**a vyberte skupinu synchronizace. Otevře se stránka **Synchronizace dat.** Vyberte **Synchronizovat**.

    ![Ruční synchronizace](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>Nejčastější dotazy

**Jak často může synchronizace synchronizace dat?**

Minimální doba mezi synchronizacemi je pět minut.

**Vytváří synchronizace dat SQL plně tabulky?**

Pokud v cílové databázi chybí tabulky schématu synchronizace, Synchronizace dat SQL je vytvoří společně se sloupci, které jste vybrali. To však nevede k úplnému schématu věrnosti z následujících důvodů:

- V cílové tabulce se vytvoří pouze vybrané sloupce. Nevybrané sloupce se ignorují.
- V cílové tabulce se vytvoří pouze indexy vybraných sloupců. Indexy nevybraných sloupců se ignorují.
- Indexy ve sloupcích typu XML se nevytvoří.
- Omezení KONTROLY nejsou vytvořena.
- Aktivační události ve zdrojových tabulkách se nevytvoří.
- Zobrazení a uložené procedury nejsou vytvořeny.

Kvůli těmto omezením doporučujeme provést následující:

- Pro produkční prostředí vytvořte schéma plné věrnosti sami.
- Při experimentování se službou použijte funkci automatického zřizování.

**Proč se zobrazují tabulky, které jsem nevytvořil(a)?**

Synchronizace dat vytvoří další tabulky v databázi pro sledování změn. Neodstraňujte tyto nebo synchronizace dat přestane fungovat.

**Jsou moje data po synchronizaci konvergentní?**

Ne nutně. Vezměte synchronizační skupinu s rozbočovačem a třemi paprsky (A, B a C), kde jsou synchronizace rozbočovač na A, rozbočovač na B a rozbočovač na C. Pokud je provedena změna databáze A *po* synchronizaci Rozbočovač na A, tato změna není zapsána do databáze B nebo databáze C až do další úlohy synchronizace.

**Jak získám změny schématu do skupiny synchronizace?**

Proveďte a propagovat všechny změny schématu ručně.

1. Replikovat změny schématu ručně do rozbočovače a všechny členy synchronizace.
1. Aktualizujte schéma synchronizace.

Přidání nových tabulek a sloupců:

Nové tabulky a sloupce nemají vliv na aktuální synchronizaci a synchronizace dat je ignoruje, dokud je nepřidá do schématu synchronizace. Při přidávání nových databázových objektů postupujte podle pořadí:

1. Přidejte do centra a do všech členů synchronizace nové tabulky nebo sloupce.
1. Přidejte nové tabulky nebo sloupce do schématu synchronizace.
1. Začněte vkládat hodnoty do nových tabulek a sloupců.

Změna datového typu sloupce:

Změníte-li datový typ existujícího sloupce, synchronizace dat bude fungovat tak dlouho, dokud nové hodnoty odpovídají původnímu datovému typu definovanému ve schématu synchronizace. Pokud například změníte typ ve zdrojové databázi z **int** na **bigint**, synchronizace dat bude fungovat, dokud nevložíte hodnotu příliš velkou pro datový typ **int.** Chcete-li dokončit změnu, replikovat změny schématu ručně do rozbočovače a všechny členy synchronizace, pak aktualizovat schéma synchronizace.

**Jak lze exportovat a importovat databázi pomocí synchronizace dat?**

Po exportu databáze jako souboru *.bacpac* a importu souboru k vytvoření databáze postupujte takto, abyste v nové databázi použili synchronizaci dat:

1. Vyčistěte objekty synchronizace dat a další tabulky v nové databázi pomocí [tohoto skriptu](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skript odstraní všechny požadované objekty synchronizace dat z databáze.
1. Znovu vytvořte skupinu synchronizace s novou databází. Pokud již starou skupinu synchronizace nepotřebujete, odstraňte ji.

**Kde najdu informace o klientském agentovi?**

Nejčastější dotazy týkající se agenta klienta naleznete v [tématu Nejčastější dotazy k agentovi](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Další kroky

Blahopřejeme. Vytvořili jste skupinu synchronizace, která zahrnuje instanci databáze SQL i databázi serveru SQL Server.

Další informace o Synchronizaci dat SQL:

- [Agent synchronizace dat pro synchronizaci dat Azure SQL](sql-database-data-sync-agent.md)
- [Doporučené postupy](sql-database-best-practices-data-sync.md) a [řešení problémů se synchronizací dat Azure SQL](sql-database-troubleshoot-data-sync.md)
- [Monitorování synchronizace dat SQL pomocí protokolů Azure Monitoru](sql-database-sync-monitor-oms.md)
- [Aktualizace schématu synchronizace pomocí transact-SQL](sql-database-update-sync-schema.md) nebo [powershellu](scripts/sql-database-sync-update-schema.md)

Další informace o službě SQL Database:

- [Přehled služby SQL Database](sql-database-technical-overview.md)
- [Správa životního cyklu databáze](https://msdn.microsoft.com/library/jj907294.aspx)
