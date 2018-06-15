---
title: Pomocí nástroje Azure Data Lake pro Visual Studio Code
description: Další informace o použití nástroje Azure Data Lake pro Visual Studio Code pro vytvoření, testování a spouštění skriptů U-SQL.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 79cd1a04c99891e5146ad20cfd36b8bd4fe4d893
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261480"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Pomocí nástroje Azure Data Lake pro Visual Studio Code

V tomto článku zjistěte, jak můžete používat nástroje Azure Data Lake pro Visual Studio Code (kód VS) k vytváření, testování a spouštění skriptů U-SQL. Informace jsou také obsaženy v následujícím videu:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Požadavky

Nástroje Azure Data Lake pro VS Code podporuje Windows, Linux a systému MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Pro systému MacOS a Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Monofonní 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Instalace nástroje Azure Data Lake

Po instalaci požadavky, můžete nainstalovat nástroje Azure Data Lake pro VS Code.

**Instalace nástroje Azure Data Lake**

1. Otevřete Visual Studio Code.
2. Vyberte **rozšíření** v levém podokně. Zadejte **nástrojů Azure Data Lake** do vyhledávacího pole.
3. Vyberte **nainstalovat** vedle **nástrojů Azure Data Lake**. 

   ![Výběr instalace nástrojů Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Za několik sekund **nainstalovat** tlačítko se změní na **opětovného načtení**.
4. Vyberte **opětovného načtení** aktivovat **nástrojů Azure Data Lake** rozšíření.
5. Vyberte **opětovného načtení okno** k potvrzení. Můžete zobrazit **nástrojů Azure Data Lake** v **rozšíření** podokně.

 
## <a name="activate-azure-data-lake-tools"></a>Aktivovat nástrojů Azure Data Lake
Vytvořte soubor .usql nebo otevře existující soubor .usql aktivovat rozšíření. 


## <a name="work-with-u-sql"></a>Práce s jazykem U-SQL

Pro práci s jazykem U-SQL, třeba otevřete soubor U-SQL nebo složku.

**Chcete-li spustit ukázkový skript**

Spusťte příkaz palety (Ctrl + Shift + P) a zadejte **ADL: Otevřete ukázkový skript**. Otevře další instanci této ukázce. Můžete také upravit, konfigurovat a odeslat skript v této instanci.

**Otevření složky pro váš projekt U-SQL**

1. Visual Studio Code, vyberte **soubor** nabídce a potom vyberte **otevřít složku**.
2. Zadejte složku a potom vyberte **vyberte složku**.
3. Vyberte **soubor** nabídce a potom vyberte **nový**. Soubor bez názvu-1 se přidá do projektu.
4. Zadejte následující kód v souboru bez názvu-1:

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

    Tento skript vytvoří soubor departments.csv se některé dat obsažených ve složce/Output.

5. Uložte soubor jako **myUSQL.usql** ve složce otevřen.

**Kompilace skript U-SQL**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: kompilace skriptu**. Výsledky kompilace se zobrazí v **výstup** okno. Můžete také klikněte pravým tlačítkem na soubor skriptu a potom vyberte **ADL: kompilace skriptu** zkompilovat úlohy U-SQL. Výsledek kompilace se zobrazí v **výstup** podokně.
 
**Odeslat skript U-SQL**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: odeslat úlohu**. Můžete také klikněte pravým tlačítkem na soubor skriptu a potom vyberte **ADL: odeslat úlohu**. 

Po odeslání úlohy U-SQL, odeslání protokolů se objeví v **výstup** okno v produktu VS Code. Zobrazení úloh se zobrazí v pravém podokně. Pokud odesílání úspěšné, zobrazí se příliš adresu URL úlohy. Adresa URL úlohu můžete otevřít ve webovém prohlížeči a sledovat stav v reálném čase úlohy. 

V zobrazení úlohy **Souhrn** karty, zobrazí se podrobnosti úlohy. Hlavní funkce patří znovu odeslat skript, duplicitní skript a otevřete na portálu. V zobrazení úlohy **DATA** kartě najdete vstupní soubory, výstupní soubory a soubory prostředků. Soubory si můžete stáhnout na místním počítači.

![Karta Souhrn v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Karta data v zobrazení úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Chcete-li nastavit výchozí kontext**

Můžete nastavit výchozí kontext, který má toto nastavení platí pro všechny soubory skriptů, pokud jste nenastavili parametry pro soubory jednotlivě.

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: nastavte výchozí kontext**. Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: nastavte výchozí kontext**.
3. Vyberte účet, databázi a schéma, které chcete. Toto nastavení se uloží do konfiguračního souboru xxx_settings.json.

   ![Účet, databázi a schéma nastaven jako výchozí kontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Chcete-li nastavit parametry skriptu**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: nastavte parametry skriptu**.
3. Otevření souboru xxx_settings.json s následujícími vlastnostmi:

   - **účet**: účet Azure Data Lake Analytics v rámci vašeho předplatného Azure, který je potřebný pro zkompilování a spuštění úloh U-SQL. Účet počítače je třeba nakonfigurovat před zkompilování a spuštění úloh U-SQL.
   - **databáze**: databáze v rámci vašeho účtu. Výchozí hodnota je **hlavní**.
   - **schéma**: schéma v databázi. Výchozí hodnota je **dbo**.
   - **optionalSettings**:
        - **Priorita**: priority rozsah je od 1 do 1000, kde 1 jako nejvyšší prioritou. Výchozí hodnota je **1000**.
        - **degreeOfParallelism**: paralelismus rozsah je od 1 do 150. Výchozí hodnota je maximální paralelismus v účtu Azure Data Lake Analytics povolen. 

   ![Obsah souboru JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Po uložení konfigurace, účet, databázi a informace o schématu zobrazí na stavovém řádku v levém horním rohu odpovídající soubor .usql Pokud nemáte výchozí kontext nastavení.

**Chcete-li nastavit Git ignorovat**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: Sada Git Ignorovat**.

   - Pokud nemáte **.gitIgnore** souboru v VS Code pracovní složka, soubor s názvem **.gitIgnore** se vytvoří ve složce. Čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) se přidají v souboru ve výchozím nastavení. V případě potřeby můžete nastavit další aktualizace.
   - Pokud již máte **.gitIgnore** souboru v VS Code pracovní složky, nástroj přidá čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) ve vaší **.gitIgnore** souboru pokud čtyři položky nebyly zahrnuty v souboru.

   ![Položky v souboru .gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Práce se soubory kódu na pozadí: C Sharp, Python a R

Nástroje Azure Data Lake podporuje více vlastní kódy. Pokyny najdete v tématu [vyvíjet U-SQL s Python, R a jazyka C Sharp pro Azure Data Lake Analytics v produktu VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Práce se sestaveními

Informace týkající se vývoje sestavení najdete v tématu [sestavení vyvíjet U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Nástroje Data Lake můžete pro registraci sestavení vlastní kód v katalogu Data Lake Analytics.

**Registrace sestavení**

Můžete zaregistrovat sestavení prostřednictvím **ADL: registrace sestavení** nebo **ADL: registraci sestavení (Upřesnit)** příkaz.

**Registrace přes ADL: příkaz registrace sestavení**
1.  Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2.  Zadejte **ADL: registrace sestavení**. 
3.  Zadejte cestu k místní sestavení. 
4.  Vyberte účet Data Lake Analytics.
5.  Vyberte databázi.

Portál je otevřen v prohlížeči a zobrazí proces registrace sestavení.  

Pohodlnější způsob, jak aktivovat **ADL: registrace sestavení** příkaz je kliknete pravým tlačítkem na soubor .dll v Průzkumníku souborů. 

**Registrace přes ADL: příkaz registraci sestavení (rozšířené)**
1.  Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2.  Zadejte **ADL: registrace sestavení (rozšířené)**. 
3.  Zadejte cestu k místní sestavení. 
4.  Zobrazí se souboru JSON. Zkontrolujte a v případě potřeby upravit závislosti sestavení a parametry prostředků. Pokyny, které se zobrazují v **výstup** okno. Chcete-li přejít k registraci sestavení, uložení (Ctrl + S) souboru JSON.

    ![Soubor JSON s parametry prostředků a závislostí sestavení](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake nástroje Automatické jestli má knihovna DLL všechny závislosti sestavení. Závislosti jsou zobrazeny v souboru JSON po jejich jste zjištěna. 
>- Můžete nahrát vašich prostředků knihovny DLL (například TXT, .png a CSV) jako součást registrace sestavení. 

Jiný způsob, jak aktivovat **ADL: registraci sestavení (Upřesnit)** příkaz je kliknete pravým tlačítkem na soubor .dll v Průzkumníku souborů. 

Následující kód U-SQL ukazuje, jak volat sestavení. V ukázce název sestavení je *testování*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Pomocí U-SQL místní spuštění a místní ladění pro uživatele systému Windows
U-SQL místní spuštění testů místní data a ověří váš skript místně před kódu je publikována ve službě Data Lake Analytics. Místní ladění funkce vám pomůže dokončit následující úkoly před kódu je odeslána do Data Lake Analytics: 
- Ladění vaší C# kódu. 
- Krokovat kód. 
- Ověřte váš skript místně.

Pokyny v místním spuštění a místní ladění najdete v tématu [U-SQL místní spuštění a místní ladění s Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Připojení k Azure

Předtím, než můžete zkompilování a spuštění skriptů U-SQL v Data Lake Analytics, je nutné připojit ke svému účtu Azure.

<b id="sign-in-by-command">Pro připojení k Azure pomocí příkazu</b>

1.  Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2.  Zadejte **ADL: přihlášení**. Přihlašovací údaje se zobrazí vpravo dole.

    ![Zadání příkazu přihlášení](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Oznámení o přihlašování a ověřování](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Vyberte **zkopírujte & Otevřít** otevřete [přihlašovací webovou stránku](https://aka.ms/devicelogin). Vložte kód do pole a potom vyberte **pokračovat**.

    ![Přihlašovací webovou stránku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Postupujte podle pokynů se přihlásit z webové stránky. Když jste připojení, název účtu Azure, zobrazí se na stavovém řádku v levém dolním rohu okna VS Code. 

> [!NOTE] 
>- Nástroje data Lake automaticky přihlašovat při příštím Pokud nemáte Odhlásit se.
>- Pokud váš účet má dvě úrovně povoleno, doporučujeme použít ověřování phone místo pomocí PIN kódu.


Chcete-li odhlásit, zadejte příkaz **ADL: odhlášení**.

**Pro připojení k Azure z Průzkumníka**

Rozbalte položku **AZURE DATALAKE**, vyberte **přihlaste k Azure**a pak postupujte podle kroku 3 a 4 z [pro připojení k Azure pomocí příkazu](#sign-in-by-command).

!["Přihlášení k Azure" výběr v Průzkumníku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Můžete se odhlásit z Průzkumníka. Odhlásit se, najdete v tématu [pro připojení k Azure pomocí příkazu](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Vytvořit skript extrakce 
Můžete vytvořit skript extrakce pro soubory .txt, .csv a TSV pomocí příkazu **ADL: vytvoření skriptu EXTRAHOVAT** nebo z Průzkumníka Azure Data Lake.

**Chcete-li vytvořit skript extrakce pomocí příkazu**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz, a zadejte **ADL: vytvoření skriptu EXTRAHOVAT**.
2. Zadejte úplnou cestu pro soubor s Azure Storage a vyberte klávesu Enter.
3. Vyberte jeden účet.
4. Pro soubor .txt vyberte oddělovač extrahujte soubor. 

![Proces extrakce skript pro](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Extrakce skript je generována hodnot. Pro skript, který nelze zjistit sloupce vyberte jeden z následujících dvou možností. V opačném případě bude vygenerován pouze jeden skript.

![Výsledek skript extrakce](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Chcete-li vytvořit skript extrakci z Průzkumníka**

Jiný způsob, jak vytvořit skript extrakce je prostřednictvím nabídky (místní) klikněte pravým tlačítkem na CSV, TSV nebo souboru .txt v Azure Data Lake Store nebo Azure Blob storage.

![Příkaz "Vytvořit výpis skriptu" z místní nabídky](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrace s Azure Data Lake Analytics prostřednictvím příkazu

Prostředky Azure Data Lake Analytics seznam účtů, metadata přístup a zobrazení úloh analytics můžete přistupovat. 

**K zobrazení seznamu účtů Azure Data Lake Analytics v rámci vašeho předplatného Azure**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2. Zadejte **ADL: seznamu účtů**. Účty se zobrazují v **výstup** podokně.

**Chcete-li získat přístup k metadatům Azure Data Lake Analytics**

1.  Vyberte Ctrl + Shift + P a potom zadejte **ADL: seznamu tabulek**.
2.  Vyberte jeden z účtů Data Lake Analytics.
3.  Vyberte jednu z databáze Data Lake Analytics.
4.  Vyberte jeden z schémat. Zobrazí se seznam tabulek.

**Chcete-li zobrazit úlohy Azure Data Lake Analytics**
1.  Otevřete příkaz palety (Ctrl + Shift + P) a vyberte **ADL: Zobrazit úlohy**. 
2.  Vyberte Data Lake Analytics nebo místní účet. 
3.  Počkejte, než pro seznam úloh, než se objeví pro účet.
4.  Ze seznamu vyberte úlohu. Nástroje data Lake otevře zobrazení úlohy v pravém podokně a zobrazí některé informace ve výstupu VS Code.

    ![Seznam úloh](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrace s Azure Data Lake Store prostřednictvím příkazu

Můžete použít příkazy související s Azure Data Lake Store k:
 - [Procházet prostředky Azure Data Lake Store](#list-the-storage-path) 
 - [Náhled souboru Azure Data Lake Store](#preview-the-storage-file) 
 - [Nahrajte soubor přímo do Azure Data Lake Store v produktu VS Code](#upload-file-or-folder)
 - [Stáhněte soubor přímo z Azure Data Lake Store v produktu VS Code](#download-file)

### <a name="list-the-storage-path"></a>Seznam cesta k úložišti 

**Do seznamu Cesta k úložišti prostřednictvím příkazu palety**

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: cesta seznamu**.
2. Vyberte složku, v seznamu, nebo vyberte **zadejte cestu** nebo **procházet z kořenovou cestu**. (Používáme **zadejte cestu** jako příklad.) 
3. Vyberte svůj účet Data Lake Analytics.
4. Vyhledejte nebo zadejte cestu ke složce úložiště (například/výstup /).  

Příkaz palety uvádí informace o cestě hodnot.

![Úložiště výsledků cesta](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Pohodlnější způsob, jak zobrazit seznam relativní cesta je prostřednictvím místní nabídky.

**Do seznamu Cesta k úložišti prostřednictvím místní nabídky**

Klikněte pravým tlačítkem na řetězec cesty a vyberte **seznamu Cesta**.

!["Seznamu cestu" v místní nabídce](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Náhled souboru úložiště

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: náhled souboru**.
2. Vyberte svůj účet Data Lake Analytics. 
3. Zadejte cestu k souboru Azure Storage (například /output/SearchLog.txt). 

Soubor se otevře v produktu VS Code.

![Kroky a výsledek pro náhled souboru úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Jiný způsob, jak zobrazit náhled souboru je prostřednictvím místní nabídky na úplnou cestu souboru nebo relativní cestu souboru v editoru skriptů. 

### <a name="upload-a-file-or-folder"></a>Nahrání souboru nebo složky

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **nahrát soubor** nebo **nahrát složky**.
2. Vyberte jeden soubor nebo více souborů, pokud jste vybrali **nahrát soubor**, nebo vyberte celou složku, pokud jste vybrali **nahrát složky**. Potom vyberte **nahrát**. 
3. V seznamu vyberte složku, úložiště, nebo vyberte **zadejte cestu** nebo **procházet z kořenovou cestu**. (Používáme **zadejte cestu** jako příklad.) 
4. Vyberte svůj účet Data Lake Analytics. 
5. Vyhledejte nebo zadejte cestu ke složce úložiště (například/výstup /). 
6. Vyberte **zvolte aktuální složce** k určení cílového umístění pro ukládání.

![Kroky a výsledek pro nahrání souboru nebo složky](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Dalším způsobem k nahrání souborů do úložiště je prostřednictvím místní nabídky na úplnou cestu souboru nebo relativní cestu souboru v editoru skriptů.

Můžete [monitorovat stav nahrávání](#check-storage-tasks-status).


### <a name="download-a-file"></a>Stažení souboru 
Soubor můžete stáhnout pomocí příkazu **ADL: stáhnout soubor** nebo **ADL: Stažení souboru (Upřesnit)**.

**Ke stažení souboru prostřednictvím ADL: stáhnout soubor (Upřesnit) – příkaz**
1. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **stáhnout soubor (Upřesnit)**.
2. VS Code zobrazí soubor JSON. Můžete zadat cesty k souborům a stahovat víc souborů současně. Pokyny, které se zobrazují v **výstup** okno. Chcete-li pokračovat ke stažení na soubor nebo soubory, uložení (Ctrl + S) souboru JSON.

    ![Soubor JSON s cesty ke stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**Výstup** okně se zobrazí stav stažení souboru.

![Výstup – okno se stavem stahování](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Můžete [monitorovat stav stahování](#check-storage-tasks-status).

**Ke stažení souboru prostřednictvím ADL: stáhnout soubor – příkaz**

1. Klikněte pravým tlačítkem na editor skriptů, vyberte **stáhnout soubor**a potom vyberte cílovou složku z **vyberte složku** dialogové okno.
2. Vyberte složku, v seznamu, nebo vyberte **zadejte cestu** nebo **procházet z kořenovou cestu**. (Používáme **zadejte cestu** jako příklad.) 
3. Vyberte svůj účet Data Lake Analytics. 
4. Vyhledejte nebo zadejte cestu ke složce úložiště (například/výstup /) a potom zvolte soubor ke stažení.

![Kroky a výsledek pro stažení souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Jiný způsob, jak stáhnout soubory úložiště je prostřednictvím místní nabídky na úplnou cestu souboru nebo relativní cestu souboru v editoru skriptů.

Můžete [monitorovat stav stahování](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Zkontrolujte stav úlohy úložiště
Nahrávání a stahování stav se zobrazí na stavovém řádku. Vyberte stavový řádek, a pak se zobrazí stav na **výstup** kartě.

![Stavový řádek a výstupní podrobnosti](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrace s Azure Data Lake Analytics z Průzkumníka

Po přihlášení, jsou všechna předplatná pro váš účet Azure uvedené v levém podokně v části **AZURE DATALAKE**. 

![Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metadata navigace

Rozbalte vašeho předplatného Azure. V části **U-SQL databáze** uzlu, můžete procházet U-SQL database a složek zobrazení, jako **schémata**, **pověření**, **sestavení**, **Tabulky**, a **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Správa entit metadata data Lake Analytics

Rozbalte položku **U-SQL databáze**. Můžete vytvořit databázi, schéma, tabulka, typ tabulky, index nebo statistiku pravým tlačítkem myši na odpovídající uzel a potom výběrem **skript pro vytvoření** v místní nabídce. Na stránce otevřenou skript upravte skript podle svých potřeb. Klikněte pravým tlačítkem myši a výběrem odeslání úlohy **ADL: odeslat úlohu**. 

Po dokončení vytvoření položky, klikněte pravým tlačítkem na uzel a potom vyberte **aktualizovat** zobrazíte položky. Můžete také odstranit položku pravým tlačítkem myši a potom výběrem **odstranit**.

![Příkaz "Skript pro vytvoření" v místní nabídce v Průzkumníku Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Skript stránky pro novou položku](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrace sestavení data Lake Analytics

Sestavení můžete zaregistrovat v databázi odpovídající kliknutím pravým tlačítkem myši **sestavení** uzel a pak vybrat **registrace sestavení**.

!["Registrace sestavení" příkaz v místní nabídce uzlu sestavení](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Z Průzkumníka integraci s Azure Data Lake Store

Přejděte do **Data Lake Store**:

- Můžete klikněte pravým tlačítkem na uzel složku a potom pomocí **aktualizovat**, **odstranit**, **nahrát**, **nahrát složky**, **kopie Relativní cesta**, a **Kopírovat úplnou cestu** příkazy v místní nabídce.

   ![Příkazy nabídky zástupce pro uzel složky v Průzkumníku Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Můžete klikněte pravým tlačítkem na uzel souboru a pak použít **Preview**, **Stáhnout**, **odstranit**, **vytvoření skriptu EXTRAHOVAT** (dostupné pouze pro sdílený svazek clusteru TSV a soubory TXT), **relativní cesta kopie**, a **Kopírovat úplnou cestu** příkazy v místní nabídce.

   ![Příkazy nabídky zástupce pro uzel soubor v Průzkumníku Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrace s úložištěm Azure Blob z Průzkumníka

Přejděte do úložiště objektů Blob:

- Můžete klikněte pravým tlačítkem na uzel kontejner objektů blob a potom pomocí **aktualizovat**, **odstranit kontejner objektů Blob**, a **nahrát objekt Blob** příkazy v místní nabídce.

   ![Příkazy nabídky zástupce pro uzel kontejner objektů blob v úložišti objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Můžete klikněte pravým tlačítkem na uzel složku a potom pomocí **aktualizovat** a **nahrát objekt Blob** příkazy v místní nabídce.

   ![Příkazy nabídky zástupce pro uzel složku v úložišti objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Můžete klikněte pravým tlačítkem na uzel souboru a pak použít **Preview či upravit**, **Stáhnout**, **odstranit**, **vytvoření skriptu EXTRAHOVAT** (k dispozici pouze u sdíleného svazku clusteru, TSV a TXT souborů) **relativní cesta kopie**, a **Kopírovat úplnou cestu** příkazy v místní nabídce.

    ![Příkazy nabídky zástupce pro uzel souboru v úložišti objektů Blob](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Otevřete Průzkumníka Data Lake na portálu
1. Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2. Zadejte **otevřete Web Azure Storage Explorer** nebo klikněte pravým tlačítkem na relativní cestu nebo úplnou cestu v editoru skript a potom vyberte **otevřete Web Azure Storage Explorer**.
3. Vyberte účet Data Lake Analytics.

Nástroje data Lake otevře cesta k úložišti Azure na portálu Azure. Můžete najít cestu a náhled souboru z webu.

## <a name="additional-features"></a>Další funkce

Nástroje data Lake pro VS Code podporuje následující funkce:

-   **Funkce automatického dokončování IntelliSense**: návrhy zobrazí v automaticky otevíraná okna kolem položky, jako jsou klíčová slova, metod a proměnné. Různé ikony představují různé typy objektů:

    - Datový typ Scala
    - Komplexní datový typ
    - Předdefinované UDT
    - Třídy a .NET collection
    - Výrazy jazyka C#
    - Předdefinované C# UDF, UDO a UDAAGs 
    - Funkce U-SQL
    - U-SQL oddílová funkce
 
    ![Typy objektů IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Funkce automatického dokončování IntelliSense v Data Lake Analytics metadata**: nástrojů Data Lake stáhne informace metadat Data Lake Analytics místně. Funkci IntelliSense automaticky naplní objekty z metadat Data Lake Analytics. Tyto objekty zahrnují databáze, schéma, tabulka, zobrazení, funkce vracející tabulku, postupy a sestavení C#.
 
    ![IntelliSense metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Chyba značky IntelliSense**: nástrojů Data Lake podtrhne úpravy chyby U-SQL a C#. 
-   **Syntaxe označuje**: nástrojů Data Lake používá barvy odlišit od položky, jako jsou proměnné, klíčová slova, datové typy a funkce. 

    ![Syntaxe pomocí různých barev](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Doporučujeme, abyste upgradu na nástroje Azure Data Lake pro Visual Studio verze 2.3.3000.4 nebo novější. Předchozí verze již nejsou k dispozici ke stažení a jsou nyní zastaralé.  
   
## <a name="next-steps"></a>Další postup
- [Vývoj U-SQL s Python, R a jazyka C Sharp pro Azure Data Lake Analytics v produktu VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL místní spuštění a místní ladění s kódem jazyka Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Kurz: Začínáme s Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Kurz: Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
