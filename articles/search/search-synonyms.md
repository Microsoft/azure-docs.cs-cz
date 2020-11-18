---
title: Synonyma pro rozšíření dotazu přes vyhledávací index
titleSuffix: Azure Cognitive Search
description: Vytvořením mapy synonym můžete rozšířit rozsah vyhledávacího dotazu na index služby Azure Kognitivní hledání. Rozsah je rozšířen tak, aby zahrnoval ekvivalentní výrazy, které zadáte v seznamu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a8f1fa07b94072d37cf83320b6c8956d3b412f12
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701112"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synonyma v Azure Kognitivní hledání

Synonyma v vyhledávačích spojují ekvivalentní termíny, které implicitně rozšiřují rozsah dotazu, a to bez toho, aby ho uživatel skutečně neposkytoval. Například s ohledem na pojem "pes" a přidružení synonym typu "Canine" a "Puppy" budou všechny dokumenty obsahující slovo "pes", "Canine" nebo "Puppy" spadat do rozsahu dotazu.

V Azure Kognitivní hledání je rozšíření synonym provedeno v době dotazu. Můžete přidat mapy synonym ke službě bez přerušení pro stávající operace. Do definice pole můžete přidat vlastnost  **synonymMaps** , aniž byste museli index znovu sestavit.

## <a name="create-synonyms"></a>Vytvořit synonyma

Neexistuje žádná podpora portálu pro vytváření synonym, ale můžete použít sadu REST API nebo .NET SDK. Pokud chcete začít s REST, doporučujeme, abyste provedli [nebo Visual Studio Code](search-get-started-rest.md) a formulování požadavků pomocí tohoto rozhraní API: [vytvořit mapy synonym](/rest/api/searchservice/create-synonym-map). Pro vývojáře v jazyce C# můžete začít [přidáním synonym v Azure rozpoznávání vyhledávání pomocí jazyka c#](search-synonyms-tutorial-sdk.md).

Pokud v případě, že používáte [klíče spravované zákazníkem](search-security-manage-encryption-keys.md) pro šifrování na straně služby, můžete použít i tuto ochranu na obsah vaší mapy synonym.

## <a name="use-synonyms"></a>Použít synonyma

V Azure Kognitivní hledání je podpora synonym založená na mapách synonym, které definujete a nahráváte do vaší služby. Tyto mapy představují nezávislý prostředek (například indexy nebo zdroje dat) a lze jej použít v jakémkoli hledaném poli libovolného indexu ve vyhledávací službě.

Mapy synonym a indexy jsou uchovávány nezávisle. Jakmile definujete mapu synonym a nahrajete ji do služby, můžete povolit funkci synonym v poli přidáním nové vlastnosti s názvem **synonymMaps** v definici pole. Vytváření, aktualizace a odstraňování mapy synonym je vždy operace celého dokumentu, což znamená, že nelze vytvořit, aktualizovat nebo odstranit části mapy synonym přírůstkově. Aktualizace dokonce jedné položky vyžaduje opětovné načtení.

Zahrnutí synonym do vaší aplikace pro hledání je proces se dvěma kroky:

1.  Přidejte mapu synonym do vyhledávací služby prostřednictvím následujících rozhraní API.  

2.  Nakonfigurujte prohledávatelné pole tak, aby v definici indexu bylo použito mapování synonym.

Pro vaši vyhledávací aplikaci můžete vytvořit několik map synonym (například podle jazyka, pokud vaše aplikace podporuje vícejazyčnou základnu). V současné době může pole použít pouze jeden z nich. Vlastnost synonymMaps pole můžete kdykoli aktualizovat.

### <a name="synonymmaps-resource-apis"></a>Rozhraní API prostředků SynonymMaps

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Přidejte nebo aktualizujte mapu synonym v rámci vaší služby pomocí POST nebo PUT.

Mapy synonym jsou odesílány do služby prostřednictvím POST nebo PUT. Každé pravidlo musí být odděleno znakem nového řádku (' \n '). Můžete definovat až 5 000 pravidel na mapování synonym v rámci bezplatné služby a 20 000 pravidel na mapu ve všech ostatních SKU. Každé pravidlo může mít až 20 rozšíření.

