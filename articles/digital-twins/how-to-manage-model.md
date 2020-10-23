---
title: Správa vlastních modelů
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit, upravit a odstranit model v rámci digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: dbe1c368a730fcd09cf5e5f6435ca247150b5c3c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426182"
---
# <a name="manage-azure-digital-twins-models"></a>Správa modelů digitálních vláken Azure

Můžete spravovat [modely](concepts-models.md) , které vaše instance digitálního vlákna Azure ví o používání [**rozhraní DigitalTwinsModels API**](how-to-use-apis-sdks.md), [sady SDK pro .NET (C#)](https://www.nuget.org/packages/Azure.DigitalTwins.Core)nebo [digitálních vláken Azure](how-to-use-cli.md). 

Mezi operace správy patří nahrávání, ověřování, načítání a odstraňování modelů. 

## <a name="create-models"></a>Vytváření modelů

Modely pro digitální vlákna Azure jsou napsané v DTDL a ukládají se jako soubory *. JSON* . K dispozici je také [rozšíření DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) pro [Visual Studio Code](https://code.visualstudio.com/), které poskytuje ověřování syntaxe a další funkce usnadňující psaní dokumentů DTDL.

Vezměte v úvahu příklad, ve kterém chce nemocnice digitálně reprezentovat své místnosti. Každá místnost obsahuje inteligentní zásobník SOAP pro sledování ručního mytí a snímačů pro monitorování provozu přes místnost.

Prvním krokem k řešení je vytvoření modelů, které reprezentují aspekty nemocnice. Místnost pro pacienty v tomto scénáři může být popsána takto:

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> Toto je vzorový text pro soubor. JSON, ve kterém je model definovaný a uložený, aby se nahrál jako součást klientského projektu. REST API volání na druhé straně převezme pole definic modelů, jako je výše (která je namapována na `IEnumerable<string>` rozhraní .NET SDK). Takže pokud chcete tento model použít přímo v REST API, uzavřete ho do závorek.

Tento model definuje název a jedinečné ID pro místnost pacienta a vlastnosti představující počet návštěvníků a stav ručního mytí (tyto čítače se budou aktualizovat ze senzorů pohybu a inteligentních zásobníků protokolu SOAP a společně se použijí k výpočtu *procentuální vlastnosti handwash* ). Model také definuje *hasDevices*vztahu, který se použije pro připojení všech [digitálních vláken](concepts-twins-graph.md) na základě tohoto modelu *Room* na skutečná zařízení.

V rámci této metody můžete přejít na, abyste definovali modely pro nemocnice, zóny nebo samotnou nemocnice.

### <a name="validate-syntax"></a>Ověřit syntaxi

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>Správa modelů pomocí rozhraní API

V následujících částech se dozvíte, jak provádět různé operace správy modelů pomocí [rozhraní API a sad SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md).

> [!NOTE]
> Níže uvedené příklady nezahrnují zpracování chyb pro zkrácení. V rámci vašich projektů se však důrazně doporučuje zabalit volání služby v blocích try/catch.

> [!TIP] 
> Pamatujte, že všechny metody sady SDK přicházejí v synchronních a asynchronních verzích. Pro volání stránkování vrátí asynchronní metody `AsyncPageable<T>` při návratu synchronních verzí `Pageable<T>` .

### <a name="upload-models"></a>Nahrávání modelů

Po vytvoření modelů je můžete nahrát do instance digitálních vláken Azure.

> [!TIP]
> Před odesláním do instance digitálního vlákna Azure doporučujeme vaše modely ověřit offline. Můžete použít [knihovnu analyzátorů DTDL na straně klienta](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) a [ukázku validátoru DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) popsané v tématu [*Postupy: analýza a ověření modelů*](how-to-parse-models.md) pro kontrolu vašich modelů před jejich odesláním do služby.

Až budete připraveni k nahrání modelu, můžete použít následující fragment kódu:

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

Všimněte si, že `CreateModels` Metoda přijímá více souborů v jedné transakci. Tady je ukázka, která ilustruje:

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

Soubory modelu mohou obsahovat více než jeden model. V takovém případě musí být modely umístěny do pole JSON. Příklad:

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
Při nahrávání se služba ověřuje pomocí souborů modelu.

### <a name="retrieve-models"></a>Načíst modely

Můžete vypsat a načíst modely uložené ve vaší instanci digitálních vláken Azure. 

Tady jsou tyto možnosti:
* Načíst všechny modely
* Načtení jednoho modelu
* Načtení jednoho modelu se závislostmi
* Načtení metadat pro modely

Tady je několik příkladů volání:

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

Rozhraní API volá pro načtení modelů všechny návratové `ModelData` objekty. `ModelData` obsahuje metadata o modelu uloženém v instanci digitálních vláken Azure, jako je název, DTMI a datum vytvoření modelu. `ModelData`Objekt také volitelně zahrnuje samotný model. V závislosti na parametrech lze proto použít volání metody načíst buď k načtení metadat (což je užitečné ve scénářích, kde chcete zobrazit seznam dostupných nástrojů, například), nebo celý model.

`RetrieveModelWithDependencies`Volání vrátí nejen požadovaný model, ale také všechny modely, na kterých je požadovaný model závislý.

Modely se nemusí nutně vracet přesně do formuláře dokumentu, ve kterém byly nahrané. Digitální vlákna Azure jenom zaručuje, že návratový formulář bude sémanticky rovnocenný. 

### <a name="update-models"></a>Aktualizovat modely

Jakmile se model nahraje do instance digitálního vlákna Azure, celé rozhraní modelu je neměnné. To znamená, že neexistují tradiční "úpravy" modelů. Digitální vlákna Azure také neumožňují opakované nahrávání stejného modelu.

Místo toho, pokud chcete provádět změny modelu, jako je například aktualizace `displayName` nebo `description` – způsob, jak to provést, je nahrát **novější verzi** modelu. 

#### <a name="model-versioning"></a>Správa verzí modelů

Pokud chcete vytvořit novou verzi existujícího modelu, začněte s DTDL původního modelu. Aktualizujte, přidejte nebo odeberte pole, která chcete změnit.

Pak tuto položku označte jako novější verzi modelu tak, že aktualizujete `id` pole modelu. Poslední část ID modelu, po `;` ,, představuje číslo modelu. Chcete-li označit, že se jedná o aktualizovanou verzi tohoto modelu, zvyšte číslo na konci `id` hodnoty na libovolné číslo vyšší, než je aktuální číslo verze.

Například pokud vaše předchozí ID modelu vypadalo takto:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

verze 2 tohoto modelu může vypadat takto:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Pak nahrajte novou verzi modelu do instance. 

Tato verze modelu pak bude k dispozici v instanci, která bude použita pro digitální vlákna. Nepřepisuje **starší** verze modelu, takže dokud je [neodeberete](#remove-models), bude v instanci existovat více verzí modelu.

#### <a name="impact-on-twins"></a>Dopad na vlákna

Když vytvoříte nové vlákny, protože verze nového modelu a stará verze modelu existují současně, může nový vlákn použít buď novou verzi modelu, nebo starší verzi.

To také znamená, že nahrání nové verze modelu automaticky neovlivní stávající vlákna. Stávající vlákna budou jednoduše zachovány instance staré verze modelu.

Tyto existující vlákna můžete aktualizovat na novou verzi modelu tím, že ji aktualizujete, jak je popsáno v části [*aktualizace modelu digitálního vlákna*](how-to-manage-twin.md#update-a-digital-twins-model) v tématu *Postupy: Správa digitálních vláken*. V rámci jedné opravy je nutné aktualizovat **ID modelu** (na novou verzi) a **všechna pole, která je nutné změnit na vlákna, aby odpovídala novému modelu**.

### <a name="remove-models"></a>Odebrat modely

Modely je také možné z této služby odebrat jedním ze dvou způsobů:
* **Vyřazení z provozu** : když je model vyřazený z provozu, už ho nemůžete používat k vytváření nových digitálních vláken. Stávající digitální vlákna, které už tento model používají, to neovlivní, takže je můžete dál aktualizovat pomocí akcí, jako jsou změny vlastností a přidávání nebo odstraňování relací.
* **Odstranění** : Tato akce zcela odebere model z řešení. Všechny nepoužité vlákna, které používaly tento model, již nejsou přidruženy k žádnému platnému modelu, takže budou považovány za to, že nemají model vůbec. Tyto vlákna můžete i nadále číst, ale nebudete je moct aktualizovat, dokud nebudou znovu přiřazená k jinému modelu.

Jedná se o samostatné funkce, které nejsou navzájem ovlivněny, i když je lze použít společně k tomu, abyste model odebrali postupně. 

#### <a name="decommissioning"></a>Vyřazení

Zde je kód pro vyřazení modelu z provozu:

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

Stav vyřazení modelu z provozu je obsažen v `ModelData` záznamech vrácených rozhraními API pro načítání modelů.

#### <a name="deletion"></a>Odstranění

Můžete odstranit všechny modely ve vaší instanci najednou, nebo je můžete udělat jednotlivě.

Příklad, jak odstranit všechny modely, najdete v ukázkové aplikaci použité v tomto [*kurzu: Prozkoumejte základy s ukázkovou klientskou aplikací*](tutorial-command-line-app.md). Soubor *CommandLoop.cs* to dělá ve `CommandDeleteAllModels` funkci.

Zbývající část této části rozdělí model na více podrobností a ukazuje, jak to udělat pro jednotlivý model.

##### <a name="before-deletion-deletion-requirements"></a>Před odstraněním: požadavky na odstranění

Obecně platí, že modely lze kdykoli odstranit.

Výjimkou jsou modely, na kterých jsou závislé jiné modely, buď se `extends` vztahem, nebo jako komponentou. Pokud například model *ConferenceRoom* rozšiřuje model *Room* a má jako komponentu model *ACUnit* , nemůžete odstranit *místnost* nebo *ACUnit* , dokud *ConferenceRoom* neodstraní příslušné odkazy. 

To můžete provést tak, že aktualizujete závislý model pro odebrání závislostí nebo úplného odstranění závislého modelu.

##### <a name="during-deletion-deletion-process"></a>Během odstraňování: proces odstranění

I v případě, že model splňuje požadavky pro okamžité odstranění, možná budete chtít nejdřív projít několik kroků, abyste se vyhnuli nezamýšleným důsledkům pro vlákna, které zůstaly na pozadí. Tady je několik kroků, které vám pomůžou se správou tohoto procesu:
1. Nejprve vyřaďte model z provozu.
2. Počkejte několik minut, abyste se ujistili, že služba zpracovala všechny žádosti o vytvoření vlákna odeslané před vyřazením do vyřazení z provozu.
3. Pokud chcete zobrazit všechny vlákna, které používají model nyní-vyřazený z provozu, proveďte dotaz na vlákna podle modelu.
4. Odstraňte vlákna, pokud je už nepotřebujete, nebo je v případě potřeby opravte do nového modelu. Můžete také zvolit, že se mají ponechat samostatně. v takovém případě se po odstranění modelu stanou nevlákenou bez modelů. Dopady tohoto stavu naleznete v další části.
5. Počkejte pár minut a ujistěte se, že se změny percolated přes
6. Odstranit model 

Chcete-li odstranit model, použijte toto volání:
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>Po odstranění: vlákna bez modelů

Po odstranění modelu se teď všechny digitální vlákna, které používaly model, považují za bez modelu. Všimněte si, že není k dispozici žádný dotaz, který vám poskytne seznam všech vláken v tomto stavu, i když je stále *možné* zadat dotaz na vlákna pomocí odstraněného modelu, abyste věděli, jaké jsou zdvojené.

Tady je přehled toho, co můžete a nemůžete dělat s zdvojenými událostmi, které nemají model.

Co **můžete** udělat:
* Dotaz na dvojitou
* Číst vlastnosti
* Čtení odchozích relací
* Přidávání a odstraňování příchozích vztahů (jako v, jiné vlákna můžou stále tvořit vztahy *k* tomuto zdvojení)
  - `target`V definici vztahu stále může odrážet DTMI odstraněného modelu. Vztah bez definovaného cíle může také fungovat zde.
* Odstranit relace
* Odstranit dvojitou

Co **nemůžete** udělat:
* Úprava odchozích relací (v rámci, vztahů *od* tohoto vlákna k ostatním nevlákenám)
* Upravit vlastnosti

##### <a name="after-deletion-re-uploading-a-model"></a>Po odstranění: Opětovné nahrání modelu

Po odstranění modelu se můžete rozhodnout později, abyste nahráli nový model se stejným ID jako ten, který jste odstranili. Tady je postup, co se stane v tomto případě.
* Z perspektivy úložiště řešení je to stejné jako nahrávání zcela nového modelu. Služba si nepamatuje, že se stará z nich nahrála.   
* Pokud v grafu existují zbývající vlákna odkazující na odstraněný model, již nebudou osamocená; Toto ID modelu je znovu platné s novou definicí. Pokud je však nová definice modelu odlišná od definice modelu, která byla odstraněna, tyto vlákna mohou mít vlastnosti a relace, které odpovídají odstraněné definici a nejsou platné s novým.

Digitální vlákna Azure nebrání tomuto stavu, proto buďte opatrní na správné opravy, abyste se ujistili, že zůstanou platné prostřednictvím přepínače definice modelu.

## <a name="manage-models-with-cli"></a>Správa modelů pomocí rozhraní příkazového řádku

Modely je také možné spravovat pomocí rozhraní příkazového řádku Azure Digital revlákens CLI. Příkazy najdete v tématu [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md).

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="next-steps"></a>Další kroky

Podívejte se, jak vytvořit a spravovat digitální vlákna na základě vašich modelů:
* [*Postupy: Správa digitálních vláken*](how-to-manage-twin.md)