---
title: Škálování U-SQL místní spuštění a testování pomocí Azure Data Lake U-SQL SDK | Microsoft Docs
description: Zjistěte, jak používat sadu SDK Azure Data Lake U-SQL k místní úlohy škálování U-SQL, spusťte a testování pomocí příkazového řádku a programovací rozhraní na místní pracovní stanici.
services: data-lake-analytics
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: yanacai
ms.openlocfilehash: 55242bcf644ca0e7f30cfe7eada2130451c36e64
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="scale-u-sql-local-run-and-test-with-azure-data-lake-u-sql-sdk"></a>Škálování U-SQL místní spuštění a testování pomocí Azure Data Lake U-SQL SDK

Při vývoji skript U-SQL, je běžné ke spuštění a testů U-SQL skriptu místně před odešlete ji do cloudu. Azure Data Lake poskytuje balíček Nuget s názvem SDK Azure Data Lake U-SQL pro tento scénář, pomocí kterých lze snadno škálovat U-SQL místní spuštění a testování. Je také možné integrovat tento test U-SQL systému CI (nepřetržité integrace) pro automatizaci kompilaci a testování.

Pokud vám záleží, jak chcete ručně místní spuštění a ladění skriptu U-SQL pomocí nástrojů s grafickým uživatelským rozhraním, můžete pomocí nástroje Azure Data Lake pro Visual Studio pro tento. Další informace z [zde](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalace Azure Data Lake U-SQL SDK

Můžete získat sadu SDK Azure Data Lake U-SQL [sem](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) na Nuget.org. A před jeho použitím, budete muset Ujistěte se, že máte následující závislosti.

### <a name="dependencies"></a>Závislosti

Data Lake U-SQL SDK vyžaduje následující závislosti:

- [Rozhraní Microsoft .NET Framework 4.6 nebo novější](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 a sady Windows SDK 10.0.10240.0 nebo novější (což se označuje jako CppSDK v tomto článku). Existují dva způsoby, jak získat CppSDK:

    - Nainstalujte [sady Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Budete mít \Windows Kits\10 složku ve složce Program Files – například C:\Program Files (x86) \Windows Kits\10\. Naleznete zde také verze Windows 10 SDK v části \Windows Kits\10\Lib. Pokud nevidíte tyto složky, přeinstalujte Visual Studio a je nutné vybrat během instalace Windows 10 SDK. Pokud je to nainstalované s Visual Studio, místní kompilátoru U-SQL najdete je automaticky.

    ![Nástroje data Lake pro Visual Studio místní spuštění Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Nainstalujte [nástroje Data Lake pro Visual Studio](http://aka.ms/adltoolsvs). Můžete najít hotových Visual C++ a Windows SDK soubory v C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Místní kompilátoru U-SQL v tomto případě nelze najít závislosti automaticky. Je třeba zadat cestu CppSDK pro ni. Můžete buď zkopírujte soubory do jiného umístění nebo ho jako je použít.

## <a name="understand-basic-concepts"></a>Pochopit základní koncepty

### <a name="data-root"></a>Kořenové datové

Data kořenové složky je pro účet místního výpočetní "místní úložiště". Je ekvivalentní k účtu Azure Data Lake Store účtu Data Lake Analytics. Přepnutí na jiný data kořenová složka je stejně jako přepnutí na účet jiné úložiště. Pokud chcete pro přístup k běžně sdílený data pomocí různých dat kořenové složky, je nutné použít absolutní cesty ve skriptech. Nebo vytvořte symbolické odkazy systému souborů (například **mklink** na systém souborů NTFS) v kořenové datové složce tak, aby odkazoval sdílená data.

Data kořenové složky se používá pro:

- Uložení místních metadat, včetně databází, tabulky, funkce vracející tabulku (Tvf) a sestavení.
- Vyhledání vstupní a výstupní cesty, které jsou definovány jako relativní cesty v U-SQL. Pomocí relativní cesty usnadňuje nasazení vašich projektů U-SQL Azure.

### <a name="file-path-in-u-sql"></a>Cesta k souboru v U-SQL

Můžete je relativní cesta a místní cestou absolutní v skriptů U-SQL. Relativní cesta je relativní k cestě zadané kořenové datové složce. Doporučujeme vám, že používáte "/" jako oddělovač cesty upravit skripty kompatibilní se na straně serveru. Zde jsou některé příklady relativní cesty a jejich ekvivalent absolutní cesty. V těchto příkladech je C:\LocalRunDataRoot kořenové datové složce.

|Relativní cesta|Absolutní cesty|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Pracovní adresář

Při místním spuštění skriptu U-SQL, vytvoří se během kompilace v aktuálním adresáři spuštěné pracovní adresář. Kromě výstupy kompilace soubory potřebné modulu runtime pro místní spuštění bude stínové kopie pro pracovní adresář. Pracovní adresář kořenové složky se označuje jako "ScopeWorkDir" a soubory v pracovní adresář jsou následující:

|Adresář nebo soubor|Adresář nebo soubor|Adresář nebo soubor|Definice|Popis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Řetězec hash verze modulu runtime|Soubory modulu runtime, které jsou potřebné pro provedení místní stínové kopie|
| |Script_66AE4909AA0ED06C| |Název skriptu + hash řetězec cestu ke skriptu|Výstupy kompilace a provádění krok protokolování|
| | |\_script\_.abr|Výstup kompilátoru|Soubor algebra|
| | |\_ScopeCodeGen\_. *|Výstup kompilátoru|Vygenerovaný spravovaného kódu|
| | |\_ScopeCodeGenEngine\_. *|Výstup kompilátoru|Vygenerovaný nativního kódu|
| | |Odkazovaná sestavení|Odkaz na sestavení|Soubory odkazované sestavení|
| | |deployed_resources|Nasazení prostředků|Soubory nasazení prostředků|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Spuštění protokolu|Protokol provádění kroků|


## <a name="use-the-sdk-from-the-command-line"></a>Použití sady SDK z příkazového řádku

### <a name="command-line-interface-of-the-helper-application"></a>Rozhraní příkazového řádku aplikace pomocné rutiny

V části sada SDK directory\build\runtime LocalRunHelper.exe je nástroj příkazového řádku se aplikace, která poskytuje rozhraní pro většinu funkcí pro běžně používané místní spuštění. Upozorňujeme, že příkaz i argument přepínače jsou malá a velká písmena. Chcete-li ji použít:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Spustit LocalRunHelper.exe bez argumentů nebo se **pomoci** přepnout na zobrazení informací nápovědy:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

V informace nápovědy:

-  **Příkaz** poskytuje název příkazu.  
-  **Vyžaduje Argument** uvádí argumenty, které je nutné zadat.  
-  **Nepovinný Argument** uvádí argumenty, které jsou volitelné, s výchozími hodnotami.  Nepovinné argumenty Boolean nemají parametry a jejich vzhled znamená záporná na jejich výchozí hodnoty.

### <a name="return-value-and-logging"></a>Vrátí hodnotu a protokolování

Vrací podpůrnou aplikací **0** úspěch a **-1** selhání. Ve výchozím nastavení odešle pomocné rutiny všechny zprávy do aktuální konzolu. Ale většina příkazů, podporují **- MessageOut cesta_k_souboru_protokolu** nepovinný argument, který přesměruje výstupy do souboru protokolu.

### <a name="environment-variable-configuring"></a>Konfigurace proměnné prostředí

U-SQL místní spuštění potřebují kořenové zadaná data jako účet místní úložiště, zadaná cesta CppSDK závislosti. Můžete i nastavit argument v proměnné prostředí příkazového řádku, nebo je nastavený pro ně.

- Nastavte **SCOPE_CPP_SDK** proměnné prostředí.

    Pokud se zobrazí Microsoft Visual C++ a sada Windows SDK prostřednictvím instalace nástrojů Data Lake pro Visual Studio, ověřte, zda máte následující složku:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Zadejte novou proměnnou prostředí s názvem **SCOPE_CPP_SDK** tak, aby odkazovaly do tohoto adresáře. Zkopírujte složku do jiného umístění a zadejte **SCOPE_CPP_SDK** jako.

    Kromě nastavení proměnné prostředí, můžete zadat **- CppSDK** argument při použití příkazového řádku. Tento argument přepíše vaše proměnná prostředí CppSDK výchozí.

- Nastavte **LOCALRUN_DATAROOT** proměnné prostředí.

    Zadejte novou proměnnou prostředí s názvem **LOCALRUN_DATAROOT** který odkazuje na kořenový data.

    Kromě nastavení proměnné prostředí, můžete zadat **- DataRoot** argument data kořenovou cestu při použití příkazového řádku. Tento argument přepíše vaše proměnná prostředí kořenové datové výchozí. Je nutné přidat tento argument pro každý příkazového řádku, které používáte, takže můžete přepsat proměnnou prostředí kořenové datové výchozí pro všechny operace.

### <a name="sdk-command-line-usage-samples"></a>Ukázky využití příkazový řádek SDK

#### <a name="compile-and-run"></a>Kompilace a spuštění

**Spustit** je příkaz použitý ke kompilaci skript a potom spusťte kompilované výsledky. Jeho argumenty příkazového řádku jsou kombinaci těchto z **zkompilovat** a **provést**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Toto jsou volitelné argumenty pro **spustit**:


|Argument|Výchozí hodnota|Popis|
|--------|-------------|-----------|
|-CodeBehind|False|Tento skript má .cs kódu na pozadí|
|-CppSDK| |CppSDK adresáře|
|-DataRoot| Proměnné prostředí DataRoot|DataRoot pro místní spuštění, výchozí do proměnné prostředí, LOCALRUN_DATAROOT.|
|-MessageOut| |Výpis zprávy v konzole do souboru|
|-Paralelní|1|Spustit s plánem s zadaný paralelismus|
|-Odkazy| |Seznam cest navíc referenční sestavení nebo datové soubory kódu na pozadí, oddělených ';'|
|-UdoRedirect|False|Generovat Udo sestavení přesměrování konfigurace|
|-UseDatabase|master|Databázi pro použití při kódu na pozadí registrace dočasné sestavení|
|-Verbose|False|Zobrazit podrobné výstupy z modulu runtime|
|-WorkDir|Aktuální adresář|Adresář pro využití kompilátoru a výstupy|
|-RunScopeCEP|0|Režim ScopeCEP používat|
|-ScopeCEPTempPath|dočasné|Dočasná cesta k použití pro datový proud|
|-OptFlags| |Seznam oddělený čárkami Optimalizátor příznaky|


Tady je příklad:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Kromě toho kombinování **zkompilovat** a **provést**, můžete zkompilovat a spustit samostatně, kompilované spustitelné soubory.

#### <a name="compile-a-u-sql-script"></a>Kompilace skript U-SQL

**Zkompilovat** příkaz se používá ke kompilaci skript U-SQL pro spustitelné soubory.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Toto jsou volitelné argumenty pro **zkompilovat**:


|Argument|Popis|
|--------|-----------|
| -CodeBehind [výchozí hodnotu "False"]|Tento skript má .cs kódu na pozadí|
| -CppSDK [výchozí hodnota:]|CppSDK adresáře|
| -DataRoot [výchozí hodnota "Proměnné prostředí DataRoot"]|DataRoot pro místní spuštění, výchozí do proměnné prostředí, LOCALRUN_DATAROOT.|
| -MessageOut [výchozí hodnota:]|Výpis zprávy v konzole do souboru|
| -Odkazuje [výchozí hodnota:]|Seznam cest navíc referenční sestavení nebo datové soubory kódu na pozadí, oddělených ';'|
| -Malou [výchozí hodnotu "False"]|Bez podstruktury kompilace|
| -UdoRedirect [výchozí hodnotu "False"]|Generovat Udo sestavení přesměrování konfigurace|
| -UseDatabase [výchozí hodnota 'hlavní']|Databázi pro použití při kódu na pozadí registrace dočasné sestavení|
| -WorkDir [výchozí hodnotu 'aktuální adresář.]|Adresář pro využití kompilátoru a výstupy|
| -RunScopeCEP [výchozí hodnota '0']|Režim ScopeCEP používat|
| -ScopeCEPTempPath [výchozí hodnota 'temp']|Dočasná cesta k použití pro datový proud|
| -OptFlags [výchozí hodnota:]|Seznam oddělený čárkami Optimalizátor příznaky|


Tady jsou některé příklady použití.

Kompilace skript U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Kompilace skript U-SQL a nastavte data kořenové složce. Všimněte si, že tato akce přepíše nastavená proměnná prostředí.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Kompilace skript U-SQL a nastavte pracovní adresář, referenční sestavení a databáze:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Spuštění kompilované výsledky

**Provést** příkaz se používá k provedení kompilované výsledky.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Toto jsou volitelné argumenty pro **provést**:

|Argument|Popis|
|--------|-----------|
|-DataRoot [výchozí hodnota:]|Kořenové datové pro provedení metadat. Výchozí hodnota **LOCALRUN_DATAROOT** proměnné prostředí.|
|-MessageOut [výchozí hodnota:]|Dump zprávy v konzole do souboru.|
|-Paralelní [výchozí hodnotu 1.]|Ukazatel ke spuštění generovaného kroků místní spuštění s úrovní zadaný stupně paralelního zpracování.|
|-Verbose [výchozí hodnotu "False"]|Ukazatel zobrazíte podrobné výstupy z modulu runtime.|

Tady je příklad použití:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Použití sady SDK pomocí rozhraní pro programování

Programovací rozhraní jsou umístěné v LocalRunHelper.exe. Můžete je používat k integraci funkce sady SDK U-SQL a test framework C# škálování svůj test místní skript U-SQL. V tomto článku použiji standardní C# projektu testování částí ukazují, jak používat tyto rozhraní k testování váš skript U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: Vytvoření projektu testování částí C# a konfigurace

- Vytvoření projektu testování částí C# prostřednictvím soubor > Nový > Projekt > Visual C# > testovací > projektu testování částí.
- Přidejte LocalRunHelper.exe jako odkaz pro projekt. LocalRunHelper.exe se nachází v \build\runtime\LocalRunHelper.exe v balíčku Nuget.

    ![Přidat odkaz na sadu SDK Azure Data Lake U-SQL](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **pouze** podporu x64 prostředí, nezapomeňte nastavit jako x64 Cílová platforma sestavení. Můžete nastavit, prostřednictvím vlastnosti projektu > sestavení > Cílová platforma.

    ![Azure Data Lake U-SQL SDK konfigurace x64 projektu](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ujistěte se, že nastavení testovacího prostředí jako x64. V sadě Visual Studio, můžete ho nastavit prostřednictvím testovací > Test nastavení > výchozí architekturu procesoru > x64.

    ![Azure Data Lake U-SQL SDK konfigurace x64 testovacího prostředí](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Nezapomeňte zkopírovat všechny soubory závislosti v rámci NugetPackage\build\runtime\ do projektu pracovní adresář, který je obvykle pod ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Vytvoření testovacího případu skript U-SQL

Níže je ukázkový kód pro test skriptu U-SQL. Pro testování, je nutné připravit skripty, soubory očekávaný výstup a vstupní soubory.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Programování rozhraní LocalRunHelper.exe

LocalRunHelper.exe poskytuje programovací rozhraní pro místní kompilace U-SQL, spusťte atd. Rozhraní jsou uvedeny v následujícím seznamu.

**Konstruktor**

veřejné LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Parametr|Typ|Popis|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|pro výstup zprávy nastavte na hodnotu null lze použít konzolu|

**Vlastnosti**

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|AlgebraPath|řetězec|Cesta k souboru algebra (algebra souboru je jednomu z výsledků kompilace)|
|CodeBehindReferences|řetězec|Pokud skript má další kódu na pozadí odkazy, zadejte cesty odděleny znakem ";"|
|CppSdkDir|řetězec|CppSDK adresáře|
|CurrentDir|řetězec|Aktuální adresář|
|Kořenová datová složka|řetězec|Kořenová cesta dat|
|DebuggerMailPath|řetězec|Cesta k zásuvky pošty ladicí program|
|GenerateUdoRedirect|BOOL|Pokud chcete generovat načítání sestavení přesměrování přepsat konfigurace|
|HasCodeBehind|BOOL|Pokud skript má kódu na pozadí|
|InputDir|řetězec|Adresář pro vstupní data|
|MessagePath|řetězec|Cesta k souboru výpisu zpráv|
|OutputDir|řetězec|Adresář pro výstupní data|
|Paralelismus|celá čísla|Ke spuštění algebra paralelismus|
|ParentPid|celá čísla|ID nadřazeného objektu, na kterém služba monitoruje ukončíte, nastavena na hodnotu 0 nebo ignorovat záporná hodnota.|
|ResultPath|řetězec|Cesta k souboru výpisu výsledek|
|RuntimeDir|řetězec|Adresář modulu runtime|
|ScriptPath|řetězec|Kde najít skriptu|
|Bez podstruktury|BOOL|Nedávná kompilace, nebo ne|
|TempDir|řetězec|Dočasný adresář|
|UseDataBase|řetězec|Zadejte databázi, kterou chcete použít pro kódu na pozadí dočasné sestavení registrace, hlavní ve výchozím nastavení|
|WorkDir|řetězec|Upřednostňované pracovní adresář|


**– Metoda**

|Metoda|Popis|Návrat|Parametr|
|------|-----------|------|---------|
|veřejné bool DoCompile()|Kompilace skript U-SQL|Hodnota TRUE, v případě úspěchu| |
|veřejné bool DoExec()|Kompilované výsledek spuštění|Hodnota TRUE, v případě úspěchu| |
|veřejné bool DoRun()|Spusťte skript U-SQL (kompilace + spouštění)|Hodnota TRUE, v případě úspěchu| |
|veřejné bool IsValidRuntimeDir (cesta řetězec)|Zkontrolujte, jestli dané cesty je cesta platná runtime|Platí pro platný|Cesta adresář modulu runtime|


## <a name="faq-about-common-issue"></a>Nejčastější dotazy o běžné problémy

### <a name="error-1"></a>1. Chyba:
E_CSC_SYSTEM_INTERNAL: Vnitřní chyba! Nepodařilo se načíst soubor nebo sestavení 'ScopeEngineManaged.dll nebo jednu ze závislostí. Zadaný modul nebyl nalezen.

Zkontrolujte následující:

- Zajistěte, aby byla x64 prostředí. Cílové platformy sestavení a testovací prostředí by měl být x64, odkazovat na **krok 1: vytvoření C# jednotkové testování projektu a konfigurace** výše.
- Ujistěte se, že jste zkopírovali všechny soubory závislosti v rámci NugetPackage\build\runtime\ do projektu pracovní adresář.


## <a name="next-steps"></a>Další postup

* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [přístup k protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, najdete v části [analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Chcete-li zobrazit podrobnosti o úlohách, najdete v části [použití úlohy prohlížeče a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Chcete-li použít zobrazení provádění vrcholů, přečtěte si téma [použít zobrazení provádění vrcholů v nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
