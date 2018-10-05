---
title: Referenční Poznámka v vstupy a výstupy v rámci kanálu kognitivního vyhledávání ve službě Azure Search | Dokumentace Microsoftu
description: Popisuje syntaxe poznámky a odkaz na poznámku na vstupy a výstupy dovedností v kanálu kognitivního vyhledávání ve službě Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ccc1fb20cb08cfd97d58984676ef4006e693118
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801943"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Způsob vytvoření odkazu poznámky v dovedností kognitivního vyhledávání

V tomto článku se dozvíte, jak odkazovat poznámky v definicích dovednosti pomocí příklady znázorňují různé scénáře. Jako obsah dokumentu prochází přes sadu znalostí, získá rozšiřují s poznámkami. Poznámky můžete použít jako vstupy pro další podřízené rozšíření nebo mapování pro výstupní pole v indexu. 
 
Příklady v tomto článku jsou založeny na *obsah* pole automaticky vygenerované [indexování objektů Blob v Azure](search-howto-indexing-azure-blob-storage.md) jako součást fáze analýzy dokumentu. Při odkazování na dokumenty z kontejneru objektů Blob, pomocí formátu, jako `"/document/content"`, kde *obsah* pole je součástí *dokumentu*. 

## <a name="background-concepts"></a>Koncepty

Před kontrola syntaxe, Vraťme se k pár důležitých konceptů k lepšímu pochopení příkladů uvedených dále v tomto článku.

| Označení | Popis |
|------|-------------|
| Bohatších možností dokumentu | Dokument bohatších možností je vnitřní struktury vytvoří a použije k uložení všechny poznámky související s dokumentem. Dokument bohatších možností můžete představit jako strom poznámky. Obecně platí anotaci vytvořené z předchozí poznámce se změní na podřízenou.<p/>Bohatších možností dokumenty existovat pouze po dobu trvání využití jeho dovedností. Jakmile obsah je mapován na index vyhledávání, se už nepotřebuje bohatších možností dokumentu. I když jste nespolupracují s bohatších možností dokumenty přímo, je užitečné mít myšlenkový model pro dokumenty, při vytváření dovedností. |
| Rozšíření kontextu | Kontext, ve kterém obohacení probíhá, z hlediska, který je element rozšiřují. Ve výchozím nastavení, rozšíření kontextu je na `"/document"` úroveň, omezená na jednotlivé dokumenty. Když konkrétní dovednosti běží, výstupy dovednosti, stane [vlastnosti definované kontextu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Příklad 1: Odkaz na poznámku jednoduchý

Ve službě Azure Blob storage Předpokládejme, že máte celou řadu soubory, které obsahují odkazy na jména osob, které mají být extrahovány pomocí rozpoznávání pojmenovaných entit. V definici dovednosti níže `"/document/content"` textovou reprezentaci celého dokumentu a "osob" je extrakci úplné názvy pro entity identifikované jako osoby.

Protože je výchozí kontext `"/document"`, seznam lidí, kteří teď může odkazovat jako `"/document/people"`. V tomto konkrétním případě `"/document/people"` je poznámka, která může nyní být namapováno na pole v indexu, nebo použít v jiné dovednosti v stejné dovednosti.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

## <a name="example-2-reference-an-array-within-a-document"></a>Příklad 2: Odkazovat na pole v rámci dokumentu

Tento příklad je založen na předchozím histogramem ukazuje, jak vyvolat jeden krok obohacování více než jednou za stejný dokument. Předpokládejme, že v předchozím příkladu vygenerované pole řetězce s názvy 10 lidí z jednoho dokumentu. Přiměřené dál může být druhý rozšíření, který extrahuje příjmení z celé jméno. Protože 10 názvy se chcete tohoto kroku můžete volat 10krát v tomto dokumentu, jednou pro každou osobu. 

Chcete-li vyvolat správný počet iterací, nastavte kontext jako `"/document/people/*"`, kde hvězdičku (`"*"`) představuje všechny uzly v dokumentu bohatších možností jako následníky `"/document/people"`. I když tato dovednosti pouze jednou definované v poli dovednosti, je volána pro každého člena v rámci dokumentu dokud jsou zpracovány všechny členy.

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

Po pole nebo kolekce řetězců se poznámky můžete cílit na konkrétní členy spíše než pole jako celek. Výše uvedený příklad generuje Poznámka volá `"last"` za každý uzel reprezentována kontextu. Pokud chcete odkazovat na tuto řadu poznámky, můžete použít syntaxi `"/document/people/*/last"`. Pokud chcete odkazovat na konkrétní poznámky, můžete použít explicitní indexu: `"/document/people/1/last`"k odkazování příjmení první, kdo v dokumentu. Všimněte si, že v této syntaxe pole "0 indexované".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Příklad 3: Odkazovat na členy v rámci pole

Někdy potřebujete seskupit všechny poznámky určitého typu předávat do konkrétní dovednosti. Vezměte v úvahu hypotetické vlastních dovedností, který identifikuje nejběžnější příjmení od poslední názvů extrahována v příkladu 2. Pokud chcete poskytnout jenom poslední názvy vlastních dovedností, určení kontextu jako `"/document"` a vstupu jako `"/document/people/*/lastname"`.

Všimněte si, že Kardinalita `"/document/people/*/lastname"` je větší než u dokumentu. Může existovat 10 uzlů lastname pouze jeden uzel dokumentu pro tento dokument se neplatí. V takovém případě systém automaticky vytvoří pole `"/document/people/*/lastname"` obsahující všechny prvky v dokumentu.

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



## <a name="see-also"></a>Další informace najdete v tématech
+ [Jak integrovat do kanálu služby rozšíření vlastních dovedností](cognitive-search-custom-skill-interface.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak namapovat bohatších možností pole indexu](cognitive-search-output-field-mapping.md)
