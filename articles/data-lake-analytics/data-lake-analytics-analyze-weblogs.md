---
title: Analýza webových protokolů pomocí Azure Data Lake Analytics
description: Naučte se analyzovat webové protokoly pomocí Azure Data Lake Analytics pro spouštění funkcí a dotazů U-SQL.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 5ad837c51fafd7df2b019c78ae4adaf4967f2df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132632"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analýza webových protokolů pomocí Azure Data Lake Analytics
Naučte se analyzovat webové protokoly pomocí Data Lake Analytics, zejména v tématu zjištění, které odhlašovací servery při pokusu o návštěvě webu narazily na chyby.

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2015 nebo Visual Studio 2013**.
* **[Nástroje Data Lake pro Visual Studio](https://aka.ms/adltoolsvs)**.

    Po nainstalování Data Lakech nástrojů pro Visual Studio se zobrazí položka **Data Lake** v nabídce **nástroje** v aplikaci Visual Studio:

    ![Nabídka U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Základní znalost Data Lake Analytics a data Lake nástrojů pro Visual Studio**. Informace o tom, jak začít, najdete v těchto tématech:

  * [Vývoj skriptu U-SQL pomocí data Lakech nástrojů pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* **Účet Data Lake Analytics.**  Viz [Vytvoření účtu Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Nainstalujte ukázková data.** Na webu Azure Portal otevřete Data Lake Analytics účet a v levé nabídce klikněte na **ukázkové skripty** a pak klikněte na **Kopírovat ukázková data**. 

## <a name="connect-to-azure"></a>Připojení k Azure
Než budete moct sestavit a otestovat skripty U-SQL, musíte se nejdřív připojit k Azure.

### <a name="to-connect-to-data-lake-analytics"></a>Postup připojení k Data Lake Analytics

1. Otevřete sadu Visual Studio.
2. Klikněte na možnost **Data Lake > možnosti a nastavení**.
3. Klikněte na **Přihlásit**, nebo **změňte uživatele** , pokud se někdo přihlásil, a postupujte podle pokynů.
4. Kliknutím na tlačítko **OK** zavřete dialogové okno Možnosti a nastavení.

### <a name="to-browse-your-data-lake-analytics-accounts"></a>Procházení účtů Data Lake Analytics

1. V aplikaci Visual Studio otevřete **Průzkumník serveru** stisknutím **kombinace kláves CTRL + ALT + S**.
2. V **Průzkumníku serveru** rozbalte položku **Azure** a pak rozbalte položku **Data Lake Analytics**. Zobrazí se seznam účtů Data Lake Analytics, pokud nějaké máte. Z studia nemůžete vytvářet účty Data Lake Analytics. Pokud chcete vytvořit účet, přečtěte si téma Začínáme [s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md) nebo [Začínáme s Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Vývoj aplikace U-SQL
Aplikace U-SQL je většinou skript U-SQL. Další informace o U-SQL najdete v tématu [Začínáme s jazykem u-SQL](data-lake-analytics-u-sql-get-started.md).

Do aplikace můžete přidat přidání uživatelem definovaných operátorů.  Další informace najdete v tématu [vývoj uživatelem definovaných operátorů u-SQL pro úlohy Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

### <a name="to-create-and-submit-a-data-lake-analytics-job"></a>Postup vytvoření a odeslání úlohy Data Lake Analytics

1. Klikněte na **soubor > nový > projekt**.

2. Vyberte typ Projekt U-SQL.

   ![Nový projekt U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klikněte na **OK**. Visual Studio vytvoří řešení se souborem Script. usql.

4. Do souboru Script. usql zadejte následující skript:

   ```usql
   // Create a database for easy reuse, so you don't need to read from a file very time.
   CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

   // Create a Table valued function. TVF ensures that your jobs fetch data from he weblog file with the correct schema.
   DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
   CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
   RETURNS @result TABLE
   (
       s_date DateTime,
       s_time string,
       s_sitename string,
       cs_method string,
       cs_uristem string,
       cs_uriquery string,
       s_port int,
       cs_username string,
       c_ip string,
       cs_useragent string,
       cs_cookie string,
       cs_referer string,
       cs_host string,
       sc_status int,
       sc_substatus int,
       sc_win32status int,
       sc_bytes int,
       cs_bytes int,
       s_timetaken int
   )
   AS
   BEGIN

       @result = EXTRACT
           s_date DateTime,
           s_time string,
           s_sitename string,
           cs_method string,
           cs_uristem string,
           cs_uriquery string,
           s_port int,
           cs_username string,
           c_ip string,
           cs_useragent string,
           cs_cookie string,
           cs_referer string,
           cs_host string,
           sc_status int,
           sc_substatus int,
           sc_win32status int,
           sc_bytes int,
           cs_bytes int,
           s_timetaken int
       FROM @"/Samples/Data/WebLog.log"
       USING Extractors.Text(delimiter:' ');
       RETURN;
   END;

   // Create a table for storing referrers and status
   DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
   @weblog = SampleDBTutorials.dbo.WeblogsView();
   CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
   (
       INDEX idx1
       CLUSTERED(Year ASC)
       DISTRIBUTED BY HASH(Year)
   ) AS

   SELECT s_date.Year AS Year,
       s_date.Month AS Month,
       s_date.Day AS Day,
       cs_referer,
       sc_status,
       COUNT(DISTINCT c_ip) AS cnt
   FROM @weblog
   GROUP BY s_date,
           cs_referer,
           sc_status;
   ```

    Další informace o U-SQL najdete v tématu [Začínáme s jazykem Data Lake Analytics u-SQL](data-lake-analytics-u-sql-get-started.md).

5. Přidejte do projektu nový skript U-SQL a zadejte následující:

   ```usql
   // Query the referrers that ran into errors
   @content =
       SELECT *
       FROM SampleDBTutorials.dbo.ReferrersPerDay
       WHERE sc_status >=400 AND sc_status < 500;

   OUTPUT @content
   TO @"/Samples/Outputs/UnsuccessfulResponses.log"
   USING Outputters.Tsv();
   ```

6. Přepněte zpátky na první skript U-SQL a vedle tlačítka **Odeslat** zadejte svůj účet Analytics.

7. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na možnost **Sestavit skript**. Ověřte výsledky v podokně výstup.

8. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na položku **Odeslat skript**.

9. Ověřte, že **účet Analytics** je ten, na kterém chcete úlohu spustit, a pak klikněte na **Odeslat**. Jakmile je odeslání hotové, v okně Výsledky nástrojů Data Lake pro Visual Studio jsou dostupné výsledky odeslání a odkaz na úlohu.

10. Počkejte, až se úloha úspěšně dokončí.  Pokud se úloha nezdařila, pravděpodobně chybí zdrojový soubor.  Přečtěte si část požadavky v tomto kurzu. Další informace o řešení potíží najdete v tématu [monitorování a odstraňování potíží s Azure Data Lake Analytics úlohami](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Po dokončení úlohy se zobrazí následující obrazovka:

    ![Analýza webových protokolů v protokolech služby Data Lake Analytics](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Nyní zopakujte kroky 7-10 pro **Script1. usql**.

### <a name="to-see-the-job-output"></a>Postup zobrazení výstupu úlohy

1. V **Průzkumníku serveru** rozbalte položku **Azure**, rozbalte položku **Data Lake Analytics**, rozbalte účet Data Lake Analytics, rozbalte položku **Účty Storage**, klikněte pravým tlačítkem na výchozí účet Data Lake Storage a potom klikněte na položku **Explorer**.
2. Dvojím kliknutím na **ukázky** otevřete složku a potom poklikejte na **výstupy**.
3. Dvakrát klikněte na **UnsuccessfulResponses. log**.
4. Můžete také dvakrát kliknout na výstupní soubor v zobrazení grafu úlohy, aby bylo možné přejít přímo na výstup.

## <a name="next-steps"></a>Další kroky
Pokud chcete začít s Data Lake Analytics pomocí různých nástrojů, projděte si témata:

* [Začínáme s Data Lake Analytics pomocí Portálu Azure](data-lake-analytics-get-started-portal.md)
* [Začínáme s Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí sady .NET SDK](data-lake-analytics-get-started-net-sdk.md)
