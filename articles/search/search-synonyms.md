---
pageTitle: Synonyms in Azure Search | Microsoft Docs
description: Použití synonym rozbalte obor vyhledávací dotaz
authors: mhko
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
manager: jlembicz
ms.author: nateko
ms.openlocfilehash: 9f887b065cf4fcc295873ee969030c67d17d9e2f
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318465"
---
# <a name="synonyms-in-azure-search"></a>Synonyma ve službě Azure Search

Synonyma ve vyhledávací weby přidružují ekvivalentní termíny, které implicitně rozbalte obor dotazu, aniž by uživatel, nebudete muset zadávat ve skutečnosti termín. Například zadaný termín "pes" a synonymum přidružení "canine" a "štěněte" všechny dokumenty, které obsahují "pes", "PSA" nebo "štěněte" přejde v rámci oboru dotazu.

Ve službě Azure Search se synonyma rozšíření provádí v době zpracování dotazu. Přidání mapy synonym k službě s žádné dopadem na existující operace. Můžete přidat **synonymMaps** vlastnost na definici pole bez nutnosti znovu sestavovat index.

## <a name="feature-availability"></a>Dostupnost funkcí

Funkce synonym je podporovaná v nejnovější verzi rozhraní api (api-version = 2017-11-11). Podpora webu Azure Portal se v současnosti neposkytuje.

## <a name="how-to-use-synonyms-in-azure-search"></a>Použití synonym ve službě Azure search

Podpora synonym ve službě Azure Search je podle map synonym, které definují a nahrát do služby. Tyto mapy představují nezávislý prostředek (jako jsou indexy nebo zdroje dat) a můžou používat v libovolném prohledávatelném poli v libovolném indexu ve vyhledávací službě.

Mapy synonym a indexy jsou udržovány nezávisle na sobě. Po definování mapu synonym a nahrajte ho do vaší služby, můžete povolit funkci synonym u pole tak, že přidáte novou vlastnost s názvem **synonymMaps** v definici pole. Vytváření, aktualizaci a odstraňování mapu synonym je vždy celého dokumentu operaci, což znamená, že je nelze vytvořit, aktualizovat nebo odstranit součástí mapy synonym postupně. Aktualizuje se ještě jedna položka vyžaduje opakované načtení.

Začlenění do vyhledávací aplikaci synonyma je dvoustupňový proces:

1.  Přidejte mapu synonym k vaší vyhledávací službě prostřednictvím rozhraní API níže.  

2.  Nakonfigurujte prohledávatelná pole mapy synonym v definici indexu.

### <a name="synonymmaps-resource-apis"></a>Rozhraní API pro SynonymMaps prostředků

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Přidat nebo aktualizovat mapu synonym v rámci služby, pomocí příspěvku nebo PUT.

Map synonym, se nahraje do služby prostřednictvím POST a PUT. Každé pravidlo musí být odděleny znak nového řádku ('\n'). Můžete definovat až 5 000 na mapu synonym v bezplatné služby a 10 000 pravidel v všechny ostatní SKU. Každé pravidlo může mít až 20 rozšíření.

