---
title: 'Kurz: extrakce strukturovaných dat pomocí počítače se naučilou entitou – LUIS'
titleSuffix: Azure Cognitive Services
description: Extrahujte strukturovaná data z utterance pomocí uživatelsky získané entity. Chcete-li zvýšit přesnost extrakce, přidejte dílčí komponenty s popisovači a omezeními.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 2d3bffd025d484ac928e21003b7cba9c63d2c514
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885811"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Kurz: extrakce strukturovaných dat ze utterance uživatelů pomocí entit strojového učení v Language Understanding (LUIS)

V tomto kurzu extrahujete strukturovaná data z utterance pomocí entity, která se naučila počítačem.

Entita získaná počítačem podporuje [koncept rozloženého modelu](luis-concept-model.md#v3-authoring-model-decomposition) tím, že poskytuje entity dílčí komponenty s jejich popisovači a omezeními.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Co se v tomto kurzu naučíte:**

> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Přidat entitu získanou počítačem
> * Přidat dílčí komponentu
> * Přidat popisovač dílčí komponenty
> * Přidat omezení dílčí součásti
> * Vytrénovat aplikaci
> * Testovací aplikace
> * Publikování aplikace
> * Získat předpověď entity z koncového bodu

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Proč používat entitu získanou počítačem?

V tomto kurzu se k extrakci dat z utterance přidá entita získaná počítačem.

Účelem entity je definování dat k extrakci. To zahrnuje poskytnutí názvu dat, typu (Pokud je to možné), jakéhokoli rozlišení dat v případě nejednoznačnosti a přesného textu, který tvoří data.

Pokud chcete definovat entitu, musíte vytvořit entitu a potom označit text reprezentující entitu v příkladu utterance. Tyto příklady s popisky LUIS, co je entita a kde se dají najít v utterance.

## <a name="entity-decomposability-is-important"></a>Devytváření entit je důležité.

Devytváření entit je důležité pro předpověď záměru i pro extrakci dat.

Začněte s datovou entitou získanou počítačem, která je začátkem a entitou nejvyšší úrovně pro extrakci dat. Pak rozloží entitu na části, které vyžaduje klientská aplikace.

I když si nejste jistí, jak vám entita při zahájení vaší aplikace požadujete, doporučujeme začít s entitou získanou počítačem, která se pak rozloží s dílčími komponentami v době, kdy se vaše aplikace nachází.

V praktických případech vytvoříte entitu získanou počítačem, která bude představovat objednávku pro aplikaci Pizza. Pořadí musí mít všechny části, které jsou nezbytné pro celou objednávku. Chcete-li začít, entita extrahuje text související s objednávkami, velikost vybírá a množství.

Utterance pro `Please deliver one large cheese pizza to me` by měl jako pořadí extrahovat `one large cheese pizza` a pak taky extrahovat `1` a `large`.

Existuje další rozklad, který můžete přidat, jako je vytváření dílčích komponent pro toppings nebo crust. Po provedení tohoto kurzu byste měli mít jistotu, že přidáte tyto dílčí komponenty do existující entity `Order`.

## <a name="import-example-json-to-begin-app"></a>Import example. JSON pro zahájení aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Popisek textu jako entity v příkladu projevy

K extrakci podrobností o pizzaém pořadí můžete vytvořit nejvyšší úroveň `Order` entitu poučenou počítač.

1. Na stránce **záměry** vyberte záměr **OrderPizza** .

1. V seznamu ukázkový projevy vyberte následující utterance.

    |Pořadí příkladu utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Začněte výběr těsně před textem `pickup` (#1) nejvíce vlevo, a pak jít hned za text, který je nejvíce vpravo, `anchovies` (#2 – tím se ukončí proces označování). Zobrazí se místní nabídka. V automaticky otevíraném okně zadejte název entity jako `Order` (#3). Pak v seznamu vyberte `Order - Create new entity` (#4).

    ![Popisek začátek a konec textu pro úplné pořadí](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Entita nebude vždy celá utterance. V tomto konkrétním případě `pickup` určuje, jak má být objednávka přijata. Z koncepční perspektivy by `pickup` měla být součástí označené entity pro danou objednávku.

1. V poli **Zvolte typ entity** vyberte **přidat strukturu** a pak vyberte **Další**. Struktura je nutná pro přidání dílčích součástí, jako je velikost a množství.

    ![Přidání struktury k entitě](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. V poli **vytvořit entitu počítač** , kterou jste získali v poli **Struktura** přidejte `Size` pak vyberte Enter.
1. Chcete-li přidat **popisovač**, vyberte `+` v rozevíracích seznamech **pro velikost** oblasti a pak vyberte možnost **vytvořit nový seznam frází**.

1. V poli **deskriptor vytvoření nového seznamu frází** zadejte název `SizeDescriptor` pak zadejte hodnoty: `small`, `medium`a `large`. Když se v poli **návrhy** vyplní, vyberte `extra large`a `xl`. Vyberte **Hotovo** a vytvořte nový seznam frází.

    Tento popisovač seznamu frází pomáhá `Size` dílčí komponentě najít slova týkající se velikosti tím, že jim poskytnou ukázková slova. Tento seznam nemusí zahrnovat slovo každé velikosti, ale měl by obsahovat slova, která se očekávají pro indikaci velikosti.

    ![Vytvoření popisovače pro podsoučást velikosti](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. V okně **vytvořit entitu počítače** , vyberte **vytvořit** a dokončete vytváření `Size` dílčí součásti.

    Entita `Order` s `Size` komponentou je vytvořena, ale v utterance byla použita pouze entita `Order`. V příkladu utterance je nutné označit text entity `Size`.

1. Ve stejném příkladu utterance označte podsoučást **velikosti** `large` tím, že vyberete slovo a pak v rozevíracím seznamu vyberete entitu **Velikost** .

    ![Označte entitu Size pro text v utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Čára je v textu plná, protože se shoduje jak s popiskem, tak s předpověďí, protože text byl explicitně označený.

1. Označte entitu `Order` ve zbývající projevy spolu s entitou Size. Hranaté závorky v textu označují označení `Order` entitu a entitu `Size` v rámci.

    |Pořadí příkladu projevy|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Ve všech zbývajících příkladech projevy vytvořit entitu a dílčí součásti.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Jak se zachází s implicitními daty, jako je například písmeno `a` implikuje jeden Pizza? Nebo chybí `pickup` a `delivery`, která značí, kde se očekává Pizza? Nebo chybějící velikost pro indikaci výchozí velikosti malých nebo velkých? Zvažte možnost zpracovat implicitní zpracování dat jako součást vašich obchodních pravidel v klientské aplikaci místo nebo kromě LUIS.

1. Pokud chcete aplikaci naučit, vyberte **vlak**. Školení aplikuje změny, jako jsou nové entity a označené projevy, na aktivní model.

1. Po školení přidejte do záměru nový příklad utterance, abyste zjistili, jak dobře LUIS rozumí entitě učené počítačem.

    |Pořadí příkladu utterance|
    |--|
    |`pickup XL meat lovers pizza`|

    Celková nejvyšší entita, `Order` je označena a `Size` dílčí komponenta je označena také tečkovanými řádky. Toto je úspěšná předpověď.

    ![Nový příklad utterance s entitou](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Tečkovaná čára označuje předpověď.

1. Pokud chcete změnit předpověď na entitu s popiskem, vyberte řádek a pak vyberte **Potvrdit entitu předpovědi**.

    ![Přijměte předpověď výběrem možnosti potvrdit předpověď entity.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    V tomto okamžiku je entita získaná počítačem funkční, protože by mohla najít entitu v rámci nového ukázkového utteranceu. Když přidáte příklad projevy, pokud entita není předpovězena správně, označte entitu a dílčí součásti. Pokud je entita předpokládaná správně, nezapomeňte předpovědi potvrdit.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Přidat předem připravené číslo, které vám pomůžou extrahovat data

Informace o objednávkách by měly obsahovat také počet položek v pořadí, například kolik pizzas. Chcete-li extrahovat tato data, je nutné do `Order` přidat novou podsoučást získanou počítačem a tato součást vyžaduje omezení předem vytvořeného čísla. Když omezíte entitu na předem sestavené číslo, entita najde a extrahuje čísla, ať už text je číslice, `2`nebo text, `two`.

Začněte tím, že do aplikace přidáte předem vytvořenou číselnou entitu.

1. V nabídce vlevo vyberte **entity** a pak vyberte **+ Přidat předem vytvořenou entitu**.

1. V poli **Přidat předem připravené entity** vyhledejte a vyberte **číslo** a potom vyberte **Hotovo**.

    ![Přidat předem připravených entit](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Předem vytvořená entita se přidá do aplikace, ale ještě není omezením.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Vytvořit entitu dílčí součásti s omezením pro získání dat

Entita `Order` by měla mít dílčí komponentu `Quantity` k určení, kolik položek je v pořadí. Množství by mělo být omezeno na číslo, aby byla extrahovaná data okamžitě použitelná klientskou aplikací.

Omezení se použije jako shoda textu, buď s přesnou shodou (jako je například entita seznamu), nebo prostřednictvím regulárních výrazů (jako je například entita regulárního výrazu nebo předem vytvořená entita).

Pomocí omezení je extrahován pouze text, který odpovídá tomuto omezení.

1. Vyberte **entity a pak vyberte** entitu `Order`.
1. Vyberte **+ Přidat komponentu** a potom zadejte název `Quantity` pak vyberte zadat, aby se nová entita přidala do aplikace.
1. Po úspěšném oznámení vyberte podsoučást `Quantity` a pak vyberte tužka omezení.
1. V rozevíracím seznamu vyberte předem vytvořené číslo.

    ![Vytvoří entitu množství s předdefinovaným číslem jako omezením.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Entita `Quantity` se použije, pokud se najde text, který odpovídá předdefinované číselné entitě.

    Entita s omezením je vytvořena, ale ještě nebyla použita na příklad projevy.

    > [!NOTE]
    > Dílčí komponenta může být vnořena v rámci jedné součásti až na 5 úrovní. I když se to v tomto článku nezobrazuje, je k dispozici z portálu a rozhraní API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Příklad popisku utterance na učení LUIS o entitě

1. V levém navigačním panelu vyberte **záměry** a pak vyberte záměr **OrderPizza** . Tři čísla v následujících projevy jsou označena, ale jsou vizuálně pod řádkem entity `Order`. Tato nižší úroveň znamená, že se entity nacházejí, ale nepovažují se za ne`Order`ou entitu.

    ![Předem sestavené číslo je nalezeno, ale není považováno za neberoucí se k tomu mimo entitu Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Označte čísla `Quantity` entitou, a to tak, že vyberete `2` v příkladu utterance a potom v seznamu vyberete `Quantity`. Označte `6` a `1` ve stejném příkladu utterance.

    ![Text popisku s entitou množství](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Výuka aplikace pro použití změn entit v aplikaci

Vyberte **vlak** , pomocí kterého se aplikace naučíte těmito novými projevy.

![Projděte si aplikaci a pak si prohlédněte příklad projevy.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

V tomto okamžiku má objednávka několik detailů, které je možné extrahovat (velikost, množství a text objednávky celkem). Existuje další úprava `Order` entity, například Pizza toppings, typ crust a vedlejší objednávky. Každý z nich by měl být vytvořen jako dílčí součásti entity `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Otestování aplikace, aby se ověřily změny

Otestujte aplikaci pomocí interaktivního **testovacího** panelu. Tento proces vám umožní zadat nový utterance a potom zobrazit výsledky předpovědi a zjistit, jak dobře aktivní a školená aplikace funguje. Předpověď záměru by měla být poměrně důvěrná (vyšší než 70%) a extrakce entit by měla vybrat alespoň `Order` entitu. Podrobnosti o entitě Order mohou chybět, protože 5 projevy není dostatečné pro zpracování každého případu.

1. V horní navigaci vyberte **Test** (Testovat).
1. Zadejte `deliver a medium veggie pizza` utterance a vyberte Enter. Aktivní model předpovídá správný záměr s více než 70% jistotou.

    ![Zadejte nový utterance k otestování záměru.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Kliknutím na **zkontrolovat** Zobrazte entitu předpovědi.

    ![Podívejte se na předpovědi entit na interaktivním panelu Test.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Velikost byla správně zjištěna. Mějte na paměti, že příklad projevy v záměru `OrderPizza` nemá příklad `medium` jako velikost, ale použijte popisovač seznamu `SizeDescriptor` fráze, který obsahuje střední hodnotu.

    Množství není správně předpovězeno. Chcete-li tento problém vyřešit, můžete přidat další příklady projevy pomocí tohoto slova k označení množství a označení tohoto slova jako entity `Quantity`.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adrese a zadejte stejný dotaz, jako jste zadali na interaktivním panelu testů.

    `deliver a medium veggie pizza`

    Poslední parametr řetězce dotazu je `query`, což je **dotaz** promluvy.

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Kurz – záměry](luis-quickstart-intents-only.md)
* [Koncept –](luis-concept-entity-types.md) koncepční informace o entitách
* [Koncept – funkce](luis-concept-feature.md) koncepční informace
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu aplikace používá entitu získanou počítačem k nalezení záměru utterance uživatele a extrakci podrobností z tohoto utterance. Pomocí entity, která se naučila počítač, můžete rozložit podrobnosti o entitě.

> [!div class="nextstepaction"]
> [Přidání předem připravené entity klíčové fráze](luis-quickstart-intent-and-key-phrase.md)
