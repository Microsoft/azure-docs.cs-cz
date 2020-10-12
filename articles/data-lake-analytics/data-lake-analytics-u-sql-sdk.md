---
title: Místní spuštění úloh U-SQL – Azure Data Lake sadu U-SQL SDK
description: Naučte se spouštět a testovat úlohy U-SQL místně pomocí příkazového řádku a programovacích rozhraní na místní pracovní stanici.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: daf72fcf7baba289b4145d06d878c8a7232f1c6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132411"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Spuštění a testování U-SQL s Azure Data Lake U-SQL SDK

Při vývoji skriptu U-SQL je běžné spouštět a testovat skript U-SQL místně před odesláním do cloudu. Azure Data Lake poskytuje balíček NuGet s názvem Azure Data Lake SDK U-SQL pro tento scénář, pomocí kterého můžete snadno škálovat běh a testování U-SQL. K automatizaci kompilace a testování je také možné integrovat tento test U-SQL se systémem CI (průběžný Integration).

Pokud se zajímáte, jak ručně spustit místní a ladit skript U-SQL pomocí nástrojů grafického uživatelského rozhraní, můžete pro to použít Nástroje Azure Data Lake pro Visual Studio. Další informace najdete [tady](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Instalace Azure Data Lake U-SQL SDK

Azure Data Lake sadu U-SQL SDK můžete získat [tady](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) na NuGet.org. A před použitím je nutné se ujistit, že máte závislosti následujícím způsobem.

### <a name="dependencies"></a>Závislosti

Data Lake SDK U-SQL vyžaduje následující závislosti:

- [Microsoft .NET Framework 4,6 nebo novější](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 a Windows SDK 10.0.10240.0 nebo novější (označované jako CppSDK v tomto článku). Existují dva způsoby, jak získat CppSDK:

  - Nainstalujte [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Budete mít složku \Windows Kits\10 ve složce Program Files – například C:\Program Files (x86) \Windows Kits\10\. V části \Windows Kits\10\Lib. najdete taky verzi Windows 10 SDK. Pokud tyto složky nevidíte, přeinstalujte sadu Visual Studio a nezapomeňte při instalaci vybrat sadu Windows 10 SDK. Pokud máte tuto instalaci se sadou Visual Studio, místní kompilátor U-SQL ho automaticky najde.

    ![Data Lake Tools for Visual Studio Local-run Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Nainstalovat [Data Lake nástroje pro Visual Studio](https://aka.ms/adltoolsvs). Předbalený Visual C++ a soubory Windows SDK můžete najít na adrese `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    V tomto případě místní kompilátor U-SQL nemůže závislosti automaticky najít. Je nutné zadat cestu k CppSDK. Soubory můžete buď zkopírovat do jiného umístění, nebo je použít tak, jak jsou.

## <a name="understand-basic-concepts"></a>Principy základních konceptů

### <a name="data-root"></a>Kořen dat

Kořenová složka data je místní úložiště pro místní výpočetní účet. Je ekvivalentní s účtem Azure Data Lake Store účtu Data Lake Analytics. Přepnutí na jiná data – kořenová složka je stejně jako přepnutí na jiný účet úložiště. Pokud chcete mít přístup k běžně sdíleným datům s různými kořenovými složkami dat, musíte ve svých skriptech použít absolutní cesty. Případně můžete vytvořit symbolické odkazy systému souborů (například **MKLINK** v systému souborů NTFS) v kořenové složce data-Point na sdílená data.

Kořenová složka data se používá k těmto akcím:

- Ukládejte místní metadata, včetně databází, tabulek, funkcí vracejících tabulku (TVF) a sestavení.
- Vyhledejte vstupní a výstupní cesty, které jsou definovány jako relativní cesty v U-SQL. Použití relativních cest usnadňuje nasazení projektů U-SQL do Azure.

### <a name="file-path-in-u-sql"></a>Cesta k souboru v U-SQL

V skriptech U-SQL můžete použít jak relativní cestu, tak i místní absolutní cestu. Relativní cesta je relativní vzhledem k zadaným datům – cesta ke kořenové složce. Doporučujeme použít "/" jako oddělovač cest k zajištění, aby byly skripty kompatibilní se serverem. Tady je několik příkladů relativních cest a jejich ekvivalentních absolutních cest. V těchto příkladech je C:\LocalRunDataRoot kořenová složka data-root.

|Relativní cesta|Absolutní cesta|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Pracovní adresář

Při místním spuštění skriptu U-SQL je pracovní adresář vytvořen během kompilace v rámci aktuálního spuštěného adresáře. Kromě výstupů kompilace budou k tomuto pracovnímu adresáři zkopírovány nezbytné běhové soubory pro místní spuštění. Kořenová složka pracovního adresáře se nazývá "ScopeWorkDir" a soubory v pracovním adresáři jsou následující:

|Adresář nebo soubor|Adresář nebo soubor|Adresář nebo soubor|Definice|Popis|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Řetězec hash verze modulu runtime|Stínová kopie souborů modulu runtime potřebných pro místní spuštění|
| |Script_66AE4909AA0ED06C| |Název skriptu + hash řetězec cesty ke skriptu|Výstupy kompilace a protokolování kroků spuštění|
| | |\_skript \_ . ABR|Výstup kompilátoru|Soubor algebraický|
| | |\_ScopeCodeGen \_ . *|Výstup kompilátoru|Generovaný spravovaný kód|
| | |\_ScopeCodeGenEngine \_ . *|Výstup kompilátoru|Generovaný nativní kód|
| | |odkazovaná sestavení|Odkaz na sestavení|Odkazované soubory sestavení|
| | |deployed_resources|Nasazení prostředků|Soubory nasazení prostředků|
| | |xxxxxxxx. xxx [1.. n] \_ \* . *|Protokol spuštění|Protokol kroků provedení|

## <a name="use-the-sdk-from-the-command-line"></a>Použití sady SDK z příkazového řádku

### <a name="command-line-interface-of-the-helper-application"></a>Rozhraní příkazového řádku pomocné aplikace

V rámci sady SDK directory\build\runtime LocalRunHelper.exe je pomocná aplikace příkazového řádku, která poskytuje rozhraní pro většinu běžně používaných funkcí místního spuštění. Všimněte si, že oba příkazy a přepínače argumentů rozlišují velká a malá písmena. Pro vyvolání:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

Pokud chcete zobrazit informace o nápovědě, spusťte LocalRunHelper.exe bez argumentů nebo pomocí přepínače **help** :

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

V informacích o nápovědě:

- **Příkaz** poskytuje název příkazu.  
- **Požadovaný argument** uvádí argumenty, které musí být dodány.  
- **Volitelný argument** uvádí nepovinné argumenty s výchozími hodnotami.  Volitelné logické argumenty nemají parametry a jejich vzhledy znamenají negativní význam na jejich výchozí hodnotu.

### <a name="return-value-and-logging"></a>Návratová hodnota a protokolování

Pomocná aplikace vrátí **0** pro úspěch a **-1** pro selhání. Ve výchozím nastavení pomocník odesílá všechny zprávy do aktuální konzoly. Většina příkazů ale podporuje **path_to_log_file** nepovinný argument, který přesměruje výstupy do souboru protokolu.

### <a name="environment-variable-configuring"></a>Konfigurace proměnné prostředí

Místní spuštění U-SQL potřebuje zadaný datový kořen jako místní účet úložiště a také zadanou cestu CppSDK pro závislosti. Můžete nastavit argument na příkazovém řádku nebo nastavit proměnnou prostředí pro ně.

- Nastavte proměnnou prostředí **SCOPE_CPP_SDK** .

  Pokud získáte Microsoft Visual C++ a Windows SDK instalací Data Lakech nástrojů pro Visual Studio, ověřte, že máte následující složku:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Definujte novou proměnnou prostředí nazvanou **SCOPE_CPP_SDK** , která odkazuje na tento adresář. Nebo zkopírujte složku do druhého umístění a zadejte **SCOPE_CPP_SDK** .

  Kromě nastavení proměnné prostředí můžete zadat argument **-CppSDK** při použití příkazového řádku. Tento argument přepíše výchozí proměnnou prostředí CppSDK.

- Nastavte proměnnou prostředí **LOCALRUN_DATAROOT** .

  Definujte novou proměnnou prostředí nazvanou **LOCALRUN_DATAROOT** , která odkazuje na kořen dat.

  Kromě nastavení proměnné prostředí můžete při použití příkazového řádku zadat **kořenový parametr-** dataroot s cestou k datovému kořenu. Tento argument přepíše výchozí proměnnou prostředí pro kořen dat. Tento argument je nutné přidat do každého spouštěného příkazového řádku, abyste mohli přepsat výchozí proměnnou prostředí kořene dat pro všechny operace.

### <a name="sdk-command-line-usage-samples"></a>Ukázky použití příkazového řádku sady SDK

#### <a name="compile-and-run"></a>Zkompilovat a spustit

Příkaz **Run** slouží ke kompilaci skriptu a následnému spuštění kompilovaných výsledků. Argumenty příkazového řádku jsou kombinací funkcí pro **kompilaci** a **spouštění**.

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

Níže jsou uvedené nepovinné argumenty pro **běh**:

|Argument|Výchozí hodnota|Description|
|--------|-------------|-----------|
|– CodeBehind|Nepravda|Skript obsahuje kód. cs za|
|-CppSDK| |Adresář CppSDK|
|– Dataroot| Proměnná prostředí dataroot|Dataroot pro místní spuštění, výchozí pro proměnnou prostředí ' LOCALRUN_DATAROOT '|
|– Zpráva| |Vypsat zprávy v konzole nástroje do souboru|
|– Paralelní|1|Spustit plán se zadaným paralelismu|
|– Odkazy| |Seznam cest k dodatečným referenčním sestavením nebo datovým souborům kódu, které jsou oddělené znakem '; '|
|-UdoRedirect|Nepravda|Generovat konfiguraci přesměrování Udo sestavení|
|-UseDatabase|master|Databáze, která se má použít pro kód za dočasnou registraci sestavení|
|– Verbose|Nepravda|Zobrazit podrobné výstupy z modulu runtime|
|-WorkDir|Aktuální adresář|Adresář pro použití a výstupy kompilátoru|
|-RunScopeCEP|0|Režim ScopeCEP, který se má použít|
|-ScopeCEPTempPath|temp|Dočasná cesta, která se má použít pro streamovaná data|
|-OptFlags| |Čárkami oddělený seznam příznaků Optimalizátoru|

Tady je příklad:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

Kromě kombinování **kompilace** a **provádění**lze zkompilovat a spustit samostatně zkompilované spustitelné soubory.

#### <a name="compile-a-u-sql-script"></a>Kompilace skriptu U-SQL

Příkaz **Compile** slouží ke kompilaci skriptu U-SQL do spustitelných souborů.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

Následující jsou nepovinné argumenty pro **kompilaci**:

|Argument|Description|
|--------|-----------|
| -CodeBehind [výchozí hodnota false]|Skript obsahuje kód. cs za|
| -CppSDK [výchozí hodnota]|Adresář CppSDK|
| -Dataroot [výchozí hodnota ' dataroot prostředí proměnná ']|Dataroot pro místní spuštění, výchozí pro proměnnou prostředí ' LOCALRUN_DATAROOT '|
| -Messageing [výchozí hodnota]|Vypsat zprávy v konzole nástroje do souboru|
| -Odkazuje na [výchozí hodnota].|Seznam cest k dodatečným referenčním sestavením nebo datovým souborům kódu, které jsou oddělené znakem '; '|
| -Omezený [výchozí hodnota false]|Podkompilace bez podstruktury|
| -UdoRedirect [výchozí hodnota false]|Generovat konfiguraci přesměrování Udo sestavení|
| -UseDatabase [výchozí hodnota ' Master ']|Databáze, která se má použít pro kód za dočasnou registraci sestavení|
| -WorkDir [výchozí hodnota ' aktuální adresář ']|Adresář pro použití a výstupy kompilátoru|
| -RunScopeCEP [výchozí hodnota 0]|Režim ScopeCEP, který se má použít|
| -ScopeCEPTempPath [výchozí hodnota ' Temp ']|Dočasná cesta, která se má použít pro streamovaná data|
| -OptFlags [výchozí hodnota]|Čárkami oddělený seznam příznaků Optimalizátoru|

Tady je několik příkladů použití.

Zkompilujte skript U-SQL:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Zkompilujte skript U-SQL a nastavte kořenovou složku data-SQL. Všimněte si, že tato akce přepíše proměnnou prostředí set.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Zkompilujte skript U-SQL a nastavte pracovní adresář, referenční sestavení a databázi:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Spustit kompilované výsledky

Příkaz **Execute** slouží ke spuštění kompilovaných výsledků.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

Níže jsou uvedené volitelné argumenty pro **provedení**:

|Argument|Výchozí hodnota|Description|
|--------|-------------|-----------|
|– Dataroot | '' |Kořen dat pro provedení metadat. Nastaví se jako výchozí proměnná prostředí **LOCALRUN_DATAROOT** .|
|– Zpráva | '' |Vypíše zprávy v konzole nástroje do souboru.|
|– Paralelní | první |Indikátor pro spuštění generovaných kroků místního spuštění se zadanou úrovní paralelismus.|
|– Verbose | Chybné |Indikátor pro zobrazení podrobných výstupů z modulu runtime.|

Tady je příklad použití:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>Použití sady SDK s programovacími rozhraními

Rozhraní pro programování jsou umístěna v LocalRunHelper.exe. Můžete je použít k integraci funkcí sady U-SQL SDK a testovacího rozhraní C# pro škálování místního testu skriptu U-SQL. V tomto článku použijeme standardní projekt testování jednotek v jazyce C# k tomu, abyste viděli, jak tato rozhraní použít k otestování skriptu U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Krok 1: vytvoření projektu a konfigurace testu jednotek C#

- Vytvořte projekt testů jednotek v jazyce C# pomocí souboru > nový > projekt > Visual C# > test > jednotek projektu.
- Přidejte LocalRunHelper.exe jako odkaz pro projekt. LocalRunHelper.exe se nachází v \build\runtime\LocalRunHelper.exe v balíčku NuGet.

   ![Azure Data Lake přidat odkaz na sadu SDK U-SQL SDK](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Sada U-SQL SDK podporuje **pouze** prostředí x64, nezapomeňte nastavit cíl platformy sestavení jako x64. Můžete nastavit, aby prostřednictvím vlastnosti projektu > cílová platforma >.

   ![Azure Data Lake sady U-SQL SDK konfigurace projektu x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ujistěte se, že jste nastavili testovací prostředí jako x64. V sadě Visual Studio je můžete nastavit pomocí testovacích > nastavení testu > výchozí architekturu procesoru > x64.

   ![Azure Data Lake sady U-SQL SDK konfigurace testovacího prostředí x64](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Nezapomeňte zkopírovat všechny soubory závislosti v rámci NugetPackage\build\runtime\ do pracovního adresáře, který je obvykle v rámci ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Krok 2: Vytvoření testovacího případu skriptu U-SQL

Níže je uveden vzorový kód pro test skriptu U-SQL. Pro účely testování musíte připravit skripty, vstupní soubory a očekávané výstupní soubory.

```usql
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
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Rozhraní pro programování v LocalRunHelper.exe

LocalRunHelper.exe poskytuje rozhraní pro programování pro místní kompilaci U-SQL, spuštění atd. Rozhraní jsou uvedena níže.

### <a name="constructor"></a>Konstruktor

Public LocalRunHelper ([System. IO. TextWriter messageOutput = null])

|Parametr|Typ|Description|
|---------|----|-----------|
|messageOutput|System. IO. TextWriter|pro výstupní zprávy nastavte na hodnotu null, aby se použila konzola.|

### <a name="properties"></a>Vlastnosti

|Vlastnost|Typ|Description|
|--------|----|-----------|
|AlgebraPath|řetězec|Cesta k souboru algebraický (soubor algebraický je jedním z výsledků kompilace)|
|CodeBehindReferences|řetězec|Pokud má skript další kód na pozadí, zadejte cesty oddělené znakem '; '|
|CppSdkDir|řetězec|Adresář CppSDK|
|CurrentDir|řetězec|Aktuální adresář|
|DataRoot|řetězec|Cesta ke kořenu dat|
|DebuggerMailPath|řetězec|Cesta k ladicímu programu zásuvek pošty|
|GenerateUdoRedirect|bool|Chcete-li generovat konfiguraci přepsání přesměrování načítání sestavení|
|HasCodeBehind|bool|Pokud má skript kód za|
|InputDir|řetězec|Adresář pro vstupní data|
|MessagePath|řetězec|Cesta k souboru výpisu zpráv|
|OutputDir|řetězec|Adresář pro výstupní data|
|Paralelismu|int|Paralelismus ke spuštění algebraický|
|ParentPid|int|PID nadřazeného objektu, na kterém se služba sleduje, je nastavená na hodnotu 0 nebo záporná hodnota, která se má ignorovat.|
|ResultPath|řetězec|Cesta k souboru výpisu výsledků|
|RuntimeDir|řetězec|Běhový adresář|
|ScriptPath|řetězec|Kde najít skript|
|Seznamu|bool|Bez podkompilace nebo ne|
|TempDir|řetězec|Dočasný adresář|
|UseDataBase|řetězec|Zadejte databázi, která se má použít pro kód za dočasným registrací sestavení, hlavní ve výchozím nastavení.|
|WorkDir|řetězec|Preferovaný pracovní adresář|

### <a name="method"></a>Metoda

|Metoda|Popis|Vrátit|Parametr|
|------|-----------|------|---------|
|Public bool DoCompile ()|Kompilace skriptu U-SQL|True při úspěchu| |
|Public bool DoExec ()|Spustí zkompilovaný výsledek.|True při úspěchu| |
|Public bool DoRun ()|Spuštění skriptu U-SQL (kompilovat a provést)|True při úspěchu| |
|Public bool IsValidRuntimeDir (cesta k řetězci)|Zkontroluje, jestli daná cesta představuje platnou cestu k běhu.|True pro platné|Cesta k adresáři modulu runtime|

## <a name="faq-about-common-issue"></a>Nejčastější dotazy týkající se běžných potíží

### <a name="error-1"></a>Chyba 1

E_CSC_SYSTEM_INTERNAL: došlo k vnitřní chybě! Nelze načíst soubor nebo sestavení ' ScopeEngineManaged.dll ' nebo některou z jeho závislostí. Zadaný modul nebyl nalezen.

Zkontrolujte prosím následující:

- Ujistěte se, že máte prostředí x64. Cílová platforma sestavení a testovací prostředí by měly být x64, Projděte si **téma krok 1: vytvoření projektu testování částí v jazyce C# a konfigurace** výše.
- Ujistěte se, že jste zkopírovali všechny soubory závislosti v NugetPackage\build\runtime\ do pracovního adresáře projektu.

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informace o diagnostice protokolu najdete v tématu [přístup k protokolům diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
- Složitější dotaz najdete v tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Podrobnosti o úlohách najdete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Chcete-li použít zobrazení spuštění vrcholu, přečtěte si téma [použití zobrazení spuštění vrcholu v nástroji Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
