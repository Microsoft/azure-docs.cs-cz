---
title: Začínáme s auditem databáze Azure SQL | Dokumentace Microsoftu
description: Auditování služby Azure SQL database použijte ke sledování databázových událostí do protokolu auditu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: bd40faf8a77a8940dc78375ec516c39742540231
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352833"
---
# <a name="get-started-with-sql-database-auditing"></a>Začínáme s auditem databáze SQL

Auditování Azure SQL database sleduje události databáze a zapisuje je do auditu protokolu ve vašem účtu úložiště Azure. Také auditování:

- Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

- Povolí a umožňuje dodržování standardů dodržování předpisů, ale nezaručuje dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v článku [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Přehled auditování Azure SQL database

Můžete použít auditování služby SQL database do:

- **Zachovat** záznam auditovaných vybrané události. Můžete definovat kategorie akce databáze, které se budou auditovat.
- **Sestava** na databázové aktivity. Abyste mohli rychle začít s aktivitou a generování sestav událostí můžete použít předem nakonfigurované sestavy a řídicí panel.
- **Analýza** sestavy. Můžete najít podezřelých událostí, neobvyklé aktivity a trendů.

Můžete nakonfigurovat auditování pro různé typy kategorie událostí, jak je vysvětleno v [nastavení auditování databáze](#subheading-2) oddílu.

> [!IMPORTANT]
> Protokoly auditu se zapisují do **doplňovací objekty BLOB** ve službě Azure Blob storage v rámci předplatného Azure.
>
> - **Storage úrovně Premium** je aktuálně **nepodporuje** podle doplňovacích objektů BLOB.
> - **Úložiště ve virtuální síti** je aktuálně **nepodporuje**.

## <a id="subheading-8"></a>Definovat na úrovni serveru oproti zásady auditování na úrovni databáze

Zásady auditu mohou být definovány pro konkrétní databázi nebo jako výchozí server zásady:

- Server zásady platí pro všechny stávající a nově vytvořenou databázi na serveru.

- Pokud *je povolené auditování objektů blob serveru*, ho *vždy platí pro databáze*. Databáze se dají auditovat, bez ohledu na nastavení auditování databáze.

- Když povolíte auditování objektů blob v databázi, kromě povolení na serveru, nemá *není* přepsat nebo změnit nastavení auditování objektů blob serveru. Obě audity budou existovat vedle sebe. Jinými slovy databáze se Audituje dvakrát paralelně; jednou tak, že server zásad a jednou sadou zásad databáze.

   > [!NOTE]
   > Měli byste se vyhnout, povolení auditování objektů blob serveru i auditování databáze objektů blob společně, pokud:
    > - Chcete použít jinou *účtu úložiště* nebo *dobu uchování* konkrétní databáze.
    > - Chcete auditovat typy událostí nebo kategorie pro určitou databázi, která se liší od ostatních databází na serveru. Například můžete mít vloží tabulky, které musí být auditován jenom pro konkrétní databáze.
   >
   > Jinak doporučujeme, abyste povolili auditování objektů blob jenom úroveň serveru a ponechat databáze auditu zakázané pro všechny databáze.

## <a id="subheading-2"></a>Nastavení auditování databáze

Následující část popisuje konfigurace auditování pomocí webu Azure portal.

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Přejděte do **auditování** v záhlaví zabezpečení v podokně serveru/databáze SQL.

    <a id="auditing-screenshot"></a> ![Navigační podokno][1]

3. Pokud chcete nastavit zásady auditu serveru, můžete vybrat **zobrazit nastavení serveru** odkaz na stránce auditování databáze. Můžete poté zobrazit nebo upravit nastavení auditování serveru. Zásady auditování serveru se vztahují na všechny stávající a nově vytvořené databáze na tomto serveru.

    ![Navigační podokno][2]

4. Pokud chcete povolit auditování na úrovni databáze, přepněte **auditování** k **ON**.

    Pokud je povolené auditování serveru, audit databáze konfigurována bude existovat vedle sebe s auditu serveru.

    ![Navigační podokno][3]

5. **Nové** – Teď máte několik možností pro konfiguraci kam se budou zapisovat protokoly auditu. Protokoly můžete napsat do účtu služby Azure storage, s pracovním prostorem OMS k využití v Log Analytics nebo centra událostí pro využití v Centru událostí. Můžete nakonfigurovat libovolnou kombinaci těchto možností a protokoly auditu se zapíšou do každého.

    ![možnosti úložiště](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Konfigurace auditování pro zápis protokolů na účet úložiště, vyberte **úložiště** a otevřete **podrobnosti o úložišti**. Vyberte účet úložiště Azure, kde se uloží protokoly a pak vyberte dobu uchování. Staré protokoly odstraní. Pak klikněte na **OK**.

    ![účet úložiště](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Konfigurace auditování pro zápis protokolů s pracovním prostorem OMS, vyberte **Log Analytics (Náhled)** a otevřete **Log Analytics podrobnosti**. Vyberte nebo vytvořte pracovní prostor OMS, kde budou zapsány protokoly a potom klikněte na **OK**.

    ![OMS](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Konfigurace auditování pro zápis protokolů do centra událostí, vyberte **centra událostí (Preview)** a otevřete **podrobnosti o Centru událostí**. Vyberte Centrum událostí, kde budou zapsány protokoly a potom klikněte na **OK**. Ujistěte se, že Centrum událostí je ve stejné oblasti jako databáze a serveru.

    ![Centrum událostí](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Klikněte na **Uložit**.
10. Pokud chcete přizpůsobit auditované události, můžete udělat prostřednictvím [rutin prostředí PowerShell](#subheading-7) nebo [rozhraní REST API](#subheading-9).
11. Po dokončení konfigurace nastavení auditu, můžete zapnout nové funkce detekce hrozeb a konfigurovat také e-maily přijímat výstrahy zabezpečení. Při použití detekce hrozeb dostávat proaktivní výstrahy na neobvyklé databázové aktivity, které můžete značí potenciální bezpečnostní hrozby. Další informace najdete v tématu [Začínáme s detekcí hrozeb](sql-database-threat-detection-get-started.md).

## <a id="subheading-3"></a>Analýza protokolů auditu a sestavy

Pokud jste zvolili pro zápis protokolů auditu do Log Analytics:

- Použití [webu Azure portal](https://portal.azure.com).  Otevřete příslušnou databázi. V horní části databáze **auditování** klikněte na **zobrazit protokoly auditu**.

    ![Zobrazení protokolů auditování](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Potom kliknutím na **otevřete v OMS** v horní části **záznamy auditu** stránka se otevře zobrazení protokolů v Log Analytics, ve kterém můžete upravit časový rozsah a vyhledávací dotaz.

    ![Otevřít v OMS](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Alternativně můžete také přistupovat protokolů auditu v okně Log Analytics. Otevření pracovního prostoru Log Analytics a v části **Obecné** klikněte na tlačítko **protokoly**. Můžete začít s jednoduchého dotazu, jako například: *hledání "SQLSecurityAuditEvents"* zobrazíte auditování protokoluje.
    Z tohoto místa můžete také použít [Operations Management Suite (OMS) Log Analytics](../log-analytics/log-analytics-log-search.md) ke spuštění rozšířené hledání na data protokolů auditu. Log Analytics nabízí provozní informace v reálném čase pomocí integrovaného vyhledávání a vlastních řídicích panelů, díky kterým můžete analyzovat miliony záznamů napříč vašimi úlohami a servery. Další užitečné informace o vyhledávací jazyk OMS Log Analytics a příkazy najdete v tématu [referenční příručce k vyhledávání Log Analytics](../log-analytics/log-analytics-log-search.md).

Pokud jste zvolili pro zápis protokolů auditu do centra událostí:

- Chcete-li využívají data protokolů auditu z centra událostí, je potřeba nastavit, aby datový proud zpracování událostí a jejich zápisu do cílového. Další informace najdete v tématu [dokumentace k Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).

Pokud jste zvolili pro zápis protokolů auditu do účtu služby Azure storage, existuje několik metod, které lze použít k zobrazení protokolů:

- Protokoly auditu se agregují v účtu, který jste zvolili během instalace. Protokoly auditu můžete prozkoumat pomocí nástroje [Průzkumníka služby Azure Storage](http://storageexplorer.com/). Ve službě Azure storage, se ukládají protokoly auditování jako kolekce souborů, objektů blob v kontejneru s názvem **sqldbauditlogs**. Další podrobnosti o hierarchii složky úložiště konvence pojmenování a formát protokolu, najdete v článku [odkaz na formát protokolu auditování objektů Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Použití [webu Azure portal](https://portal.azure.com).  Otevřete příslušnou databázi. V horní části databáze **auditování** klikněte na **zobrazit protokoly auditu**.

    ![Navigační podokno][7]

    **Záznamy auditu** se otevře, ze kterých budete mít k zobrazení protokolů.

  - Kliknutím si můžete zobrazit konkrétní kalendářní data **filtr** v horní části **záznamy auditu** stránky.
  - Můžete přepínat mezi záznamy auditu, které byly vytvořeny *zásady auditu serveru* a *databázi zásady auditu* přepnutím **auditovat zdroj**.
  - Můžete zobrazit pouze prostřednictvím injektáže SQL související záznamy auditu tak, že zkontrolujete **zobrazit jenom záznamy pro injektáže SQL auditu** zaškrtávací políčko.

       ![Navigační podokno][8]

- Použít funkci systému **sys.fn_get_audit_file** (T-SQL) vrátit data protokolu auditování v tabulkovém formátu. Další informace o použití této funkce najdete v tématu [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Použití **sloučit soubory auditu** v SQL Server Management Studio (od verze aplikace SSMS 17):
    1. Vyberte z nabídky aplikace SSMS **souboru** > **otevřít** > **sloučit soubory auditu**.

        ![Navigační podokno][9]
    2. **Přidat soubory auditu** zobrazí se dialogové okno. Vyberte jednu z **přidat** možnosti a vyberte, jestli se má sloučit soubory auditu z místního disku nebo importovat ze služby Azure Storage. Je potřeba zadat podrobnosti o Azure Storage a klíč účtu.

    3. Po přidání všech souborů ke sloučení klikněte na tlačítko **OK** k dokončení operace sloučení.

    4. Sloučený soubor se otevře v SSMS, kde je můžete zobrazit a analyzovat, jakož i exportovat do souboru XEL nebo sdíleného svazku clusteru nebo na tabulku.

- Použití Power BI. Můžete zobrazit a analyzovat data protokolů auditu v Power BI. Další informace a pro přístup k šabloně ke stažení najdete na stránce [Analyzie data protokolu auditování v Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Stáhněte si soubory protokolů z vašeho kontejneru objektů blob v Azure Storage prostřednictvím portálu nebo pomocí nástroje [Průzkumníka služby Azure Storage](http://storageexplorer.com/).
  - Po stažení souboru protokolu místně, poklikejte na soubor otevřít, zobrazení a analýza protokolů v aplikaci SSMS.
  - Můžete také stáhnout více souborů najednou pomocí Průzkumníka služby Azure Storage. Chcete-li to provést, klikněte pravým tlačítkem na konkrétní podsložku a vyberte **uložit jako** uložit do místní složky.

- Další metody:

  - Po stažení několik souborů nebo na podsložku, která obsahuje soubory protokolu, sloučit je místně, jak je popsáno v SSMS sloučení auditu soubory pokynů je popsáno výše.
  - Auditování objektů blob k zobrazení protokolů prostřednictvím kódu programu:

    - Použití [rozšířené události čtečky](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) knihovny v jazyce C#.
    - [Dotazování rozšířené události soubory](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) pomocí prostředí PowerShell.

## <a id="subheading-5"></a>Postupy v produkčním prostředí

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditování geograficky replikované databáze</a>

S geograficky replikované databáze když povolíte auditování u primární databáze sekundární databáze mají shodné zásady auditování. Je také možné nastavit auditování v sekundární databázi povolením auditování **sekundární server**, nezávisle na primární databázi.

- Úroveň serveru (**doporučuje**): zapněte auditování na obou **primární server** i na **sekundární server** – primární a sekundární databáze bude každý auditovat nezávisle na sobě podle jejich příslušné zásady na úrovni serveru.
- Úrovni databáze: Databáze úrovni auditování u sekundárních databází se dá nakonfigurovat jenom z nastavení auditování primární databáze.
  - Auditování musí být povolené na *primární samotná databáze*, nikoli na server.
  - Jakmile u primární databáze je povolené auditování, se také zpřístupní v sekundární databázi.

    >[!IMPORTANT]
    >Díky auditování na úrovni databáze, nastavení úložiště pro sekundární databáze bude stejné jako primární databáze, způsobí přenos mezi zónami. Doporučujeme, abyste povolili auditování jenom úroveň serveru a nechte auditování databáze zakázané pro všechny databáze.

### <a id="subheading-6">Opětovném generování přístupového klíče úložiště</a>

V produkčním prostředí budete pravděpodobně pravidelně aktualizují vaše klíče úložiště. Při zápisu protokoly auditu do služby Azure storage, budete muset znovu zásady auditování při aktualizacích klíče. Probíhá takto:

1. Otevřít **podrobnosti o úložišti**. V **přístupový klíč k úložišti** vyberte **sekundární**a klikněte na tlačítko **OK**. Pak klikněte na tlačítko **Uložit** v horní části stránky konfigurace auditování.

    ![Navigační podokno][5]
2. Přejděte na stránku konfigurace úložiště a znovu vygenerovat primární přístupový klíč.

    ![Navigační podokno][6]
3. Přejděte zpět na stránku Konfigurace auditování přepnout ze sekundární do primární přístupový klíč úložiště a pak klikněte na tlačítko **OK**. Pak klikněte na tlačítko **Uložit** v horní části stránky konfigurace auditování.
4. Přejděte zpět na stránku konfigurace úložiště a znovu vygenerovat sekundární přístupový klíč (při přípravě další klávesu cyklu aktualizace).

## <a name="additional-information"></a>Další informace

- Podrobné informace o protokolu formátování, hierarchie složky úložiště a zásady vytváření názvů, najdete v článku [odkaz na formát protokolu auditování objektů Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Auditování Azure SQL Database ukládá 4000 znaků data pro znak pole v záznamu auditu. Když **příkaz** nebo **data_sensitivity_information** hodnoty vrácené určitou akcí auditovatelných obsahovat více než 4 000 znaků, budou všechna data nad rámec nejprve 4000 znaků  **zkrátí a ne auditovat**.

- Protokoly auditu se zapisují do **doplňovací objekty BLOB** ve službě Azure Blob storage v rámci předplatného Azure:
  - **Storage úrovně Premium** je aktuálně **nepodporuje** podle doplňovacích objektů BLOB.
  - **Úložiště ve virtuální síti** je aktuálně **nepodporuje**.

- Výchozí zásady auditu obsahuje všechny akce a následující sadu skupin akcí, které bude auditovat dotazů a uložených procedur provedených vůči databáze, jakož i úspěšných a neúspěšných přihlášení:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Můžete nakonfigurovat auditování pro různé typy akcí a skupiny akcí pomocí Powershellu, jak je popsáno v [spravovat auditování služby SQL database pomocí prostředí Azure PowerShell](#subheading-7) oddílu.

## <a id="subheading-7"></a>Spravovat auditování služby SQL database pomocí prostředí Azure PowerShell

**Rutiny Powershellu**:

- [Vytvořit nebo aktualizovat objekt Blob databáze auditování zásad (Set-AzureRMSqlDatabaseAuditing)][105]
- [Vytvořit nebo aktualizovat objekt Blob Server auditování zásad (Set-AzureRMSqlServerAuditing)][106]
- [Získat databázi zásady auditu (Get-AzureRMSqlDatabaseAuditing)][101]
- [Získat (Get-AzureRMSqlServerAuditing) objektů Blob serveru zásady auditu][102]

Ukázkový skript, naleznete v tématu [konfigurace auditování a detekce hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Spravovat auditování služby SQL database pomocí rozhraní REST API

**Rozhraní REST API – auditování objektů Blob**:

- [Vytvořit nebo aktualizovat objekt Blob databázi zásady auditu](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/databaseblobauditingpolicies_createorupdate)
- [Vytvořit nebo aktualizovat objekt Blob Server zásady auditu](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/serverblobauditingpolicies_createorupdate)
- [Získat zásady auditování objektů Blob v databázi](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/databaseblobauditingpolicies_get)
- [Získat zásady auditování objektů Blob v serveru](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/serverblobauditingpolicies_get)

Rozšířené zásady pomocí kde klauzule podpory pro další filtrování:

- [Vytvořit nebo aktualizovat databázi *rozšířené* zásady auditování objektů Blob](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/extendeddatabaseblobauditingpolicies_createorupdate)
- [Vytvořit nebo aktualizovat Server *rozšířené* zásady auditování objektů Blob](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/serverblobauditingpolicies_createorupdate)
- [Získat databázi *rozšířené* zásady auditování objektů Blob](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/extendeddatabaseblobauditingpolicies_get)
- [Získat Server *rozšířené* zásady auditování objektů Blob](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/serverblobauditingpolicies_get)

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
