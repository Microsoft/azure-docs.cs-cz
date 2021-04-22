---
title: Použití nástrojů Azure Data Lake pro Visual Studio Code
description: Naučte se používat kód Nástroje Azure Data Lake pro Visual Studio k vytváření, testování a spouštění skriptů U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878573"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Použití nástrojů Azure Data Lake pro Visual Studio Code

V tomto článku se dozvíte, jak můžete pomocí Nástroje Azure Data Lake pro Visual Studio kódu (VS Code) vytvářet, testovat a spouštět skripty U-SQL. Tyto informace jsou také zahrnuty v následujícím videu:

[![Video Player: Azure Data Lake nástroje pro VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Požadavky

Azure Data Lake nástroje pro VS Code podporují systémy Windows, Linux a macOS. Místní spuštění U-SQL a místní ladění funguje pouze v systému Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Pro macOS a Linux:

- [.NET 5,0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Nainstalovat Azure Data Lake nástroje

Po instalaci požadovaných součástí můžete nainstalovat Azure Data Lake nástroje pro VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Instalace nástrojů Azure Data Lake

1. Otevřete Visual Studio Code.
2. V levém podokně vyberte **rozšíření** . Do vyhledávacího pole zadejte **Azure Data Lake nástroje** .
3. Vyberte **instalovat** vedle **Azure Data Lake nástroje**.

   ![Výběry pro instalaci nástrojů Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po několika sekundách se tlačítko **nainstalovat** změní na **znovu načíst**.
4. Vyberte možnost **znovu načíst** a aktivujte rozšíření **Azure Data Lake Tools** .
5. Pro potvrzení vyberte **znovu načíst okno** . **Azure Data Lake nástroje** můžete zobrazit v podokně **rozšíření** .

## <a name="activate-azure-data-lake-tools"></a>Aktivovat Azure Data Lake nástroje

Vytvořte soubor. usql nebo otevřete existující soubor. usql pro aktivaci rozšíření.

## <a name="work-with-u-sql"></a>Práce s U-SQL

Chcete-li pracovat s jazykem U-SQL, je nutné otevřít soubor U-SQL nebo složku.

### <a name="to-open-the-sample-script"></a>Otevření ukázkového skriptu

Otevřete paletu příkazů (CTRL + SHIFT + P) a zadejte **ADL: Open Sample Script**. Otevře se další instance této ukázky. V této instanci můžete také upravit, konfigurovat a odeslat skript.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Otevření složky pro projekt U-SQL

1. Z Visual Studio Code vyberte nabídku **soubor** a pak vyberte **Otevřít složku**.
2. Zadejte složku a potom vyberte **možnost vybrat složku**.
3. Vyberte nabídku **soubor** a pak vyberte možnost **Nový**. Do projektu se přidá soubor bez názvu-1.
4. Do souboru bez názvu-1 zadejte následující kód:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   VÝSTUP @departments     do "složku/output/departments.csv" pomocí Outputters.Csv ();

    Skript vytvoří soubor departments.csv s některými daty zahrnutými do složky/Output.

5. Uložte soubor jako **myUSQL. usql** v otevřené složce.

### <a name="to-compile-a-u-sql-script"></a>Kompilace skriptu U-SQL

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: zkompiluje skript**. Výsledky kompilace se zobrazí v okně **výstup** . Můžete také kliknout pravým tlačítkem na soubor skriptu a pak vybrat **ADL: kompilovat skript** pro zkompilování úlohy U-SQL. Výsledek kompilace se zobrazí v podokně **výstup** .

### <a name="to-submit-a-u-sql-script"></a>Odeslání skriptu U-SQL

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: Submit Job**. Můžete také kliknout pravým tlačítkem na soubor skriptu a pak vybrat **ADL: odeslat úlohu**.

Po odeslání úlohy U-SQL se protokoly odeslání zobrazí v okně **výstup** v vs Code. Zobrazení úlohy se zobrazí v pravém podokně. Pokud je odeslání úspěšné, zobrazí se také adresa URL úlohy. Pokud chcete sledovat stav úlohy v reálném čase, můžete otevřít adresu URL úlohy ve webovém prohlížeči.

Na kartě **Souhrn** zobrazení úlohy si můžete prohlédnout podrobnosti o úloze. Hlavní funkce zahrnují opětovné odeslání skriptu, duplikování skriptu a otevření na portálu. Na kartě **data** zobrazení úlohy můžete odkazovat na vstupní soubory, výstupní soubory a soubory prostředků. Soubory lze stáhnout do místního počítače.

![Karta souhrn v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta data v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Nastavení výchozího kontextu

Výchozí kontext můžete nastavit tak, aby toto nastavení bylo použito pro všechny soubory skriptu, pokud jste nastavili parametry pro soubory jednotlivě.

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: Nastavte výchozí kontext**. Nebo klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: Nastavte výchozí kontext**.
3. Vyberte účet, databázi a schéma, které chcete. Nastavení je uloženo do xxx_settings.jskonfiguračního souboru.

   ![Účet, databáze a sada schémat nastaveny jako výchozí kontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Nastavení parametrů skriptu

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: Nastavte parametry skriptu**.
3. xxx_settings.jssoubor je otevřen s následujícími vlastnostmi:

   - **účet**: účet Azure Data Lake Analytics v rámci předplatného Azure, který je potřebný pro kompilaci a spouštění úloh U-SQL. Účet počítače je potřeba nakonfigurovat před tím, než budete kompilovat a spouštět úlohy U-SQL.
   - **databáze**: databáze pod vaším účtem. Výchozí hodnota je **Master**.
   - **schéma**: schéma v rámci vaší databáze. Výchozí hodnota je **dbo**.
   - **optionalSettings**:
        - **Priorita**: rozsah priority je od 1 do 1000, přičemž 1 má nejvyšší prioritu. Výchozí hodnota je **1000**.
        - **degreeOfParallelism**: rozsah paralelismus je od 1 do 150. Výchozí hodnota je maximální paralelismus povolený v účtu Azure Data Lake Analytics.

   ![Obsah souboru JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Po uložení konfigurace se informace o účtu, databázi a schématu zobrazí na stavovém řádku v levém dolním rohu odpovídajícího souboru. usql, pokud nemáte nastaven výchozí kontext.

### <a name="to-set-git-ignore"></a>Nastavení ignorování Gitu

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: nastavte ignorovat Git**.

   - Pokud v VS Code pracovní složce nemáte soubor **. gitIgnore** , vytvoří se ve složce soubor s názvem **. gitIgnore** . Ve výchozím nastavení jsou do souboru přidány čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**). V případě potřeby můžete provádět další aktualizace.
   - Pokud již máte soubor **. gitIgnore** ve vaší pracovní složce vs Code, nástroj přidá čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) do souboru **. gitIgnore** , pokud se v souboru nezahrnuly čtyři položky.

   ![Položky v souboru. gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Práce se soubory kódu na pozadí: Sharp, Python a R

Azure Data Lake nástroje podporují více vlastních kódů. Pokyny najdete v tématu [vývoj u-SQL pomocí Pythonu, R a C ostře pro Azure Data Lake Analytics v vs Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Práce se sestaveními

Informace o vývoji sestavení naleznete v tématu [vývoj sestavení U-SQL pro úlohy Azure Data Lake Analytics]().

Můžete použít nástroje Data Lake k registraci vlastních sestavení kódu v katalogu Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Registrace sestavení

Sestavení lze registrovat prostřednictvím příkazu **ADL: Register Assembly** nebo **ADL: Register Assembly (rozšířené)** .

### <a name="to-register-through-the-adl-register-assembly-command"></a>Registrace pomocí příkazu ADL: Register Assembly

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: Register Assembly**.
3. Zadejte cestu k místnímu sestavení.
4. Vyberte účet Data Lake Analytics.
5. Vyberte databázi.

Portál se otevře v prohlížeči a zobrazí se proces registrace sestavení.  

Pohodlnější způsob, jak aktivovat příkaz **ADL: Register Assembly** , je kliknout pravým tlačítkem myši na soubor. dll v Průzkumníkovi souborů.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Registrace pomocí příkazu ADL: Register Assembly (rozšířené)

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.
2. Zadejte **ADL: Register Assembly (rozšířené)**.
3. Zadejte cestu k místnímu sestavení.
4. Zobrazí se soubor JSON. V případě potřeby zkontrolujte a upravte závislosti sestavení a parametry prostředků. Pokyny se zobrazí v okně **výstup** . Chcete-li pokračovat v registraci sestavení, uložte soubor JSON (CTRL + S).

   ![Soubor JSON se závislostmi sestavení a parametry prostředku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Nástroje Azure Data Lake automaticky zjišťují, zda má knihovna DLL nějaké závislosti sestavení. Po zjištění jsou závislosti zobrazeny v souboru JSON.
>- Prostředky knihovny DLL (například. txt,. png a. csv) můžete nahrát jako součást registrace sestavení.

Dalším způsobem, jak aktivovat příkaz **ADL: Register Assembly (rozšířené)** , je kliknout pravým tlačítkem myši na soubor. dll v Průzkumníkovi souborů.

Následující kód U-SQL ukazuje, jak zavolat sestavení. V ukázce je název sestavení *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Použití místního spuštění U-SQL a místního ladění pro uživatele Windows

Místní spuštění U-SQL testuje vaše místní data a ověří váš skript místně, než bude váš kód publikován Data Lake Analytics. Můžete použít místní funkci ladění k dokončení následujících úkolů před odesláním kódu do Data Lake Analytics:

- Ladit kód v jazyce C# – na pozadí.
- Projděte si kód.
- Ověřte skript místně.

Funkce místního běhu a místního ladění funguje pouze v prostředích systému Windows a není podporována v operačních systémech macOS a Linux.

Pokyny k místnímu běhu a místnímu ladění najdete v tématu [místní spuštění U-SQL a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Připojení k Azure

Než budete moct kompilovat a spouštět skripty U-SQL v Data Lake Analytics, musíte se připojit ke svému účtu Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Připojení k Azure pomocí příkazu

1. Kliknutím na CTRL + SHIFT + P otevřete paletu příkazů.

2. Zadejte **ADL: login**. Přihlašovací informace se zobrazí v pravém dolním rohu.

   ![Zadání příkazu pro přihlášení](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Oznámení o přihlášení a ověřování](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Vyberte **kopírovat & otevřít** a otevřete [přihlašovací webovou stránku](https://aka.ms/devicelogin). Vložte kód do pole a pak vyberte **pokračovat**.

    ![Přihlašovací webová stránka](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Podle pokynů se přihlaste z webové stránky. Až budete připojeni, váš název účtu Azure se zobrazí na stavovém řádku v levém dolním rohu okna VS Code.

> [!NOTE]
>
> - Pokud se odhlásíte, Data Lake nástroje se automaticky odhlásí k následujícímu.
> - Pokud má váš účet dva faktory, doporučujeme místo kódu PIN používat ověřování pomocí telefonu.

Pokud se chcete odhlásit, zadejte příkaz **ADL: logout**.

### <a name="to-connect-to-azure-from-the-explorer"></a>Připojení k Azure z Průzkumníka

Rozbalte **Azure datalake**, vyberte **Přihlásit se k Azure** a pak postupujte podle kroků 3 a 4 [pro připojení k Azure pomocí příkazu](#sign-in-by-command).

![Výběr možnosti přihlásit se k Azure v Průzkumníkovi](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nemůžete se odhlásit z Průzkumníka. Pokud se chcete odhlásit, přečtěte si téma [připojení k Azure pomocí příkazu](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Vytvoření skriptu pro extrakci

Můžete vytvořit skript pro extrakci pro soubory. csv,. TSV a. txt pomocí příkazu **ADL: Create Extract Script** nebo z Průzkumníka Azure Data Lake.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Vytvoření skriptu pro extrakci pomocí příkazu

1. Vyberte CTRL + SHIFT + P a otevřete paletu příkazů a zadejte **ADL: Create Extract Script**.
2. Zadejte úplnou cestu k souboru Azure Storage a vyberte klávesu ENTER.
3. Vyberte jeden účet.
4. Pro soubor. txt vyberte oddělovač pro extrakci souboru.

![Proces vytvoření skriptu pro extrakci](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Skript pro extrakci se generuje na základě vašich záznamů. Pro skript, který nemůže detekovat sloupce, vyberte jednu z těchto dvou možností. V takovém případě se vygeneruje pouze jeden skript.

![Výsledek vytvoření skriptu pro extrakci](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Vytvoření skriptu pro extrakci z Průzkumníka

Dalším způsobem, jak vytvořit skript pro extrakci, je pomocí nabídky (zástupce) pravým tlačítkem myši v souboru. csv,. TSV nebo. txt v Azure Data Lake Store nebo v úložišti objektů BLOB v Azure.

![Příkaz "vytvořit EXTRAKCi skriptu" z místní nabídky](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Další kroky

- [Vývoj U-SQL pomocí Pythonu, R a C ostrého pro Azure Data Lake Analytics v VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Místní spuštění U-SQL a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Kurz: Začínáme s Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Kurz: vývoj skriptů U-SQL pomocí Data Lakech nástrojů pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
