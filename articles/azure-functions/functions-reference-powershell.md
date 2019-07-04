---
title: Referenční informace prostředí PowerShell pro vývojáře pro službu Azure Functions
description: Naučte se vyvíjet funkce s použitím prostředí PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 489c94f37b6c88db001dee437cc6ed89383e6053
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442186"
---
# <a name="azure-functions-powershell-developer-guide"></a>Příručka pro vývojáře Azure Functions Powershellu

Tento článek obsahuje podrobnosti o tom, jak psát Azure Functions pomocí Powershellu.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

PowerShell Azure – funkce (funkce) je vyjádřena jako skript prostředí PowerShell, který se spustí při spuštění. Každý skript funkce má se souvisejícím `function.json` soubor, který definuje, jak funkce se chová, jako je například jak se aktivuje a její vstupní a výstupní parametry. Další informace najdete v tématu [triggery a vazby článku](functions-triggers-bindings.md). 

Stejně jako jiné druhy funkce, funkce skriptu Powershellu trvat, než se parametry, které odpovídají názvům vstupní vazby definované v `function.json` souboru. A `TriggerMetadata` , který obsahuje další informace o aktivační událost, která spuštění funkce také předat parametr.

Tento článek předpokládá, že jste si už přečetli [referenční informace pro vývojáře Azure Functions](functions-reference.md). Byste měli mít také dokončenou [funkce Rychlý start pro PowerShell](functions-create-first-function-powershell.md) k vytvoření první funkce prostředí PowerShell.

## <a name="folder-structure"></a>struktura složek

