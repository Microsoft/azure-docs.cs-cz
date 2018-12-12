---
title: Role modelu
titleSuffix: Azure Cognitive Services
description: Použijte vzor k extrakci dat z dobře formulované šablony promluvy. Šablona promluvy používá jednoduchou entitu a role k extrakci takových souvisejících dat, jako jsou umístění původu a umístění cíle.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b6d800705509edc31b410d1e9cd30f8b53702010
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094402"
---
# <a name="tutorial-4-extract-contextually-related-patterns"></a>Kurz 4: Extrakce vzorů souvisejících s kontextem

V tomto kurzu budete používat vzor k extrakci dat z dobře formulované šablony promluvy. Šablona promluvy používá jednoduchou entitu a role k extrakci takových souvisejících dat, jako jsou umístění původu a umístění cíle.  Záměr bude při použití vzorů potřebovat méně ukázkových promluv.

Účelem rolí je extrahovat z promluvy entity související s kontextem. V promluvě `Move new employee Robert Williams from Sacramento and San Francisco` jsou hodnoty města původu a cílového města ve vztahu jedna k druhé a odlišují se jen obecným jazykem. 


Jméno nového zaměstnance (Billy Patterson) ještě není součástí seznamu entity **Zaměstnanec**. Jméno nového zaměstnance se extrahuje jako první, aby bylo možné poslat je do externího systému, kde se vytvoří přihlašovací údaje společnosti. Po jejich vytvoření se přihlašovací údaje zaměstnance přidají do seznamu entity **Zaměstnanec**.

Nového zaměstnance s rodinou je teď potřeba přesunout z aktuálního města do města, kde sídlí fiktivní společnost. Protože může nový zaměstnanec pocházet z libovolného města, je potřeba zjistit umístění. Seznam sad, jako například seznam entity, by nefungoval, protože to by dovolovalo extrahovat pouze města, která tento seznam obsahuje.

Názvy rolí spojených s městem původu a cílovým městem musejí být jedinečná napříč všemi entitami. Snadný způsob, jak zajistit, aby byly role jedinečné, je spojit tyto role s entitou, která je obsahuje, pomocí strategie vytváření názvů. Entita **NewEmployeeRelocation** je jednoduchá entita obsahující dvě role: **NewEmployeeReloOrigin** and **NewEmployeeReloDestination**. Relo je zkráceně relokace (přemístění).

Protože má ukázková promluva `Move new employee Robert Williams from Sacramento and San Francisco` pouze strojově naučené entity, je důležité poskytnout záměru dostatek ukázkových promluv, aby se daly entity určit.  

**Vzory sice umožňují poskytovat méně ukázkových promluv, když ale není detekovaná entita, vzor se nespáruje.**

Pokud máte potíže s určováním jednoduché entity, protože jde o název, například města, zvažte přidání seznamu frází podobných hodnot. Pomáhá to detekci názvu města tím, že LUIS dostane další signál, že jde o určitý typ slova nebo fráze. Seznamy frází jen pomáhají vzoru detekovat entitu. To je nutné pro spárování vzoru. 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Používat existující ukázkovou aplikaci
> * Tvorba nových entit
> * Tvorba nového záměru
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu
> * Vytvořit vzor s rolemi
> * Vytvořit seznam frází měst
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `roles`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="create-new-entities"></a>Tvorba nových entit

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. V levé navigaci vyberte **Entities** (Entity). 

3. Vyberte **Create new entity** (Vytvořit novou entitu).

4. V místním okně zadejte `NewEmployee` jako **Simple** (Jednoduchá) entitu.

5. Vyberte **Create new entity** (Vytvořit novou entitu).

6. V místním okně zadejte `NewEmployeeRelocation` jako **Simple** (Jednoduchá) entitu.

7. Vyberte **NewEmployeeRelocation** ze seznamu entit. 

8. Zadejte `NewEmployeeReloOrigin` jako první roli.

9. Zadejte `NewEmployeeReloDestination` jako druhou roli.

## <a name="create-new-intent"></a>Tvorba nového záměru
Označování entit v následujících krocích může být jednodušší, když před jeho zahájením odstraníte předpřipravenou entitu keyPhrase. V tom případě pak tuto entitu po dokončení popsaného postupu přidejte zpátky. 

1. Vyberte **Intents** (Záměry) v levé navigaci.

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. Zadejte `NewEmployeeRelocationProcess` jako název záměru do vyskakovacího dialogového okna.

