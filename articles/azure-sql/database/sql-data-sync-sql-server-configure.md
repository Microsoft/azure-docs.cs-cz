---
title: Nastavení synchronizace dat SQL
description: V tomto kurzu se dozvíte, jak nastavit Synchronizace dat SQL pro Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 75de7b122bff75ea13e3b66bb0b79452142dc36c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500086"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Kurz: nastavení Synchronizace dat SQL mezi databázemi v Azure SQL Database a SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se naučíte nastavit Synchronizace dat SQL tím, že vytvoříte skupinu synchronizace, která bude obsahovat instance Azure SQL Database a SQL Server. Skupina synchronizace je nastavena na vlastní konfiguraci a synchronizuje podle nastaveného plánu.

V tomto kurzu se předpokládá, že máte minimálně některé předchozí zkušenosti s SQL Database a SQL Server.

Přehled Synchronizace dat SQL najdete v tématu [synchronizace dat napříč cloudem a místními databázemi pomocí synchronizace dat SQL](sql-data-sync-data-sql-server-sql-database.md).

Příklady prostředí PowerShell týkající se konfigurace Synchronizace dat SQL najdete v tématu [synchronizace mezi databázemi v SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md) nebo [mezi databázemi v Azure SQL Database a SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Synchronizace dat SQL v tuto **chvíli nepodporuje spravovanou** instanci SQL Azure.

## <a name="create-sync-group"></a>Vytvořit skupinu synchronizace

1. Přejít na [Azure Portal](https://portal.azure.com) a vyhledat svou databázi v SQL Database. Vyhledejte a vyberte **databáze SQL**.

    ![Vyhledejte databáze portál Microsoft Azure](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Vyberte databázi, kterou chcete použít jako databázi centra pro synchronizaci dat.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > Databáze centra je centrální koncový bod synchronizační topologie, ve kterém má skupina synchronizace více koncových bodů databáze. Všechny ostatní členské databáze s koncovými body ve skupině synchronizace se synchronizují s databází centra.

1. V nabídce **databáze SQL** pro vybranou databázi vyberte možnost **synchronizovat s ostatními databázemi**.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. Na stránce **synchronizovat s ostatními databázemi** vyberte **Nová skupina synchronizace**. Otevře se stránka **Nová skupina synchronizace** s **vytvořením skupiny synchronizace (krok 1)**.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   Na stránce **vytvořit skupinu synchronizace dat** změňte následující nastavení:

   | Nastavení                        | Popis |
   | ------------------------------ | ------------------------------------------------- |
   | **Název skupiny synchronizace** | Zadejte název nové skupiny synchronizace. Tento název se liší od názvu samotné databáze. |
   | **Synchronizovat databázi metadat** | Vyberte Vytvoření databáze (doporučeno) nebo pro použití existující databáze.<br/><br/>Zvolíte-li možnost **Nová databáze**, vyberte možnost **vytvořit novou databázi.** Pak na stránce **SQL Database** zadejte název a nakonfigurujte novou databázi a vyberte **OK**.<br/><br/>Pokud zvolíte možnost **použít existující databázi**, vyberte databázi ze seznamu. |
   | **Automatická synchronizace** | Vyberte **zapnuto** nebo **vypnuto**.<br/><br/>Pokud **se rozhodnete, zadejte** číslo a v části **četnost synchronizací** vyberte **sekundy**, **minuty**, **hodiny** nebo **dny** .<br/> První synchronizace začíná po zvoleném období intervalu od doby uložení konfigurace.|
   | **Řešení konfliktů** | Vyberte možnost **Win hub** nebo **Member Win**.<br/><br/>**Výher centra** znamená, že když dojde ke konfliktům, data v databázi centra přepíší konfliktní data v členské databázi.<br/><br/>**Člen výher** znamená, že když dojde ke konfliktům, data v členské databázi přepíší konfliktní data v databázi centra. |
   | **Použít privátní odkaz** | Vyberte privátní koncový bod spravovaný službou pro vytvoření zabezpečeného připojení mezi službou synchronizace a databází centra. |

   > [!NOTE]
   > Společnost Microsoft doporučuje vytvořit novou, prázdnou databázi pro použití jako **databázi metadat synchronizace**. Synchronizace dat vytvoří tabulky v této databázi a spustí časté úlohy. Tato databáze je sdílená jako **databáze metadat synchronizace** pro všechny skupiny synchronizace ve vybrané oblasti a předplatném. Nemůžete změnit databázi ani její název, aniž byste odebrali všechny skupiny synchronizace a agenty synchronizace v oblasti. Databázi elastických úloh navíc nelze použít jako databázi metadat Synchronizace dat SQL a naopak.  

   Vyberte **OK** a počkejte, než se skupina synchronizace vytvoří a nasadí.
   
1. Pokud jste vybrali **možnost použít privátní odkaz**, na stránce **Nová skupina synchronizace** bude nutné schválit připojení privátního koncového bodu. Odkaz v informační zprávě vás převezme do prostředí připojení privátního koncového bodu, kde můžete připojení schválit. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Přidat členy synchronizace

Po vytvoření a nasazení nové skupiny synchronizace je na stránce **Nová skupina synchronizace** zvýrazněna položka **přidat členy synchronizace (krok 2)** .

V části **databáze centra** Zadejte existující pověření pro server, na kterém se nachází databáze centra. Nezadávejte *nové* přihlašovací údaje v této části.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Přidání databáze v Azure SQL Database

V části **databáze členů** můžete volitelně přidat databázi v Azure SQL Database do skupiny synchronizace výběrem možnosti **Přidat Azure SQL Database**. Otevře se stránka **konfigurace Azure SQL Database** .
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  Na stránce **konfigurace Azure SQL Database** změňte následující nastavení:

  | Nastavení                       | Popis |
  | ----------------------------- | ------------------------------------------------- |
  | **Název člena synchronizace** | Zadejte název nového člena synchronizace. Tento název se liší od samotného názvu databáze. |
  | **Předplatné** | Vyberte přidružené předplatné Azure pro účely fakturace. |
  | **Azure SQL Server** | Vyberte existující server. |
  | **Azure SQL Database** | Vyberte existující databázi v SQL Database. |
  | **Pokyny pro synchronizaci** | Vyberte **obousměrnou synchronizaci**, **do centra** nebo **z centra**. |
  | **Uživatelské jméno** a **heslo** | Zadejte existující pověření pro server, na kterém je umístěna členský databáze. Nezadávejte *nové* přihlašovací údaje v této části. |
  | **Použít privátní odkaz** | Vyberte privátní koncový bod spravovaný službou pro vytvoření zabezpečeného připojení mezi synchronizační službou a členskou databází. |

  Vyberte **OK** a počkejte, než se nový člen synchronizace vytvoří a nasadí.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Přidání databáze SQL Server

V části **databáze členů** můžete volitelně přidat SQL Server databázi do skupiny synchronizace výběrem možnosti **Přidat místní databázi**. Otevře se stránka **Konfigurace na** místě, kde můžete provádět následující akce:

1. Vyberte možnost **zvolit bránu agenta synchronizace**. Otevře se stránka **Vybrat agenta synchronizace** .

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. Na stránce **Vybrat agenta synchronizace** vyberte, jestli se má použít stávající agent nebo vytvořit agenta.

   Pokud zvolíte možnost **stávající agenti**, vyberte ze seznamu existující agent.

   Pokud zvolíte možnost **vytvořit nového agenta**, proveďte následující akce:

   1. Stáhněte agenta synchronizace dat z poskytnutého odkazu a nainstalujte ho do počítače, kde se nachází SQL Server. Agenta můžete také stáhnout přímo z [Azure synchronizace dat SQL agenta](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Musíte otevřít odchozí port TCP 1433 v bráně firewall, aby agent klienta mohl komunikovat se serverem.

   1. Zadejte název agenta.

   1. Vyberte **vytvořit a vygenerovat klíč** a zkopírujte klíč agenta do schránky.

   1. Výběrem **OK** zavřete stránku **Vybrat agenta synchronizace** .

1. V SQL Server počítači Najděte a spusťte aplikaci agenta synchronizace klienta.

   ![Aplikace agenta klienta synchronizace dat](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. V aplikaci agenta synchronizace vyberte **Odeslat klíč agenta**. Otevře se dialogové okno **Konfigurace databáze synchronizace metadat** .

    1. V dialogovém okně **synchronizovat konfiguraci databáze** proveďte vložení do klíče agenta zkopírovaného z Azure Portal. Zadejte taky existující přihlašovací údaje pro server, na kterém se nachází databáze metadat. (Pokud jste vytvořili databázi metadat, je tato databáze na stejném serveru jako databáze centra.) Vyberte **OK** a počkejte na dokončení konfigurace.

        ![Zadejte klíč agenta a přihlašovací údaje serveru.](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Pokud se zobrazí chyba brány firewall, vytvořte v Azure pravidlo brány firewall, které povolí příchozí provoz z SQL Server počítače. Pravidlo můžete vytvořit ručně na portálu nebo v SQL Server Management Studio (SSMS). V SSMS se připojte k databázi centra v Azure zadáním jejího názvu jako <hub_database_name>. database.windows.net.

    1. Vyberte **Registrovat** pro registraci SQL Server databáze s agentem. Otevře se dialogové okno **konfigurace SQL Server** .

        ![Přidání a konfigurace databáze SQL Server](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. V dialogovém okně **konfigurace SQL Server** vyberte možnost připojit se pomocí ověřování SQL Server nebo ověřování systému Windows. Pokud zvolíte SQL Server ověřování, zadejte existující přihlašovací údaje. Zadejte SQL Server název a název databáze, kterou chcete synchronizovat, a vyberte **Test připojení** pro otestování nastavení. Pak vyberte **Uložit** a v seznamu se zobrazí registrovaná databáze.

        ![SQL Server databáze je nyní zaregistrována.](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Zavřete aplikaci agenta synchronizace klienta.

1. Na portálu na stránce **Konfigurovat** místní vyberte **možnost vybrat databázi**.

1. Na stránce **Vybrat databázi** zadejte do pole **název člena synchronizace** název nového člena synchronizace. Tento název se liší od názvu samotné databáze. Vyberte databázi ze seznamu. V poli **pokyny pro synchronizaci** vyberte **obousměrnou synchronizaci**, **do centra** nebo **z centra**.

    ![Výběr místní databáze](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Výběrem **OK** zavřete stránku **Vybrat databázi** . Pak výběrem **OK** zavřete stránku **Konfigurovat** místní a počkejte, než se vytvoří a nasadí nový člen synchronizace. Nakonec kliknutím na **tlačítko OK** zavřete stránku **Vybrat členy synchronizace** .

> [!NOTE]
> Pokud se chcete připojit k Synchronizace dat SQL a místnímu agentovi, přidejte své uživatelské jméno do *DataSync_Executor* role. Synchronizace dat vytvoří tuto roli na instanci SQL Server.

## <a name="configure-sync-group"></a>Konfigurovat skupinu synchronizace

Po vytvoření a nasazení nových členů skupiny synchronizace se na stránce **Nová skupina synchronizace** zvýrazní **Konfigurace skupiny synchronizace (krok 3)** .

![Krok 3 – nastavení](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Na stránce **tabulky** vyberte databázi ze seznamu členů skupiny synchronizace a vyberte možnost **Aktualizovat schéma**.

1. V seznamu vyberte tabulky, které chcete synchronizovat. Ve výchozím nastavení jsou vybrány všechny sloupce, takže políčko pro sloupce, které nechcete synchronizovat, zakažte. Nezapomeňte ponechat vybraný sloupec primárního klíče.

1. Vyberte **Uložit**.

1. Ve výchozím nastavení nejsou databáze synchronizovány, dokud je neplánujete nebo ručně spustíte. Chcete-li spustit ruční synchronizaci, přejděte do databáze v SQL Database Azure Portal, vyberte možnost **synchronizovat s ostatními databázemi** a vyberte skupinu synchronizace. Otevře se stránka **synchronizace dat** . Vyberte **Synchronizovat**.

    ![Ruční synchronizace](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Časté otázky

**Synchronizace dat SQL plně vytvořit tabulky?**

Pokud v cílové databázi chybí tabulky schématu synchronizace, Synchronizace dat SQL je vytvoří společně se sloupci, které jste vybrali. To ale nevede ke schématu s plnou přesností z následujících důvodů:

- V cílové tabulce se vytvoří pouze vybrané sloupce. Nevybrané sloupce se ignorují.
- V cílové tabulce se vytvoří pouze indexy vybraných sloupců. Indexy nevybraných sloupců se ignorují.
- Indexy ve sloupcích typu XML nejsou vytvořeny.
- Omezení CHECK nejsou vytvořena.
- Aktivační události ve zdrojových tabulkách nejsou vytvořeny.
- Zobrazení a uložené procedury nejsou vytvořeny.

Kvůli těmto omezením doporučujeme provést následující:

- V produkčních prostředích vytvořte schéma s plnou přesností sami.
- Při experimentování se službou použijte funkci automatického zřizování.

**Proč se mi zobrazují tabulky, které jsem nevytvořil?**

Při synchronizaci dat se v databázi vytvoří další tabulky pro sledování změn. Neodstraňujte je ani synchronizace dat přestane fungovat.

**Je moje data Convergent po synchronizaci?**

Ne nutně. Pořídit skupinu synchronizace s rozbočovačem a třemi paprsky (A, B a C), kde se synchronizují rozbočovače k, rozbočovači a B a hub do C. Pokud provedete změnu v databázi A *po* synchronizaci centra, tato změna nebude zapsána do databáze B nebo databáze C až do další úlohy synchronizace.

**Návody získat změny schématu do skupiny synchronizace?**

Udělejte všechny změny schématu a rozšiřte je ručně.

1. Ruční replikace změn schématu do centra a všech členů synchronizace.
1. Aktualizujte schéma synchronizace.

Pro přidání nových tabulek a sloupců:

Nové tabulky a sloupce nemají vliv na aktuální synchronizaci a synchronizace dat je ignorují, dokud nebudou přidány do schématu synchronizace. Při přidávání nových databázových objektů postupujte podle pořadí:

1. Přidejte nové tabulky nebo sloupce do centra a všech členů synchronizace.
1. Přidejte nové tabulky nebo sloupce do schématu synchronizace.
1. Začněte vkládat hodnoty do nových tabulek a sloupců.

Změna datového typu sloupce:

Když změníte datový typ existujícího sloupce, synchronizace dat bude fungovat, dokud se nové hodnoty přizpůsobí původnímu datovému typu definovanému ve schématu synchronizace. Pokud například změníte typ ve zdrojové databázi z **int** na **bigint**, synchronizace dat pokračuje v práci, dokud nevložíte příliš velkou hodnotu pro datový typ **int** . Chcete-li dokončit změnu, proveďte ruční replikaci změn schématu do centra a všech členů synchronizace a pak aktualizujte schéma synchronizace.

**Jak můžu exportovat a importovat databázi se synchronizací dat?**

Po exportu databáze jako souboru *. BacPac* a importu souboru pro vytvoření databáze proveďte následující postup pro použití synchronizace dat v nové databázi:

1. Vyčistěte objekty synchronizace dat a další tabulky v nové databázi pomocí [tohoto skriptu](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skript odstraní z databáze všechny požadované objekty synchronizace dat.
1. Znovu vytvořte skupinu synchronizace s novou databází. Pokud už starou skupinu synchronizace nepotřebujete, odstraňte ji.

**Kde najdu informace o agentovi klienta?**

Nejčastější dotazy týkající se klientského agenta najdete v tématu Nejčastější dotazy k [agentům](sql-data-sync-agent-overview.md#agent-faq).

**Je nutné před zahájením používání privátního propojení ručně schválit?**

Ano, na stránce připojení privátního koncového bodu Azure Portal během nasazování skupiny synchronizace nebo pomocí prostředí PowerShell musíte ručně schválit soukromý koncový bod spravované službou.

**Proč se při zřizování úlohy synchronizace s Azure Database zobrazí chyba brány firewall?**

K tomu může dojít, protože prostředky Azure nemají povolený přístup k vašemu serveru. Zajistěte, aby brána firewall v Azure Database má nastavení "Povolte službám a prostředkům Azure přístup k tomuto serveru" nastavené na hodnotu "Ano".


## <a name="next-steps"></a>Další kroky

Blahopřejeme. Vytvořili jste skupinu synchronizace, která zahrnuje instanci SQL Database a databázi SQL Server.

Další informace o Synchronizaci dat SQL:

- [Agent synchronizace dat pro Azure Synchronizace dat SQL](sql-data-sync-agent-overview.md)
- [Osvědčené postupy](sql-data-sync-best-practices.md) a [řešení potíží s Azure synchronizace dat SQL](sql-data-sync-troubleshoot.md)
- [Monitorování Synchronizace dat SQL pomocí protokolů Azure Monitor](./monitor-tune-overview.md)
- [Aktualizace schématu synchronizace pomocí jazyka Transact-SQL](sql-data-sync-update-sync-schema.md) nebo [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Další informace o službě SQL Database:

- [Přehled služby SQL Database](sql-database-paas-overview.md)
- [Správa životního cyklu databáze](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
