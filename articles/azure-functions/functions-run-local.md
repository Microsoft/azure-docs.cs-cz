---
title: "Vývoj a spustit místně na Azure functions | Microsoft Docs"
description: "Zjistěte, jak kód a testovat Azure functions v místním počítači před spuštěním na Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 59a15697641dd8e4bdfdb974436d46a34b47ffb5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Kód a testovat místně na Azure Functions

Když [portál Azure] poskytuje úplnou sadu nástrojů pro vývoj a testování Azure Functions se celá řada vývojářů raději místní vývojové prostředí. Azure Functions umožňuje snadno použít editor Oblíbené kódu a místní vývojové nástroje pro vývoj a testování funkcí v místním počítači. Funkce můžete aktivovat pro události v Azure a při ladění vašich C# a funkce jazyka JavaScript v místním počítači. 

Pokud jste Azure Functions Visual Studio C# vývojář, také [se integruje s Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> Nemíchat místní vývoj pomocí portálu vývoj v aplikaci stejné funkce. Při vytváření a publikování funkce z místní projektu, by neměl pokusíte udržovat nebo upravit kód projektu na portálu.

## <a name="install-the-azure-functions-core-tools"></a>Instalace nástrojů Azure Functions Core

[Nástroje Azure základní funkce] je místní verzi modulu runtime Azure Functions, který můžete spustit na místním vývojovém počítači. Není emulátor ani simulátor. Je stejný modul runtime, který zajišťuje funkce v Azure. Existují dvě verze nástrojů pro základní funkce Azure, jeden pro verze 1.x modulu runtime a jeden pro verze 2.x. Obě verze jsou uvedeny jako [balíčku npm](https://docs.npmjs.com/getting-started/what-is-npm).

>[!NOTE]  
> Než nainstalujete jednu z verzí, je nutné [nainstalovat NodeJS](https://docs.npmjs.com/getting-started/installing-node), což zahrnuje npm. Verze 2.x nástroje, jenom na Node.js 8.5 a novější verze podporují. 

### <a name="version-2x-runtime"></a>Verze 2.x runtime

Verze 2.x nástroje používá modulu runtime Azure Functions 2.x, která je založená na .NET Core. Tato verze je podporována na všech platformách .NET Core 2.x podporuje. Tuto verzi použít pro vývoj pro různé platformy a kdy Functions runtime 2.x je vyžadován. 

>[!IMPORTANT]   
> Před instalací nástroje základní funkce Azure, [nainstalujte .NET Core 2.0](https://www.microsoft.com/net/core).  
>
> Azure Functions runtime 2.0 je ve verzi preview a aktuálně ne všechny funkce Azure Functions podporované. Další informace najdete v tématu [Azure Functions za běhu 2.0 známé problémy](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) 

 Použijte následující příkaz k instalaci nástroje verze 2.0:

```bash
npm install -g azure-functions-core-tools@core
```

Při instalaci na použití Ubuntu `sudo`, a to takto:

```bash
sudo npm install -g azure-functions-core-tools@core
```

Při instalaci v systému macOS a Linux, musíte zahrnout `unsafe-perm` příznak následujícím způsobem:

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

### <a name="version-1x-runtime"></a>Verze runtime 1.x

Původní verzi nástroje používá modul runtime 1.x funkce. Tato verze používá rozhraní .NET Framework a je podporován pouze na počítačích s Windows. Použijte následující příkaz k instalaci nástroje 1.x verze:

```bash
npm install -g azure-functions-core-tools
```

## <a name="run-azure-functions-core-tools"></a>Spuštění nástrojů Azure Functions jádra
 
Nástroje Azure základní funkce přidá následující aliasy příkazů:
* **Func**
* **azfun**
* **azurefunctions**

Některé z těchto aliasy slouží kde `func` je zobrazeno v příkladech.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Vytvoření projektu místní funkce

Při místním spuštění projektu funkce je adresář, který má soubory [host.json](functions-host-json.md) a [local.settings.json](#local-settings-file). Tento adresář je ekvivalentem funkce aplikace v Azure. Další informace o struktuře složky Azure Functions najdete v tématu [Příručka pro vývojáře Azure Functions](functions-reference.md#folder-structure).

V okně terminálu nebo z příkazového řádku spusťte následující příkaz pro vytvoření projektu a místní úložiště Git:

```
func init MyFunctionProj
```

Výstup bude vypadat jako v následujícím příkladu:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Chcete-li vytvořit projekt bez místní úložiště Git, použijte `--no-source-control [-n]` možnost.

## <a name="register-extensions"></a>Registrace rozšíření

Ve verzi 2.x modulu runtime Azure Functions, je nutné explicitně zaregistrovat [vazby rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) používaných ve vaší aplikaci funkce. 

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

Další informace najdete v tématu [Azure Functions triggerů a vazeb koncepty](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Nastavení místního souboru

Soubor local.settings.json ukládá nastavení aplikace, řetězce připojení a nastavení nástroje základní funkce Azure. Má následující strukturu:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
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
| **isEncrypted** | Pokud nastavíte hodnotu **true**, všechny hodnoty jsou šifrované pomocí klíče místního počítače. Použít s `func settings` příkazy. Výchozí hodnota je **false**. |
| **Hodnoty** | Kolekce nastavení aplikace používá při místním spuštění. **AzureWebJobsStorage** a **AzureWebJobsDashboard** jsou příklady; úplný seznam najdete v tématu [referenční příručka k nastavení aplikace](functions-app-settings.md).  |
| **Hostitel** | Nastavení v této části přizpůsobit funkce hostitelský proces, při místním spuštění. | 
| **LocalHttpPort** | Nastaví výchozí port použitý při spuštění místního hostitele funkce (`func host start` a `func run`). `--port` Možnost příkazového řádku má přednost před tuto hodnotu. |
| **CORS** | Definuje zdroje povolené pro [(CORS) pro sdílení prostředků různého původu](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Zdroje se zadávají jako seznam oddělený čárkami bez mezer. Hodnota zástupného znaku (\*) je podporováno, což umožňuje požadavky od jakýkoli původ. |
| **ConnectionStrings** | Obsahuje databázové připojovací řetězce pro funkcí. Připojovací řetězce v tento objekt se přidají do prostředí s typem zprostředkovatele **System.Data.SqlClient**.  | 

Většina triggerů a vazeb mít **připojení** vlastnost, která se mapuje na název nastavení aplikace nebo proměnné prostředí. Pro každou vlastnost připojení musí být definována v souboru local.settings.json nastavení aplikace. 

Tato nastavení lze také přečíst v kódu jako proměnné prostředí. V jazyce C#, použijte [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) nebo [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). V jazyce JavaScript, použijte `process.env`. Bylo nastaveno jako systémové proměnné prostředí mají přednost před hodnot v souboru local.settings.json. 

Nastavení v souboru local.settings.json používají funkce nástroje jenom, při místním spuštění. Ve výchozím nastavení tato nastavení se nemigrují automaticky při publikování projektu do Azure. Použití `--publish-local-settings` přepínač [při publikování](#publish) a ujistěte se, tato nastavení jsou přidány do aplikaci funkce v Azure.

Když je nastavená žádný platný úložiště připojovací řetězec pro **AzureWebJobsStorage**, se zobrazí následující chybová zpráva:  

>Chybí hodnota pro AzureWebJobsStorage v local.settings.json. To je potřeba pro všechny aktivační události než HTTP. Můžete spustit ' func azure functionapp načítání app nastavení <functionAppName>, nebo zadejte připojovací řetězec v local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Konfigurace nastavení aplikace

Pokud chcete nastavit hodnotu pro připojovací řetězce, můžete provést jednu z následujících možností:
* Zadejte připojovací řetězec z [Azure Storage Explorer](http://storageexplorer.com/).
* Použijte jednu z následujících příkazů:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Oba příkazy vyžadovat, abyste první přihlášení do Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Vytvoření funkce

Pokud chcete vytvořit funkci, spusťte následující příkaz:

```
func new
``` 
`func new` podporuje následující volitelné argumenty:

| Argument     | Popis                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Šablona programovací jazyk, například C#, F # nebo JavaScript. |
| **`--template -t`** | Název šablony. |
| **`--name -n`** | Název funkce. |

Například pokud chcete vytvořit aktivační událost jazyka JavaScript HTTP, spusťte tento příkaz:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Chcete-li vytvořit funkci aktivovaného fronty, spusťte:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Místní spuštění funkce

Pokud chcete spustit funkce projektu, spusťte hostiteli funkce. Hostitel umožňuje aktivačních událostí pro všechny funkce v projektu:

```
func host start
```

`func host start` podporuje následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Místní port pro naslouchání. Výchozí hodnota: 7071. |
| **`--debug <type>`** | Možnosti jsou `VSCode` a `VS`. |
| **`--cors`** | Seznam původů CORS, bez mezer oddělených čárkami. |
| **`--nodeDebugPort -n`** | Port pro uzel ladicí program používat. Výchozí hodnota: Hodnota z launch.json nebo 5858. |
| **`--debugLevel -d`** | Úroveň trasování konzoly (vypnuto, podrobné nastavení, informace, varování nebo chyba). Výchozí: informace.|
| **`--timeout -t`** | Časový limit pro hostitele funkce spustit v sekundách. Výchozí hodnota: 20 sekund.|
| **`--useHttps`** | Vytvořit vazbu na https://localhost: {port} místo na http://localhost: {port}. Ve výchozím nastavení tato možnost vytvoří důvěryhodný certifikát v počítači.|
| **`--pause-on-error`** | Pozastavení další vstupní před ukončení procesu. Užitečné při spuštění nástroje základní funkce Azure z integrované vývojové prostředí (IDE).|

Při spuštění funkce hostitele výstupy funkce aktivované protokolem URL HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Ladění ve VS Code nebo Visual Studio

Chcete-li připojit ladicí program, předat `--debug` argument. Chcete-li ladit funkce jazyka JavaScript, použijte Visual Studio Code. Pro C# funkce použijte sadu Visual Studio.

Chcete-li ladit funkce jazyka C#, použijte `--debug vs`. Můžete také použít [Azure funkce 2017 nástroje sady Visual Studio](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Spusťte hostiteli a nastavte ladění jazyka JavaScript, spusťte příkaz:

```
func host start --debug vscode
```

> [!IMPORTANT]
> Pro ladění, pouze Node.js 8.x je podporována. Node.js 9.x není podporován. 

Potom v sadě Visual Studio Code v **ladění** zobrazit, vyberte možnost **připojit k Azure Functions**. Můžete připojit zarážky, zkontrolujte proměnné a krok prostřednictvím kódu.

![Ladění jazyka JavaScript s kódem jazyka Visual Studio](./media/functions-run-local/vscode-javascript-debugging.png)

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

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Následující příklad je stejný funkce volat z požadavek POST předávání _název_ v těle žádosti:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Všimněte si, že lze provádět získat požadavky z prohlížeče předávání dat v řetězci dotazu. Pro všechny ostatní metody HTTP musíte použít cURL aplikaci Fiddler, Postman nebo podobné nástroj testování HTTP.  

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

```
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

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Zobrazení protokolu soubory místně

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publikování v Azure

K publikování funkce projektu do aplikace pro funkce v Azure, použijte `publish` příkaz:

```
func azure functionapp publish <FunctionAppName>
```

Můžete použít následující možnosti:

| Možnost     | Popis                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Nastavení publikování v local.settings.json do Azure, výzvy přepsat, pokud nastavení již existuje.|
| **`--overwrite-settings -y`** | Musí být použit s `-i`. Pokud se liší, přepíše místní hodnotou AppSettings v Azure. Výchozí hodnota je výzva.|

Tento příkaz publikuje do stávající funkce aplikace v Azure. Dojde k chybě při `<FunctionAppName>` neexistuje ve vašem předplatném. Další postupy k vytvoření aplikace pro funkce z příkazového řádku nebo okno terminálu pomocí rozhraní příkazového řádku Azure najdete v tématu [vytvoření funkce aplikace bez serveru provedení](./scripts/functions-cli-create-serverless.md).

`publish` Příkaz odešle obsah adresáře projektu funkce. Pokud odstraníte soubory místně, `publish` příkaz nedojde k jejich odstranění z Azure. Můžete odstranit soubory v Azure pomocí [Kudu nástroj](functions-how-to-use-azure-function-app-settings.md#kudu) v [portál Azure].  

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

[Nástroje Azure základní funkce]: https://www.npmjs.com/package/azure-functions-core-tools
[portál Azure]: https://portal.azure.com 
