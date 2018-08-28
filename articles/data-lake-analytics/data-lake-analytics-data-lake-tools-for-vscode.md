---
title: Pomocí nástrojů Azure Data Lake pro Visual Studio Code
description: Zjistěte, jak vytvářet, testovat a spouštět skripty U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio Code.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: b06b4c4484e3f41b64ff2d60ce558726c555a2e3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047907"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Pomocí nástrojů Azure Data Lake pro Visual Studio Code

V tomto článku zjistěte, jak můžete pomocí nástroje Azure Data Lake pro Visual Studio Code (VS Code) vytvářet, testovat a spouštět skripty U-SQL. Informace jsou také obsaženy v následujícím videu:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Požadavky

Azure Data Lake Tools pro VS Code podporuje Windows, Linux a MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Pro MacOS a Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalace nástrojů Azure Data Lake

Po instalaci požadavky, můžete nainstalovat Azure Data Lake Tools pro VS Code.

**Instalace nástrojů Azure Data Lake**

1. Otevřete Visual Studio Code.
2. Vyberte **rozšíření** v levém podokně. Zadejte **nástroje Azure Data Lake** do vyhledávacího pole.
3. Vyberte **nainstalovat** vedle **nástroje Azure Data Lake**. 

   ![Vybrané možnosti instalace nástrojů Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po pár sekundách **nainstalovat** tlačítko se změní na **Reload**.
4. Vyberte **Reload** aktivovat **nástroje Azure Data Lake** rozšíření.
5. Vyberte **znovu načíst okno** potvrďte. Můžete zobrazit **nástroje Azure Data Lake** v **rozšíření** podokně.

 
## <a name="activate-azure-data-lake-tools"></a>Aktivovat Azure Data Lake Tools
Vytvořte soubor .usql nebo otevřete existující soubor .usql aktivovat rozšíření. 


## <a name="work-with-u-sql"></a>Práce s jazykem U-SQL

Pro práci s U-SQL, třeba otevřít soubor U-SQL nebo složka.

**Chcete-li spustit ukázkový skript**

Otevřete paletu příkazů (Ctrl + Shift + P) a zadejte **ADL: otevřít ukázkový skript**. Otevře se další instance této ukázky. Můžete také upravit, konfiguraci a odešlete skript v této instanci.

**Chcete-li otevřít složku pro váš projekt v U-SQL**

1. Visual Studio Code, vyberte **souboru** nabídky a pak vyberte **otevřít složku**.
2. Zadejte složku a pak vyberte **vybrat složku**.
3. Vyberte **souboru** nabídky a pak vyberte **nový**. Soubor bez názvu – 1 se přidá do projektu.
4. Zadejte následující kód v souboru bez názvu – 1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Tento skript vytvoří soubor departments.csv s daty nachází ve složce/Output.

5. Uložte soubor jako **myUSQL.usql** v otevřené složce.

**Chcete-li zkompilovat skript U-SQL**

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů. 
2. Zadejte **ADL: kompilace skriptu**. Výsledky kompilace se zobrazí v **výstup** okna. Můžete také klikněte pravým tlačítkem na soubor skriptu a pak vyberte **ADL: kompilace skriptu** kompilace úlohy U-SQL. Výsledek kompilace se zobrazí ve **výstup** podokně.
 
**Odeslat skript U-SQL**

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů. 
2. Zadejte **ADL: odeslat úlohu**. Můžete také klikněte pravým tlačítkem na soubor skriptu a pak vyberte **ADL: odeslat úlohu**. 

Po odeslání úlohy U-SQL, odesílání protokolů se objeví v **výstup** okna ve VS Code. V pravém podokně se zobrazí zobrazení úloh. Pokud odeslání proběhne úspěšně, zobrazí se příliš adresa URL úlohy. Adresa URL úlohy můžete otevřít ve webovém prohlížeči a sledovat stav úlohy v reálném čase. 

V zobrazení úlohy **Souhrn** kartě, zobrazí se podrobnosti o úloze. Mezi hlavní funkce patří znovu spustit skript, duplicitní skriptu a otevřete na portálu. V zobrazení úlohy **DATA** kartu, můžete odkazovat na vstupní soubory, výstupní soubory a soubory prostředků. Soubory můžete stáhnout do místního počítače.

![Karta Souhrn v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta data v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Chcete-li nastavit výchozí kontext**

Můžete nastavit výchozí kontext pro toto nastavení platí pro všechny soubory skriptu, pokud jste nenastavili parametry pro soubory jednotlivě.

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů. 
2. Zadejte **ADL: nastavit výchozí kontext**. Nebo klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: nastavit výchozí kontext**.
3. Zvolte účet, databázi a schéma, které chcete. Toto nastavení se uloží do konfiguračního souboru xxx_settings.json.

   ![Účet, databázi a nastavit jako výchozí kontext schématu](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Chcete-li nastavit parametry skriptu**

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů. 
2. Zadejte **ADL: nastavte parametry skriptu**.
3. Otevře se soubor xxx_settings.json s následujícími vlastnostmi:

   - **účet**: účtu Azure Data Lake Analytics v rámci vašeho předplatného Azure, který je nezbytný pro kompilaci a spouštění úloh U-SQL. Účet počítače nutné konfigurovat před kompilace a spuštění úloh U-SQL.
   - **databáze**: databáze v rámci vašeho účtu. Výchozí hodnota je **hlavní**.
   - **schéma**: schéma v rámci vaší databáze. Výchozí hodnota je **dbo**.
   - **optionalSettings**:
        - **Priorita**: Priorita rozsah je od 1 do 1000, přičemž 1 nejvyšší prioritu. Výchozí hodnota je **1000**.
        - **z degreeOfParallelism**: paralelismu rozsah je od 1 do 150. Výchozí hodnota je maximální paralelismu povolená ve vašem účtu Azure Data Lake Analytics. 

   ![Obsah souboru JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Po uložení konfigurace účtu, databáze a informace o schématu se zobrazí ve stavovém řádku v levém horním rohu odpovídající soubor .usql Pokud nemáte k dispozici výchozí kontext nastavení.

**Chcete-li nastavit Git ignorovat**

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů. 
2. Zadejte **ADL: ignorování Git sady**.

   - Pokud nemáte k dispozici **.gitIgnore** souboru ve VS Code pracovní složky, do souboru s názvem **.gitIgnore** se vytvoří ve složce. Čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) jsou přidány do souboru ve výchozím nastavení. V případě potřeby můžete provést další aktualizace.
   - Pokud už máte **.gitIgnore** přidá soubor do pracovní složky VS Code, nástroj čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) ve vaší **.gitIgnore** souboru, pokud čtyři položky nebyly zahrnuty v souboru.

   ![Položky v souboru .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Práce se soubory kódu na pozadí: C Sharp, Pythonu a r.

Azure Data Lake Tools podporují více vlastní kódy. Pokyny najdete v tématu [vývoj U-SQL pomocí Pythonu, R a jazyka C Sharp pro Azure Data Lake Analytics v nástroji VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Práce se sestaveními

Informace o vývoji sestavení naleznete v tématu [sestavení vývoj U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Data Lake Tools můžete použít k registraci sestavení vlastního kódu v katalogu Data Lake Analytics.

**Registrace sestavení**

Lze zaregistrovat sestavení prostřednictvím **ADL: zaregistrovat sestavení** nebo **ADL: registraci sestavení (Upřesnit)** příkazu.

**Registrace přes ADL: příkaz zaregistrovat sestavení**
1.  Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů.
2.  Zadejte **ADL: zaregistrovat sestavení**. 
3.  Zadejte cestu místní sestavení. 
4.  Vyberte účet Data Lake Analytics.
5.  Vyberte databázi.

Na portálu se otevře v prohlížeči a zobrazí v registraci sestavení.  

Pohodlnější způsob, jak spouštět **ADL: zaregistrovat sestavení** příkaz je klikněte pravým tlačítkem na soubor .dll v Průzkumníku souborů. 

**Registrace přes ADL: příkaz registraci sestavení (rozšířené)**
1.  Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů.
2.  Zadejte **ADL: zaregistrovat sestavení (rozšířené)**. 
3.  Zadejte cestu místní sestavení. 
4.  Soubor JSON se zobrazí. Zkontrolujte a v případě potřeby upravit závislosti sestavení a parametry prostředku. Pokyny jsou zobrazeny v **výstup** okna. Aby bylo možné pokračovat k registraci sestavení, uložte (Ctrl + S) souboru JSON.

    ![Soubor JSON s parametry prostředku a závislosti sestavení](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools automatické Určuje, zda má knihovna DLL všechny závislosti sestavení. Závislosti jsou zobrazovány v souboru JSON se zjistil. 
>- Prostředky knihovny DLL (třeba .txt, PNG a CSV) můžete nahrát jako součást registrace sestavení. 

Dalším způsobem, jak aktivovat **ADL: registraci sestavení (Upřesnit)** příkaz je klikněte pravým tlačítkem na soubor .dll v Průzkumníku souborů. 

Následující kód U-SQL ukazuje, jak volat sestavení. V příkladu je název sestavení *testování*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Pomocí U-SQL místní spuštění a místní ladění pro uživatele Windows
U-SQL místní spuštění testy vaše místní data a ověřuje svůj skript místně před publikováním kódu ke službě Data Lake Analytics. K dokončení následujících úloh před odesláním kódu ke službě Data Lake Analytics můžete použít místní ladění funkce: 
- Ladit vaše C# kódu na pozadí. 
- Krokovat kód. 
- Ověřte skript místně.

Místní spuštění a místní ladění, v tématu [U-SQL místní spuštění a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Připojení k Azure

Aby bylo možné zkompilovat a spouštět skripty U-SQL ve službě Data Lake Analytics, musíte se připojit k účtu Azure.

<b id="sign-in-by-command">Pro připojení k Azure pomocí příkazu</b>

1.  Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů. 
2.  Zadejte **ADL: přihlášení**. Přihlašovací údaje se zobrazí vpravo dole.

    ![Zadání příkazu přihlášení](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Oznámení o přihlašování a ověřování](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Vyberte **zkopírovat a otevřít** otevřít [přihlašovací webovou stránku](https://aka.ms/devicelogin). Vložte kód do pole a pak vyberte **pokračovat**.

    ![Webová přihlašovací stránka](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Postupujte podle pokynů k přihlášení z webové stránky. Pokud jste připojení, název svého účtu Azure se zobrazí ve stavovém řádku v levém horním rohu okna nástroje VS Code. 

> [!NOTE] 
>- Nástroje data Lake automaticky přihlášení při příštím Pokud nemáte Odhlásit se.
>- Pokud váš účet má dva faktory povoleno, doporučujeme, že používáte ověřovací telefonní místo používání kódu PIN.


Chcete-li odhlásit, zadejte příkaz **ADL: odhlášení**.

**Pro připojení k Azure z Průzkumníka**

Rozbalte **AZURE DATALAKE**vyberte **přihlášení k Azure**a pak postupujte podle kroku 3 a kroku 4 [pro připojení k Azure pomocí příkazu](#sign-in-by-command).

!["Přihlaste se k Azure" výběr v Průzkumníku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Můžete se odhlásit z Průzkumníka. Chcete-li odhlásit, přečtěte si téma [pro připojení k Azure pomocí příkazu](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Vytvořit skript extrakce 
Pomocí příkazu můžete vytvořit skript extrakce pro soubory CSV, TSV a txt **ADL: vytvoření skriptu EXTRAHOVAT** nebo z Azure Data Lake explorer.

**Chcete-li vytvořit skript extrakci pomocí příkazu**

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů a zadejte **ADL: vytvoření skriptu EXTRAHOVAT**.
2. Zadejte úplnou cestu k souboru úložiště Azure a stisknutím klávesy Enter.
3. Vyberte jeden účet.
4. Soubor .txt vyberte oddělovač, aby se extrahoval soubor. 

![Proces vytváření skriptu extrakce](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Extrakce skript je generován a základě zadané. Pro skript, který nelze rozpoznat sloupce vyberte jednu ze dvou možností. V opačném případě se vygeneruje pouze jeden skript.

![Výsledek vytvoření skriptu extrakce](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Chcete-li vytvořit skript extrakce z Průzkumníka**

Dalším způsobem, jak vytvořit skript extrakce je prostřednictvím nabídky (místní) klikněte pravým tlačítkem na CSV, TSV nebo souboru .txt ve službě Azure Data Lake Store nebo Azure Blob storage.

![Příkaz "Vytvořit skript EXTRACT" z místní nabídky](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrace s Azure Data Lake Analytics pomocí příkazu

Můžete přístup k prostředkům Azure Data Lake Analytics umožňuje výpis účtů, metadata přístup a zobrazit analytics úlohy. 

**Seznam účtů Azure Data Lake Analytics v rámci vašeho předplatného Azure**

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů.
2. Zadejte **ADL: výpis účtů**. Účty se zobrazí v **výstup** podokně.

**Chcete-li získat přístup k metadatům Azure Data Lake Analytics**

1.  Vyberte kombinaci kláves Ctrl + Shift + P a potom zadejte **ADL: seznam tabulek**.
2.  Vyberte jeden z účtů Data Lake Analytics.
3.  Vyberte jednu z databáze Data Lake Analytics.
4.  Vyberte jednu z schémat. Zobrazí se seznam tabulek.

**Chcete-li zobrazit úlohy Azure Data Lake Analytics**
1.  Otevřete paletu příkazů (Ctrl + Shift + P) a vyberte **ADL: Zobrazit úlohy**. 
2.  Vyberte Data Lake Analytics nebo místní účet. 
3.  Vyčkat, než seznamu úloh, se zobrazí pro tento účet.
4.  Vyberte úlohu ze seznamu. Data Lake Tools v pravém podokně otevře zobrazení úloh a některé informace se zobrazí ve výstupu VS Code.

    ![Seznam úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrace s Azure Data Lake Store prostřednictvím příkazu

Můžete použít příkazy související s Azure Data Lake Store:
 - [Procházení prostředků Azure Data Lake Store](#list-the-storage-path) 
 - [Zobrazit náhled souboru Azure Data Lake Store](#preview-the-storage-file) 
 - [Nahrajte soubor přímo do Azure Data Lake Store v nástroji VS Code](#upload-file-or-folder)
 - [Stažení souboru přímo ze služby Azure Data Lake Store v nástroji VS Code](#download-file)

### <a name="list-the-storage-path"></a>Seznam cest úložiště 

**Do seznamu Cesta k úložišti prostřednictvím paletu příkazů**

1. Pravým tlačítkem myši na editor skriptů a vyberte **ADL: cesta k seznamu**.
2. Vyberte složku, v seznamu, nebo vyberte **zadejte cestu** nebo **procházet z kořenové cestě**. (My používáme **zadejte cestu** s ukázkovým.) 
3. Vyberte svůj účet Data Lake Analytics.
4. Vyhledejte nebo zadejte cestu ke složce úložiště (třeba/výstup /).  

Paleta příkazů uvádí informace o cestě na základě vašich záznamů.

![Výsledky cesta úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Přes nabídku je pohodlnější způsob, jak seznamu relativní cestu.

**Cesta k úložišti přes místní nabídku seznamu**

Klikněte pravým tlačítkem na řetězec cesty a vyberte **cesta k seznamu**.

!["Seznam cest" v místní nabídce](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Zobrazit náhled souboru úložiště

1. Pravým tlačítkem myši na editor skriptů a vyberte **ADL: náhled souboru**.
2. Vyberte svůj účet Data Lake Analytics. 
3. Zadejte cestu k souboru Azure Storage (například /output/SearchLog.txt). 

Soubor se otevře v nástroji VS Code.

![Kroky a výsledek pro zobrazení náhledu souboru úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Dalším způsobem, jak zobrazit náhled souboru je přes nabídku na úplnou cestu k souboru nebo relativní cesty souboru v editoru skriptů. 

### <a name="upload-a-file-or-folder"></a>Nahrání souboru nebo složky

1. Pravým tlačítkem myši na editor skriptů a vyberte **nahrát soubor** nebo **nahrát složku**.
2. Zvolte soubor nebo více souborů, pokud jste vybrali **nahrát soubor**, nebo vyberte celou složku, pokud jste vybrali **nahrát složku**. Potom vyberte **nahrát**. 
3. V seznamu vyberte složku, úložiště, nebo vyberte **zadejte cestu** nebo **procházet z kořenové cestě**. (My používáme **zadejte cestu** s ukázkovým.) 
4. Vyberte svůj účet Data Lake Analytics. 
5. Vyhledejte nebo zadejte cestu ke složce úložiště (třeba/výstup /). 
6. Vyberte **vybrat aktuální složku** zadat cíl nahrávání.

![Kroky a výsledek pro nahrávání souboru nebo složky](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Jiný způsob k nahrání souborů do úložiště je přes nabídku na úplnou cestu k souboru nebo relativní cesty souboru v editoru skriptů.

Je možné [monitorovat stav nahrávání](#check-storage-tasks-status).


### <a name="download-a-file"></a>Stažení souboru 
Soubor můžete stáhnout pomocí příkazu **ADL: stáhnout soubor** nebo **ADL: Stažení souboru (Upřesnit)**.

**Stáhnout soubor přes ADL: příkaz pro stažení souboru (Upřesnit)**
1. Pravým tlačítkem myši na editor skriptů a pak vyberte **stáhnout soubor (Upřesnit)**.
2. VS Code zobrazí soubor JSON. Můžete zadat cesty k souborům a stáhnout více souborů najednou. Pokyny jsou zobrazeny v **výstup** okna. Přejděte na soubor nebo soubory stáhnout, uložte (Ctrl + S) souboru JSON.

    ![Soubor JSON s cestami ke stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**Výstup** okně se zobrazí stav stahování souboru.

![Okně výstupu se stavem stahování](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Je možné [monitorování stavu stažení](#check-storage-tasks-status).

**Stáhnout soubor přes ADL: Stáhněte si soubor – příkaz**

1. Pravým tlačítkem myši na editor skriptů, vyberte **stáhnout soubor**a potom vyberte cílovou složku z **vybrat složku** dialogové okno.
2. Vyberte složku, v seznamu, nebo vyberte **zadejte cestu** nebo **procházet z kořenové cestě**. (My používáme **zadejte cestu** s ukázkovým.) 
3. Vyberte svůj účet Data Lake Analytics. 
4. Procházením nebo zadejte cestu ke složce úložiště (třeba/výstup /) a pak zvolte soubor ke stažení.

![Kroky a výsledek pro stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Dalším způsobem, jak stáhnout soubory úložiště je přes nabídku na úplnou cestu k souboru nebo relativní cesty souboru v editoru skriptů.

Je možné [monitorování stavu stažení](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Zkontrolujte stav úlohy úložiště
Stav nahrávání a stahování se zobrazí ve stavovém řádku. Vyberte stavový řádek a pak se zobrazí stav na **výstup** kartu.

![Stavový řádek tak za výstupní podrobnosti](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrace s Azure Data Lake Analytics z Průzkumníka

Po přihlášení se všechna předplatná pro váš účet Azure jsou uvedené v části v levém podokně **AZURE DATALAKE**. 

![Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metadat navigace

Rozbalte vaše předplatné Azure. V části **databáze U-SQL** uzlu, můžete procházet pomocí U-SQL database a složek zobrazení, jako jsou **schémata**, **pověření**, **sestavení**, **Tabulky**, a **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Správa data Lake Analytics metadata entit

Rozbalte **databáze U-SQL**. Můžete vytvořit databáze, schéma, tabulka, typ tabulky, index nebo statistiku kliknutím pravým tlačítkem na odpovídající uzel a pak vyberete **skript k vytvoření** v místní nabídce. Na stránce otevřený skript upravte skript podle vašich potřeb. Pak odešlete úlohu tak, že pravým tlačítkem myši a vyberete **ADL: odeslat úlohu**. 

Po dokončení vytváření položky, klikněte pravým tlačítkem na uzel a potom vyberte **aktualizovat** zobrazíte položky. Můžete také odstranit položku pravým tlačítkem myši a potom výběrem **odstranit**.

![Příkaz "Skript k vytvoření" v místní nabídce v Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skript stránky pro novou položku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrace sestavení data Lake Analytics

Sestavení v odpovídající databázi můžete zaregistrovat klepnutím pravým tlačítkem myši **sestavení** uzlu a pak vyberete **zaregistrovat sestavení**.

![Příkaz "Zaregistrovat sestavení" v místní nabídce pro uzel sestavení](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrace s Azure Data Lake Store v Průzkumníku

Přejděte do **Data Lake Store**:

- Můžete klikněte pravým tlačítkem na uzel složky a potom použít **aktualizovat**, **odstranit**, **nahrát**, **nahrát složku**, **kopírování Relativní cesta**, a **Kopírovat úplnou cestu** příkazy v místní nabídce.

   ![Příkazy místní nabídky pro uzel složky v Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Můžete klikněte pravým tlačítkem na uzel souboru a pak použít **ve verzi Preview**, **Stáhnout**, **odstranit**, **vytvořit skript EXTRAHOVAT** (k dispozici pouze pro sdílený svazek clusteru TSV, TXT soubory a), **kopírovat relativní cestu**, a **Kopírovat úplnou cestu** příkazy v místní nabídce.

   ![Příkazy místní nabídky pro uzel souboru v Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrace s Azure Blob storage z Průzkumníka

Přejděte do úložiště objektů Blob:

- Můžete klikněte pravým tlačítkem na uzel kontejneru objektů blob a pak použít **aktualizovat**, **odstranit kontejner objektů Blob**, a **nahrát objekt Blob** příkazy v místní nabídce.

   ![Příkazy místní nabídky pro uzel kontejneru objektů blob v úložišti objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Můžete klikněte pravým tlačítkem na uzel složky a potom použít **aktualizovat** a **nahrát objekt Blob** příkazy v místní nabídce.

   ![Příkazy místní nabídky pro uzel složky v úložišti objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Můžete klikněte pravým tlačítkem na uzel souboru a pak použít **ve verzi Preview nebo upravit**, **Stáhnout**, **odstranit**, **vytvořit skript EXTRAHOVAT** (k dispozici pouze pro CSV, TSV, TXT soubory a) **kopírovat relativní cestu**, a **Kopírovat úplnou cestu** příkazy v místní nabídce.

    ![Příkazy místní nabídky pro uzel souboru v úložišti objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Data Lake explorer otevřete na portálu
1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů.
2. Zadejte **otevřete Web Azure Storage Explorer** nebo relativní cestu nebo úplnou cestu v editoru klikněte pravým tlačítkem a pak vyberte **otevřete Web Azure Storage Explorer**.
3. Vyberte účet Data Lake Analytics.

Nástroje data Lake otevře cesta k úložišti Azure na webu Azure Portal. Můžete najít cestu a zobrazit náhled souboru z webu.

## <a name="additional-features"></a>Další funkce

Data Lake Tools for VS Code podporuje následující funkce:

-   **Automatické dokončování IntelliSense**: návrhů se zobrazí v automaticky otevíraných oken kolem položky, jako jsou klíčová slova, metody a proměnné. Různé ikony představují různé typy objektů:

    - Datový typ Scala
    - Komplexní datový typ.
    - Integrované uživatelsky definované typy
    - Třídy a .NET collection
    - Výrazy jazyka C#
    - Integrované C# uživatelem definovanými funkcemi, UDO a UDAAGs 
    - Funkce U-SQL
    - Funkce okna U-SQL
 
    ![Typy objektů IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Funkce automatického dokončování IntelliSense v Data Lake Analytics metadat**: Data Lake Tools stáhne informace metadat Data Lake Analytics místně. Funkce technologie IntelliSense automaticky naplní objekty z metadat Data Lake Analytics. Mezi tyto objekty patří, databáze, schéma, tabulka, zobrazení, funkci vracející tabulku, postupy a sestavení C#.
 
    ![Metadata technologie IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Značky chyb IntelliSense**: Data Lake Tools podtrhuje úpravy chyby pro jazyk U-SQL a C#. 
-   **Zvýraznění syntaxe**: nástrojů Data Lake používá barvy k rozlišení různé věci, třeba proměnné, klíčová slova, datové typy a funkce. 

    ![Syntaxe s různými barvami](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Doporučujeme upgradovat na Azure Data Lake Tools for Visual Studio verze 2.3.3000.4 nebo novější. Předchozí verze jsou zastaralé a nejsou už k dispozici ke stažení.  
   
## <a name="next-steps"></a>Další postup
- [Vývoj U-SQL pomocí Pythonu, R a jazyka C Sharp pro Azure Data Lake Analytics v nástroji VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL místní spuštění a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Kurz: Začínáme s Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Kurz: Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
