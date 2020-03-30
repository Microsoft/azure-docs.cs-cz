---
title: Testování Azure Functions
description: Vytvoření automatizovaných testů funkce jazyka C# v sadě Visual Studio a funkce JavaScriptu v kódu VS
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: a37fd886e1bc70226b2e54750540dfcb79ee5973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768873"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie testování kódu ve službě Azure Functions

Tento článek ukazuje, jak vytvořit automatizované testy pro funkce Azure. 

Testování všech kód se doporučuje, ale můžete získat nejlepší výsledky zabalením funkce logiku a vytváření testů mimo funkce. Abstrahování logiky pryč omezuje řádky kódu funkce a umožňuje funkce výhradně zodpovědný za volání jiných tříd nebo modulů. Tento článek však ukazuje, jak vytvořit automatizované testy proti funkce http a časovač spouštěné.

Následující obsah je rozdělen do dvou různých částí určených k cílení na různé jazyky a prostředí. Můžete se naučit vytvářet testy v:

- [C# v sadě Visual Studio s xUnit](#c-in-visual-studio)
- [JavaScript ve VS kódu s Jest](#javascript-in-vs-code)

Ukázkové úložiště je k dispozici na [GitHubu](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# v sadě Visual Studio
Následující příklad popisuje, jak vytvořit aplikaci C# Function v sadě Visual Studio a spustit a testy s [xUnit](https://xunit.github.io).

![Testování funkcí Azure s C# v sadě Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Nastavení

Chcete-li nastavit prostředí, vytvořte aplikaci Function a test. Následující kroky vám pomohou vytvořit aplikace a funkce potřebné pro podporu testů:

1. [Vytvoření nové aplikace Functions](./functions-create-first-azure-function.md) a její pojmenování *Funkce*
2. [Vytvořte funkci HTTP ze šablony](./functions-create-first-azure-function.md) a pojmenujte ji *HttpTrigger*.
3. [Vytvořte funkci časovače ze šablony](./functions-create-scheduled-function.md) a pojmenujte ji *TimerTrigger*.
4. [Vytvořte aplikaci xUnit Test](https://xunit.github.io/docs/getting-started-dotnet-core) v sadě Visual Studio kliknutím na **soubor > nový > projekt > Visual C# > .NET Core > xUnit Test Project** a pojmenujte jej *Functions.Test*. 
5. Přidání odkazu z testovací aplikace do [aplikace Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) pomocí nugetu
6. [Odkaz aplikace *Funkce* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z *functions.Test* aplikace.

### <a name="create-test-classes"></a>Vytvořit testovací třídy

Nyní, když jsou vytvořeny aplikace, můžete vytvořit třídy používané ke spuštění automatizovaných testů.

Každá funkce trvá instancí [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) pro zpracování protokolování zpráv. Některé testy buď neprotokolují zprávy nebo nemají žádné obavy o způsob implementace protokolování. Další testy je třeba vyhodnotit zprávy protokolované k určení, zda je test předávání.

Třída `ListLogger` implementuje `ILogger` rozhraní a obsahuje interní seznam zpráv pro hodnocení během testu.

**Klikněte pravým tlačítkem myši** na aplikaci *Functions.Test* a vyberte **přidat > třídu**, pojmenujte **ji NullScope.cs** a zadejte následující kód:

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

Dále **klikněte pravým tlačítkem myši** na aplikaci *Functions.Test* a vyberte **přidat > třídu**, pojmenujte ji **ListLogger.cs** a zadejte následující kód:

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

Třída `ListLogger` implementuje následující členy jako `ILogger` smluvně rozhraní:

- **BeginScope**: Obory přidat kontext k protokolování. V tomto případě test pouze odkazuje na `NullScope` statickou instanci ve třídě, aby test fungoval.

- **IsEnabled**: Je `false` k dispozici výchozí hodnota.

- **Log**: Tato metoda `formatter` používá zapředpokladu funkci k formátování zprávy `Logs` a potom přidá výsledný text do kolekce.

Kolekce `Logs` je instancí třídy `List<string>` a je inicializována v konstruktoru.

Dále **klikněte pravým tlačítkem myši** na aplikaci *Functions.Test* a vyberte **přidat > třídu**, pojmenujte **ji LoggerTypes.cs** a zadejte následující kód:

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
Tento výčet určuje typ protokolování používané testy. 

Dále **klikněte pravým tlačítkem myši** na aplikaci *Functions.Test* a vyberte **přidat > třídu**, pojmenujte ji **TestFactory.cs** a zadejte následující kód:

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

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
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
Třída `TestFactory` implementuje následující členy:

- **Data**: Tato vlastnost vrátí kolekci ukázkových dat [iEnumerable.](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) Dvojice hodnot klíče představují hodnoty, které jsou předány do řetězce dotazu.

- **CreateDictionary**: Tato metoda přijímá dvojici klíč/hodnota jako `Dictionary` argumenty `QueryCollection` a vrací nový použít k vytvoření představují hodnoty řetězce dotazu.

- **CreateHttpRequest**: Tato metoda vytvoří požadavek HTTP inicializovaný s parametry daného řetězce dotazu.

- **CreateLogger**: Na základě typu protokolování vrátí tato metoda třídu protokolování použitou pro testování. Sleduje `ListLogger` protokolované zprávy, které jsou k dispozici pro vyhodnocení v testech.

Dále **klikněte pravým tlačítkem myši** na aplikaci *Functions.Test* a vyberte **přidat > třídu**, pojmenujte ji **FunctionsTests.cs** a zadejte následující kód:

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
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Členy implementované v této třídě jsou:

- **Http_trigger_should_return_known_string**: Tento test vytvoří požadavek `name=Bill` s hodnotami řetězce dotazu na funkci HTTP a zkontroluje, zda je vrácena očekávaná odpověď.

- **Http_trigger_should_return_string_from_member_data**: Tento test používá atributy xUnit k poskytnutí ukázkových dat funkci HTTP.

- **Timer_should_log_message**: Tento test `ListLogger` vytvoří instanci a předá ji funkcím časovače. Po spuštění funkce je protokol zkontrolován, aby se zajistilo, že je k dispozici očekávaná zpráva.

Pokud chcete získat přístup k nastavení aplikace v testech, můžete použít [System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Spouštění testů

Chcete-li spustit testy, přejděte do **Průzkumníka testů** a klepněte na tlačítko **Spustit vše**.

![Testování funkcí Azure s C# v sadě Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Testy ladění

Chcete-li testy ladit, nastavte v testu zarážku, přejděte do **Průzkumníka testů** a klepněte na tlačítko **Spustit > ladění posledního spuštění**.

## <a name="javascript-in-vs-code"></a>JavaScript v kódu VS

Následující příklad popisuje, jak vytvořit aplikaci Funkce JavaScriptu v Kódu VS a spustit a testy s [Jest](https://jestjs.io). Tento postup používá [rozšíření VS Code Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) k vytvoření funkce Azure.

![Testování funkcí Azure pomocí JavaScriptu v kódu VS](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Nastavení

Chcete-li nastavit prostředí, inicializovat novou aplikaci Node.js v prázdné složce spuštěním `npm init`.

```bash
npm init -y
```
Dále nainstalujte Jest spuštěním následujícího příkazu:

```bash
npm i jest
```
Nyní aktualizujte _soubor package.json_ a nahraďte existující testovací příkaz následujícím příkazem:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Vytvoření testovacích modulů
Při inicializování projektu můžete vytvořit moduly používané ke spuštění automatizovaných testů. Začněte vytvořením nové složky s názvem *testování* pro uložení modulů podpory.

Do *testovací* složky přidejte nový soubor, pojmenujte jej **defaultContext.js**a přidejte následující kód:

```javascript
module.exports = {
    log: jest.fn()
};
```
Tento modul nasune funkci *protokolu* tak, aby představovala výchozí kontext spuštění.

Dále přidejte nový soubor, pojmenujte jej **defaultTimer.js**a přidejte následující kód:

```javascript
module.exports = {
    IsPastDue: false
};
```

Tento modul implementuje `IsPastDue` vlastnost stát je jako falešný časovač instance. Konfigurace časovače, jako jsou výrazy NCRONTAB, zde nejsou vyžadovány, protože testovací postroj jednoduše volá funkci přímo k testování výsledku.

Dále použijte rozšíření VS Code Functions k [vytvoření nové funkce HTTP JavaScript](/azure/javascript/tutorial-vscode-serverless-node-01) a pojmenujte ji *HttpTrigger*. Po vytvoření funkce přidejte nový soubor do stejné složky s názvem **index.test.js**a přidejte následující kód:

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
Funkce HTTP ze šablony vrátí řetězec "Hello" zřetězený s názvem poskytnutým v řetězci dotazu. Tento test vytvoří falešnou instanci požadavku a předá ji funkci HTTP. Test zkontroluje, zda je metoda *protokolu* volána jednou a vrácený text se rovná "Hello Bill".

Dále použijte rozšíření VS Code Functions k vytvoření nové funkce časovače JavaScript a pojmenujte ji *TimerTrigger*. Po vytvoření funkce přidejte nový soubor do stejné složky s názvem **index.test.js**a přidejte následující kód:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Funkce časovače ze šablony zaznamená zprávu na konci těla funkce. Tento test zajišťuje, že funkce *protokolu* je volána jednou.

### <a name="run-tests"></a>Spouštění testů
Chcete-li spustit testy, otevřete **příkazové** okno `npm test`stisknutím kláves CTRL + ~ a spusťte :

```bash
npm test
```

![Testování funkcí Azure pomocí JavaScriptu v kódu VS](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Testy ladění

Chcete-li testy ladit, přidejte do souboru *launch.json* následující konfiguraci:

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

Dále nastavte zarážku v testu a stiskněte **klávesu F5**.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili psát automatizované testy pro vaše funkce, pokračujte v těchto zdrojích:
- [Ruční spuštění funkce neaktivované protokolem HTTP](./functions-manually-run-non-http.md)
- [Zpracování chyb Azure Functions](./functions-bindings-error-pages.md)
- [Mřížka událostí Azure aktivační událost místní ladění](./functions-debug-event-grid-trigger-local.md)