4. Vložte následující ukázkové promluvy, které označují nové entity. Hodnoty entita a role jsou tučně. Pokud je pro vás jednodušší text označovat, přepněte **Tokens View** (Zobrazení tokenů). 

    Označením záměru nestanovíte roli entity. To uděláte později, při tvorbě vzoru. 

    |Promluva|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Přestěhovat **Bob Jones** ze **Seattle** do **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Přestěhovat **Dave C. Cooper** z **Redmond** do **New York City**|Dave C. Cooper|Redmond, New York City|
    |Přestěhovat **Jim Paul Smith** z **Toronto** do **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Přestěhovat **J. Benson** z **Boston** do **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Přestěhovat **Travis "Trav" Hinton** z **Castelo Branco** do **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Přestěhovat **Trevor Nottington III** z **Aranda de Duero** do **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Přestěhovat **Dr. Greg Williams** z **Orlando** do **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Přestěhovat **Robert "Bobby" Gregson** z **Kansas City** do **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Přestěhovat **Patti Owens** z **Bellevue** do **Rockford**|Patti Owens|Bellevue, Rockford|
    |Přestěhovat **Janet Bartlet** z **Tuscan** do **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    Jméno zaměstnance obsahuje řadu prefixů a sufixů, má různý počet slov a různou syntaxi. Pomáhá to LUIS pochopit variace nového jména zaměstnance. Názvy měst mají také různý počet slov a různou syntaxi. Tato rozmanitost učí LUIS, jak se tyto entity můžou objevovat v promluvě uživatele. 
    
    Pokud by měla entita stejný počet slov a byla bez dalších rozdílů, naučíte tím LUIS, že tato entita může mít pouze tento počet slov a žádné další varianty. LUIS nebude schopný správně predikovat širší sadu variací, protože jste mu žádné neukázali. 

    Pokud jste odebrali entitu keyPhrase, přidejte ji teď zpátky do aplikace.

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Na konec adresy URL zadejte `Move Wayne Berry from Miami to Mount Vernon`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

Skóre predikce záměru je pouze kolem 50 %. Pokud vaše klientská aplikace vyžaduje skóre vyšší, je to potřeba opravit. Ani entity se nepredikovaly.

Jedna lokace se extrahovala, druhá nikoli. 

Vzory pomůžou se skóre predikce, ale entity musejí být správně predikované dřív, než se vzor s promluvou spáruje. 

## <a name="pattern-with-roles"></a>Vzor s rolemi

1. V horní navigaci vyberte **Build** (Sestavení).

2. Vyberte **Patterns** (Vzory) v levé navigaci.

3. Vyberte **NewEmployeeRelocationProcess** z rozevíracího seznamu **Select an intent** (Výběr záměru). 

4. Zadejte následující vzor: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Když trénujete, publikujete nebo se dotazujete koncového bodu, můžete být zklamaní, že se entitu nedaří najít, takže se vzor nespáruje a predikce se proto nevylepšuje. To je důsledek nedostatku ukázkových promluv s označenými entitami. Přidejte seznam frází, ne větší počet ukázek. Problém tak odstraníte.

## <a name="cities-phrase-list"></a>Seznam frází měst
Názvy měst jsou, stejně jako jména lidí, nevypočitatelné. Můžou obsahovat libovolnou směs slov a interpunkce. Města v oblasti a ve světě ale známe, takže LUIS potřebuje jen seznam frázi měst, aby se začal učit. 

1. Vyberte **Phrase list** (Seznam frází) ze sekce **Improve app performance** (Zvýšit výkon aplikace) v nabídce vlevo. 

2. Nazvěte seznam `Cities` a nastavte pro něj následující `values`:

    |Hodnoty seznamu frází|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Nepřidávejte všechna města světa, dokonce ani všechna z oblasti. LUIS musí umět zobecnit, co ze seznamu je město. Ujistěte se, že položka **These values are interchangeable** (Tyto hodnoty jsou zaměnitelné) zůstane vybraná. Tohle nastavení znamená, že všechna slova ze seznamu se budou považovat za synonyma. 

3. Trénujte a publikujte aplikaci.

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na konec adresy URL zadejte `Move wayne berry from miami to mount vernon`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

Skóre záměru je mnohem vyšší a názvy rolí jsou součástí odpovědi entity.

## <a name="hierarchical-entities-versus-roles"></a>Hierarchické entity a role

V [hierarchical tutorial](luis-quickstart-intent-and-hier-entity.md) (hierarchický kurz) záměr **MoveEmployee** detekoval, kdy přestěhovat existujícího zaměstnance z jedné budovy a kanceláře do jiné. Ukázkové promluvy obsahovali umístění původu a cílové umístění, ale nepoužívaly role. Místo toho byly počátek a cíl podřízenými prvky hierarchické entity. 

V tomto kurzu aplikace Human Resources detekuje promluvy, které se týkají stěhování zaměstnanců z jednoho města do druhého. Tyhle dva typy promluv jsou sice shodné, ale LUIS je řeší jinými schopnostmi.

|Kurz|Ukázková promluva|Umístění původu a cíle|
|--|--|--|
|[Hierarchicky (bez rolí)](luis-quickstart-intent-and-hier-entity.md)|ps Jill Jones z **a-2349** do **b-1298**|a-2349, b-1298|
|Tento kurz (s rolemi)|Přestěhovat Billy Patterson z **Yuma** do **Denver**.|Yuma, Denver|

Další informace najdete v tématu věnovaném [porovnání rolí a hierarchických entit](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

Tento kurz přidal entitu s rolemi a záměr s ukázkovými promluvami. První predikce koncového bodu za použití entity správně predikovala záměr, ale s nízkým skóre sebejistoty. Detekovala se jen jedna entita ze dvou. Tento kurz dále přidal vzor, který použil role entity a seznam frází ke zvýšení hodnoty názvů měst v promluvách. Druhá predikce koncového bodu vrátila vysoké skóre sebejistoty a našla obě role entity. 

> [!div class="nextstepaction"]
> [Naučte se osvědčené postupy pro aplikace LUIS](luis-concept-best-practices.md)
