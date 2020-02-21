---
title: Práce s Azure Functions Core Tools
description: Přečtěte si, jak pomocí příkazového řádku nebo terminálu v místním počítači Code a testovat službu Azure Functions, abyste je mohli spustit na Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 0b15b35f6fc83097e94f7d69815a163a0e98a228
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523267"
---
# <a name="work-with-azure-functions-core-tools"></a>Práce s Azure Functions Core Tools

Azure Functions Core Tools umožňuje vyvíjet a testovat funkce na místním počítači z příkazového řádku nebo terminálu. Vaše místní funkce se můžou připojit k živým službám Azure a své funkce můžete ladit na místním počítači pomocí modulu runtime Full Functions. Můžete dokonce nasadit aplikaci Function App do předplatného Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Vývoj funkcí v místním počítači a jejich publikování v Azure pomocí základních nástrojů se řídí těmito základními kroky:

> [!div class="checklist"]
> * [Nainstalujte základní nástroje a závislosti.](#v2)
> * [Vytvořte projekt Function App z šablony specifické pro konkrétní jazyk.](#create-a-local-functions-project)
> * [Zaregistrujte Trigger a rozšíření vazby.](#register-extensions)
> * [Definujte úložiště a další připojení.](#local-settings-file)
> * [Vytvořte funkci z triggeru a šablony pro konkrétní jazyk.](#create-func)
> * [Spusťte funkci místně.](#start)
> * [Publikujte projekt do Azure.](#publish)

## <a name="core-tools-versions"></a>Verze základních nástrojů

Existují tři verze Azure Functions Core Tools. Použitá verze závisí na vašem místním vývojovém prostředí, [výběru jazyka](supported-languages.md)a požadované úrovni podpory:

+ **Verze 1. x**: podporuje verzi 1. x modulu runtime Azure Functions. Tato verze nástrojů je podporována pouze v počítačích se systémem Windows a je instalována z [balíčku npm](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Verze 2. x/3. x**](#v2): podporuje buď [verzi 2. x, nebo 3. x modulu Azure Functions runtime](functions-versions.md). Tyto verze podporují [Windows](/azure/azure-functions/functions-run-local?tabs=windows#v2), [MacOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)a [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) a k instalaci používají správce balíčků pro konkrétní platformu nebo npm.

Pokud není uvedeno jinak, příklady v tomto článku jsou pro verzi 3. x.

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

[Azure Functions Core Tools] obsahuje verzi stejného modulu runtime, který funguje Azure Functions runtime, který můžete spustit na místním vývojovém počítači. Poskytuje také příkazy pro vytváření funkcí, připojení k Azure a nasazení projektů funkcí.

>[!IMPORTANT]
>Aby bylo možné publikovat do Azure z Azure Functions Core Tools, musíte mít místně nainstalované rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .  

### <a name="v2"></a>Verze 2. x a 3. x

Verze 2. x/3. x nástroje používá modul runtime Azure Functions, který je postaven na .NET Core. Tato verze je podporovaná na všech platformách .NET Core podporovaných [systémem](/azure/azure-functions/functions-run-local?tabs=windows#v2), včetně Windows, [MacOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)a [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> Požadavek na instalaci .NET Core SDK můžete obejít pomocí [sad rozšíření].

# <a name="windows"></a>[Windows](#tab/windows)

Následující kroky používají npm k instalaci základních nástrojů v systému Windows. Můžete také použít [čokolády](https://chocolatey.org/). Další informace najdete v [souboru Readme pro základní nástroje](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Nainstalujte [Node.js], který zahrnuje npm.
    - Pro verzi 2. x nástroje se podporují jenom Node. js 8,5 a novější verze.
    - Pro verzi 3. x nástroje je podporována pouze Node. js 10 a novější verze.

1. Nainstalujte balíček Core Tools:

    ##### <a name="v2x"></a>v2. x

    ```bash
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>V3. x

    ```bash
    npm install -g azure-functions-core-tools@3
    ```

   Stažení a instalace balíčku Core Tools může trvat několik minut, než se npm.

1. Pokud neplánujete použít [sad rozšíření], nainstalujte [sadu .NET Core 2. x SDK pro Windows](https://www.microsoft.com/net/download/windows).

# <a name="macos"></a>[MacOS](#tab/macos)

Následující kroky používají homebrew k instalaci základních nástrojů na macOS.

1. Nainstalujte [homebrew](https://brew.sh/), pokud ještě není nainstalovaný.

1. Nainstalujte balíček Core Tools:

    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>V3. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

Následující kroky používají [apt](https://wiki.debian.org/Apt) k instalaci základních nástrojů na distribuci Ubuntu/Debian Linux. Další distribuce pro Linux najdete v [souboru Readme pro základní nástroje](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Pokud chcete ověřit integritu balíčku, nainstalujte GPG klíč úložiště Microsoft Package.

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Před provedením aktualizace APT nastavte zdrojový seznam vývoje .NET.

   Chcete-li nastavit zdrojový seznam APT pro Ubuntu, spusťte tento příkaz:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Chcete-li nastavit zdrojový seznam APT pro Debian, spusťte tento příkaz:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. V souboru `/etc/apt/sources.list.d/dotnetdev.list` vyhledejte jeden z odpovídajících řetězců verze Linux uvedených níže:

    | Linuxové distribuce | Version |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mentolová 18    | `xenial`  |

1. Spusťte aktualizaci zdroje APT:

    ```bash
    sudo apt-get update
    ```

1. Nainstalujte balíček Core Tools:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Pokud neplánujete použít [sad rozšíření], nainstalujte [sadu .NET Core 2. x SDK pro Linux](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Vytvořte projekt místní funkce

Adresář projektu Functions obsahuje soubory [Host. JSON](functions-host-json.md) a [Local. Settings. JSON](#local-settings-file)spolu s podsložkami, které obsahují kód pro jednotlivé funkce. Tento adresář je ekvivalentem aplikace Function App v Azure. Další informace o struktuře složek Functions najdete v příručce pro [vývojáře Azure Functions](functions-reference.md#folder-structure).

Verze 2. x vyžaduje, abyste při inicializaci vybrali výchozí jazyk pro svůj projekt. Ve verzi 2. x se všechny přidané funkce použijí jako výchozí jazykové šablony. Ve verzi 1. x určíte jazyk pokaždé, když vytvoříte funkci.

V okně terminálu nebo z příkazového řádku spusťte následující příkaz, který vytvoří projekt a místní úložiště Git:

```bash
func init MyFunctionProj
```

Když zadáte název projektu, vytvoří se a inicializuje nová složka s tímto názvem. V opačném případě se aktuální složka inicializuje.  
Pokud ve verzi 2. x spustíte příkaz, musíte zvolit modul runtime pro váš projekt. 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

Pomocí kláves se šipkami nahoru/dolů vyberte jazyk a potom stiskněte klávesu ENTER. Pokud plánujete vývoj funkcí JavaScriptu nebo TypeScript, zvolte **uzel**a pak vyberte jazyk. TypeScript má [několik dalších požadavků](functions-reference-node.md#typescript). 

Výstup vypadá jako v následujícím příkladu pro projekt JavaScriptu:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` podporuje následující možnosti, které jsou pouze verze 2. x, pokud není uvedeno jinak:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Inicializuje [ C# projekt knihovny tříd (. cs)](functions-dotnet-class-library.md). |
| **`--csx`** | Inicializuje [ C# projekt skriptu (. csx)](functions-reference-csharp.md). V následujících příkazech je nutné zadat `--csx`. |
| **`--docker`** | Vytvořte souboru Dockerfile pro kontejner pomocí základní image, která je založená na zvolené `--worker-runtime`. Tuto možnost použijte, když plánujete publikování do vlastního kontejneru Linux. |
| **`--docker-only`** |  Přidá souboru Dockerfile do existujícího projektu. Vyzve se k zadání pracovního procesu – modul runtime, pokud není zadaný, nebo nastavený v souboru Local. Settings. JSON. Tuto možnost použijte, pokud plánujete publikovat existující projekt do vlastního kontejneru Linux. |
| **`--force`** | Inicializujte projekt i v případě, že v projektu existují existující soubory. Toto nastavení přepíše existující soubory se stejným názvem. Ostatní soubory ve složce projektu nejsou ovlivněny. |
| **`--java`**  | Inicializuje [projekt Java](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Inicializuje [projekt JavaScriptu](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Zabrání výchozímu vytvoření úložiště Git ve verzi 1. x. Ve výchozím nastavení ve verzi 2. x není úložiště Git vytvořeno. |
| **`--powershell`**  | Inicializuje [projekt prostředí PowerShell](functions-reference-powershell.md). |
| **`--python`**  | Inicializuje projekt v jazyce [Python](functions-reference-python.md). |
| **`--source-control`** | Určuje, zda je vytvořeno úložiště Git. Ve výchozím nastavení není úložiště vytvořeno. Při `true`se vytvoří úložiště. |
| **`--typescript`**  | Inicializuje [projekt TypeScript](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Nastaví jazykový modul runtime pro projekt. Podporovány jsou následující hodnoty: `csharp`, `dotnet`, `java`, `javascript`,`node` (JavaScript), `powershell`, `python`a `typescript`. Pokud není nastaven, budete vyzváni k výběru modulu runtime během inicializace. |

> [!IMPORTANT]
> Ve výchozím nastavení verze 2. x základních nástrojů vytváří projekty aplikací funkcí pro modul runtime .NET jako [ C# třídy projektů](functions-dotnet-class-library.md) (. csproj). Tyto C# projekty, které lze použít se sadou Visual Studio nebo Visual Studio Code, jsou kompilovány během testování a při publikování do Azure. Pokud místo toho chcete vytvořit a pracovat se stejnými C# soubory skriptu (. csx), které byly vytvořeny ve verzi 1. x a na portálu, je při vytváření a nasazování funkcí nutné zahrnout parametr `--csx`.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se tato nastavení nemigrují automaticky, když je projekt publikován do Azure. [Při publikování](#publish) použijte přepínač `--publish-local-settings`, abyste měli jistotu, že se tato nastavení přidají do aplikace Function App v Azure. Všimněte si, že hodnoty v **connectionStrings** nejsou nikdy publikovány.

Hodnoty nastavení aplikace Function App lze ve vašem kódu přečíst také jako proměnné prostředí. Další informace naleznete v části proměnné prostředí v těchto referenčních tématech specifických pro konkrétní jazyk:

* [C#předkompilované](functions-dotnet-class-library.md#environment-variables)
* [C#skript (. csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Pokud není nastaven žádný platný připojovací řetězec úložiště pro [`AzureWebJobsStorage`] a emulátor se nepoužívá, zobrazí se následující chybová zpráva:

> Chybějící hodnota pro AzureWebJobsStorage v Local. Settings. JSON. To je vyžadováno pro všechny triggery kromě HTTP. Můžete spustit funkce Func Azure functionapp Fetch-App-Settings \<functionAppName\>nebo zadat připojovací řetězec v souboru Local. Settings. JSON.

### <a name="get-your-storage-connection-strings"></a>Získání připojovacích řetězců úložiště

I při použití Emulátor úložiště Microsoft Azure pro vývoj budete možná chtít testovat pomocí skutečného připojení úložiště. Za předpokladu, že jste již [vytvořili účet úložiště](../storage/common/storage-create-storage-account.md), můžete získat platný připojovací řetězec úložiště jedním z následujících způsobů:

- V [Azure Portal]vyhledejte a vyberte **účty úložiště**. 
  ![vybrat účty úložiště z Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Vyberte svůj účet úložiště, vyberte **přístupové klíče** v **Nastavení**a pak zkopírujte jednu z hodnot **připojovacího řetězce** .
  ![zkopírování připojovacího řetězce z Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Pomocí [Průzkumník služby Azure Storage](https://storageexplorer.com/) se připojte ke svému účtu Azure. V **Průzkumníku**rozbalte své předplatné, rozbalte **účty úložiště**, vyberte svůj účet úložiště a zkopírujte primární nebo sekundární připojovací řetězec.

  ![Kopírovat připojovací řetězec z Průzkumník služby Storage](./media/functions-run-local/storage-explorer.png)

+ Použijte základní nástroje ke stažení připojovacího řetězce z Azure pomocí jednoho z následujících příkazů:

  + Stáhnout všechna nastavení z existující aplikace Function App:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Získání připojovacího řetězce pro konkrétní účet úložiště:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Pokud jste se ještě přihlásili k Azure, budete vyzváni k tomu.

## <a name="create-func"></a>Vytvoření funkce

Vytvořit funkci, spusťte následující příkaz:

```bash
func new
```

Když ve verzi 2. x spustíte `func new` budete vyzváni k výběru šablony ve výchozím jazyce aplikace Function App, zobrazí se také výzva k výběru názvu funkce. Ve verzi 1. x se zobrazí také výzva k výběru jazyka.

```output
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
```

Kód funkce se vygeneruje v podsložce se zadaným názvem funkce, jak vidíte v následujícím výstupu triggeru fronty:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Tyto možnosti můžete zadat také v příkazu pomocí následujících argumentů:

| Argument     | Popis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Verze 2. x) Vytvoří stejnou C# šablonu skriptu (. csx) použitou ve verzi 1. x a na portálu. |
| **`--language`** **`-l`**| Programovací jazyk šablony, například C#, F#nebo JavaScript. Tato možnost je vyžadována ve verzi 1. x. Ve verzi 2. x tuto možnost nepoužívejte nebo vyberte jazyk, který se shoduje s modulem runtime pracovního procesu. |
| **`--name`** **`-n`** | Název funkce |
| **`--template`** **`-t`** | Pomocí příkazu `func templates list` můžete zobrazit úplný seznam dostupných šablon pro každý podporovaný jazyk.   |

Například pro vytvoření triggeru HTTP JavaScriptu v jednom příkazu spusťte:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Chcete-li vytvořit funkci aktivovanou frontou v jednom příkazu, spusťte příkaz:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Místní spuštění funkcí

Chcete-li spustit projekt funkcí, spusťte hostitele Functions. Hostitel povolí triggery pro všechny funkce v projektu. 

### <a name="version-2x"></a>Verze 2. x

Ve verzi 2. x modulu runtime se spouštěcí příkaz liší v závislosti na jazyku projektu.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Verze 1. x

Verze 1. x modulu runtime Functions vyžaduje příkaz `host`, jak je uvedeno v následujícím příkladu:

```command
func host start
```

`func start` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Nevytvářejte aktuální projekt před spuštěním. Pouze pro projekty dotnet. Výchozí nastavení je false. Pouze verze 2. x. |
| **`--cert`** | Cesta k souboru. pfx, který obsahuje privátní klíč. Používá se jenom pro `--useHttps`. Pouze verze 2. x. |
| **`--cors-credentials`** | Povolte pouze ověřené požadavky mezi zdroji (tj. soubory cookie a záhlaví ověřování) pouze verze 2. x. |
| **`--cors`** | Čárkami oddělený seznam původů CORS bez mezer. |
| **`--language-worker`** | Argumenty pro konfiguraci modulu Language Worker. Můžete například povolit ladění pro Language Worker tím, že poskytnete [port ladění a další požadované argumenty](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Pouze verze 2. x. |
| **`--nodeDebugPort`** **`-n`** | Port pro použití ladicího programu Node. js. Výchozí: hodnota ze sady Launch. JSON nebo 5858. Pouze verze 1. x. |
| **`--password`** | Buď heslo, nebo soubor, který obsahuje heslo pro soubor. pfx. Používá se jenom pro `--cert`. Pouze verze 2. x. |
| **`--port`** **`-p`** | Místní port, na kterém má naslouchat. Výchozí hodnota: 7071. |
| **`--pause-on-error`** | Před ukončením procesu ponechejte další vstup. Používá se jenom při spouštění základních nástrojů z integrovaného vývojového prostředí (IDE).|
| **`--script-root`** **`--prefix`** | Slouží k zadání cesty ke kořenu aplikace Function App, která má být spuštěna nebo nasazena. Používá se pro kompilované projekty, které generují soubory projektu do podsložky. Například při sestavování projektu knihovny C# tříd se soubory Host. JSON, Local. Settings. JSON a Function. JSON generují v *kořenové* podsložce s cestou, jako je `MyProject/bin/Debug/netstandard2.0`. V takovém případě nastavte předponu jako `--script-root MyProject/bin/Debug/netstandard2.0`. Toto je kořen aplikace Function App při spuštění v Azure. |
| **`--timeout`** **`-t`** | Časový limit pro spuštění funkce Host v sekundách. Výchozí hodnota: 20 sekund.|
| **`--useHttps`** | Připojte se k `https://localhost:{port}` místo `http://localhost:{port}`. Ve výchozím nastavení tato možnost vytvoří důvěryhodný certifikát na vašem počítači.|

Když se hostitel funkce spustí, vypíše adresu URL funkcí aktivovaných protokolem HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Při místním spuštění není autorizace pro koncové body HTTP vynutila. To znamená, že všechny místní požadavky HTTP jsou zpracovávány jako `authLevel = "anonymous"`. Další informace najdete v [článku vázání http](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Předávání testovacích dat do funkce

Chcete-li místně testovat své funkce, [spusťte hostitele funkcí](#start) a koncové body volání na místním serveru pomocí požadavků HTTP. Koncový bod, který zavoláte, závisí na typu funkce.

>[!NOTE]
> V příkladech v tomto tématu se k posílání požadavků HTTP z terminálu nebo příkazového řádku používají nástroj pro otáčení. K odeslání požadavků HTTP na místní server můžete použít libovolný nástroj. Nástroj kudrlinkou je ve výchozím nastavení k dispozici v systémech Linux a Windows 10 Build 17063 a novějších. Ve starších oknech musíte nejdřív stáhnout a nainstalovat [Nástroj pro otáčení](https://curl.haxx.se/).

Obecnější informace o testovacích funkcích naleznete [v tématu strategie pro testování kódu v Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkce aktivované protokolem HTTP a webhookem

Zavoláte následující koncový bod pro místně spouštěné funkce HTTP a Webhooku:

    http://localhost:{port}/api/{function_name}

Ujistěte se, že používáte stejný název serveru a port, na kterém hostitel funkce naslouchá. Toto se zobrazí ve výstupu vygenerovaném při spouštění hostitele funkce. Tuto adresu URL můžete zavolat pomocí jakékoli metody HTTP podporované triggerem.

Následující příkaz oblé spustí funkci `MyHttpTrigger` Starter z požadavku GET s parametrem _Name_ předaným v řetězci dotazu.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

V následujícím příkladu je stejná funkce, která se volá z předávaného _názvu_ žádosti post v textu žádosti:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Můžete vytvářet požadavky GET z prohlížeče, které přecházejí data v řetězci dotazu. Pro všechny ostatní metody HTTP je nutné použít nástroj Fiddler, post nebo podobný nástroj pro testování HTTP.

#### <a name="non-http-triggered-functions"></a>Funkce, které nejsou aktivované protokolem HTTP

Pro všechny druhy funkcí kromě triggerů HTTP a webhooků můžete své funkce místně testovat voláním koncového bodu správy. Volání tohoto koncového bodu s požadavkem HTTP POST na místním serveru aktivuje funkci. Volitelně můžete předat testovací data do provádění v těle žádosti POST. Tato funkce je podobná kartě **test** v Azure Portal.

Zavoláte následující koncový bod správce, který aktivuje funkce jiného typu než HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Chcete-li předat testovací data do koncového bodu správce funkce, je nutné zadat data v těle zprávy s požadavkem POST. Tělo zprávy musí mít následující formát JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

Hodnota `<trigger_input>` obsahuje data ve formátu očekávaném funkcí. Následující příklad Oblé je příspěvek na funkci `QueueTriggerJS`. V tomto případě je vstupem řetězec, který je ekvivalentní zprávě, která se má ve frontě najít.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Použití příkazu `func run` ve verzi 1. x

>[!IMPORTANT]
> Příkaz `func run` není podporován ve verzi 2. x nástrojů. Další informace naleznete v tématu [jak cílit na Azure Functions verze modulu runtime](set-runtime-version.md).

Funkci lze také vyvolat přímo pomocí `func run <FunctionName>` a zadat vstupní data pro funkci. Tento příkaz je podobný spuštění funkce pomocí karty **test** v Azure Portal.

`func run` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--content`** **`-c`** | Vložený obsah. |
| **`--debug`** **`-d`** | Před spuštěním funkce připojte k hostitelskému procesu ladicí program.|
| **`--timeout`** **`-t`** | Doba, po kterou se má čekat (v sekundách), dokud nebude hostitel místní funkce připravený.|
| **`--file`** **`-f`** | Název souboru, který se má použít jako obsah|
| **`--no-interactive`** | Nezobrazuje výzvu k zadání. Užitečné pro scénáře automatizace.|

Například pro volání funkce aktivované protokolem HTTP a předejte tělo obsahu spusťte následující příkaz:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publikování do Azure

Azure Functions Core Tools podporuje dva typy nasazení: nasazení souborů projektu funkce přímo do aplikace Function App prostřednictvím nástroje [zip Deploy](functions-deployment-technologies.md#zip-deploy) a [nasazení vlastního kontejneru Docker](functions-deployment-technologies.md#docker-container). Musíte mít už [vytvořenou aplikaci Function App v předplatném Azure](functions-cli-samples.md#create), do které budete kód nasazovat. Projekty, které vyžadují kompilaci, by měly být sestaveny tak, aby mohly být nasazeny binární soubory.

>[!IMPORTANT]
>Aby bylo možné publikovat do Azure ze základních nástrojů, je nutné mít místně nainstalované rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) .  

Složka projektu může obsahovat soubory a adresáře specifické pro konkrétní jazyk, které by neměly být publikovány. Vyloučené položky jsou uvedeny v souboru. funcignore v kořenové složce projektu.     

### <a name="project-file-deployment"></a>Nasazení (soubory projektu)

Pokud chcete publikovat místní kód do aplikace Function App v Azure, použijte příkaz `publish`:

```bash
func azure functionapp publish <FunctionAppName>
```

Tento příkaz se publikuje do existující aplikace Function App v Azure. Při pokusu o publikování do `<FunctionAppName>`, která v předplatném neexistují, se zobrazí chyba. Informace o tom, jak vytvořit aplikaci funkcí z příkazového řádku nebo okna terminálu pomocí Azure CLI, najdete v tématu [vytvoření Function App pro provádění bez serveru](./scripts/functions-cli-create-serverless.md). Ve výchozím nastavení tento příkaz používá [vzdálené sestavení](functions-deployment-technologies.md#remote-build) a nasadí vaši aplikaci, aby [běžela z balíčku pro nasazení](run-functions-from-deployment-package.md). Pokud chcete tento doporučený režim nasazení zakázat, použijte možnost `--nozip`.

>[!IMPORTANT]
> Když vytváříte aplikaci funkcí v Azure Portal, používá ve výchozím nastavení verze 2. x modulu runtime funkce. Pokud chcete, aby aplikace Function App používala verzi 1. x modulu runtime, postupujte podle pokynů v části [Spustit ve verzi 1. x](functions-versions.md#creating-1x-apps).
> Nemůžete změnit verzi modulu runtime pro aplikaci Function App, která obsahuje existující funkce.

Následující možnosti publikování platí pro obě verze, 1. x a 2. x:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publikování nastavení v Local. Settings. JSON do Azure, zobrazení výzvy k přepsání, pokud už nastavení existuje. Pokud používáte Emulátor úložiště Microsoft Azure, změňte nejprve nastavení aplikace na [skutečné připojení úložiště](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Potlačí výzvu k přepsání nastavení aplikace při použití `--publish-local-settings -i`.|

Následující možnosti publikování jsou podporovány pouze ve verzi 2. x:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`** **`-o`** |  Pouze publikování nastavení a přeskočení obsahu. Výchozí hodnota je prompt. |
|**`--list-ignored-files`** | Zobrazí seznam souborů, které jsou během publikování ignorovány, které jsou založeny na souboru. funcignore. |
| **`--list-included-files`** | Zobrazí seznam souborů, které jsou publikovány, které jsou založeny na souboru. funcignore. |
| **`--nozip`** | Zapne výchozí režim `Run-From-Package`. |
| **`--build-native-deps`** | Při publikování aplikací funkcí Pythonu přeskočí vygenerování složky. kolaes. |
| **`--build`** **`-b`** | Provede akci sestavení při nasazení do aplikace Functions pro Linux. Akceptuje: `remote` a `local`. |
| **`--additional-packages`** | Seznam balíčků, které se mají nainstalovat při vytváření nativních závislostí Například: `python3-dev libevent-dev`. |
| **`--force`** | Ignorovat ověření před publikováním v některých scénářích. |
| **`--csx`** | Publikujte C# projekt skriptu (. csx). |
| **`--no-build`** | Nevytvářejte funkce knihovny tříd .NET. |
| **`--dotnet-cli-params`** | Při publikování kompilovaných C# funkcí (. csproj) volají základní nástroje "dotnet Build--Output bin/Publish". Všechny předané parametry budou připojeny k příkazovému řádku. |

### <a name="deployment-custom-container"></a>Nasazení (vlastní kontejner)

Azure Functions umožňuje nasadit projekt funkce ve [vlastním kontejneru Docker](functions-deployment-technologies.md#docker-container). Další informace najdete v tématu [Vytvoření funkce na platformě Linux s použitím vlastní image](functions-create-function-linux-custom-image.md). Vlastní kontejnery musí mít souboru Dockerfile. Pokud chcete vytvořit aplikaci s souboru Dockerfile, použijte možnost--souboru Dockerfile na `func init`.

```bash
func deploy
```

K dispozici jsou následující možnosti nasazení vlastního kontejneru:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Název registru Docker, ke kterému se přihlásil aktuální uživatel. |
| **`--platform`** | Platforma hostování aplikace Function App. Platné možnosti jsou `kubernetes` |
| **`--name`** | Název aplikace Function App |
| **`--max`**  | Volitelně můžete nastavit maximální počet instancí aplikace Function App, na které se mají nasadit. |
| **`--min`**  | Volitelně nastaví minimální počet instancí aplikace Function App, na které se má nasadit. |
| **`--config`** | Nastaví volitelný konfigurační soubor nasazení. |

## <a name="monitoring-functions"></a>Funkce monitorování

Doporučený způsob, jak monitorovat provádění vašich funkcí, je integrace s Azure Application Insights. Do svého místního počítače můžete také streamovat protokoly spouštění. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integrace Application Insights

Při vytváření aplikace Function App v Azure by měla být povolená integrace Application Insights. Pokud z nějakého důvodu vaše aplikace Function není připojená k instanci Application Insights, můžete tuto integraci snadno provést v Azure Portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Povolit streamování protokolů

Proud souborů protokolu generovaných funkcemi můžete zobrazit v relaci příkazového řádku na místním počítači. 

#### <a name="native-streaming-logs"></a>Nativní protokoly streamování

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Tento typ protokolů streamování vyžaduje, aby byla pro aplikaci Function App povolená Application Insights integrace.   


## <a name="next-steps"></a>Další kroky

Naučte se vyvíjet, testovat a publikovat Azure Functions pomocí Azure Functions Core Tools [modulu Microsoft učení](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools je [Open Source a hostovaný na GitHubu](https://github.com/azure/azure-functions-cli).  
Pokud chcete zaslat žádost o chybu nebo funkci, [otevřete problém GitHubu](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[sad rozšíření]: functions-bindings-register.md#extension-bundles
