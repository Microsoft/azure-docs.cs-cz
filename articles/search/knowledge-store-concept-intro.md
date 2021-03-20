---
title: Koncepty znalostní báze Knowledge Store
titleSuffix: Azure Cognitive Search
description: Posílání obohacených dokumentů na Azure Storage, kde můžete zobrazit, změnit tvar a využívat obohacené dokumenty v Azure Kognitivní hledání a v jiných aplikacích.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924295"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Znalostní báze ve službě Azure Kognitivní hledání

Znalostní báze je funkce služby Azure Kognitivní hledání, která uchovává výstup [kanálu rozšíření AI](cognitive-search-concept-intro.md) pro nezávislou analýzu nebo zpracování pro příjem dat. *Obohacený dokument* je výstup kanálu vytvořený z obsahu, který byl extrahován, strukturovaný a analyzován pomocí procesů AI. V standardním kanálu AI jsou obohacené dokumenty přechodné, používané jenom při indexování a pak se zahodí. Zvolíte-li vytvoření znalostní databáze, budete moci zachovat obohacené dokumenty. 

Pokud jste v minulosti používali rozpoznávání vnímání, již víte, že *dovednosti* přesouvá dokument v rámci posloupnosti rozšíření. Výsledkem může být index vyhledávání nebo projekce ve znalostní bázi. Dva výstupy, vyhledávací index a znalostní obchod jsou produkty stejného kanálu. odvozeno ze stejných vstupů, ale vede výstup, který je strukturovaný, uložený a používá se velmi různými způsoby.

Znalostní báze Knowledge Store je fyzicky [Azure Storage](../storage/common/storage-account-overview.md), buď úložiště tabulek Azure, Azure Blob Storage, nebo obojí. Libovolný nástroj nebo proces, který se může připojit k Azure Storage může využívat obsah znalostní báze Knowledge Store.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Znalostní úložiště v diagramu kanálu")

## <a name="benefits-of-knowledge-store"></a>Výhody znalostní báze Knowledge Store

Znalostní báze poskytuje strukturu, kontext a skutečný obsah – mohli z nestrukturovaných a částečně strukturovaných datových souborů, jako jsou objekty blob, soubory obrázků, které prošly analýzou, nebo dokonce strukturovaná data, která se přetvarují na nové formuláře. V [podrobném návodu](knowledge-store-create-rest.md)se můžete podívat, jak se má zhuštěný dokument JSON rozdělit do podstruktur, rekládat do nových struktur a jinak zpřístupnit pro podřízené procesy, jako jsou Machine Learning a úlohy datových věd.

I když je užitečné zjistit, co kanál pro obohacení AI může vytvořit, skutečný potenciál úložiště Knowledge Store je možnost změnit tvarování dat. Můžete začít se základní dovednosti a potom iterovat přes něj a přidat tak zvýšené úrovně struktury, které pak můžete zkombinovat do nových struktur, a to i v jiných aplikacích než Azure Kognitivní hledání.

Ve výčtu jsou výhody znalostní báze Knowledge Store následující:

