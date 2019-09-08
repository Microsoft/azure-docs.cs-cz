---
title: Referenční příručka pro vývojáře PowerShellu pro Azure Functions
description: Naučte se vyvíjet funkce pomocí prostředí PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d24e798e73ef336324eedadee1ba3fec4c0e1d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773033"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions příručka pro vývojáře PowerShellu

Tento článek poskytuje podrobné informace o tom, jak píšete Azure Functions pomocí prostředí PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Funkce PowerShellu Azure (Function) je reprezentovaná jako PowerShellový skript, který se spustí, když se aktivuje. Každý skript funkce má související `function.json` soubor, který definuje, jak se funkce chová, jako je například jeho aktivace a vstupní a výstupní parametry. Další informace najdete v [článku triggery a vazby](functions-triggers-bindings.md). 

Podobně jako u jiných druhů funkcí mají funkce skriptu PowerShellu parametry, které odpovídají názvům všech vstupních vazeb definovaných v `function.json` souboru. Předává se i parametr, který obsahuje další informace o triggeru, který tuto funkci spustil. `TriggerMetadata`

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions referenci pro vývojáře](functions-reference.md). K vytvoření první funkce PowerShellu byste měli také dokončit [rychlé zprovoznění funkcí pro PowerShell](functions-create-first-function-powershell.md) .

## <a name="folder-structure"></a>Struktura složek