Mapy synonym musí být ve formátu Apache Solr, který je vysvětlen níže. Pokud máte existující slovník synonym v jiném formátu a chcete ho použít přímo, dejte nám prosím na [UserVoice](https://feedback.azure.com/forums/263029-azure-search)informace.

Novou mapu synonym můžete vytvořit pomocí HTTP POST, jak je znázorněno v následujícím příkladu:

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

Alternativně můžete použít PUT a zadat název mapy synonym na identifikátoru URI. Pokud mapování synonym neexistuje, vytvoří se.

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Formát synonyma Apache Solr

Formát Solr podporuje mapování ekvivalentních a explicitních synonym. Pravidla mapování dodržují specifikace Open Source filtru synonym pro Apache Solr, která je popsaná v tomto dokumentu: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Níže je vzorové pravidlo pro ekvivalentní synonyma.

```
USA, United States, United States of America
```

S výše uvedeným pravidlem se vyhledávací dotaz "USA" rozšíří na "USA" nebo "USA" nebo "USA of America".

Explicitní mapování je označeno šipkou "=>". Je-li tento parametr zadán, nahradí se v pravé straně termín sekvence vyhledávacího dotazu, která odpovídá levé straně slova "=>". Podle níže uvedeného pravidla hledejte dotazy "Washington", "mycí cyklus". neboli "WA", všechna budou přepsána do "WA". Explicitní mapování platí pouze v zadaném směru a nepřepíše dotaz "WA" do "Washington" v tomto případě.

```
Washington, Wash., WA => WA
```

Pokud potřebujete definovat synonyma, která obsahují čárky, můžete je pomocí zpětného lomítka zrušit, jako v tomto příkladu:

```
WA\, USA, WA, Washington
```

Vzhledem k tomu, že zpětné lomítko sám o sobě speciální znak v jiných jazycích, jako je JSON a C#, budete ho pravděpodobně muset poklikatě řídicím panelem. Například JSON odeslaný do REST API pro výše uvedené mapování synonym by vypadalo takto:

```json
    {
       "format":"solr",
       "synonyms": "WA\\, USA, WA, Washington"
    }
```

#### <a name="list-synonym-maps-under-your-service"></a>Vypíše mapy synonym v rámci vaší služby.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>Získejte mapu synonym v rámci vaší služby.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>Odstraňte mapu synonym v rámci vaší služby.

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Nakonfigurujte prohledávatelné pole tak, aby v definici indexu bylo použito mapování synonym.

Novou vlastnost pole **synonymMaps** lze použít k určení mapy synonym, která se má použít pro pole, které chcete prohledávat. Mapy synonym jsou prostředky na úrovni služby a můžou na ně odkazovat libovolné pole indexu v rámci služby.

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
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
```

**synonymMaps** lze zadat pro hledaná pole typu EDM. String nebo Collection (EDM. String).

> [!NOTE]
> Pro každé pole můžete mít pouze jednu mapu synonym. Pokud chcete použít více map synonym, dejte nám prosím na [UserVoice](https://feedback.azure.com/forums/263029-azure-search)informace.

## <a name="impact-of-synonyms-on-other-search-features"></a>Dopad synonym na jiné funkce hledání

Funkce synonym přepíše původní dotaz pomocí synonym pomocí operátoru OR. Z tohoto důvodu se zvýrazňování přístupů a profily vyhodnocování považují za původní podmínky a synonyma jako ekvivalentní.

Funkce synonym se vztahuje na vyhledávací dotazy a nevztahuje se na filtry nebo omezující vlastnosti. Podobně se návrhy opírají jenom na původní období; shody synonym nejsou v odpovědi zobrazeny.

Rozšíření synonym neplatí pro výrazy vyhledávání se zástupnými znaky; výrazy s předponou, přibližnými a regulárními výrazy nejsou rozbaleny.

Pokud potřebujete provést jeden dotaz, který používá rozšíření synonym a zástupné znaky, regulární výrazy nebo přibližné vyhledávání, můžete kombinovat dotazy pomocí syntaxe nebo. Například pro kombinování synonym se zástupnými znaky pro jednoduchou syntaxi dotazu by byl termín `<query> | <query>*` .

Pokud máte ve vývojovém (neprodukčním) prostředí existující index, Experimentujte s malým slovníkem, abyste viděli, jak Přidání synonym mění možnosti hledání, včetně dopadu na profily vyhodnocování, zvýrazňování přístupů a návrhy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření mapy synonym](/rest/api/searchservice/create-synonym-map)