+ Využívání obohacených dokumentů v [nástrojích pro analýzu a vytváření sestav](#tools-and-apps) kromě hledání. Power BI s Power Query je přesvědčivá volba, ale jakýkoli nástroj nebo aplikace, které se můžou připojit k Azure Storage, můžou vyžádat z úložiště, které vytvoříte.

+ Upřesnění kanálu indexování AI při ladění kroků a definicí dovednosti. Znalostní báze Knowledge Store vám ukáže produkt definice dovednosti v kanálu indexování AI. Pomocí těchto výsledků můžete navrhnout lepší dovednosti, protože vidíte přesně to, co vypadá rozšíření. V Azure Storage můžete použít [Průzkumník služby Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) k zobrazení obsahu úložiště Knowledge Store.

+ Natvarujte data do nových formulářů. Změna tvaru je kodifikována v dovednosti, ale v tomto bodě je to, že dovednosti může tuto funkci nyní poskytnout. [Shaper dovednosti](cognitive-search-skill-shaper.md) v Azure kognitivní hledání se rozšířily tak, aby odpovídaly této úloze. Změna tvaru umožňuje definovat projekci, která se zarovnává s zamýšleným použitím dat při zachování vztahů.

> [!Note]
> Novinka pro rozšíření AI a vnímání zkušeností? Azure Kognitivní hledání se integruje s funkcemi Cognitive Services Vision a Language pro extrakci a obohacení zdrojových dat pomocí optického rozpoznávání znaků (OCR) přes soubory obrázků, rozpoznávání entit a extrakce klíčových frází z textových souborů a dalších. Další informace najdete v tématu věnovaném [rozšíření AI v Azure kognitivní hledání](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fyzické úložiště


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


Fyzický výraz úložiště znalostí je kloubem prostřednictvím `projections` elementu `knowledgeStore` definice v dovednosti. Projekce definuje strukturu výstupu tak, aby odpovídala zamýšlenému použití.

Projekce lze nakloubovat jako tabulky, objekty nebo soubory.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Typ projekce, kterou zadáte v této struktuře, určuje typ úložiště používaný službou Knowledge Store.

+ Tabulka úložiště se používá při definování `tables` . Definujte projekci tabulky, když potřebujete struktury tabulkového vytváření sestav pro vstupy do analytických nástrojů nebo exportovat jako datové snímky do jiných úložišť dat. Můžete zadat vícenásobné `tables` pro získání podmnožiny nebo průřezu obohacených dokumentů. V rámci stejné skupiny projekce jsou vztahy mezi tabulkami zachované, takže můžete pracovat se všemi nimi.

+ Úložiště objektů BLOB se používá při definování `objects` nebo `files` . Fyzická reprezentace prvku `object` je hierarchická struktura JSON, která představuje obohacený dokument. A `file` je obrázek extrahovaný z dokumentu, který se přenese beze změny do úložiště objektů BLOB.

Jeden objekt projekce obsahuje jednu sadu `tables` ,, a `objects` `files` pro mnoho scénářů může vytvoření jedné projekce stačit. 

Je ale možné vytvořit několik sad `table` - `object` - `file` projekce a můžete to udělat, pokud chcete použít různé datové vztahy. V rámci sady se data vztahují, za předpokladu, že tyto relace existují a lze je zjistit. Pokud vytvoříte další sady, dokumenty v každé skupině se nikdy netýkají. Příkladem použití více skupin projekce může být, pokud chcete, aby se stejná data mohla zajímat pro použití s vaším online systémem, a je potřeba, aby se stejná data mohla vystupovat v kanálu pro datové vědy, který je reprezentován jiným způsobem.

## <a name="requirements"></a>Požadavky 

[Azure Storage](../storage/index.yml) se vyžaduje. Poskytuje fyzické úložiště. Můžete použít úložiště objektů blob, tabulkové úložiště nebo obojí. Úložiště objektů BLOB se používá pro nedotčené rozšířené dokumenty, většinou když výstup probíhá na navazující procesy. Table Storage je pro řezy obohacených dokumentů, které se běžně používají k analýze a vytváření sestav.

[Dovednosti](cognitive-search-working-with-skillsets.md) je povinný. Obsahuje definici a `knowledgeStore` určuje strukturu a složení obohaceného dokumentu. Nemůžete vytvořit úložiště znalostí pomocí prázdného dovednosti. Musíte mít aspoň jednu dovednost v dovednosti.

[Indexer](search-indexer-overview.md) je povinný. Dovednosti je vyvolán indexerem, který řídí spuštění. Indexery jsou dodávány s vlastní sadou požadavků a atributů. Některé z těchto atributů mají přímý vliv na znalostní bázi Knowledge Store:

+ Indexery vyžadují [podporovaný zdroj dat Azure](search-indexer-overview.md#supported-data-sources) (kanál, který nakonec vytvoří službu Knowledge Store, začne vyvoláním dat z podporovaného zdroje v Azure). 

+ Indexery vyžadují index vyhledávání. Indexer vyžaduje, abyste zadali schéma indexu, a to i v případě, že ho nikdy neplánujete použít. Minimální index obsahuje jedno pole řetězce určené jako klíč.

+ Indexery poskytují volitelná mapování polí, která se používají pro vytvoření aliasu zdrojového pole do cílového pole. Pokud mapování výchozích polí vyžaduje změnu (Pokud chcete použít jiný název nebo typ), můžete v indexeru vytvořit [mapování polí](search-indexer-field-mappings.md) . Pro výstup ve znalostní bázi Store může být cílem pole v objektu BLOB nebo v tabulce.

+ Indexery mají plány a další vlastnosti, jako jsou například mechanismy detekce změn poskytované různými zdroji dat, lze také použít pro znalostní bázi Knowledge Store. Například můžete [naplánovat](search-howto-schedule-indexers.md) rozšíření v pravidelných intervalech, abyste mohli aktualizovat obsah. 

## <a name="how-to-create-a-knowledge-store"></a>Postup vytvoření úložiště znalostí

K vytvoření úložiště Knowledge Store použijte portál nebo REST API ( `api-version=2020-06-30` ).

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Průvodce **importem dat** obsahuje možnosti pro vytvoření úložiště znalostí. V případě prvotního průzkumu [Vytvořte své první znalostní báze ve čtyřech krocích](knowledge-store-connect-power-bi.md).

1. Vyberte podporovaný zdroj dat.

1. Zadat rozšíření: připojit prostředek, vybrat dovednosti a zadat úložiště znalostí. 

1. Vytvořte schéma indexu. Průvodce ho vyžaduje a může pro vás odvodit.

1. Spusťte průvodce. V tomto posledním kroku dojde k extrakci, obohacení a ukládání.

### <a name="use-create-skillset-rest-api"></a>Použít Create dovednosti (REST API)]

A `knowledgeStore` je definován v rámci objektu [dovednosti](cognitive-search-working-with-skillsets.md), který je zase volán [indexerem](search-indexer-overview.md). Během obohacení Azure Kognitivní hledání ve vašem účtu Azure Storage vytvoří prostor a projekty obohacených dokumentů jako objekty blob nebo do tabulek v závislosti na vaší konfiguraci.

REST API je jedním z mechanismů, pomocí nichž můžete vytvořit úložiště znalostí programově. Snadným způsobem, jak prozkoumat, je [vytvořit první znalostní bázi Knowledge Store pomocí post a REST API](knowledge-store-create-rest.md).

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Jak se připojit pomocí nástrojů a aplikací

Jakmile rozšíření existují v úložišti, můžete použít jakýkoli nástroj nebo technologii, která se připojuje ke službě Azure Blob nebo Table Storage, k prozkoumávání, analýze nebo využívání obsahu. Následující seznam je začátek:

+ [Průzkumník služby Storage](knowledge-store-view-storage-explorer.md) k zobrazení obohacené struktury dokumentu a obsahu. Vezměte ho jako základní nástroj pro zobrazení obsahu znalostní databáze.

+ [Power BI](knowledge-store-connect-power-bi.md) pro vytváření sestav a analýzy. 

+ [Azure Data Factory](../data-factory/index.yml) pro další manipulaci.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API – referenční informace

REST API verze `2020-06-30` poskytuje znalostní bázi s dalšími definicemi na dovednosti. Kromě odkazu najdete informace o tom, jak volat rozhraní API, v tématu  [Vytvoření úložiště znalostí pomocí služby post](knowledge-store-create-rest.md) .

+ [Create dovednosti (rozhraní API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Update dovednosti (rozhraní API-Version = 2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>Další kroky

Znalostní báze Knowledge Store nabízí persistenci obohacených dokumentů, které jsou užitečné při navrhování dovednosti nebo vytváření nových struktur a obsahu pro použití všemi klientskými aplikacemi, které mají přístup k Azure Storagemu účtu.

Nejjednodušším přístupem k vytváření obohacených dokumentů je [portál](knowledge-store-create-portal.md), ale můžete také použít post a REST API, což je užitečnější, pokud chcete získat přehled o způsobu vytváření a odkazů na objekty.

> [!div class="nextstepaction"]
> [Vytvoření úložiště znalostí pomocí post a REST](knowledge-store-create-rest.md)

Další informace o projekcich, možnostech a způsobu [jejich definování ve dovednosti](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projekce ve znalostní bázi Knowledge Store](knowledge-store-projection-overview.md)

Kurz týkající se pokročilých konceptů projekce, jako je například vytváření řezů, line Shaping a Relationships, Začínáme s [definováním projekce ve znalostní bázi Knowledge Store](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definování projekce ve znalostní bázi Knowledge Store](knowledge-store-projections-examples.md)