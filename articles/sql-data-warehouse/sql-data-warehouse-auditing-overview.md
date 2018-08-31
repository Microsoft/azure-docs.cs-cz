---
title: Auditování ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Další informace o auditování a jak nastavit auditování ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: c894fed56393e1504f54999e57bba6f9ffed27a7
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288250"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditování ve službě Azure SQL Data Warehouse

Další informace o auditování a jak nastavit auditování ve službě Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Co je auditování?
Auditování SQL Data Warehouse umožňuje záznam protokolu události v databázi do auditování ve vašem účtu úložiště Azure. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

Nástroje auditování umožňují a usnadnění dodržování standardů dodržování předpisů, ale nezaručují dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v článku [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Základní informace o auditování
Auditování databáze SQL Data Warehouse vám umožňuje:

* **Zachovat** záznam auditovaných vybrané události. Můžete definovat kategorie akce databáze, které se budou auditovat.
* **Sestava** na databázové aktivity. Abyste mohli rychle začít s aktivitou a generování sestav událostí můžete použít předem nakonfigurované sestavy a řídicí panel.
* **Analýza** sestavy. Můžete najít podezřelých událostí, neobvyklé aktivity a trendů.

Protokoly auditu se ukládají v účtu úložiště Azure. Můžete definovat na dobu uchování protokolu auditu.


## <a id="subheading-4"></a>Definovat na úrovni serveru oproti zásady auditování na úrovni databáze

Zásady auditu mohou být definovány pro konkrétní databázi nebo jako výchozí server zásady:

* Zásada serveru **se vztahuje na všechny stávající a nově vytvořené databáze** na serveru.

* Pokud *je povolené auditování objektů blob serveru*, ho *vždy platí pro databáze*. Databáze se dají auditovat, bez ohledu na nastavení auditování databáze.

* Povolení auditování pro databáze, kromě povolení na serveru, nemá *není* přepsat nebo změnit nastavení auditování objektů blob serveru. Obě audity budou existovat vedle sebe. Jinými slovy databáze se Audituje dvakrát paralelně; jednou tak, že server zásad a jednou sadou zásad databáze.

> [!NOTE]
> Doporučujeme, abyste povolili **auditování objektů blob jenom úroveň serveru** a databáze auditu zakázané pro všechny databáze.
> Měli byste se vyhnout, povolení auditování serveru a databáze auditu společně, pokud:
> * Chcete použít jinou *účtu úložiště* nebo *dobu uchování* konkrétní databáze.
> * Chcete auditovat typy událostí nebo kategorie pro určitou databázi, která se liší od ostatních databází na serveru. Například můžete mít vloží tabulky, které musí být auditován jenom pro konkrétní databáze.
> * Chcete použít detekci hrozeb, která je aktuálně podporuje jen při auditování na úrovni databáze.

> [!IMPORTANT]
>Povolení auditování služby Azure SQL Data Warehouse, nebo na serveru, který má službu Azure SQL Data Warehouse, **povede k datovému skladu obnovuje**, i v případě, kde bylo dříve pozastaveno. **Zkontrolujte prosím, že se pozastavit datový sklad znovu po povolení auditování**.

## <a id="subheading-5"></a>Nastavení auditování na úrovni serveru pro všechny databáze

Zásady auditu serveru platí pro **všechny stávající a nově vytvořené databáze** na serveru.

Následující část popisuje konfigurace auditování pomocí webu Azure portal.

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Přejděte **systému SQL server** , kterou chcete auditu (důležité, ujistěte se, že si prohlížíte SQL serveru, ne konkrétní databáze/datový Sklad). V **zabezpečení** nabídce vyberte možnost **auditování a detekce hrozeb**.

    ![Navigační podokno][6]
4. V *auditování a detekce hrozeb* okně pro **auditování** vyberte **ON**. Toto auditování zásady budou vztahovat na všechny stávající a nově vytvořené databáze na tomto serveru.

    ![Navigační podokno][7]
5. Chcete-li otevřít **úložiště protokolů auditu** okně vyberte **podrobnosti o úložišti**. Vyberte nebo vytvořte účet úložiště Azure, kde se uloží protokoly a pak vyberte dobu uchování (staré protokoly odstraní). Pak klikněte na **OK**.

    ![Navigační podokno][8]

    > [!IMPORTANT]
    > Protokoly auditování na úrovni serveru se zapisují do **doplňovací objekty BLOB** ve službě Azure Blob storage v rámci předplatného Azure.
    >
    > * **Storage úrovně Premium** je aktuálně **nepodporuje** podle doplňovacích objektů BLOB.
    > * **Úložiště ve virtuální síti** je aktuálně **nepodporuje**.

8. Klikněte na **Uložit**.



## <a id="subheading-2"></a>Nastavení auditování na úrovni databáze pro izolovanou databázi

Můžete definovat zásady auditu pro konkrétní databázi nebo jako výchozí server zásady.

Důrazně doporučujeme auditování úrovni serveru a ne databáze na úrovni auditování, jak je popsáno v [definovat úrovni serveru oproti zásady auditování na úrovni databáze](#subheading-4)

Před nastavením auditování zaškrtněte, pokud používáte auditu ["Starších klientů"](sql-data-warehouse-auditing-downlevel-clients.md).


1. Spusťte [webu Azure portal](https://portal.azure.com).
2. Přejděte na **nastavení** pro SQL Data Warehouse, které chcete auditovat. Vyberte **auditování a detekce hrozeb**.

    ![][1]
3. V dalším kroku povolení auditování kliknutím **ON** tlačítko.

    ![][3]
4. Na panelu Konfigurace auditování vyberte **podrobnosti o ÚLOŽIŠTI** otevřete panel úložiště protokolů auditu. Vyberte účet úložiště Azure pro protokoly a dobu uchování.
    >[!TIP]
    >Pomocí stejného účtu úložiště pro všechny auditované databáze naplno využít předem nakonfigurované sestavy šablony.

    ![][4]

5. Klikněte na tlačítko **OK** tlačítko Uložit podrobnosti o konfiguraci úložiště.
6. V části **protokolování podle událostí**, klikněte na tlačítko **úspěch** a **selhání** protokolovaly všechny události, nebo zvolte kategorie jednotlivých událostí.
7. Pokud konfigurujete auditování pro databáze, budete muset změnit připojovací řetězec k zajištění, že data auditování je správně zachytit váš klient. Zkontrolujte [upravit FDQN serveru v připojovacím řetězci](sql-data-warehouse-auditing-downlevel-clients.md) tématu pro připojení klientů nižší úrovně.
8. Klikněte na **OK**.

## <a id="subheading-3"></a>Analýza protokolů auditu a sestavy

###<a name="server-level-policy-audit-logs"></a>Protokoly auditu zásady na úrovni serveru
Protokoly auditování na úrovni serveru se zapisují do **doplňovací objekty BLOB** ve službě Azure Blob storage v rámci předplatného Azure. Jsou uloženy jako kolekce souborů, objektů blob v kontejneru s názvem **sqldbauditlogs**.

Další podrobnosti o hierarchii složky úložiště konvence pojmenování a formát protokolu, najdete v článku [odkaz na formát protokolu auditování objektů Blob](https://go.microsoft.com/fwlink/?linkid=829599).

Existuje několik metod, které lze použít k zobrazení protokolů auditování objektů blob:

* Použití **sloučit soubory auditu** v SQL Server Management Studio (od verze aplikace SSMS 17):
    1. Vyberte z nabídky aplikace SSMS **souboru** > **otevřít** > **sloučit soubory auditu**.

    2. **Přidat soubory auditu** zobrazí se dialogové okno. Vyberte jednu z **přidat** možnosti a vyberte, jestli se má sloučit soubory auditu z místního disku nebo importovat ze služby Azure Storage. Je potřeba zadat podrobnosti o Azure Storage a klíč účtu.

    3. Po přidání všech souborů ke sloučení klikněte na tlačítko **OK** k dokončení operace sloučení.

    4. Sloučený soubor se otevře v SSMS, kde je můžete zobrazit a analyzovat, jakož i exportovat do souboru XEL nebo sdíleného svazku clusteru nebo na tabulku.

* Použití [synchronizovat aplikace](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) , kterou jsme vytvořili. Spuštění v Azure a využívá Log Analytics veřejných rozhraní API pro protokoly auditu SQL nasdílení změn do Log Analytics. Synchronizace aplikace nabízených oznámení protokoly auditu SQL do Log Analytics pro spotřebu prostřednictvím řídicích panelů Log Analytics.

* Použití Power BI. Můžete zobrazit a analyzovat data protokolů auditu v Power BI. Další informace o [Power BI a přístup ke stažení šablony](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Stáhněte si soubory protokolů z vašeho kontejneru objektů blob v Azure Storage prostřednictvím portálu nebo pomocí nástroje [Průzkumníka služby Azure Storage](http://storageexplorer.com/).
    * Po stažení souboru protokolu místně, dvakrát klikněte na soubor otevřít, zobrazení a analýza protokolů v aplikaci SSMS.
    * Můžete také stáhnout více souborů najednou pomocí Průzkumníka služby Azure Storage. Klikněte pravým tlačítkem na konkrétní podsložku a vyberte **uložit jako** uložit do místní složky.

* Další metody:
   * Po stažení několik souborů nebo na podsložku, která obsahuje soubory protokolu, sloučit je místně, jak je popsáno v SSMS sloučení auditu soubory pokyny popsané výše.

   * Auditování objektů blob k zobrazení protokolů prostřednictvím kódu programu:

     * Použití [rozšířené události čtečky](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) knihovny v jazyce C#.
     * [Dotazování rozšířené události soubory](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) pomocí prostředí PowerShell.



<br>
###<a name="database-level-policy-audit-logs"></a>Protokoly auditu zásady na úrovni databáze
Protokoly auditování na úrovni databáze se agregují v kolekci Store tabulek s **SQLDBAuditLogs** předponu v účtu úložiště Azure, který jste zvolili během instalace. Můžete zobrazit soubory protokolů pomocí nástroje, jako například [Průzkumníka služby Azure Storage](http://azurestorageexplorer.codeplex.com).

Je k dispozici jako šablona sestavy řídicí panel předkonfigurovaného [ke stažení Excelové tabulce](http://go.microsoft.com/fwlink/?LinkId=403540) umožňují rychle analyzovat data protokolů. Použití šablony v protokolech auditu, budete potřebovat Excel 2013 nebo novější a Power Query, který můžete [stáhnete tady](http://www.microsoft.com/download/details.aspx?id=39379).

Šablona v sobě obsahuje fiktivní ukázková data a můžete nastavit Power Query importovat protokolů auditu přímo ze svého účtu úložiště Azure.

## <a id="subheading-4"></a>Opětovném generování přístupového klíče úložiště
V produkčním prostředí budete pravděpodobně pravidelně aktualizují vaše klíče úložiště. Při aktualizaci klíčů, budete muset uložit zásady. Probíhá takto:

1. Auditování panel konfigurace, která je popsána v předchozím nastavení auditování oddíl, změňte **přístupový klíč k úložišti** z *primární* k *sekundární* a  **Uložit**.

   ![][4]
2. Přejděte na panel konfigurace úložiště a **znovu vygenerovat** *primární přístupový klíč*.
3. Přejděte zpět na panelu Konfigurace auditování
4. Přepnout **přístupový klíč k úložišti** z *sekundární* k *primární* a stiskněte klávesu **Uložit**.
4. Vraťte se zpět do úložiště uživatelského rozhraní a **znovu vygenerovat** *sekundární přístupový klíč* (jako příprava pro další klíče obnovit cyklu.

## <a id="subheading-5"></a>Automatizace (Powershellu nebo rozhraní REST API)
Můžete taky nakonfigurovat auditování ve službě Azure SQL Data Warehouse pomocí následujících nástrojů pro automatizaci:

* **Rutiny Powershellu**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Podpora klientů nižší úrovně pro auditování a dynamické maskování dat
Auditování spolupracuje s klienti SQL, která podporují přesměrování TDS.

Libovolného klienta, který implementuje TDS 7.4 by měl také podporovat přesměrování. Výjimky zahrnují JDBC 4.0, ve kterém se plně nepodporuje funkci přesměrování a Tedious pro Node.JS, v které přesměrování nebyla implementována.

Pro "Klienty nižší úrovně", které podporují TDS verzi 7.3 a níže, změnit server plně kvalifikovaný název domény v připojovacím řetězci následujícím způsobem:

- Původní server plně kvalifikovaný název domény v připojovacím řetězci: <*název serveru*>. database.windows.net
- Upravené server plně kvalifikovaný název domény v připojovacím řetězci: <*název serveru*> .database. **zabezpečené**. windows.net

Částečný seznam "Klienti nižší úrovně" zahrnuje:

* Rozhraní .NET 4.0 a nižší,
* ODBC 10.0 a nižší.
* JDBC (zatímco JDBC podporují TDS 7.4, funkce přesměrování protokolu TDS není plně podporovány)
* Tedious (pro Node.JS)

**Poznámka:** předchozí server plně kvalifikovaného názvu domény změny mohou být užitečné také pro použití zásady auditování služby SQL Server úroveň bez potřebu konfigurační krok v každé databázi (dočasná omezení rizik).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
