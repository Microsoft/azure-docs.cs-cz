---
title: Auditování v Azure SQL Data Warehouse | Microsoft Docs
description: Další informace o auditování a jak nastavit auditování v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: rortloff
ms.reviewer: jrj
ms.openlocfilehash: 7bce59114dcfef7f9faa7f112c62dbb88044f2ab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditování v Azure SQL Data Warehouse

Další informace o auditování a jak nastavit auditování v Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Co je auditování?
Auditování SQL Data Warehouse umožňuje záznam, který protokolují se události v databázi auditování v účtu úložiště Azure. Auditování vám může pomoct zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které by mohly být známkou problémů obchodního charakteru nebo by mohly vzbuzovat podezření narušení zabezpečení. Auditování SQL Data Warehouse taky integruje s Microsoft Power BI pro vytváření sestav a analýzu.

Nástroje auditování povolit a zajištění dodržování standardů dodržování předpisů, ale nezaručují dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v části <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centrum zabezpečení Azure</a>.

## <a id="subheading-1"></a>Základní informace o auditování
Auditování databáze SQL Data Warehouse umožňuje:

* **Zachovat** monitorovat vybrané události. Můžete definovat kategorie akce databáze k auditování.
* **Sestava** v databázové aktivitě. Abyste mohli rychle začít s aktivity a události vytváření sestav můžete předem nakonfigurované sestavy a řídicí panel.
* **Analýza** sestavy. Můžete najít podezřelé události, neobvyklé aktivity a trendy.

Můžete nakonfigurovat auditování pro kategorii událostí následující:

**Nešifrovaná SQL** a **parametry SQL** pro které jsou klasifikovány protokolů auditu shromážděných jako  

* **Přístup k datům**
* **Změny schématu (DDL)**
* **Změny dat (DML)**
* **Účty, rolí a oprávnění (DCL)**
* **Uložené procedury**, **přihlášení** a, **transakce správu**.

Pro každou kategorii událostí auditování z **úspěch** a **selhání** operace jsou nakonfigurovány odděleně.

Další informace o aktivitách a auditovat události najdete v tématu <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">odkazu formátu protokolu auditu (soubor dokumentace ke stažení)</a>.

Protokoly auditu jsou uložené v účtu úložiště Azure. Můžete definovat po dobu uchování protokolu auditu.

Můžete definovat zásady pro auditování pro konkrétní databázi nebo jako výchozí zásady serveru. Výchozí zásady auditování serveru se vztahuje na všechny databáze na serveru, které nemají žádný zvláštní přepsání databáze auditování zásady definované.

Před nastavením auditování kontrolu, pokud používáte auditu ["Starších klientů."](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Nastavení auditování pro vaši databázi
1. Spusťte <a href="https://portal.azure.com" target="_blank">portál Azure</a>.
2. Přejděte na **nastavení** pro SQL Data Warehouse, které chcete auditovat. Vyberte **auditování a detekce hrozeb**.
   
    ![][1]
3. Dál povolte auditování kliknutím **ON** tlačítko.
   
    ![][3]
4. V panelu Konfigurace auditování vyberte **podrobnosti úložiště** otevřete panel úložiště protokoly auditu. Vyberte účet úložiště Azure pro protokoly a dobu uchování. 
>[!TIP]
>Použijte stejný účet úložiště pro všechny databáze, auditované k plnému využití mimo šablony předem nakonfigurované sestavy.
   
    ![][4]
5. Klikněte **OK** tlačítko pro uložení podrobnosti o konfiguraci úložiště.
6. V části **protokolování podle událostí**, klikněte na tlačítko **úspěch** a **selhání** protokolovaly všechny události, nebo zvolte kategorií jednotlivých událostí.
7. Pokud konfigurujete auditování pro databázi, musíte změnit připojovací řetězec vašeho klienta zajistit, že data auditování pořízen správně. Zkontrolujte [upravit FDQN serveru v připojovacím řetězci](sql-data-warehouse-auditing-downlevel-clients.md) tématu pro připojení klientů nižší úrovně.
8. Klikněte na **OK**.

## <a id="subheading-3"></a>Analýza protokolů auditu a sestavy
Protokoly auditu je agregován v kolekci úložiště tabulek s **SQLDBAuditLogs** předpony v účtu úložiště Azure, který jste zvolili během instalace. Můžete zobrazit soubory protokolů pomocí některého nástroje, jako třeba <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage Explorer</a>.

Šablona sestavy předkonfigurované řídicího panelu je k dispozici jako <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">ke stažení tabulku aplikace Excel</a> můžete rychle analyzovat data protokolu. Abyste mohli použít šablonu na vaše protokoly auditu, budete potřebovat Excel 2013 nebo novější a Power Query, kterou si můžete stáhnout <a href="http://www.microsoft.com/download/details.aspx?id=39379">zde</a>.

Šablona má fiktivních ukázkových dat v něm a můžete nastavit Power Query pro import auditní protokol přímo z vašeho účtu úložiště Azure.

## <a id="subheading-4"></a>Opětovné generování klíče úložiště
V produkčním prostředí budete pravděpodobně pravidelně aktualizovat klíče úložiště. Při aktualizaci klíče, je nutné uložit zásadu. Proces je následující:

1. Auditování konfigurace panel, který je popsaný v předchozím nastavení auditování oddíl, změňte **přístupový klíč k úložišti** z *primární* k *sekundární* a  **Uložit**.

   ![][4]
2. Přejděte na panelu Konfigurace úložiště a **znovu vygenerovat** *primární přístupový klíč*.
3. Přejděte zpět na panelu Konfigurace auditování 
4. Přepnout **přístupový klíč k úložišti** z *sekundární* k *primární* a stiskněte klávesu **Uložit**.
4. Přejděte zpět na úložiště uživatelského rozhraní a **znovu vygenerovat** *sekundární přístupový klíč* (jako příprava pro další aktualizace cyklu klíče.

## <a id="subheading-5"></a>Automatizace (prostředí PowerShell nebo REST API)
Můžete taky nakonfigurovat auditování v Azure SQL Data Warehouse pomocí následujících nástrojů automatizace:

* **Rutiny prostředí PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Podpora klientů nižší úrovně pro auditování a dynamické maskování dat.
Auditování funguje s klienty SQL, které podporují přesměrování TDS.

Libovolného klienta, který implementuje TDS 7.4 také podporuje přesměrování. Výjimky zahrnují JDBC 4.0, ve kterém funkci přesměrování není plně podporována a Tedious pro Node.JS, ve které přesměrování nebyla implementována.

Pro "Klienty nižší úrovně" podporující TDS verze 7.3 a níže, upravte plně kvalifikovaný název domény serveru v připojovacím řetězci následujícím způsobem:

- Původní server plně kvalifikovaný název domény v připojovacím řetězci: <*název serveru*>. database.windows.net
- Upravené plně kvalifikovaný název domény serveru v připojovacím řetězci: <*název serveru*> .database. **zabezpečené**. windows.net

Částečný seznam "Klienty nižší úrovně" zahrnuje:

* Rozhraní .NET 4.0 a níže,
* ODBC 10.0 a níže.
* JDBC (při JDBC podporuje TDS 7.4, TDS přesměrování funkce není podporována plně)
* Zdlouhavé (pro platformu Node.JS)

**Poznámka:** předchozí server úpravy plně kvalifikovaného názvu domény může být užitečné také pro použití zásad auditování na úrovni SQL serveru bez potřebu konfiguraci krok v každé databázi (dočasné zmírnění).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


