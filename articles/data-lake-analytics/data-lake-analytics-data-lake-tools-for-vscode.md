---
title: 'Nástroje Azure Data Lake: Nástroje použití Azure Data Lake pro Visual Studio Code | Microsoft Docs'
description: 'Další informace o použití nástroje Azure Data Lake pro Visual Studio Code pro vytvoření, testování a spouštění skriptů U-SQL. '
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: ''
author: Jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: jejiang
ms.openlocfilehash: fcd821c91a8c94792eeed83940abe1c72d0b2fb8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Pomocí nástroje Azure Data Lake pro Visual Studio Code

Další nástroje Azure Data Lake pro Visual Studio Code (kód VS) k vytváření, testování a spouštět skripty U-SQL. Informace jsou také obsaženy v následujícím videu:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Požadavky

Nástroje Azure Data Lake pro VSCode podporuje Windows, Linux a systému MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

Pro systému MacOS a Linux:
- [.NET core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Monofonní 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Instalace nástrojů Data Lake

Po instalaci požadavky, můžete nainstalovat nástroje Data Lake pro VS Code.

**Chcete-li nainstalovat nástroje Data Lake**

1. Otevřete Visual Studio Code.
2. Klikněte na tlačítko **rozšíření** v levém podokně. Zadejte **Azure Data Lake** do vyhledávacího pole.
3. Klikněte na tlačítko **nainstalovat** vedle **nástroje Azure Data Lake**. Za několik sekund **nainstalovat** tlačítko se změní na **opětovného načtení**.
4. Klikněte na tlačítko **opětovného načtení** aktivovat **nástroje Azure Data Lake** rozšíření.
5. Klikněte na tlačítko **opětovného načtení okno** k potvrzení. Můžete zobrazit **nástroje Azure Data Lake** v podokně rozšíření.

    ![Nástroje data Lake pro Visual Studio rozšíření kódu podokno](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Aktivovat nástrojů Azure Data Lake
Vytvořte nový soubor .usql nebo otevře existující soubor .usql aktivovat rozšíření. 

## <a name="open-the-sample-script"></a>Otevřete ukázkový skript
Spusťte příkaz palety (Ctrl + Shift + P) a zadejte **ADL: Otevřete ukázkový skript**. Otevře další instanci této ukázce. Můžete také upravit, konfigurovat a odeslat skript v této instanci.

## <a name="work-with-u-sql"></a>Práce s jazykem U-SQL

Je nutné otevřít soubor U-SQL nebo složku pro práci s jazykem U-SQL.

**Otevření složky pro váš projekt U-SQL**

1. Visual Studio Code, vyberte **soubor** nabídce a potom vyberte **otevřít složku**.
2. Zadejte složku a potom vyberte **vyberte složku**.
3. Vyberte **soubor** nabídce a potom vyberte **nový**. Soubor bez názvu-1 se přidá do projektu.
4. Zadejte následující kód do souboru bez názvu-1:

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
2. Zadejte **ADL: odeslat úlohu**.  Můžete také klikněte pravým tlačítkem na soubor skriptu a potom vyberte **ADL: odeslat úlohu**. 

 Po odeslání úlohy U-SQL, odeslání protokolů se objeví v **výstup** okno v produktu VS Code. Zobrazení úloh se zobrazí v pravém podokně. Pokud je odesílání úspěšné, úlohy adresa URL se zobrazí také. Adresa URL úlohu můžete otevřít ve webovém prohlížeči sledovat stav úlohy v reálném čase. Na kartě úlohy zobrazení souhrnu se zobrazí podrobnosti úlohy. Hlavní funkce patří odešlete skript, duplicitní skriptu, otevřete v portálu. Na kartě Data zobrazení úlohy najdete soubory vstupní, výstupní soubory prostředků. Soubory si můžete stáhnout na místním počítači.

   ![Nástroje data Lake pro Visual Studio Code konfiguračního souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![Nástroje data Lake pro Visual Studio Code konfiguračního souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Nastavit výchozí kontext**

 Můžete nastavit výchozí kontext pro toto nastavení platí pro všechny soubory skriptů, pokud jste nenastavili parametry pro soubor v uvedeném pořadí.

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: nastavte výchozí kontext**.
3. Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: nastavte výchozí kontext**, zvolte účet, databázi a schéma, které chcete. Toto nastavení se uloží do xxx_settings.json konfiguračního souboru.

    ![Nástroje data Lake pro Visual Studio Code konfiguračního souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Nastavit parametry skriptu**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: nastavte parametry skriptu**.
3. otevření souboru xxx_settings.JSON s následujícími vlastnostmi:

  - Účet: Účet Data Lake Analytics v rámci vašeho předplatného Azure, který je potřebný pro zkompilování a spuštění úloh U-SQL, takže je nutné nakonfigurovat účet počítače před kompilaci a spouštění úloh U-SQL.
    - Databáze: Databáze v rámci vašeho účtu. Výchozí hodnota je **hlavní**.
    - Schémat: Schéma v databázi. Výchozí hodnota je **dbo**.
    - Volitelné nastavení:
        - Priorita: Rozsah priority je od 1 do 1000 s 1 jako nejvyšší prioritou. Výchozí hodnota je **1000**.
        - Paralelismus: Paralelismus rozsah je od 1 do 150. Výchozí hodnota je maximální paralelismus v účtu Azure Data Lake Analytics povolen. 

        ![Nástroje data Lake pro Visual Studio Code konfiguračního souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > Po uložení konfigurace účet, databázi a informace o schématu se zobrazí na hlavním panelu v okraje v levém dolním rohu odpovídající soubor souboru .usql Pokud nemáte výchozí kontext nastavení.

**Ignorovat sadu Git**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2. Zadejte **ADL: Sada Git Ignorovat**.

    - Pokud nemáte **.gitIgnore** souboru v VSCode pracovní složka, soubor s názvem **.gitIgnor** se vytvoří ve složce. Čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) jsou přidány do souboru ve výchozím nastavení. Aktualizace můžete nastavit další v případě potřeby.
    - Pokud již máte **.gitIgnore** souboru v VSCode pracovní složky, nástroj přidá čtyři položky (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) do vaší **.gitIgnore** souboru pokud čtyři položky nebyly zahrnuty v souboru.

    ![Nástroje data Lake pro Visual Studio Code konfiguračního souboru](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Použít Python, R a CSharp souboru kódu na pozadí
Azure Data Lake nástroj podporuje víc vlastní kódů, podle pokynů v tématu [vyvíjet U-SQL s Python, R a CSharp pro Azure Data Lake Analytics v VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Použití sestavení

Informace týkající se vývoje sestavení najdete v tématu [sestavení vyvíjet U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md).

Nástroje Data Lake můžete pro registraci sestavení vlastní kód v katalogu Data Lake Analytics.

**Registrace sestavení**

Můžete zaregistrovat sestavení prostřednictvím **ADL: registrace sestavení** nebo **ADL: registraci sestavení (Upřesnit)** příkazy.

**Registrace přes ADL: příkaz registrace sestavení**
1.  Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2.  Zadejte **ADL: registrace sestavení**. 
3.  Zadejte cestu k místní sestavení. 
4.  Vyberte účet Data Lake Analytics.
5.  Vyberte databázi.

Výsledky: Portál se otevře v prohlížeči a zobrazí proces registrace sestavení.  

Jiné pohodlný způsob, jak aktivovat **ADL: registrace sestavení** příkaz je kliknete pravým tlačítkem na soubor .dll v Průzkumníku souborů. 

**K registraci, když ADL: registraci sestavení (rozšířené)**
1.  Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2.  Zadejte **ADL: registrace sestavení (rozšířené)**. 
3.  Zadejte cestu k místní sestavení. 
4.  Zobrazí se souboru JSON. Zkontrolujte a v případě potřeby upravit závislosti sestavení a parametry prostředků. Pokyny, které se zobrazují v **výstup** okno. Chcete-li přejít k registraci sestavení, uložení (Ctrl + S) souboru JSON.

    ![Nástroje data Lake pro Visual Studio Code kódu](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- Závislosti sestavení: automatické nástrojů Azure Data Lake jestli knihovnu DLL nemá žádné závislosti. Po zjištění, zobrazí se závislostí v souboru JSON. 
   >- Prostředky: Jako součást registrace k sestavení můžete nahrát vašich prostředků knihovny DLL (například TXT, .png a CSV). 

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


## <a name="connect-to-azure"></a>Připojení k Azure

Předtím, než můžete zkompilování a spuštění skriptů U-SQL v Data Lake Analytics, je nutné připojit ke svému účtu Azure.

**Pro připojení k Azure**

1.  Vyberte Ctrl + Shift + P otevřete paletu příkaz. 
2.  Zadejte **ADL: přihlášení**. Hlavní oblasti, zobrazí se přihlašovací údaje.

    ![Nástroje data Lake pro Visual Studio Code příkaz palety](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![nástroje Data Lake pro Visual Studio Code zařízení přihlašovací informace](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Klikněte na tlačítko **zkopírujte & Otevřít** otevřete přihlašovací webovou stránku s adresou URL: https://aka.ms/devicelogin. Vložte kód **G567LX42V** do textového pole a pak vyberte **pokračovat**.

   ![Nástroje data Lake pro Visual Studio Code přihlášení vložte kód](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Postupujte podle pokynů se přihlásit z webové stránky. Když jste připojení, název účtu Azure se zobrazí na stavovém řádku v levém horním rohu **VS Code** okno. 

    > [!NOTE] 
    >- Nástroje data Lake příště přihlásí automaticky, pokud jste se zaregistrovali před, ale nebyly protokolují ještě.
    >- Pokud váš účet má dvě úrovně povoleno, doporučujeme použít ověřování phone místo pomocí PIN kódu.


Chcete-li odhlásit, zadejte příkaz **ADL: odhlášení**.

## <a name="list-your-data-lake-analytics-accounts"></a>Seznam svých účtů Data Lake Analytics

Chcete-li otestovat připojení, získáte seznam svých účtů Data Lake Analytics.

**Seznam účtů Data Lake Analytics v rámci vašeho předplatného Azure**

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2. Zadejte **ADL: seznamu účtů**. Účty se zobrazují v **výstup** podokně.


## <a name="access-the-data-lake-analytics-catalog"></a>Přístup ke katalogu Data Lake Analytics

Po připojení k Azure, můžete použít následující kroky přístup ke katalogu U-SQL.

**Chcete-li získat přístup k metadatům Azure Data Lake Analytics**

1.  Vyberte Ctrl + Shift + P a potom zadejte **ADL: seznamu tabulek**.
2.  Vyberte jeden z účtů Data Lake Analytics.
3.  Vyberte jednu z databáze Data Lake Analytics.
4.  Vyberte jeden z schémat. Zobrazí se seznam tabulek.

## <a name="view-data-lake-analytics-jobs"></a>Úlohy zobrazení Data Lake Analytics

**Chcete-li zobrazit úlohy Data Lake Analytics**
1.  Otevřete příkaz palety (Ctrl + Shift + P) a vyberte **ADL: Zobrazit úlohy**. 
2.  Vyberte Data Lake Analytics nebo místní účet. 
3.  Počkejte, než pro účet, který chcete zobrazit v seznamu úloh.
4.  Vyberte ze seznamu úloh úlohu, nástrojů Data Lake v pravém podokně se otevře zobrazení úloh a zobrazí některé informace v produktu VS Code **výstup**.

    ![Typy objektů nástroje data Lake pro Visual Studio kódu IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Integrace úložiště Azure Data Lake

Můžete použít příkazy související s Azure Data Lake Storage na:
 - Procházejte prostředky služby Azure Data Lake Storage. [Seznam cesta k úložišti](#list-the-storage-path). 
 - Zobrazte náhled souboru Azure Data Lake Storage. [Náhled souboru úložiště](#preview-the-storage-file). 
 - Nahrajte soubor přímo do Azure Data Lake Storage v produktu VS Code. [Nahrát soubor nebo složku](#upload-file-or-folder).
 - Stáhněte soubor přímo z Azure Data Lake Storage v produktu VS Code. [Stáhněte si soubor](#download-file).

### <a name="list-the-storage-path"></a>Seznam cesta k úložišti 

**Do seznamu Cesta k úložišti prostřednictvím příkazu palety**

Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: cesta seznamu**.

Vyberte složku, v seznamu, nebo klikněte na tlačítko **zadejte cestu** nebo **procházet z kořenového** (používá zadejte cestu jako příklad). -> Vyberte vaše **ADLA účet**. -> Přejděte nebo zadejte cestu ke složce úložiště (například: / výstup /). -> Seznamy palety příkaz založené na vámi zadaných informací o cestě.

![Nástroje data Lake pro Visual Studio Code seznam výsledků cesta úložiště](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Pohodlnější způsob, jak zobrazit seznam relativní cesta je prostřednictvím v místní nabídce klikněte pravým tlačítkem.

**Do seznamu Cesta k úložišti prostřednictvím klikněte pravým tlačítkem na**

Klikněte pravým tlačítkem na řetězec cesty k výběru **seznamu Cesta** pokračujte.

![Nástroje data Lake pro Visual Studio Code, klikněte pravým tlačítkem na kontextové nabídky](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>Náhled souboru úložiště

Klikněte pravým tlačítkem na editor skriptů a vyberte **ADL: náhled souboru**.

Vyberte vaše **ADLA účet**. -> Zadejte cestu souboru úložiště Azure (například /output/SearchLog.txt). -> Výsledek: soubor se otevře v VSCode.

   ![Nástroje data Lake pro Visual Studio Code náhled souboru výsledků](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Jiný způsob, jak zobrazit náhled souboru je prostřednictvím v místní nabídce na úplnou cestu souboru nebo relativní cestu souboru v editoru skriptů. 

### <a name="upload-file-or-folder"></a>Nahrání souboru nebo složky

1. Klikněte pravým tlačítkem na editor skriptů a vyberte **nahrát soubor** nebo **nahrát složky**.

2. Vyberte jeden soubor nebo více souborů, pokud vyberte nahrát soubor, nebo vyberte celou složku, pokud vyberte nahrát složky, poté klikněte na tlačítko **nahrát**. -> Vyberte v seznamu složku úložiště, nebo klikněte na tlačítko **zadejte cestu** nebo **procházet z kořenového** (používá zadejte cestu jako příklad). -> Vyberte vaše **ADLA účet**. -> Přejděte nebo zadejte cestu ke složce úložiště (například: / výstup /). -> klikněte na tlačítko **zvolte aktuální složce** k určení cílového umístění pro ukládání.

   ![Nástroje data Lake pro Visual Studio Code nahrát stav](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Dalším způsobem odesílání souborů do úložiště je prostřednictvím v místní nabídce na úplnou cestu souboru nebo relativní cestu souboru v editoru skriptů.

Jako současně, můžete sledovat [odesílání stav](#check-storage-tasks-status).


### <a name="download-file"></a>Stáhnout soubor 
Soubory si můžete stáhnout tak, že zadáte příkazy **ADL: stáhnout soubor** nebo **ADL: Stažení souboru (Upřesnit)**.

**Chcete-li stáhnout soubory, když ADL: Stažení souboru (Upřesnit)**
1. Klikněte pravým tlačítkem na editor skriptů a potom vyberte **stáhnout soubor (Upřesnit)**.
2. VS Code zobrazí soubor JSON. Můžete zadat cesty k souborům a stahovat víc souborů současně. Pokyny, které se zobrazují v **výstup** okno. Chcete-li pokračovat ke stažení souboru, uložte (Ctrl + S) souboru JSON.

    ![Nástroje data Lake pro Visual Studio Code stáhnout soubory pomocí konfigurace](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Výsledky: **výstup** okně se zobrazí stav nahrávání souboru.

    ![Nástroje data Lake pro Visual Studio Code stáhnout více souborů výsledky](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Jako současně, můžete sledovat [stahování stav](#check-storage-tasks-status).

**Ke stažení souborů, když ADL: stáhnout soubor**

1. Klikněte pravým tlačítkem na editor skriptů, vyberte **stáhnout soubor**a potom vyberte cílovou složku z **vyberte složku** dialogové okno.

2. Vyberte složku, v seznamu, nebo klikněte na tlačítko **zadejte cestu** nebo **procházet z kořenového** (používá zadejte cestu jako příklad). -> Vyberte vaše **ADLA účet**. -> Přejděte nebo zadejte cestu ke složce úložiště (například: / výstup /) -> vyberte soubor, který chcete stáhnout.

   ![Stav stahování nástroje data Lake pro Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Dalším způsobem stahování souborů úložiště je prostřednictvím v místní nabídce na úplnou cestu souboru nebo relativní cestu souboru v editoru skriptů.

Jako současně, můžete sledovat [stahování stav](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Zkontrolujte stav úlohy úložiště
Stav se zobrazí v dolní části stavový řádek po dokončení stahování a odesílání.
1. Klikněte na stavovém řádku mocí následujících a pak stažení a odesílání stav zobrazíte v **výstup** panelu.

   ![Nástroje data Lake pro Visual Studio Code zkontrolujte úložiště stavu](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Průzkumník VSCode integraci s Azure Data Lake

**Integrace se službou Azure** 

- Před přihlášení k Azure, můžete vždy rozšířit **AZURE DATALAKE**, pak klikněte na tlačítko **přihlaste k Azure** k přihlášení k Azure. Po přihlášení, zobrazí se všechny odběry v rámci účtu Azure, jsou uvedeny v levém panelu **AZURE DATALAKE**. 

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**Navigační ADLA metadat**

- Rozbalte vašeho předplatného Azure, můžete přejít U-SQL databáze, zobrazení **schémata**, **pověření**, **sestavení**, **tabulky**, **Index**a tak dále pod uzlem U-SQL databáze.

**Správa entit Metadata ADLA**

- Rozbalte položku **U-SQL databáze**, můžete vytvořit novou databázi, schéma, tabulka, typů tabulek, index, Statistika kliknutím pravým tlačítkem myši **skript pro vytvoření** kontextovou nabídku odpovídající uzlu. Na stránce otevřenou skript upravte skript podle vašich potřeb a potom odeslání úlohy kliknutím pravým tlačítkem na kontextovou nabídku **ADL: odeslat úlohu**. Po dokončení její vytvoření, klikněte na tlačítko kontextovou nabídku **aktualizovat** zobrazíte nové položky vytvořili. Můžete také odstranit položku kliknutím pravým tlačítkem myši v místní nabídce **odstranit**.

   ![Průzkumník DataLake vytvoří nové položky nabídky](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![Průzkumník DataLake vytvoří nový skript položky](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**Registrace sestavení ADLA**

 - Můžete **registrace sestavení** do příslušné databáze kliknutím pravým tlačítkem na **sestavení** uzlu.

    ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**Integrace ADLS** 

Přejděte na **Data Lake Store**

 - Na uzlu složky můžete **aktualizovat**, **odstranit**, **nahrát**, **nahrát složky**, **kopírovat relativní cestu**, **Kopírovat úplnou cestu** v místní nabídce klikněte pravým tlačítkem.

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

 - Na uzlu souboru můžete **Stáhnout**, **Preview**, **odstranit**, **relativní cesta kopie**, **Kopírovat úplnou cestu**v místní nabídce klikněte pravým tlačítkem. 

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

**Integrace WASB**

Přejděte na **úložiště objektů Blob**

- Na uzlu kontejner objektů blob můžete **aktualizovat**, **odstranit kontejner objektů Blob**, **nahrát objekt Blob** v místní nabídce klikněte pravým tlačítkem.

    ![Uzel kontejner objektu blob úložiště objektů BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Na uzlu složky můžete **aktualizovat**, **nahrát objekt Blob** v místní nabídce klikněte pravým tlačítkem.

    ![Uzel složky úložiště objektů BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Na uzlu souboru můžete **Preview či upravit**, **Stáhnout**, **odstranit**, **relativní cesta kopie**, **Kopírovat úplnou cestu** v místní nabídce klikněte pravým tlačítkem.

    ![Uzel souborového úložiště objektů BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-file-node.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Otevřete Průzkumníka úložiště ADL portálu
1. Vyberte Ctrl + Shift + P otevřete paletu příkaz.
2. Zadejte **otevřete Web Azure Storage Explorer** nebo klikněte pravým tlačítkem na relativní cestu nebo úplnou cestu v editoru skript a potom vyberte **otevřete Web Azure Storage Explorer**.
3. Vyberte účet Data Lake Analytics.

Nástroje data Lake otevře cesta k úložišti Azure na portálu Azure. Můžete najít cestu a náhled souboru z webu.

## <a name="local-run-and-local-debug-for-windows-users"></a>Místní spuštění a místní ladění pro systém Windows uživatele
U-SQL místní spuštění testů místní data a ověří váš skript místně před kódu je publikována ve službě Data Lake Analytics. Místní ladění funkce umožňuje než kódu je odeslána do Data Lake Analytics dokončit následující úlohy: 
- Ladění vaší C# kódu. 
- Krokovat kód. 
- Ověřte váš skript místně.

Pokyny v místním spuštění a místní ladění najdete v tématu [U-SQL místní spuštění a místní ladění s Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Další funkce

Nástroje data Lake pro VS Code podporuje následující funkce:

-   Automatické dokončování IntelliSense: návrhy zobrazí v automaticky otevíraná okna kolem položky, jako jsou klíčová slova, metod a proměnné. Různé ikony představují různé typy objektů:

    - Datový typ Scala
    - Komplexní datový typ
    - Předdefinované UDT
    - Třídy a .NET collection
    - Výrazy jazyka C#
    - Předdefinované C# UDF, UDO a UDAAGs 
    - Funkce U-SQL
    - U-SQL oddílová funkce
 
    ![Typy objektů nástroje data Lake pro Visual Studio kódu IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   IntelliSense automatického dokončování v Data Lake Analytics metadata: nástrojů Data Lake stáhne informace metadat Data Lake Analytics místně. Funkci IntelliSense automaticky naplní objektů, včetně databáze, schéma, tabulka, zobrazení, funkce vracející tabulku, postupy a sestavení C#, z metadat Data Lake Analytics.
 
    ![Nástroje data Lake pro Visual Studio kódu IntelliSense metadat](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Chyba značky IntelliSense: nástrojů Data Lake podtrhne úpravy chyby U-SQL a C#. 
-   Označuje syntaxe: nástrojů Data Lake pomocí různých barev odlišit od položky, jako jsou proměnné, klíčová slova, datový typ a funkce. 

    ![Nástroje data Lake pro Visual Studio Code syntaxe označuje](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Další postup
- [Vývoj U-SQL s Python, R a CSharp pro Azure Data Lake Analytics v VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL místní spuštění a místní ladění s kódem jazyka Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Kurz: Začínáme s Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Kurz: Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Vývoj sestavení U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-assemblies.md)




