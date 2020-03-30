---
title: Synonyma pro rozšíření dotazu přes index vyhledávání
titleSuffix: Azure Cognitive Search
description: Vytvořte mapu synonym pro rozšíření oboru vyhledávacího dotazu v indexu Azure Cognitive Search. Obor je rozšířen tak, aby zahrnoval ekvivalentní termíny, které zadáte do seznamu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194338"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonyma v Azure Cognitive Search

Synonyma ve vyhledávačích přidružit ekvivalentní termíny, které implicitně rozbalit rozsah dotazu, aniž by uživatel musí skutečně zadat termín. Například vzhledem k termínu "pes" a synonymum sdružení "psí" a "štěně", všechny dokumenty obsahující "pes", "psí" nebo "štěně" budou spadat do působnosti dotazu.

V Azure Cognitive Search, synonymum rozšíření se provádí v době dotazu. Do služby můžete přidat mapy synonym bez přerušení stávajících operací. Vlastnost **synonymMapy** můžete přidat do definice pole, aniž byste museli znovu vytvořit index.

## <a name="create-synonyms"></a>Vytváření synonym

Neexistuje žádná podpora portálu pro vytváření synonym, ale můžete použít rozhraní REST API nebo .NET SDK. Chcete-li začít s REST, doporučujeme [použít Postman](search-get-started-postman.md) a formulace požadavků pomocí tohoto rozhraní API: [Vytvořit synonyma mapy](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map). Pro vývojáře jazyka C# můžete začít s [přidáním synonym v Azure Cognitive Searching pomocí jazyka C#](search-synonyms-tutorial-sdk.md).

Volitelně pokud používáte [klíče spravované zákazníkem](search-security-manage-encryption-keys.md) pro šifrování na straně služby v klidovém stavu, můžete tuto ochranu použít na obsah mapy synonym.

## <a name="use-synonyms"></a>Použití synonym

V Azure Cognitive Search je podpora synonym založená na mapách synonym, které definujete a nahrajete do své služby. Tyto mapy představují nezávislý prostředek (například indexy nebo zdroje dat) a mohou být použity libovolným prohledávatelným polem v libovolném indexu ve vyhledávací službě.

Mapy a indexy synonym jsou udržovány nezávisle. Jakmile definujete mapu synonym a nahrajete ji do služby, můžete povolit funkci synonyma v poli přidáním nové vlastnosti s názvem **synonymaMapy** v definici pole. Vytvoření, aktualizace a odstranění mapy synonym je vždy operace celého dokumentu, což znamená, že nelze vytvářet, aktualizovat nebo odstraňovat části mapy synonyma postupně. Aktualizace i jedné položky vyžaduje opětovné načtení.

Začlenění synonym do vyhledávací aplikace je dvoustupňový proces:

1.  Přidejte mapu synonym do vyhledávací služby prostřednictvím níže uvedených rozhraní API.  

2.  Nakonfigurujte prohledávatelné pole tak, aby v definici indexu používalo mapu synonym.

Můžete vytvořit více synonym mapy pro vyhledávací aplikace (například podle jazyka, pokud vaše aplikace podporuje vícejazyčné zákaznické základny). V současné době může pole použít pouze jedno z nich. Vlastnost synonymmapy pole můžete kdykoli aktualizovat.

### <a name="synonymmaps-resource-apis"></a>Synonyma rozhraní API prostředků

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Přidejte nebo aktualizujte mapu synonym pod vaší službou pomocí POST nebo PUT.

Synonyma mapy jsou nahrány do služby přes POST nebo PUT. Každé pravidlo musí být odděleno novým znakem řádku (\n'). Můžete definovat až 5 000 pravidel na mapu synonym a ve volné službě a 20 000 pravidel na mapu ve všech ostatních sku. Každé pravidlo může mít až 20 rozšíření.

Synonyma mapy musí být ve formátu Apache Solr, který je vysvětlen níže. Pokud máte existující synonymní slovník v jiném formátu a chcete jej používat přímo, dejte nám prosím vědět na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Můžete vytvořit novou mapu synonym pomocí HTTP POST, jako v následujícím příkladu:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternativně můžete použít PUT a zadat název mapy synonyma na URI. Pokud mapa synonym neexistuje, bude vytvořena.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr synonymum formát

Formát Solr podporuje ekvivalentní a explicitní mapování synonym. Pravidla mapování dodržují specifikaci filtru synonym a open source apache solr, popsanou v tomto dokumentu: [Synonymfilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Níže je ukázkové pravidlo pro ekvivalentní synonyma.
```
USA, United States, United States of America
```

S výše uvedeným pravidlem se vyhledávací dotaz "USA" rozšíří na "USA" NEBO "Spojené státy" nebo "Spojené státy americké".

Explicitní mapování je označeno šipkou "=>". Pokud je zadán, termín sekvence vyhledávacídotaz, který odpovídá levé straně "=>" bude nahrazen alternativami na pravé straně. Vzhledem k níže uvedenému pravidlu se vyhledávací dotazy "Washington", "Wash". nebo "WA" budou všechny přepsány na "WA". Explicitní mapování platí pouze v zadaném směru a v tomto případě nepřepíše dotaz "WA" na "Washington".
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>Seznam synonym mapy pod vaší služby.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Získejte mapu synonym pod vaší službou.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Odstraňte mapu synonym pod vaší službou.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Nakonfigurujte prohledávatelné pole tak, aby v definici indexu používalo mapu synonym.

Nová vlastnost pole **synonymaMapy** lze použít k určení mapy synonympro použití pro prohledávatelné pole. Mapy synonym jsou prostředky na úrovni služby a lze na ně odkazovat podle libovolného pole indexu v rámci služby.

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

**synonymumMapy** lze zadat pro prohledávatelná pole typu Edm.String nebo Collection(Edm.String)".

> [!NOTE]
> Pro jedno pole můžete mít pouze jednu mapu synonym. Pokud chcete použít více synonymmap, dejte nám prosím vědět na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Dopad synonym na další funkce vyhledávání

Funkce synonym přepíše původní dotaz synonymy s operátorem OR. Z tohoto důvodu přístupů zvýraznění a bodování profily považovat původní termín a synonyma jako ekvivalentní.

Funkce synonyma se vztahuje na vyhledávací dotazy a nevztahuje se na filtry nebo omezující vlastnosti. Podobně jsou návrhy založeny pouze na původním termínu; synonyma shody se nezobrazí v odpovědi.

Rozšíření synonyma se nevztahují na zástupné hledané výrazy. termíny předpony, rozmazání a regulárnívýraz nejsou rozbaleny.

Pokud potřebujete provést jeden dotaz, který použije rozšíření synonyma a zástupné výrazy, regulární výraz nebo přibližné hledání, můžete kombinovat dotazy pomocí syntaxe OR. Chcete-li například kombinovat synonyma se zástupnými znaky `<query> | <query>*`pro jednoduchou syntaxi dotazu, termín by byl .

Pokud máte existující index ve vývojovém (neprodukčním) prostředí, experimentujte s malým slovníkem, abyste zjistili, jak přidání synonym změní prostředí vyhledávání, včetně dopadu na profily hodnocení, zvýraznění přístupů a návrhy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření mapy synonym](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)