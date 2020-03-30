---
title: Spouštění úloh U-SQL místně – Azure Data Lake U-SQL SDK
description: Naučte se spouštět a testovat úlohy U-SQL místně pomocí příkazového řádku a programovacích rozhraní na místní pracovní stanici.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309684"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Spuštění a testování U-SQL pomocí azure data lake U-SQL SDK

Při vývoji U-SQL skript, je běžné spustit a otestovat U-SQL skript místně před odesláním do cloudu. Azure Data Lake poskytuje balíček Nuget s názvem Azure Data Lake U-SQL SDK pro tento scénář, pomocí kterého můžete snadno škálovat U-SQL spustit a testovat. Je také možné integrovat tento U-SQL test s CI (kontinuální integrace) systém pro automatizaci kompilace a testování.

Pokud vám záleží na tom, jak ručně místní spuštění a ladění U-SQL skript s nástroji GUI, pak můžete použít Azure Data Lake Tools pro Visual Studio pro to. Můžete se dozvědět více [zde](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalace sady Azure Data Lake U-SQL SDK

Azure Data Lake U-SQL SDK můžete získat [zde](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) na Nuget.org. A před použitím, musíte se ujistit, že máte závislosti takto.

### <a name="dependencies"></a>Závislosti

Sada Data Lake U-SQL SDK vyžaduje následující závislosti:

- [Rozhraní Microsoft .NET Framework 4.6 nebo novější](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 a Windows SDK 10.0.10240.0 nebo novější (který se nazývá CppSDK v tomto článku). CppSDK lze získat dvěma způsoby:

  - Nainstalujte [aplikaci Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Ve složce Program Files budete mít složku \Windows Kits\10, například C:\Program Files (x86)\Windows Kits\10\. Verzi sady Windows 10 najdete také v části \Windows Kit\10\Lib. Pokud tyto složky nevidíte, přeinstalujte Visual Studio a nezapomeňte během instalace vybrat sadu Windows 10 SDK. Pokud máte to to nainstalované s Visual Studio, u-SQL místní kompilátor najde automaticky.

    ![Nástroje datového jezera pro místní windows 10 SDK pro Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Nainstalujte [nástroje datového jezera pro visual studio](https://aka.ms/adltoolsvs). Soubory sady Visual C++ a Windows SDK naleznete v souborech C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. V tomto případě místní kompilátor U-SQL nemůže najít závislosti automaticky. Je třeba zadat cestu CppSDK pro něj. Soubory můžete zkopírovat do jiného umístění nebo je použít tak, jak jsou.

## <a name="understand-basic-concepts"></a>Porozumět základním pojmům

### <a name="data-root"></a>Kořenová data

Složka kořenová data je "místní úložiště" pro místní výpočetní účet. Je ekvivalentní účtu Azure Data Lake Store účtu Data Lake Analytics. Přepnutí do jiné složky kořenových dat je stejné jako přepnutí na jiný účet úložiště. Pokud chcete získat přístup k běžně sdíleným datům s různými složkami kořenových dat, musíte ve skriptech použít absolutní cesty. Nebo vytvořte symbolické odkazy systému souborů (například **mklink** na NTFS) pod složkou kořenových dat, které odkazují na sdílená data.

Složka kořenových dat se používá k:

- Uklápěte místní metadata, včetně databází, tabulek, funkcí s hodnotou tabulky (TVF) a sestavení.
- Vyhledejte vstupní a výstupní cesty, které jsou definovány jako relativní cesty v U-SQL. Použití relativní cesty usnadňuje nasazení projektů U-SQL do Azure.

### <a name="file-path-in-u-sql"></a>Cesta k souboru v U-SQL

Ve skriptech U-SQL můžete použít relativní i místní absolutní cestu. Relativní cesta je relativní k zadané cestě kořenové složky dat. Doporučujeme použít "/" jako oddělovač cesty, aby byly skripty kompatibilní se stranou serveru. Zde jsou některé příklady relativnícesty a jejich ekvivalentní absolutní cesty. V těchto příkladech je C:\LocalRunDataRoot složka kořenová složka dat.

|Relativní cesta|Absolutní cesta|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Pracovní adresář

Při místním spuštění skriptu U-SQL je během kompilace vytvořen pracovní adresář pod aktuálním spuštěným adresářem. Kromě výstupů kompilace budou potřebné soubory runtime pro místní spuštění zkopírovány do tohoto pracovního adresáře. Kořenová složka pracovního adresáře se nazývá "ScopeWorkDir" a soubory pod pracovním adresářem jsou následující:

|Adresář/soubor|Adresář/soubor|Adresář/soubor|Definice|Popis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Řetězec hash verze runtime|Stínová kopie souborů runtime potřebných pro místní spuštění|
| |Script_66AE4909AA0ED06C| |Název skriptu + hash řetězec cesty skriptu|Výstupy kompilace a protokolování kroků spuštění|
| | |\_skript\_.abr|Výstup kompilátoru|Soubor algebry|
| | |\_ScopeCodeGen\_.*|Výstup kompilátoru|Vygenerovaný spravovaný kód|
| | |\_ScopeCodeGenEngine\_.*|Výstup kompilátoru|Generovaný nativní kód|
| | |odkazovaná sestavení|Odkaz na sestavení|Odkazované soubory sestavení|
| | |deployed_resources|Nasazení prostředků|Soubory nasazení prostředků|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Protokol provádění|Protokol kroků spuštění|


## <a name="use-the-sdk-from-the-command-line"></a>Použití sady SDK z příkazového řádku

### <a name="command-line-interface-of-the-helper-application"></a>Rozhraní příkazového řádku pomocné aplikace

V části Adresář sady SDK\build\runtime je soubor LocalRunHelper.exe pomocnou aplikací příkazového řádku, která poskytuje rozhraní většině běžně používaných funkcí místního spuštění. Všimněte si, že příkaz a argument přepínače jsou malá a velká písmena. Chcete-li jej vyvolat:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Spusťte soubor LocalRunHelper.exe bez argumentů nebo pomocí přepínače **nápovědy,** který zobrazí informace nápovědy:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

V nápovědě:

-  **Příkaz** udává název příkazu.  
-  **Požadovaný argument** uvádí argumenty, které musí být zadány.  
-  **Volitelný argument** uvádí argumenty, které jsou volitelné, s výchozími hodnotami.  Volitelné logické argumenty nemají parametry a jejich vzhled znamená negativní na jejich výchozí hodnotu.

### <a name="return-value-and-logging"></a>Vrácená hodnota a protokolování

Pomocná aplikace vrátí **0** pro úspěch a **-1** pro selhání. Ve výchozím nastavení pomocník odesílá všechny zprávy do aktuální konzoly. Většina příkazů však podporuje volitelný argument **-MessageOut path_to_log_file,** který přesměruje výstupy do souboru protokolu.

### <a name="environment-variable-configuring"></a>Konfigurace proměnných prostředí

Místní spuštění U-SQL potřebuje zadaný kořen dat jako účet místního úložiště a také zadanou cestu CppSDK pro závislosti. Argument můžete nastavit v příkazovém řádku nebo nastavit proměnnou prostředí pro ně.

- Nastavte **proměnnou prostředí SCOPE_CPP_SDK.**

    Pokud získáte sadu Microsoft Visual C++ a sadu Windows SDK instalací nástrojů Data Lake Tools pro sadu Visual Studio, ověřte, zda máte následující složku:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definujte novou proměnnou prostředí nazvanou **SCOPE_CPP_SDK,** která bude na tento adresář ukazovat. Nebo zkopírujte složku do jiného umístění a zadejte **SCOPE_CPP_SDK.**

    Kromě nastavení proměnné prostředí můžete při použití příkazového řádku zadat argument **-CppSDK.** Tento argument přepíše výchozí proměnnou prostředí CppSDK.

- Nastavte **proměnnou prostředí LOCALRUN_DATAROOT.**

    Definujte novou proměnnou prostředí nazvanou **LOCALRUN_DATAROOT,** která odkazuje na kořen dat.

    Kromě nastavení proměnné prostředí můžete při použití příkazového řádku zadat argument **-DataRoot** s cestou kořene dat. Tento argument přepíše výchozí proměnnou prostředí kořenového data. Tento argument je třeba přidat do každého příkazového řádku, který používáte, abyste mohli přepsat výchozí proměnnou prostředí kořenového data pro všechny operace.

### <a name="sdk-command-line-usage-samples"></a>Ukázky využití příkazového řádku sady SDK

#### <a name="compile-and-run"></a>Kompilace a spuštění

Příkaz **spustit** se používá ke kompilaci skriptu a následnému spuštění zkompilovaných výsledků. Jeho argumenty příkazového řádku jsou kombinací argumentů z **kompilace** a **spuštění**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Následují volitelné argumenty pro **spuštění**:


|Argument|Výchozí hodnota|Popis|
|--------|-------------|-----------|
|-CodeBehind|False|Skript má kód .cs za sebou|
|-CppSDK| |Adresář CppSDK|
|-Datový kořen| Proměnná prostředí DataRoot|Název_data pro místní spuštění, výchozí proměnná prostředí "LOCALRUN_DATAROOT".|
|-MessageOut| |Výpis zpráv v konzole do souboru|
|-Paralelní|1|Spuštění plánu se zadaným paralelismem|
|-Reference| |Seznam cest k nadbytečným referenčním sestavením nebo datovým souborům kódu, oddělených ';'|
|-UdoRedirect|False|Generovat konfiguraci přesměrování sestavení Udo|
|-UseDatabase|master|Databáze, která se má použít pro kód za dočasnou registrací sestavení|
|-Podrobné|False|Zobrazit podrobné výstupy z běhu|
|-WorkDir|Aktuální adresář|Adresář pro použití kompilátoru a výstupy|
|-RunScopeCEP|0|Režim ScopeCEP, který se má použít|
|-ScopeCEPTempPath|Temp|Dočasná cesta, kterou se má použít pro streamování dat|
|-OptFlags| |Seznam příznaků optimalizátorů oddělených čárkami|


Tady je příklad:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Kromě kombinování **kompilace** a **spuštění**můžete kompilovat a spouštět zkompilované spustitelné soubory samostatně.

#### <a name="compile-a-u-sql-script"></a>Kompilace skriptu U-SQL

Příkaz **kompilace** se používá ke kompilaci skriptu U-SQL do spustitelných souborů.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Následují volitelné argumenty pro **kompilaci**:


|Argument|Popis|
|--------|-----------|
| -CodeBehind [výchozí hodnota 'False']|Skript má kód .cs za sebou|
| -CppSDK [výchozí hodnota '']|Adresář CppSDK|
| -DataRoot [výchozí hodnota 'DataRoot proměnné prostředí']|Název_data pro místní spuštění, výchozí proměnná prostředí "LOCALRUN_DATAROOT".|
| -MessageOut [výchozí hodnota '']|Výpis zpráv v konzole do souboru|
| -Reference [výchozí hodnota '']|Seznam cest k nadbytečným referenčním sestavením nebo datovým souborům kódu, oddělených ';'|
| -Shallow [výchozí hodnota 'False']|Mělká kompilace|
| -UdoRedirect [výchozí hodnota 'False']|Generovat konfiguraci přesměrování sestavení Udo|
| -UseDatabase [výchozí hodnota 'master']|Databáze, která se má použít pro kód za dočasnou registrací sestavení|
| -WorkDir [výchozí hodnota 'Aktuální adresář']|Adresář pro použití kompilátoru a výstupy|
| -RunScopeCEP [výchozí hodnota '0']|Režim ScopeCEP, který se má použít|
| -ScopeCEPTempPath [výchozí hodnota 'temp']|Dočasná cesta, kterou se má použít pro streamování dat|
| -OptFlags [výchozí hodnota '']|Seznam příznaků optimalizátorů oddělených čárkami|


Zde jsou některé příklady použití.

Kompilace skriptu U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Zkompilujte skript U-SQL a nastavte složku kořenových dat. Všimněte si, že to přepíše proměnnou nastavené prostředí.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Zkompilujte skript U-SQL a nastavte pracovní adresář, referenční sestavení a databázi:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Spustit zkompilované výsledky

Příkaz **execute** se používá ke spuštění kompilovaných výsledků.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Následují volitelné argumenty pro **spuštění**:

|Argument|Výchozí hodnota|Popis|
|--------|-------------|-----------|
|-Datový kořen | '' |Kořen dat pro spuštění metadat. Výchozí hodnota je **LOCALRUN_DATAROOT** proměnné prostředí.|
|-MessageOut | '' |Vypáčit zprávy v konzole do souboru.|
|-Paralelní | '1' |Indikátor pro spuštění generovaných kroků místního spuštění se zadanou úrovní paralelismu.|
|-Podrobné | "Nepravdivé" |Indikátor pro zobrazení podrobných výstupů z běhu.|

Zde je příklad použití:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Použití sady SDK s programovacími rozhraními

Všechna programovací rozhraní jsou umístěna v programu LocalRunHelper.exe. Můžete je použít k integraci funkcí U-SQL SDK a C# testovací rozhraní pro škálování u-SQL skript místní test. V tomto článku budu používat standardní c# projekt testování částí ukázat, jak používat tato rozhraní k testování u-SQL skript.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: Vytvoření projektu a konfigurace testování částí c#

- Vytvořte projekt testování částí C# prostřednictvím projektu File > New > Project > Visual C# > Test > projekt testování částí.
- Jako referenci pro projekt přidejte soubor LocalRunHelper.exe. Soubor LocalRunHelper.exe je umístěn na adrese \build\runtime\LocalRunHelper.exe v balíčku Nuget.

    ![Azure Data Lake U-SQL SDK přidat odkaz](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK podporuje **pouze** prostředí x64, ujistěte se, že nastavit cíl platformy sestavení jako x64. Můžete nastavit, že prostřednictvím vlastnosti projektu > sestavení > platformy cíl.

    ![Azure Data Lake U-SQL SDK Konfigurace projektu x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ujistěte se, že nastavit testovací prostředí jako x64. V sadě Visual Studio ji můžete nastavit pomocí nastavení testování test > > výchozí architektura procesoru > x64.

    ![Azure Data Lake U-SQL SDK Konfigurace testovacího prostředí x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Nezapomeňte zkopírovat všechny soubory závislostí v části NugetPackage\build\runtime\ do pracovního adresáře projektu, který je obvykle v části ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Vytvoření testovacího případu skriptu U-SQL

Níže je ukázkový kód pro test skriptu U-SQL. Pro testování je třeba připravit skripty, vstupní soubory a očekávané výstupní soubory.

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


### <a name="programming-interfaces-in-localrunhelperexe"></a>Programovací rozhraní v souboru LocalRunHelper.exe

LocalRunHelper.exe poskytuje programovací rozhraní pro U-SQL lokální kompilaci, spuštění, atd. Rozhraní jsou uvedeny takto.

**Konstruktor**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parametr|Typ|Popis|
|---------|----|-----------|
|messageOutput|System.io.TextWriter|pro výstupní zprávy, nastavte hodnotu null pro použití konzoly|

**Vlastnosti**

|Vlastnost|Typ|Popis|
|--------|----|-----------|
|Algebracesta|řetězec|Cesta k souboru algebry (soubor algebry je jedním z výsledků kompilace)|
|CodeBehindReferences|řetězec|Pokud má skript za odkazy další kód, zadejte cesty oddělené ";".|
|CppSdkDir|řetězec|Adresář CppSDK|
|CurrentDir|řetězec|Aktuální adresář|
|Kořenová_složka_dat|řetězec|Kořenová cesta dat|
|DebuggerMailPath|řetězec|Cesta k slotu pro ladicí program|
|GenerateUdoRedirect|bool|Pokud chceme generovat přepsání přepsání přepsání přesměrovávacího zařízení načítání sestavy|
|HasCodeBehind|bool|Pokud má skript kód za|
|InputDir|řetězec|Adresář pro vstupní data|
|MessagePath|řetězec|Cesta k souboru výpisu zprávy|
|Výstupní dir|řetězec|Adresář pro výstupní data|
|Paralelnost|int|Paralelismus pro spuštění algebry|
|ParentPid|int|PID nadřazeného objektu, ve kterém služba monitoruje ukončení, nastaveno na hodnotu 0 nebo záporné, aby bylo ignorováno|
|Výsledná cesta|řetězec|Cesta k souboru výpisu výsledků|
|RuntimeDir|řetězec|Adresář runtime|
|Cesta skriptu|řetězec|Kde najít skript|
|Mělké|bool|Mělká kompilace nebo ne|
|Tempdir|řetězec|Dočasný adresář|
|UseDataBase|řetězec|Zadejte databázi, která se má použít pro kód za dočasnou registrací sestavení, hlavní ve výchozím nastavení|
|WorkDir|řetězec|Preferovaný pracovní adresář|


**Metoda**

|Metoda|Popis|Vrátit|Parametr|
|------|-----------|------|---------|
|veřejné bool DoCompile()|Kompilace skriptu U-SQL|Pravda o úspěchu| |
|veřejné bool DoExec()|Provedení zkompilovaného výsledku|Pravda o úspěchu| |
|veřejné bool DoRun()|Spuštění skriptu U-SQL (kompilace + spuštění)|Pravda o úspěchu| |
|veřejné bool IsValidRuntimeDir(cesta řetězce)|Zkontrolujte, zda je daná cesta platná cesta runtime|Platí pro platné|Cesta k adresáři runtime|


## <a name="faq-about-common-issue"></a>Nejčastější dotazy k běžnému problému

### <a name="error-1"></a>Chyba 1:
E_CSC_SYSTEM_INTERNAL: Vnitřní chyba! Nelze načíst soubor nebo sestavení ScopeEngineManaged.dll nebo jednu z jeho závislostí. Zadaný modul nebyl nalezen.

Zkontrolujte prosím následující:

- Ujistěte se, že máte prostředí x64. Cílová platforma sestavení a testovací prostředí by měly být x64, viz **krok 1: Vytvoření projektu testování částí c# a konfigurace** výše.
- Ujistěte se, že jste zkopírovali všechny soubory závislostí v rámci NugetPackage\build\runtime\ do pracovního adresáře projektu.


## <a name="next-steps"></a>Další kroky

* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informace o diagnostice najdete [v tématu Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Složitější dotaz najdete v [tématu Analýza protokolů webů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Podrobnosti o úloze najdete [v tématu Použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Pokud chcete použít zobrazení spuštění vrcholu, [přečtěte si informace o použití zobrazení spuštění vrcholu v nástrojích datového jezera pro visual studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
