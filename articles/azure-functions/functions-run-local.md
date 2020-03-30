---
title: Práce s nástroji Azure Functions Core Tools
description: Zjistěte, jak kódovat a testovat funkce Azure z příkazového řádku nebo terminálu v místním počítači, než je spustíte ve službě Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276657"
---
# <a name="work-with-azure-functions-core-tools"></a>Práce s nástroji Azure Functions Core Tools

Nástroje Azure Functions Core Tools umožňují vyvíjet a testovat funkce v místním počítači z příkazového řádku nebo terminálu. Místní funkce se můžou připojit k živým službám Azure a můžete ladit své funkce v místním počítači pomocí úplného běhu Funkce. Do předplatného Azure můžete dokonce nasadit aplikaci funkcí.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Vývoj funkcí v místním počítači a jejich publikování v Azure pomocí základních nástrojů se řídí následujícími základními kroky:

> [!div class="checklist"]
> * [Nainstalujte základní nástroje a závislosti.](#v2)
> * [Vytvořte projekt aplikace funkce ze šablony specifické pro jazyk.](#create-a-local-functions-project)
> * [Zaregistrujte aktivační události a rozšíření vazby.](#register-extensions)
> * [Definujte úložiště a další připojení.](#local-settings-file)
> * [Vytvořte funkci z aktivační události a šablony specifické pro jazyk.](#create-func)
> * [Spusťte funkci místně.](#start)
> * [Publikujte projekt do Azure.](#publish)

## <a name="core-tools-versions"></a>Verze nástrojů Core Tools

Existují tři verze nástrojů Azure Functions Core Tools. Verze, kterou používáte, závisí na místním vývojovém prostředí, [volbě jazyka](supported-languages.md)a požadované úrovni podpory:

+ **Verze 1.x**: Podporuje verzi 1.x runtime Funkce Azure. Tato verze nástrojů je podporována pouze v počítačích se systémem Windows a je nainstalována z [balíčku npm](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Verze 2.x/3.x**](#v2): Podporuje buď [verzi 2.x nebo 3.x runtime Funkce Azure](functions-versions.md). Tyto verze podporují [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2), [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)a [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) a pro instalaci používají správce balíčků specifických pro platformu nebo npm.

Pokud není uvedeno jinak, příklady v tomto článku jsou pro verzi 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

[Nástroje Azure Functions Core Tools] obsahuje verzi stejného běhu, která pohání runtime Azure Functions, který můžete spustit na místním vývojovém počítači. Poskytuje také příkazy k vytváření funkcí, připojení k Azure a nasazení projektů funkcí.

>[!IMPORTANT]
>Musíte mít azure [cli](/cli/azure/install-azure-cli) nainstalované místně, aby bylo možné publikovat do Azure z nástrojů Azure Functions Core Tools.  

### <a name="version-2x-and-3x"></a><a name="v2"></a>Verze 2.x a 3.x

Verze 2.x/3.x nástrojů používá runtime Azure Functions, který je postavený na .NET Core. Tato verze je podporována na všech platformách .NET Core podporuje, včetně [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2), [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)a [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> Požadavek na instalaci sady .NET Core SDK můžete obejít pomocí [rozšiřujících balíčků].

# <a name="windows"></a>[Windows](#tab/windows)

Následující kroky používají npm k instalaci nástrojů Core Tools v systému Windows. Můžete také použít [Chocolatey](https://chocolatey.org/). Další informace naleznete v [tématu Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Nainstalujte [soubor Node.js], který obsahuje soubor npm.
    - Pro verzi 2.x nástrojů jsou podporovány pouze Node.js 8.5 a novější verze.
    - Pro verzi 3.x nástrojů jsou podporovány pouze Node.js 10 a novější verze.

1. Nainstalujte balíček Nástroje jádra:

    ##### <a name="v2x"></a>v2.x

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   Stažení a instalace balíčku Core Tools může trvat několik minut.

1. Pokud neplánujete používat [balíčky rozšíření], nainstalujte sadu [.NET Core 2.x SDK pro Windows](https://www.microsoft.com/net/download/windows).

# <a name="macos"></a>[Macos](#tab/macos)

Následující kroky slouží k instalaci základních nástrojů do systému macOS pomocí homebrew.

1. Nainstalujte [Homebrew](https://brew.sh/), pokud ještě není nainstalován.

1. Nainstalujte balíček Nástroje jádra:

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

Následující kroky používají [APT](https://wiki.debian.org/Apt) k instalaci základních nástrojů do distribuce Ubuntu/Debian Linux. Další distribuce Linuxu najdete v tématu [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Nainstalujte klíč GPG úložiště balíčků společnosti Microsoft, abyste ověřili integritu balíčku:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Před provedením aktualizace APT nastavte seznam zdrojů vývoje rozhraní .NET.

   Chcete-li nastavit zdrojový seznam APT pro Ubuntu, spusťte tento příkaz:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Chcete-li nastavit zdrojový seznam APT pro Debian, spusťte tento příkaz:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Zkontrolujte, zda v souboru `/etc/apt/sources.list.d/dotnetdev.list` není uveden jeden z příslušných řetězců verzí Linuxu:

    | Linuxová distribuce | Version |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Spusťte zdrojovou aktualizaci APT:

    ```bash
    sudo apt-get update
    ```

1. Nainstalujte balíček Nástroje jádra:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Pokud neplánujete používat [balíčky rozšíření], nainstalujte [sadu .NET Core 2.x SDK pro Linux](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Vytvoření projektu místních funkcí

Adresář projektu funkce obsahuje soubory [host.json](functions-host-json.md) a [local.settings.json](#local-settings-file)spolu s podsložkami, které obsahují kód pro jednotlivé funkce. Tento adresář je ekvivalentem aplikace funkce v Azure. Další informace o struktuře složek Functions najdete v [průvodci vývojáři Azure Functions](functions-reference.md#folder-structure).

Verze 2.x vyžaduje, abyste při inicializaci projektu vybrali výchozí jazyk projektu. Ve verzi 2.x používají všechny přidané funkce výchozí jazykové šablony. Ve verzi 1.x určíte jazyk při každém vytvoření funkce.

V okně terminálu nebo na příkazovém řádku vytvořte projekt a místní úložiště Git následujícím příkazem:

```
func init MyFunctionProj
```

Pokud zadáte název projektu, vytvoří se a inicializuje se nová složka s tímto názvem. V opačném případě je inicializována aktuální složka.  
Ve verzi 2.x při spuštění příkazu je nutné zvolit runtime pro váš projekt. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Pomocí kláves se šipkami nahoru/dolů vyberte jazyk a stiskněte Enter. Pokud plánujete vyvíjet funkce JavaScriptu nebo TypeScriptu, zvolte **uzel**a pak vyberte jazyk. TypeScript má [některé další požadavky](functions-reference-node.md#typescript). 

Výstup vypadá jako následující příklad pro projekt JavaScriptu:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init`podporuje následující možnosti, které jsou pouze verze 2.x, pokud není uvedeno jinak:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Inicializuje [projekt knihovny tříd jazyka C# (.cs).](functions-dotnet-class-library.md) |
| **`--csx`** | Inicializuje [projekt skriptu Jazyka C# (.csx).](functions-reference-csharp.md) Je nutné `--csx` zadat v následujících příkazech. |
| **`--docker`** | Vytvořte dockerfile pro kontejner pomocí základní bitové `--worker-runtime`kopie, která je založena na zvoleném . Tuto možnost použijte, pokud plánujete publikovat do vlastního kontejneru Linuxu. |
| **`--docker-only`** |  Přidá dockerfile do existujícího projektu. Zobrazí výzvu k zadání doby běhu pracovníka, pokud není zadán nebo nastaven v souboru local.settings.json. Tuto možnost použijte, pokud plánujete publikovat existující projekt do vlastního kontejneru Linuxu. |
| **`--force`** | Inicializovat projekt i v případě, že existují existující soubory v projektu. Toto nastavení přepíše existující soubory se stejným názvem. Ostatní soubory ve složce projektu nejsou ovlivněny. |
| **`--java`**  | Inicializuje [projekt Java](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Inicializuje [projekt JavaScriptu](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Zabrání výchozímu vytvoření úložiště Git ve verzi 1.x. Ve verzi 2.x není úložiště git ve výchozím nastavení vytvořeno. |
| **`--powershell`**  | Inicializuje [projekt prostředí PowerShell](functions-reference-powershell.md). |
| **`--python`**  | Inicializuje [projekt Pythonu](functions-reference-python.md). |
| **`--source-control`** | Určuje, zda je vytvořeno úložiště git. Ve výchozím nastavení se úložiště nevytvoří. Když `true`je vytvořeno úložiště. |
| **`--typescript`**  | Inicializuje [projekt jazyka TypeScript](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Nastaví za běhu jazyka pro projekt. Podporované hodnoty `csharp`jsou: `java` `javascript`,`node` `dotnet`, , `powershell` `python`(JavaScript), , a `typescript`. Pokud není nastavena, budete vyzváni k výběru běhu během inicializace. |

> [!IMPORTANT]
> Ve výchozím nastavení verze 2.x základních nástrojů vytvoří projekty aplikací funkcí pro běh .NET jako [projekty třídy C#](functions-dotnet-class-library.md) (.csproj). Tyto projekty Jazyka C#, které lze použít s Visual Studio nebo Visual Studio Code, jsou kompilovány během testování a při publikování do Azure. Pokud místo toho chcete vytvořit a pracovat se stejným i c# skript (.csx) soubory vytvořené `--csx` ve verzi 1.x a na portálu, musíte zahrnout parametr při vytváření a nasazování funkcí.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se tato nastavení při publikování projektu do Azure nemigrují automaticky. Při `--publish-local-settings` publikování použijte přepínač a [ujistěte](#publish) se, že se tato nastavení přidají do aplikace funkce v Azure. Všimněte si, že hodnoty v **připojovacích řetězcích** nejsou nikdy publikovány.

Hodnoty nastavení aplikace funkce lze také číst ve vašem kódu jako proměnné prostředí. Další informace naleznete v části Proměnné prostředí v těchto referenčních tématech specifických pro daný jazyk:

* [Předkompilováno.](functions-dotnet-class-library.md#environment-variables)
* [Skript jazyka C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

Pokud není nastaven žádný platný [`AzureWebJobsStorage`] připojovací řetězec úložiště a emulátor se nepoužívá, zobrazí se následující chybová zpráva:

> Chybí hodnota pro AzureWebJobsStorage v local.settings.json. To je vyžadováno pro všechny aktivační události než HTTP. Můžete spustit 'func azure functionapp fetch-app-settings \<functionAppName\>' nebo zadat připojovací řetězec v local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Získání připojovacích řetězců úložiště

I při použití emulátoru úložiště Microsoft Azure pro vývoj můžete chtít otestovat s připojením skutečného úložiště. Za předpokladu, že jste již [vytvořili účet úložiště](../storage/common/storage-create-storage-account.md), můžete získat platný připojovací řetězec úložiště jedním z následujících způsobů:

- Na [webu Azure Portal]vyhledejte a vyberte **účty úložiště**. 
  ![Vybrat účty úložiště z webu Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Vyberte účet úložiště, **v**nastavení vyberte **Přístupové klávesy** a zkopírujte jednu z hodnot **připojovacího řetězce.**
  ![Kopírování připojovacího řetězce z webu Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- K připojení ke svému účtu Azure použijte [Průzkumníka azure.](https://storageexplorer.com/) V **Průzkumníkovi**rozbalte předplatné, rozbalte **účty úložiště**, vyberte účet úložiště a zkopírujte primární nebo sekundární připojovací řetězec.

  ![Kopírování připojovacího řetězce z Průzkumníka úložiště](./media/functions-run-local/storage-explorer.png)

+ Pomocí základních nástrojů stáhněte připojovací řetězec z Azure pomocí jednoho z následujících příkazů:

  + Stáhněte si všechna nastavení z existující aplikace funkcí:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Získejte připojovací řetězec pro konkrétní účet úložiště:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Když ještě nejste přihlášení k Azure, budete vyzváni k tomu.

## <a name="create-a-function"></a><a name="create-func"></a>Vytvoření funkce

Chcete-li vytvořit funkci, spusťte následující příkaz:

```
func new
```

Ve verzi 2.x se `func new` při spuštění zobrazí výzva k výběru šablony ve výchozím jazyce aplikace funkce, pak budete také vyzváni k výběru názvu funkce. Ve verzi 1.x budete také vyzváni k výběru jazyka.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

Kód funkce je generován v podsložce s názvem zadaným funkcí, jak můžete vidět v následujícím výstupu aktivační události fronty:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Tyto možnosti můžete také zadat v příkazu pomocí následujících argumentů:

| Argument     | Popis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Verze 2.x) Generuje stejné šablony skriptu Jazyka C# (.csx) používané ve verzi 1.x a na portálu. |
| **`--language`**, **`-l`**| Programovací jazyk šablony, například C#, F# nebo JavaScript. Tato možnost je vyžadována ve verzi 1.x. Ve verzi 2.x nepoužívejte tuto možnost nebo zvolte jazyk, který odpovídá pracovnímu běhu. |
| **`--name`**, **`-n`** | Název funkce. |
| **`--template`**, **`-t`** | Pomocí `func templates list` příkazu zobrazíte úplný seznam dostupných šablon pro každý podporovaný jazyk.   |

Chcete-li například vytvořit aktivační událost HTTP javascriptu v jednom příkazu, spusťte:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Chcete-li vytvořit funkci aktivovanou frontou v jednom příkazu, spusťte:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Spouštět funkce místně

Chcete-li spustit projekt Functions, spusťte hostitele Functions. Hostitel umožňuje aktivační události pro všechny funkce v projektu. Příkaz start se liší v závislosti na jazyce projektu.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[Javascript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Tento příkaz musí být [spuštěn ve virtuálním prostředí](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Verze 1.x funkce runtime `host` vyžaduje příkaz, jako v následujícím příkladu:
>
> ```
> func host start
> ```

`func start`podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Před spuštěním nevytvářejte aktuální projekt. Pouze pro projekty dotnet. Výchozí hodnota je nastavena na hodnotu false. Není podporováno pro verzi 1.x. |
| **`--cert`** | Cesta k souboru .pfx, který obsahuje soukromý klíč. Používá se `--useHttps`pouze s . Není podporováno pro verzi 1.x. |
| **`--cors-credentials`** | Povolit ověřené požadavky pro více původ (tj. soubory cookie a záhlaví ověřování) Není podporováno pro verzi 1.x. |
| **`--cors`** | Seznam počátků CORS oddělených čárkami bez mezer. |
| **`--language-worker`** | Argumenty pro konfiguraci jazykového pracovníka. Můžete například povolit ladění pro jazykového pracovníka poskytnutím [ladicího portu a dalších požadovaných argumentů](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Není podporováno pro verzi 1.x. |
| **`--nodeDebugPort`**, **`-n`** | Port pro ladicí program Node.js, který chcete použít. Výchozí: Hodnota z launch.json nebo 5858. Pouze verze 1.x. |
| **`--password`** | Heslo nebo soubor, který obsahuje heslo pro soubor .pfx. Používá se `--cert`pouze s . Není podporováno pro verzi 1.x. |
| **`--port`**, **`-p`** | Místní port poslouchat. Výchozí hodnota: 7071. |
| **`--pause-on-error`** | Pauza pro další vstup před ukončením procesu. Používá se pouze při spouštění nástrojů Core z integrovaného vývojového prostředí (IDE).|
| **`--script-root`**, **`--prefix`** | Slouží k určení cesty ke kořenové mu aplikace funkce, která má být spuštěna nebo nasazena. Používá se pro kompilované projekty, které generují soubory projektu do podsložky. Například při vytváření projektu knihovny tříd jazyka C# jsou soubory host.json, local.settings.json a function.json generovány v *kořenové* podsložce s cestou jako `MyProject/bin/Debug/netstandard2.0`. V takovém případě nastavte předponu jako `--script-root MyProject/bin/Debug/netstandard2.0`. Toto je kořen aplikace funkce při spuštění v Azure. |
| **`--timeout`**, **`-t`** | Časový čas pro hostitele Functions spustit v sekundách. Výchozí: 20 sekund.|
| **`--useHttps`** | Svázat `https://localhost:{port}` na `http://localhost:{port}`spíše než na . Ve výchozím nastavení tato možnost vytvoří v počítači důvěryhodný certifikát.|

Když se spustí hostitel Functions, vyvezí adresu URL funkcí spouštěných protokolem HTTP:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Při spuštění místně není autorizace vynucena pro koncové body HTTP. To znamená, že všechny místní `authLevel = "anonymous"`požadavky HTTP jsou zpracovány jako . Další informace naleznete v [článku vazby PROTOKOLU HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Předání testovacích dat funkci

Chcete-li otestovat své funkce místně, [spusťte funkce hostitele](#start) a koncových bodů volání na místním serveru pomocí požadavků HTTP. Koncový bod, který voláte, závisí na typu funkce.

>[!NOTE]
> Příklady v tomto tématu používají nástroj cURL k odesílání požadavků HTTP z terminálu nebo příkazového řádku. K odesílání požadavků HTTP na místní server můžete použít nástroj podle vašeho výběru. Nástroj cURL je ve výchozím nastavení k dispozici v systémech založených na Linuxu a systému Windows 10 sestavení 17063 a novějším. Ve starším systému Windows je třeba nejprve stáhnout a nainstalovat [nástroj cURL](https://curl.haxx.se/).

Další obecné informace o testovacích funkcích najdete [v tématu Strategie pro testování kódu ve funkcích Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkce spouštěné http a webhookem

Volání následující koncový bod místně spustit http a webhooku aktivované funkce:

    http://localhost:{port}/api/{function_name}

Ujistěte se, že používáte stejný název serveru a port, na který hostitel funkce naslouchá. Uvidíte to ve výstupu generované při spuštění hostitele funkce. Tuto adresu URL můžete volat pomocí libovolné metody HTTP podporované aktivační událostí.

Následující příkaz cURL aktivuje `MyHttpTrigger` funkci rychlého startu z požadavku GET s parametrem _name_ předaným v řetězci dotazu.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Následující příklad je stejná funkce volána z požadavku POST předávání _název_ v těle požadavku:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Požadavky GET můžete provádět z prohlížeče předávání dat v řetězci dotazu. Pro všechny ostatní metody HTTP je nutné použít cURL, Fiddler, Postman nebo podobný nástroj http testování.

#### <a name="non-http-triggered-functions"></a>Funkce aktivované protokolem HTTP

Pro všechny druhy funkcí než aktivační události HTTP a webhooky a aktivační události Event Grid, můžete otestovat funkce místně voláním koncový bod správy. Volání tohoto koncového bodu s požadavkem HTTP POST na místním serveru spustí funkci. 

Chcete-li testovat funkce aktivované službou Event Grid místně, přečtěte si [informace o místním testování pomocí webové aplikace prohlížeče](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Volitelně můžete předat data testu spuštění v těle požadavku POST. Tato funkce je podobná kartě **Test** na webu Azure Portal.

Volání následujícího koncového bodu správce pro aktivaci funkcí bez protokolu HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Chcete-li předat testovací data koncovému bodu správce funkce, je nutné zadat data v těle zprávy požadavku POST. Text zprávy musí mít následující formát JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

Hodnota `<trigger_input>` obsahuje data ve formátu očekávaném funkcí. Následující příklad cURL je POST `QueueTriggerJS` funkce. V tomto případě vstup je řetězec, který je ekvivalentní zprávy, které mají být nalezeny ve frontě.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Použití `func run` příkazu (pouze verze 1.x)

>[!IMPORTANT]
> Příkaz `func run` je podporován pouze ve verzi 1.x nástrojů. Další informace najdete v tématu [Jak cílit na runtime verze Azure Functions](set-runtime-version.md).

Ve verzi 1.x můžete také vyvolat funkci `func run <FunctionName>` přímo pomocí a poskytnout vstupní data pro funkci. Tento příkaz je podobný spuštění funkce pomocí karty **Test** na webu Azure Portal.

`func run`podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Obsah vsazený. |
| **`--debug`**, **`-d`** | Před spuštěním funkce připojte ladicí program k hostitelskému procesu.|
| **`--timeout`**, **`-t`** | Čas počkat (v sekundách), dokud nebude místní hostitel funkcí připraven.|
| **`--file`**, **`-f`** | Název souboru, který se má použít jako obsah.|
| **`--no-interactive`** | Nezobrazí výzvu k zadání. Užitečné pro scénáře automatizace.|

Chcete-li například volat funkci spouštěnou protokolem HTTP a předat tělo obsahu, spusťte následující příkaz:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publikování aplikací do Azure

Nástroje Azure Functions Core Tools podporují dva typy nasazení: nasazování souborů projektových souborů funkcí přímo do vaší aplikace funkcí prostřednictvím [nasazení ZIP](functions-deployment-technologies.md#zip-deploy) a nasazení [vlastního kontejneru Dockeru](functions-deployment-technologies.md#docker-container). Musíte mít už [vytvořili aplikaci funkce ve vašem předplatném Azure](functions-cli-samples.md#create), na které budete nasazovat kód. Projekty, které vyžadují kompilaci by měly být sestaveny tak, aby binární soubory lze nasadit.

>[!IMPORTANT]
>Musíte mít [azure cli](/cli/azure/install-azure-cli) nainstalované místně, aby bylo možné publikovat do Azure z základních nástrojů.  

Složka projektu může obsahovat soubory specifické pro jazyk a adresáře, které by neměly být publikovány. Vyloučené položky jsou uvedeny v souboru .funcignore v kořenové složce projektu.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Nasazení souborů projektu

Pokud chcete publikovat místní kód do aplikace `publish` funkcí v Azure, použijte příkaz:

```
func azure functionapp publish <FunctionAppName>
```

Tento příkaz publikuje do existující aplikace funkce v Azure. Pokud se pokusíte publikovat na a `<FunctionAppName>` která ve vašem předplatném neexistuje, zobrazí se chyba. Informace o tom, jak vytvořit aplikaci funkcí z příkazového řádku nebo okna terminálu pomocí příkazového příkazu Azure, najdete v [tématu Vytvoření aplikace funkcí pro spuštění bez serveru](./scripts/functions-cli-create-serverless.md). Ve výchozím nastavení tento příkaz používá [vzdálené sestavení](functions-deployment-technologies.md#remote-build) a nasazuje aplikaci ke spuštění z [balíčku nasazení](run-functions-from-deployment-package.md). Chcete-li zakázat tento `--nozip` doporučený režim nasazení, použijte tuto možnost.

>[!IMPORTANT]
> Když vytvoříte aplikaci funkcí na webu Azure Portal, použije ve výchozím nastavení verzi 2.x runtime Funkce. Chcete-li, aby aplikace funkce používala verzi 1.x runtime, postupujte podle pokynů v [části Spustit ve verzi 1.x](functions-versions.md#creating-1x-apps).
> Nelze změnit verzi runtime pro aplikaci funkce, která má existující funkce.

Následující možnosti publikování platí pro obě verze, 1.x a 2.x:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publikujte nastavení v local.settings.json do Azure s výzvou k přepsání, pokud nastavení již existuje. Pokud používáte emulátor úložiště Microsoft Azure, nejprve změňte nastavení aplikace na [skutečné připojení úložiště](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Potlačte výzvu k `--publish-local-settings -i` přepsání nastavení aplikace při použití.|

Následující možnosti publikování jsou podporovány pouze ve verzi 2.x:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Publikujte pouze nastavení a přeskočte obsah. Výchozí hodnota je výzva. |
|**`--list-ignored-files`** | Zobrazí seznam souborů, které jsou během publikování ignorovány, což je založeno na souboru .funcignore. |
| **`--list-included-files`** | Zobrazí seznam publikovaných souborů, který je založen na souboru .funcignore. |
| **`--nozip`** | Vypne výchozí `Run-From-Package` režim. |
| **`--build-native-deps`** | Přeskočí generování složky .wheels při publikování aplikací funkcí Pythonu. |
| **`--build`**, **`-b`** | Provádí akci sestavení při nasazování do aplikace funkce Linuxu. Přijímá: `remote` a `local`. |
| **`--additional-packages`** | Seznam balíčků, které mají být nainstalovány při vytváření nativních závislostí. Například: `python3-dev libevent-dev`. |
| **`--force`** | Ignorovat ověření před publikováním v určitých scénářích. |
| **`--csx`** | Publikujte projekt skriptu Jazyka C# (.csx). |
| **`--no-build`** | Nevytvářejte funkce knihovny tříd .NET. |
| **`--dotnet-cli-params`** | Při publikování kompilovaných funkcí jazyka C# (.csproj) základní nástroje volají "dotnet build --output bin/publish". Všechny parametry, které k tomu budou předány, budou připojeny k příkazovému řádku. |

### <a name="deploy-custom-container"></a>Nasazení vlastního kontejneru

Funkce Azure vám umožní nasadit projekt funkcí ve [vlastním kontejneru Dockeru](functions-deployment-technologies.md#docker-container). Další informace naleznete [v tématu Vytvoření funkce v Linuxu pomocí vlastní image](functions-create-function-linux-custom-image.md). Vlastní kontejnery musí mít Dockerfile. Chcete-li vytvořit aplikaci s dockerfile, použijte `func init`volbu --dockerfile na .

```
func deploy
```

K dispozici jsou následující možnosti vlastního nasazení kontejneru:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Název registru Dockeru, ke který se aktuální uživatel přihlásil. |
| **`--platform`** | Hostingová platforma pro aplikaci funkce. Platné možnosti jsou`kubernetes` |
| **`--name`** | Název aplikace funkce. |
| **`--max`**  | Volitelně nastaví maximální počet instancí aplikace funkce pro nasazení. |
| **`--min`**  | Volitelně nastaví minimální počet instancí aplikace funkce pro nasazení. |
| **`--config`** | Nastaví volitelný konfigurační soubor nasazení. |

## <a name="monitoring-functions"></a>Monitorovací funkce

Doporučený způsob, jak sledovat provádění vašich funkcí, je integrace s Azure Application Insights. Protokoly spuštění můžete také streamovat do místního počítače. Další informace najdete [v tématu Monitorování funkcí Azure](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integrace Application Insights

Integrace Application Insights by měla být povolena při vytváření aplikace funkcí v Azure. Pokud z nějakého důvodu vaše aplikace funkce není připojen k instanci Application Insights, je snadné provést tuto integraci na webu Azure Portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Povolení protokolů datových proudů

Datový proud souborů protokolu generovaných funkcemi můžete zobrazit v relaci příkazového řádku v místním počítači. 

#### <a name="native-streaming-logs"></a>Nativní protokoly streamování

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Tento typ protokolů datových proudů vyžaduje, aby byla povolena integrace Application Insights pro vaši aplikaci funkcí.   


## <a name="next-steps"></a>Další kroky

Zjistěte, jak vyvíjet, testovat a publikovat funkce Azure pomocí nástrojů Azure Functions Core Tools [Microsoft learn module](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools je open source a [hostovaný na GitHubu](https://github.com/azure/azure-functions-cli).  
Chcete-li podat žádost o chybu nebo funkci, [otevřete problém s GitHubem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Základní nástroje Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Portál Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
["AzureWebJobsStorage"]: functions-app-settings.md#azurewebjobsstorage
[rozšiřující balíčky]: functions-bindings-register.md#extension-bundles
