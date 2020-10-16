---
title: Práce s Azure Functions Core Tools
description: Přečtěte si, jak pomocí příkazového řádku nebo terminálu v místním počítači Code a testovat službu Azure Functions, abyste je mohli spustit na Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 094449ddcbfc2baf41657d00de030ef3c1cddbb5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108486"
---
# <a name="work-with-azure-functions-core-tools"></a>Práce s Azure Functions Core Tools

Nástroje Azure Functions Core Tools umožňují vyvíjet a testovat funkce na místním počítači z příkazového řádku nebo terminálu. Vaše místní funkce se můžou připojit k živým službám Azure a své funkce můžete ladit na místním počítači pomocí modulu runtime Full Functions. Můžete dokonce nasadit aplikaci Function App do předplatného Azure.

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

## <a name="core-tools-versions"></a>Verze nástrojů Core Tools

Existují tři verze Azure Functions Core Tools. Použitá verze závisí na vašem místním vývojovém prostředí, [výběru jazyka](supported-languages.md)a požadované úrovni podpory:

+ [**Verze 3. x/2. x**](#v2): podporuje buď [verzi 3. x, nebo 2. x Azure Functions runtime](functions-versions.md). Tyto verze podporují [Windows](?tabs=windows#v2), [MacOS](?tabs=macos#v2)a [Linux](?tabs=linux#v2) a k instalaci používají správce balíčků pro konkrétní platformu nebo npm.

+ **Verze 1. x**: podporuje verzi 1. x modulu runtime Azure Functions. Tato verze nástrojů je podporována pouze v počítačích se systémem Windows a je instalována z [balíčku npm](https://www.npmjs.com/package/azure-functions-core-tools).

V daném počítači můžete nainstalovat jenom jednu verzi základních nástrojů. Pokud není uvedeno jinak, příklady v tomto článku jsou pro verzi 3. x.

## <a name="prerequisites"></a>Předpoklady

Azure Functions Core Tools v současné době závisí na Azure CLI pro ověřování pomocí účtu Azure. To znamená, že musíte [Azure CLI nainstalovat místně](/cli/azure/install-azure-cli) , aby bylo možné [publikovat do Azure](#publish) z Azure Functions Core Tools. 

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

[Azure Functions Core Tools] obsahuje verzi stejného modulu runtime, který funguje Azure Functions runtime, který můžete spustit na místním vývojovém počítači. Poskytuje také příkazy pro vytváření funkcí, připojení k Azure a nasazení projektů funkcí.

### <a name="version-3x-and-2x"></a><a name="v2"></a>Verze 3. x a 2. x

Verze 3. x/2. x nástroje používá modul runtime Azure Functions, který je postaven na .NET Core. Tato verze je podporovaná na všech platformách .NET Core podporovaných [systémem](?tabs=windows#v2), včetně Windows, [MacOS](?tabs=macos#v2)a [Linux](?tabs=linux#v2). 

> [!IMPORTANT]
> Požadavek na instalaci .NET Core SDK můžete obejít pomocí [sad rozšíření].

# <a name="windows"></a>[Windows](#tab/windows)

Následující kroky používají Instalační službu systému Windows (MSI) k instalaci základních nástrojů v3. x. Další informace o dalších instalačních nástrojích na základě balíčků, které jsou potřeba k instalaci základních nástrojů v2. x, najdete v [souboru Readme pro základní nástroje](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Stáhněte a spusťte instalační program základních nástrojů na základě vaší verze systému Windows:

    - [v3. x-Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2135274) (doporučeno. [Visual Studio Code ladění](functions-develop-vs-code.md#debugging-functions-locally) vyžaduje 64-bit.)
    - [V3. x-Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2135275)

1. Pokud neplánujete použít [sady rozšíření](functions-bindings-register.md#extension-bundles), nainstalujte [sadu .NET Core 3. x SDK pro Windows](https://dotnet.microsoft.com/download).

# <a name="macos"></a>[macOS](#tab/macos)

Následující kroky používají homebrew k instalaci základních nástrojů na macOS.

1. Nainstalujte [homebrew](https://brew.sh/), pokud ještě není nainstalovaný.

1. Nainstalujte balíček Core Tools:

    ##### <a name="v3x-recommended"></a>V3. x (doporučeno)

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```
    
    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. Pokud neplánujete použít [sady rozšíření](functions-bindings-register.md#extension-bundles), nainstalujte [sadu .NET Core 3. x SDK pro MacOS](https://dotnet.microsoft.com/download).

# <a name="linux"></a>[Linux](#tab/linux)

Následující kroky používají [apt](https://wiki.debian.org/Apt) k instalaci základních nástrojů na distribuci Ubuntu/Debian Linux. Další distribuce pro Linux najdete v [souboru Readme pro základní nástroje](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Pokud chcete ověřit integritu balíčku, nainstalujte GPG klíč úložiště Microsoft Package.

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Před provedením aktualizace APT nastavte zdrojový seznam APT.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Podívejte se na `/etc/apt/sources.list.d/dotnetdev.list` soubor pro jeden z odpovídajících řetězců verze Linux uvedených níže:

    | Distribuce systému Linux | Verze |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mentolová 18    | `xenial`  |

1. Spusťte aktualizaci zdroje APT:

    ```bash
    sudo apt-get update
    ```

1. Nainstalujte balíček Core Tools:

    ##### <a name="v3x-recommended"></a>V3. x (doporučeno)
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```
    
    ##### <a name="v2x"></a>v2. x
    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. Pokud neplánujete použít [sady rozšíření](functions-bindings-register.md#extension-bundles), nainstalujte [sadu .NET Core 3. x SDK pro Linux](https://dotnet.microsoft.com/download).

---

## <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

Adresář projektu Functions obsahuje soubory [host.jszapnuto](functions-host-json.md) a [local.settings.jsna](#local-settings-file), společně s podsložkami, které obsahují kód pro jednotlivé funkce. Tento adresář je ekvivalentem aplikace Function App v Azure. Další informace o struktuře složek Functions najdete v příručce pro [vývojáře Azure Functions](functions-reference.md#folder-structure).

Verze 3. x/2. x vyžaduje, abyste při inicializaci pro svůj projekt vybrali výchozí jazyk. Ve verzi 3. x/2. x se přidané všechny funkce používají výchozí jazykové šablony. Ve verzi 1. x určíte jazyk pokaždé, když vytvoříte funkci.

V okně terminálu nebo z příkazového řádku spusťte následující příkaz, který vytvoří projekt a místní úložiště Git:

```
func init MyFunctionProj
```

>[!IMPORTANT]
> Java používá Maven Archetype k vytvoření projektu místní funkce spolu s první funkcí aktivovanou protokolem HTTP. Pro vytvoření projektu Java použijte následující příkaz: `mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype` . Příklad použití Maven Archetype naleznete v tématu [rychlý Start k příkazovému řádku](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java).  

Když zadáte název projektu, vytvoří se a inicializuje nová složka s tímto názvem. V opačném případě se aktuální složka inicializuje.  
Pokud ve verzi 3. x/2. x spustíte příkaz, musíte zvolit modul runtime pro váš projekt. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Pomocí kláves se šipkami nahoru/dolů vyberte jazyk a potom stiskněte klávesu ENTER. Pokud plánujete vývoj funkcí JavaScriptu nebo TypeScript, zvolte **uzel**a pak vyberte jazyk. TypeScript má [několik dalších požadavků](functions-reference-node.md#typescript). 

Výstup vypadá jako v následujícím příkladu pro projekt JavaScriptu:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` podporuje následující možnosti, které jsou verze 3. x/2. x-Only, pokud není uvedeno jinak:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Vytvoří funkce .NET jako skript jazyka C#, což je chování verze 1. x. Platí pouze pro `--worker-runtime dotnet` . |
| **`--docker`** | Vytvoří souboru Dockerfile pro kontejner pomocí základní image, která je založena na zvoleném typu `--worker-runtime` . Tuto možnost použijte, když plánujete publikování do vlastního kontejneru Linux. |
| **`--docker-only`** |  Přidá souboru Dockerfile do existujícího projektu. Vyzve se k zadání pracovního procesu – modul runtime, pokud není zadaný, nebo nastavený v local.settings.js. Tuto možnost použijte, pokud plánujete publikovat existující projekt do vlastního kontejneru Linux. |
| **`--force`** | Inicializujte projekt i v případě, že v projektu existují existující soubory. Toto nastavení přepíše existující soubory se stejným názvem. Ostatní soubory ve složce projektu nejsou ovlivněny. |
| **`--language`** | Inicializuje projekt specifický pro jazyk. Aktuálně podporováno, pokud je `--worker-runtime` nastavena na `node` . Možnosti jsou `typescript` a `javascript` . Můžete také použít `--worker-runtime javascript` nebo `--worker-runtime typescript` .  |
| **`--managed-dependencies`**  | Nainstaluje spravované závislosti. V současné době je tato funkce podporovaná jenom modulem runtime PowerShell Worker. |
| **`--source-control`** | Určuje, zda je vytvořeno úložiště Git. Ve výchozím nastavení není úložiště vytvořeno. Kdy `true` je vytvořeno úložiště. |
| **`--worker-runtime`** | Nastaví jazykový modul runtime pro projekt. Podporovány jsou následující hodnoty: `csharp` , `dotnet` , `javascript` , `node` (JavaScript),, a `powershell` `python` `typescript` . Pro Java použijte [Maven](functions-reference-java.md#create-java-functions). Pokud není nastaven, budete vyzváni k výběru modulu runtime během inicializace. |
|
> [!IMPORTANT]
> Ve výchozím nastavení používají verze 2. x a novější verze základních nástrojů k vytváření projektů aplikací Function App pro modul runtime .NET jako [projekty tříd C#](functions-dotnet-class-library.md) (. csproj). Tyto projekty C#, které lze použít se sadou Visual Studio nebo Visual Studio Code, jsou kompilovány během testování a při publikování do Azure. Pokud chcete vytvořit a pracovat se stejnými soubory skriptu C# (. csx), které byly vytvořeny ve verzi 1. x a na portálu, je nutné `--csx` při vytváření a nasazování funkcí použít parametr.

## <a name="register-extensions"></a>Registrovat rozšíření

S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby funkcí v modulu runtime verze 2. x a vyšší implementovány jako balíčky rozšíření. Vazby HTTP a triggery časovače nevyžadují rozšíření. 

Pro snížení nekompatibility mezi různými balíčky rozšíření vám funkce umožní odkazovat na sadu rozšíření v host.jssouboru projektu. Pokud se rozhodnete nepoužívat sady rozšíření, budete také muset nainstalovat sadu .NET Core 2. x SDK místně a udržovat rozšíření. csproj pomocí projektu Functions.  

Ve verzi 2. x a mimo Azure Functions runtime musíte explicitně zaregistrovat rozšíření pro typy vazeb používané ve vašich funkcích. Můžete zvolit instalaci rozšíření vazby jednotlivě nebo můžete přidat odkaz na balíček rozšíření na host.jsv souboru projektu. Sady rozšíření odstraňují možnost mít problémy s kompatibilitou balíčků při použití více typů vazeb. Je doporučený postup pro registraci rozšíření vazby. Sady rozšíření také odstraňují požadavek na instalaci sady .NET Core 2. x SDK. 

### <a name="use-extension-bundles"></a>Použití sad rozšíření

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

Další informace najdete v tématu [Registrace rozšíření vazby Azure Functions](functions-bindings-register.md#extension-bundles). Než přidáte vazby do function.jsv souboru, měli byste do host.jspřidat balíčky rozšíření.

### <a name="explicitly-install-extensions"></a>Explicitní instalace rozšíření

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ve výchozím nastavení se tato nastavení nemigrují automaticky, když je projekt publikován do Azure. `--publish-local-settings` [Při publikování](#publish) použijte přepínač, abyste měli jistotu, že se tato nastavení přidají do aplikace Function App v Azure. Všimněte si, že hodnoty v **connectionStrings** nejsou nikdy publikovány.

Hodnoty nastavení aplikace Function App lze ve vašem kódu přečíst také jako proměnné prostředí. Další informace naleznete v části proměnné prostředí v těchto referenčních tématech specifických pro konkrétní jazyk:

* [Předkompilovaná C#](functions-dotnet-class-library.md#environment-variables)
* [Skript jazyka C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

Pokud není nastaven žádný platný připojovací řetězec úložiště pro [`AzureWebJobsStorage`] a emulátor se nepoužívá, zobrazí se následující chybová zpráva:

> Chybějící hodnota pro AzureWebJobsStorage v local.settings.js. To je vyžadováno pro všechny triggery kromě HTTP. Můžete spustit rutinu Func Azure functionapp Fetch-App-Settings \<functionAppName\> nebo zadat připojovací řetězec v local.settings.js.

### <a name="get-your-storage-connection-strings"></a>Získání připojovacích řetězců úložiště

I při použití Emulátor úložiště Microsoft Azure pro vývoj budete možná chtít testovat pomocí skutečného připojení úložiště. Za předpokladu, že jste již [vytvořili účet úložiště](../storage/common/storage-account-create.md), můžete získat platný připojovací řetězec úložiště jedním z následujících způsobů:

- V [Azure Portal]vyhledejte a vyberte **účty úložiště**. 
  ![Vyberte účty úložiště z Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Vyberte svůj účet úložiště, vyberte **přístupové klíče** v **Nastavení**a pak zkopírujte jednu z hodnot **připojovacího řetězce** .
  ![Kopírovat připojovací řetězec z Azure Portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Pomocí [Průzkumník služby Azure Storage](https://storageexplorer.com/) se připojte ke svému účtu Azure. V **Průzkumníku**rozbalte své předplatné, rozbalte **účty úložiště**, vyberte svůj účet úložiště a zkopírujte primární nebo sekundární připojovací řetězec.

  ![Kopírovat připojovací řetězec z Průzkumník služby Storage](./media/functions-run-local/storage-explorer.png)

+ Použijte základní nástroje z kořenového adresáře projektu ke stažení připojovacího řetězce z Azure pomocí jednoho z následujících příkazů:

  + Stáhnout všechna nastavení z existující aplikace Function App:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + Získání připojovacího řetězce pro konkrétní účet úložiště:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Pokud jste se ještě přihlásili k Azure, budete vyzváni k tomu. Tyto příkazy přepíšou všechna existující nastavení v local.settings.jssouboru. 

## <a name="create-a-function"></a><a name="create-func"></a>Vytvoření funkce

Chcete-li vytvořit funkci, spusťte následující příkaz:

```
func new
```

Když ve verzi 3. x/2. x spustíte `func new` výzvu k výběru šablony ve výchozím jazyce aplikace Function App, zobrazí se také výzva k výběru názvu funkce. Ve verzi 1. x se zobrazí také výzva k výběru jazyka.

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

Kód funkce se vygeneruje v podsložce se zadaným názvem funkce, jak vidíte v následujícím výstupu triggeru fronty:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Tyto možnosti můžete zadat také v příkazu pomocí následujících argumentů:

| Argument     | Popis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Verze 2. x a novější verze) Vygeneruje stejné šablony skriptu C# (. csx), které se používají ve verzi 1. x a na portálu. |
| **`--language`**, **`-l`**| Programovací jazyk šablony, například C#, F # nebo JavaScript. Tato možnost je vyžadována ve verzi 1. x. Ve verzi 2. x a novějších verzích tuto možnost nepoužívejte nebo vyberte jazyk, který se shoduje s modulem runtime pracovního procesu. |
| **`--name`**, **`-n`** | Název funkce |
| **`--template`**, **`-t`** | Pomocí `func templates list` příkazu můžete zobrazit úplný seznam dostupných šablon pro každý podporovaný jazyk.   |


Například pro vytvoření triggeru HTTP JavaScriptu v jednom příkazu spusťte:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Chcete-li vytvořit funkci aktivovanou frontou v jednom příkazu, spusťte příkaz:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Místní spuštění funkcí

Chcete-li spustit projekt funkcí, spusťte hostitele Functions. Hostitel povolí triggery pro všechny funkce v projektu. Spouštěcí příkaz se liší v závislosti na jazyku projektu.

# <a name="c"></a>[R\#](#tab/csharp)

```
func start --build
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Tento příkaz musí být [spuštěn ve virtuálním prostředí](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Verze 1. x modulu runtime Functions vyžaduje `host` příkaz, jak je uvedeno v následujícím příkladu:
>
> ```
> func host start
> ```

`func start` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Nevytvářejte aktuální projekt před spuštěním. Pouze pro projekty dotnet. Výchozí nastavení je false. Nepodporováno pro verzi 1. x. |
| **`--cors-credentials`** | Povoluje ověřené požadavky mezi zdroji (tj. soubory cookie a záhlaví ověřování) nejsou podporovány pro verzi 1. x. |
| **`--cors`** | Čárkami oddělený seznam původů CORS bez mezer. |
| **`--language-worker`** | Argumenty pro konfiguraci modulu Language Worker. Můžete například povolit ladění pro Language Worker tím, že poskytnete [port ladění a další požadované argumenty](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Nepodporováno pro verzi 1. x. |
| **`--cert`** | Cesta k souboru. pfx, který obsahuje privátní klíč. Používá se jenom pro `--useHttps` . Nepodporováno pro verzi 1. x. |
| **`--password`** | Buď heslo, nebo soubor, který obsahuje heslo pro soubor. pfx. Používá se jenom pro `--cert` . Nepodporováno pro verzi 1. x. |
| **`--port`**, **`-p`** | Místní port, na kterém má naslouchat. Výchozí hodnota: 7071. |
| **`--pause-on-error`** | Před ukončením procesu ponechejte další vstup. Používá se jenom při spouštění základních nástrojů z integrovaného vývojového prostředí (IDE).|
| **`--script-root`**, **`--prefix`** | Slouží k zadání cesty ke kořenu aplikace Function App, která má být spuštěna nebo nasazena. Používá se pro kompilované projekty, které generují soubory projektu do podsložky. Například při sestavování projektu knihovny tříd jazyka C# jsou host.jsna, local.settings.jsna a function.jsna soubory generovány v *kořenové* podsložce s cestou, jako je například `MyProject/bin/Debug/netstandard2.0` . V takovém případě nastavte předponu jako `--script-root MyProject/bin/Debug/netstandard2.0` . Toto je kořen aplikace Function App při spuštění v Azure. |
| **`--timeout`**, **`-t`** | Časový limit pro spuštění funkce Host v sekundách. Výchozí hodnota: 20 sekund.|
| **`--useHttps`** | Vytvořte vazby na `https://localhost:{port}` místo pro `http://localhost:{port}` . Ve výchozím nastavení tato možnost vytvoří důvěryhodný certifikát na vašem počítači.|

Když se hostitel funkce spustí, vypíše adresu URL funkcí aktivovaných protokolem HTTP:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Při místním spuštění není autorizace pro koncové body HTTP vynutila. To znamená, že všechny místní požadavky HTTP jsou zpracovávány jako `authLevel = "anonymous"` . Další informace najdete v [článku vázání http](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Předávání testovacích dat do funkce

Chcete-li místně testovat své funkce, [spusťte hostitele funkcí](#start) a koncové body volání na místním serveru pomocí požadavků HTTP. Koncový bod, který zavoláte, závisí na typu funkce.

>[!NOTE]
> V příkladech v tomto tématu se k posílání požadavků HTTP z terminálu nebo příkazového řádku používají nástroj pro otáčení. K odeslání požadavků HTTP na místní server můžete použít libovolný nástroj. Nástroj kudrlinkou je ve výchozím nastavení k dispozici v systémech Linux a Windows 10 Build 17063 a novějších. Ve starších oknech musíte nejdřív stáhnout a nainstalovat [Nástroj pro otáčení](https://curl.haxx.se/).

Obecnější informace o testovacích funkcích naleznete [v tématu strategie pro testování kódu v Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funkce aktivované protokolem HTTP a webhookem

Zavoláte následující koncový bod pro místně spouštěné funkce HTTP a Webhooku:

```
http://localhost:{port}/api/{function_name}
```

Ujistěte se, že používáte stejný název serveru a port, na kterém hostitel funkce naslouchá. Toto se zobrazí ve výstupu vygenerovaném při spouštění hostitele funkce. Tuto adresu URL můžete zavolat pomocí jakékoli metody HTTP podporované triggerem.

Následující příkaz oblé spustí `MyHttpTrigger` funkci rychlé zprovoznění z požadavku GET s parametrem _Name_ předaným v řetězci dotazu.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

V následujícím příkladu je stejná funkce, která se volá z předávaného _názvu_ žádosti post v textu žádosti:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Můžete vytvářet požadavky GET z prohlížeče, které přecházejí data v řetězci dotazu. Pro všechny ostatní metody HTTP je nutné použít nástroj Fiddler, post nebo podobný nástroj pro testování HTTP.

#### <a name="non-http-triggered-functions"></a>Funkce, které nejsou aktivované protokolem HTTP

Pro všechny druhy funkcí kromě triggerů HTTP a webhooků a aktivačních událostí pro Event Grid můžete místně testovat své funkce voláním koncového bodu správy. Volání tohoto koncového bodu s požadavkem HTTP POST na místním serveru aktivuje funkci. 

Chcete-li otestovat Event Grid aktivované funkce místně, přečtěte si téma [místní testování pomocí webové aplikace v prohlížeči](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Volitelně můžete předat testovací data do provádění v těle žádosti POST. Tato funkce je podobná kartě **test** v Azure Portal.

Zavoláte následující koncový bod správce, který aktivuje funkce jiného typu než HTTP:

```
http://localhost:{port}/admin/functions/{function_name}
```

Chcete-li předat testovací data do koncového bodu správce funkce, je nutné zadat data v těle zprávy s požadavkem POST. Tělo zprávy musí mít následující formát JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>`Hodnota obsahuje data ve formátu očekávaném funkcí. Následující oblý příklad je příspěvek na `QueueTriggerJS` funkci. V tomto případě je vstupem řetězec, který je ekvivalentní zprávě, která se má ve frontě najít.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Použití `func run` příkazu (jenom verze 1. x)

>[!IMPORTANT]
> `func run`Příkaz se podporuje jenom ve verzi 1. x těchto nástrojů. Další informace naleznete v tématu [jak cílit na Azure Functions verze modulu runtime](set-runtime-version.md).

Ve verzi 1. x můžete také vyvolat funkci přímo pomocí `func run <FunctionName>` a zadat vstupní data pro funkci. Tento příkaz je podobný spuštění funkce pomocí karty **test** v Azure Portal.

`func run` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Vložený obsah. |
| **`--debug`**, **`-d`** | Před spuštěním funkce připojte k hostitelskému procesu ladicí program.|
| **`--timeout`**, **`-t`** | Doba, po kterou se má čekat (v sekundách), dokud nebude hostitel místní funkce připravený.|
| **`--file`**, **`-f`** | Název souboru, který se má použít jako obsah|
| **`--no-interactive`** | Nezobrazuje výzvu k zadání. Užitečné pro scénáře automatizace.|

Například pro volání funkce aktivované protokolem HTTP a předejte tělo obsahu spusťte následující příkaz:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publikování do Azure

Azure Functions Core Tools podporuje dva typy nasazení: nasazení souborů projektu funkce přímo do aplikace Function App prostřednictvím nástroje [zip Deploy](functions-deployment-technologies.md#zip-deploy) a [nasazení vlastního kontejneru Docker](functions-deployment-technologies.md#docker-container). Musíte mít už [vytvořenou aplikaci Function App v předplatném Azure](functions-cli-samples.md#create), do které budete kód nasazovat. Projekty, které vyžadují kompilaci, by měly být sestaveny tak, aby mohly být nasazeny binární soubory.

>[!IMPORTANT]
>Aby bylo možné publikovat do Azure ze základních nástrojů, je nutné mít místně nainstalované rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) .  

Složka projektu může obsahovat soubory a adresáře specifické pro konkrétní jazyk, které by neměly být publikovány. Vyloučené položky jsou uvedeny v souboru. funcignore v kořenové složce projektu.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Nasadit soubory projektu

Pokud chcete publikovat místní kód do aplikace Function App v Azure, použijte `publish` příkaz:

```
func azure functionapp publish <FunctionAppName>
```

>[!IMPORTANT]
> Java používá Maven k publikování místního projektu do Azure. K publikování do Azure použijte následující příkaz: `mvn azure-functions:deploy` . Prostředky Azure se vytvářejí při počátečním nasazení.

Tento příkaz se publikuje do existující aplikace Function App v Azure. Pokud se pokusíte publikovat do `<FunctionAppName>` , který ve vašem předplatném neexistuje, zobrazí se chyba. Informace o tom, jak vytvořit aplikaci funkcí z příkazového řádku nebo okna terminálu pomocí Azure CLI, najdete v tématu [vytvoření Function App pro provádění bez serveru](./scripts/functions-cli-create-serverless.md). Ve výchozím nastavení tento příkaz používá [vzdálené sestavení](functions-deployment-technologies.md#remote-build) a nasadí vaši aplikaci, aby [běžela z balíčku pro nasazení](run-functions-from-deployment-package.md). Pokud chcete tento doporučený režim nasazení zakázat, použijte `--nozip` možnost.

>[!IMPORTANT]
> Když vytvoříte aplikaci funkcí v Azure Portal, používá ve výchozím nastavení verzi 3. x modulu runtime funkce. Pokud chcete, aby aplikace Function App používala verzi 1. x modulu runtime, postupujte podle pokynů v části [Spustit ve verzi 1. x](functions-versions.md#creating-1x-apps).
> Nemůžete změnit verzi modulu runtime pro aplikaci Function App, která obsahuje existující funkce.

Následující možnosti publikování platí pro všechny verze:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Nastavení publikování v local.settings.jsv systému Azure. zobrazí se výzva k přepsání, pokud už nastavení existuje. Pokud používáte Emulátor úložiště Microsoft Azure, změňte nejprve nastavení aplikace na [skutečné připojení úložiště](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Potlačí výzvu k přepsání nastavení aplikace `--publish-local-settings -i` , když se použije.|

Následující možnosti publikování jsou podporovány pouze pro verzi 2. x a novější verze:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Pouze publikování nastavení a přeskočení obsahu. Výchozí hodnota je prompt. |
|**`--list-ignored-files`** | Zobrazí seznam souborů, které jsou během publikování ignorovány, které jsou založeny na souboru. funcignore. |
| **`--list-included-files`** | Zobrazí seznam souborů, které jsou publikovány, které jsou založeny na souboru. funcignore. |
| **`--nozip`** | Zapne výchozí `Run-From-Package` režim. |
| **`--build-native-deps`** | Při publikování aplikací funkcí Pythonu přeskočí vygenerování složky. kolaes. |
| **`--build`**, **`-b`** | Provede akci sestavení při nasazení do aplikace Functions pro Linux. Akceptuje: `remote` a `local` . |
| **`--additional-packages`** | Seznam balíčků, které se mají nainstalovat při vytváření nativních závislostí Například: `python3-dev libevent-dev`. |
| **`--force`** | Ignorovat ověření před publikováním v některých scénářích. |
| **`--csx`** | Publikujte projekt skriptu C# (. csx). |
| **`--no-build`** | Projekt není sestaven během publikování. Pro Python se `pip install` neprovede. |
| **`--dotnet-cli-params`** | Při publikování kompilovaných funkcí jazyka C# (. csproj) volá základní nástroje "dotnet Build--Output bin/Publish". Všechny předané parametry budou připojeny k příkazovému řádku. |

### <a name="deploy-custom-container"></a>Nasazení vlastního kontejneru

Azure Functions umožňuje nasadit projekt funkce ve [vlastním kontejneru Docker](functions-deployment-technologies.md#docker-container). Další informace najdete v tématu [Vytvoření funkce na platformě Linux s použitím vlastní image](functions-create-function-linux-custom-image.md). Vlastní kontejnery musí mít souboru Dockerfile. Pokud chcete vytvořit aplikaci s souboru Dockerfile, použijte možnost--souboru Dockerfile na `func init` .

```
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

Naučte se vyvíjet, testovat a publikovat Azure Functions pomocí Azure Functions Core Tools [modulu Microsoft učení](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools je [Open Source a hostovaný na GitHubu](https://github.com/azure/azure-functions-cli).  
Pokud chcete zaslat žádost o chybu nebo funkci, [otevřete problém GitHubu](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[sady rozšíření]: functions-bindings-register.md#extension-bundles