---
title: Referenční příručka pro vývojáře PowerShellu pro Azure Functions
description: Naučte se vyvíjet funkce pomocí prostředí PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 61ed3ed274505101c65e251260bd759fe78f7b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936783"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions příručka pro vývojáře PowerShellu

Tento článek poskytuje podrobné informace o tom, jak píšete Azure Functions pomocí prostředí PowerShell.

Funkce PowerShellu Azure (Function) je reprezentovaná jako PowerShellový skript, který se spustí, když se aktivuje. Každý skript funkce má související `function.json` soubor, který definuje, jak se funkce chová, jako je například jeho aktivace a vstupní a výstupní parametry. Další informace najdete v [článku triggery a vazby](functions-triggers-bindings.md). 

Podobně jako u jiných druhů funkcí mají funkce skriptu PowerShellu parametry, které odpovídají názvům všech vstupních vazeb definovaných v `function.json` souboru. `TriggerMetadata`Předává se i parametr, který obsahuje další informace o triggeru, který tuto funkci spustil.

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions referenci pro vývojáře](functions-reference.md). K vytvoření první funkce PowerShellu byste měli také dokončit [rychlé zprovoznění funkcí pro PowerShell](./create-first-function-vs-code-powershell.md) .

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

V kořenovém adresáři projektu je k dispozici sdílený [`host.json`](functions-host-json.md) soubor, který lze použít ke konfiguraci aplikace Function App. Každá funkce má složku se svým vlastním souborem kódu (. ps1) a konfiguračním souborem vazby ( `function.json` ). Název function.jsv nadřazeném adresáři souboru je vždycky název vaší funkce.

Některé vazby vyžadují přítomnost `extensions.csproj` souboru. Rozšíření vazby, které vyžaduje [verze 2. x a novější verze](functions-versions.md) modulu runtime Functions, jsou v souboru definovány se `extensions.csproj` skutečnými soubory knihoven ve `bin` složce. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

