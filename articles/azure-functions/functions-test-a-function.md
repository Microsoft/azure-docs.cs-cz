---
title: Azure Functions testování
description: Vytváření automatizovaných testů pro C# funkci v aplikaci Visual Studio a funkce javascriptu v vs Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: funkce Azure Functions, Functions, zpracování událostí, Webhooky, dynamická výpočty, architektura bez serveru, testování
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: ff3d7d1272f9067f6bf9791c7964f8bf5f71945b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709344"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie pro testování kódu v Azure Functions

Tento článek ukazuje, jak vytvořit automatizované testy pro Azure Functions. 

Testování veškerého kódu je doporučeno, ale můžete dosáhnout nejlepších výsledků zabalením logiky funkce a vytvořením testů mimo funkci. Vyabstrakce logiky omezuje řádky kódu funkce a umožňuje, aby byla funkce výhradně zodpovědná za volání jiných tříd nebo modulů. Tento článek však ukazuje, jak vytvořit automatizované testy proti funkci aktivované protokolem HTTP a časovačem.

Následující obsah je rozdělen do dvou různých sekcí určených pro cílení na různé jazyky a prostředí. Můžete se naučit sestavit testy v nástroji:

- [C#v aplikaci Visual Studio s xUnit](#c-in-visual-studio)
- [JavaScript v VS Code s jest](#javascript-in-vs-code)

Ukázkové úložiště je k dispozici na [GitHubu](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# v sadě Visual Studio
Následující příklad popisuje, jak vytvořit aplikaci C# funkcí v aplikaci Visual Studio a spustit a testy pomocí [xUnit](https://xunit.github.io).

![Testování Azure Functions C# v aplikaci Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Instalace

Pokud chcete nastavit prostředí, vytvořte funkci a otestujte aplikaci. Následující kroky vám pomůžou vytvořit aplikace a funkce, které jsou potřeba pro podporu testů:

1. [Vytvoření nové aplikace Functions](./functions-create-first-azure-function.md) a pojmenování IT *funkcí*
2. [Vytvořte funkci http ze šablony](./functions-create-first-azure-function.md) a pojmenujte ji *HttpTrigger*.
3. [Vytvořte funkci časovače ze šablony](./functions-create-scheduled-function.md) a pojmenujte ji *TimerTrigger*.
4. V aplikaci Visual Studio [vytvořte aplikaci XUnit test](https://xunit.github.io/docs/getting-started-dotnet-core) tak, že kliknete na **soubor > nový C# > projekt > Visual > .NET Core > xUnit test Project** a pojmenujte ho IT *Functions. test*. 
5. Použití Nugetu k přidání odkazů z testovací aplikace [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Odkázat na aplikaci *Functions* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z *Functions. test* App.

### <a name="create-test-classes"></a>Vytváření testovacích tříd

Nyní, když jsou vytvořeny aplikace, můžete vytvořit třídy používané ke spouštění automatizovaných testů.

Každá funkce převezme instanci [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) , která zpracovává protokolování zpráv. Některé testy buď neprotokolují zprávy, nebo nemají žádné obavy o implementaci protokolování. Jiné testy potřebují k vyhodnocení zpráv protokolovaných za účelem určení, zda test projde.

Třída `ListLogger` je určena k implementaci rozhraní `ILogger` a při testování podrží interní seznam zpráv pro vyhodnocení.

**Klikněte pravým tlačítkem** na aplikaci *Functions. test* a vyberte **Přidat > třídu**, pojmenujte ji **NullScope.cs** a zadejte následující kód:

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

Potom klikněte **pravým tlačítkem myši** na aplikace *Functions. test* a vyberte **Přidat > třídu**, pojmenujte ji **ListLogger.cs** a zadejte následující kód:

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

Třída `ListLogger` implementuje následující členy, které jsou vynásobené rozhraním `ILogger`:

- **BeginScope**: obory přidávají kontext do svého protokolování. V tomto případě test pouze odkazuje na statickou instanci na třídě `NullScope`, aby test mohl fungovat.

- **Povoleno**: je k dispozici výchozí hodnota `false`.

- **Log**: Tato metoda používá k formátování zprávy poskytnutou funkci `formatter` a následně do kolekce `Logs` přidá výsledný text.

Kolekce `Logs` je instancí `List<string>` a inicializuje se v konstruktoru.

Potom klikněte **pravým tlačítkem myši** na aplikace *Functions. test* a vyberte **Přidat > třídu**, pojmenujte ji **LoggerTypes.cs** a zadejte následující kód:

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

Potom klikněte **pravým tlačítkem myši** na aplikace *Functions. test* a vyberte **Přidat > třídu**, pojmenujte ji **TestFactory.cs** a zadejte následující kód:

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

- **Data**: Tato vlastnost vrací kolekci [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) vzorových dat. Páry klíč-hodnota představují hodnoty, které jsou předány do řetězce dotazu.

- **CreateDictionary –** : Tato metoda přijímá jako argumenty dvojici klíč/hodnota a vrátí novou `Dictionary` použitou k vytvoření `QueryCollection` pro reprezentaci hodnot řetězce dotazu.

- **CreateHttpRequest**: Tato metoda vytvoří požadavek HTTP inicializovaný pomocí daných parametrů řetězce dotazu.

- **CreateLogger**: v závislosti na typu protokolovacího nástroje Tato metoda vrátí třídu protokolovacího nástroje použitou pro testování. @No__t-0 uchovává záznam protokolovaných zpráv, které jsou k dispozici pro vyhodnocení v testech.

Potom klikněte **pravým tlačítkem myši** na aplikace *Functions. test* a vyberte **Přidat > třídu**, pojmenujte ji **FunctionsTests.cs** a zadejte následující kód:

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
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
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

- **Http_trigger_should_return_known_string**: Tento test vytváří požadavek s hodnotami řetězce dotazu `name=Bill` na funkci http a kontroluje, zda je vrácena očekávaná odpověď.

- **Http_trigger_should_return_string_from_member_data**: Tento test pomocí atributů xUnit poskytuje ukázková data funkce http.

- **Timer_should_log_message**: Tento test vytvoří instanci `ListLogger` a předá ji funkcím časovače. Po spuštění funkce se zkontroluje protokol, aby se zajistilo, že je k dispozici očekávaná zpráva.

Chcete-li získat přístup k nastavení aplikace v testech, můžete použít [System. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Spouštění testů

Chcete-li spustit testy, přejděte do **Průzkumníka testů** a klikněte na možnost **Spustit vše**.

![Testování Azure Functions C# v aplikaci Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Ladit testy

Chcete-li ladit testy, nastavte zarážku na test, přejděte do **Průzkumníka testů** a klikněte na tlačítko **Spustit > poslední spuštění ladění**.

## <a name="javascript-in-vs-code"></a>JavaScript v VS Code

Následující příklad popisuje, jak vytvořit aplikaci funkcí JavaScriptu v VS Code a spustit a testy pomocí [jest](https://jestjs.io). Tento postup používá [rozšíření vs Code Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) k vytvoření Azure Functions.

![Testování Azure Functions pomocí JavaScriptu v VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Instalace

Chcete-li nastavit prostředí, inicializujte novou aplikaci Node. js v prázdné složce spuštěním `npm init`.

```bash
npm init -y
```
Dále nainstalujte jest spuštěním následujícího příkazu:

```bash
npm i jest
```
Nyní aktualizujte soubor _Package. JSON_ a nahraďte existující testovací příkaz následujícím příkazem:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Vytváření testovacích modulů
V případě inicializovaného projektu můžete vytvořit moduly používané pro spuštění automatizovaných testů. Začněte vytvořením nové složky s názvem *testování* , která bude obsahovat moduly podpory.

Ve složce *testování* přidejte nový soubor a pojmenujte jej **defaultContext. js**a přidejte následující kód:

```javascript
module.exports = {
    log: jest.fn()
};
```
Tento modul nakládá funkce *protokolu* tak, aby reprezentovala výchozí kontext spuštění.

Dále přidejte nový soubor a pojmenujte jej **defaultTimer. js**a přidejte následující kód:

```javascript
module.exports = {
    IsPastDue: false
};
```
Tento modul implementuje vlastnost `IsPastDue` jako nefalešnou instanci časovače.

Dále pomocí rozšíření VS Code Functions [vytvořte novou funkci http jazyka JavaScript](/azure/javascript/tutorial-vscode-serverless-node-01) a pojmenujte ji *HttpTrigger*. Po vytvoření funkce přidejte nový soubor do stejné složky s názvem **index. test. js**a přidejte následující kód:

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

Potom pomocí rozšíření VS Code Functions vytvořte novou funkci časovače JavaScriptu a pojmenujte ji *TimerTrigger*. Po vytvoření funkce přidejte nový soubor do stejné složky s názvem **index. test. js**a přidejte následující kód:

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
Chcete-li spustit testy, otevřete okno příkazového řádku stisknutím **kombinace kláves CTRL + ~** a spusťte `npm test`:

```bash
npm test
```

![Testování Azure Functions pomocí JavaScriptu v VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Ladit testy

Chcete-li ladit testy, přidejte do souboru *Launch. JSON* následující konfiguraci:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Dále nastavte zarážku v testu a stiskněte klávesu **F5**.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak psát automatizované testy pro vaše funkce, pokračujte v těchto prostředcích:
- [Ruční spuštění funkce bez protokolu HTTP aktivované](./functions-manually-run-non-http.md)
- [Zpracování chyb Azure Functions](./functions-bindings-error-pages.md)
- [Funkce Azure Function Event Grid aktivovat místní ladění](./functions-debug-event-grid-trigger-local.md)
