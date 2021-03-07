---
title: Správa digitálních dvojčat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak načíst, aktualizovat a odstranit jednotlivé vlákna a relace.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e20cd09ce3d9eb1937819da79cea17bdd14a07dc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433263"
---
# <a name="manage-digital-twins"></a>Správa digitálních dvojčat

Entity ve vašem prostředí jsou reprezentovány pomocí [digitálních vláken](concepts-twins-graph.md). Správa digitálních vláken může zahrnovat vytváření, úpravy a odebírání. K provedení těchto operací můžete použít [**rozhraní API DigitalTwins**](/rest/api/digital-twins/dataplane/twins), [sadu .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)nebo rozhraní příkazového [řádku Azure Digital vlákens](how-to-use-cli.md).

Tento článek se zaměřuje na správu digitálních vláken; Chcete-li pracovat se vztahy a s [dvojitým grafem](concepts-twins-graph.md) v podobě celku, přečtěte si téma [*Postup: Správa nefunkčního grafu s relacemi*](how-to-manage-graph.md).

> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Způsoby, jak spravovat vlákna

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Vytvoření digitálního vlákna

Chcete-li vytvořit dvojitou hodnotu, použijte `CreateOrReplaceDigitalTwinAsync()` metodu na klientovi služby, například:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

K vytvoření digitálního vlákna musíte zadat:
* Požadované ID digitálního vlákna
* [Model](concepts-models.md) , který chcete použít

Volitelně můžete zadat počáteční hodnoty pro všechny vlastnosti digitálního vlákna. Vlastnosti jsou považovány za volitelné a lze je nastavit později, ale **nebudou zobrazeny jako součást vlákna, dokud nebudou nastaveny.**

