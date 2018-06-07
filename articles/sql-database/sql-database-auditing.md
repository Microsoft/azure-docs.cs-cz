---
title: Začínáme s auditování databáze Azure SQL | Microsoft Docs
description: Auditování databáze Azure SQL použijte ke sledování databáze události do protokolu auditování.
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: giladm
ms.openlocfilehash: 62c6e576a5ef7fa953618dcba4ddd158df131148
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644585"
---
# <a name="get-started-with-sql-database-auditing"></a>Začínáme s auditem databáze SQL
Auditování databáze SQL Azure sleduje události databáze a zápisu, které mají auditu přihlášení účtu úložiště Azure. Auditování také:

* Pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které by mohly být známkou problémů obchodního charakteru nebo by mohly vzbuzovat podezření narušení zabezpečení.

* Umožňuje a zjednodušuje dodržování standardů dodržování předpisů, i když není zárukou toho, dodržování předpisů. Další informace o Azure programy dodržování standardů tuto podporu, najdete v části [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Databáze SQL Azure auditování – přehled
Můžete použít auditování databáze SQL pro:


* **Zachovat** monitorovat vybrané události. Můžete definovat kategorie akce databáze k auditování.
* **Sestava** v databázové aktivitě. Abyste mohli rychle začít s aktivity a události vytváření sestav můžete předem nakonfigurované sestavy a řídicí panel.
* **Analýza** sestavy. Můžete najít podezřelé události, neobvyklé aktivity a trendy.

Můžete nakonfigurovat auditování pro různé typy událostí kategorie, jak je popsáno v [nastavit auditování pro databázi](#subheading-2) části.

> [!IMPORTANT]
> Protokoly auditu se zapisují do **doplňovacích objektů blob** v úložišti objektů Blob v Azure na vaše předplatné Azure.
>
> * **Storage úrovně Premium** právě **nepodporuje** podle doplňovacích objektů BLOB.
> * **Úložiště ve virtuální síti** právě **nepodporuje**.

## <a id="subheading-8"></a>Definovat úroveň serveru oproti auditování zásady na úrovni databáze

Zásady auditu je možné definovat pro konkrétní databázi nebo jako výchozí zásady serveru:

* Server zásady platí pro všechny stávající a nově vytvořené databáze na serveru.

* Pokud *auditování objektů blob serveru zapnutá*, ho *vždy aplikuje na databázi*. Databáze bude auditovat, bez ohledu na nastavení auditování databáze.

* Povolení auditování objektů blob v databázi, kromě povolení na serveru nemá *není* přepsat nebo změnit libovolné nastavení auditování serveru objektů blob. Obě audity, budou existovat vedle sebe. Jinými slovy je databáze auditováno dvakrát paralelně; jednou zásad serveru a jednou zásadami databáze.

   > [!NOTE]
   > Povolení auditování objektů blob serveru i auditování databáze blob společně, pokud byste neměli:
    > * Chcete použít jiné *účet úložiště* nebo *dobu uchování* pro konkrétní databázi.
    > * Chcete auditovat typů událostí nebo kategorie pro konkrétní databázi, která se liší od zbytku databáze na serveru. Například můžete mít vložení tabulky, které je nutné auditovat jenom pro konkrétní databázi.
   >
   > Jinak doporučujeme povolit auditování objektů blob jenom úrovni serveru a nechte úroveň databáze auditování zakázáno pro všechny databáze.


## <a id="subheading-2"></a>Nastavení auditování pro vaši databázi
Následující část popisuje konfigurace auditování pomocí portálu Azure.

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Přejděte na **nastavení** okno chcete auditovat serveru SQL database nebo SQL Server. V **nastavení** vyberte **auditování a detekce hrozeb**.

    <a id="auditing-screenshot"></a> ![Navigační podokno][1]
3. Pokud chcete nastavit zásady auditu serveru, můžete vybrat **zobrazit nastavení serveru** odkaz v okně auditování databáze. Můžete pak zobrazit nebo upravit nastavení auditování serveru. Zásady auditu serveru platí pro všechny stávající a nově vytvořené databáze na tomto serveru.

    ![Navigační podokno][2]
4. Pokud chcete povolit auditování objektů blob na úrovni databáze pro **auditování**, vyberte **ON**a pro **auditování typ**, vyberte **Blob**.

    Pokud je auditování objektů blob serveru je povoleno, budou existovat audit databáze konfigurována node souběžně s auditování objektů blob serveru.

    ![Navigační podokno][3]
5. Chcete-li otevřít **úložiště protokolů auditu** vyberte **podrobnosti úložiště**. Vyberte účet úložiště Azure, kde bude uložena protokoly a pak vyberte dobu uchování. Starých protokolů se odstraní. Pak klikněte na **OK**.
    >[!TIP]
    >K plnému využití mimo auditování šablon sestav, použijte stejný účet úložiště pro všechny auditování databáze.

    <a id="storage-screenshot"></a> ![Navigační podokno][4]
6. Pokud chcete přizpůsobit auditované události, můžete k tomu prostřednictvím [rutiny prostředí PowerShell](#subheading-7) nebo [REST API](#subheading-9).
7. Po dokončení konfigurace nastavení auditování, můžete zapnout funkci nové detekce hrozeb a konfigurovat výstrahy zabezpečení e-mailů. Pokud používáte detekce hrozeb, obdržíte proaktivní výstrahy na nezvyklé databázové aktivity, které může znamenat potenciální bezpečnostní hrozby. Další informace najdete v tématu [Začínáme s detekce hrozeb](sql-database-threat-detection-get-started.md).
8. Klikněte na **Uložit**.





## <a id="subheading-3"></a>Analýza protokolů auditu a sestavy
Protokoly auditu je agregován v účtu úložiště Azure, které jste zvolili během instalace. Protokoly auditu můžete prozkoumat pomocí nástroje, jako například [Azure Storage Explorer](http://storageexplorer.com/).

Protokoly auditování objektů BLOB jsou uloženy jako kolekce souborů, objektů blob do kontejneru, s názvem **sqldbauditlogs**.

Další podrobnosti o hierarchii složky úložiště zásady vytváření názvů a formát protokolu najdete [odkaz formátu protokolu auditu objekt Blob](https://go.microsoft.com/fwlink/?linkid=829599).

Existuje několik metod, které můžete použít k zobrazení protokolů auditování objektů blob:

* Použití [portál Azure](https://portal.azure.com).  Otevře příslušné databáze. V horní části této databáze **auditování a detekce hrozeb** okně klikněte na tlačítko **zobrazit protokoly auditu**.

    ![Navigační podokno][7]

    **Audit záznamy** otevře se okno, z nichž budete moci zobrazit protokoly.

    - Kliknutím můžete zobrazit konkrétní kalendářní data **filtru** v horní části **Audit záznamy** okno.
    - Můžete přepínat mezi záznamy auditu, které byly vytvořeny auditu pro zásady zásady nebo databázi serveru.

       ![Navigační podokno][8]

* Pomocí funkce systému **sys.fn_get_audit_file** (T-SQL) se vrátit daty protokolu auditování v tabulkovém formátu. Další informace o použití této funkce najdete v tématu [sys.fn_get_audit_file dokumentaci](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Použití **sloučení soubory auditu** v aplikaci SQL Server Management Studio (počínaje SSMS 17):
    1. V nabídce aplikace SSMS vyberte **soubor** > **otevřete** > **sloučení soubory auditu**.

        ![Navigační podokno][9]
    2. **Přidat soubory auditu** otevře se dialogové okno. Vyberte jednu z **přidat** možnost vyberte, zda chcete sloučení auditu soubory z místního disku nebo importovat z Azure Storage. Je nutné zajistit podrobnosti úložiště Azure a klíč účtu.

    3. Po přidání všech souborů, sloučení, klikněte na tlačítko **OK** k dokončení operace sloučení.

    4. Sloučené soubor se otevře v aplikaci SSMS, kde je můžete zobrazit a analyzujte ji, a také ho exportovat do souboru souboru XEL nebo sdíleného svazku clusteru nebo do tabulky.

* Použití [synchronizace aplikace](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) které jsme vytvořili. Jeho spuštění v Azure a využívá analýzy protokolů veřejná rozhraní API, tak, aby nabízel protokoly auditu SQL do analýzy protokolů. Synchronizace aplikace doručí protokoly auditu SQL do analýzy protokolů pro používání prostřednictvím řídicího panelu analýzy protokolů.

* Pomocí Power BI. Můžete zobrazit a analyzovat data protokolu auditování v Power BI. Další informace o [Power BI a přístup ke stažení šablony](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Stáhnout soubory protokolů z vašeho kontejneru objektů blob Azure Storage prostřednictvím portálu nebo pomocí nástroje, jako například [Azure Storage Explorer](http://storageexplorer.com/).
    * Po stažení souboru protokolu, který je místně, můžete dvakrát kliknete na soubor otevřít, zobrazit a analyzovat protokoly v aplikaci SSMS.
    * Můžete také stáhnout víc souborů současně prostřednictvím Azure Storage Explorer. Klikněte pravým tlačítkem na konkrétní podsložku a vyberte **uložit jako** uložení do místní složky.

* Další metody:
   * Po stažení několik souborů nebo na podsložku, který obsahuje soubory protokolu, můžete sloučit je místně jak je popsáno v pokynech soubory auditu sloučení SSMS popsané výše.

   * Auditování objektů blob k zobrazení protokolů prostřednictvím kódu programu:

     * Použití [rozšířené události čtečky](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) knihovna jazyka C#.
     * [Dotaz Rozšířené události soubory](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) pomocí prostředí PowerShell.




## <a id="subheading-5"></a>Provozní postupy
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Auditování geograficky replikované databáze</a>
Geograficky replikované databáze když povolíte auditování v primární databázi sekundární databázi se mají stejné zásady auditu. Je také možné nastavit auditování v sekundární databázi povolením auditování **sekundární server**, nezávisle z primární databáze.

* Úrovni serveru (**doporučená**): zapnout auditování pro obě **primární server** společně s **sekundární server** -primární a sekundární databáze každý auditování nezávisle založené na jejich příslušné zásady na úrovni serveru.

* Úroveň databáze: Databáze úrovni auditování pro sekundární databáze lze konfigurovat pouze v primární databázi, nastavení auditování.
   * Auditování objektů BLOB musí být povolená na *primární databázi, samotné*, nikoli na server.
   * Povolíte auditování objektů blob v primární databázi, bude ho také přístupné v sekundární databázi.

    >[!IMPORTANT]
    >Úroveň databáze auditování, nastavení pro sekundární databázi bude stejné jako primární databáze, způsobuje provoz mezi místní. Doporučujeme, abyste povolili auditování pouze úrovni serveru a nechte úroveň databáze auditování zakázáno pro všechny databáze.
<br>

### <a id="subheading-6">Opětovné generování klíče úložiště</a>
V produkčním prostředí budete pravděpodobně pravidelně aktualizovat klíče úložiště. Při aktualizaci klíče, je nutné k opakovanému uložení zásad auditu. Proces je následující:

1. Otevřete **podrobnosti úložiště** okno. V **přístupový klíč k úložišti** vyberte **sekundární**a klikněte na tlačítko **OK**. Pak klikněte na tlačítko **Uložit** v horní části okna Konfigurace auditování.

    ![Navigační podokno][5]
2. Přejděte do okna konfigurace úložiště a znovu vygenerovat primární přístupový klíč.

    ![Navigační podokno][6]
3. Přejděte zpět do okna auditování konfigurace přepínače ze sekundární pro primární přístupový klíč úložiště a pak klikněte na tlačítko **OK**. Pak klikněte na tlačítko **Uložit** v horní části okna Konfigurace auditování.
4. Přejděte zpět do okna konfigurace úložiště a znovu vygenerovat sekundární přístupový klíč (v rámci přípravy cyklus aktualizace Další klíč).

## <a name="additional-information"></a>Další informace

* Podrobnosti o protokol formátu, hierarchie složky úložiště a konvence vytváření názvů, najdete v článku [odkaz formátu protokolu auditu objekt Blob](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL Database Audit ukládá 4000 znaků dat pro pole znaků v záznam auditu. Když **příkaz** nebo **data_sensitivity_information** hodnot vrácených z kontrolovatelný akce obsahovat více než 4 000 znaků, bude veškerá data větší než první 4 000 znaků  **zkrácené a není auditovat**.

* Protokoly auditu se zapisují do **doplňovacích objektů blob** v úložišti objektů Blob v Azure na vaše předplatné Azure:
    * **Storage úrovně Premium** právě **nepodporuje** podle doplňovacích objektů BLOB.
    * **Úložiště ve virtuální síti** právě **nepodporuje**.

* Výchozí zásady auditování zahrnuje všechny akce a následující sadu skupin akce, které bude auditovat všechny dotazy a uložených procedur provedených proti databázi, jakož i úspěšná a neúspěšná přihlášení:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Můžete nakonfigurovat auditování pro různé typy akcí a akce skupiny pomocí prostředí PowerShell, jak je popsáno v [auditování databáze SQL spravovat pomocí Azure PowerShell](#subheading-7) části.

## <a id="subheading-7"></a>Spravovat auditování databáze SQL pomocí Azure PowerShell

**Rutiny prostředí PowerShell**:

* [Vytvořit nebo aktualizovat databázi Blob auditování zásady (Set-AzureRMSqlDatabaseAuditing)][105]
* [Vytvořit nebo aktualizovat Server Blob auditování zásady (Set-AzureRMSqlServerAuditing)][106]
* [Získat zásady auditování databáze (Get-AzureRMSqlDatabaseAuditing)][101]
* [Získání objektu Blob serveru zásady auditování (Get-AzureRMSqlServerAuditing)][102]

Příklad skriptu najdete v tématu [konfigurace auditování a zjišťování hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Spravovat auditování databáze SQL pomocí rozhraní REST API

**REST API – auditování objektů Blob**:

* [Vytvořit nebo aktualizovat zásady auditování Blob databáze](https://msdn.microsoft.com/library/azure/mt695939.aspx)
* [Vytvořit nebo aktualizovat Server Blob zásady auditování](https://msdn.microsoft.com/library/azure/mt771861.aspx)
* [Získání objektu Blob databáze zásady auditování](https://msdn.microsoft.com/library/azure/mt695938.aspx)
* [Získání objektu Blob serveru zásady auditování](https://msdn.microsoft.com/library/azure/mt771860.aspx)
* [Získání objektu Blob serveru auditování výsledek operace](https://msdn.microsoft.com/library/azure/mt771862.aspx)


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
