---
title: Použití nástrojů Azure Data Lake pro Visual Studio Code
description: Zjistěte, jak pomocí nástrojů Datového jezera Azure pro kód Visual Studia vytvářet, testovat a spouštět skripty U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60508890"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Použití nástrojů Azure Data Lake pro Visual Studio Code

V tomto článku se dozvíte, jak můžete pomocí nástroje Azure Data Lake Tools for Visual Studio Code (VS Code) vytvářet, testovat a spouštět skripty U-SQL. Informace jsou také zahrnuty v následujícím videu:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Požadavky

Nástroje datového jezera Azure pro VS Code podporují Windows, Linux a macOS.U-SQL místní spuštění a místní ladění funguje pouze v systému Windows.

- [Kód visual studia](https://www.visualstudio.com/products/code-vs.aspx)

Pro MacOS a Linux:
- [Sada .NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalace nástrojů Datového jezera Azure

Po instalaci předpokladů můžete nainstalovat Nástroje Azure Data Lake pro Kód VS.

**Instalace nástrojů Azure Data Lake Tools**

1. Otevřete Visual Studio Code.
2. V levém podokně vyberte **Rozšíření.** Do vyhledávacího pole zadejte **Nástroje datového jezera Azure.**
3. Vyberte **Instalovat** vedle **nástroje datového jezera Azure**. 

   ![Výběry pro instalaci nástrojů data lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Po několika sekundách se tlačítko **Instalovat** změní na **Znovu načíst**.
4. Vyberte **Znovu načíst,** chcete-li aktivovat rozšíření **Nástroje datového jezera Azure.**
5. Vyberte **Znovu načíst okno** pro potvrzení. Nástroje **datového jezera Azure** najdete v podokně **Rozšíření.**

 
## <a name="activate-azure-data-lake-tools"></a>Aktivace nástrojů Datového jezera Azure
Vytvořte soubor .usql nebo otevřete existující soubor .usql pro aktivaci přípony. 


## <a name="work-with-u-sql"></a>Práce s U-SQL

Chcete-li pracovat s U-SQL, musíte otevřít soubor U-SQL nebo složku.

**Otevření ukázkového skriptu**

Otevřete paletu příkazů (Ctrl+Shift+P) a zadejte **ADL: Otevřít ukázkový skript**. Otevře jinou instanci této ukázky. Můžete také upravit, nakonfigurovat a odeslat skript v této instanci.

**Otevření složky pro projekt U-SQL**

1. V kódu sady Visual Studio vyberte nabídku **Soubor** a pak vyberte **Otevřít složku**.
2. Zadejte složku a pak vyberte **Vybrat složku**.
3. Vyberte nabídku **Soubor** a pak vyberte **Nový**. Do projektu je přidán soubor Bez názvu 1.
4. Do souboru Untitled-1 zadejte následující kód:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    Skript vytvoří soubor departments.csv s některými daty obsaženými ve složce /output.

5. Uložte soubor jako **myUSQL.usql** v otevřené složce.

**Kompilace skriptu U-SQL**

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů. 
2. Zadejte **ADL: Kompilovat skript**. Výsledky kompilace se zobrazí v okně **Výstup.** Můžete také klepnout pravým tlačítkem myši na soubor skriptu a potom vybrat **ADL: Kompilovat skript** pro kompilaci úlohy U-SQL. Výsledek kompilace se zobrazí v podokně **Výstup.**
 
**Odeslání skriptu U-SQL**

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů. 
2. Zadejte **ADL: Odeslat úlohu**. Můžete také klepnout pravým tlačítkem myši na soubor skriptu a potom vybrat **Možnost ADL: Odeslat úlohu**. 

Po odeslání úlohy U-SQL se protokoly odeslání zobrazí v okně **Výstup** v kódu VS. Zobrazení úlohy se zobrazí v pravém podokně. Pokud je odeslání úspěšné, zobrazí se také adresa URL úlohy. Adresu URL úlohy můžete otevřít ve webovém prohlížeči a sledovat stav úlohy v reálném čase. 

Na kartě **SOUHRN** zobrazení úlohy se zobrazí podrobnosti o úloze. Mezi hlavní funkce patří opětovné odeslání skriptu, duplikace skriptu a otevření na portálu. Na kartě **DATA** zobrazení úloh můžete odkazovat na vstupní soubory, výstupní soubory a soubory prostředků. Soubory lze stáhnout do místního počítače.

![Karta Souhrn v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta Data v zobrazení úlohy](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Nastavení výchozího kontextu**

Pokud nemáte nastavené parametry pro soubory jednotlivě, můžete nastavit výchozí kontext pro použití tohoto nastavení na všechny soubory skriptu.

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů. 
2. Zadejte **ADL: Nastavit výchozí kontext**. Nebo klepněte pravým tlačítkem myši na editor skriptů a vyberte **Možnost ADL: Nastavit výchozí kontext**.
3. Vyberte požadovaný účet, databázi a schéma. Nastavení se uloží do konfiguračního souboru xxx_settings.json.

   ![Účet, databáze a schéma nastavené jako výchozí kontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Nastavení parametrů skriptu**

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů. 
2. Zadejte **ADL: Nastavit parametry skriptu**.
3. Soubor xxx_settings.json je otevřen s následujícími vlastnostmi:

   - **účet**: Účet Azure Data Lake Analytics v rámci předplatného Azure, který je potřeba ke kompilaci a spuštění úloh U-SQL. Před kompilací a spuštěním úloh U-SQL je třeba nakonfigurovat účet počítače.
   - **databáze**: Databáze pod vaším účtem. Výchozí hodnota je **master**.
   - **Schéma**: Schéma pod databází. Výchozí hodnota je **dbo**.
   - **volitelnéNastavení**:
        - **priorita**: Prioritní rozsah je od 1 do 1000, přičemž 1 má nejvyšší prioritu. Výchozí hodnota je **1000**.
        - **degreeOfParallelism**: Rozsah paralelismu je od 1 do 150. Výchozí hodnota je maximální paralelismus povolený ve vašem účtu Azure Data Lake Analytics. 

   ![Obsah souboru JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Po uložení konfigurace se informace o účtu, databázi a schématu zobrazí na stavovém řádku v levém dolním rohu odpovídajícího souboru .usql, pokud nemáte nastaven výchozí kontext.

**Nastavení ignorování Gitu**

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů. 
2. Zadejte **ADL: Nastavit Git Ignorovat**.

   - Pokud nemáte soubor **.gitIgnore** v pracovní složce VS Code, soubor s názvem **.gitIgnore** je vytvořen ve vaší složce. Ve výchozím nastavení jsou do souboru přidány čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**). V případě potřeby můžete provést další aktualizace.
   - Pokud již máte soubor **.gitIgnore** v pracovní složce VS Code, přidá nástroj do souboru **.gitIgnore** čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) , pokud čtyři položky nebyly do souboru zahrnuty.

   ![Položky v souboru gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Práce se soubory s kódem na pozadí: C Sharp, Python a R

Azure Data Lake Tools podporuje několik vlastních kódů. Pokyny najdete [v tématu Vývoj U-SQL s Pythonem, R a C Sharp pro Azure Data Lake Analytics v Kódu VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Práce se sestavami

Informace o vývoji sestavení najdete v [tématu Vývoj sestavení U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Pomocí nástrojů Data Lake Tools můžete zaregistrovat vlastní sestavení kódu v katalogu Data Lake Analytics.

**Registrace sestavení**

Sestavení můžete zaregistrovat pomocí příkazu **ADL: Register Assembly** nebo **ADL: Register Assembly (Advanced).**

**Registrace prostřednictvím příkazu ADL: Register Assembly**
1.  Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů.
2.  Zadejte **ADL: Register Assembly**. 
3.  Zadejte cestu místního sestavení. 
4.  Vyberte účet Data Lake Analytics.
5.  Vyberte databázi.

Portál je otevřen v prohlížeči a zobrazuje proces registrace sestavení.  

Pohodlnější způsob, jak aktivovat příkaz **ADL: Register Assembly,** je klepnout pravým tlačítkem myši na soubor DLL v Průzkumníkovi souborů. 

**Registrace pomocí příkazu ADL: Register Assembly (Advanced)**
1.  Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů.
2.  Zadejte **ADL: Register Assembly (Upřesnit)**. 
3.  Zadejte cestu místního sestavení. 
4.  Zobrazí se soubor JSON. V případě potřeby zkontrolujte a upravte závislosti sestavení a parametry prostředků. Pokyny jsou zobrazeny v okně **Výstup.** Chcete-li přejít k registraci sestavení, uložte (Ctrl+S) soubor JSON.

    ![Soubor JSON se závislostmi sestavení a parametry prostředků](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools automaticky zjistí, zda dll má všechny závislosti sestavení. Závislosti jsou zobrazeny v souboru JSON po jejich zjištění. 
>- Prostředky dll (například .txt, PNG a .csv) můžete nahrát jako součást registrace sestavení. 

Dalším způsobem, jak spustit příkaz **ADL: Register Assembly (Upřesnit),** je klepnout pravým tlačítkem myši na soubor DLL v Průzkumníku souborů. 

Následující kód U-SQL ukazuje, jak volat sestavení. Ve vzorku je název sestavení *test*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Použití místního spuštění U-SQL a místního ladění pro uživatele systému Windows
U-SQL místní spuštění testuje vaše místní data a ověří skript místně před váš kód je publikován do Data Lake Analytics. Funkci místního ladění můžete použít k dokončení následujících úloh před odesláním kódu do služby Data Lake Analytics: 
- Ladění kódu jazyka C# na pozadí. 
- Projděte kód. 
- Ověřte skript místně.

Funkce místního spuštění a místního ladění funguje pouze v prostředích Windows a není podporována v operačních systémech macOS a Linux.

Pokyny k místnímu spuštění a místnímu ladění naleznete [v tématu Místní spuštění u-SQL a místní ladění s kódem sady Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Připojení k Azure

Než budete moci kompilovat a spouštět skripty U-SQL v Data Lake Analytics, musíte se připojit ke svému účtu Azure.

<b id="sign-in-by-command">Připojení k Azure pomocí příkazu</b>

1.  Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů. 
2.  Zadejte **ADL: Přihlásit**se . Přihlašovací údaje se zobrazí vpravo dole.

    ![Zadání příkazu pro přihlášení](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Oznámení o přihlášení a ověřování](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Chcete-li otevřít [přihlašovací webovou stránku](https://aka.ms/devicelogin), vyberte **možnost Kopírovat & otevřít** . Vložte kód do pole a pak vyberte **Pokračovat**.

    ![Přihlašovací webová stránka](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Podle pokynů se přihlaste z webové stránky. Když jste připojeni, název účtu Azure se zobrazí na stavovém řádku v levém dolním rohu okna Kód VS. 

> [!NOTE] 
>- Nástroje Data Lake Tools vás při příštím odhlášení automaticky podepíšou.
>- Pokud je na vašem účtu povoleny dva faktory, doporučujeme použít místo pinkódu telefonické ověřování.


Chcete-li se odhlásit, zadejte příkaz **ADL: Odhlášení**.

**Připojení k Azure z průzkumníka**

Rozbalte **AZURE DATALAKE**, vyberte **Přihlásit se k Azure**a pak postupujte podle kroku 3 a kroku 4 [Chcete-li se připojit k Azure pomocí příkazu](#sign-in-by-command).

![Výběr "Přihlásit se k Azure" v průzkumníku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Z průzkumníka se nemůžete odhlásit. Pokud se chcete odhlásit, přečtěte si informace [o připojení k Azure pomocí příkazu](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Vytvoření skriptu pro extrakci 
Můžete vytvořit odvětvovací skript pro soubory .csv, .tsv a .txt pomocí příkazu **ADL: Create EXTRACT Script** nebo z průzkumníka Datového jezera Azure.

**Vytvoření odvětvového skriptu pomocí příkazu**

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů, a zadejte **ADL: Create EXTRACT Script**.
2. Zadejte úplnou cestu pro soubor úložiště Azure a vyberte klíč Enter.
3. Vyberte jeden účet.
4. U souboru TXT vyberte oddělovač, který soubor extrahuje. 

![Proces vytvoření extrakčního skriptu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Extrakční skript je generován na základě vašich položek. Pro skript, který nemůže rozpoznat sloupce, vyberte jednu ze dvou možností. Pokud tomu tak není, bude vygenerován pouze jeden skript.

![Výsledek vytvoření extrakčního skriptu](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Vytvoření extrakčního skriptu z průzkumníka**

Dalším způsobem, jak vytvořit odsávací skript, je prostřednictvím nabídky .csv, TSV nebo .txt v úložišti Azure Data Lake Store nebo Azure Blob Storage.

![Příkaz "Vytvořit skript EXTRAKTu" z místní nabídky](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrace s Azure Data Lake Analytics pomocí příkazu

K prostředkům Azure Data Lake Analytics můžete získat seznam účtů, přístup k metadatům a zobrazení analytických úloh. 

**Seznam účtů Azure Data Lake Analytics v rámci předplatného Azure**

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů.
2. Zadejte **ADL: Seznam účtů**. Účty se zobrazí v podokně **Výstup.**

**Přístup k metadatům Azure Data Lake Analytics**

1.  Vyberte Ctrl+Shift+P a zadejte **ADL: Seznam tabulek**.
2.  Vyberte jeden z účtů Data Lake Analytics.
3.  Vyberte jednu z databází Data Lake Analytics.
4.  Vyberte jedno ze schémat. Můžete zobrazit seznam tabulek.

**Zobrazení úloh Azure Data Lake Analytics**
1.  Otevřete paletu příkazů (Ctrl+Shift+P) a vyberte **ADL: Zobrazit úlohy**. 
2.  Vyberte data Lake Analytics nebo místní účet. 
3.  Počkejte, až se pro účet zobrazí seznam úloh.
4.  Vyberte úlohu ze seznamu úloh. Nástroje data lake otevře zobrazení úlohy v pravém podokně a zobrazí některé informace ve výstupu kódu VS.

    ![Seznam pracovních míst](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrace s Azure Data Lake Store pomocí příkazu

Pomocí příkazů souvisejících s Azure Data Lake Store můžete použít k:
 - [Procházení prostředků Azure Data Lake Store](#list-the-storage-path) 
 - [Náhled souboru Azure Data Lake Store](#preview-the-storage-file) 
 - Nahrání souboru přímo do Úložiště datových jezer Azure v kódu VS
 - Stažení souboru přímo z Azure Data Lake Store v kódu VS

### <a name="list-the-storage-path"></a>Vypsat cestu úložiště 

**Seznam cesty úložiště prostřednictvím palety příkazů**

1. Klepněte pravým tlačítkem myši na editor skriptů a vyberte příkaz **ADL: List Path**.
2. Vyberte složku v seznamu nebo vyberte **Zadat cestu** nebo **Procházet z kořenové cesty**. (Jako příklad používáme **Enter a path.)** 
3. Vyberte svůj účet Data Lake Analytics.
4. Vyhledejte nebo zadejte cestu ke složce úložiště (například /output/).  

Paleta příkazů obsahuje informace o cestě založené na vašich položkách.

![Výsledky cesty úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Pohodlnější způsob, jak vypsat relativní cestu, je prostřednictvím místní nabídky.

**Seznam cesty úložiště v místní nabídce**

Klepněte pravým tlačítkem myši na řetězec cesty a vyberte **příkaz Cesta k seznamu**.

!["Cesta seznamu" v místní nabídce](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Náhled souboru úložiště

1. Klikněte pravým tlačítkem myši na editor skriptů a vyberte **ADL: Náhled souboru**.
2. Vyberte svůj účet Data Lake Analytics. 
3. Zadejte cestu k souboru úložiště Azure (například /output/SearchLog.txt). 

Soubor se otevře v kódu VS.

![Kroky a výsledek pro zobrazení náhledu souboru úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Dalším způsobem, jak zobrazit náhled souboru, je prostřednictvím místní nabídky na úplné cestě souboru nebo relativní cesty souboru v editoru skriptů. 

### <a name="upload-a-file-or-folder"></a>Nahrání souboru nebo složky

1. Klepněte pravým tlačítkem myši na editor skriptů a vyberte **příkaz Nahrát soubor** nebo **Nahrát složku**.
2. Pokud jste vybrali **Nahrát soubor,** zvolte jeden nebo více souborů nebo zvolte celou složku, pokud jste vybrali **Možnost Nahrát složku**. Potom vyberte **Nahrát**. 
3. Vyberte složku úložiště v seznamu nebo vyberte **Zadat cestu** nebo **Procházet z kořenové cesty**. (Jako příklad používáme **Enter a path.)** 
4. Vyberte svůj účet Data Lake Analytics. 
5. Vyhledejte nebo zadejte cestu ke složce úložiště (například /output/). 
6. Chcete-li zadat cíl nahrávání, vyberte **Vybrat aktuální složku.**

![Kroky a výsledek pro nahrání souboru nebo složky](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Dalším způsobem, jak nahrávat soubory do úložiště, je prostřednictvím místní nabídky na úplné cestě souboru nebo relativní cesty souboru v editoru skriptů.

Stav [nahrávání](#check-storage-tasks-status)můžete sledovat .


### <a name="download-a-file"></a>Stažení souboru 
Soubor můžete stáhnout pomocí příkazu **ADL: Stáhnout soubor** nebo **ADL: Stáhnout soubor (Upřesnit)**.

**Stažení souboru pomocí příkazu ADL: Stáhnout soubor (upřesnit)**
1. Klepněte pravým tlačítkem myši na editor skriptů a vyberte **příkaz Stáhnout soubor (Upřesnit).**
2. Kód VS zobrazí soubor JSON. Můžete zadat cesty k souborům a stáhnout více souborů současně. Pokyny jsou zobrazeny v okně **Výstup.** Chcete-li pokračovat ke stažení souboru nebo souborů, uložte soubor JSON (Ctrl+S).

    ![Soubor JSON s cestami ke stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

V okně **Výstup** se zobrazí stav stahování souboru.

![Okno Výstupu se stavem stahování](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Stav [stahování](#check-storage-tasks-status)můžete sledovat .

**Stažení souboru pomocí příkazu ADL: Stáhnout soubor**

1. Klepněte pravým tlačítkem myši na editor skriptů, vyberte **stáhnout soubor**a pak vdialogovéc **Vybrat složku** vyberte cílovou složku.
2. Vyberte složku v seznamu nebo vyberte **Zadat cestu** nebo **Procházet z kořenové cesty**. (Jako příklad používáme **Enter a path.)** 
3. Vyberte svůj účet Data Lake Analytics. 
4. Vyhledejte nebo zadejte cestu ke složce úložiště (například /output/) a pak zvolte soubor, který chcete stáhnout.

![Kroky a výsledek pro stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Dalším způsobem, jak stahovat soubory úložiště, je prostřednictvím místní nabídky na úplné cestě souboru nebo relativní cesty souboru v editoru skriptů.

Stav [stahování](#check-storage-tasks-status)můžete sledovat .

### <a name="check-storage-tasks-status"></a>Kontrola stavu úloh úložiště
Stav nahrávání a stahování se zobrazí na stavovém řádku. Vyberte stavový řádek a stav se zobrazí na kartě **VÝSTUP.**

![Stavový řádek a podrobnosti výstupu](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrace s Azure Data Lake Analytics z průzkumníka

Po přihlášení jsou všechna předplatná pro váš účet Azure uvedená v levém podokně v části **AZURE DATALAKE**. 

![Průzkumník datových jezer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigace metadat Data Lake Analytics

Rozšiřte své předplatné Azure. V uzlu **U-SQL Databases** můžete procházet databázi U-SQL a zobrazit složky, jako **jsou schémata**, **pověření**, **sestavení**, **tabulky**a **index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Správa entit metadat Data Lake Analytics

Rozbalte **u-SQL databáze**. Databázi, schéma, tabulku, typ tabulky, rejstřík nebo statistiku můžete vytvořit tak, že kliknete pravým tlačítkem myši na odpovídající uzel a v místní nabídce vyberete **skript, který chcete vytvořit.** Na otevřené stránce skriptu upravte skript podle svých potřeb. Potom úlohu odešlete tak, že na něj kliknete pravým tlačítkem myši a vyberete **ADL: Odeslat úlohu**. 

Po dokončení vytváření položky klikněte pravým tlačítkem myši na uzel a pak vyberte **Aktualizovat,** chcete-li položku zobrazit. Položku můžete odstranit také tak, že na ní klepnete pravým tlačítkem myši a vyberete **možnost Odstranit**.

![Příkaz "Skript k vytvoření" v místní nabídce v průzkumníku Datového jezera](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Stránka skriptu pro novou položku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrace sestavení Data Lake Analytics

Sestavení můžete zaregistrovat v odpovídající databázi klepnutím pravým tlačítkem myši na uzel **Sestavení** a výběrem **příkazu Register assembly**.

![Příkaz "Registrovat sestavení" v místní nabídce uzlu Sestavení](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrace s Azure Data Lake Store z průzkumníka

Přejděte do **obchodu Data Lake Store**:

- V místní nabídce můžete klepnout pravým tlačítkem myši na uzel složky a potom použít příkazy **Aktualizovat**, **Odstranit**, **Nahrát**, **Nahrát ,** **Nahrát, Zkopírovat relativní cestu**a Kopírovat **úplnou cestu.**

   ![Příkazy místní nabídky pro uzel složky v průzkumníku datového jezera](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Můžete klepnout pravým tlačítkem myši na uzel souboru a potom použít příkazy **Náhled**, **Stáhnout**, **Odstranit**, **Vytvořit extraHŤ** (k dispozici pouze pro soubory CSV, TSV a TXT), **Kopírovat relativní cestu**a **Kopírovat úplnou cestu** v místní nabídce.

   ![Příkazy místní nabídky pro uzel souboru v průzkumníku datového jezera](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrace s úložištěm objektů blob Azure z průzkumníka

Přejděte do úložiště objektů Blob:

- Můžete klepnout pravým tlačítkem myši na uzel kontejneru objektů blob a potom použít příkazy **Aktualizovat**, **Odstranit kontejner objektů blob**a Odeslat objekt **blob** v místní nabídce.

   ![Příkazy místní nabídky pro uzel kontejneru objektů blob v části Úložiště objektů blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Můžete kliknout pravým tlačítkem myši na uzel složky a potom použít příkazy **Aktualizovat** a **nahrát objekt Blob** v místní nabídce.

   ![Příkazy místní nabídky pro uzel složky v části Úložiště objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Můžete klepnout pravým tlačítkem myši na uzel souboru a potom použít příkazy **Náhled/Úprava**, **Stáhnout**, **Odstranit**, **Vytvořit extraHŤ** (k dispozici pouze pro soubory CSV, TSV a TXT), **Kopírovat relativní cestu**a Kopírovat **úplnou cestu** v místní nabídce.

    ![Příkazy místní nabídky pro uzel souboru v části Úložiště objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otevření průzkumníka datového jezera na portálu
1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů.
2. Zadejte **Open Web Azure Storage Explorer** nebo klikněte pravým tlačítkem myši na relativní cestu nebo úplnou cestu v editoru skriptů a pak vyberte Otevřít Web Azure Storage **Explorer**.
3. Vyberte účet Data Lake Analytics.

Nástroje datového jezera otevřou cestu úložiště Azure na webu Azure Portal. Cestu můžete najít a zobrazit náhled souboru z webu.

## <a name="additional-features"></a>Další funkce

Nástroje data lake pro kód VS podporují následující funkce:

-   **Automatické dokončování technologie IntelliSense**: Návrhy se zobrazují v automaticky otevíraných oknech kolem položek, jako jsou klíčová slova, metody a proměnné. Různé ikony představují různé typy objektů:

    - Datový typ Scala
    - Komplexní datový typ
    - Vestavěné UDT
    - Kolekce a třídy rozhraní .NET
    - Výrazy jazyka C#
    - Vestavěné C# UDFs, UDO a UDAAGs 
    - Funkce U-SQL
    - U-SQL okenní funkce
 
    ![Typy objektů IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Automatické dokončování technologie IntelliSense v metadatech Data Lake Analytics**: Nástroje datového jezera stahuje informace o metadatech Data Lake Analytics místně. Funkce IntelliSense automaticky naplní objekty z metadat Data Lake Analytics. Tyto objekty zahrnují databáze, schéma, tabulka, zobrazení, funkce s hodnotou tabulky, procedury a sestavení jazyka C#.
 
    ![Metadata technologie IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Značka chyb technologie IntelliSense**: Nástroje datového jezera podtrhávají chyby při úpravách u u-SQL a C#. 
-   **Zvýraznění syntaxe**: Nástroje datového jezera používají barvy k odlišení položek, jako jsou proměnné, klíčová slova, datové typy a funkce. 

    ![Syntaxe s různými barvami](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Doporučujeme upgradovat na Nástroje datového jezera Azure pro Visual Studio verze 2.3.3000.4 nebo novější. Předchozí verze jsou zastaralé a nejsou už k dispozici ke stažení.  
   
## <a name="next-steps"></a>Další kroky
- [Vývoj U-SQL s Pythonem, R a C Sharpem pro Azure Data Lake Analytics v kódu VS](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Místní spuštění u-SQL a místní ladění s kódem sady Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Kurz: Začínáme s Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Kurz: Vývoj skriptů U-SQL pomocí nástrojů Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