Požadovaná struktura složky pro projekt PowerShell vypadá následovně. Toto výchozí nastavení lze změnit. Další informace najdete v části [scriptFile](#configure-function-scriptfile) níže.

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

V kořenovém adresáři projektu je k dispozici sdílený [`host.json`](functions-host-json.md) soubor, který lze použít ke konfiguraci aplikace Function App. Každá funkce má složku se svým vlastním souborem kódu (. ps1) a konfiguračním souborem vazby (`function.json`). Název nadřazeného adresáře souboru Function. JSON je vždycky název vaší funkce.

Některé vazby vyžadují přítomnost `extensions.csproj` souboru. Rozšíření vazby požadovaná ve [verzi 2. x](functions-versions.md) modulu runtime Functions jsou definována v `extensions.csproj` souboru se skutečnými soubory `bin` knihoven ve složce. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

Ve funkcích aplikace PowerShell Functions můžete volitelně mít, `profile.ps1` která se spustí, když se spustí aplikace Function App (jinak ví, jak *[začít znovu spustit](#cold-start)* ). Další informace najdete v tématu [profil PowerShellu](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definování skriptu PowerShellu jako funkce

Ve výchozím nastavení vyhledává modul runtime Functions funkci v `run.ps1`, kde `run.ps1` sdílí stejný nadřazený adresář jako odpovídající `function.json`.

Vašemu skriptu je při provádění předán určitý počet argumentů. Chcete-li tyto parametry zpracovat, `param` přidejte do horní části skriptu blok, jako v následujícím příkladu:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametr TriggerMetadata

`TriggerMetadata` Parametr se používá k poskytnutí dalších informací o triggeru. Další metadata se liší od vazby k vazbě, ale všechny obsahují `sys` vlastnost, která obsahuje následující data:

```powershell
$TriggerMetadata.sys
```

| Vlastnost   | Description                                     | type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Když v UTC, byla funkce aktivována.        | DateTime |
| MethodName | Název aktivované funkce     | řetězec   |
| RandGuid   | Jedinečný identifikátor GUID tohoto spuštění funkce | řetězec   |

Každý typ triggeru má jinou sadu metadat. Například `$TriggerMetadata` pro `QueueTrigger` obsahuje ,`InsertionTime` ,`DequeueCount`,mimo jiné. `Id` Další informace o metadatech triggeru fronty najdete v [oficiální dokumentaci k aktivačním událostem fronty](functions-bindings-storage-queue.md#trigger---message-metadata). V dokumentaci k [aktivačním událostem](functions-triggers-bindings.md) , se kterými pracujete, můžete zjistit, co se nachází uvnitř metadat triggeru.

## <a name="bindings"></a>Vazby

V prostředí PowerShell jsou [vazby](functions-triggers-bindings.md) konfigurovány a definovány ve funkci Function. JSON. Funkce pracují s vazbami různými způsoby.

### <a name="reading-trigger-and-input-data"></a>Čtení triggerů a vstupních dat

Triggery a vstupní vazby jsou čteny jako parametry předané do vaší funkce. Vstupní vazby mají `direction` `in` v Function. JSON nastavenou hodnotu. Vlastnost definovaná v `function.json` je název parametru v `param` bloku. `name` Vzhledem k tomu, že prostředí PowerShell používá pro vazbu pojmenované parametry, pořadí parametrů nezáleží. Osvědčeným postupem je však postupovat podle pořadí vazeb definovaných v `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zápis výstupních dat

Ve funkcích má `direction` výstupní vazba `out` nastavenou hodnotu ve funkci Function. JSON. Do výstupní vazby můžete zapisovat pomocí `Push-OutputBinding` rutiny, která je k dispozici pro modul runtime Functions. Ve všech případech `name` vlastnost vazby, jak je definováno v `function.json` , odpovídá `Name` parametru `Push-OutputBinding` rutiny.

Následující příklad ukazuje, jak zavolat `Push-OutputBinding` do skriptu funkce:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Můžete také předat hodnotu konkrétní vazby prostřednictvím kanálu.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`se chová odlišně na základě hodnoty zadané pro `-Name`:

* Pokud zadaný název nelze přeložit na platnou výstupní vazbu, je vyvolána chyba.

* Když výstupní vazba akceptuje kolekci hodnot, můžete opakovaně volat `Push-OutputBinding` , aby bylo možné vložit více hodnot.

* Pokud výstupní vazba akceptuje jenom hodnotu typu Singleton, vyvolá `Push-OutputBinding` se chyba při druhém volání.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`syntaktick

Níže jsou uvedené platné parametry pro volání `Push-OutputBinding`:

| Name | type | Pozice | Popis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Řetězec | 1 | Název výstupní vazby, kterou chcete nastavit. |
| **`-Value`** | Object | 2 | Hodnota výstupní vazby, kterou chcete nastavit, která je přijímána z ByValue kanálu. |
| **`-Clobber`** | Přepínací parametr | Jmenovanou | Volitelné Když se tato hodnota zadá, vynutí nastavení hodnoty pro zadanou výstupní vazbu. | 

Podporovány jsou i tyto společné parametry: 
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

#### <a name="push-outputbinding-example-http-responses"></a>Příklad push-OutputBinding: Odpovědi HTTP

Aktivační událost protokolu HTTP vrátí odpověď pomocí výstupní vazby s názvem `response`. V následujícím příkladu `response` má výstupní vazba hodnotu "výstupní #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Vzhledem k tomu, že výstup je http, který přijímá pouze hodnotu singleton, je vyvolána chyba, `Push-OutputBinding` Pokud je volána podruhé.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

U výstupů, které přijímají pouze hodnoty typu Singleton, `-Clobber` lze použít parametr k přepsání staré hodnoty místo pokusu o přidání do kolekce. Následující příklad předpokládá, že jste již přidali hodnotu. Pomocí příkazu `-Clobber`, odpověď z následujícího příkladu přepíše existující hodnotu a vrátí hodnotu "Output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Příklad push-OutputBinding: Výstupní vazba fronty

`Push-OutputBinding`slouží k odesílání dat do výstupních vazeb, jako je například [Výstupní vazba Azure Queue Storage](functions-bindings-storage-queue.md#output). V následujícím příkladu má zpráva zapsaná do fronty hodnotu "výstupní #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Výstupní vazba pro frontu úložiště přijímá více výstupních hodnot. V tomto případě volání následujícího příkladu po prvním zápisu do fronty seznam se dvěma položkami: "výstupní #1" a "výstupní #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Následující příklad, který je volán za předchozích dvou, přidá do výstupní kolekce dvě další hodnoty:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Při zápisu do fronty obsahuje zpráva tyto čtyři hodnoty: "výstupní #1", "výstupní #2", "výstupní #3" a "výstupní #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`rutiny

Pomocí `Get-OutputBinding` rutiny můžete načíst hodnoty, které jsou aktuálně nastaveny pro vaše výstupní vazby. Tato rutina načte zatřiďovací tabulku, která obsahuje názvy výstupních vazeb s příslušnými hodnotami. 

Následuje příklad použití `Get-OutputBinding` pro vrácení aktuálních hodnot vazby:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`obsahuje také parametr s názvem `-Name`, který lze použít k filtrování vrácené vazby, jako v následujícím příkladu:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Zástupné znaky (*) jsou `Get-OutputBinding`podporovány v.

## <a name="logging"></a>Protokolování

Protokolování funkcí prostředí PowerShell funguje jako běžné protokolování do PowerShellu. K zápisu do každého výstupního datového proudu můžete použít rutiny protokolování. Každá rutina se mapuje na úroveň protokolu využívané funkcemi.

| Úroveň protokolování funkcí | Rutina protokolování |
| ------------- | -------------- |
| Chyba | **`Write-Error`** |
| Upozornění | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Zapisuje do protokolování na úrovni _informací_ . |
| Ladění | **`Write-Debug`** |
| Trasování | **`Write-Progress`** <br /> **`Write-Verbose`** |

Kromě těchto rutin se vše zapsané do kanálu přesměruje na `Information` úroveň protokolu a zobrazí se s výchozím formátováním PowerShellu.

> [!IMPORTANT]
> Použití rutiny `Write-Debug`nebonení dostatečné pro zobrazení protokolování na úrovni podrobností a ladění. `Write-Verbose` Musíte taky nakonfigurovat prahovou hodnotu úrovně protokolu, která deklaruje, na jakou úroveň protokolů se vám ve skutečnosti záleží. Další informace najdete v tématu [Konfigurace úrovně protokolu aplikace Function App](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurace úrovně protokolu aplikace Function App

Azure Functions umožňuje definovat úroveň prahové hodnoty, která usnadňuje kontrolu způsobu, jakým funkce zapisuje do protokolů. Chcete-li nastavit prahovou hodnotu pro všechna trasování zapsaná do konzoly `logging.logLevel.default` , použijte vlastnost [ `host.json` ]v[odkazu soubor host. JSON]. Toto nastavení platí pro všechny funkce aplikace Function App.

Následující příklad nastaví prahovou hodnotu pro povolení podrobného protokolování pro všechny funkce, ale nastaví prahovou hodnotu pro povolení protokolování ladění pro funkci s `MyFunction`názvem:

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

Další informace naleznete v tématu [reference Host. JSON].

### <a name="viewing-the-logs"></a>Zobrazení protokolů

Pokud váš Function App běží v Azure, můžete ho monitorovat pomocí Application Insights. Přečtěte si [Azure Functions monitorování](functions-monitoring.md) , kde najdete další informace o zobrazení a dotazování protokolů funkcí.

Pokud používáte Function App místně pro vývoj, protokoluje výchozí systém souborů. Chcete-li zobrazit protokoly v konzole nástroje, nastavte `AZURE_FUNCTIONS_ENVIRONMENT` `Development` před spuštěním Function App proměnnou prostředí.

## <a name="triggers-and-bindings-types"></a>Triggery a typy vazeb

K dispozici je řada triggerů a vazeb pro použití s aplikací Function App. Úplný seznam aktivačních událostí a vazeb najdete [tady](functions-triggers-bindings.md#supported-bindings).

Všechny triggery a vazby jsou reprezentovány v kódu jako několik reálných datových typů:

* Hashtable
* řetězec
* Byte
* int
* double
* HttpRequestContext
* HttpResponseContext

Prvních pět typů v tomto seznamu je standardní typy .NET. Poslední dva jsou používány pouze [triggerem HttpTrigger](#http-triggers-and-bindings).

Každý parametr vazby ve vašich funkcích musí být jedním z těchto typů.

### <a name="http-triggers-and-bindings"></a>Aktivační události a vazby HTTP

Aktivační události HTTP a Webhooku a výstupní vazby HTTP používají objekty žádosti a odpovědi, které reprezentují zprávy HTTP.

#### <a name="request-object"></a>Request – objekt

Objekt Request, který je předán do skriptu, je typu `HttpRequestContext`, který má následující vlastnosti:

| Vlastnost  | Description                                                    | type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Objekt, který obsahuje tělo žádosti. `Body`je serializována do nejlepšího typu na základě dat. Například pokud jsou data JSON, předává se jako zatřiďovací tabulka. Pokud jsou data řetězcem, je předáno jako řetězec. | object |
| **`Headers`** | Slovník, který obsahuje hlavičky požadavku.                | Řetězec < slovníku, řetězec ><sup>*</sup> |
| **`Method`** | Metoda HTTP požadavku.                                | řetězec                    |
| **`Params`**  | Objekt, který obsahuje parametry směrování požadavku. | Řetězec < slovníku, řetězec ><sup>*</sup> |
| **`Query`** | Objekt, který obsahuje parametry dotazu.                  | Řetězec < slovníku, řetězec ><sup>*</sup> |
| **`Url`** | Adresa URL požadavku.                                        | řetězec                    |

<sup>*</sup>U `Dictionary<string,string>` všech klíčů se nerozlišují velká a malá písmena.

#### <a name="response-object"></a>Objekt odpovědi

Objekt Response, který byste měli poslat zpátky, je typu `HttpResponseContext`, který má následující vlastnosti:

| Vlastnost      | Description                                                 | type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Objekt, který obsahuje tělo odpovědi.           | object                    |
| **`ContentType`** | Krátká ruka pro nastavení typu obsahu pro odpověď. | řetězec                    |
| **`Headers`** | Objekt, který obsahuje hlavičky odpovědi.               | Slovník nebo zatřiďovací tabulka   |
| **`StatusCode`**  | Stavový kód protokolu HTTP odpovědi.                       | řetězec nebo int             |

#### <a name="accessing-the-request-and-response"></a>Přístup k žádosti a odpovědi

Když pracujete s triggery HTTP, můžete získat přístup k požadavku HTTP stejným způsobem jako u jakékoli jiné vstupní vazby. Je v `param` bloku.

`HttpResponseContext` Pomocí objektu můžete vrátit odpověď, jak je znázorněno v následujícím příkladu:

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

Výsledek vyvolání této funkce by byl:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Přetypování typů pro triggery a vazby

Pro určité vazby, jako je vazba objektů blob, můžete zadat typ parametru.

Pokud například chcete, aby data z úložiště objektů BLOB byla dodávána jako řetězec, přidejte do svého `param` bloku následující přetypování:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil PowerShellu

V prostředí PowerShell existuje koncept profilu PowerShellu. Pokud nejste obeznámeni s profily PowerShellu, přečtěte si téma [o profilech](/powershell/module/microsoft.powershell.core/about/about_profiles).

Ve funkcích PowerShellu se skript profilu spustí při spuštění aplikace Function App. Aplikace Function App se spustí při prvním nasazení a po nečinnosti (při[studeném startu](#cold-start)).

Když vytvoříte aplikaci funkcí pomocí nástrojů, jako je například Visual Studio Code a Azure Functions Core Tools, vytvoří se pro `profile.ps1` vás výchozí hodnota. Výchozí profil se udržuje [v úložišti GitHub Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) a obsahuje:

* Automatické ověřování MSI do Azure
* Možnost zapnout aliasy Azure PowerShell `AzureRM` PowerShellu, pokud byste chtěli.

## <a name="powershell-version"></a>Verze prostředí PowerShell

Následující tabulka ukazuje verzi prostředí PowerShell, kterou používá každá hlavní verze běhového modulu Functions:

| Verze funkcí | Verze prostředí PowerShell                             |
|-------------------|------------------------------------------------|
| verze               | Windows PowerShell 5,1 (uzamčeno modulem runtime) |
| 2.x               | PowerShell Core 6                              |

Aktuální verzi můžete zobrazit pomocí tisku `$PSVersionTable` z libovolné funkce.

## <a name="dependency-management"></a>Správa závislostí

Funkce PowerShellu podporují stažení a správu modulů [Galerie prostředí PowerShell](https://www.powershellgallery.com) prostřednictvím služby. Úpravou souboru Host. JSON a nastavením vlastnosti managedDependency Enabled na hodnotu true se zpracuje soubor požadavky. psd1. Zadané moduly budou automaticky staženy a zpřístupněny této funkci. 

Maximální počet modulů, které jsou aktuálně podporovány, je 10. Podporovanou syntaxí je MajorNumber. * nebo přesná verze modulu, jak je znázorněno níže. Když se vytvoří nová aplikace funkce PowerShellu, ve výchozím nastavení se zahrnou modul Azure AZ.

Modul Language Worker při restartování vybere všechny aktualizované moduly.

host.json
```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

požadavky. psd1

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Využití vlastních modulů je trochu jiné, než jak byste to prostupovali normálně.

Když modul nainstalujete do místního počítače, přejde do jedné z globálních dostupných složek v `$env:PSModulePath`. Vzhledem k tomu, že vaše funkce běží v Azure, nebudete mít přístup k modulům nainstalovaným na vašem počítači. To vyžaduje, aby `$env:PSModulePath` se aplikace funkce PowerShellu odlišná `$env:PSModulePath` na běžném skriptu PowerShellu.

Ve funkcích `PSModulePath` obsahuje dvě cesty:

* `Modules` Složka, která existuje v kořenu Function App.
* Cesta ke `Modules` složce, která je umístěná v pracovním procesu PowerShellu jazyka.

### <a name="function-app-level-modules-folder"></a>Složka na úrovni `Modules` aplikace Function App

Chcete-li použít vlastní moduly, můžete umístit moduly, na kterých jsou vaše funkce `Modules` závislé ve složce. Z této složky jsou moduly automaticky dostupné pro modul runtime Functions. Všechny funkce ve Function App můžou tyto moduly používat. 

> [!NOTE]
> Moduly zadané v souboru požadavků. psd1 se automaticky stáhnou a zahrnou do cesty, takže je nemusíte vkládat do složky moduly. Ty jsou uložené lokálně ve složce $env: LOCALAPPDATA/AzureFunctions a ve složce/data/ManagedDependencies při spuštění v cloudu.

Pokud chcete využít funkci vlastního modulu, vytvořte `Modules` složku v kořenovém adresáři aplikace Function App. Do tohoto umístění zkopírujte moduly, které chcete použít ve svých funkcích.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Ve složce modulů by vaše aplikace Function App měla mít následující strukturu složek:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Když spustíte aplikaci Function App, pracovní proces PowerShellu přidá tuto `Modules` složku `$env:PSModulePath` na, takže se můžete spoléhat na automatické načítání modulu stejně jako v běžném skriptu PowerShellu.

### <a name="language-worker-level-modules-folder"></a>Složka na úrovni `Modules` jazykových pracovních procesů

PowerShell Language Worker obvykle používá několik modulů. Tyto moduly jsou definovány v poslední pozici `PSModulePath`. 

Aktuální seznam modulů je následující:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modul používaný pro práci s archivy, `.zip`jako `.nupkg`je, a další.
* **ThreadJob**: Implementace rozhraní API úlohy PowerShellu založené na vláknech.

Nejnovější verzi těchto modulů používají funkce. Pokud chcete použít konkrétní verzi těchto modulů, můžete zadat konkrétní verzi ve `Modules` složce aplikace Function App.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí `$env:NAME_OF_ENV_VAR`, jak je znázorněno v následujícím příkladu:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění se nastavení aplikace čtou ze souboru [Local. Settings. JSON](functions-run-local.md#local-settings-file) projektu.

## <a name="concurrency"></a>Souběžnost

Ve výchozím nastavení může běhový modul PowerShellu funkcí zpracovat pouze jedno vyvolání funkce. Tato úroveň souběžnosti ale nemusí být dostatečná v následujících situacích:

* Při pokusu o zpracování velkého počtu vyvolání současně.
* Pokud máte funkce, které vyvolávají jiné funkce v rámci stejné aplikace Function App.

Toto chování můžete změnit nastavením následující proměnné prostředí na celočíselnou hodnotu:

```
PSWorkerInProcConcurrencyUpperBound
```

Tuto proměnnou prostředí nastavíte v [nastavení aplikace](functions-app-settings.md) Function App.

### <a name="considerations-for-using-concurrency"></a>Předpoklady pro použití souběžnosti

PowerShell je ve výchozím nastavení jediným skriptovacím jazykem s _více vlákny_ . Souběžnost se však dá přidat pomocí několika prostředí runspace prostředí PowerShell v jednom procesu. Vytvořené množství prostředí runspace se bude shodovat s nastavením aplikace PSWorkerInProcConcurrencyUpperBound. Propustnost bude mít vliv na množství CPU a paměti, které jsou k dispozici ve vybraném plánu.

Azure PowerShell používá některé kontexty _na úrovni procesu_ a stav, které vám pomůžou ušetřit nadměrné typování. Pokud však zapnete souběžnost ve vaší aplikaci Function App a vyvoláte akce, které mění stav, můžete se zaměřit na konflikty časování. Tyto konflikty časování je obtížné ladit, protože jedno vyvolání spoléhá na určitý stav a druhé vyvolání změnilo stav.

Obrovské je hodnota v souběžnosti s Azure PowerShell, protože některé operace mohou trvat značnou dobu. Je však třeba postupovat opatrně. Pokud máte podezření, že jste se setkali s časováním, nastavte nastavení aplikace PSWorkerInProcConcurrencyUpperBound `1` na a místo toho použijte [izolaci úrovně pracovního procesu jazyka](functions-app-settings.md#functions_worker_process_count) pro souběžnost.

## <a name="configure-function-scriptfile"></a>Konfigurovat funkci`scriptFile`

Ve výchozím nastavení je funkce PowerShellu spouštěna `run.ps1`z, soubor, který sdílí stejný nadřazený adresář jako odpovídající. `function.json`

Vlastnost v rámci `function.json` lze použít k získání struktury složky, která vypadá jako v následujícím příkladu: `scriptFile`

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

V tomto případě zahrnuje `function.json` `scriptFile` vlastnost for `myFunction` , která odkazuje na soubor s exportovanou funkcí, která má být spuštěna.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Použití modulů PowerShellu konfigurací vstupního bodu

V tomto článku se zobrazily funkce PowerShellu `run.ps1` ve výchozím souboru skriptu generovaném šablonami.
Do modulů PowerShellu ale můžete také zahrnout své funkce. Můžete odkazovat na konkrétní kód funkce v modulu pomocí `scriptFile` polí a `entryPoint` v konfiguračním souboru Function. JSON.

V tomto případě je `entryPoint` název funkce nebo rutiny v modulu PowerShellu, na který se odkazuje `scriptFile`v.

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

V tomto příkladu konfigurace pro `myFunction` `scriptFile` zahrnuje vlastnost, která odkazuje `PSFunction.psm1`na, což je modul prostředí PowerShell v jiné složce.  Vlastnost odkazuje na `Invoke-PSTestFunc` funkci, která je vstupním bodem v modulu. `entryPoint`

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

V této konfiguraci bude `Invoke-PSTestFunc` provedeno přesně tak, `run.ps1` jako by bylo.

## <a name="considerations-for-powershell-functions"></a>Předpoklady pro funkce PowerShellu

Při práci s funkcemi PowerShellu si pamatujte na informace v následujících částech.

### <a name="cold-start"></a>Studený start

Při vývoji Azure Functions v [modelu hostování bez serveru](functions-scale.md#consumption-plan)je to realita. *Studená Start* odkazuje na dobu, kterou aplikace Function App spustí pro zpracování žádosti. K studenému startu dochází častěji v plánu spotřeby, protože aplikace Function App se během období nečinnosti ukončí.

### <a name="bundle-modules-instead-of-using-install-module"></a>Místo použití použít modul sady`Install-Module`

Váš skript se spustí při každém vyvolání. Vyhněte `Install-Module` se použití ve vašem skriptu. Místo toho `Save-Module` použijte před publikováním, aby vaše funkce nemusela ztrácet čas stažením modulu. Pokud mají tyto funkce vliv na studená spuštění, zvažte nasazení aplikace Function App do [plánu App Service](functions-scale.md#app-service-plan) nastaveného na hodnotu *Always On* nebo [Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Další postup

Další informace naleznete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)

[Reference Host. JSON]: functions-host-json.md
