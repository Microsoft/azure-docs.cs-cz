---
title: Úvod do úložiště znalostí (náhled)
titleSuffix: Azure Cognitive Search
description: Posílejte obohacené dokumenty do Azure Storage, kde můžete zobrazit, přetvořit a využívat obohacené dokumenty v Azure Cognitive Search a v jiných aplikacích. Tato funkce je ve verzi Public Preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943000"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Úvod do úložišť znalostí v Azure Cognitive Search

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Úložiště znalostí je funkce Azure Cognitive Search, která přetrvává výstup z [kanálu obohacení AI](cognitive-search-concept-intro.md) pro nezávislé analýzy nebo následné zpracování. *Obohacený dokument* je výstup kanálu, vytvořený z obsahu, který byl extrahován, strukturován a analyzován pomocí procesů AI. Ve standardním kanálu AI jsou rozšířené dokumenty přechodné, používané pouze během indexování a poté zahozeny. Díky úložišti znalostí jsou zachovány obohacené dokumenty. 

Pokud jste v minulosti používali kognitivní dovednosti, již víte, že *dovednosti* přesouvají dokument prostřednictvím sekvence obohacení. Výsledkem může být index vyhledávání nebo (nový v tomto náhledu) projekce v úložišti znalostí. Tyto dva výstupy, index vyhledávání a úložiště znalostí, jsou produkty stejného kanálu; odvozenze stejných vstupů, ale výsledkem je výstup, který je strukturován, uložen a používán velmi odlišnými způsoby.

Fyzicky úložiště znalostí je [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), buď Azure Table storage, Azure Blob storage nebo obojí. Jakýkoli nástroj nebo proces, který se může připojit k Azure Storage, může spotřebovat obsah úložiště znalostí.

