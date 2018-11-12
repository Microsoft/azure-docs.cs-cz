---
title: Spuštění a testování úloh U-SQL místně pomocí sady SDK služby Azure Data Lake U-SQL
description: Zjistěte, jak spustit a otestovat úloh U-SQL místně z příkazového řádku a programovacích rozhraní na místní pracovní stanici.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 6a73ef058a76152678099eca3f1bd15590b0b03d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238790"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Spuštění a testování U-SQL s Azure Data Lake U-SQL SDK

Při vývoji skript U-SQL, je společné pro spuštění a skriptu testu U-SQL místně před odesláním do cloudu. Azure Data Lake nabízí balíček Nuget s názvem Azure Data Lake U-SQL SDK pro tento scénář, pomocí které můžete snadno škálovat spuštění U-SQL a testování. Je také možné integrovat systém CI (průběžná integrace) pro automatizaci kompilace a testování tohoto testu U-SQL.

Pokud vás zajímají jak na místní ručně spustit a ladit skript U-SQL pomocí nástrojů s grafickým uživatelským rozhraním, pak můžete použít Azure Data Lake Tools pro Visual Studio pro tento. Další informace z [tady](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalace Azure Data Lake U-SQL SDK

Azure Data Lake U-SQL SDK můžete získat [tady](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) na Nuget.org. A před použitím, budete muset Ujistěte se, že máte následující závislosti.

### <a name="dependencies"></a>Závislosti

Data Lake U-SQL SDK vyžaduje následující závislosti:

- [Microsoft .NET Framework 4.6 nebo novější](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 a sada Windows SDK 10.0.10240.0 nebo novější (která je volána CppSDK v tomto článku). Existují dva způsoby, jak získat CppSDK:

    - Nainstalujte [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Ve složce Program Files – například C:\Program Files (x86) \Windows Kits\10\ budete mít \Windows Kits\10 složku. Také zjistíte verzi Windows 10 SDK v \Windows Kits\10\Lib. Pokud nevidíte těchto složek, přeinstalujte Visual Studio a je nutné vybrat během instalace Windows 10 SDK. Pokud to instalace sady Visual Studio, kompilátor místní U-SQL najdete ji automaticky.

    ![Data Lake Tools pro Visual Studio Windows 10 SDK místního spuštění](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Nainstalujte [nástroje Data Lake pro Visual Studio](https://aka.ms/adltoolsvs). Můžete najít předpřipravenou Visual C++ a Windows SDK soubory na C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Místní kompilátor U-SQL v tomto případě nelze najít závislosti automaticky. Je třeba zadat cestu CppSDK pro něj. Můžete buď zkopírujte soubory jinam nebo použít to tak je.

## <a name="understand-basic-concepts"></a>Základní koncepce

### <a name="data-root"></a>Kořen dat

Data kořenové složky je "místní úložiště" pro místní výpočetní účet. Je ekvivalentní k účtu Azure Data Lake Store účtu Data Lake Analytics. Přepnutí do složky jiný kořen dat je stejně jako přepnutí na jiné úložiště účtu. Pokud chcete získat přístup k datům běžně sdílené složky jiný kořen dat, je nutné použít absolutní cesty ve skriptech. Vytvořit symbolické odkazy systému souborů (například **mklink** v systému souborů NTFS) ve složce kořen dat tak, aby odkazoval ke sdíleným datům.

Data kořenové složky se používá pro:

- Store místní metadata, včetně databází, tabulek, funkce vracející tabulku (Tvf) a sestavení.
- Vyhledání vstupní a výstupní cesty, které jsou definované jako relativní cesty v U-SQL. Pomocí relativní cesty usnadňuje nasazování projektů U-SQL do Azure.

### <a name="file-path-in-u-sql"></a>Cesta k souboru v U-SQL

V skriptů U-SQL můžete použít relativní cestu a místní absolutní cestu. Relativní cesta je relativní vzhledem k zadané kořen dat cesta ke složce. Doporučujeme vám, že používáte "/" jako oddělovače cesty, aby vaše skripty kompatibilní s na straně serveru. Tady je několik příkladů relativní cesty a jejich odpovídající absolutní cesty. V těchto příkladech je C:\LocalRunDataRoot data kořenové složce.

|Relativní cesta|Absolutní cesta|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Pracovní adresář

Při místním spuštění skriptu U-SQL, do pracovního adresáře se vytvoří během kompilace v rámci aktuální spouštěcího adresáře. Kromě výstupy kompilace soubory modulu runtime potřebné pro místní spuštění bude zkopírována do pracovní adresář. Pracovní složky kořenový adresář se nazývá "ScopeWorkDir" a soubory v pracovním adresáři jsou následující:

|Soubor či složku|Soubor či složku|Soubor či složku|Definice|Popis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hodnota hash řetězce verze modulu runtime|Stínové kopie soubory modulu runtime potřebné pro místní spuštění|
| |Script_66AE4909AA0ED06C| |Název skriptu a hodnoty hash řetězce cesta ke skriptu|Výstupy kompilace a spuštění krok protokolování|
| | |\_skript\_.abr|Výstup kompilátoru|Algebraický soubor|
| | |\_ScopeCodeGen\_. *|Výstup kompilátoru|Vygenerovat spravovaný kód|
| | |\_ScopeCodeGenEngine\_. *|Výstup kompilátoru|Generovaný nativní kód|
| | |Odkazovaná sestavení|Odkaz na sestavení|Soubory odkazovaná sestavení|
| | |deployed_resources|Nasazení prostředků|Nasazení souborů prostředků|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Prováděcí protokol|Protokol z provádění kroků|


## <a name="use-the-sdk-from-the-command-line"></a>Použití sady SDK z příkazového řádku

### <a name="command-line-interface-of-the-helper-application"></a>Rozhraní příkazového řádku aplikace pomocné rutiny

V rámci sady SDK directory\build\runtime LocalRunHelper.exe je aplikace příkazového řádku pomocné rutiny, která poskytuje rozhraní k většině funkcí pro běžně používané místní spuštění. Všimněte si, že příkaz a argument přepínače jsou malá a velká písmena. Chcete-li ho vyvolat:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Spustit LocalRunHelper.exe bez argumentů nebo se **pomáhají** přepínač tak, aby zobrazit informace nápovědy:

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
-  **Vyžaduje Argument** obsahuje argumenty, které je nutné zadat.  
-  **Volitelný Argument** obsahuje argumenty, které jsou volitelné, s výchozími hodnotami.  Volitelné, logická argumenty nemají parametry a jejich vzhled znamená záporná hodnota, při jejich výchozí hodnotu.

### <a name="return-value-and-logging"></a>Návratová hodnota a protokolování

Vrací podpůrnou aplikací **0** k dosažení úspěchu a **-1** chyby. Pomocná rutina ve výchozím nastavení, odešle všechny zprávy na aktuální konzolu. Ale podporují většinu příkazů **- MessageOut cesta_k_souboru_protokolu** volitelný argument, který přesměruje výstup do souboru protokolu.

### <a name="environment-variable-configuring"></a>Konfigurace proměnných prostředí

U-SQL místní spuštění vyžaduje kořenový zadaná data jako účet místní úložiště, stejně jako zadaná cesta CppSDK pro závislosti. Obě nastavíte argument v proměnné prostředí příkazového řádku nebo nastavte pro ně.

- Nastavte **SCOPE_CPP_SDK** proměnné prostředí.

    Pokud se zobrazí Microsoft Visual C++ a sady Windows SDK prostřednictvím instalace nástrojů Data Lake pro Visual Studio, ověřte, že máte následující složku:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definovat novou proměnnou prostředí s názvem **SCOPE_CPP_SDK** tak, aby odkazoval do tohoto adresáře. Zkopírujte složku do jiného umístění nebo zadejte **SCOPE_CPP_SDK** takhle.

    Kromě nastavení proměnné prostředí, můžete určit, **- CppSDK** argumentu při použití příkazového řádku. Tento argument přepíše vaší proměnné prostředí CppSDK výchozí.

- Nastavte **LOCALRUN_DATAROOT** proměnné prostředí.

    Definovat novou proměnnou prostředí s názvem **LOCALRUN_DATAROOT** , která odkazuje na kořen dat.

    Kromě nastavení proměnné prostředí, můžete určit, **- DataRoot** argument s cestou kořen dat při použití příkazového řádku. Tento argument přepíše vaší proměnné prostředí kořen dat výchozí. Budete muset přidat tento argument pro každý příkazový řádek, který používáte, takže můžete přepsat proměnnou prostředí výchozí kořen dat pro všechny operace.

### <a name="sdk-command-line-usage-samples"></a>Ukázky použití příkazového řádku sady SDK

#### <a name="compile-and-run"></a>Kompilace a spuštění

**Spustit** příkaz se používá ke kompilaci skriptu a následné provádění zkompilované výsledky. Argumenty příkazového řádku jsou ty z kombinace **kompilaci** a **provést**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Toto jsou volitelné argumenty pro **spustit**:


|Argument|Výchozí hodnota|Popis|
|--------|-------------|-----------|
|-CodeBehind|False|Tento skript má .cs kódu na pozadí|
|-CppSDK| |CppSDK adresáře|
|-DataRoot| Proměnná prostředí DataRoot|DataRoot pro místní spuštění, výchozí do proměnné prostředí 'LOCALRUN_DATAROOT.|
|-MessageOut| |Zprávy v konzole do souboru výpisu paměti|
|-Parallel|1|Spustit plán se zadaným paralelismu|
|-Odkazy| |Seznam cest k další odkaz na sestavení nebo datové soubory kódu na pozadí, oddělené středník (;)|
|-UdoRedirect|False|Generovat konfigurace přesměrování sestavení Udo|
|-UseDatabase|master|Databázi pro použití při registraci dočasného sestavení s kódem|
|-Verbose|False|Zobrazit podrobný výstup z modulu runtime|
|-WorkDir|Aktuální adresář|Adresář pro použití v kompilátoru a výstupy|
|-RunScopeCEP|0|Režim ScopeCEP|
|-ScopeCEPTempPath|temp|Dočasná cesta pro streamování dat|
|-OptFlags| |Čárkami oddělený seznam příznaky optimalizace|


Tady je příklad:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Kromě kombinování **kompilaci** a **provést**, můžete zkompilovat a spustit kompilovaných spustitelných souborech samostatně.

#### <a name="compile-a-u-sql-script"></a>Kompilace skriptu U-SQL

**Kompilaci** příkaz se používá ke kompilaci skriptu U-SQL do spustitelných souborů.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Toto jsou volitelné argumenty pro **kompilaci**:


|Argument|Popis|
|--------|-----------|
| -CodeBehind [výchozí hodnota 'False']|Tento skript má .cs kódu na pozadí|
| -CppSDK [výchozí hodnotu "]|CppSDK adresáře|
| -DataRoot [výchozí hodnota 'DataRoot proměnné prostředí']|DataRoot pro místní spuštění, výchozí do proměnné prostředí 'LOCALRUN_DATAROOT.|
| -MessageOut [výchozí hodnotu "]|Zprávy v konzole do souboru výpisu paměti|
| -Odkazuje na [výchozí hodnotu "]|Seznam cest k další odkaz na sestavení nebo datové soubory kódu na pozadí, oddělené středník (;)|
| -Nedávná [výchozí hodnota 'False']|Bez podstruktury kompilace|
| -UdoRedirect [výchozí hodnota 'False']|Generovat konfigurace přesměrování sestavení Udo|
| -UseDatabase [výchozí hodnotu "master"]|Databázi pro použití při registraci dočasného sestavení s kódem|
| -WorkDir [výchozí hodnotu 'Aktuální adresář']|Adresář pro použití v kompilátoru a výstupy|
| -RunScopeCEP [výchozí hodnota '0']|Režim ScopeCEP|
| -ScopeCEPTempPath [výchozí hodnota 'temp']|Dočasná cesta pro streamování dat|
| -OptFlags [výchozí hodnotu "]|Čárkami oddělený seznam příznaky optimalizace|


Tady jsou některé příklady využití.

Kompilace skriptu U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Kompilace skriptu U-SQL a nastavte data kořenové složce. Všimněte si, že tato akce přepíše nastavte proměnnou prostředí.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Kompilace skriptu U-SQL a nastavte pracovní adresář, referenční sestavení a databázi:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Spuštění zkompilované výsledky

**Provést** příkaz se používá k provedení zkompilované výsledky.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Toto jsou volitelné argumenty pro **provést**:

|Argument|Výchozí hodnota|Popis|
|--------|-------------|-----------|
|-DataRoot | '' |Kořen dat pro provedení metadat. Výchozí hodnota **LOCALRUN_DATAROOT** proměnné prostředí.|
|-MessageOut | '' |Vypsat zprávy v konzole do souboru.|
|-Parallel | '1' |Ukazatel k provedení kroků generované místní spuštění s požadovanou úrovní aktualizace zadané paralelismu.|
|-Verbose | "Nepravda" |Indikátor zobrazíte podrobné vytvořené jako výstupy z modulu runtime.|

Tady je příklad použití:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Použití sady SDK pomocí rozhraní pro programování

Programovací rozhraní jsou umístěny v LocalRunHelper.exe. Můžete je integrovat funkce SDK U-SQL a C# rozhraní pro testování škálování místní test skript U-SQL. V tomto článku použiji standard jazyka C# projektu testování částí věnovaných ukázce používání těchto rozhraní k testování skript U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: Vytvořte projekt testu jednotek C# a konfigurace

- Vytvořit projekt testování částí jazyka C# pomocí souboru > Nový > Projekt > Visual C# > Test > Projekt testů jednotek.
- Přidáte LocalRunHelper.exe jako reference pro projekt. LocalRunHelper.exe nachází ve \build\runtime\LocalRunHelper.exe v balíčku Nuget.

    ![Přidat odkaz na sadu SDK Azure Data Lake U-SQL](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **pouze** podporu x64 prostředí, nezapomeňte nastavit jako x64 Cílová platforma sestavení. Můžete nastavit, která prostřednictvím vlastnosti projektu > sestavení > Cílová platforma.

    ![Azure Data Lake U-SQL SDK nakonfigurovat x64 projektu](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ujistěte se, že nastavení testovacího prostředí jako x64. V sadě Visual Studio, můžete ho nastavit prostřednictvím testu > Nastavení testu > výchozí architektura procesoru > x64.

    ![Azure Data Lake U-SQL SDK nakonfigurovat x64 testovacího prostředí](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Nezapomeňte si zkopírovat všechny soubory závislosti v rámci NugetPackage\build\runtime\ do pracovního adresáře, který je obvykle v rámci ProjectFolder\bin\x64\Debug projektu.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Vytvoření testovacích případů skript U-SQL

Tady je ukázkový kód pro testování skriptů U-SQL. Pro účely testování, je nutné připravit skripty, soubory očekávaný výstup a vstupní soubory.

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


### <a name="programming-interfaces-in-localrunhelperexe"></a>Rozhraní pro programování v LocalRunHelper.exe

LocalRunHelper.exe poskytuje programovací rozhraní pro místní kompilace U-SQL, spusťte atd. Rozhraní jsou uvedeny následovně.

**Konstruktor**

veřejné LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Parametr|Typ|Popis|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|pro výstupní zprávy nastavte na hodnotu null, pomocí konzoly|

**Vlastnosti**

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|AlgebraPath|řetězec|Cesta k souboru algebraický (algebraický soubor je jedním z výsledků kompilace)|
|CodeBehindReferences|řetězec|Pokud tento skript má další kódu na pozadí odkazy, zadejte cesty odděleny ";"|
|CppSdkDir|řetězec|CppSDK adresáře|
|CurrentDir|řetězec|Aktuální adresář|
|Kořenová datová složka|řetězec|Kořenová cesta dat|
|DebuggerMailPath|řetězec|Cesta k zásuvky pošty ladicího programu|
|GenerateUdoRedirect|BOOL|Pokud chcete generovat načítání přesměrování přepsání konfigurace sestavení|
|HasCodeBehind|BOOL|Pokud tento skript má kódu na pozadí|
|InputDir|řetězec|Adresář pro vstupní data|
|MessagePath|řetězec|Cesta k souboru s výpisem paměti zprávy|
|OutputDir|řetězec|Adresář pro výstupní data|
|Paralelismus|int|Paralelismus pro spuštění algebraický|
|ParentPid|int|Identifikátor PID nadřazeného objektu, na kterém služba monitoruje ukončíte, nastavena na hodnotu 0 nebo záporné ignorovat|
|ResultPath|řetězec|Cesta k souboru s výpisem paměti výsledek|
|RuntimeDir|řetězec|Adresáře modulu runtime|
|ScriptPath|řetězec|Kde najít skript|
|Bez podstruktury|BOOL|Nedávná kompilace nebo ne|
|Dočasný adresář|řetězec|Dočasný adresář|
|UseDataBase|řetězec|Zadejte databázi pro účely registrace dočasného sestavení, ve výchozím nastavení hlavní s kódem|
|WorkDir|řetězec|Upřednostňované pracovní adresář|


**Metoda**

|Metoda|Popis|Vrátí|Parametr|
|------|-----------|------|---------|
|veřejné bool DoCompile()|Kompilace skriptu U-SQL|Hodnota TRUE v případě úspěchu| |
|veřejné bool DoExec()|Spuštění zkompilovaného výsledku|Hodnota TRUE v případě úspěchu| |
|veřejné bool DoRun()|Spusťte skript U-SQL (kompilace a spuštění)|Hodnota TRUE v případě úspěchu| |
|veřejné bool IsValidRuntimeDir (řetězec cesty)|Zkontrolujte, jestli dané cesty je cesta platná modulu runtime|Hodnota true pro platné|Cesta adresáře modulu runtime|


## <a name="faq-about-common-issue"></a>Nejčastější dotazy týkající se běžných potíží

### <a name="error-1"></a>1. Chyba:
E_CSC_SYSTEM_INTERNAL: Vnitřní chyba! Nepovedlo se načíst soubor nebo sestavení 'ScopeEngineManaged.dll' nebo některou z jeho závislostí. Zadaný modul nebyl nalezen.

Zkontrolujte prosím následující:

- Ujistěte se, že máte x64 prostředí. Cílová platforma sestavení a testovací prostředí by měl být x64, přečtěte si **krok 1: vytvoření C# testování projektu a konfigurace** výše.
- Ujistěte se, že jste zkopírovali všechny soubory závislosti v rámci NugetPackage\build\runtime\ do projektu pracovního adresáře.


## <a name="next-steps"></a>Další postup

* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [přístup k protokolům diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, naleznete v tématu [analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Chcete-li zobrazit podrobnosti o úloze, naleznete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Použití zobrazení provádění vrcholů, najdete v článku [použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