Mapy synonym musí být ve formátu Apache Solr, který je popsán níže. Pokud máte existující slovník synonym v jiném formátu a chcete ho použít přímo, dejte nám prosím vědět o [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Můžete vytvořit novou mapu synonym pomocí HTTP POST, jako v následujícím příkladu:

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

    {  
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

Alternativně můžete pomocí PUT a zadejte název mapy synonym v identifikátoru URI. Pokud mapu synonym neexistuje, vytvoří se.

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

    {  
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Formát synonymum Apache Solr

Solr formát podporuje mapování synonym ekvivalentní a explicitní. Pravidla mapování dodržovat specifikace filtru synonymum opensourcových Apache Solr, popsané v tomto dokumentu: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Následuje ukázka pravidla ekvivalentní synonym.
```
USA, United States, United States of America
```

S pravidlem nad vyhledávací dotaz se rozbalí "USA" na "USA" nebo "USA" nebo "USA".

Šipka označuje explicitního mapování "= >". Pokud zadaný výraz posloupnost vyhledávací dotaz, který odpovídá levé straně výrazu "= >" bude nahrazena adresou alternativy na pravé straně. Zadané pravidlo níže, vyhledávací dotazy "Washington", "Wash." nebo "WA" budou všechny být přepsány, aby "WA". Explicitní mapování pouze platí v určeném směru a nepřepíše dotazu "WA" k "Washington" v tomto případě.
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>V rámci služby mapy synonym seznamu.

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2017-11-11
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>Získáte mapu synonym v rámci služby.

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>Odstraňte mapu synonym v rámci služby.

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2017-11-11
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Nakonfigurujte prohledávatelná pole mapy synonym v definici indexu.

Nové vlastnosti pole **synonymMaps** slouží k určení mapu synonym pro prohledávatelné pole. Mapy synonym jsou prostředky úrovně služeb a může být odkazováno podle libovolného pole indexu v rámci služby.

    POST https://[servicename].search.windows.net/indexes?api-version=2017-11-11
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

**synonymMaps** je možné zadat pro prohledávatelná pole typu 'Edm.String' nebo "Collection(Edm.String)".

> [!NOTE]
> Můžete mít jenom jeden synonymum namapovat na pole. Pokud chcete použít několik map synonym, dejte nám prosím vědět o [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Dopad synonyma u dalších funkcí pro hledání

Funkce synonym přepíše původní dotaz s synonyma s operátorem OR. Z tohoto důvodu přístupů zvýrazňování a profily skórování považovat za původní období a synonyma ekvivalentní.

Funkce synonym platí pro vyhledávací dotazy a neplatí pro filtry nebo omezující vlastnosti. Obdobně doporučení vycházejí pouze původní termín; synonymum shody se nezobrazují v odpovědi.

Synonymum rozšíření se nedá použít u hledané termíny zástupný znak; Předpona, přibližných shod a regulární výraz podmínky nejsou rozšířit.

Pokud je potřeba udělat pomocí jediného dotazu, který se týká synonymum rozšíření a zástupný znak, regex, vyhledávání přibližných shod, můžete kombinovat pomocí syntaxe nebo dotazy. Synonyma zkombinovat se zástupnými znaky pro jednoduchá syntaxe dotazů, jako by třeba `<query> | <query>*`.

## <a name="tips-for-building-a-synonym-map"></a>Tipy pro vytváření mapy synonym

- Mapu synonym stručný a dobře navržené je efektivnější než o vyčerpávající seznam možných shod. Příliš velký nebo složitý slovníky trvat delší dobu analyzovat a vliv latence dotazu, pokud dotaz rozšíří na mnoho synonyma. Místo odhad, ve kterém mohou být použity podmínky, můžete získat skutečný podmínky prostřednictvím [hledání sestavy analýzy provozu](search-traffic-analytics.md).

- Jako předběžná verze i ověřování vykonávat, povolit a pak tuto sestavu, jež přesně určit, jaké termíny bude těžit z synonymum shoda a pak ho nadále používat jako ověření, že se vaše mapy synonym vytváření lepší výsledky. V předdefinované sestavě dlaždice "nejběžnější vyhledávací dotazy" a "nula výsledků vyhledávacích dotazů" získáte potřebné informace.

- Můžete vytvořit několik map synonym pro vaše vyhledávací aplikace (například v jazyce, pokud vaše aplikace podporuje základní vícejazyčné zákazníka). V současné době pole lze použít pouze jeden z nich. Kdykoli můžete aktualizovat vlastnost synonymMaps pole.

## <a name="next-steps"></a>Další postup

- Pokud máte existující index ve vývojovém prostředí (neprodukčním), můžete Experimentujte s malé slovník naleznete v tématu jak přidání synonym mění možnosti vyhledávání, včetně dopad na bodovací profily, zvýrazňování a návrhy.

- [Povolení analýzy provozu vyhledávání](search-traffic-analytics.md) pomocí předdefinované sestavy Power BI další podmínky, které se používají nejvíce a ty, které vrátí nulové dokumenty. Ozbrojené díky těmto poznatkům revidovat slovníku, který bude obsahovat synonyma pro neproduktivní dotazy, které by měl být řešení na dokumenty v indexu.