Ve funkcích aplikace PowerShell Functions můžete volitelně mít, `profile.ps1` která se spustí, když se spustí aplikace Function App (jinak ví, jak *[začít znovu spustit](#cold-start)*). Další informace najdete v tématu [profil PowerShellu](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definování skriptu PowerShellu jako funkce

Ve výchozím nastavení vyhledává modul runtime Functions funkci v `run.ps1` , kde `run.ps1` sdílí stejný nadřazený adresář jako odpovídající `function.json` .

Vašemu skriptu je při provádění předán určitý počet argumentů. Chcete-li tyto parametry zpracovat, přidejte do `param` horní části skriptu blok, jako v následujícím příkladu:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametr TriggerMetadata

`TriggerMetadata`Parametr se používá k poskytnutí dalších informací o triggeru. Další metadata se liší od vazby k vazbě, ale všechny obsahují `sys` vlastnost, která obsahuje následující data:

```powershell
$TriggerMetadata.sys
```

| Vlastnost   | Popis                                     | Typ     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Když v UTC, byla funkce aktivována.        | DateTime |
| MethodName | Název aktivované funkce     | řetězec   |
| RandGuid   | Jedinečný identifikátor GUID tohoto spuštění funkce | řetězec   |

Každý typ triggeru má jinou sadu metadat. Například `$TriggerMetadata` pro `QueueTrigger` obsahuje `InsertionTime` ,, `Id` `DequeueCount` , mimo jiné. Další informace o metadatech triggeru fronty najdete v [oficiální dokumentaci k aktivačním událostem fronty](functions-bindings-storage-queue-trigger.md#message-metadata). V dokumentaci k [aktivačním událostem](functions-triggers-bindings.md) , se kterými pracujete, můžete zjistit, co se nachází uvnitř metadat triggeru.

## <a name="bindings"></a>Vazby

V prostředí PowerShell jsou [vazby](functions-triggers-bindings.md) konfigurovány a definovány ve function.jsfunkce na. Funkce pracují s vazbami různými způsoby.

### <a name="reading-trigger-and-input-data"></a>Čtení triggerů a vstupních dat

Triggery a vstupní vazby jsou čteny jako parametry předané do vaší funkce. Vstupní vazby mají `direction` `in` v function.jszapnutou hodnotu. `name`Vlastnost definovaná v `function.json` je název parametru v `param` bloku. Vzhledem k tomu, že prostředí PowerShell používá pro vazbu pojmenované parametry, pořadí parametrů nezáleží. Osvědčeným postupem je však postupovat podle pořadí vazeb definovaných v `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zápis výstupních dat

Ve funkcích má výstupní vazba `direction` nastavenou na `out` v function.js. Do výstupní vazby můžete zapisovat pomocí `Push-OutputBinding` rutiny, která je k dispozici pro modul runtime Functions. Ve všech případech `name` vlastnost vazby, jak je definováno v, `function.json` odpovídá `Name` parametru `Push-OutputBinding` rutiny.

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

`Push-OutputBinding` se chová odlišně na základě hodnoty zadané pro `-Name` :

* Pokud zadaný název nelze přeložit na platnou výstupní vazbu, je vyvolána chyba.

* Když výstupní vazba akceptuje kolekci hodnot, můžete opakovaně volat, aby bylo možné `Push-OutputBinding` Vložit více hodnot.

* Pokud výstupní vazba akceptuje jenom hodnotu typu Singleton, `Push-OutputBinding` vyvolá se chyba při druhém volání.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` syntaktick

Níže jsou uvedené platné parametry pro volání `Push-OutputBinding` :

| Název | Typ | Pozice | Description |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Řetězec | 1 | Název výstupní vazby, kterou chcete nastavit. |
| **`-Value`** | Objekt | 2 | Hodnota výstupní vazby, kterou chcete nastavit, která je přijímána z ByValue kanálu. |
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

Další informace najdete v tématu [o CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

#### <a name="push-outputbinding-example-http-responses"></a>Příklad Push-OutputBinding: odpovědi HTTP

Aktivační událost protokolu HTTP vrátí odpověď pomocí výstupní vazby s názvem `response` . V následujícím příkladu má výstupní vazba `response` hodnotu "výstupní #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Vzhledem k tomu, že výstup je HTTP, který přijímá pouze hodnotu singleton, je vyvolána chyba, pokud `Push-OutputBinding` je volána podruhé.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

U výstupů, které přijímají pouze hodnoty typu Singleton, lze použít `-Clobber` parametr k přepsání staré hodnoty místo pokusu o přidání do kolekce. Následující příklad předpokládá, že jste již přidali hodnotu. Pomocí příkazu `-Clobber` , odpověď z následujícího příkladu přepíše existující hodnotu a vrátí hodnotu "output #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Příklad Push-OutputBinding: Queue Output Binding

`Push-OutputBinding` slouží k odesílání dat do výstupních vazeb, jako je například [Výstupní vazba Azure Queue Storage](functions-bindings-storage-queue-output.md). V následujícím příkladu má zpráva zapsaná do fronty hodnotu "výstupní #1":

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

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` rutiny

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

`Get-OutputBinding` obsahuje také parametr s názvem `-Name` , který lze použít k filtrování vrácené vazby, jako v následujícím příkladu:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Zástupné znaky (*) jsou podporovány v `Get-OutputBinding` .

## <a name="logging"></a>protokolování

Protokolování funkcí prostředí PowerShell funguje jako běžné protokolování do PowerShellu. K zápisu do každého výstupního datového proudu můžete použít rutiny protokolování. Každá rutina se mapuje na úroveň protokolu využívané funkcemi.

| Úroveň protokolování funkcí | Rutina protokolování |
| ------------- | -------------- |
| Chybová | **`Write-Error`** |
| Upozornění | **`Write-Warning`**  | 
| Informační | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informační | Zapisuje do protokolování na úrovni _informací_ . |
| Ladění | **`Write-Debug`** |
| Trasování | **`Write-Progress`** <br /> **`Write-Verbose`** |

Kromě těchto rutin se vše zapsané do kanálu přesměruje na `Information` úroveň protokolu a zobrazí se s výchozím formátováním PowerShellu.

> [!IMPORTANT]
> Použití `Write-Verbose` `Write-Debug` rutiny nebo není dostatečné pro zobrazení protokolování na úrovni podrobností a ladění. Musíte taky nakonfigurovat prahovou hodnotu úrovně protokolu, která deklaruje, na jakou úroveň protokolů se vám ve skutečnosti záleží. Další informace najdete v tématu [Konfigurace úrovně protokolu aplikace Function App](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurace úrovně protokolu aplikace Function App

Azure Functions umožňuje definovat úroveň prahové hodnoty, která usnadňuje kontrolu způsobu, jakým funkce zapisuje do protokolů. Chcete-li nastavit prahovou hodnotu pro všechna trasování zapsaná do konzoly, použijte `logging.logLevel.default` vlastnost v [ `host.json` souboru] [host.jsv referenci]. Toto nastavení platí pro všechny funkce aplikace Function App.

Následující příklad nastaví prahovou hodnotu pro povolení podrobného protokolování pro všechny funkce, ale nastaví prahovou hodnotu pro povolení protokolování ladění pro funkci s názvem `MyFunction` :

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

Další informace najdete v tématu [host.jsv referenci].

### <a name="viewing-the-logs"></a>Zobrazení protokolů

Pokud váš Function App běží v Azure, můžete ho monitorovat pomocí Application Insights. Přečtěte si [Azure Functions monitorování](functions-monitoring.md) , kde najdete další informace o zobrazení a dotazování protokolů funkcí.

Pokud používáte Function App místně pro vývoj, protokoluje výchozí systém souborů. Chcete-li zobrazit protokoly v konzole nástroje, nastavte `AZURE_FUNCTIONS_ENVIRONMENT` `Development` před spuštěním Function App proměnnou prostředí.

## <a name="triggers-and-bindings-types"></a>Triggery a typy vazeb

K dispozici je řada triggerů a vazeb pro použití s aplikací Function App. Úplný seznam aktivačních událostí a vazeb najdete [tady](functions-triggers-bindings.md#supported-bindings).

Všechny triggery a vazby jsou reprezentovány v kódu jako několik reálných datových typů:

* Hashtable
* řetězec
* Byte []
* int
* double
* HttpRequestContext
* HttpResponseContext

Prvních pět typů v tomto seznamu je standardní typy .NET. Poslední dva jsou používány pouze [triggerem HttpTrigger](#http-triggers-and-bindings).

Každý parametr vazby ve vašich funkcích musí být jedním z těchto typů.

### <a name="http-triggers-and-bindings"></a>Aktivační události a vazby HTTP

Aktivační události HTTP a Webhooku a výstupní vazby HTTP používají objekty žádosti a odpovědi, které reprezentují zprávy HTTP.

#### <a name="request-object"></a>Request – objekt

Objekt Request, který je předán do skriptu, je typu `HttpRequestContext` , který má následující vlastnosti:

| Vlastnost  | Popis                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Objekt, který obsahuje tělo žádosti. `Body` je serializována do nejlepšího typu na základě dat. Například pokud jsou data JSON, předává se jako zatřiďovací tabulka. Pokud jsou data řetězcem, je předáno jako řetězec. | object |
| **`Headers`** | Slovník, který obsahuje hlavičky požadavku.                | Řetězec<slovníku, řetězec><sup>*</sup> |
| **`Method`** | Metoda HTTP požadavku.                                | řetězec                    |
| **`Params`**  | Objekt, který obsahuje parametry směrování požadavku. | Řetězec<slovníku, řetězec><sup>*</sup> |
| **`Query`** | Objekt, který obsahuje parametry dotazu.                  | Řetězec<slovníku, řetězec><sup>*</sup> |
| **`Url`** | Adresa URL požadavku                                        | řetězec                    |

<sup>*</sup>`Dictionary<string,string>`U všech klíčů se nerozlišují velká a malá písmena.

#### <a name="response-object"></a>Objekt odpovědi

Objekt Response, který byste měli poslat zpátky, je typu `HttpResponseContext` , který má následující vlastnosti:

| Vlastnost      | Popis                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Objekt, který obsahuje tělo odpovědi.           | object                    |
| **`ContentType`** | Krátká ruka pro nastavení typu obsahu pro odpověď. | řetězec                    |
| **`Headers`** | Objekt, který obsahuje hlavičky odpovědi.               | Slovník nebo zatřiďovací tabulka   |
| **`StatusCode`**  | Stavový kód protokolu HTTP odpovědi.                       | řetězec nebo int             |

#### <a name="accessing-the-request-and-response"></a>Přístup k žádosti a odpovědi

Když pracujete s triggery HTTP, můžete získat přístup k požadavku HTTP stejným způsobem jako u jakékoli jiné vstupní vazby. Je v `param` bloku.

Pomocí `HttpResponseContext` objektu můžete vrátit odpověď, jak je znázorněno v následujícím příkladu:

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

Pokud například chcete, aby data z úložiště objektů BLOB byla dodávána jako řetězec, přidejte do svého bloku následující přetypování `param` :

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil PowerShellu

V prostředí PowerShell existuje koncept profilu PowerShellu. Pokud nejste obeznámeni s profily PowerShellu, přečtěte si téma [o profilech](/powershell/module/microsoft.powershell.core/about/about_profiles).

Ve funkcích PowerShellu se skript profilu spustí jednou za instanci pracovního procesu PowerShellu v aplikaci při prvním nasazení a po nečinnosti ([spuštění](#cold-start)po ukončení). Pokud je souběžnost povolená nastavením hodnoty [PSWorkerInProcConcurrencyUpperBound](#concurrency) , skript profilu se spustí pro každé vytvořené prostředí runspace.

Když vytvoříte aplikaci funkcí pomocí nástrojů, jako je například Visual Studio Code a Azure Functions Core Tools, vytvoří `profile.ps1` se pro vás výchozí hodnota. Výchozí profil se udržuje [v úložišti GitHub Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) a obsahuje:

* Automatické ověřování MSI do Azure
* Možnost zapnout `AzureRM` aliasy Azure PowerShell PowerShellu, pokud byste chtěli.

## <a name="powershell-versions"></a>Verze PowerShellu

V následující tabulce jsou uvedeny verze prostředí PowerShell, které jsou k dispozici pro každou hlavní verzi modulu runtime functions a požadovaná verze rozhraní .NET:

| Verze funkcí | Verze Powershellu                               | Verze .NET  | 
|-------------------|--------------------------------------------------|---------------|
| 3. x (doporučeno) | PowerShell 7 (doporučeno)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

Aktuální verzi můžete zobrazit pomocí tisku `$PSVersionTable` z libovolné funkce.

### <a name="running-local-on-a-specific-version"></a>Místní spuštění na konkrétní verzi

Při místním spuštění Azure Functions modul runtime standardně používá PowerShell Core 6. Chcete-li místo toho použít prostředí PowerShell 7 při místním spuštění, je nutné přidat nastavení `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` do `Values` pole v local.setting.jssouboru v kořenovém adresáři projektu. Při místním spuštění v prostředí PowerShell 7 vaše local.settings.jsv souboru vypadá jako v následujícím příkladu: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>Změna verze prostředí PowerShell

Vaše aplikace Function App musí běžet na verzi 3. x, aby bylo možné upgradovat z PowerShellu Core 6 na PowerShell 7. Další informace o tom, jak to provést, najdete v tématu [zobrazení a aktualizace aktuální verze modulu runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

Pomocí následujících kroků můžete změnit verzi prostředí PowerShell, kterou používá aplikace Function App. To můžete provést buď v Azure Portal, nebo pomocí prostředí PowerShell.

# <a name="portal"></a>[Azure Portal](#tab/portal)

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function App.

1. V části **Nastavení** vyberte **Konfigurace**. Na kartě **Obecné nastavení** vyhledejte **verzi prostředí PowerShell**. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="Zvolit verzi prostředí PowerShell, kterou používá aplikace Function App"::: 

1. Zvolte požadovanou **verzi PowerShell Core** a vyberte **Uložit**. Pokud se zobrazí upozornění na nedokončené restartování, vyberte **pokračovat**. Aplikace Function App se restartuje ve zvolené verzi prostředí PowerShell. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Spuštěním následujícího skriptu změňte verzi prostředí PowerShell: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

Nahraďte `<SUBSCRIPTION_ID>` , `<RESOURCE_GROUP>` a `<FUNCTION_APP>` číslem ID vašeho předplatného Azure, název vaší skupiny prostředků a aplikace Function App (v uvedeném pořadí).  Nahraďte také `<VERSION>` buď `~6` nebo `~7` . Můžete ověřit aktualizovanou hodnotu `powerShellVersion` nastavení v `Properties` vrácené zatřiďovací tabulce. 

---

Aplikace Function App se restartuje po provedení změny v konfiguraci.

## <a name="dependency-management"></a>Správa závislostí

Funkce umožňují využít [galerii prostředí PowerShell](https://www.powershellgallery.com) pro správu závislostí. Se zapnutou správou závislostí se k automatickému stahování požadovaných modulů používá soubor requirements.psd1. Toto chování povolíte nastavením `managedDependency` vlastnosti na `true` kořen [host.jsv souboru](functions-host-json.md), jako v následujícím příkladu:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Když vytvoříte nový projekt PowerShell Functions, Správa závislostí je ve výchozím nastavení povolená s [ `Az` modulem](/powershell/azure/new-azureps-module-az) Azure, který je součástí služby. Maximální počet modulů, které jsou aktuálně podporovány, je 10. Podporovanou syntaxí je _`MajorNumber`_ `.*` nebo přesná verze modulu, jak je znázorněno v následujícím příkladu requirements.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Když aktualizujete soubor requirements.psd1, po restartování se nainstalují aktualizované moduly.

> [!NOTE]
> Spravované závislosti vyžadují přístup k www.powershellgallery.com, aby bylo možné stahovat moduly. Pokud spouštíte místně, ujistěte se, že modul runtime má k této adrese URL přístup přidáním požadovaných pravidel brány firewall.

> [!NOTE]
> Spravované závislosti aktuálně nepodporují moduly, které vyžadují, aby uživatel přijal licenci, a to buď prostřednictvím interaktivního přijetí licence, nebo zadáním `-AcceptLicense` přepínače při vyvolání `Install-Module` .

Pomocí následujících nastavení aplikace můžete změnit způsob stažení a instalace spravovaných závislostí. Upgrade vaší aplikace se spouští v rámci nástroje `MDMaxBackgroundUpgradePeriod` a proces upgradu se dokončí přibližně v `MDNewSnapshotCheckPeriod` .

| Nastavení Function App              | Výchozí hodnota             | Description                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dnů)     | Každý pracovní proces PowerShellu inicializuje kontrolu upgradů modulů na Galerie prostředí PowerShell spuštění procesu a každé z nich `MDMaxBackgroundUpgradePeriod` . Když je v Galerie prostředí PowerShell k dispozici nová verze modulu, nainstaluje se do systému souborů a zpřístupní se pro pracovní procesy prostředí PowerShell. Snížením této hodnoty umožníte, aby aplikace Function App získala novější verze modulu, ale také zvyšuje využití prostředků aplikace (v/v sítě, CPU, úložiště). Zvýšením této hodnoty se sníží využití prostředků aplikace, ale může také dojít k zpoždění doručení nových verzí modulu do aplikace. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 hodina)       | Až se v systému souborů nainstalují nové verze modulů, musí se všechny pracovní procesy PowerShellu restartovat. Restartování pracovních procesů PowerShell ovlivní dostupnost vaší aplikace, protože může přerušit aktuální spuštění funkce. Dokud nebudou všechny pracovní procesy prostředí PowerShell restartovány, mohou být vyvolány funkce buď staré, nebo nové verze modulu. Restart všech pracovních procesů PowerShellu se dokončil v rámci `MDNewSnapshotCheckPeriod` . Zvýšením této hodnoty se zkrátí frekvence přerušení, ale může se prodloužit i čas, kdy volání funkcí používají buď starou, nebo nové verze modulu, které nejsou deterministické. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 den)     | Aby nedocházelo k nadměrným upgradům modulů v častých restartech pracovních procesů, neprovádí se kontrola upgradů modulů, pokud kterýkoli pracovník již zahájil kontrolu za poslední `MDMinBackgroundUpgradePeriod` . |

Využití vlastních modulů je trochu jiné, než jak byste to prostupovali normálně.

V místním počítači se modul nainstaluje do jedné z globálních dostupných složek v `$env:PSModulePath` . Při spuštění v Azure nemáte přístup k modulům nainstalovaným na vašem počítači. To znamená, že se `$env:PSModulePath` aplikace funkce PowerShellu liší od `$env:PSModulePath` v běžném skriptu PowerShellu.

Ve funkcích `PSModulePath` obsahuje dvě cesty:

* `Modules`Složka, která existuje v kořenu aplikace Function App.
* Cesta ke `Modules` složce, kterou řídí pracovní proces jazyka PowerShell.


### <a name="function-app-level-modules-folder"></a>Složka na úrovni aplikace Function App `Modules`

Chcete-li použít vlastní moduly, můžete umístit moduly, na kterých jsou vaše funkce závislé ve `Modules` složce. Z této složky jsou moduly automaticky dostupné pro modul runtime Functions. Všechny funkce ve Function App můžou tyto moduly používat. 

> [!NOTE]
> Moduly zadané v souboru requirements.psd1 jsou automaticky stahovány a zahrnuty v cestě, takže je nemusíte vkládat do složky moduly. Ukládají se místně do `$env:LOCALAPPDATA/AzureFunctions` složky a ve `/data/ManagedDependencies` složce při spuštění v cloudu.

Pokud chcete využít funkci vlastního modulu, vytvořte `Modules` složku v kořenovém adresáři aplikace Function App. Do tohoto umístění zkopírujte moduly, které chcete použít ve svých funkcích.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Ve `Modules` složce by vaše aplikace Function App měla mít následující strukturu složek:

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

Když spustíte aplikaci Function App, pracovní proces PowerShellu přidá tuto `Modules` složku na, `$env:PSModulePath` takže se můžete spoléhat na automatické načítání modulu stejně jako v běžném skriptu PowerShellu.

### <a name="language-worker-level-modules-folder"></a>Složka na úrovni jazykových pracovních procesů `Modules`

PowerShell Language Worker obvykle používá několik modulů. Tyto moduly jsou definovány v poslední pozici `PSModulePath` . 

Aktuální seznam modulů je následující:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modul používaný pro práci s archivy, jako `.zip` je, `.nupkg` a další.
* **ThreadJob**: implementace rozhraní API úlohy PowerShellu založené na vláknech.

Ve výchozím nastavení funkce používají nejnovější verzi těchto modulů. Pokud chcete použít konkrétní verzi modulu, vložte tuto specifickou verzi do `Modules` složky aplikace Function App.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí `$env:NAME_OF_ENV_VAR` , jak je znázorněno v následujícím příkladu:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění jsou nastavení aplikace načítána z [local.settings.jsv](functions-run-local.md#local-settings-file) souboru projektu.

## <a name="concurrency"></a>Souběžnost

Ve výchozím nastavení může běhový modul PowerShellu funkcí zpracovat pouze jedno vyvolání funkce. Tato úroveň souběžnosti ale nemusí být dostatečná v následujících situacích:

* Při pokusu o zpracování velkého počtu vyvolání současně.
* Pokud máte funkce, které vyvolávají jiné funkce v rámci stejné aplikace Function App.

Existuje několik modelů souběžnosti, které byste mohli prozkoumat v závislosti na typu úlohy:

* Zvýšit ```FUNCTIONS_WORKER_PROCESS_COUNT``` . To umožňuje zpracování volání funkcí ve více procesech v rámci stejné instance, což přináší určité nároky na procesor a paměť. Obecně platí, že funkce vázané na vstupně-výstupní operace nebudou z této režie ovlivněny. V případě funkcí vázaných na procesor může být dopad významný.

* Zvyšte ```PSWorkerInProcConcurrencyUpperBound``` hodnotu nastavení aplikace. To umožňuje vytvoření více prostředí runspace v rámci stejného procesu, což významně snižuje nároky na procesor a paměť.

Tyto proměnné prostředí nastavíte v [nastavení aplikace](functions-app-settings.md) vaší aplikace Function App.

V závislosti na vašem případu použití může Durable Functions významně zlepšit škálovatelnost. Další informace najdete v tématu [Durable Functions vzorech aplikací](./durable/durable-functions-overview.md?tabs=powershell#application-patterns).

>[!NOTE]
> Je možné, že se "požadavky zařadí do fronty z důvodu žádného dostupného upozornění prostředí runspace". Upozorňujeme, že se nejedná o chybu. Zpráva oznamuje, že požadavky se zařadí do fronty a budou zpracovány po dokončení předchozích požadavků.

### <a name="considerations-for-using-concurrency"></a>Předpoklady pro použití souběžnosti

PowerShell je ve výchozím nastavení jediným skriptovacím jazykem s _více vlákny_ . Souběžnost se však dá přidat pomocí několika prostředí runspace prostředí PowerShell v jednom procesu. Vytvořené množství prostředí runspace se bude shodovat s ```PSWorkerInProcConcurrencyUpperBound``` nastavením aplikace. Propustnost bude mít vliv na množství CPU a paměti, které jsou k dispozici ve vybraném plánu.

Azure PowerShell používá některé kontexty _na úrovni procesu_ a stav, které vám pomůžou ušetřit nadměrné typování. Pokud však zapnete souběžnost ve vaší aplikaci Function App a vyvoláte akce, které mění stav, můžete se zaměřit na konflikty časování. Tyto konflikty časování je obtížné ladit, protože jedno vyvolání spoléhá na určitý stav a druhé vyvolání změnilo stav.

Obrovské je hodnota v souběžnosti s Azure PowerShell, protože některé operace mohou trvat značnou dobu. Je však třeba postupovat opatrně. Pokud máte podezření, že jste se setkali s časováním, nastavte nastavení aplikace PSWorkerInProcConcurrencyUpperBound na `1` a místo toho použijte [izolaci úrovně pracovního procesu jazyka](functions-app-settings.md#functions_worker_process_count) pro souběžnost.

## <a name="configure-function-scriptfile"></a>Konfigurovat funkci `scriptFile`

Ve výchozím nastavení je funkce PowerShellu spouštěna z `run.ps1` , soubor, který sdílí stejný nadřazený adresář jako odpovídající `function.json` .

`scriptFile`Vlastnost v rámci `function.json` lze použít k získání struktury složky, která vypadá jako v následujícím příkladu:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

V tomto případě `function.json` zahrnuje vlastnost for, která `myFunction` `scriptFile` odkazuje na soubor s exportovanou funkcí, která má být spuštěna.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Použití modulů PowerShellu konfigurací vstupního bodu

V tomto článku se zobrazily funkce PowerShellu ve výchozím `run.ps1` souboru skriptu generovaném šablonami.
Do modulů PowerShellu ale můžete také zahrnout své funkce. Můžete odkazovat na konkrétní kód funkce v modulu pomocí `scriptFile` `entryPoint` polí a v konfiguračním souboru function.js.

V tomto případě `entryPoint` je název funkce nebo rutiny v modulu PowerShellu, na který se odkazuje v `scriptFile` .

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

V tomto příkladu konfigurace pro `myFunction` zahrnuje `scriptFile` vlastnost, která odkazuje na `PSFunction.psm1` , což je modul prostředí PowerShell v jiné složce.  `entryPoint`Vlastnost odkazuje na `Invoke-PSTestFunc` funkci, která je vstupním bodem v modulu.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

V této konfiguraci bude `Invoke-PSTestFunc` provedeno přesně tak, jako `run.ps1` by bylo.

## <a name="considerations-for-powershell-functions"></a>Předpoklady pro funkce PowerShellu

Při práci s funkcemi PowerShellu si pamatujte na informace v následujících částech.

### <a name="cold-start"></a>Studený start

Při vývoji Azure Functions v [modelu hostování bez serveru](consumption-plan.md)je to realita. *Studená Start* odkazuje na dobu, kterou aplikace Function App spustí pro zpracování žádosti. K studenému startu dochází častěji v plánu spotřeby, protože aplikace Function App se během období nečinnosti ukončí.

### <a name="bundle-modules-instead-of-using-install-module"></a>Místo použití použít modul sady `Install-Module`

Váš skript se spustí při každém vyvolání. Vyhněte se použití `Install-Module` ve vašem skriptu. Místo toho použijte `Save-Module` před publikováním, aby vaše funkce nemusela ztrácet čas stažením modulu. Pokud mají tyto funkce vliv na studená spuštění, zvažte nasazení aplikace Function App do [plánu App Service](dedicated-plan.md) nastaveného na hodnotu *Always On* nebo [Premium](functions-premium-plan.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)

[Reference k host.json]: functions-host-json.md