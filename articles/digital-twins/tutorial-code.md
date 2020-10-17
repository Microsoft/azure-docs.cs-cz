---
title: 'Kurz: vytvoření kódu klientské aplikace'
titleSuffix: Azure Digital Twins
description: Kurz pro zápis minimálního kódu pro klientskou aplikaci pomocí sady .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 830052237580101d9141bbf812c050430c6269f2
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144574"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Kurz: kódování pomocí rozhraní API digitálních vláken Azure

Pro vývojáře, kteří pracují s digitálními podmnožinami Azure, je běžné, že napíší klientskou aplikaci pro komunikaci s její instancí služby Azure Digital Working. Tento kurz zaměřený na vývojáře poskytuje Úvod do programování služby Azure Digital prokážely pomocí [klientské knihovny Azure IoT Digital vláken pro .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Provede vás vytvořením kroku klientské aplikace konzoly C# od začátku.

> [!div class="checklist"]
> * Nastavit projekt
> * Začínáme s kódem projektu   
> * Ukázka kompletního kódu
> * Vyčištění prostředků
> * Další kroky

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se používá příkazový řádek pro nastavení a práci v projektu. Proto můžete použít libovolný editor kódu k procházení cvičení.

Co je potřeba začít:
* Jakýkoli Editor kódu
* **.NET Core 3,1** na vašem vývojovém počítači. Tuto verzi .NET Core SDK můžete stáhnout pro více platforem od [stažení .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Nastavit projekt

Až budete připraveni přejít na instanci digitálního vlákna Azure, začněte nastavovat projekt klientské aplikace. 

V počítači otevřete příkazový řádek nebo jiné okno konzoly a vytvořte prázdný adresář projektu, do kterého chcete během tohoto kurzu uložit práci. Pojmenujte adresář libovolným způsobem, který chcete (například *DigitalTwinsCodeTutorial*).

Přejděte do nového adresáře.

Jednou v adresáři projektu vytvořte prázdný projekt konzolové aplikace .NET. V příkazovém okně spusťte následující příkaz pro vytvoření minimálního projektu C# pro konzolu:

```cmd/sh
dotnet new console
```

Tím se vytvoří několik souborů v adresáři, včetně jednoho s názvem *program.cs* , kde budete psát většinu kódu.

Dále přidejte dvě nezbytné závislosti pro práci s digitálními úkoly Azure:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity
```

První závislost je [Klientská knihovna pro Azure IoT, která je pro .NET Vyzdvojená](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Druhá závislost poskytuje nástroje, které vám pomůžou s ověřováním v Azure.

Nechejte příkazové okno otevřené, jak ho budete dál používat v průběhu tohoto kurzu.

## <a name="get-started-with-project-code"></a>Začínáme s kódem projektu

V této části začnete psát kód pro nový projekt aplikace, abyste mohli pracovat s digitálními úkoly Azure. Mezi zahrnuté akce patří:
* Ověřování proti službě
* Nahrává se model.
* Zachycení chyb
* Vytváření digitálních vláken
* Vytváření relací
* Dotazování na digitální vlákna

K dispozici je také část zobrazující kompletní kód na konci tohoto kurzu. Tuto možnost můžete použít jako referenci ke kontrole vašeho programu.

Začněte tím, že otevřete soubor *program.cs* v editoru kódu. Zobrazí se minimální šablona kódu, která vypadá nějak takto:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Nejprve přidejte `using` do horní části kódu některé řádky, které budou vyžádané v nezbytných závislostech.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Dále do tohoto souboru přidáte kód, který vyplní některé funkce. 

### <a name="authenticate-against-the-service"></a>Ověřování proti službě

První věc, kterou bude vaše aplikace muset udělat, je ověřování vůči službě Azure Digital prokážed. Pak můžete vytvořit třídu klienta služby pro přístup k funkcím sady SDK.

Aby bylo možné ověřit, potřebujete tři části informací:
* *ID adresáře (tenanta)* pro vaše předplatné
* *ID aplikace (klienta)* vytvořené při nastavování instance služby Azure Digital provláknas dříve
* *Název hostitele* instance digitálního vlákna Azure

>[!TIP]
> Pokud neznáte *ID adresáře (tenant)*, můžete ho získat spuštěním tohoto příkazu v [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli
> az account show --query tenantId
> ```

Do *program.cs*vložte následující kód pod text "Hello, World!". čára tisku v `Main` metodě Nastavte hodnotu `adtInstanceUrl` na *název hostitele*instance digitálního vlákna Azure, `clientId` *ID vaší aplikace*a ID `tenantId` vašeho *adresáře*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Soubor uložte. 

Všimněte si, že v tomto příkladu se používá interaktivní přihlašovací údaje prohlížeče:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Tento typ přihlašovacích údajů způsobí, že se otevře okno prohlížeče s výzvou k zadání přihlašovacích údajů Azure. 

>[!NOTE]
> Informace o dalších typech přihlašovacích údajů najdete v dokumentaci k [ověřovacím knihovnám Microsoft Identity Platform](../active-directory/develop/reference-v2-libraries.md).

V příkazovém okně spusťte kód pomocí tohoto příkazu: 

```cmd/sh
dotnet run
```

Tato akce obnoví závislosti při prvním spuštění a pak program spustí. 
* Pokud nedojde k žádné chybě, program vytiskne *klienta služby, který je vytvořený a připravený k přechodu*.
* Vzhledem k tomu, že v tomto projektu není žádná manipulace s chybami, v případě chyby se zobrazí výjimka, která je vyvolána kódem.

### <a name="upload-a-model"></a>Nahrání modelu

Digitální vlákna Azure nemá žádný vnitřní slovník domény. Typy prvků v prostředí, které můžete reprezentovat v rámci digitálních vláken Azure, jsou definovány pomocí **modelů**. [Modely](concepts-models.md) jsou podobné třídám v objektově orientovaném programovacím jazyce; poskytují uživatelsky definované šablony pro [digitální vlákna](concepts-twins-graph.md) , které budou následovat a vytvoří se později. Jsou napsané v jazyce podobném formátu JSON, který se nazývá **Digital DTDL (digitální vlákna Definition Language)**.

Prvním krokem při vytváření řešení digitálních vláken Azure je definování alespoň jednoho modelu v souboru DTDL.

V adresáři, ve kterém jste vytvořili projekt, vytvořte nový soubor *. JSON* s názvem *SampleModel.jsv*. Vložte do následujícího textu souboru: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Pokud pro tento kurz používáte sadu Visual Studio, možná budete chtít vybrat nově vytvořený soubor JSON a nastavit vlastnost *Kopírovat do výstupního adresáře* v inspektoru vlastností na hodnotu *Kopírovat, pokud* je vždycky novější nebo *Kopírovat*. To umožní aplikaci Visual Studio najít soubor JSON s výchozí cestou při spuštění programu s klávesou **F5** během zbývající části kurzu.

> [!TIP] 
> Je k dispozici [Ukázka validátoru](/samples/azure-samples/dtdl-validator/dtdl-validator) jazyka nezávislá DTDL, kterou můžete použít ke kontrole modelu dokumentů, abyste měli jistotu, že je DTDL platný. Je postaven na knihovně analyzátoru DTDL, kterou si můžete přečíst v tématu [*Postupy: analýza a ověření modelů*](how-to-parse-models.md).

Dále přidejte další kód do *program.cs* , abyste nahráli model, který jste právě vytvořili, do instance digitálního vlákna Azure.

Nejprve přidejte `using` do horní části souboru několik příkazů:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Dále připravte na použití asynchronních metod v sadě SDK služby C# tak, že změníte `Main` signaturu metody tak, aby povolovala asynchronní spuštění. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> Použití `async` není bezpodmínečně nutné, protože sada SDK také poskytuje synchronní verze všech volání. V tomto kurzu se používají postupy `async` .

Další součástí je první bit kódu, který komunikuje se službou Azure Digital revlákens. Tento kód načte soubor DTDL, který jste vytvořili z disku, a pak ho nahraje do instance služby digitálního vláken Azure. 

Vložte následující kód v rámci autorizačního kódu, který jste přidali dříve.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

V příkazovém okně spusťte program s tímto příkazem: 

```cmd/sh
dotnet run
```
"Nahrání modelu" se ve výstupu vytiskne, ale ještě neexistuje žádný výstup k označení toho, jestli se modely úspěšně nahrály.

Pokud chcete přidat příkaz Print, který určuje, jestli se modely skutečně odesílají úspěšně, přidejte za předchozí část následující kód:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```
Předtím, než program znovu spustíte pro otestování tohoto nového kódu, zajistěte, aby jste svůj model nahráli již při posledním spuštění programu. Digitální vlákna Azure vám neumožní nahrát stejný model dvakrát, takže pokud se pokusíte znovu nahrát stejný model, program by měl vyvolat výjimku.

Nyní spusťte program znovu s tímto příkazem v příkazovém okně:

```cmd/sh
dotnet run
```

Program by měl vyvolat výjimku. Při pokusu o nahrání modelu, který již byl nahrán, služba vrátí chybu "špatný požadavek" prostřednictvím REST API. Výsledkem je, že klientská sada SDK pro Azure Digital rejímka vyvolá výjimku pro každou službu, která vrací jiný kód než úspěch. 

V další části se dozvíte o výjimkách, jako jsou tyto výjimky a jak je zpracovat v kódu.

### <a name="catch-errors"></a>Zachytit chyby

Chcete-li zabránit selhání programu, můžete přidat kód výjimky kolem kódu nahrávání modelu. Zabalte existující volání klienta `client.CreateModelsAsync` v obslužné rutině try/catch, například takto:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```
Pokud program spustíte `dotnet run` v okně příkazového okna nyní, uvidíte, že se vám vrátí kód chyby. Výstup bude vypadat nějak takto:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Model with same ID already exists dtmi:com:contoso:SampleModel;1. Use Model_List API to view models that already exist. See the Swagger example. (http://aka.ms/ModelListSwSmpl):}}

Headers:
api-supported-versions: REDACTED
Date: Thu, 10 Sep 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Od tohoto okamžiku se v kurzu zabalí všechna volání metod služby v obslužných rutinách try/catch.

### <a name="create-digital-twins"></a>Vytváření digitálních vláken

Teď, když jste nahráli model do digitálních vláken Azure, můžete k vytváření **digitálních vláken**použít tuto definici modelu. [Digitální vlákna](concepts-twins-graph.md) jsou instance modelu a představují entity v rámci vašeho podnikového prostředí – například senzory ve farmě, místnosti v budově nebo světla v kleci. Tato část vytvoří několik digitálních vláken na základě modelu, který jste nahráli dříve.

Přidejte nový `using` příkaz v horní části, protože budete potřebovat vestavěný serializátor .NET JSON v `System.Text.Json` :

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Pak na konec metody přidejte následující kód, `Main` který vytvoří a inicializuje tři digitální vlákna na základě tohoto modelu.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Pak opakujte spuštění programu znovu. 

Všimněte si, že není vyvolána žádná chyba při druhém vytvoření vlákna, a to i v případě, že vlákna již existují po prvním spuštění. Na rozdíl od vytváření modelů je vytvoření vlákna na úrovni REST, volání *Put* se sémantikou *Upsert* . To znamená, že pokud už existuje, zkuste ho znovu vytvořit, jenom nahradit ho. Nepožaduje se žádná chyba.

### <a name="create-relationships"></a>Vytvoření relací

V dalším kroku můžete vytvořit **relace** mezi dvojitými vytvořenými podmnožinami a propojit je s **dvojitým grafem**. K reprezentaci celého prostředí se používají [Dvojitá grafu](concepts-twins-graph.md) .

Pro usnadnění vytváření relací používá tato ukázka kódu `Azure.DigitalTwins.Core.Serialization` obor názvů. Toto jste přidali do projektu výše pomocí tohoto `using` příkazu:

```csharp
using Azure.DigitalTwins.Core.Serialization;
```

>[!NOTE]
>`Azure.DigitalTwins.Core.Serialization` není vyžadována pro práci s digitálními úkoly a vztahy; je to volitelný obor názvů, který může pomáhat získat data do správného formátu. Mezi další alternativy použití patří:
>* Zřetězení řetězců pro vytvoření objektu JSON
>* Použití analyzátoru JSON jako `System.Text.Json` dynamického sestavení objektu JSON
>* Modelování vlastních typů v jazyce C#, jejich instance a jejich serializace do řetězců

Přidejte do třídy novou statickou metodu `Program` pod `Main` metodou:

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Dále přidejte následující kód na konec `Main` metody, pro volání `CreateRelationship` metody a použijte kód, který jste právě napsali:

```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

V příkazovém okně spusťte program pomocí příkazu `dotnet run` .

Všimněte si, že digitální vlákna Azure vám neumožní vytvořit relaci, pokud už existuje jedna se stejným ID – Pokud spustíte program několikrát, při vytváření relace se zobrazí výjimky. Tento kód zachytí výjimky a ignoruje je. 

### <a name="list-relationships"></a>Výpis relací

Další kód, který přidáte, vám umožní zobrazit seznam relací, které jste vytvořili.

Do třídy `Program` přidejte následující novou metodu:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Pak přidejte následující kód na konec `Main` metody pro volání `ListRelationships` kódu:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Měl by se zobrazit seznam všech relací, které jste vytvořili.

Tady je příklad výstupu:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Type name: System.Collections.Generic.Dictionary'2[System.String,System.String]: dtmi:contosocom:DigitalTwins:SampleModel;1
Create twin: sampleTwin-0
Create twin: sampleTwin-1
Create twin: sampleTwin-2
Created relationship successfully
Created relationship successfully
Twin sampleTwin-0 is connected to:
-contains->sampleTwin-1
-contains->sampleTwin-2

```

### <a name="query-digital-twins"></a>Dotazování na digitální vlákna

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí.

Poslední část kódu, který se má přidat v tomto kurzu, spustí dotaz na instanci digitálních vláken Azure. Dotaz použitý v tomto příkladu vrátí všechny digitální vlákna v instanci.

Na konec metody přidejte následující kód `Main` :

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

V příkazovém okně spusťte program pomocí příkazu `dotnet run` . Ve výstupu by se měly zobrazit všechny digitální vlákna v této instanci.

## <a name="complete-code-example"></a>Příklad kompletního kódu

V tomto okamžiku v tomto kurzu máte úplnou klientskou aplikaci, která umožňuje provádět základní akce proti digitálním vazbám Azure. Pro referenci je úplný kód programu v *program.cs* uveden níže:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Vyčištění prostředků
 
Instance použitá v tomto kurzu se dá znovu použít v dalším kurzu. [*kurz: Prozkoumejte základy pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md). Pokud máte v úmyslu pokračovat k dalšímu kurzu, můžete ponechat instanci digitálních vláken Azure, kterou jste nastavili tady.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Nakonec odstraňte složku projektu, kterou jste vytvořili na místním počítači.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili klientskou aplikaci konzoly .NET od nuly. Napsali jste kód pro tuto klientskou aplikaci k provádění základních akcí v instanci digitálních vláken Azure.

Pokračujte k dalšímu kurzu, abyste prozkoumali věci, které můžete s touto ukázkovou klientskou aplikací dělat: 

> [!div class="nextstepaction"]
> [*Kurz: zkoumání základních informací pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md)