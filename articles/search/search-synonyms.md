---
title: Synonyma pro rozšíření dotazu přes vyhledávací index
titleSuffix: Azure Cognitive Search
description: Vytvořením mapy synonym můžete rozšířit rozsah vyhledávacího dotazu na index služby Azure Kognitivní hledání. Rozsah je rozšířen tak, aby zahrnoval ekvivalentní výrazy, které zadáte v seznamu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251620"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonyma v Azure Kognitivní hledání

Pomocí map synonym můžete přidružit ekvivalentní podmínky a rozšířit rozsah dotazu, aniž by museli uživatelé ve skutečnosti zadávat podmínky. Například předpokládá, že "pes", "Canine" a "Puppy" jsou synonyma, dotaz na "Canine" se bude shodovat s dokumentem obsahujícím "pes".

## <a name="create-synonyms"></a>Vytvořit synonyma

Mapa synonym je Asset, který se dá vytvořit jednou a použít v mnoha indexech. [Úroveň služby](search-limits-quotas-capacity.md#synonym-limits) určuje, kolik mapování synonym můžete vytvořit (v rozsahu od tří než tří synonymních map pro úrovně Free a Basic, až 20 pro úrovně Standard. 

Můžete vytvořit více map synonym pro různé jazyky, jako jsou anglické a francouzské verze, nebo lexikony, pokud obsah obsahuje technickou nebo nadkrývající terminologii. I když ve vyhledávací službě můžete vytvořit několik map synonym, může pole použít jenom jeden z nich.

Mapa synonym se skládá z názvu, formátu a pravidel, které fungují jako položky mapování synonym. Jediným podporovaným formátem je `solr` a `solr` formát Určuje vytváření pravidel.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

K vytvoření mapy synonym použijte [mapu pro vytvoření synonym (REST API)](/rest/api/searchservice/create-synonym-map) nebo sadu Azure SDK. Pro vývojáře v jazyce C# doporučujeme začít s [přidáním synonym v Azure rozpoznávání vyhledávání pomocí jazyka c#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Definovat pravidla

Pravidla mapování dodržují specifikace Open Source filtru synonym pro Apache Solr, která je popsaná v tomto dokumentu: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). `solr` Formát podporuje dva druhy pravidel:

+ ekvivalenci (kde se v dotazu shodují výrazy)

+ explicitní mapování (kde jsou před dotazem namapovány podmínky na jeden explicitní výraz)

