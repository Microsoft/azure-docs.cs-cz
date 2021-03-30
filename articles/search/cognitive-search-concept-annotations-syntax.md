---
title: Reference na vstupy a výstupy v dovednosti
titleSuffix: Azure Cognitive Search
description: Vysvětluje syntaxi poznámek a postup odkazování na anotaci v vstupech a výstupech dovednosti v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935530"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Postup při odkazování na poznámky v Azure Kognitivní hledání dovednosti

V tomto článku se dozvíte, jak odkazovat na poznámky v definicích dovedností, a to pomocí příkladů k ilustraci různých scénářů. Vzhledem k tomu, že obsah dokumentu projde sadou dovedností, je obohacen s poznámkami. Poznámky se dají použít jako vstupy pro další rozšíření pro příjem dat nebo namapované na výstupní pole v indexu. 
 
Příklady v tomto článku jsou založené na poli *obsahu* generovaném automaticky [indexery Azure Blob](search-howto-indexing-azure-blob-storage.md) v rámci fáze odhalující dokumentu. Při odkazování na dokumenty z kontejneru objektů BLOB použijte formát, jako je například `"/document/content"` , kde je pole *Content* součástí *dokumentu*. 

## <a name="background-concepts"></a>Koncepce na pozadí

Než začnete s syntaxí zkontrolovat, Podívejme se na několik důležitých konceptů, abychom lépe porozuměli ukázkám uvedeným dále v tomto článku.

| Období | Popis |
|------|-------------|
| Obohacený dokument | Obohacený dokument je interní struktura vytvořená a používaná kanálem, aby obsahovala všechny poznámky týkající se dokumentu. Obohacený dokument si můžete představit jako strom poznámek. Obecně platí, že Poznámka vytvořená z předchozí poznámky se zobrazí jako podřízená.<p/>Obohacené dokumenty existují pouze po dobu trvání provádění dovednosti. Po namapování obsahu na index vyhledávání už obohacený dokument není potřeba. I když nepracujete s obohacenými dokumenty přímo, je vhodné mít při vytváření dovednosti k dispozici duševní modely dokumentů. |
| Kontext obohacení | Kontext, ve kterém probíhá obohacení, s ohledem na to, který prvek je obohacen. Ve výchozím nastavení je kontext rozšíření obohacen na `"/document"` úrovni a je vymezen na jednotlivé dokumenty. Když se dovednost spustí, výstupy této dovednosti se stanou [vlastnostmi definovaného kontextu](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Příklad 1: odkaz na jednoduchou poznámku

V úložišti objektů BLOB v Azure Předpokládejme, že máte nejrůznější soubory obsahující odkazy na názvy lidí, které chcete extrahovat pomocí rozpoznávání entit. V definici dovednosti níže `"/document/content"` je textová reprezentace celého dokumentu a "lidé" je extrakce úplných názvů entit identifikovaných jako osoby.

Vzhledem k tomu, že výchozí kontext je `"/document"` , seznam lidí se teď může odkazovat jako `"/document/people"` . V tomto konkrétním případě `"/document/people"` je to anotace, která by teď mohla být namapována na pole v indexu nebo použitá v jiné dovednosti ve stejném dovednosti.

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

## <a name="example-2-reference-an-array-within-a-document"></a>Příklad 2: odkazování na pole v rámci dokumentu

Tento příklad sestaví na předchozím, kde se dozvíte, jak v jednom dokumentu vyvolat krok obohacení několikrát. Předpokládejte, že předchozí příklad vygeneroval pole řetězců s 10 jmény osob z jednoho dokumentu. Přiměřeným dalším krokem může být druhé rozšíření, které extrahuje příjmení z celého jména. Vzhledem k tomu, že existuje 10 názvů, budete chtít, aby tento krok byl v tomto dokumentu v jednom případě v každém z nich zavolán desetkrát. 

Chcete-li vyvolat správný počet iterací, nastavte kontext jako `"/document/people/*"` , kde hvězdička ( `"*"` ) představuje všechny uzly v obohaceném dokumentu jako následníky `"/document/people"` . I když je tato dovednost definována pouze jednou v poli dovednosti, je volána pro každého člena v rámci dokumentu, dokud nebudou všichni členové zpracováni.

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

Když jsou poznámky pole nebo kolekce řetězců, můžete chtít cílit na konkrétní členy, nikoli na pole jako celek. Výše uvedený příklad generuje poznámku volanou `"last"` pod každým uzlem reprezentovaným kontextem. Pokud chcete odkazovat na tuto rodinu poznámek, můžete použít syntaxi `"/document/people/*/last"` . Pokud chcete odkazovat na konkrétní anotaci, můžete použít explicitní index: `"/document/people/1/last` "Chcete-li odkazovat na příjmení první osoby identifikované v dokumentu. Všimněte si, že v těchto polích syntaxe jsou "0 Indexed".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Příklad 3: referenční členové v rámci pole

Někdy je nutné seskupit všechny poznámky určitého typu, aby je bylo možné předat konkrétní dovednosti. Vezměte v úvahu hypotetickou vlastní dovednost, která určuje nejběžnější příjmení ze všech posledních názvů extrahovaných v příkladu 2. Chcete-li zadat pouze poslední názvy vlastní dovednosti, zadejte kontext jako a jako `"/document"` vstup `"/document/people/*/lastname"` .

Všimněte si, že mohutnost `"/document/people/*/lastname"` je větší než v dokumentu. V případě, že je pro tento dokument k dispozici pouze jeden uzel dokumentu, může dojít k 10 uzlům LastName. V takovém případě systém automaticky vytvoří pole  `"/document/people/*/lastname"` obsahující všechny prvky v dokumentu.

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
+ [Jak integrovat vlastní dovednosti do kanálu pro obohacení](cognitive-search-custom-skill-interface.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](/rest/api/searchservice/create-skillset)
+ [Mapování obohacených polí na index](cognitive-search-output-field-mapping.md)