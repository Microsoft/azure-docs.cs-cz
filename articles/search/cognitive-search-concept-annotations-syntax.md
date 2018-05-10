---
title: Referenční poznámky v vstupy a výstupy v kanálu kognitivní vyhledávání ve službě Azure Search | Microsoft Docs
description: Popisuje syntaxi poznámky a jak odkazovat poznámky v vstupy a výstupy skillset v kanálu kognitivní vyhledávání ve službě Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 2e838e9c94d5b19565bea3d02890fe6164bb37d0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Jak odkazovat poznámky v skillset kognitivní vyhledávání

V tomto článku se naučíte tak, aby odkazovaly poznámky v definicích odborností, příklady použití pro ilustraci různé scénáře. Jako obsah dokumentu toků pomocí sady dovedností, získá rozšíření s poznámky. Poznámky lze použít jako vstupy pro další podřízené obohacení nebo mapována na výstupní pole v indexu. 
 
Příklady v tomto článku jsou založeny na *obsah* pole automaticky vygenerované [objektů Blob v Azure indexery](search-howto-indexing-azure-blob-storage.md) jako součást dokumentu krakování fáze. Při odkazování na dokumenty z kontejneru objektů Blob, použijte formát, jako `"/document/content"`, kde *obsah* je pole součástí *dokumentu*. 

## <a name="background-concepts"></a>Koncepty pozadí

Před kontrola syntaxe, můžeme pokroku několik důležité koncepty, abyste lépe pochopili, příklady uvedené dále v tomto článku.

| Označení | Popis |
|------|-------------|
| Provádět rozšířené dokumentu | Dokument provádět rozšířené je strukturu interní vytvořen a v kanálu použije k uložení všech poznámky související s dokumentu. Dokument provádět rozšířené si můžete představit jako strom poznámky. Obecně platí poznámky vytvořené z předchozí poznámky se změní na jeho podřízené.<p/>Provádět rozšířené dokumenty existovat pouze po dobu trvání provádění skillset. Jakmile obsah mapován na index vyhledávání, provádět rozšířené dokumentu již není potřeba. I když jste nemáte komunikovat přímo s provádět rozšířené dokumenty, je užitečné při vytváření skillset mají duševní model dokumentů. |
| Obohacení kontextu | Kontext, ve kterém obohacení probíhá, z hlediska, což je rozšíření elementu. Ve výchozím kontextu obohacení je na `"/document"` úrovni, omezená na jednotlivé dokumenty. Kvalifikaci spuštění, výstupy dovednosti, stane [vlastnosti definované kontextu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Příklad 1: Jednoduchý poznámky odkaz

V úložišti objektů Azure Blob Předpokládejme, že máte různé soubory obsahující odkazy na jména osob, které mají být extrahovány použití funkce rozpoznávání pojmenované entity. V definici odborností níže `"/document/content"` je textovou reprezentaci celého dokumentu a "uživatelé" extrakci úplné názvy entit, které jsou identifikovány jako osob.

Protože je výchozí kontext `"/document"`, seznam lidí, kteří by se teď může odkazovat jako `"/document/people"`. V tomto případě konkrétní `"/document/people"` je poznámka, která může nyní být namapované na pole v indexu, nebo použít v jiné odborností ve stejné skillset.

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

## <a name="example-2-reference-an-array-within-a-document"></a>Příklad 2: Odkazovat na pole v dokumentu

Tento příklad vychází předchozímu, ukazuje, jak má být vyvolán na krok obohacení vícekrát přes stejného dokumentu. Předpokládejme, že předchozí příklad generované pole řetězce s názvy 10 osoby z jednotlivý dokument. Je možné logicky dalším krokem může být druhý obohacení, který extrahuje příjmení z jméno a příjmení. Vzhledem k tomu, že se 10 názvy, budete chtít tento krok k volání 10krát v tomto dokumentu, jednou pro každou osobu. 

Chcete-li vyvolání správný počet iterací, nastavte kontext jako `"/document/people/*"`, kde se tedy hvězdička (`"*"`) představuje všechny uzly v provádět rozšířené dokumentu jako následníků `"/document/people"`. I když tento odborností pouze jednou definovaná v poli znalosti, se nazývá pro každého člena v tomto dokumentu dokud se zpracovávají všechny členy.

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

Pokud jsou poznámky pole nebo kolekce řetězce, můžete chtít cílové konkrétních členů než pole jako celek. Výše uvedený příklad generuje poznámky názvem `"last"` za každý uzel reprezentována kontextu. Pokud chcete odkazovat na tuto řadu poznámky, můžete použít syntaxi `"/document/people/*/last"`. Pokud chcete odkazovat na konkrétní poznámky, můžete použít explicitní index: `"/document/people/1/last`"tak, aby odkazovaly příjmení první, kdo v dokumentu. Všimněte si, že v této syntaxe pole jsou "indexované 1".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Příklad 3: Odkaz na členy v rámci pole

V některých případech budete muset skupiny všechny poznámky určitého typu předejte konkrétní znalostí. Vezměte v úvahu hypotetické vlastní odborností, který identifikuje nejběžnější příjmení od poslední názvů extrahovat v příkladu 2. Pokud chcete pouze poslední jména vlastní odborností, zadejte kontext jako `"/document"` a vstup jako `"/document/people/*/lastname"`.

Všimněte si, že na kardinalitu `"/document/people/*/lastname"` je větší než dokumentu. Může mít 10 uzly lastname sice jenom jeden uzel dokumentu tohoto dokumentu. V takovém případě systém automaticky vytvoří pole `"/document/people/*/lastname"` obsahující všechny elementy v dokumentu.

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
+ [Jak integrovat vlastní znalostí do kanálu obohacení](cognitive-search-custom-skill-interface.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Vytvoření Skillset (REST)](ref-create-skillset.md)
+ [Postup mapování na index provádět rozšířené pole](cognitive-search-output-field-mapping.md)
