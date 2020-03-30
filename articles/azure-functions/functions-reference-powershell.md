---
title: Odkaz na vývojáře Prostředí PowerShell pro funkce Azure
description: Zjistěte, jak vyvíjet funkce pomocí prostředí PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 41f977e7e7c23c2f49fd656461b7a3920802997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276735"
---
# <a name="azure-functions-powershell-developer-guide"></a>Průvodce vývojářem prostředí Azure Functions PowerShell

Tento článek obsahuje podrobnosti o tom, jak psát funkce Azure pomocí PowerShellu.

Funkce (funkce) Prostředí PowerShell azure je reprezentováno jako skript prostředí PowerShell, který se spustí při aktivaci. Každý skript funkce `function.json` má související soubor, který definuje, jak se funkce chová, například jak je spuštěna a její vstupní a výstupní parametry. Další informace naleznete v [článku Aktivační události a vazby](functions-triggers-bindings.md). 

Stejně jako ostatní druhy funkcí, powershellové skriptové funkce berou parametry, `function.json` které odpovídají názvům všech vstupních vazeb definovaných v souboru. Je `TriggerMetadata` také předán parametr, který obsahuje další informace o aktivační události, která spustila funkci.

Tento článek předpokládá, že jste si již přečetli [odkaz na vývojáře Azure Functions](functions-reference.md). Měli byste také dokončit [funkce rychlý start pro PowerShell](functions-create-first-function-powershell.md) vytvořit první funkci Prostředí PowerShell.

## <a name="folder-structure"></a>Struktura složek