Strukturu složek požadované pro prostředí PowerShell projekt vypadá takto. Toto výchozí nastavení je možné změnit. Další informace najdete v tématu [Soubor_skriptu](#configure-function-scriptfile) níže v části.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

V kořenovém adresáři projektu neexistuje sdílené [ `host.json` ](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má složku s vlastními souboru s kódem (.ps1) a vazbu konfigurační soubor (`function.json`). Název souboru function.json nadřazený adresář je vždy název vaší funkce.

Některé vazby vyžaduje přítomnost `extensions.csproj` souboru. Vazba rozšíření vyžaduje [verze 2.x](functions-versions.md) funkce modulu runtime, které jsou definovány v `extensions.csproj` souboru se soubory knihovny v `bin` složky. Při vývoji místně, musíte [registraci rozšíření vazby](functions-bindings-register.md#extension-bundles). Při vytváření funkcí na webu Azure Portal, je tato registrace provede za vás.

V prostředí PowerShell aplikace Function App, může volitelně obsahovat `profile.ps1` která se spouští při spuštění aplikace function app (jinak znát pod některým z  *[studený start](#cold-start)* . Další informace najdete v tématu [Powershellu profilu](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definice skriptu prostředí PowerShell jako funkce

Ve výchozím nastavení, modul runtime služby Functions hledá vaši funkci v `run.ps1`, kde `run.ps1` sdílí stejné nadřazený adresář jako odpovídající `function.json`.

Skript je předán počet argumentů na spuštění. Chcete-li zpracovat tyto parametry, přidejte `param` bloku do horní části skriptu jako v následujícím příkladu:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametr TriggerMetadata

`TriggerMetadata` Parametr slouží k zadání dalších informací o aktivační události. Další metadata se liší od vazby vazby, ale všechny obsahují `sys` vlastnost, která obsahuje následující data:

```powershell
$TriggerMetadata.sys
```

| Vlastnost   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Pokud ve standardu UTC, funkce aktivovalo        | DateTime |
| methodName | Název funkce, která byla aktivována.     | string   |
| RandGuid   | Jedinečný identifikátor guid pro toto spuštění funkce | string   |

Každý typ aktivační události má jinou sadu metadat. Například `$TriggerMetadata` pro `QueueTrigger` obsahuje `InsertionTime`, `Id`, `DequeueCount`, mimo jiné. Další informace o aktivační událost fronty metadat, přejděte [oficiální dokumentaci pro aktivační procedury řízení front](functions-bindings-storage-queue.md#trigger---message-metadata). Přečtěte si dokumentaci k na [aktivační události](functions-triggers-bindings.md) pracujete, pokud chcete zobrazit, co se dodává v metadatech aktivační události.

## <a name="bindings"></a>Vazby

V prostředí PowerShell [vazby](functions-triggers-bindings.md) se konfigurují a definované v function.json funkce. Funkce s vazby pracovat několika způsoby.

### <a name="reading-trigger-and-input-data"></a>Čtení triggeru a vstupních dat

Aktivační událost a vstupní vazby se číst jako parametry předané do funkce. Mít vstupní vazby `direction` nastavena na `in` v function.json. `name` Vlastnosti definované v `function.json` se název parametru, `param` bloku. Protože pojmenované parametry Powershellu použije k vytvoření vazby, není důležité pořadí parametrů. Je však osvědčeným postupem podle pořadí z vazeb definovaných v `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zápis výstupní data

Ve funkcích, má výstupní vazbu `direction` nastavena na `out` v function.json. Je možné zapsat do výstupní vazbu pomocí `Push-OutputBinding` rutiny, která je k dispozici pro modul runtime služby Functions. Ve všech případech `name` vlastnost vazby, jak jsou definovány v `function.json` odpovídá `Name` parametr `Push-OutputBinding` rutiny.

Následující ukazuje způsob volání `Push-OutputBinding` ve skriptu funkce:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Můžete také předat hodnotu pro konkrétní vazbu prostřednictvím kanálu.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` jak se bude chovat různě v závislosti na hodnotě zadané pro `-Name`:

* Když se zadaným názvem nelze přeložit na platnou výstupní vazby, je vržena chyba.

* Pokud výstupní vazbu přijímá kolekci hodnot, můžete volat `Push-OutputBinding` opakovaně tak, aby nabízel více hodnot.

* Pokud výstupní vazbu akceptuje pouze hodnotu singleton, volání `Push-OutputBinding` podruhé vyvolá chybu.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Syntaxe

Následují platné parametry pro volání `Push-OutputBinding`:

| Název | Type | Pozice | Popis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Název výstupní vazbu chcete nastavit. |
| **`-Value`** | Object | 2 | Hodnota výstupní vazbu chcete nastavit, který je přijímán z kanálu ByValue. |
| **`-Clobber`** | SwitchParameter | s názvem | (Volitelné) -Li zadána, vynutí hodnota k nastavení pro vazby zadaným výstupem. | 

Rovněž jsou podporovány následující běžné parametry: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Další informace najdete v tématu [o CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Příklad OutputBinding nabízených oznámení: Odpovědi protokolu HTTP

Aktivační událost HTTP vrátí odpověď s použitím výstupní vazbu s názvem `response`. V následujícím příkladu výstupu vazba `response` má hodnotu "výstup #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Protože výstupem je do protokolu HTTP, který přijímá hodnotu singleton, je vyvolána k chybě, když `Push-OutputBinding` nazývá podruhé.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Výstupy, které přijímají pouze hodnoty typu singleton, můžete použít `-Clobber` parametr přepsat původní hodnotu namísto pokusu o přidání do kolekce. V následujícím příkladu se předpokládá, že jste ho už přidali hodnotu. S použitím `-Clobber`, přepíše existující hodnoty k vrácení hodnoty "výstup #3" odpověď z následujícího příkladu:

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Příklad OutputBinding nabízených oznámení: Výstupní vazba fronty

`Push-OutputBinding` slouží k odesílání dat do výstupní vazby, například [Azure Queue storage výstupní vazby](functions-bindings-storage-queue.md#output). Zprávy do fronty v následujícím příkladu má hodnotu "výstup #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Výstupní vazby pro fronty úložiště je možné zadat více výstupní hodnoty. V takovém případě volání v následujícím příkladu po první zapisuje do fronty, seznam se dvěma položkami: "výstup #1" a "výstup #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Následující příklad, když je volána po předchozí dvě přidá dva další hodnoty do kolekce výstup:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Při zápisu do fronty, zpráva obsahuje tyto čtyři hodnoty: "output #1", "výstupu #2", "výstup #3" a "výstup #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Rutiny

Můžete použít `Get-OutputBinding` rutina pro načtení hodnoty aktuálně nastavené pro výstupní vazby. Tato rutina načte zatřiďovací tabulka, která obsahuje názvy výstupních vazeb s jejich příslušných hodnot. 

Následuje příklad použití `Get-OutputBinding` k vrácení aktuální hodnoty vazby:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` také obsahuje parametr s názvem `-Name`, které můžete použít k filtrování vrácených vazby, jako v následujícím příkladu:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Zástupné znaky (*) jsou podporovány v `Get-OutputBinding`.

## <a name="logging"></a>Protokolování

Protokolování v prostředí PowerShell funkce funguje jako regulární protokolování Powershellu. Rutiny protokolování můžete použít k zápisu do každého výstupního datového proudu. Každá rutina mapuje na úroveň protokolu používané funkcí.

| Funkce úroveň protokolování | Rutiny protokolování |
| ------------- | -------------- |
| Chyba | **`Write-Error`** |
| Upozornění | **`Write-Warning`**  | 
| Informace | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informace | Zapíše do _informace_ úrovně protokolování. |
| Ladění | **`Write-Debug`** |
| Trasování | **`Write-Progress`** <br /> **`Write-Verbose`** |

Kromě těchto rutin, nic se zapisují do kanálu je přesměrován na `Information` protokolu úrovně a zobrazují se pomocí Powershellu výchozí formátování.

> [!IMPORTANT]
> Použití `Write-Verbose` nebo `Write-Debug` rutiny není dostatečně podrobné a ladit úrovně protokolování. Musíte také nakonfigurovat práh úroveň protokolu, který deklaruje jaké úroveň protokoly vám skutečně záleží. Další informace najdete v tématu [nakonfigurovat úroveň protokolu aplikace funkce](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Nakonfigurujte úroveň protokolu aplikace – funkce

Azure Functions umožňuje definovat prahové hodnoty úroveň k tomu, aby ovládací prvek způsob, jak funkce zapíše do protokolů. Chcete-li nastavení prahové hodnoty pro všechna trasování zapsána do konzoly, použijte `logging.logLevel.default` vlastnost v [ `host.json` souboru][referenční materiály k host.json]. Toto nastavení platí pro všechny funkce v aplikaci function app.

Následující příklad nastaví prahovou hodnotu Zapnutí podrobného protokolování pro všechny funkce, ale nastaví prahovou hodnotu a povolit protokolování ladění pro funkci s názvem `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Další informace najdete v tématu [referenční materiály k host.json].

### <a name="viewing-the-logs"></a>Zobrazení protokolů

Pokud vaše aplikace Function App běží v Azure, můžete ji sledovat Application Insights. Čtení [monitorování Azure Functions](functions-monitoring.md) získat další informace o zobrazení a dotazování protokolů funkce.

Pokud používáte aplikaci Function App místně pro vývoj, protokoluje výchozí systém souborů. Chcete-li zobrazit protokoly v konzole, nastavte `AZURE_FUNCTIONS_ENVIRONMENT` proměnnou prostředí, aby `Development` před spuštěním aplikace Function App.

## <a name="triggers-and-bindings-types"></a>Triggery a vazby typů

Existuje několik triggerů a vazeb k dispozici pro použití s vaší aplikace function app. Úplný seznam aktivačními událostmi a vazbami [najdete tady](functions-triggers-bindings.md#supported-bindings).

Všechny aktivační události a vazby jsou vyjádřené v kódu několik typů reálná data:

* Zatřiďovací tabulka
* string
* Byte
* int
* double
* HttpRequestContext
* HttpResponseContext

Prvních pět typů v tomto seznamu jsou standardní typy .NET. Poslední dva se používají pouze [HttpTrigger trigger](#http-triggers-and-bindings).

Každý parametr vazby ve vašich funkcí musí být jeden z těchto typů.

### <a name="http-triggers-and-bindings"></a>HTTP aktivačními událostmi a vazbami

HTTP a triggerů webhooků a HTTP výstupní vazby pomocí žádostí a odpovědí objekty představují zasílání zpráv protokolu HTTP.

#### <a name="request-object"></a>Objekt žádosti

Žádost o objekt, který je předán do skriptu je typu `HttpRequestContext`, který má následující vlastnosti:

| Vlastnost  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Objekt, který obsahuje text žádosti. `Body` je serializován do nejlepší typ založené na datech. Například pokud jsou data JSON, to je předáno jako zatřiďovací tabulku. Pokud jsou data řetězce, je předán v podobě řetězce. | objekt |
| **`Headers`** | Slovník, který obsahuje hlavičky požadavku.                | Dictionary < string, string ><sup>*</sup> |
| **`Method`** | Metoda HTTP požadavku.                                | string                    |
| **`Params`**  | Objekt, který obsahuje směrování parametry požadavku. | Dictionary < string, string ><sup>*</sup> |
| **`Query`** | Objekt, který obsahuje parametry dotazu.                  | Dictionary < string, string ><sup>*</sup> |
| **`Url`** | Adresa URL požadavku.                                        | string                    |

<sup>*</sup> Všechny `Dictionary<string,string>` klíče jsou malá a velká písmena.

#### <a name="response-object"></a>Objekt odpovědi

Objekt odpovědi, který se má odeslat zpět je typu `HttpResponseContext`, který má následující vlastnosti:

| Vlastnost      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Objekt, který obsahuje text odpovědi.           | objekt                    |
| **`ContentType`** | Krátký ručně k nastavení typu obsahu pro odpověď. | string                    |
| **`Headers`** | Objekt, který obsahuje hlavičky odpovědi.               | Slovník nebo zatřiďovací tabulky   |
| **`StatusCode`**  | Stavový kód HTTP odpovědi.                       | řetězec nebo int             |

#### <a name="accessing-the-request-and-response"></a>Přístup k požadavku a odpovědi

Při práci s triggerů HTTP, můžete přistupovat požadavku HTTP stejným způsobem jako u jiných vstupní vazby. Jedná `param` bloku.

Použití `HttpResponseContext` objekt vrátí odpověď, jak je znázorněno v následujícím:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Výsledek volání této funkce by byl:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Přetypování typu pro triggery a vazby

Pro některé vazby jako vazby objektů blob, mohli jste k určení typu parametru.

Například pokud chcete, aby data z úložiště objektů Blob zadaná jako řetězec, přidejte následující přetypovat na typ Moje `param` blok:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil prostředí PowerShell

V prostředí PowerShell je Princip profil prostředí PowerShell. Pokud nejste obeznámeni s profily Powershellu, přečtěte si téma [o profilech](/powershell/module/microsoft.powershell.core/about/about_profiles).

V prostředí PowerShell funkce spustí skript profil při spuštění aplikace function app. Aplikace Function App spustit při prvním nasazení a po se nečinný ([studený start](#cold-start)).

Když vytvoříte aplikaci function app pomocí nástrojů, jako je Visual Studio Code a Azure Functions Core Tools, výchozí `profile.ps1` se vytvoří za vás. Výchozí profil je udržován [v úložišti Core Tools GitHub](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) a obsahuje:

* Automatické ověřování MSI do Azure.
* Možnost zapnout prostředí Azure PowerShell `AzureRM` aliasy prostředí PowerShell, pokud byste o ni.

## <a name="powershell-version"></a>Verze prostředí PowerShell

Následující tabulka uvádí verze Powershellu používá každá hlavní verze modul runtime služby Functions:

| Verze funkcí | Verze prostředí PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (uzamčeno modulem runtime) |
| 2.x               | PowerShell Core 6                              |

Zobrazí aktuální verzi s tiskem `$PSVersionTable` z jakékoli funkce.

## <a name="dependency-management"></a>Správa závislostí

Funkce Powershellu podporují správu moduly Azure pomocí služby. Úpravy host.json a nastavením vlastnosti managedDependency povoleno na hodnotu true, bude soubor requirements.psd1 zpracovat. Nejnovější moduly Azure bude automaticky staženy a k dispozici funkce.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

Requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Využívá vlastní nebo moduly z [Galerie prostředí PowerShell](https://powershellgallery.com) se trochu liší od jak by to provedete normálně.

Při instalaci modulu na místním počítači odkazuje v jednom z globálně dostupné složky ve vaší `$env:PSModulePath`. Vaše funkce běží v Azure, nebudete mít přístup k modulům na vašem počítači nainstalovaný. To vyžaduje, aby `$env:PSModulePath` funkce prostředí PowerShell aplikace se liší od `$env:PSModulePath` regulární Powershellového skriptu.

Ve službě Functions `PSModulePath` obsahuje dvě cesty:

* A `Modules` složku, která existuje v kořenovém adresáři aplikace Function App.
* Cesta k `Modules` složku, která se nachází uvnitř pracovních procesů jazyk prostředí PowerShell.

### <a name="function-app-level-modules-folder"></a>Funkce na úrovni aplikace `Modules` složky

Pokud chcete použít vlastní moduly nebo moduly Powershellu z Galerie prostředí PowerShell, můžete umístit moduly, na kterých závisí vaše funkce v `Modules` složky. Z této složky moduly jsou automaticky dostupné pro modul runtime služby functions. Všechny funkce v aplikaci function app můžete použít tyto moduly.

Pokud chcete využít výhod této funkce, vytvořte `Modules` složku v kořenovém adresáři aplikace function App. Uložte modulů, které chcete používat ve vašich funkcí v tomto umístění.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Použít `Save-Module` uložit všechny moduly, funkce použít nebo zkopírovat vlastní moduly pro `Modules` složky. Se složkou moduly aplikace function app by měl mít následující strukturu složek:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Při spuštění aplikace function app se pracovní proces jazyk prostředí PowerShell přidá to `Modules` složku `$env:PSModulePath` tak, aby se můžete spolehnout na modul autoloading stejně, jako byste to udělali v pravidelných skript prostředí PowerShell.

### <a name="language-worker-level-modules-folder"></a>Úroveň pracovních procesů jazyka `Modules` složky

Několik modulů se běžně používají pracovní proces jazyk prostředí PowerShell. Tyto moduly, které jsou definovány v poslední pozice `PSModulePath`. 

Aktuální seznam modulů je následující:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modul používaný pro práci s archivy, jako je třeba `.zip`, `.nupkg`a další.
* **ThreadJob**: Implementace založená na vláknech úlohy prostředí PowerShell, rozhraní API.

Funkce používá nejnovější verze těchto modulů. Pokud chcete použít konkrétní verzi tyto moduly, můžete umístit na konkrétní verzi `Modules` složky vaší aplikace function App.

## <a name="environment-variables"></a>Proměnné prostředí

Ve službě Functions [nastavení aplikace](functions-app-settings.md), jako je například připojení služby jsou řetězce, zveřejní jako proměnné prostředí během provádění. Může přistupovat tato nastavení pomocí `$env:NAME_OF_ENV_VAR`, jak je znázorněno v následujícím příkladu:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění, nastavení aplikace se načítají z [local.settings.json](functions-run-local.md#local-settings-file) souboru projektu.

## <a name="concurrency"></a>Souběžnost

Ve výchozím nastavení modul runtime funkcí Powershellu zpracovat pouze jednoho vyvolání funkce v čase. Tato úroveň souběžnosti však nemusí být dostatečné v následujících situacích:

* Když se snažíte zpracovat velký počet volání ve stejnou dobu.
* Pokud máte funkce, které vyvolají další funkce uvnitř stejné aplikace function app.

Toto chování můžete změnit nastavením následující proměnné prostředí na celočíselnou hodnotu:

```
PSWorkerInProcConcurrencyUpperBound
```

Nastavte tuto proměnnou prostředí [nastavení aplikace](functions-app-settings.md) vaší aplikace Function App.

### <a name="considerations-for-using-concurrency"></a>Důležité informace týkající se použití souběžného zpracování

PowerShell je _jeden zřetězený_ skriptovací jazyk, ve výchozím nastavení. Souběžnost však lze přidat pomocí více prostředí runspace prostředí PowerShell v rámci stejného procesu. Tato funkce je, jak modul runtime Azure Functions PowerShell funguje.

Existují některé nevýhody tohoto přístupu.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Je jenom tak dobré jako počítač, na kterém je spuštěn na souběžnost

Pokud vaše aplikace function app běží na [plán služby App Service](functions-scale.md#app-service-plan) , který podporuje pouze jedním jádrem a většinu souběžnosti nepomůže. Důvodem je skutečnost, že neexistují žádné další jádra k vyrovnávají zatížení. V takovém případě výkon se může lišit při jedním jádrem musí přepnutí kontextu mezi prostředí runspace.

[Plánu Consumption](functions-scale.md#consumption-plan) spouští pomocí pouze jednoho jádra, nemůžou využívat souběžnosti. Pokud chcete využívat všech výhod souběžnosti, místo toho nasazení vašich funkcí na aplikaci funkcí spuštěnou v vyhrazený plán služby App Service s dostatečný počet jader.

#### <a name="azure-powershell-state"></a>Stav Azure Powershellu

Prostředí Azure PowerShell používá některé _úrovni procesu_ kontextů a stav šetřit z nadbytečné psát. Nicméně pokud zapnete souběžnosti v aplikaci function app a vyvolat akce, které mění stav, vám může dojít k jejich časování. Tyto konflikty časování je obtížné ladit, protože jeden volání spoléhá na a jejich určitého stavu a o vyvolání změnil stav.

Je tak mimořádnou hodnot souběžnosti ovládacím prvkem prostředí Azure PowerShell, protože některé operace může trvat poměrně dlouhou dobu. Však musí pokračovat opatrně. Pokud máte podezření, že se zobrazuje časování, nastavte souběžnost zpět `1` a akci opakujte.

## <a name="configure-function-scriptfile"></a>Konfigurace funkce `scriptFile`

Ve výchozím nastavení, prostředí PowerShell funkce se provede, z `run.ps1`, soubor, který sdílí stejné nadřazený adresář jako odpovídající `function.json`.

`scriptFile` Vlastnost `function.json` můžete použít k získání struktury složek, která vypadá jako v následujícím příkladu:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

V takovém případě `function.json` pro `myFunction` zahrnuje `scriptFile` vlastnost odkazuje na soubor s exportovanou funkci spustit.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Pomocí konfigurace objektu entryPoint modulů Powershellu

Tento článek obsahuje uvedené funkce Powershellu ve výchozím `run.ps1` vygenerované šablony souboru skriptu.
Ale můžete použít také funkce do modulů prostředí PowerShell. Můžete odkazovat pomocí kódu specifické funkce v modulu `scriptFile` a `entryPoint` polí function.json' konfigurační soubor.

V takovém případě `entryPoint` je název funkce nebo rutiny v modulu prostředí PowerShell odkazuje `scriptFile`.

Vezměte v úvahu následující strukturu složek:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Kde `PSFunction.psm1` obsahuje:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

V tomto příkladu konfiguraci `myFunction` zahrnuje `scriptFile` vlastnost, která odkazuje na `PSFunction.psm1`, což je modul prostředí PowerShell v jiné složce.  `entryPoint` Odkazy na vlastnosti `Invoke-PSTestFunc` funkce, která je vstupním bodem v modulu.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

S touto konfigurací `Invoke-PSTestFunc` přesně tak, jak se provede `run.ps1` by.

## <a name="considerations-for-powershell-functions"></a>Důležité informace týkající se funkcí Powershellu

Při práci s funkcemi prostředí PowerShell, mějte na paměti aspekty uvedené v následujících částech.

### <a name="cold-start"></a>Studený Start

Při vývoji v Azure Functions [bez serveru model hostingu](functions-scale.md#consumption-plan), studenou zprovoznění jsou realitou. *Studený start* odkazuje na dobu to trvá pro aplikace function app na spuštění ke zpracování požadavku. Úplné spuštění se stane více často spotřeby plánování, protože během období nečinnosti získá vypnout aplikaci function app.

### <a name="bundle-modules-instead-of-using-install-module"></a>Moduly sady místo použití `Install-Module`

Spuštění skriptu na každé vyvolání. Vyhněte se použití `Install-Module` ve skriptu. Místo toho použijte `Save-Module` před publikováním tak, že funkce nebude muset ztrácet čas stahování modulu. Pokud souvisejícím s úplným spuštěním mají vliv na vaše funkce, zvažte nasazení aplikace function app na [plán služby App Service](functions-scale.md#app-service-plan) nastavena na *AlwaysOn* nebo [plán Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)

[referenční materiály k Host.JSON]: functions-host-json.md