![Úložiště znalostí v diagramu potrubí](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Úložiště znalostí v diagramu potrubí")

## <a name="benefits-of-knowledge-store"></a>Výhody úložiště znalostí

Úložiště znalostí poskytuje strukturu, kontext a skutečný obsah – získaný z nestrukturovaných a částečně strukturovaných datových souborů, jako jsou objekty BLOB, obrazové soubory, které prošly analýzou, nebo dokonce strukturovaná data, přetvořené do nových forem. V [podrobném návodu](knowledge-store-create-rest.md)můžete z první ruky zobrazit, jak je hustý dokument JSON rozdělen do podstruktur, rekonstituován do nových struktur a jinak k dispozici pro podřízené procesy, jako je strojové učení a úlohy datové vědy.

I když je užitečné zjistit, co může vytvořit kanál obohacení umělou ai, skutečným potenciálem úložiště znalostí je schopnost přetvořit data. Můžete začít se základní sadou dovedností a pak ji iterate přes něj přidat rostoucí úrovně struktury, které pak můžete kombinovat do nových struktur, spotřební doplněk v jiných aplikacích kromě Azure Cognitive Search.

Ve výčtu, výhody úložiště znalostí patří následující:

+ Využijte obohacené dokumenty v [jiných analytických nástrojích a nástrojích pro vytváření sestav,](#tools-and-apps) než je vyhledávání. Power BI s Power Query je přesvědčivá volba, ale jakýkoli nástroj nebo aplikace, které se můžou připojit k Azure Storage, můžou vytáhnout z úložiště znalostí, které vytvoříte.

+ Upřesněte kanál indexování ai při ladění kroků a definice dovedností. Úložiště znalostí zobrazuje součin definice sady dovedností v kanálu indexování AI. Tyto výsledky můžete použít k návrhu lepší dovednosti, protože můžete přesně vidět, jak obohacení vypadat. [Pomocí Průzkumníka úložiště](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) ve službě Azure Storage můžete zobrazit obsah úložiště znalostí.

+ Vytvarujte data do nových formulářů. Přetváření je kodifikováno v skillsets, ale jde o to, že skillset může nyní poskytnout tuto schopnost. [Shaper dovednosti](cognitive-search-skill-shaper.md) v Azure Cognitive Search byla rozšířena tak, aby vyhovovaly této úloze. Změna tvaru umožňuje definovat projekci, která je zarovnána s zamýšleným použitím dat při zachování vztahů.

> [!Note]
> Začínáte s obohacením a kognitivními dovednostmi a umělou a neodprádkou? Azure Cognitive Search se integruje s funkcemi Cognitive Services Vision a Language pro extrahování a obohacování zdrojových dat pomocí optického rozpoznávání znaků (OCR) přes obrazové soubory, rozpoznávání entit a extrakci klíčových frází z textových souborů a další. Další informace najdete [v tématu Obohacení umělou přípravou v Azure Cognitive Search](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fyzické skladování

Fyzické vyjádření úložiště znalostí je formulována prostřednictvím `projections` prvku `knowledgeStore` definice v Skillset. Projekce definuje strukturu výstupu tak, aby odpovídala zamýšlenému použití.

Projekce mohou být formulovány jako tabulky, objekty nebo soubory.

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

Typ projekce, který zadáte v této struktuře určuje typ úložiště používanéúložiště znalostí.

+ Úložiště tabulky se používá `tables`při definování . Definujte projekci tabulky, když potřebujete struktury tabulkových vykazování pro vstupy do analytických nástrojů nebo exportujte jako datové rámce do jiných úložišť dat. Můžete zadat `tables` více, abyste získali podmnožinu nebo průřez obohacených dokumentů. V rámci stejné skupiny projekce jsou relace mezi tabulkami zachovány, takže můžete pracovat se všemi z nich.

+ Úložiště objektů blob se `objects` `files`používá při definování nebo . Fyzická reprezentace `object` je hierarchická struktura JSON, která představuje obohacený dokument. A `file` je obrázek extrahovaný z dokumentu, přenesený beze změny do úložiště objektů Blob.

Jeden objekt projekce obsahuje `tables`jednu `files`sadu , `objects`, a pro mnoho scénářů může stačit vytvoření jedné projekce. 

Je však možné vytvořit více `table` - `object` - `file` sad projekcí a můžete tak učinit, pokud chcete různé datové vztahy. V rámci sady jsou data příbuzná za předpokladu, že tyto vztahy existují a mohou být detekovány. Pokud vytvoříte další sady, dokumenty v každé skupině spolu nikdy nesouvisejí. Příkladem použití více projekčních skupin může být, pokud chcete, aby stejná data promítla pro použití s online systémem a musí být reprezentována určitým způsobem, chcete také stejná data promítnutá pro použití v kanálu datové vědy, který je reprezentován Jinak.

## <a name="requirements"></a>Požadavky 

[Azure Storage](https://docs.microsoft.com/azure/storage/) je povinné. Poskytuje fyzické úložiště. Můžete použít úložiště objektů blob, úložiště tabulek nebo obojí. Úložiště objektů blob se používá pro neporušené rozšířené dokumenty, obvykle když výstup bude na vazovém přenosu procesů. Úložiště tabulek je pro řezy obohacených dokumentů, běžně používané pro analýzu a vytváření sestav.

[Skillset](cognitive-search-working-with-skillsets.md) je vyžadována. Obsahuje definici `knowledgeStore` a určuje strukturu a složení obohaceného dokumentu. Úložiště znalostí nelze vytvořit pomocí prázdné sady dovedností. Musíte mít alespoň jednu dovednost v skillset.

Je vyžadován [indexer.](search-indexer-overview.md) Skillset je vyvolána indexer, který řídí provádění. Indexery jsou dodávány s vlastní sadou požadavků a atributů. Některé z těchto atributů mají přímý vliv na úložiště znalostí:

+ Indexery vyžadují [podporovaný zdroj dat Azure](search-indexer-overview.md#supported-data-sources) (kanál, který nakonec vytvoří úložiště znalostí spustí natahání dat z podporovaného zdroje v Azure). 

+ Indexery vyžadují index vyhledávání. Indexer vyžaduje, abyste zadali schéma indexu, i když nikdy neplánujete jeho použití. Minimální index má jedno pole řetězce, označené jako klíč.

+ Indexery poskytují volitelná mapování polí používaná k aliasu zdrojového pole cílovému poli. Pokud výchozí mapování polí vyžaduje změnu (chcete-li použít jiný název nebo typ), můžete vytvořit [mapování polí](search-indexer-field-mappings.md) v rámci indexeru. Pro výstup úložiště znalostí může být cílem pole v objektu objektu blob nebo tabulce.

+ Indexery mají plány a další vlastnosti, jako jsou mechanismy zjišťování změn poskytované různými zdroji dat, lze také použít pro úložiště znalostí. Můžete například [naplánovat](search-howto-schedule-indexers.md) obohacení v pravidelných intervalech aktualizovat obsah. 

## <a name="how-to-create-a-knowledge-store"></a>Jak vytvořit úložiště znalostí

Chcete-li vytvořit úložiště znalostí, použijte`api-version=2019-05-06-Preview`portál nebo rozhraní REST API náhledu ( ).

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Průvodce **importem dat** obsahuje možnosti pro vytvoření úložiště znalostí. Pro počáteční průzkum [vytvořte svůj první úložiště znalostí ve čtyřech krocích](knowledge-store-connect-power-bi.md).

1. Vyberte podporovaný zdroj dat.

1. Určete obohacení: připojte zdroj, vyberte dovednosti a určete úložiště znalostí. 

1. Vytvořte schéma indexu. Průvodce to vyžaduje a může odvodit jeden pro vás.

1. Spusťte průvodce. Extrakce, obohacení a úložiště dojít v tomto posledním kroku.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Použití sady dovedností a rozhraní REST API náhledu

A `knowledgeStore` je definována v rámci [skillset](cognitive-search-working-with-skillsets.md), který je vyvolán [indexerem](search-indexer-overview.md). Během obohacení Azure Cognitive Search vytvoří místo ve vašem účtu Azure Storage a projekty obohacené dokumenty jako objekty BLOB nebo do tabulek, v závislosti na konfiguraci.

V současné době preview ROZHRANÍ REST API je jediný mechanismus, pomocí kterého můžete vytvořit úložiště znalostí programově. Snadný způsob, jak prozkoumat, je [vytvořit svůj první úložiště znalostí pomocí Postman a REST API](knowledge-store-create-rest.md).

Referenční obsah pro tuto funkci náhledu je umístěn v [části referenční rozhraní API](#kstore-rest-api) tohoto článku. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Jak se spojit s nástroji a aplikacemi

Jakmile obohacení existují v úložišti, jakýkoli nástroj nebo technologie, která se připojuje k azure blob nebo table storage lze prozkoumat, analyzovat nebo spotřebovávat obsah. Následující seznam je začátek:

+ [Průzkumník úložiště](knowledge-store-view-storage-explorer.md) pro zobrazení obohacené struktury dokumentů a obsahu. Považujte to za základní nástroj pro zobrazení obsahu úložiště znalostí.

+ [Power BI](knowledge-store-connect-power-bi.md) pro vytváření sestav a analýzy. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) pro další manipulaci.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>referenční dokumentace k rozhraní API

VERZE ROZHRANÍ `2019-05-06-Preview` REST API poskytuje úložiště znalostí prostřednictvím dalších definic na skillsets. Kromě odkazu najdete v [tématu Vytvoření úložiště znalostí pomocí Postman](knowledge-store-create-rest.md) podrobnosti o tom, jak volat api.

+ [Vytvořit skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Aktualizovat skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Další kroky

Úložiště znalostí nabízí trvalost obohacených dokumentů, užitečné při navrhování skillset nebo vytváření nových struktur a obsahu pro spotřebu všechny klientské aplikace schopné přístupu k účtu Azure Storage.

Nejjednodušší přístup pro vytváření obohacených dokumentů je [prostřednictvím portálu](knowledge-store-create-portal.md), ale můžete také použít Postman a REST API, což je užitečnější, pokud chcete získat přehled o tom, jak jsou vytvářeny a odkazovány na objekty.

> [!div class="nextstepaction"]
> [Vytvoření úložiště znalostí pomocí Pošťáka a REST](knowledge-store-create-rest.md)

Chcete-li se dozvědět více o projekcích, schopnostech a [jejich definování v sadě dovedností](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projekce v obchodě se znalostmi](knowledge-store-projection-overview.md)

Pro kurz zahrnující pokročilé projekce pojmy, jako je krájení, inline tvarování a vztahy, začít s [definovat projekce v úložišti znalostí](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definování projekcí v úložišti znalostí](knowledge-store-projections-examples.md)