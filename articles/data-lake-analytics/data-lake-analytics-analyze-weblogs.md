---
title: Analýza webových protokolů pomocí Azure Data Lake Analytics
description: Zjistěte, jak analyzovat protokoly webů pomocí Azure Data Lake Analytics ke spuštění funkcí A DOTAZŮ U-SQL.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672882"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analýza webových protokolů pomocí Azure Data Lake Analytics
Zjistěte, jak analyzovat protokoly webových stránek pomocí data lake analytics, zejména na zjištění, které referrers narazili na chyby, když se pokusili navštívit webové stránky.

## <a name="prerequisites"></a>Požadavky
* **Visual Studio 2015 nebo Visual Studio 2013**.
* **[Nástroje Data Lake pro Visual Studio](https://aka.ms/adltoolsvs)**.

    Po instalaci nástrojů data lake pro Visual Studio se v nabídce **Nástroje** v sadě Visual Studio zobrazí položka **Datové jezero:**

    ![Nabídka U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Základní znalosti Data Lake Analytics a Nástrojů data lake pro Visual Studio**. Chcete-li začít, přečtěte si:

  * [Vývoj skriptu U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Účet Data Lake Analytics.**  Viz [Vytvoření účtu Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Nainstalujte ukázková data.** Na Portálu Azure otevřete účet Data Lake Analytics a v levé nabídce klikněte na **Ukázkové skripty** a potom klikněte na **Kopírovat ukázková data**. 

## <a name="connect-to-azure"></a>Připojení k Azure
Než budete moci vytvářet a testovat všechny skripty U-SQL, musíte se nejprve připojit k Azure.

**Postup připojení k Data Lake Analytics**

1. Otevřete sadu Visual Studio.
2. Klepněte na **položku Možnosti a nastavení > datového jezera**.
3. Klikněte na **Přihlásit**se nebo **Změnit uživatele,** pokud se někdo přihlásil, a postupujte podle pokynů.
4. Klepnutím na **tlačítko OK** zavřete dialogové okno Možnosti a nastavení.

**Procházení účtů Data Lake Analytics**

1. V sadě Visual Studio otevřete **Průzkumníka serveru** stisknutím **kláves CTRL+ALT+S**.
2. V **Průzkumníku serveru** rozbalte položku **Azure** a pak rozbalte položku **Data Lake Analytics**. Zobrazí se seznam účtů Data Lake Analytics, pokud nějaké máte. Účty Data Lake Analytics nelze vytvářet ze studia. Pokud si chcete vytvořit účet, [přečtěte si, že začínáme s Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-get-started-portal.md) nebo [Začínáme s Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Vývoj aplikace U-SQL
U-SQL aplikace je většinou U-SQL skript. Další informace o U-SQL najdete [v tématu Začínáme s U-SQL](data-lake-analytics-u-sql-get-started.md).

Do aplikace můžete přidat další uživatelem definované operátory.  Další informace naleznete v [tématu Vývoj uživatelem definovaných operátorů U-SQL pro úlohy Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Postup vytvoření a odeslání úlohy Data Lake Analytics**

1. Klepněte na **soubor > nový > project**.
2. Vyberte typ Projekt U-SQL.

    ![Nový projekt U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Klikněte na tlačítko **OK**. Visual Studio vytvoří řešení se souborem Script.usql.
4. Do souboru Script.usql zadejte následující skript:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
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

    Informace o porozumění u-SQL najdete [v tématu Začínáme s jazykem U-SQL analýzy datového jezera](data-lake-analytics-u-sql-get-started.md).    
5. Přidejte do projektu nový skript U-SQL a zadejte následující:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Přepněte zpět na první skript U-SQL a vedle tlačítka **Odeslat** zadejte svůj účet Analytics.
7. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na možnost **Sestavit skript**. Ověřte výsledky v podokně Výstup.
8. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na položku **Odeslat skript**.
9. Ověřte, zda je **účet Analytics** ten, ve kterém chcete úlohu spustit, a klepněte na tlačítko **Odeslat**. Jakmile je odeslání hotové, v okně Výsledky nástrojů Data Lake pro Visual Studio jsou dostupné výsledky odeslání a odkaz na úlohu.
10. Počkejte, dokud nebude úloha úspěšně dokončena.  Pokud se úloha nezdařila, pravděpodobně chybí zdrojový soubor.  Naleznete v části Požadavky tohoto kurzu. Další informace o řešení potíží najdete [v tématu Sledování a řešení potíží s úlohami Azure Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Po dokončení úlohy se zobrazí následující obrazovka:

    ![data lake analytics analyzovat weblogy webové záznamy](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Nyní opakujte kroky 7- 10 pro **Script1.usql**.

**Postup zobrazení výstupu úlohy**

1. V **Průzkumníku serveru** rozbalte položku **Azure**, rozbalte položku **Data Lake Analytics**, rozbalte účet Data Lake Analytics, rozbalte položku **Účty Storage**, klikněte pravým tlačítkem na výchozí účet Data Lake Storage a potom klikněte na položku **Explorer**.
2. Poklepáním na **ukázky** otevřete složku a poklepejte na **výstupy**.
3. Poklepejte na **soubor UnsuccessfulResponses.log**.
4. Můžete také poklepat na výstupní soubor uvnitř zobrazení grafu úlohy, abyste mohli přejít přímo na výstup.

## <a name="see-also"></a>Viz také
Pokud chcete začít s Data Lake Analytics pomocí různých nástrojů, projděte si témata:

* [Začínáme s Data Lake Analytics pomocí Portálu Azure](data-lake-analytics-get-started-portal.md)
* [Začínáme s Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí sady .NET SDK](data-lake-analytics-get-started-net-sdk.md)
