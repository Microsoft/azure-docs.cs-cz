---
title: Referenční vstupy a výstupy v skillsets
titleSuffix: Azure Cognitive Search
description: Vysvětluje syntaxi poznámky a jak odkazovat na poznámku ve vstupech a výstupech skillset v kanálu obohacení AI v Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113867"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Jak odkazovat na poznámky v sadě dovedností Azure Cognitive Search

V tomto článku se dozvíte, jak odkazovat na poznámky v definicích dovedností, pomocí příkladů pro ilustraci různých scénářů. Jak obsah dokumentu protéká sadou dovedností, obohacuje se o poznámky. Anotace lze použít jako vstupy pro další následné obohacení nebo namapovat na výstupní pole v indexu. 
 
Příklady v tomto článku jsou založeny na poli *obsahu* generované automaticky [indexery objektů blob Azure](search-howto-indexing-azure-blob-storage.md) jako součást fáze prolomení dokumentu. Když odkazujete na dokumenty z kontejneru objektů `"/document/content"`Blob, použijte formát, například , kde je pole *obsahu* součástí *dokumentu*. 

## <a name="background-concepts"></a>Koncepty pozadí

Před kontrolou syntaxe, pojďme znovu několik důležitých konceptů lépe pochopit příklady uvedené dále v tomto článku.

| Označení | Popis |
|------|-------------|
| Obohacený dokument | Obohacený dokument je vnitřní struktura vytvořená a používaná kanálem k uložení všech poznámk souvisejících s dokumentem. Hledějte obohacený dokument jako strom anotací. Obvykle se anotace vytvořená z předchozí poznámky stane jejím podřízeným.<p/>Obohacené dokumenty existují pouze po dobu provádění sady dovedností. Jakmile je obsah mapován na index vyhledávání, obohacený dokument již není potřeba. I když s obohacenými dokumenty nekomunikujete přímo, je užitečné mít mentální model dokumentů při vytváření dovedností. |
| Kontext obohacení | Kontext, ve kterém dochází k obohacení, v souvislosti s tím, který prvek je obohacen. Ve výchozím nastavení je kontext `"/document"` obohacení na úrovni, s rozsahem jednotlivých dokumentů. Při spuštění dovednosti se výstupy této dovednosti stanou [vlastnostmi definovaného kontextu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Příklad 1: Jednoduchý odkaz na poznámky

V úložišti objektů blob Azure předpokládejme, že máte různé soubory obsahující odkazy na názvy lidí, které chcete extrahovat pomocí rozpoznávání entit. V níže uvedené `"/document/content"` definici dovednosti je textová reprezentace celého dokumentu a "lidé" je extrakce úplných jmen pro subjekty identifikované jako osoby.

Vzhledem k `"/document"`tomu, že výchozí kontext je `"/document/people"`, lze nyní odkazovat na seznam osob jako na . V tomto `"/document/people"` konkrétním případě je anotace, která by nyní mohla být mapována na pole v indexu nebo použita v jiné dovednosti ve stejné sadě dovedností.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Příklad 2: Odkazování na pole v dokumentu

Tento příklad vychází z předchozího, ukazuje, jak vyvolat krok obohacení vícekrát přes stejný dokument. Předpokládejme, že předchozí příklad vygeneroval pole řetězců s názvy 10 osob z jednoho dokumentu. Rozumným dalším krokem může být druhé obohacení, které extrahuje příjmení z celého jména. Vzhledem k tomu, že existuje 10 jmen, chcete, aby byl tento krok v tomto dokumentu volán 10krát, jednou pro každou osobu. 

Chcete-li vyvolat správný počet iterací, nastavte kontext `"/document/people/*"`jako`"*"`, kde hvězdička ( ) představuje všechny `"/document/people"`uzly v obohaceném dokumentu jako následníky . Přestože je tato dovednost definována pouze jednou v poli dovednosti, je volána pro každého člena v dokumentu, dokud nebudou zpracovány všechny členy.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Pokud jsou poznámky matice nebo kolekce řetězců, můžete chtít cílit na určité členy, nikoli na pole jako celek. Výše uvedený příklad generuje poznámku `"last"` volanou pod každým uzlem reprezentovaným kontextem. Pokud chcete odkazovat na tuto rodinu anotací, `"/document/people/*/last"`můžete použít syntaxi . Pokud chcete odkazovat na konkrétní poznámku, můžete použít `"/document/people/1/last`explicitní index: " k odkazu na příjmení první osoby identifikované v dokumentu. Všimněte si, že v této pole syntaxe jsou "0 indexovány".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Příklad 3: Referenční členy v rámci pole

Někdy je třeba seskupit všechny poznámky určitého typu, abyste je předali určité dovednosti. Zvažte hypotetickou vlastní dovednost, která identifikuje nejběžnější příjmení ze všech příjmení extrahovaných v příkladu 2. Chcete-li zadat pouze příjmení vlastní dovednosti, `"/document"` zadejte kontext `"/document/people/*/lastname"`jako a vstup jako .

Všimněte si, že `"/document/people/*/lastname"` mohutnost je větší než dokument. Může existovat 10 lastname uzly, zatímco je pouze jeden uzel dokumentu pro tento dokument. V takovém případě systém automaticky vytvoří `"/document/people/*/lastname"` pole obsahující všechny prvky v dokumentu.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Viz také
+ [Jak integrovat vlastní dovednost do kanálu obohacování](cognitive-search-custom-skill-interface.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Vytvořit sadu dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole do indexu](cognitive-search-output-field-mapping.md)