>[!NOTE]
>I když nemusíte inicializovat zdvojené vlastnosti, musí být při vytvoření **vlákna nastaveny** všechny [komponenty](concepts-models.md#elements-of-a-model) na vlákna. Můžou to být prázdné objekty, ale samotné komponenty musí existovat.

Model a jakékoli počáteční hodnoty vlastností jsou k dispozici prostřednictvím `initData` parametru, což je řetězec JSON obsahující relevantní data. Další informace o strukturování tohoto objektu získáte, když budete pokračovat k další části.

> [!TIP]
> Po vytvoření nebo aktualizaci vlákna může být latence až 10 sekund, než se změny projeví v [dotazech](how-to-query-graph.md). `GetDigitalTwin`Rozhraní API (popsané [dále v tomto článku) v](#get-data-for-a-digital-twin)této prodlevě nefunguje, takže pokud potřebujete okamžitou reakci, použijte volání rozhraní API namísto dotazování, abyste viděli nově vytvořené vlákna. 

### <a name="initialize-model-and-properties"></a>Inicializace modelu a vlastností

Můžete inicializovat vlastnosti vlákna v době, kdy je vytvořena dvojitá vlákna. 

Rozhraní API pro vytvoření vlákna přijímá objekt, který je serializován do platného popisu JSON vlastností. V tématu [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md) pro Popis formátu JSON pro dvojitou hodnotu. 

Nejprve můžete vytvořit datový objekt, který bude představovat vlákna a data vlastností. Můžete vytvořit objekt parametru buď ručně, nebo pomocí poskytnuté pomocné třídy. Tady je příklad každé z nich.

#### <a name="create-twins-using-manually-created-data"></a>Vytváření dvojitých vláken pomocí ručně vytvořených dat

Bez použití vlastních pomocných tříd můžete reprezentovat vlastnosti vlákna v `Dictionary<string, object>` , kde `string` je název vlastnosti a `object` je objekt představující vlastnost a její hodnotu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Vytvoření vláken s podpůrnou třídou

Pomocná třída `BasicDigitalTwin` umožňuje ukládat pole vlastností přímo do objektu "vlákna". Přesto můžete chtít sestavit seznam vlastností pomocí `Dictionary<string, object>` , který lze následně přidat k dodanému objektu jako `CustomProperties` přímo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` objekty jsou dodávány s `Id` polem. Toto pole můžete nechat prázdné, ale pokud přidáte hodnotu ID, musí se shodovat s parametrem ID předaným `CreateOrReplaceDigitalTwinAsync()` volání. Například:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Získání dat z digitálního vlákna

Můžete získat přístup k podrobnostem jakéhokoliv digitálního vlákna voláním `GetDigitalTwin()` metody, jako je tato:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Toto volání vrátí dvojitá data jako typ objektu silně typovaného typu, jako je například `BasicDigitalTwin` . `BasicDigitalTwin` je pomocná třída serializace, která je součástí sady SDK, která vrátí základní a vlastnosti s dvojitou analýzou. Tady je příklad toho, jak se má použít k zobrazení podrobností o zdvojených událostech:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Po načtení vlákna s metodou se vrátí pouze vlastnosti, které byly nastaveny alespoň jednou `GetDigitalTwin()` .

>[!TIP]
>`displayName`Pro objekt, který je pro vlákna, je součástí metadat modelu, takže se při získávání dat pro dvojitou instanci nebude zobrazovat. Chcete-li zobrazit tuto hodnotu, můžete [ji načíst z modelu](how-to-manage-model.md#retrieve-models).

Chcete-li načíst více vláken pomocí jediného volání rozhraní API, přečtěte si příklady rozhraní API pro dotazování v tématu [*Postupy: vytvoření dotazu na nevlákenný graf*](how-to-query-graph.md).

Vezměte v úvahu následující model (napsaný v [digitálním DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL), který definuje *měsíc*:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

Výsledek volání `object result = await client.GetDigitalTwinAsync("my-moon");` na vlákna typu *měsíc* může vypadat takto:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Definované vlastnosti digitálního vlákna jsou vráceny jako vlastnosti nejvyšší úrovně u digitálního vlákna. Metadata nebo systémové informace, které nejsou součástí definice DTDL, se vrátí s `$` předponou. Mezi vlastnosti metadat patří:
* ID digitálního vlákna v této instanci digitálních vláken Azure, jako je `$dtId` .
* `$etag`, standardní pole HTTP přiřazené webovým serverem.
* Další vlastnosti v `$metadata` oddílu. Tady jsou některé z nich:
    - DTMI modelu digitálního vlákna.
    - Stav synchronizace pro každou zapisovatelnou vlastnost. To je nejužitečnější pro zařízení, kde je možné, že služba a zařízení mají Rozbíhající se stavy (například když je zařízení offline). V současné době se tato vlastnost vztahuje pouze na fyzická zařízení připojená k IoT Hub. S daty v části metadata je možné pochopit úplný stav vlastnosti a také poslední změněná časová razítka. Další informace o stavu synchronizace najdete v [tomto IoT Hub kurzu](../iot-hub/tutorial-device-twins.md) synchronizace stavu zařízení.
    - Metadata specifická pro službu, například z IoT Hub nebo z digitálních vláken Azure. 

Můžete si přečíst další informace o pomocných třídách serializace, jako `BasicDigitalTwin` v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Zobrazit všechny digitální vlákna

Chcete-li zobrazit všechny digitální vlákna ve vaší instanci, použijte [dotaz](how-to-query-graph.md). Dotaz můžete spustit pomocí [rozhraní API pro dotazy](/rest/api/digital-twins/dataplane/query) nebo [příkazů CLI](how-to-use-cli.md).

Zde je text základního dotazu, který vrátí seznam všech digitálních vláken v instanci:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Aktualizace digitálního vlákna

Chcete-li aktualizovat vlastnosti digitálního vlákna, zapište informace, které chcete nahradit ve formátu [opravy JSON](http://jsonpatch.com/) . Tímto způsobem můžete nahradit více vlastností najednou. Pak předáte dokument opravy JSON do `UpdateDigitalTwin()` metody:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Volání opravy může aktualizovat libovolný počet vlastností u jediného vlákna, jak byste chtěli (dokonce i u všech). Pokud potřebujete aktualizovat vlastnosti v rámci více vláken, budete potřebovat samostatné volání aktualizace pro každý z vláken.

> [!TIP]
> Po vytvoření nebo aktualizaci vlákna může být latence až 10 sekund, než se změny projeví v [dotazech](how-to-query-graph.md). `GetDigitalTwin`Rozhraní API (popsané [dříve v tomto článku) v](#get-data-for-a-digital-twin)této prodlevě nefunguje, proto použijte volání rozhraní API namísto dotazování, abyste viděli nově aktualizovaná vlákna, pokud potřebujete okamžitou reakci. 

Tady je příklad kódu opravy JSON. Tento dokument nahrazuje hodnoty  vlastností Replace a *poloměru* digitálního vlákna, na které se aplikuje.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Můžete vytvářet opravy pomocí [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument)sady Azure .NET SDK. Zde je příklad.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Aktualizovat vlastnosti v digitálních nevlákenných komponentách

Odvolání, že model může obsahovat komponenty a umožňuje, aby byl vytvořen z dalších modelů. 

Chcete-li opravit vlastnosti v komponentách digitálního vlákna, můžete použít syntaxi cesty v opravě JSON:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Aktualizace modelu digitálního vlákna

`UpdateDigitalTwin()`Funkci lze také použít k migraci digitálního vlákna na jiný model. 

Zvažte například následující dokument opravy JSON, který nahrazuje pole metadat digitálního vlákna `$model` :

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Tato operace proběhne úspěšně pouze v případě, že digitální práce, která je upravována opravou, bude v souladu s novým modelem. 

Uvažujte následující příklad:
1. Představte si digitální dvojitou hodnotu s modelem *foo_old*. *foo_old* definuje požadovanou *hmotnost* vlastnosti.
2. Nový model *foo_new* definuje hmotnostní vlastnost a přidává novou *teplotu* požadované vlastnosti.
3. Po opravě musí mít digitální vlákna současně vlastnost pro hmotnost i teplotu. 

Oprava pro tuto situaci musí aktualizovat model i vlastnost teploty vlákna, například takto:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Zpracování konfliktních volání aktualizace

Digitální vlákna Azure zajišťuje, že se všechny příchozí žádosti zpracovávají jednou po druhém. To znamená, že i v případě, že se více funkcí pokusí aktualizovat stejnou vlastnost na vlákna ve stejnou dobu, není **nutné** psát explicitní kód uzamykání pro zpracování konfliktu.

Toto chování je na základě vlákna. 

Představte si například scénář, ve kterém tato tři volání dorazí ve stejnou dobu: 
*   Zapsat vlastnost A v *Twin1*
*   Zapsat vlastnost B v *Twin1*
*   Zapsat vlastnost A v *Twin2*

Dvě volání, která mění *Twin1* , se spustí jednou po druhém a při každé změně se vygenerují zprávy o změně. Volání úpravy *Twin2* může být spuštěno souběžně bez konfliktu, jakmile bude dodáno.

## <a name="delete-a-digital-twin"></a>Odstranění digitálního vlákna

Můžete odstranit vlákna pomocí `DeleteDigitalTwin()` metody. Můžete však odstranit pouze dvojitou hodnotu, pokud nemá žádné další relace. Proto nejprve odstraňte příchozí a odchozí vztahy vlákna.

Zde je příklad kódu pro odstranění vláken a jejich vztahů. `DeleteDigitalTwin`Volání sady SDK je zvýrazněno k objasnění, kde se nachází v kontextu širšího příkladu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Odstranit všechny digitální vlákna

Příklad, jak odstranit všechny vlákna najednou, si můžete stáhnout ukázkovou aplikaci používanou v tomto [*kurzu: Prozkoumejte základy s ukázkovou klientskou aplikací*](tutorial-command-line-app.md). Soubor *CommandLoop.cs* to dělá ve `CommandDeleteAllTwins()` funkci.

## <a name="runnable-digital-twin-code-sample"></a>Ukázka digitálního zdvojeného kódu spustitelný

Následující příklad kódu spustitelný můžete použít k vytvoření vlákna, aktualizaci jeho podrobností a odstranění vlákna. 

### <a name="set-up-the-runnable-sample"></a>Nastavení ukázky spustitelný

Fragment kódu používá [Room.jsv](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definici modelu z [*kurzu: Prozkoumejte digitální vlákna Azure pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md). Pomocí tohoto odkazu můžete přejít přímo k souboru [nebo si ho](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)stáhnout jako součást celého uceleného ukázkového projektu.

Než spustíte ukázku, udělejte toto:
1. Stáhněte si soubor modelu, umístěte ho do projektu a nahraďte `<path-to>` zástupný symbol v následujícím kódu a sdělte tak programu, kde se má najít.
2. Zástupný symbol nahraďte `<your-instance-hostname>` názvem hostitele instance digitálního vlákna Azure.
3. Přidejte do projektu dvě závislosti, které budete potřebovat pro práci s digitálními úkoly Azure. První je balíček pro [sadu Azure Digital prokážed SDK pro .NET](/dotnet/api/overview/azure/digitaltwins/client), druhá nabízí nástroje, které vám pomůžou s ověřováním v Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Pokud chcete ukázku spustit přímo, budete také muset nastavit místní přihlašovací údaje. V další části se to provede.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Spuštění ukázky

Po dokončení výše uvedeného postupu můžete přímo spustit následující vzorový kód.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Tady je výstup konzoly výše uvedeného programu: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Výstup na konzole ukazující, že se vytvoří, aktualizuje a odstraní zdvojený" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Další kroky

Podívejte se, jak vytvořit a spravovat vztahy mezi vašimi digitálními dvojitými hodnotami:
* [*Postupy: Správa dvojitých grafů s relacemi*](how-to-manage-graph.md)