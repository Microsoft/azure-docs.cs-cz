---
title: Práce s Azure Functions Core Tools | Dokumentace Microsoftu
description: Zjistěte, jak pro kódování a testování Azure functions z příkazového řádku nebo terminálu v místním počítači před spuštěním ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: glenga
ms.openlocfilehash: 214f32c4dc35661480b96477caf0cdf6243c75a8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094237"
---
# <a name="work-with-azure-functions-core-tools"></a>Práce s Azure Functions Core Tools

Nástroje Azure Functions Core můžete vyvíjet a testovat funkce v místním počítači z příkazového řádku nebo terminálu. Lokální funkce se můžete připojit k za provozu služeb Azure a vaší funkce můžete ladit v místním počítači pomocí úplné modul runtime služby Functions. Aplikace function app můžete nasadit i do vašeho předplatného Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Základní verze nástroje

Existují dvě verze nástrojů Azure Functions Core. Verze použijete, závisí na svoje místní vývojové prostředí [volba jazyka](supported-languages.md)a je vyžadována podpora na úrovni:

+ [Verzi 1.x](#v1): podporuje verzi 1.x modulu runtime. Tato verze nástroje je podporována pouze na počítačích s Windows a je nainstalovaná ze [balíčku npm](https://docs.npmjs.com/getting-started/what-is-npm). S touto verzí můžete vytvořit funkce v následujících experimentálních jazyků, které nejsou oficiálně podporované. Další informace najdete v tématu [podporované jazyky ve službě Azure Functions](supported-languages.md)

+ [Verze 2.x](#v2): podporuje [verze 2.x modulu runtime](functions-versions.md). Tato verze podporuje [Windows](#windows-npm), [macOS](#brew), a [Linux](#linux). Správce balíčků specifických pro platformu používá nebo npm pro instalaci.

Pokud není uvedeno jinak, příklady v tomto článku platí pro verze 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

[Nástroje Azure Functions Core] obsahuje verzi stejného modulu runtime, která je základem modul runtime služby Azure Functions, který můžete spustit na místním počítači pro vývoj. Také poskytuje příkazy, které funkce můžete vytvářet připojení k Azure a nasazení projektů funkce.

### <a name="v2"></a>Verze 2.x

Verze 2.x nástrojů používá modul runtime služby Azure Functions 2.x, která je založená na prostředí .NET Core. Tato verze se podporuje na všech platformách .NET Core 2.x podporuje, včetně [Windows](#windows-npm), [macOS](#brew), a [Linux](#linux). Je třeba nejprve nainstalovat rozhraní .NET Core 2.x SDK.

#### <a name="windows-npm"></a>Windows

Následující kroky pomocí npm nainstalujte základní nástroje na Windows. Můžete také použít [Chocolatey](https://chocolatey.org/). Další informace najdete v tématu [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Nainstalujte [.NET Core 2.x SDK pro Windows](https://www.microsoft.com/net/download/windows).

2. Nainstalujte [Node.js], což zahrnuje npm. Pro verzi 2.x nástroje pouze Node.js 8.5 a novější verze podporují.

3. Instalace balíčku Core Tools:

    ```bash
    npm install -g azure-functions-core-tools
    ```

#### <a name="brew"></a>MacOS pomocí Homebrew

Následující kroky pomocí Homebrew v systému macOS nainstalujte základní nástroje.

1. Nainstalujte [.NET Core 2.x sady SDK pro macOS](https://www.microsoft.com/net/download/macos).

2. Nainstalujte [Homebrew](https://brew.sh/), pokud ještě není nainstalovaná.

3. Instalace balíčku Core Tools:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

#### <a name="linux"></a> Linux (Ubuntu nebo Debian) pomocí APT

Následující kroky použijte [APT](https://wiki.debian.org/Apt) instalace nástrojů pro jádra na vaší distribuci Linuxu Ubuntu nebo Debian. Pro jiné distribuce Linuxu najdete v článku [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Nainstalujte [.NET Core 2.x SDK pro Linux](https://www.microsoft.com/net/download/linux).

2. Zaregistrujte jako důvěryhodný kód product key společnosti Microsoft:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Ověřte, že váš server se systémem Ubuntu s některým z příslušné verze z tabulky níže. Pokud chcete přidat zdroje apt, spusťte:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linuxové distribuce | Verze |
    | --------------- | ----------- |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mátová 18    | `xenial`  |

4. Instalace balíčku Core Tools:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Vytvořte projekt místní funkce

Adresář projektu funkce obsahuje soubory [host.json](functions-host-json.md) a [local.settings.json](#local-settings-file), spolu s podsložky, které obsahují kód pro jednotlivé funkce. Tento adresář je ekvivalentem aplikaci function app v Azure. Další informace o struktuře složek funkce, najdete v článku [Příručka pro vývojáře Azure Functions](functions-reference.md#folder-structure).

Verze 2.x je potřeba vybrat výchozí jazyk pro projekt při inicializaci a použití výchozí jazyk šablony přidány všechny funkce. Ve verzi 1.x, můžete určit jazyk pokaždé, když vytvoříte funkci.

V okně terminálu nebo z příkazového řádku spusťte následující příkaz k vytvoření projektu a místní úložiště Git:

```bash
func init MyFunctionProj
```

Když zadáte název projektu, nová složka s tímto názvem je vytvořen a inicializován. V opačném případě je inicializován do aktuální složky.  
Ve verzi 2.x, když spustíte tento příkaz musíte zvolit modul runtime pro váš projekt. Pokud se chystáte vyvíjet funkce jazyka JavaScript, zvolte **uzel**:

```output
Select a worker runtime:
dotnet
node
```

Použití nahoru a dolů šipkami zvolte jazyk, stiskněte klávesu Enter. Výstup bude vypadat jako v následujícím příkladu pro projekt jazyka JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` podporuje následující možnosti, které jsou verze 2.x čistě, pokud není uvedeno jinak:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Inicializuje skriptu (.csx) projektu C#. Je nutné zadat `--csx` v následné příkazy. |
| **`--docker`** | Vytvořit soubor Dockerfile pro kontejner pomocí základní image, která je založena na zvolený `--worker-runtime`. Tuto možnost použijte, když chcete publikovat do vlastního kontejneru Linuxu. |
| **`--force`** | Inicializace projektu i v případě, že existují existující soubory v projektu. Toto nastavení přepíše existující soubory se stejným názvem. Nejsou k ovlivnění ostatních souborů ve složce projektu. |
| **`--no-source-control -n`** | Zabraňuje vytváření výchozí úložiště Git ve verzi 1.x. Ve verzi 2.x, ve výchozím nastavení se vytvoří úložiště git. |
| **`--source-control`** | Určuje, zda se vytvoří úložiště git. Ve výchozím nastavení se vytvoří úložiště. Když `true`, se vytvoří úložiště. |
| **`--worker-runtime`** | Nastaví modul runtime jazyka pro projekt. Podporované hodnoty jsou `dotnet`, `node` (JavaScript) `java`, a `python`. Pokud není nastavený, zobrazí se výzva k výběru prostředí runtime během inicializace. |

> [!IMPORTANT]
> Ve výchozím nastavení verze 2.x základní nástroje pro projekty aplikací pro .NET runtime jako vytvoří funkci [třídy projekty jazyka C#](functions-dotnet-class-library.md) (.csproj). Tyto projekty jazyka C#, které je možné použít s Visual Studio nebo Visual Studio Code, jsou zkompilovány při testování a publikování do Azure. Pokud místo toho chcete vytvořit a pracovat stejném skript jazyka C# (.csx) soubory vytvořené ve verzi 1.x a na portálu, musíte zahrnout `--csx` parametr při vytváření a nasazení služby functions.

## <a name="register-extensions"></a>Registrace rozšíření

Ve verzi 2.x modulu runtime Azure Functions, je nutné provést explicitně registraci rozšíření vazby (typy vazby), které používáte ve své aplikaci function app.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Další informace najdete v tématu [aktivace Azure Functions a vazby koncepty](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Soubor místního nastavení

Soubor local.settings.json ukládá nastavení aplikace, připojovacích řetězců a nastavení pro Azure Functions Core Tools. Nastavení v souboru local.settings.json používají pouze pomocí nástrojů funkce při místním spuštění. Ve výchozím nastavení se nemigrují automaticky při publikování projektu do Azure. Použití `--publish-local-settings` přepnout [při publikování](#publish) k Ujistěte se, že tato nastavení jsou přidány do aplikace function app v Azure. Všimněte si, že hodnoty v **ConnectionStrings** se nikdy publikováno. Soubor má následující strukturu:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Nastavení      | Popis                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Pokud je nastavena na **true**, všechny hodnoty jsou šifrované pomocí klíče místního počítače. Použít s `func settings` příkazy. Výchozí hodnota je **false**. |
| **Hodnoty** | Kolekce nastavení aplikace a připojovacích řetězců použité při místním spuštění. Tyto hodnoty odpovídají nastavení aplikace ve vaší aplikaci function app v Azure, jako například **AzureWebJobsStorage** a **AzureWebJobsDashboard**. Řada triggerů a vazeb mají vlastnost, která odkazuje na nastavení aplikace řetězec připojení, jako například **připojení** pro [aktivační událost objektů Blob storage](functions-bindings-storage-blob.md#trigger---configuration). Pro tyto vlastnosti definované v nastavení aplikace potřebujete **hodnoty** pole. <br/>**AzureWebJobsStorage** je požadovaná aplikace nastavení pro aktivační události než HTTP. Pokud máte [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) nainstalovaný místně, můžete nastavit **AzureWebJobsStorage** k `UseDevelopmentStorage=true` a základní nástroje pomocí emulátoru. To je užitečné při vývoji, ale měli byste otestovat připojení k skutečného úložiště před nasazením. |
| **Hostitel** | Nastavení v této části přizpůsobit funkce hostitelský proces, při místním spuštění. |
| **LocalHttpPort** | Nastaví výchozí port použitý při spuštění místního hostitele funkce (`func host start` a `func run`). `--port` Možnost příkazového řádku má přednost před tuto hodnotu. |
| **CORS** | Určuje původ, odkud můžou pro [prostředků mezi zdroji (CORS) pro sdílení obsahu](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje jsou dodávány jako seznam oddělený čárkami bez mezer. Hodnota zástupného znaku (\*) je podporován, umožňující žádosti z původu. |
| **ConnectionStrings** | Nepoužívejte připojovací řetězce, používá funkce vazby této kolekce. Tato kolekce používá pouze rozhraní, které obvykle získat připojovací řetězce z **ConnectionStrings** část konfigurační soubor, třeba [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tomto objektu jsou přidány do prostředí s typem zprostředkovatele [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci nejsou publikovány do Azure s jinými nastaveními aplikace. Musíte explicitně přidat tyto hodnoty **připojovací řetězce** kolekce vaše nastavení aplikace function app. Pokud vytváříte [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) v kódu funkce, měli byste uložit hodnotu připojovacího řetězce v **nastavení aplikace** u vašich připojení. |

Hodnoty nastavení aplikace funkcí můžete číst také ve vašem kódu jako proměnné prostředí. Další informace najdete v sekci proměnných prostředí z těchto témat reference specifická pro jazyk:

+ [Předkompilované C#](functions-dotnet-class-library.md#environment-variables)
+ [C# skript (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#skript (.fsx)](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables)
+ [JavaScript](functions-reference-node.md#environment-variables)

Pokud se žádný platný připojovací řetězec úložiště jsou nastavené pro **AzureWebJobsStorage** a se nepoužívá emulátor, se zobrazí následující chybová zpráva:

> Chybí hodnota pro AzureWebJobsStorage v local.settings.json. To je potřeba pro všechny aktivační události než HTTP. Můžete spustit "func azure functionapp načtení app-settings \<functionAppName\>" nebo zadat připojovací řetězec v local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Získejte připojovací řetězce úložiště

I když se používá pro vývoj pro emulátor úložiště, můžete otestovat s připojením k skutečného úložiště. Za předpokladu, že už máte [nevytvořili účet úložiště](../storage/common/storage-create-storage-account.md), platným připojovacím řetězcem úložiště můžete získat v jednom z následujících způsobů:

+ Z [Azure Portal]. Přejděte do svého účtu úložiště, vyberte **přístupové klíče** v **nastavení**, zkopírujte jeden z **připojovací řetězec** hodnoty.

  ![Zkopírujte připojovací řetězec z portálu Azure portal](./media/functions-run-local/copy-storage-connection-portal.png)

+ Použití [Průzkumníka služby Azure Storage](https://storageexplorer.com/) se připojit ke svému účtu Azure. V **Explorer**, rozšířit vaše předplatné, vyberte svůj účet úložiště a zkopírování primárního nebo sekundárního připojovacího řetězce.

  ![Zkopírujte připojovací řetězec z Průzkumníka služby Storage](./media/functions-run-local/storage-explorer.png)

+ Základní nástroje stáhněte si pomocí připojovacího řetězce z Azure vedenou jedním z následujících příkazů:

    + Stáhněte si všechna nastavení z existující aplikaci function app:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Získání připojovacího řetězce pro konkrétnímu účtu úložiště:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Pokud nejste ještě přihlášení do Azure, zobrazí se výzva k tomu.

## <a name="create-func"></a>Vytvoření funkce

Vytvořit funkci, spusťte následující příkaz:

```bash
func new
```

Ve verzi 2.x, když spustíte `func new` budete vyzváni k výběru šablony ve výchozím jazyce aplikace function App a potom zvolte název funkce se také výzva. Ve verzi 1.x, také budete vyzváni k volbě jazyka.

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

Funkce kód je generován v podsložce s názvem zadaná funkce, jak je vidět v následujícím výstupu aktivační událost fronty:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Můžete také zadat tyto možnosti v příkaz s následujícími argumenty:

| Argument     | Popis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Verze 2.x) Generuje stejné jazyka C# (.csx) skript šablony používané ve verzi 1.x a na portálu. |
| **`--language -l`**| Šablona programovací jazyk, jako například C#, F#, nebo JavaScript. Tato možnost je vyžadována ve verzi 1.x. Ve verzi 2.x, tuto možnost použijte, nebo zvolte jazyk, který odpovídá modulu runtime pracovního procesu. |
| **`--name -n`** | Název funkce. |
| **`--template -t`** | Použití `func templates list` příkazu zobrazte úplný seznam dostupných šablon pro každý podporovaný jazyk.   |

Například pokud chcete vytvořit trigger JavaScript HTTP stačí jediný příkaz, spusťte:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Vytvoření funkce aktivované fronty stačí jediný příkaz, spusťte:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Místní spuštění funkce

Chcete-li spustit projekt Functions, spusťte hostitele funkce. Hostitel umožňuje aktivační události pro všechny funkce v projektu:

```bash
func host start
```

`host` Příkazu je potřeba jenom ve verzi 1.x.

`func host start` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Provést aktuální projekt před provozováním žádná sestavení. Dotnet pouze pro projekty. Výchozí je nastavena na hodnotu false. Verze 2.x pouze. |
| **`--cert`** | Cesta k souboru .pfx, který obsahuje privátní klíč. Použít pouze s `--useHttps`. Verze 2.x pouze. |
| **`--cors-credentials`** | Povolit ověřený požadavky mezi zdroji (tj. soubory cookie a hlavičky ověřování) verze 2.x pouze. |
| **`--cors`** | Čárkou oddělený seznam zdrojů CORS, bez mezer. |
| **`--language-worker`** | Argumentů pro konfiguraci jazyka pracovního procesu. Verze 2.x pouze. |
| **`--nodeDebugPort -n`** | Port pro ladicí program uzel používat. Výchozí: Hodnota z launch.json nebo 5858. Verzi 1.x pouze. |
| **`--password`** | Heslo nebo soubor, který obsahuje heslo pro soubor .pfx. Použít pouze s `--cert`. Verze 2.x pouze. |
| **`--port -p`** | Místní port pro naslouchání. Výchozí hodnota: 7071. |
| **`--pause-on-error`** | Pozastavit další vstupní před ukončením procesu. Používá pouze v případě, že spuštění nástroje Core z integrovaného vývojového prostředí (IDE).|
| **`--script-root --prefix`** | Slouží k zadání cesty do kořenového adresáře aplikace function app, který se má spustit nebo nasadit. Používá se pro kompilované projekty, které Generovat soubory projektu do podsložky. Například při vytváření knihovny tříd jazyka C# projekt, host.json, local.settings.json a function.json soubory jsou generovány *kořenové* , jako je podsložka s cestou `MyProject/bin/Debug/netstandard2.0`. V takovém případě nastavte předponu jako `--script-root MyProject/bin/Debug/netstandard2.0`. To je kořenový adresář aplikace function app, při spuštění v Azure. |
| **`--timeout -t`** | Časový limit pro hostitele funkce spustit v řádu sekund. Výchozí: 20 sekund.|
| **`--useHttps`** | Vytvoření vazby k `https://localhost:{port}` spíše než na `http://localhost:{port}`. Ve výchozím nastavení tato volba vytvoří důvěryhodný certifikát ve vašem počítači.|

C# projekt knihovny tříd (.csproj), je třeba zahrnout `--build` možnost k vygenerování knihovny DLL.

Při spuštění funkce hostitele, uloží funkce aktivované protokolem URL HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Při místním spuštění ověřování se vynucují s ohledem koncové body HTTP. To znamená, že všechny místní požadavky HTTP jsou zpracovávány jako `authLevel = "anonymous"`. Další informace najdete v tématu [článku vazby HTTP](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Testovací předání dat pro funkci

K testování vašich funkcí místně, můžete [spuštění funkce hostitele](#start) a volají koncové body na místním serveru pomocí žádosti protokolu HTTP. Koncový bod, co zavoláte, závisí na typu funkce.

>[!NOTE]
> Příklady v tomto tématu použijte nástroj cURL k odeslání požadavků HTTP z terminálu nebo příkazového řádku. Nástroje podle vašeho výběru můžete použít k odesílání požadavků HTTP na místním serveru. Je nástroj cURL k dispozici ve výchozím nastavení v systémech založených na Linuxu. Na Windows, musíte nejdřív stáhnout a nainstalovat [nástroj cURL](https://curl.haxx.se/).

Další obecné informace o testování funkcí najdete v tématu [strategie pro testování kódu ve službě Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkce aktivované pomocí protokolu HTTP a webhookové

Volání této funkce aktivované pomocí koncového bodu HTTP a webhookové místně spustit:

    http://localhost:{port}/api/{function_name}

Nezapomeňte použít stejný název serveru a port, který naslouchá funkce hostitele. Se zobrazí ve výstupu generovány při spuštění funkce hostitele. Můžete volat tuto adresu URL pomocí libovolné metody HTTP nepodporuje aktivační události.

Následující cURL příkaz aktivační události `MyHttpTrigger` funkce rychlého startu z požadavku GET s _název_ parametr předaný řetězec dotazu.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

V následujícím příkladu je stejnou funkci volat z požadavku POST předávání _název_ v textu požadavku:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Můžete vytvořit žádosti o získání z prohlížeče předávání dat v řetězci dotazu. Pro všechny ostatní metody HTTP musíte použít cURL, Fiddler, Postman nebo podobný nástroj testování protokolu HTTP.

#### <a name="non-http-triggered-functions"></a>Funkce aktivované pomocí jiným protokolem než HTTP

Pro všechny typy funkcí jiné než triggerů HTTP a webhooky můžete otestovat funkce místně pomocí volání koncového bodu správy. Volání tohoto koncového bodu pomocí požadavku HTTP POST na místním serveru aktivuje funkci. Testovací data můžete volitelně předat ke spuštění v textu požadavku POST. Tato funkce je podobný **Test** karta na portálu Azure portal.

Můžete volat následující koncový bod správce k aktivaci funkce jiným protokolem než HTTP:

    http://localhost:{port}/admin/functions/{function_name}

K předání dat testu ke koncovému bodu správce funkce, je nutné zadat data v textu zprávy požadavku POST. Text zprávy musí mít následující formát JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` Hodnota obsahuje data ve formátu očekávání tím, funkce. Následující příklad cURL je odeslat do `QueueTriggerJS` funkce. V tomto případě je vstupní řetězec, který je ekvivalentní zpráva je očekávána nalezen ve frontě.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Použití `func run` příkaz ve verzi 1.x

>[!IMPORTANT]
> `func run` Příkaz není podporovaný ve verzi 2.x nástroje. Další informace naleznete v tématu [způsobu cílení verze modulu runtime Azure Functions](set-runtime-version.md).

Funkci lze vyvolat přímo pomocí `func run <FunctionName>` a poskytují vstupní data pro tuto funkci. Tento příkaz je podobný spouštění funkcí s použitím **Test** karta na portálu Azure portal.

`func run` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Vložený obsah. |
| **`--debug -d`** | Připojte ladicí program k hostitelskému procesu před spuštěním funkce.|
| **`--timeout -t`** | Doba čekání (v sekundách), dokud nebude připravené místního hostitele funkce.|
| **`--file -f`** | Název souboru použitého jako obsah.|
| **`--no-interactive`** | Dotaz na vstup. Užitečné pro scénáře automatizace.|

Volat funkci aktivovanou protokolem HTTP a předat text obsahu, spusťte následující příkaz:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Zobrazení protokolů místně

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publikování do Azure

Základní nástroje podporuje dva typy nasazení, nasazení soubory projektu funkce přímo do aplikace function app a nasazením vlastního kontejneru Linuxu, který je podporován pouze ve verzi 2.x. Musíte mít již [vytvoří aplikaci funkcí ve vašem předplatném Azure](functions-cli-samples.md#create).

Ve verzi 2.x, musíte mít [zaregistrovaný rozšíření](#register-extensions) ve vašem projektu před publikováním. Projekty, které vyžadují kompilace by měly být sestaveny tak, aby binární soubory je možné nasadit.

### <a name="project-file-deployment"></a>Nasazení souboru projektu

Nejběžnější metoda nasazení zahrnuje použití základní nástroje pro balení projektu aplikace funkcí, binární soubory a závislosti a nasadit balíček do vaší aplikace function app. Volitelně můžete [spustit přímo z balíčku pro nasazení vašich funkcí](run-functions-from-deployment-package.md).

Chcete-li publikovat projekt Functions do aplikace function app v Azure, použijte `publish` příkaz:

```bash
func azure functionapp publish <FunctionAppName>
```

Tento příkaz publikuje do existující aplikaci function app v Azure. Dojde k chybě při `<FunctionAppName>` neexistuje ve vašem předplatném. Zjistěte, jak vytvořit aplikaci function app z příkazového řádku nebo v okně terminálu pomocí Azure CLI, najdete v článku [vytvoření aplikace funkcí pro provádění bez serveru](./scripts/functions-cli-create-serverless.md).

`publish` Příkaz odešle obsah adresáře projektu funkce. Pokud odstraníte soubory lokálně, `publish` příkaz neodstranila z Azure. Můžete odstranit soubory v Azure pomocí [Kudu nástroj](functions-how-to-use-azure-function-app-settings.md#kudu) v [Azure Portal].

>[!IMPORTANT]
> Když vytvoříte aplikaci function app na webu Azure Portal, používá verzi 2.x modul runtime funkce ve výchozím nastavení. Chcete-li funkce aplikace použijte verzi 1.x modulu runtime, postupujte podle pokynů v [spustit ve verzi 1.x](functions-versions.md#creating-1x-apps).
> Nelze změnit verzi modulu runtime aplikace function App, který má existující funkce.

Následující projekt publikovat možnosti platí pro verze, 1.x a 2.x:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Nastavení publikování v local.settings.json do Azure, s výzvou k přepsání, pokud nastavení už existuje. Pokud používáte emulátor úložiště, můžete změnit nastavení aplikace, které chcete [skutečného úložiště připojení](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Potlačit výzva k nastavení aplikace přepsat při `--publish-local-settings -i` se používá.|

Následující projekt možnosti publikování jsou podporovány pouze ve verzi 2.x:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Pouze nastavení publikování a přejít obsah. Výchozí hodnota je prompt. |
|**`--list-ignored-files`** | Zobrazí seznam souborů, které jsou během publikování, který je založen na souboru .funcignore ignorován. |
| **`--list-included-files`** | Zobrazí seznam souborů, které jsou publikovány, který je založen na souboru .funcignore. |
| **`--nozip`** | Výchozí hodnota se změní `Run-From-Zip` režimu vypnout. |
| **`--build-native-deps`** | Aplikace function přeskočí .wheels složky při publikování pythonu. |
| **`--additional-packages`** | Seznam balíčků, které mají nainstalovat při sestavování nativních závislosti. Například: `python3-dev libevent-dev`. |
| **`--force`** | Ignorujte předem publikování ověřování v některých scénářích. |
| **`--csx`** | Publikování projektu skriptu (.csx) jazyka C#. |
| **`--no-build`** | Přeskočit sestavení dotnet funkce. |
| **`--dotnet-cli-params`** | Při publikování zkompilován funkcí jazyka C# (.csproj), základní nástroje volá 'dotnet build--bin/publikovat výstup'. Všechny parametry předané tomuto se připojí k příkazovému řádku. |

### <a name="custom-container-deployment"></a>Nasazení vlastního kontejneru

Funkce vám umožní nasadit projekt funkcí do vlastního kontejneru Linuxu. Další informace najdete v tématu [vytvoření funkce v Linuxu pomocí vlastní image](functions-create-function-linux-custom-image.md). Verze 2.x Core Tools podporuje nasazením vlastního kontejneru. Soubor Dockerfile musí mít vlastní kontejnery. Pomocí možnosti--soubor dockerfile v `func init`.

```bash
func deploy
```

Jsou k dispozici následující možnosti nasazení vlastního kontejneru:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Název registru Dockeru aktuálního uživatele přihlášeného k. |
| **`--platform`** | Hostitelskou platformu pro danou aplikaci funkcí. Platné možnosti jsou `kubernetes` |
| **`--name`** | Název aplikace funkcí. |
| **`--max`**  | Případně Nastaví maximální počet instancí funkce aplikace nasadit. |
| **`--min`**  | Volitelně Nastaví minimální počet instancí funkce aplikace nasadit. |
| **`--config`** | Nastaví konfigurační soubor volitelné nasazení. |

## <a name="next-steps"></a>Další postup

Nástroje Azure Functions Core je [open source a hostovaná na Githubu](https://github.com/azure/azure-functions-cli).  
Do souboru žádost chybu nebo funkce [otevřete problém na Githubu](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Nástroje Azure Functions Core]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
