---
title: Testování Azure Functions
description: Vytváření automatizovaných testů pro C# funkce v sadě Visual Studio a funkce jazyka JavaScript v nástroji VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, webhooky, dynamické výpočty, architekturu bez serveru, testování
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: cshoe
ms.openlocfilehash: 44d2960d9cf5828af588f9392667553c18dedb0f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103451"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie pro testování kódu ve službě Azure Functions

Tento článek ukazuje, jak vytvořit automatizované testy pro službu Azure Functions. 

Testování veškerý kód se doporučuje, ale může dosáhnout nejlepších výsledků zabalením funkce logiku a vytváření testů mimo funkci. Poskytuje abstrakci logiky hned omezuje funkce řádků kódu a umožňuje funkci zodpovídat výhradně jen za volání jiné třídy nebo moduly. Tento článek ale ukazuje, jak vytvořit automatizované testy HTTP a funkci aktivovanou časovačem.

Obsah, který následuje je rozdělený do dvou různých oddílů určená k cílení na různé jazyky a prostředí. Můžete naučit vytvářet testy:

- [C#v sadě Visual Studio s použitím xUnit](#c-in-visual-studio)
- [JavaScript v nástroji VS Code s Jest](#javascript-in-vs-code)

Úložišti ukázek je k dispozici na [Githubu](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#v sadě Visual Studio
Následující příklad popisuje, jak vytvořit C# aplikace v sadě Visual Studio funkce a spustit testy pomocí služby [xUnit](https://xunit.github.io).

![Testování Azure Functions se službou C# v sadě Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Nastavení

K nastavení prostředí, vytvořte funkci a testování aplikací. Následující kroky vám pomůžou vytvořit aplikace a funkce, které jsou potřeba k podpoře testy:

1. [Vytvořit novou aplikaci funkcí](./functions-create-first-azure-function.md) a pojmenujte ho *funkce*
2. [Vytvoření funkce protokolu HTTP v šabloně](./functions-create-first-azure-function.md) a pojmenujte ho *HttpTrigger*.
3. [Vytvoření funkce časovačem ze šablony](./functions-create-scheduled-function.md) a pojmenujte ho *TimerTrigger*.
4. [Vytvoření aplikace testů xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) a pojmenujte ho *Functions.Test*.
5. [Odkaz *funkce* aplikace](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z *Functions.Test* aplikace.

### <a name="create-test-classes"></a>Vytvoření tříd testu

Teď, když se vytvoří aplikace, můžete vytvořit třídy používané pro spuštění automatizovaných testů.

Každá funkce přebírá instanci [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) pro zpracování přihlašování zprávy. Některé testy protokolování zpráv nebo mít žádné obavy způsob implementace protokolování. Několik dalších testů je nutné vyhodnotit zprávy zaprotokolované k určení, zda je předání testu.

`ListLogger` Třída slouží k implementaci `ILogger` rozhraní a udržení ve vnitřním seznamu zpráv pro vyhodnocení během testu.

**Klikněte pravým tlačítkem na** na *Functions.Test* aplikaci a vyberte **Přidat > třída**, pojmenujte ho **ListLogger.cs** a zadejte následující kód:

```csharp
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions.Internal;
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

`ListLogger` Třída implementuje následující členy jako zakázku podle `ILogger` rozhraní:

- **BeginScope**: obory přidání kontextu do vaší protokolování. V tomto případě test právě odkazuje na statickou instanci služby [NullScope](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.abstractions.internal.nullscope) třídu, která umožňuje testovací funkce.

- **Hodnotu IsEnabled**: výchozí hodnota `false` je k dispozici.

- **Protokol**: Tato metoda používá zadaných `formatter` fungovat jako formát zprávy a poté přidá výsledný text, který má `Logs` kolekce.

`Logs` Kolekce je instance `List<string>` a je inicializována v konstruktoru.

Dále **klikněte pravým tlačítkem na** na *Functions.Test* aplikaci a vyberte **Přidat > třída**, pojmenujte ho **LoggerTypes.cs** a zadejte Následující kód:

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
Tento výčet Určuje typ protokolovacího nástroje, testy použít. 

Dále **klikněte pravým tlačítkem na** na *Functions.Test* aplikaci a vyberte **Přidat > třída**, pojmenujte ho **TestFactory.cs** a zadejte Následující kód:

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
`TestFactory` Třída implementuje následující členy:

- **Data**: Tato vlastnost vrátí [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) kolekce ukázková data. Páry klíč-hodnota představují hodnoty, které jsou předány do řetězce dotazu.

- **Createdictionary –**: Tato metoda přijímá dvojice klíč/hodnota jako argumenty a vrátí nový `Dictionary` použitý k vytvoření `QueryCollection` k reprezentaci hodnoty řetězce dotazu.

- **CreateHttpRequest**: Tato metoda vytvoří inicializovat pomocí parametrů řetězce dotazu daného požadavku HTTP.

- **CreateLogger**: podle typu protokolovač, tato metoda vrátí třídu protokolovací nástroj pro testování. `ListLogger` Uchovává informace o zprávy zaznamenané dříve k dispozici pro vyhodnocení v testech.

Dále **klikněte pravým tlačítkem na** na *Functions.Test* aplikaci a vyberte **Přidat > třída**, pojmenujte ho **FunctionsTests.cs** a zadejte Následující kód:

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
            TimerFunction.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Členy implementovány v této třídě jsou:

- **Http_trigger_should_return_known_string**: Tento test vytvoří žádost s dotazem řetězcové hodnoty `name=Bill` funkce protokolem HTTP a kontroly, které se vrátí očekávaná odezva.

- **Http_trigger_should_return_string_from_member_data**: Tento test pomocí atributů xUnit poskytuje ukázková data do funkce protokolu HTTP.

- **Timer_should_log_message**: Tento test vytvoří instanci `ListLogger` a předá ji do funkcí časovače. Po spuštění funkce protokolu se kontroluje k zajištění, že je k dispozici očekávaná zpráva.

### <a name="run-tests"></a>Spouštění testů

Chcete-li spustit testy, přejděte na **Průzkumník testů** a klikněte na tlačítko **spustit všechny**.

![Testování Azure Functions se službou C# v sadě Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Ladit testy

Ladit testy, nastavte zarážku v testu, přejděte na **Průzkumníka testů** a klikněte na tlačítko **spuštění > ladění poslední spuštění**.

## <a name="javascript-in-vs-code"></a>JavaScript v nástroji VS Code

Následující příklad popisuje, jak vytvořit aplikaci funkcí jazyka JavaScript v nástroji VS Code a spustit a testy s [Jest](https://jestjs.io). Tento postup používá [funkcí VS Code příponou](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vytvářet funkce Azure.

![Testování Azure Functions s použitím jazyka JavaScript v nástroji VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Nastavení

K nastavení prostředí, inicializovat spuštěním nové aplikace Node.js v prázdné složce `npm init`.

```bash
npm init -y
```
Dále nainstalujte Jest spuštěním následujícího příkazu:

```bash
npm i jest
```
Teď aktualizovat _package.json_ nahradit stávající příkazu test pomocí tohoto příkazu:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Vytvořit test moduly
S projektem inicializován můžete vytvořit moduly používané ke spuštění automatizovaných testů. Začněte tím, že vytvoříte novou složku s názvem *testování* pro uložení podpora modulů.

V *testování* složky přidejte nový soubor, pojmenujte ho **defaultContext.js**a přidejte následující kód:

```javascript
module.exports = {
    log: jest.fn()
};
```
Tento modul mocks *protokolu* funkce představující výchozí kontext spuštění.

V dalším kroku přidejte nový soubor, pojmenujte ho **defaultTimer.js**a přidejte následující kód:

```javascript
module.exports = {
    isPastDue: false
};
```
Tento modul implementuje `isPastDue` vlastnost stát je jako instance falešné časovače.

Pak pomocí rozšíření VS Code funkce [vytvořit novou funkci jazyka JavaScript HTTP](https://code.visualstudio.com/tutorials/functions-extension/getting-started) a pojmenujte ho *HttpTrigger*. Jakmile se vytvoří funkci, přidejte nový soubor ve stejné složce s názvem **index.test.js**a přidejte následující kód:

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
Funkce protokolu HTTP v šabloně vrátí řetězec "Hello", které jsou spojeny s názvem zadaná v řetězci dotazu. Tento test vytvoří falešnou instanci požadavku a předá ho do funkce protokolu HTTP. Test kontroluje, zda *protokolu* metoda se volá jednou a vráceném textu budou zobrazeny se rovná "Hello Bill".

Pak pomocí rozšíření VS Code funkce k vytvoření nové funkce jazyka JavaScript časovač a pojmenujte ho *TimerTrigger*. Jakmile se vytvoří funkci, přidejte nový soubor ve stejné složce s názvem **index.test.js**a přidejte následující kód:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Funkce časovačem ze šablony zaznamená zprávu na konci těla funkce. Tento test zajistí *protokolu* funkce se volá jednou.

### <a name="run-tests"></a>Spouštění testů
Chcete-li spustit testy, stiskněte **CTRL + ~** otevřete okno příkazového řádku a spuštění `npm test`:

```bash
npm test
```

![Testování Azure Functions s použitím jazyka JavaScript v nástroji VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Ladit testy

Chcete-li ladit testy, přidejte následující konfiguraci pro vaše *launch.json* souboru:

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

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak sepsání automatizovaných testů pro vaše funkce, pokračujte s těmito prostředky:

- [Zpracování chyb Azure Functions](./functions-bindings-error-pages.md)
- [Funkce Azure Event Grid aktivovat místní ladění](./functions-debug-event-grid-trigger-local.md)