Každé pravidlo musí být odděleno znakem nového řádku ( `\n` ). Můžete definovat až 5 000 pravidel na mapování synonym v rámci bezplatné služby a 20 000 pravidel na mapu v jiných úrovních. Každé pravidlo může mít až 20 rozšíření (nebo položek v pravidle). Další informace najdete v tématu [omezení synonym](search-limits-quotas-capacity.md#synonym-limits).

Analyzátory dotazů budou mít malá písmena velká a malá písmena, ale pokud chcete zachovat speciální znaky v řetězci, jako je čárka nebo pomlčka, přidejte při vytváření mapy synonym příslušné řídicí znaky.

### <a name="equivalency-rules"></a>Pravidla pro rovnocennost

Pravidla pro ekvivalentní podmínky jsou oddělená čárkou v rámci stejného pravidla. V prvním příkladu se dotaz na `USA` se rozšíří na `USA` nebo `"United States"` nebo `"United States of America"` . Všimněte si, že pokud chcete shodovat s frází, samotný dotaz musí být dotaz fráze uzavřený v uvozovkách.

V případě rovnosti dotaz pro způsobí, `dog` že dotaz rozbalí a také zahrne `puppy` a `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Explicitní mapování

Pravidla pro explicitní mapování jsou označena pomocí šipky `=>` . Je-li tento parametr zadán, nahradí se sekvence vyhledávacího dotazu, který odpovídá levé straně, `=>` nahrazena alternativami na pravé straně v době dotazu.

V případě explicitního případu je dotaz na `Washington` `Wash.` nebo `WA` přepsán jako `WA` a dotazovací stroj bude hledat pouze shody s termínem `WA` . Explicitní mapování platí pouze v zadaném směru a nepřepíše dotaz `WA` do `Washington` tohoto případu.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Speciální znaky pro uvozovací znaky

Při zpracování dotazu se analyzují synonyma. Pokud potřebujete definovat synonyma, která obsahují čárky nebo jiné speciální znaky, můžete je zadat pomocí zpětného lomítka, jako v tomto příkladu:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Vzhledem k tomu, že zpětné lomítko je sám speciálním znakem v jiných jazycích, jako je JSON a C#, budete ho pravděpodobně muset poklepat. Například JSON odeslaný do REST API pro výše uvedené mapování synonym by vypadalo takto:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Nahrávání a Správa map synonym

Jak bylo zmíněno dříve, můžete vytvořit nebo aktualizovat mapu synonym bez přerušení úloh dotazů a indexování. Mapa synonym je samostatný objekt (například indexy nebo zdroje dat), a pokud ho žádné pole nepoužívá, aktualizace nezpůsobí selhání indexování nebo dotazů. Když však přidáte mapu synonym do definice pole a odstraníte mapování synonym, jakýkoli dotaz, který obsahuje příslušná pole, selže s chybou 404.

Vytváření, aktualizace a odstraňování mapy synonym je vždy operace celého dokumentu, což znamená, že nemůžete aktualizovat nebo odstranit části mapy synonym přírůstkově. Aktualizace ani jedno pravidlo vyžaduje opětovné načtení.

## <a name="assign-synonyms-to-fields"></a>Přiřazení synonym k polím

Po nahrání mapy synonym můžete povolit synonyma pro pole typu `Edm.String` nebo `Collection(Edm.String)` v polích, které mají `"searchable":true` . Jak je uvedeno, definice pole může používat pouze jednu mapu synonym.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Dotaz na ekvivalentních nebo mapovaných polích

Přidání synonym nezavádí nové požadavky na vytváření dotazů. Můžete vystavit dotazy a fráze stejným způsobem jako před přidáním synonym. Jediným rozdílem je, že pokud se v mapě synonym vyskytuje termín dotazu, stroj dotazu buď rozbalí nebo nahradí podmínky nebo frázi v závislosti na pravidle.

## <a name="how-synonyms-are-used-during-query-execution"></a>Jak se používají synonyma při provádění dotazu

Synonyma jsou techniky rozšíření dotazu, která doplňují obsah indexu se stejnými výrazy, ale pouze pro pole, která mají přiřazení synonym. Pokud dotaz v oboru pole *vyloučí* pole s povoleným synonymem, neuvidíte shody z mapy synonym.

V případě polí s povoleným synonymem jsou synonyma považována za stejnou analýzu textu jako související pole. Například pokud se pole analyzuje pomocí standardního analyzátoru aplikace Lucene, budou se za tyto výrazy v době dotazu také vztahovat standardní analyzátor Lucene. Pokud chcete zachovat interpunkční znaménka, jako jsou tečky nebo pomlčky, v termínu synonyma použijte analyzátor pro zachování obsahu v poli.

Interně funkce synonym přepíše původní dotaz s synonymy pomocí operátoru OR. Z tohoto důvodu se zvýrazňování přístupů a profily vyhodnocování považují za původní podmínky a synonyma jako ekvivalentní.

Synonyma se vztahují pouze na textové dotazy bezplatného formuláře a nejsou podporovány pro filtry, omezující vlastnosti, automatické dokončování a návrhy. Automatické dokončování a návrhy jsou založené jenom na původním období; shody synonym nejsou v odpovědi zobrazeny.

Rozšíření synonym neplatí pro výrazy vyhledávání se zástupnými znaky; výrazy s předponou, přibližnými a regulárními výrazy nejsou rozbaleny.

Pokud potřebujete provést jeden dotaz, který používá rozšíření synonym a zástupné znaky, regulární výrazy nebo přibližné vyhledávání, můžete kombinovat dotazy pomocí syntaxe nebo. Například pro kombinování synonym se zástupnými znaky pro jednoduchou syntaxi dotazu by byl termín `<query> | <query>*` .

Pokud máte ve vývojovém (neprodukčním) prostředí existující index, Experimentujte s malým slovníkem, abyste viděli, jak Přidání synonym mění možnosti hledání, včetně dopadu na profily vyhodnocování, zvýrazňování přístupů a návrhy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření mapy synonym (REST API)](/rest/api/searchservice/create-synonym-map)