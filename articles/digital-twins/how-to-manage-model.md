---
title: Správa modelů DTDL
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit, upravit a odstranit model v rámci digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 75911e91bb61b84d2e9315421f794739f2b5088b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953693"
---
# <a name="manage-azure-digital-twins-models"></a>Správa modelů digitálních vláken Azure

Můžete spravovat [modely](concepts-models.md) , které vaše instance digitálního vlákna Azure ví o používání [**rozhraní DigitalTwinModels API**](/rest/api/digital-twins/dataplane/models), [sady SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client)nebo [digitálních vláken Azure](how-to-use-cli.md). 

Mezi operace správy patří nahrávání, ověřování, načítání a odstraňování modelů. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>Způsoby správy modelů

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>Vytváření modelů

Modely pro digitální vlákna Azure jsou napsané v DTDL a ukládají se jako soubory *. JSON* . K dispozici je také [rozšíření DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) pro [Visual Studio Code](https://code.visualstudio.com/), které poskytuje ověřování syntaxe a další funkce usnadňující psaní dokumentů DTDL.

Vezměte v úvahu příklad, ve kterém chce nemocnice digitálně reprezentovat své místnosti. Každá místnost obsahuje inteligentní zásobník SOAP pro sledování ručního mytí a snímačů pro monitorování provozu přes místnost.

Prvním krokem k řešení je vytvoření modelů, které reprezentují aspekty nemocnice. Místnost pro pacienty v tomto scénáři může být popsána takto:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Toto je vzorový text pro soubor. JSON, ve kterém je model definovaný a uložený, aby se nahrál jako součást klientského projektu. REST API volání na druhé straně převezme pole definic modelů, jako je výše (která je namapována na `IEnumerable<string>` rozhraní .NET SDK). Takže pokud chcete tento model použít přímo v REST API, uzavřete ho do závorek.

Tento model definuje název a jedinečné ID pro místnost pacienta a vlastnosti představující počet návštěvníků a stav ručního mytí (tyto čítače se budou aktualizovat ze senzorů pohybu a inteligentních zásobníků protokolu SOAP a společně se použijí k výpočtu *procentuální vlastnosti handwash* ). Model také definuje *hasDevices* vztahu, který se použije pro připojení všech [digitálních vláken](concepts-twins-graph.md) na základě tohoto modelu *Room* na skutečná zařízení.

V rámci této metody můžete přejít na, abyste definovali modely pro nemocnice, zóny nebo samotnou nemocnice.

### <a name="validate-syntax"></a>Ověřit syntaxi

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Nahrávání modelů

Po vytvoření modelů je můžete nahrát do instance digitálních vláken Azure.

Až budete připraveni k nahrání modelu, můžete použít následující fragment kódu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Všimněte si, že `CreateModels` Metoda přijímá více souborů v jedné transakci. Tady je ukázka, která ilustruje:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Soubory modelu mohou obsahovat více než jeden model. V takovém případě musí být modely umístěny do pole JSON. Například:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

Při nahrávání se služba ověřuje pomocí souborů modelu.

## <a name="retrieve-models"></a>Načíst modely

Můžete vypsat a načíst modely uložené ve vaší instanci digitálních vláken Azure. 

Tady jsou tyto možnosti:
* Načtení jednoho modelu
* Načíst všechny modely
* Načtení metadat a závislostí pro modely

Tady je několik příkladů volání:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Rozhraní API volá pro načtení modelů všechny návratové `DigitalTwinsModelData` objekty. `DigitalTwinsModelData` obsahuje metadata o modelu uloženém v instanci digitálních vláken Azure, jako je název, DTMI a datum vytvoření modelu. `DigitalTwinsModelData`Objekt také volitelně zahrnuje samotný model. V závislosti na parametrech lze proto použít volání metody načíst buď k načtení metadat (což je užitečné ve scénářích, kde chcete zobrazit seznam dostupných nástrojů, například), nebo celý model.

`RetrieveModelWithDependencies`Volání vrátí nejen požadovaný model, ale také všechny modely, na kterých je požadovaný model závislý.

Modely se nemusí nutně vracet přesně do formuláře dokumentu, ve kterém byly nahrané. Digitální vlákna Azure jenom zaručuje, že návratový formulář bude sémanticky rovnocenný. 

## <a name="update-models"></a>Aktualizovat modely

Jakmile se model nahraje do instance digitálního vlákna Azure, celé rozhraní modelu je neměnné. To znamená, že neexistují tradiční "úpravy" modelů. Digitální vlákna Azure také neumožňují opakované nahrávání stejného modelu.

Místo toho, pokud chcete provádět změny modelu, jako je například aktualizace `displayName` nebo `description` – způsob, jak to provést, je nahrát **novější verzi** modelu. 

### <a name="model-versioning"></a>Správa verzí modelů

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

### <a name="impact-on-twins"></a>Dopad na vlákna

Když vytvoříte nové vlákny, protože verze nového modelu a stará verze modelu existují současně, může nový vlákn použít buď novou verzi modelu, nebo starší verzi.

To také znamená, že nahrání nové verze modelu automaticky neovlivní stávající vlákna. Stávající vlákna budou jednoduše zachovány instance staré verze modelu.

Tyto existující vlákna můžete aktualizovat na novou verzi modelu tím, že ji aktualizujete, jak je popsáno v části [*aktualizace modelu digitálního vlákna*](how-to-manage-twin.md#update-a-digital-twins-model) v tématu *Postupy: Správa digitálních vláken*. V rámci jedné opravy je nutné aktualizovat **ID modelu** (na novou verzi) a **všechna pole, která je nutné změnit na vlákna, aby odpovídala novému modelu**.

## <a name="remove-models"></a>Odebrat modely

Modely je také možné z této služby odebrat jedním ze dvou způsobů:
* **Vyřazení z provozu** : když je model vyřazený z provozu, už ho nemůžete používat k vytváření nových digitálních vláken. Stávající digitální vlákna, které už tento model používají, to neovlivní, takže je můžete dál aktualizovat pomocí akcí, jako jsou změny vlastností a přidávání nebo odstraňování relací.
* **Odstranění** : Tato akce zcela odebere model z řešení. Všechny nepoužité vlákna, které používaly tento model, již nejsou přidruženy k žádnému platnému modelu, takže budou považovány za to, že nemají model vůbec. Tyto vlákna můžete i nadále číst, ale nebudete je moct aktualizovat, dokud nebudou znovu přiřazená k jinému modelu.

Jedná se o samostatné funkce, které nejsou navzájem ovlivněny, i když je lze použít společně k tomu, abyste model odebrali postupně. 

### <a name="decommissioning"></a>Vyřazení

Zde je kód pro vyřazení modelu z provozu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

Stav vyřazení modelu z provozu je obsažen v `ModelData` záznamech vrácených rozhraními API pro načítání modelů.

### <a name="deletion"></a>Odstranění

Můžete odstranit všechny modely ve vaší instanci najednou, nebo je můžete udělat jednotlivě.

Příklad, jak odstranit všechny modely, najdete v ukázkové aplikaci použité v tomto [*kurzu: Prozkoumejte základy s ukázkovou klientskou aplikací*](tutorial-command-line-app.md). Tento soubor *CommandLoop. cs* funguje ve `CommandDeleteAllModels` funkci.

Zbývající část této části rozdělí model na více podrobností a ukazuje, jak to udělat pro jednotlivý model.

#### <a name="before-deletion-deletion-requirements"></a>Před odstraněním: požadavky na odstranění

Obecně platí, že modely lze kdykoli odstranit.

Výjimkou jsou modely, na kterých jsou závislé jiné modely, buď se `extends` vztahem, nebo jako komponentou. Pokud například model *ConferenceRoom* rozšiřuje model *Room* a má jako komponentu model *ACUnit* , nemůžete odstranit *místnost* nebo *ACUnit* , dokud *ConferenceRoom* neodstraní příslušné odkazy. 

To můžete provést tak, že aktualizujete závislý model pro odebrání závislostí nebo úplného odstranění závislého modelu.

#### <a name="during-deletion-deletion-process"></a>Během odstraňování: proces odstranění

I v případě, že model splňuje požadavky pro okamžité odstranění, možná budete chtít nejdřív projít několik kroků, abyste se vyhnuli nezamýšleným důsledkům pro vlákna, které zůstaly na pozadí. Tady je několik kroků, které vám pomůžou se správou tohoto procesu:
1. Nejprve vyřaďte model z provozu.
2. Počkejte několik minut, abyste se ujistili, že služba zpracovala všechny žádosti o vytvoření vlákna odeslané před vyřazením do vyřazení z provozu.
3. Pokud chcete zobrazit všechny vlákna, které používají model nyní-vyřazený z provozu, proveďte dotaz na vlákna podle modelu.
4. Odstraňte vlákna, pokud je už nepotřebujete, nebo je v případě potřeby opravte do nového modelu. Můžete také zvolit, že se mají ponechat samostatně. v takovém případě se po odstranění modelu stanou nevlákenou bez modelů. Dopady tohoto stavu naleznete v další části.
5. Počkejte pár minut a ujistěte se, že se změny percolated přes
6. Odstranit model 

Chcete-li odstranit model, použijte toto volání:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>Po odstranění: vlákna bez modelů

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

#### <a name="after-deletion-re-uploading-a-model"></a>Po odstranění: Opětovné nahrání modelu

Po odstranění modelu se můžete rozhodnout později, abyste nahráli nový model se stejným ID jako ten, který jste odstranili. Tady je postup, co se stane v tomto případě.
* Z perspektivy úložiště řešení je to stejné jako nahrávání zcela nového modelu. Služba si nepamatuje, že se stará z nich nahrála.   
* Pokud v grafu existují zbývající vlákna odkazující na odstraněný model, již nebudou osamocená; Toto ID modelu je znovu platné s novou definicí. Pokud je však nová definice modelu odlišná od definice modelu, která byla odstraněna, tyto vlákna mohou mít vlastnosti a relace, které odpovídají odstraněné definici a nejsou platné s novým.

Digitální vlákna Azure nebrání tomuto stavu, proto buďte opatrní na správné opravy, abyste se ujistili, že zůstanou platné prostřednictvím přepínače definice modelu.

## <a name="next-steps"></a>Další kroky

Podívejte se, jak vytvořit a spravovat digitální vlákna na základě vašich modelů:
* [*Postupy: Správa digitálních vláken*](how-to-manage-twin.md)