Požadovaná struktura složek pro projekt prostředí PowerShell vypadá takto. Toto výchozí nastavení lze změnit. Další informace naleznete v části [scriptFile](#configure-function-scriptfile) níže.

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

V kořenovém adresáři projektu je [`host.json`](functions-host-json.md) sdílený soubor, který lze použít ke konfiguraci aplikace funkce. Každá funkce má složku s vlastním souborem kódu (.ps1) a konfiguračním souborem vazby (`function.json`). Název nadřazeného adresáře souboru function.json je vždy název funkce.

Některé vazby vyžadují přítomnost `extensions.csproj` souboru. Vsouboru jsou definovány rozšíření vazby, které jsou požadovány ve [verzi 2.x a novějších verzích](functions-versions.md) běhu Functions, se skutečnými soubory knihovny ve `bin` složce. `extensions.csproj` Při vývoji místně, musíte [zaregistrovat rozšíření vazby](functions-bindings-register.md#extension-bundles). Při vývoji funkcí na webu Azure Portal se tato registrace provádí za vás.

V aplikacích funkce prostředí PowerShell, `profile.ps1` můžete volitelně mít, který běží, když se spustí aplikace funkce (jinak vědět, jako *[studený start](#cold-start)*. Další informace naleznete v [tématu PowerShell profile](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definování skriptu prostředí PowerShell jako funkce

Ve výchozím nastavení hledá zaběhu Funkce `run.ps1`vaši `run.ps1` funkci v aplikaci `function.json`, kde sdílí stejný nadřazený adresář jako odpovídající .

Váš skript je předán několik argumentů o spuštění. Chcete-li zpracovat tyto `param` parametry, přidejte blok do horní části skriptu jako v následujícím příkladu:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parametr TriggerMetadata

Parametr `TriggerMetadata` se používá k zadání dalších informací o aktivační události. Další metadata se liší od vazby na `sys` vazbu, ale všechny obsahují vlastnost, která obsahuje následující data:

```powershell
$TriggerMetadata.sys
```

| Vlastnost   | Popis                                     | Typ     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Když byla v UTC spuštěna funkce        | DateTime |
| Methodname | Název funkce, která byla spuštěna     | řetězec   |
| RandGuid (Fr.)   | jedinečný identifikátor GUID k tomuto provedení funkce | řetězec   |

Každý typ aktivační události má jinou sadu metadat. Například `$TriggerMetadata` `QueueTrigger` for obsahuje `InsertionTime`, `Id` `DequeueCount`, , , mimo jiné. Další informace o metadatech aktivační události fronty naleznete v [oficiální dokumentaci aktivačních událostí fronty](functions-bindings-storage-queue-trigger.md#message-metadata). Podívejte se do dokumentace k [aktivačním událostem,](functions-triggers-bindings.md) se kterými pracujete, a podívejte se, co se dostane do metadat aktivační události.

## <a name="bindings"></a>Vazby

V prostředí PowerShell jsou [vazby nakonfigurovány](functions-triggers-bindings.md) a definovány v souboru function.json funkce. Funkce interagují s vazbami několika způsoby.

### <a name="reading-trigger-and-input-data"></a>Čtení spouštěcích a vstupních dat

Aktivační a vstupní vazby jsou čteny jako parametry předané vaší funkci. Vstupní vazby `direction` mají `in` nastavenou na function.json. Vlastnost `name` definovaná `function.json` v je název parametru `param` v bloku. Vzhledem k tomu, že Prostředí PowerShell používá pojmenované parametry pro vazbu, nezáleží na pořadí parametrů. Je však osvědčeným postupem dodržovat pořadí vazeb definovaných `function.json`v .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Zápis výstupních dat

V functions, výstupní vazba má nastavena `direction` na `out` v function.json. Můžete zapisovat do výstupní `Push-OutputBinding` vazby pomocí rutiny, která je k dispozici funkce runtime. Ve všech případech `name` vlastnost vazby, `function.json` jak je `Name` definována `Push-OutputBinding` v odpovídá parametru rutiny.

Následující text ukazuje, `Push-OutputBinding` jak volat ve skriptu funkce:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Můžete také předat hodnotu pro konkrétní vazbu prostřednictvím kanálu.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`chová odlišně v závislosti na `-Name`hodnotě určené pro :

* Pokud zadaný název nelze přeložit na platnou výstupní vazbu, je vyvolána chyba.

* Když výstupní vazba přijme kolekci hodnot, `Push-OutputBinding` můžete volat opakovaně push více hodnot.

* Pokud výstupní vazba přijímá pouze hodnotu `Push-OutputBinding` singleton, volání podruhé vyvolá chybu.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding`Syntaxe

Následující jsou platné parametry `Push-OutputBinding`pro volání :

| Name (Název) | Typ | Pozice | Popis |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | Řetězec | 1 | Název výstupní vazby, kterou chcete nastavit. |
| **`-Value`** | Objekt | 2 | Hodnota výstupní vazby, kterou chcete nastavit, která je přijata z kanálu ByValue. |
| **`-Clobber`** | Přepínací parametr | S názvem | (Nepovinné) Pokud je zadán, vynutí hodnotu, která má být nastavena pro zadanou výstupní vazbu. | 

Podporovány jsou také následující běžné parametry: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Další informace naleznete v tématu [O CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Příklad push-OutputBinding: Odpovědi HTTP

Aktivační událost HTTP vrátí odpověď pomocí `response`výstupní vazby s názvem . V následujícím příkladu má `response` výstupní vazba hodnotu "výstupní #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Vzhledem k tomu, že výstup je http, který přijímá pouze `Push-OutputBinding` hodnotu singleton, je vyvolána chyba, když je volána podruhé.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Pro výstupy, které přijímají pouze hodnoty singleton, můžete použít `-Clobber` parametr přepsat starou hodnotu namísto pokusu o přidání do kolekce. Následující příklad předpokládá, že jste již přidali hodnotu. Pomocí `-Clobber`použití přepíše odpověď z následujícího příkladu existující hodnotu a vrátí hodnotu "výstupní #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding Příklad: Výstupní vazba fronty

`Push-OutputBinding`slouží k odesílání dat do výstupních vazeb, jako je například [výstupní vazba úložiště fronty Azure](functions-bindings-storage-queue-output.md). V následujícím příkladu má zpráva zapsaná do fronty hodnotu "výstupní #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

Výstupní vazba pro frontu úložiště přijímá více výstupních hodnot. V tomto případě volání následujícípříklad po první zapíše do fronty seznam se dvěma položkami: "výstup #1" a "výstupní #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

Následující příklad při volání po předchozích dvou přidá další dvě hodnoty do kolekce výstupu:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Při zápisu do fronty zpráva obsahuje tyto čtyři hodnoty: "výstupní #1", "výstupní #2", "výstupní #3" a "výstupní #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding`Rutina

Rutinu `Get-OutputBinding` můžete použít k načtení hodnot aktuálně nastavených pro výstupní vazby. Tato rutina načte hodnotitelnou hodnotu, která obsahuje názvy výstupních vazeb s jejich příslušnými hodnotami. 

Následuje příklad použití k `Get-OutputBinding` vrácení aktuálních hodnot vazby:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding`obsahuje také parametr `-Name`s názvem , který lze použít k filtrování vrácené vazby, jako v následujícím příkladu:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Zástupné znaky (*) `Get-OutputBinding`jsou podporovány v .

## <a name="logging"></a>protokolování

Protokolování funkcí Prostředí PowerShell funguje jako běžné protokolování prostředí PowerShell. Rutiny protokolování můžete použít k zápisu do každého výstupního datového proudu. Každá rutina se mapuje na úroveň protokolu používanou funkcemi.

| Úroveň protokolování funkcí | Rutina protokolování |
| ------------- | -------------- |
| Chyba | **`Write-Error`** |
| Upozornění | **`Write-Warning`**  | 
| Informace | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informace | Zapisuje do protokolování na úrovni _informací._ |
| Ladění | **`Write-Debug`** |
| Trasování | **`Write-Progress`** <br /> **`Write-Verbose`** |

Kromě těchto rutin je vše, co je zapsáno do `Information` kanálu, přesměrováno na úroveň protokolu a zobrazeno s výchozím formátováním prostředí PowerShell.

> [!IMPORTANT]
> Použití `Write-Verbose` rutiny nebo `Write-Debug` nestačí zobrazit podrobné a ladění úrovně protokolování. Je také nutné nakonfigurovat prahovou hodnotu úrovně protokolu, která deklaruje, na jaké úrovni protokolů skutečně záleží. Další informace najdete [v tématu Konfigurace úrovně protokolu aplikace funkce](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Konfigurace úrovně protokolu aplikace funkcí

Funkce Azure umožňuje definovat prahovou hodnotu, která usnadňuje řízení způsobu, jakým funkce zapisují do protokolů. Chcete-li nastavit prahovou hodnotu pro všechna `logging.logLevel.default` trasování zapsaná do konzoly, použijte vlastnost v[odkazu host.json] [ `host.json` souboru]. Toto nastavení platí pro všechny funkce ve vaší aplikaci funkce.

Následující příklad nastaví prahovou hodnotu tak, aby umožňovala podrobné protokolování pro všechny `MyFunction`funkce, ale nastaví prahovou hodnotu tak, aby umožňovala protokolování ladění pro funkci s názvem :

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

Další informace naleznete v [tématu host.json reference].

### <a name="viewing-the-logs"></a>Zobrazení protokolů

Pokud vaše aplikace funkce běží v Azure, můžete použít Application Insights sledovat. Přečtěte si [monitorování funkcí Azure](functions-monitoring.md) další informace o zobrazení a dotazování protokoly funkcí.

Pokud používáte aplikaci function místně pro vývoj, protokoly výchozí systémsouborů. Chcete-li zobrazit protokoly v `AZURE_FUNCTIONS_ENVIRONMENT` konzole, `Development` nastavte proměnnou prostředí před spuštěním aplikace funkce.

## <a name="triggers-and-bindings-types"></a>Typy aktivačních událostí a vazeb

Existuje celá řada aktivačních událostí a vazeb, které můžete použít s aplikací funkce. Úplný seznam spouštěčů a vazeb [naleznete zde](functions-triggers-bindings.md#supported-bindings).

Všechny aktivační události a vazby jsou reprezentovány v kódu jako několik reálných datových typů:

* Hashtable
* řetězec
* bajt[]
* int
* double
* HttpRequestContext
* Kontext httpresponse

Prvních pět typů v tomto seznamu jsou standardní typy .NET. Poslední dva jsou používány pouze [aktivační událost HttpTrigger](#http-triggers-and-bindings).

Každý parametr vazby ve vašich funkcích musí být jedním z těchto typů.

### <a name="http-triggers-and-bindings"></a>Aktivační události a vazby HTTP

Aktivační události HTTP a webhooku a výstupní vazby PROTOKOLU HTTP používají objekty požadavku a odpovědi k reprezentaci zasílání zpráv HTTP.

#### <a name="request-object"></a>Požadavek na objekt

Objekt požadavku, který je předán do skriptu je typu `HttpRequestContext`, který má následující vlastnosti:

| Vlastnost  | Popis                                                    | Typ                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Objekt, který obsahuje tělo požadavku. `Body`je serializován do nejlepšího typu na základě dat. Například pokud data json, je předán jako hashtable. Pokud jsou data řetězec, je předán jako řetězec. | objekt |
| **`Headers`** | Slovník, který obsahuje hlavičky požadavku.                | Slovník<řetězec,>řetězců<sup>*</sup> |
| **`Method`** | Metoda HTTP požadavku.                                | řetězec                    |
| **`Params`**  | Objekt, který obsahuje parametry směrování požadavku. | Slovník<řetězec,>řetězců<sup>*</sup> |
| **`Query`** | Objekt, který obsahuje parametry dotazu.                  | Slovník<řetězec,>řetězců<sup>*</sup> |
| **`Url`** | Adresa URL požadavku.                                        | řetězec                    |

<sup>*</sup>Všechny `Dictionary<string,string>` klíče nerozlišují malá a velká písmena.

#### <a name="response-object"></a>Objekt odpovědi

Objekt odpovědi, který byste měli odeslat zpět, je typu `HttpResponseContext`, který má následující vlastnosti:

| Vlastnost      | Popis                                                 | Typ                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Objekt, který obsahuje tělo odpovědi.           | objekt                    |
| **`ContentType`** | Krátká kombinace pro nastavení typu obsahu pro odpověď. | řetězec                    |
| **`Headers`** | Objekt, který obsahuje hlavičky odpovědí.               | Slovník nebo hashtable   |
| **`StatusCode`**  | Stavový kód HTTP odpovědi.                       | řetězec nebo int             |

#### <a name="accessing-the-request-and-response"></a>Přístup k požadavku a odpovědi

Při práci s aktivačními událostmi HTTP můžete přistupovat k požadavku HTTP stejným způsobem jako u jakékoli jiné vstupní vazby. Je to v `param` bloku.

Pomocí `HttpResponseContext` objektu vraťte odpověď, jak je znázorněno v následujícím textu:

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

Výsledkem vyvolání této funkce by bylo:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Typové lití pro spouštěče a vázání

Pro některé vazby, jako je vazby objektu blob, můžete zadat typ parametru.

Chcete-li například mít data z úložiště objektů Blob zadaná jako řetězec, přidejte do `param` bloku následující přetypování typu:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil prostředí PowerShell

V Prostředí PowerShell je koncept profilu Prostředí PowerShell. Pokud nejste obeznámeni s profily PowerShellu, přečtěte si informace [o profilech](/powershell/module/microsoft.powershell.core/about/about_profiles).

Ve funkcích prostředí PowerShell se skript profilu spustí při spuštění aplikace funkce. Aplikace funkcí se spustí při prvním nasazení a po nečinnosti[(studený start).](#cold-start)

Když vytvoříte aplikaci funkcí pomocí nástrojů, jako je Visual Studio `profile.ps1` Code a Nástroje azure functions core, vytvoří se pro vás výchozí nastavení. Výchozí profil je udržován [v úložišti GitHub core tools](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) a obsahuje:

* Automatické ověřování MSI do Azure.
* Možnost zapnout aliasy Prostředí `AzureRM` Azure PowerShell, pokud chcete.

## <a name="powershell-version"></a>Verze prostředí PowerShell

V následující tabulce je uvedena verze prostředí PowerShell používaná jednotlivými hlavními verzemi runtime Functions:

| Verze funkcí | Verze prostředí PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Prostředí Windows PowerShell 5.1 (uzamčeno za běhu) |
| 2.x               | PowerShell Core 6                              |

Aktuální verzi můžete zobrazit `$PSVersionTable` tiskem z libovolné funkce.

## <a name="dependency-management"></a>Správa závislostí

Funkce umožňují využít [galerii Prostředí PowerShell](https://www.powershellgallery.com) pro správu závislostí. Pokud je povolena správa závislostí, soubor requirements.psd1 se používá k automatickému stahování požadovaných modulů. Toto chování povolíte `managedDependency` nastavením vlastnosti `true` v kořenovém adresáři [souboru host.json](functions-host-json.md), jako v následujícím příkladu:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Když vytvoříte nový projekt funkcí Prostředí PowerShell, správa závislostí je ve výchozím nastavení povolená s [ `Az` součástí modulu](/powershell/azure/new-azureps-module-az) Azure. Maximální počet aktuálně podporovaných modulů je 10. Podporovaná syntaxe je _`MajorNumber`_ `.*` nebo přesná verze modulu, jak je znázorněno v následujícím příkladu requirements.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Při aktualizaci souboru requirements.psd1 jsou po restartování nainstalovány aktualizované moduly.

> [!NOTE]
> Spravované závislosti vyžadují přístup ke www.powershellgallery.com ke stažení modulů. Při místním spuštění se ujistěte, že runtime má přístup k této adrese URL přidáním všech požadovaných pravidel brány firewall. 

Následující nastavení aplikace lze změnit způsob stahování a instalace spravovaných závislostí. Upgrade aplikace se `MDMaxBackgroundUpgradePeriod`spustí v rámci aplikace a `MDNewSnapshotCheckPeriod`proces upgradu se dokončí v rámci přibližně .

| Nastavení aplikace funkce              | Výchozí hodnota             | Popis                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00`(7 dní)     | Každý pracovní proces prostředí PowerShell iniciuje kontrolu upgradů modulů v Galerii prostředí PowerShell při spuštění procesu a po každém. `MDMaxBackgroundUpgradePeriod` Když je v Galerii Prostředí PowerShell dostupná nová verze modulu, je nainstalovaná do systému souborů a dostupná pracovníkům prostředí PowerShell. Snížení této hodnoty umožňuje aplikaci funkce získat novější verze modulu dříve, ale také zvyšuje využití prostředků aplikace (vstupně-co a o sítě, CPU, úložiště). Zvýšení této hodnoty snižuje využití prostředků aplikace, ale může také zpozdit doručení nových verzí modulu do vaší aplikace. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00`(1 hod.)       | Po instalaci nových verzí modulu do systému souborů musí být každý pracovní proces prostředí PowerShell restartován. Restartování pracovníků prostředí PowerShell ovlivňuje dostupnost vaší aplikace, protože může přerušit spuštění aktuální funkce. Dokud nebudou restartovány všechny pracovní procesy prostředí PowerShell, vyvolání funkcí může používat buď staré, nebo nové verze modulu. Restartování všech pracovníků prostředí `MDNewSnapshotCheckPeriod`PowerShell dokončeno v rámci aplikace . Zvýšení této hodnoty snižuje četnost přerušení, ale může také zvýšit dobu, kdy vyvolání funkce použít buď staré nebo nové verze modulu non-deterministically. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00`(1 den)     | Chcete-li se vyhnout nadměrné upgrady modulu na časté restartování pracovního procesu, kontrola upgradů `MDMinBackgroundUpgradePeriod`modulu se neprovádí, pokud některý pracovník již zahájila tuto kontrolu v poslední . |

Využití vlastních modulů je trochu jiné, než jak byste to udělali normálně.

V místním počítači se modul nainstaluje do jedné z `$env:PSModulePath`globálně dostupných složek ve vašem počítači . Když běží v Azure, nemáte přístup k modulům nainstalovaným v počítači. To znamená, `$env:PSModulePath` že pro aplikaci funkce `$env:PSModulePath` Prostředí PowerShell se liší od v běžném skriptu PowerShellu.

Ve funkcích `PSModulePath` obsahuje dvě cesty:

* Složka, `Modules` která existuje v kořenovém adresáři aplikace funkce.
* Cesta ke `Modules` složce, která je řízena jazykovým pracovníkem prostředí PowerShell.

### <a name="function-app-level-modules-folder"></a>Složka na `Modules` úrovni aplikace

Chcete-li používat vlastní moduly, můžete umístit moduly, na kterých závisí vaše funkce ve `Modules` složce. Z této složky jsou moduly automaticky k dispozici moduly pro modul y runtime. Tyto moduly můžete používat libovolnou funkcí v aplikaci funkce. 

> [!NOTE]
> Moduly zadané v souboru requirements.psd1 jsou automaticky staženy a zahrnuty do cesty, takže je nemusíte zařazovat do složky modulů. Ty jsou uloženy `$env:LOCALAPPDATA/AzureFunctions` místně ve `/data/ManagedDependencies` složce a ve složce při spuštění v cloudu.

Chcete-li využít funkci vlastního modulu, vytvořte `Modules` složku v kořenovém adresáři aplikace funkce. Zkopírujte moduly, které chcete použít ve svých funkcích, do tohoto umístění.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Ve `Modules` složce by aplikace funkcí měla mít následující strukturu složek:

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

Při spuštění aplikace funkce, pracovník jazyka PowerShell přidá tuto `Modules` `$env:PSModulePath` složku, takže se můžete spolehnout na automatické načítání modulu stejně jako v běžném skriptu prostředí PowerShell.

### <a name="language-worker-level-modules-folder"></a>Složka jazykové `Modules` úrovně pracovníka

Několik modulů se běžně používá pracovník jazyka PowerShell. Tyto moduly jsou definovány `PSModulePath`v poslední poloze . 

Aktuální seznam modulů je následující:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): modul používaný pro práci `.zip` `.nupkg`s archivy, jako je , a další.
* **ThreadJob**: Implementace rozhraní API úlohy prostředí PowerShell na základě vlákna.

Ve výchozím nastavení funkce používá nejnovější verzi těchto modulů. Chcete-li použít konkrétní verzi modulu, `Modules` vložte tuto konkrétní verzi do složky aplikace funkce.

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), například připojovací řetězce služby, během provádění vystavena jako proměnné prostředí. K těmto nastavením `$env:NAME_OF_ENV_VAR`můžete přistupovat pomocí aplikace , jak je znázorněno v následujícím příkladu:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Při místním spuštění se nastavení aplikace načtou ze souboru projektu [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="concurrency"></a>Souběžnost

Ve výchozím nastavení může prostředí Functions PowerShell zpracovat pouze jedno vyvolání funkce současně. Tato úroveň souběžnosti však nemusí být dostatečná v následujících situacích:

* Při pokusu o zpracování velkého počtu vyvolání současně.
* Pokud máte funkce, které vyvolávají další funkce uvnitř stejné aplikace funkce.

Toto chování můžete změnit nastavením následující proměnné prostředí na celou hodnotu:

```
PSWorkerInProcConcurrencyUpperBound
```

Tuto proměnnou prostředí nastavíte v [nastavení aplikace](functions-app-settings.md) aplikace funkce.

### <a name="considerations-for-using-concurrency"></a>Důležité informace pro použití souběžnosti

Prostředí PowerShell je ve výchozím nastavení skriptovací jazyk _s jedním vláknem._ Souběžnost však lze přidat pomocí více runspaces prostředí PowerShell ve stejném procesu. Množství vytvořených runspaces bude odpovídat nastavení aplikace PSWorkerInProcConcurrencyUpperBound. Propustnost bude ovlivněna množstvím procesoru a paměti, které jsou k dispozici ve vybraném plánu.

Azure PowerShell používá některé kontexty a stav _na úrovni procesu,_ které vám pomohou ušetřit před nadbytečným psaním. Pokud však zapnete souběžnost v aplikaci funkce a vyvoláte akce, které změní stav, můžete skončit s časovacími podmínkami. Tyto časování podmínky je obtížné ladit, protože jeden vyvolání závisí na určitý stav a druhé vyvolání změnil stav.

Je obrovská hodnota souběžnosti s Azure PowerShell, protože některé operace může trvat značné množství času. Musíte však postupovat opatrně. Pokud máte podezření, že dochází ke spor, nastavte nastavení aplikace PSWorkerInProcConcurrencyUpperBound a `1` místo toho použijte [izolaci jazykového pracovního procesu](functions-app-settings.md#functions_worker_process_count) pro souběžnost.

## <a name="configure-function-scriptfile"></a>Konfigurovat funkci`scriptFile`

Ve výchozím nastavení je funkce prostředí `run.ps1`PowerShell spuštěna ze souboru `function.json`, který sdílí stejný nadřazený adresář jako odpovídající .

Vlastnost `scriptFile` v `function.json` lze získat strukturu složek, která vypadá jako následující příklad:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

V tomto případě `function.json` `myFunction` for `scriptFile` obsahuje vlastnost odkazující na soubor s exportovnou funkcí, která má být spuštěna.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Použití modulů PowerShellu konfigurací entryPointu

Tento článek zobrazuje funkce prostředí `run.ps1` PowerShell ve výchozím souboru skriptu generovaném šablonami.
Můžete však také zahrnout funkce v modulech Prostředí PowerShell. Můžete odkazovat na váš konkrétní kód `scriptFile` funkce `entryPoint` v modulu pomocí polí a v konfiguračním souboru function.json.

V tomto `entryPoint` případě je název funkce nebo rutiny v modulu `scriptFile`Prostředí PowerShell odkazuje v .

Zvažte následující strukturu složek:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Pokud `PSFunction.psm1` obsahuje:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

V tomto příkladu `myFunction` konfigurace `scriptFile` pro zahrnuje `PSFunction.psm1`vlastnost, která odkazuje , což je modul Prostředí PowerShell v jiné složce.  Vlastnost `entryPoint` odkazuje na `Invoke-PSTestFunc` funkci, která je vstupním bodem v modulu.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

S touto konfigurací `Invoke-PSTestFunc` získá provedeny `run.ps1` přesně tak, jak by.

## <a name="considerations-for-powershell-functions"></a>Důležité informace o funkcích prostředí PowerShell

Při práci s funkcemi prostředí PowerShell, uvědomte si důležité informace v následujících částech.

### <a name="cold-start"></a>Studený start

Při vývoji funkcí Azure v [modelu hostování bez serveru](functions-scale.md#consumption-plan)jsou studené starty realitou. *Studený start* označuje dobu, po které se vaše aplikace funkcí spustí ke zpracování požadavku. Studený start se děje častěji v plánu spotřeba, protože vaše aplikace funkce se vypne během období nečinnosti.

### <a name="bundle-modules-instead-of-using-install-module"></a>Balíčkové moduly namísto použití`Install-Module`

Váš skript je spuštěn na každé vyvolání. Vyhněte se použití `Install-Module` ve vašem skriptu. Místo `Save-Module` toho použijte před publikováním tak, aby vaše funkce nemusí ztrácet čas stahování modulu. Pokud studené starty mají vliv na vaše funkce, zvažte nasazení aplikace funkce do [plánu služby App Service](functions-scale.md#app-service-plan) nastaveného na vždy *zapnutý* nebo na [plán Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)

[Reference k host.json]: functions-host-json.md
