---
title: Testování Azure Functions
description: Vytváření automatizovaných testů pro funkci jazyka C# v aplikaci Visual Studio a funkce JavaScriptu v VS Code
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 2488a476fe40c2bf1f3e290b462babceff30a9b0
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601386"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie testování kódu ve službě Azure Functions

Tento článek ukazuje, jak vytvořit automatizované testy pro Azure Functions.

Testování veškerého kódu je doporučeno, ale můžete dosáhnout nejlepších výsledků zabalením logiky funkce a vytvořením testů mimo funkci. Vyabstrakce logiky omezuje řádky kódu funkce a umožňuje, aby byla funkce výhradně zodpovědná za volání jiných tříd nebo modulů. Tento článek však ukazuje, jak vytvořit automatizované testy proti funkcím aktivovaným protokolem HTTP a časovačem.

Následující obsah je rozdělen do dvou různých sekcí určených pro cílení na různé jazyky a prostředí. Můžete se naučit sestavit testy v nástroji:

- [C# v aplikaci Visual Studio s xUnit](#c-in-visual-studio)
- [JavaScript v VS Code s jest](#javascript-in-vs-code)

Ukázkové úložiště je k dispozici na [GitHubu](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# v sadě Visual Studio

Následující příklad popisuje, jak vytvořit aplikaci funkcí jazyka C# v aplikaci Visual Studio a spustit a testy pomocí [xUnit](https://github.com/xunit/xunit).

![Testování Azure Functions pomocí jazyka C# v aplikaci Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Nastavení

Pokud chcete nastavit prostředí, vytvořte funkci a otestujte aplikaci. Následující kroky vám pomůžou vytvořit aplikace a funkce, které jsou potřeba pro podporu testů:

1. [Vytvoření nové aplikace Functions](./functions-create-first-azure-function.md) a pojmenování IT **funkcí**
2. [Vytvořte funkci http ze šablony](./functions-create-first-azure-function.md) a pojmenujte ji **MyHttpTrigger**.
3. [Vytvořte funkci časovače ze šablony](./functions-create-scheduled-function.md) a pojmenujte ji **MyTimerTrigger**.
4. [Vytvořte v řešení aplikaci XUnit test](https://xunit.net/docs/getting-started/netcore/cmdline) a pojmenujte ji **Functions. Tests**.
5. Pomocí NuGet přidejte odkaz z testovací aplikace do [Microsoft. AspNetCore. Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Odkázat na aplikaci *Functions*](/visualstudio/ide/managing-references-in-a-project?view=vs-2017) v aplikaci *Functions. Tests* .

### <a name="create-test-classes"></a>Vytváření testovacích tříd

Nyní, když jsou vytvořeny projekty, můžete vytvořit třídy používané ke spouštění automatizovaných testů.

Každá funkce převezme instanci [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) , která zpracovává protokolování zpráv. Některé testy buď neprotokolují zprávy, nebo nemají žádné obavy o implementaci protokolování. Jiné testy potřebují k vyhodnocení zpráv protokolovaných za účelem určení, zda test projde.

Vytvoříte novou třídu s názvem `ListLogger` , která obsahuje interní seznam zpráv k vyhodnocení během testování. Chcete-li implementovat požadované `ILogger` rozhraní, třída potřebuje obor. Následující třída napodobá oboru pro testovací případy, které se mají předat `ListLogger` třídě.

Vytvořte novou třídu v projektu *Functions. Tests* s názvem **NullScope.cs** a zadejte následující kód:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Dále vytvořte novou třídu v projektu *Functions. Tests* s názvem **ListLogger.cs** a zadejte následující kód:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

`ListLogger`Třída implementuje následující členy, které jsou vynásobené `ILogger` rozhraním:

- **BeginScope**: obory přidávají kontext do svého protokolování. V tomto případě test pouze odkazuje na statickou instanci `NullScope` třídy, aby test mohl fungovat.

- **Povoleno**: je k dispozici výchozí hodnota `false` .

- **Log**: Tato metoda používá `formatter` k formátování zprávy poskytnutou funkci a následně do kolekce přidá výsledný text `Logs` .

`Logs`Kolekce je instancí `List<string>` a je inicializována v konstruktoru.

Dále vytvořte nový soubor v projektu *Functions. Tests* s názvem **LoggerTypes.cs** a zadejte následující kód:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

Tento výčet Určuje typ protokolovacího nástroje používaného testy.

Nyní vytvořte novou třídu v projektu *Functions. Tests* s názvem **TestFactory.cs** a zadejte následující kód:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

`TestFactory`Třída implementuje následující členy:

- **Data**: Tato vlastnost vrací kolekci [IEnumerable](/dotnet/api/system.collections.ienumerable) vzorových dat. Páry klíč-hodnota představují hodnoty, které jsou předány do řetězce dotazu.

- **CreateDictionary –**: Tato metoda přijímá dvojici klíč/hodnota jako argumenty a vrátí novou hodnotu `Dictionary` použitou k vytvoření `QueryCollection` pro reprezentaci hodnot řetězce dotazu.

- **CreateHttpRequest**: Tato metoda vytvoří požadavek HTTP inicializovaný pomocí daných parametrů řetězce dotazu.

- **CreateLogger**: v závislosti na typu protokolovacího nástroje Tato metoda vrátí třídu protokolovacího nástroje použitou pro testování. `ListLogger`Uchovává záznam protokolovaných zpráv, které jsou k dispozici pro vyhodnocení v testech.

Nakonec vytvořte novou třídu v projektu *Functions. Tests* s názvem **FunctionsTests.cs** a zadejte následující kód:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

Členy implementované v této třídě jsou:

- **Http_trigger_should_return_known_string**: Tento test vytváří požadavek s hodnotami řetězce dotazu `name=Bill` na funkci http a kontroluje, zda je vrácena očekávaná odpověď.

- **Http_trigger_should_return_string_from_member_data**: Tento test používá atributy xUnit k poskytování ukázkových dat funkci http.

- **Timer_should_log_message**: Tento test vytvoří instanci třídy `ListLogger` a předá ji funkcím časovače. Po spuštění funkce se zkontroluje protokol, aby se zajistilo, že je k dispozici očekávaná zpráva.

Chcete-li získat přístup k nastavení aplikace v testech, můžete [Vložit](./functions-dotnet-dependency-injection.md) `IConfiguration` instanci s vydanými hodnotami proměnné prostředí do funkce.

### <a name="run-tests"></a>Spouštění testů

Chcete-li spustit testy, přejděte do **Průzkumníka testů** a klikněte na možnost **Spustit vše**.

![Testování Azure Functions pomocí jazyka C# v aplikaci Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Ladit testy

Chcete-li ladit testy, nastavte zarážku na test, přejděte do **Průzkumníka testů** a klikněte na tlačítko **Spustit > poslední spuštění ladění**.

## <a name="javascript-in-vs-code"></a>JavaScript v VS Code

Následující příklad popisuje, jak vytvořit aplikaci funkcí JavaScriptu v VS Code a spustit a testy pomocí [jest](https://jestjs.io). Tento postup používá [rozšíření vs Code Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) k vytvoření Azure Functions.

![Testování Azure Functions pomocí JavaScriptu v VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Nastavení

Pokud chcete nastavit prostředí, inicializujte novou aplikaci Node.js v prázdné složce spuštěním `npm init` .

```bash
npm init -y
```

Dále nainstalujte jest spuštěním následujícího příkazu:

```bash
npm i jest
```

Nyní aktualizujte _package.jsna_ a nahraďte stávající testovací příkaz následujícím příkazem:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Vytváření testovacích modulů

V případě inicializovaného projektu můžete vytvořit moduly používané pro spuštění automatizovaných testů. Začněte vytvořením nové složky s názvem *testování* , která bude obsahovat moduly podpory.

Ve složce *testování* přidejte nový soubor, pojmenujte ho **defaultContext.js** a přidejte následující kód:

```javascript
module.exports = {
    log: jest.fn()
};
```

Tento modul nakládá funkce *protokolu* tak, aby reprezentovala výchozí kontext spuštění.

Dále přidejte nový soubor, pojmenujte ho **defaultTimer.js** a přidejte následující kód:

```javascript
module.exports = {
    IsPastDue: false
};
```

Tento modul implementuje `IsPastDue` vlastnost jako nefalešnou instanci časovače. Konfigurace časovače, jako jsou výrazy NCRONTAB, zde nejsou požadovány, protože testovací svazek je pouhým voláním funkce přímo k otestování výsledku.

Dále pomocí rozšíření VS Code Functions [vytvořte novou funkci http jazyka JavaScript](/azure/developer/javascript/tutorial-vscode-serverless-node-01) a pojmenujte ji *HttpTrigger*. Po vytvoření funkce přidejte nový soubor do stejné složky s názvem **index.test.js** a přidejte následující kód:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

Funkce HTTP ze šablony vrací řetězec "Hello" zřetězený s názvem zadaným v řetězci dotazu. Tento test vytvoří falešnou instanci požadavku a předá ji funkci HTTP. Test ověří, zda je metoda *protokolu* volána jednou a vrácený text se rovná "Hello Bill".

Potom pomocí rozšíření VS Code Functions vytvořte novou funkci časovače JavaScriptu a pojmenujte ji *TimerTrigger*. Po vytvoření funkce přidejte nový soubor do stejné složky s názvem **index.test.js** a přidejte následující kód:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

Funkce časovače v šabloně protokoluje zprávu na konci těla funkce. Tento test zajišťuje, aby funkce *protokolu* byla volána jednou.

### <a name="run-tests"></a>Spouštění testů

Chcete-li spustit testy, otevřete okno příkazového řádku stisknutím **kombinace kláves CTRL + ~** a spusťte příkaz `npm test` :

```bash
npm test
```

![Testování Azure Functions pomocí JavaScriptu v VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Ladit testy

Chcete-li ladit testy, přidejte do souboru *launch.js* následující konfiguraci:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Dále nastavte zarážku v testu a stiskněte klávesu **F5**.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak psát automatizované testy pro vaše funkce, pokračujte v těchto prostředcích:

- [Ruční spuštění funkce neaktivované protokolem HTTP](./functions-manually-run-non-http.md)
- [Zpracování chyb Azure Functions](./functions-bindings-error-pages.md)
- [Funkce Azure Function Event Grid aktivovat místní ladění](./functions-debug-event-grid-trigger-local.md)
