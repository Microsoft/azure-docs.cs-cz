---
title: Práce s Azure Functions základní nástroje | Microsoft Docs
description: Zjistěte, jak kód a testovat Azure functions z příkazového řádku nebo Terminálové v místním počítači před spuštěním na Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 5c582b080ec6f2cff801758fc4bff4f7d07fd7df
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083065"
---
# <a name="work-with-azure-functions-core-tools"></a>Práce s Azure Functions základní nástroje

Nástroje Azure základní funkce umožňuje vývoj a testování funkcí v místním počítači z příkazového řádku nebo terminál. Lokální funkce se může připojit k Azure služby live a funkcí můžete ladit v místním počítači pomocí úplné funkce runtime. Funkce aplikace můžete nasadit i do vašeho předplatného Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Základní verze nástroje

Existují dvě verze nástroje základní funkce Azure. Verze, kterou použijete, závisí na vaší místní vývojové prostředí, volba jazyka a úroveň požadované podpory:

+ [Verze 1.x](#v1): podporuje verze 1.x modul runtime, který je obvykle dostupná (GA). Tato verze nástroje je podporován pouze na počítačích s Windows a je nainstalovat z [balíčku npm](https://docs.npmjs.com/getting-started/what-is-npm). S touto verzí můžete vytvořit v experimentální jazyky, které nejsou oficiálně podporované funkce. Další informace najdete v tématu [podporované jazyky v Azure Functions](supported-languages.md)

+ [Verze 2.x](#v2): podporuje verze 2.x modulu runtime. Tato verze podporuje [Windows](#windows-npm), [systému macOS](#brew), a [Linux](#linux). Používá správce balíčku specifické pro platformu nebo npm pro instalaci. Jako modul runtime 2.x je tato verze nástroje základní aktuálně ve verzi preview.

Pokud není uvedeno jinak, příklady v tomto článku jsou verze 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

[Azure Functions základní nástroje] obsahuje verzi stejné runtime, která pohání modulu runtime Azure Functions, který můžete spustit na místním vývojovém počítači. Také poskytuje příkazy pro vytvoření funkce, připojení k Azure a nasazení funkce projekty.

### <a name="v1"></a>Verze 1.x

Původní verzi nástroje používá modul runtime 1.x funkce. Tato verze rozhraní .NET Framework (4.7.1) používá a je podporován pouze v počítačích se systémem Windows. Před instalací nástroje verze 1.x musíte [nainstalovat NodeJS](https://docs.npmjs.com/getting-started/installing-node), což zahrnuje npm.

Použijte následující příkaz k instalaci nástroje 1.x verze:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Verze 2.x

>[!NOTE]
> Azure Functions runtime 2.0 je ve verzi preview a aktuálně ne všechny funkce Azure Functions podporované. Další informace najdete v tématu [verze Azure Functions](functions-versions.md) 

Verze 2.x nástroje používá modulu runtime Azure Functions 2.x, která je založená na .NET Core. Tato verze není podporována na všech platformách podporuje 2.x .NET Core, včetně [Windows](#windows-npm), [systému macOS](#brew), a [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Následující kroky pomocí npm nainstalujte základní nástroje v systému Windows. Můžete také použít [Chocolatey](https://chocolatey.org/). Další informace najdete v tématu [jádra nástroje readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Nainstalujte [.NET Core 2.0 pro Windows](https://www.microsoft.com/net/download/windows).

2. Nainstalujte [Node.js], což zahrnuje npm. Verze 2.x nástroje, jenom na Node.js 8.5 a novější verze podporují.

3. Nainstalujte sadu nástrojů jádra:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>Systému MacOS s Homebrew

Následující kroky použijte k instalaci nástroje jádra systému macOS Homebrew.

1. Nainstalujte [rozhraní .NET 2.0 jádra pro systému macOS](https://www.microsoft.com/net/download/macos).

2. Nainstalujte [Homebrew](https://brew.sh/), pokud je ještě není nainstalován.

3. Nainstalujte sadu nástrojů jádra:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) s byt č

Následující postup použijte [byt č](https://wiki.debian.org/Apt) při instalaci nástrojů základní distribuční Ubuntu/Debian Linux. Další Linuxových distribucích najdete v článku [jádra nástroje readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Nainstalujte [.NET Core 2.0 pro Linux](https://www.microsoft.com/net/download/linux).

2. Zaregistrujte kód product key společnosti Microsoft jako důvěryhodný:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Ověřte, že jeden z příslušné verze Ubuntu server je spuštěna v následující tabulce. Chcete-li přidat výstižný zdroje, spusťte:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Distribuce systému Linux | Verze |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu č. 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mátová 18    | `xenial`  |

4. Nainstalujte sadu nástrojů jádra:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

Adresář projektu funkce obsahuje soubory [host.json](functions-host-json.md) a [local.settings.json](#local-settings-file), podél podsložkám, které obsahují kódu pro jednotlivé funkce. Tento adresář je ekvivalentem funkce aplikace v Azure. Další informace o struktuře složky funkce, najdete v článku [Příručka pro vývojáře Azure Functions](functions-reference.md#folder-structure).

Verze 2.x vyžaduje, abyste při inicializaci a použijte výchozí jazyk šablony přidat všechny funkce, vyberte výchozí jazyk pro váš projekt. Ve verzi 1.x, zadáte jazyk pokaždé, když vytvoříte funkci.

V okně terminálu nebo z příkazového řádku spusťte následující příkaz pro vytvoření projektu a místní úložiště Git:

```bash
func init MyFunctionProj
```

Ve verzi 2.x, když spustíte příkaz musíte zvolit modul runtime pro váš projekt. Pokud máte v plánu k vývoji funkce jazyka JavaScript, zvolte **uzlu**:

```output
Select a worker runtime:
dotnet
node
```

Nahoru/dolů klávesy se šipkami vybrat jazyk, stiskněte klávesu Enter. Výstup bude vypadat jako v následujícím příkladu pro projekt jazyka JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Chcete-li vytvořit projekt bez místní úložiště Git, použijte `--no-source-control [-n]` možnost.

## <a name="register-extensions"></a>Registrace rozšíření

Ve verzi 2.x modulu runtime Azure Functions, budete muset explicitně zaregistrovat rozšíření vazby (typy vazeb), které používáte ve vaší aplikaci funkce.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Další informace najdete v tématu [Azure Functions triggerů a vazeb koncepty](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Nastavení místního souboru

Soubor local.settings.json ukládá nastavení aplikace, řetězce připojení a nastavení nástroje základní funkce Azure. Má následující strukturu:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

| Nastavení      | Popis                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Pokud nastavíte hodnotu **true**, všechny hodnoty jsou šifrované pomocí klíče místního počítače. Použít s `func settings` příkazy. Výchozí hodnota je **false**. |
| **Hodnoty** | Kolekce nastavení aplikace a připojovacích řetězců použít při místním spuštění. Tyto hodnoty odpovídají nastavení aplikace v aplikaci funkce v Azure, jako například **AzureWebJobsStorage** a **AzureWebJobsDashboard**. Mnoho triggerů a vazeb mají vlastnost, která odkazuje na nastavení aplikace připojovací řetězec, například **připojení** pro [aktivační události objektu Blob úložiště](functions-bindings-storage-blob.md#trigger---configuration). Pro tyto vlastnosti je třeba definované v nastavení aplikace **hodnoty** pole. <br/>**AzureWebJobsStorage** je nastavení požadovaná aplikace pro aktivační procedury než HTTP. Pokud máte [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) nainstalovány místně, můžete nastavit **AzureWebJobsStorage** k `UseDevelopmentStorage=true` a základní nástroje používá emulátor. To je užitečné při vývoji, ale měli byste otestovat s připojením k skutečné úložiště před nasazením. |
| **Hostitel** | Nastavení v této části přizpůsobit funkce hostitelský proces, při místním spuštění. |
| **LocalHttpPort** | Nastaví výchozí port použitý při spuštění místního hostitele funkce (`func host start` a `func run`). `--port` Možnost příkazového řádku má přednost před tuto hodnotu. |
| **CORS** | Definuje zdroje povolené pro [(CORS) pro sdílení prostředků různého původu](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje se zadávají jako seznam oddělený čárkami bez mezer. Hodnota zástupného znaku (\*) je podporováno, což umožňuje požadavky od jakýkoli původ. |
| **ConnectionStrings** | Nepoužívejte tuto kolekci pro připojovací řetězce, které používá vaše vazby funkcí. Tato kolekce je používán pouze architektury, které musíte získat připojovací řetězce z **ConnectionStrings** část konfigurace souborů, jako například [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Připojovací řetězce v tento objekt se přidají do prostředí s typem zprostředkovatele [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Položky v této kolekci nejsou vydané ve službě Azure s jiným nastavením aplikace. Musíte explicitně přidat tyto hodnoty **připojovací řetězce** části **nastavení aplikace** pro vaši aplikaci funkce. |

Hodnoty nastavení funkce aplikace lze také přečíst v kódu jako proměnné prostředí. Další informace najdete v části proměnných prostředí z těchto témat pro specifický jazyk odkaz:

+ [Předkompilované C#](functions-dotnet-class-library.md#environment-variables)
+ [C# skript (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Nastavení v souboru local.settings.json používají funkce nástroje jenom, při místním spuštění. Ve výchozím nastavení tato nastavení se nemigrují automaticky při publikování projektu do Azure. Použití `--publish-local-settings` přepínač [při publikování](#publish) a ujistěte se, tato nastavení jsou přidány do aplikaci funkce v Azure. Hodnoty v **ConnectionStrings** nikdy jsou publikovány.

Když je nastavená žádný platný úložiště připojovací řetězec pro **AzureWebJobsStorage** a není používán emulátoru, se zobrazí následující chybová zpráva:  

>Chybí hodnota pro AzureWebJobsStorage v local.settings.json. To je potřeba pro všechny aktivační události než HTTP. Můžete spustit ' func azure functionapp načítání app nastavení <functionAppName>, nebo zadejte připojovací řetězec v local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Získat připojovací řetězce úložiště

I když pro vývoj pomocí emulátoru úložiště, můžete otestovat s připojením k skutečné úložiště. Za předpokladu, že už máte [vytvořili účet úložiště](../storage/common/storage-create-storage-account.md), můžete získat připojovací řetězec platné úložiště v jednom z následujících způsobů:

+ Z [Azure Portal]. Přejděte na svůj účet úložiště, vyberte **přístupové klíče** v **nastavení**, poté zkopírovat jednu **připojovací řetězec** hodnoty.

  ![Zkopírujte připojovací řetězec z portálu Azure](./media/functions-run-local/copy-storage-connection-portal.png)

+ Použití [Azure Storage Explorer](http://storageexplorer.com/) pro připojení k účtu Azure. V **Explorer**, rozbalte vašeho předplatného, vyberte svůj účet úložiště a zkopírujte primární nebo sekundární připojovací řetězec. 

  ![Zkopírujte připojovací řetězec z Průzkumníka úložiště](./media/functions-run-local/storage-explorer.png)

+ Použijte základní nástroje ke stažení připojovací řetězec z Azure s jedním z následujících příkazů:

    + Stáhněte si všechna nastavení z existující aplikaci funkce:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Získáte připojovací řetězec pro účet konkrétní úložiště:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Pokud už nejste přihlášení do Azure, budete vyzváni k tomu.

## <a name="create-func"></a>Vytvoření funkce

Pokud chcete vytvořit funkci, spusťte následující příkaz:

```bash
func new
```

Ve verzi 2.x, když spustíte `func new` budete vyzváni k výběru šablony ve výchozím jazyce funkce aplikace a potom zobrazí výzva k zvolte název funkce. Ve verzi 1.x, zobrazí výzva k volbě jazyka.

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

Funkce kód je vygenerován v podsložce s názvem zadané funkce, jak můžete vidět v následující aktivační událost výstupní fronty:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Tyto možnosti můžete zadat také v příkaz s těmito argumenty:

| Argument     | Popis                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| Šablona programovací jazyk, například C#, F # nebo JavaScript. Tato možnost je vyžadována ve verzi 1.x. Ve verzi 2.x, tuto možnost použijte, nebo vyberte výchozí jazyk projektu. |
| **`--template -t`** | Název šablony, které může být jedna z hodnot:<br/><ul><li>`Blob trigger`</li><li>`Cosmos DB trigger`</li><li>`Event Grid trigger`</li><li>`HTTP trigger`</li><li>`Queue trigger`</li><li>`SendGrid`</li><li>`Service Bus Queue trigger`</li><li>`Service Bus Topic trigger`</li><li>`Timer trigger`</li></ul> |
| **`--name -n`** | Název funkce. |

Například pokud chcete vytvořit aktivační událost JavaScript HTTP v příkazu, spusťte tento příkaz:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Chcete-li vytvořit funkci aktivovaného fronty v příkazu, spusťte:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Místní spuštění funkce

Pokud chcete spustit funkce projektu, spusťte hostiteli funkce. Hostitel umožňuje aktivačních událostí pro všechny funkce v projektu:

```bash
func host start
```

`func host start` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Místní port pro naslouchání. Výchozí hodnota: 7071. |
| **`--debug <type>`** | Spustí hostitele s portem ladění otevřete tak, aby se můžete připojit k **func.exe** procesů z [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) nebo [Visual Studio 2017](functions-dotnet-class-library.md). *\<Typ\>* možnosti jsou `VSCode` a `VS`.  |
| **`--cors`** | Seznam původů CORS, bez mezer oddělených čárkami. |
| **`--nodeDebugPort -n`** | Port pro uzel ladicí program používat. Výchozí hodnota: Hodnota z launch.json nebo 5858. |
| **`--debugLevel -d`** | Úroveň trasování konzoly (vypnuto, podrobné nastavení, informace, varování nebo chyba). Výchozí: informace.|
| **`--timeout -t`** | Časový limit pro hostitele funkce spustit v sekundách. Výchozí hodnota: 20 sekund.|
| **`--useHttps`** | Vytvořit vazbu na `https://localhost:{port}` spíše než do `http://localhost:{port}`. Ve výchozím nastavení tato možnost vytvoří důvěryhodný certifikát v počítači.|
| **`--pause-on-error`** | Pozastavení další vstupní před ukončení procesu. Použít při spuštění z Visual Studio nebo VS Code nástrojů jádra.|

Při spuštění funkce hostitele výstupy funkce aktivované protokolem URL HTTP:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>Předávání testů dat na funkci

K testování funkcí místně, můžete [spuštění funkce hostitele](#start) a volání koncové body na místním serveru pomocí protokolu HTTP žádosti. Koncový bod, které volat závisí na typu funkce.

>[!NOTE]  
> Příklady v tomto tématu použít nástroj cURL k odeslání požadavků HTTP z terminálu nebo z příkazového řádku. Nástroj podle svého výběru slouží k odesílání požadavků HTTP na místním serveru. Nástroj cURL je k dispozici ve výchozím nastavení počítače se systémem Linux. V systému Windows, musíte nejdřív stáhnout a nainstalovat [cURL nástroj](https://curl.haxx.se/).

Další obecné informace o testování funkcí najdete v tématu [strategie pro testování kódu v Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Protokol HTTP a webhooku aktivované funkce

Volání následující koncový bod místně spustit HTTP a webhooku se aktivuje funkce:

    http://localhost:{port}/api/{function_name}

Nezapomeňte použít stejný název serveru a port, který hostitel funkce naslouchá na. Toto zobrazí ve výstupu vygenerované při spouštění hostitel funkce. Obraťte se na tuto adresu URL pomocí jakékoli metody HTTP nepodporuje aktivační událost. 

Následující cURL příkaz aktivační události `MyHttpTrigger` funkce Rychlé spuštění z požadavek GET s _název_ předán parametr v řetězci dotazu. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Následující příklad je stejný funkce volat z požadavek POST předávání _název_ v těle žádosti:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Můžete nastavit získat požadavky z prohlížeče předávání dat v řetězci dotazu. Pro všechny ostatní metody HTTP musíte použít cURL aplikaci Fiddler, Postman nebo podobné nástroj testování HTTP.  

#### <a name="non-http-triggered-functions"></a>Jiným protokolem než HTTP aktivované funkce

Pro všechny typy funkcí než aktivace protokolu HTTP a pomocí webhooků můžete otestovat funkcí místně voláním koncového bodu správy. Volání metody tento koncový bod s požadavek HTTP POST na místním serveru aktivuje funkce. Volitelně můžete předat data testovací spuštění v textu požadavku POST. Tato funkce je podobná **Test** na portálu Azure.  

Zavoláte následující koncový bod správce k aktivaci funkce jiným protokolem než HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Chcete-li předat test data ke koncovému bodu správce funkce, je nutné zadat data v těle zprávu požadavku POST. Text zprávy je potřeba mít následující formát JSON:

```JSON
{
    "input": "<trigger_input>"
}
````

`<trigger_input>` Hodnota obsahuje data ve formátu očekávanou funkce. Následující příklad cURL je metodu POST SMĚŘUJÍCÍ `QueueTriggerJS` funkce. V takovém případě je vstupní řetězec, který je ekvivalentem zprávy očekává ve frontě.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Pomocí `func run` příkazu ve verzi 1.x

>[!IMPORTANT]  
> `func run` Příkaz není podporován v verze 2.x nástroje. Další informace naleznete v tématu [jak mít verze modulu runtime Azure Functions](set-runtime-version.md).

Můžete také vyvolat funkci přímo pomocí `func run <FunctionName>` a zadejte vstupní data pro funkce. Tento příkaz je podobná spuštění funkce pomocí **Test** na portálu Azure. 

`func run` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Vložený obsah. |
| **`--debug -d`** | Připojte ladicí program k hostitelskému procesu před spuštěním funkce.|
| **`--timeout -t`** | Doba čekání (v sekundách), dokud místního hostitele funkce není připravena.|
| **`--file -f`** | Název souboru, který chcete použít jako obsah.|
| **`--no-interactive`** | Nezobrazí výzvu pro vstup. Tato možnost je užitečná pro scénáře automatizace.|

Například volání funkce aktivované protokolem HTTP a předat obsahu, spusťte následující příkaz:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Zobrazení protokolu soubory místně

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publikování v Azure

K publikování funkce projektu do aplikace pro funkce v Azure, použijte `publish` příkaz:

```bash
func azure functionapp publish <FunctionAppName>
```

Můžete použít následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Nastavení publikování v local.settings.json do Azure, výzvy přepsat, pokud nastavení již existuje. Pokud používáte emulátor úložiště, můžete změnit nastavení aplikace nastavte na [připojení skutečné úložiště](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Musí být použit s `-i`. Pokud se liší, přepíše místní hodnotou AppSettings v Azure. Výchozí hodnota je výzva.|

Tento příkaz publikuje do stávající funkce aplikace v Azure. Dojde k chybě při `<FunctionAppName>` neexistuje ve vašem předplatném. Další postupy k vytvoření aplikace pro funkce z příkazového řádku nebo okno terminálu pomocí rozhraní příkazového řádku Azure najdete v tématu [vytvoření funkce aplikace bez serveru provedení](./scripts/functions-cli-create-serverless.md).

`publish` Příkaz odešle obsah adresáře projektu funkce. Pokud odstraníte soubory místně, `publish` příkaz nedojde k jejich odstranění z Azure. Můžete odstranit soubory v Azure pomocí [Kudu nástroj](functions-how-to-use-azure-function-app-settings.md#kudu) v [Azure Portal].  

>[!IMPORTANT]  
> Když vytvoříte aplikaci funkce v Azure, používá verzi 1.x modulu runtime funkce ve výchozím nastavení. Chcete-li funkce aplikace použitelná verze 2.x modulu runtime přidat nastavení aplikace `FUNCTIONS_EXTENSION_VERSION=beta`.  
Použijte následující kód rozhraní příkazového řádku Azure pro přidání do aplikaci funkce toto nastavení:

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Další postup

Nástroje Azure základní funkce je [otevřít zdroj a hostované na Githubu](https://github.com/azure/azure-functions-cli).  
Do souboru žádost chyb nebo funkce [otevřete potíže Githubu](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions základní nástroje]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
