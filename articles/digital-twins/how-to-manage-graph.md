---
title: Správa grafu dvojčat s využitím vztahů
titleSuffix: Azure Digital Twins
description: Podívejte se, jak spravovat graf digitálních vláken pomocí propojení s relacemi.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 54001cde76bd89305eb77544c6e6858defda6096
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222543"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Správa grafu digitálních vláken pomocí vztahů

Srdcem digitálních vláken Azure je [dvojitě graf](concepts-twins-graph.md) , který představuje celé prostředí. Neřízený graf se skládá z jednotlivých digitálních vláken propojených prostřednictvím **vztahů**. 

Jakmile budete mít funkční [instanci digitálních vláken Azure](how-to-set-up-instance-portal.md) a nastavili jste [ověřovací](how-to-authenticate-client.md) kód v klientské aplikaci, můžete použít [**rozhraní API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) k vytváření, úpravám a odstraňování digitálních vláken a jejich vztahů v instanci digitálních vláken Azure. Můžete také použít [rozhraní .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md).

Tento článek se zaměřuje na správu vztahů a grafu jako celku. Chcete-li pracovat s jednotlivými digitálními podseznamy, přečtěte si téma [*Postup: Správa digitálních vláken*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Způsoby správy grafu

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Můžete také provádět změny v grafu pomocí ukázky pro Průzkumníka digitálních vláken Azure (ADT), která umožňuje vizualizovat vlákna a graf a využívá sadu SDK na pozadí. V další části je podrobně popsána tato ukázka.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Vytvoření relací

Vztahy popisují, jak jsou vzájemně propojeny různé digitální vazby, které tvoří základ pro dvojitou graf.

Vztahy se vytvářejí pomocí `CreateOrReplaceRelationshipAsync()` volání. 

Chcete-li vytvořit relaci, je nutné zadat následující:
* Zdrojové ID vlákna ( `srcId` ve vzorovém kódu níže): ID vlákna, kde vztah pochází.
* Cílové ID vlákna ( `targetId` v níže uvedeném příkladu kódu): ID vlákna, do kterého relace dorazí.
* Název vztahu ( `relName` v níže uvedeném příkladu kódu): obecný typ vztahu, například _Contains_.
* ID vztahu ( `relId` v níže uvedeném příkladu kódu): konkrétní název pro tento vztah, například _Relationship1_.

ID vztahu musí být jedinečné v rámci daného zdroje vlákna. Nemusí být globálně jedinečný.
Například pro zdvojenou *foo* musí být každé konkrétní ID vztahu jedinečné. Nicméně jiný ovládací prvek s dvojitou *čárkou* může mít odchozí vztah, který odpovídá stejnému ID relace *foo* .

Následující ukázka kódu ukazuje, jak vytvořit relaci v instanci digitálních vláken Azure.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod":::

V metodě Main teď můžete zavolat `CreateRelationship()` funkci a vytvořit tak relaci, jako je  tato: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Chcete-li vytvořit více relací, můžete opakovat volání stejné metody a předáním různých typů relací do argumentu. 

Další informace o pomocné třídě `BasicRelationship` naleznete v tématu [*How to: use the Azure Digital revlákens API and SDK*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Vytvoření více vztahů mezi dvojitými hodnotami

Vztahy mohou být klasifikovány buď: 

* Odchozí vztahy: vztahy patřící k tomuto zdvojenému bodu směrem ven, aby se připojily k ostatním nevlákenám. `GetRelationshipsAsync()`Metoda slouží k získání odchozích vztahů vlákna.
* Příchozí vztahy: relace patřící k ostatním nevlákenám, které odkazují na tento objekt vláken, aby se vytvořil odkaz "příchozí". `GetIncomingRelationshipsAsync()`Metoda slouží k získání příchozích relací vlákna.

Neexistuje žádné omezení počtu vztahů, které můžete mít mezi dvěma dvojitými vláknami – můžete mít tolik vztahů mezi dvojitými možnostmi, jak budete chtít. 

To znamená, že můžete vyjádřit několik různých typů vztahů mezi dvěma dvojitými vlákna najednou. Například *Dvojitá* a může mít jak *uloženou* *relaci, tak* i vytvářený vztah s *dvojitým B*.

V případě potřeby můžete dokonce vytvořit několik instancí stejného typu relace mezi dvěma dvěma typy vláken. V tomto příkladu může mít *Dvojitá a* dvě různé *uložené* relace s *dvojitým B*, pokud mají relace různá ID vztahů.

## <a name="list-relationships"></a>Výpis relací

Chcete-li získat přístup k seznamu **odchozích** relací pro danou dvojitou hodnotu v grafu, můžete použít `GetRelationships()` metodu, například:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Vrátí `Azure.Pageable<T>` nebo `Azure.AsyncPageable<T>` , v závislosti na tom, zda používáte synchronní nebo asynchronní verzi volání.

Tady je příklad, který načte seznam vztahů:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Nyní můžete zavolat tuto metodu pro zobrazení odchozích vztahů vláken, jako jsou:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Načtené relace můžete použít k přechodu na další vlákna v grafu. Provedete to tak, že si přečtete `target` pole z vráceného vztahu a použijete ho jako ID pro vaše další volání `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Najít příchozí relace k digitálnímu vlákna

Digitální vlákna Azure také obsahuje rozhraní API pro vyhledání všech **příchozích** vztahů k danému vlákna. To je často užitečné pro zpětnou navigaci nebo při odstraňování vlákna.

Předchozí ukázka kódu se zaměřuje na hledání odchozích relací z vlákna. Následující příklad je strukturován podobně, ale místo toho najde *příchozí* relace na vlákna.

Všimněte si, že `IncomingRelationship` volání nevrátí úplný text vztahu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod":::

Nyní můžete zavolat tuto metodu, chcete-li zobrazit příchozí vztahy vláken, jako jsou tyto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Vypsat všechny zdvojené vlastnosti a vztahy

Pomocí výše uvedených metod můžete pro výpis odchozích a příchozích vztahů na vlákna vytvořit metodu, která vytiskne úplné informace, včetně vlastností vlákna a obou typů jejich vztahů. Tady je ukázková metoda, `FetchAndPrintTwinAsync()` která ukazuje, jak to udělat.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Tuto funkci teď můžete zavolat v metodě Main takto: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="delete-relationships"></a>Odstranit relace

První parametr určuje zdrojovou nevlákennou (zdvojené místo, kde relace vznikla). Druhým parametrem je ID vztahu. Potřebujete jak zdvojené ID, tak ID vztahu, protože identifikátory relací jsou jedinečné pouze v rámci rozsahu vlákna.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod":::

Nyní můžete zavolat tuto metodu a odstranit tak relaci, například:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Ukázka zdvojeného grafu spustitelný

Následující fragment kódu spustitelný používá operace vztahu z tohoto článku k vytvoření cyklického grafu z digitálních vláken a vztahů.

### <a name="set-up-the-runnable-sample"></a>Nastavení ukázky spustitelný

Fragment kódu používá [*Room.jsv*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) a [*Floor.jsv*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definicích modelů z [*kurzu: Prozkoumejte digitální vlákna Azure pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md). Pomocí těchto odkazů můžete přejít přímo na soubory nebo je stáhnout jako součást celého kompletního ukázkového [projektu.](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 

Než spustíte ukázku, udělejte toto:
1. Stáhněte si soubory modelu, umístěte je do projektu a nahraďte `<path-to>` zástupné symboly v následujícím kódu a sdělte tak programu, kde se mají najít.
2. Zástupný symbol nahraďte `<your-instance-hostname>` názvem hostitele instance digitálního vlákna Azure.
3. Přidejte do projektu dvě závislosti, které budete potřebovat pro práci s digitálními úkoly Azure. Pomocí níže uvedených odkazů můžete přejít k balíčkům v NuGet, kde můžete najít příkazy konzoly (včetně rozhraní .NET CLI) a přidat do projektu nejnovější verzi.
    * [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Toto je balíček pro [sadu Azure Digital Revlákens SDK pro .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
    * [**Azure. identity**](https://www.nuget.org/packages/Azure.Identity). Tato knihovna poskytuje nástroje, které vám pomůžou s ověřováním v Azure.

Pokud chcete ukázku spustit přímo, budete také muset nastavit místní přihlašovací údaje. V další části se to provede.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Spuštění ukázky

Po dokončení výše uvedeného postupu můžete přímo spustit následující vzorový kód.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Tady je výstup konzoly výše uvedeného programu: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Výstup na konzole zobrazuje zdvojené podrobnosti, příchozí a odchozí vztahy vláken." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Dvojitým grafem je koncept vytváření vztahů mezi vlákna. Chcete-li zobrazit vizuální znázornění s dvojitým grafem, přečtěte si část [*vizualizace*](how-to-manage-graph.md#visualization) tohoto článku. 

## <a name="create-graph-from-a-csv-file"></a>Vytvoření grafu ze souboru CSV

V praktických případech použití budou zdvojené hierarchie často vytvářeny z dat uložených v jiné databázi nebo v tabulce nebo v souboru CSV. V této části se dozvíte, jak číst data ze souboru CSV a vytvořit z něj graf s dvojitou platností.

Vezměte v úvahu následující tabulku dat s popisem sady digitálních vláken a vztahů.

|  ID modelu    | Zdvojené ID (musí být jedinečné) | Název vztahu  | Cílové ID vlákna  | Dvojitá inicializační data |
| --- | --- | --- | --- | --- |
| dtmi: Příklad: Floor; 1    | Floor1 | obsahuje | Room1 | |
| dtmi: Příklad: Floor; 1    | Floor0 | obsahuje | Room0 | |
| dtmi: Příklad: místnost; 1    | Room1 | | | {"Teplota": 80} |
| dtmi: Příklad: místnost; 1    | Room0 | | | {"Teplota": 70} |

Jedním ze způsobů, jak tato data získat do digitálních vláken Azure, je převést tabulku na soubor CSV a napsat kód pro interpretaci souboru na příkazy pro vytvoření dvojitých vláken a vztahů. Následující ukázka kódu ukazuje, jak číst data ze souboru CSV a vytvořit dvojitou graf v digitálních proobjektech Azure.

V následujícím kódu se soubor CSV nazývá *data.csv* a obsahuje zástupný text, který představuje **název hostitele** instance digitálního vlákna Azure. Ukázka také využívá několik balíčků, které můžete přidat do projektu, abyste s tímto procesem mohli pomáhat.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Další kroky

Přečtěte si o dotazování na vyzdvojený graf digitálních vláken Azure:
* [*Koncepty: dotazovací jazyk*](concepts-query-language.md)
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)