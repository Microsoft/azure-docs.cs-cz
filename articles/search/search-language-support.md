---
title: Indexování neanglických vícejazykového vyhledávacích dotazů – Azure Search
description: Služba Azure Search podporuje 56 jazycích využitím jazykové analyzátory z technologie Lucene a zpracování přirozeného jazyka od Microsoftu.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 3f88965e0d48a9ed3e9a91b4df42a79b83d2a79b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313322"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Vytvoření indexu pro dokumenty v několika jazycích ve službě Azure Search
> [!div class="op_single_selector"]
>
> * [Azure Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Odemčení síly skryté v jazykové analyzátory je stejně jednoduché jako jednu vlastnost nastavení na prohledávatelném poli v definici indexu. Nyní můžete provést tento krok na portálu.

Níže jsou snímky obrazovky z oken webu Azure Portal pro Azure Search, které povoluje uživatelům definovat schématu indexu. Z tohoto okna můžete uživatelům vytvořit všechna pole a nastavte vlastnost analyzátor pro každý z nich.

> [!IMPORTANT]
> Lze nastavit pouze analyzátor jazyka při definici pole, stejně jako při vytváření nového indexu zdola nahoru nebo při přidávání nového pole do stávajícího indexu. Ujistěte se, že zadáte plně všechny atributy, včetně analyzátoru, při vytváření pole. Nebudete moci upravit vlastnosti nebo změňte typ analyzátoru po uložení změn.
>
>

## <a name="define-a-new-field-definition"></a>Definovat novou definici pole
1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a otevře se okno služby vaší služby search.
2. Klikněte na tlačítko **přidat index** na panelu příkazů v horní části řídicího panelu služby ke spuštění nového indexu, nebo otevřete existující index k nastavení analyzátor na nová pole, které přidáváte do existujícího indexu.
3. Zobrazí se okno polí, získáte možnosti pro definování schématu indexu, včetně kartě analyzátor používá pro výběr analyzátor jazyka.
4. V polích začněte definici pole poskytnutí názvu, vyberete typ dat a nastavení atributů, které mají označení pole textu v plném znění prohledávatelné, retrievable ve výsledcích hledání, použitelné ve strukturách omezující vlastnost navigace, seřaditelné a podobně.
5. Než budete pokračovat na další pole, otevřete **analyzátor** kartu.

![][1]
*Vyberte analyzátor, klikněte na kartu analýza v okně pole*

## <a name="choose-an-analyzer"></a>Vyberte analyzátor
1. Posuňte se k vyhledání pole, které definujete.
2. Pokud jste neoznačili jako prohledávatelná pole, klikněte na zaškrtávací políčko hned a označte ji jako **Searchable**.
3. Klikněte na oblast analyzátor zobrazíte seznam dostupných analyzátory.
4. Vyberte analyzátor, který chcete použít.

![][2]
*Vyberte jednu z podporovaných analyzátory pro každé pole*

Ve výchozím nastavení, všechna prohledatelná pole použít [analyzátor Lucene standardní](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) což je jazykově nezávislé. Chcete-li zobrazit úplný seznam podporovaných analyzátory, naleznete v tématu [jazykovou podporu ve službě Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Po výběru analyzátoru jazyka pro pole, se použije spolu s každou žádostí indexování a vyhledávání pro toto pole. Při dotazu je vydaný pro více polí pomocí různých analyzátorů, dotaz se zpracuje nezávisle na sobě správné analyzátory pro každé pole.

Mnoho webových a mobilních aplikací poskytovat uživatelům na celém světě používající různé jazyky. Je možné definovat index pro takovéhoto scénáře tím, že vytvoříte pole pro každý jazyk podporovaný.

![][3]
*Definice indexu pole Popis pro každý jazyk podporovaný*

Pokud jazyk agenta zadání dotazu je znám, žádost o vyhledávání se dají vymezit na konkrétní pole pomocí **searchFields** parametr dotazu. Následující dotaz bude vydaný pouze pro popis v polština:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

Indexu z portálu, můžete zadávat dotazy pomocí **Průzkumníka služby Search** vložit v podobně jako výše uvedeném dotazu. Průzkumník služby Search je dostupná na panelu příkazů v okně služby. Zobrazit [dotazování indexu Azure Search na portálu](search-explorer.md) podrobnosti.

Někdy není známý jazyk agenta zadání dotazu, v takovém případě dotazu může být vydaný pro všechna pole současně. V případě potřeby předvolby pro výsledky v určitém jazyce, lze definovat pomocí [profily skórování](https://msdn.microsoft.com/library/azure/dn798928.aspx). V následujícím příkladu se shody nalezený v popisu v angličtině vyšší skóre relativní vzhledem k shody v polština a Francouzština:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

Pokud jste vývojář .NET, mějte na paměti, že můžete nakonfigurovat pomocí jazykové analyzátory [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). Nejnovější vydaná verze zahrnuje podporu pro jazykové analyzátory Microsoft také.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
