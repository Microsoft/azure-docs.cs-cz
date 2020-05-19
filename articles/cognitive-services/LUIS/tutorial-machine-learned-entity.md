---
title: 'Kurz: extrakce strukturovaných dat pomocí počítače se naučilou entitou – LUIS'
description: Extrahujte strukturovaná data z utterance pomocí uživatelsky získané entity. Chcete-li zvýšit přesnost extrakce, přidejte dílčí entity s funkcemi.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: d1bc8fc6aac52e264cb4352ca05f9df45ccfc50e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588866"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Kurz: extrakce strukturovaných dat ze utterance uživatelů pomocí entit strojového učení v Language Understanding (LUIS)

V tomto kurzu extrahujete strukturovaná data z utterance pomocí entity, která se naučila počítačem.

Entita získaná počítačem podporuje [koncept dekompozice modelu](luis-concept-model.md#v3-authoring-model-decomposition) tím, že poskytuje entity subentity s [funkcemi](luis-concept-feature.md).

**V tomto kurzu:**

> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Přidat entitu získanou počítačem
> * Přidat subentitu a funkci
> * Výuka, testování, publikování aplikace
> * Získat předpověď entity z koncového bodu

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Proč používat entitu získanou počítačem?

V tomto kurzu se k extrakci dat z utterance uživatele přidá entita získaná počítačem.

Entita definuje data, která se mají extrahovat v rámci utterance. To zahrnuje poskytnutí názvu dat, typu (Pokud je to možné), jakéhokoli rozlišení dat v případě nejednoznačnosti a přesného textu, který tvoří data.

Aby bylo možné definovat data, je třeba:
* Vytvoření entity
* Označte text v rámci příkladu projevy, který představuje entitu. Tyto příklady s popisky LUIS, co je entita a kde se dají najít v utterance.

## <a name="entity-decomposability-is-important"></a>Devytváření entit je důležité.

Devytváření entit je důležité pro předpověď záměrů a pro extrakci dat s entitou.

Začněte s datovou entitou získanou počítačem, která je začátkem a entitou nejvyšší úrovně pro extrakci dat. Pak rozložíte entitu na subentity.

I když si nejste jistí, jak vám entita při zahájení aplikace požadujete, doporučujeme začít s entitou učenou počítačem a pak je rozložit s podentitami, když vaše aplikace bude vyspělá.

V tomto kurzu vytvoříte entitu získanou počítačem, která bude představovat objednávku pro aplikaci Pizza. Entita extrahuje text související s objednávkami, velikost vybírá a množství.

Utterance `Please deliver one large cheese pizza to me` by měla být extrahována `one large cheese pizza` jako objednávka a pak také extrahovat `1` pro množství a `large` velikost.

## <a name="download-json-file-for-app"></a>Stáhnout soubor JSON pro aplikaci

Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>Importovat soubor JSON pro aplikaci

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Popisek textu jako entity v příkladu projevy

Chcete-li extrahovat podrobnosti o pizzaém pořadí, vytvořte entitu na nejvyšší úrovni, která se naučila počítačem `Order` .

1. Na stránce **záměry** vyberte záměr **OrderPizza** .

1. V seznamu ukázkový projevy vyberte následující utterance.

    |Pořadí příkladu utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Začněte výběr těsně před textem, který je nejvíce vlevo `pickup` (#1), a pak jít hned za text, který je nejvíce vpravo, `anchovies` (#2 – tím se ukončí proces označování). Zobrazí se místní nabídka. V automaticky otevíraném okně zadejte název entity jako `Order` (#3). Pak vyberte `Order - Create new entity` ze seznamu (#4).

    ![Popisek začátek a konec textu pro úplné pořadí](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Entita nebude vždy celá utterance. V tomto konkrétním případě `pickup` indikuje, jak má být objednávka přijata. Z koncepční perspektivy `pickup` by měla být součástí označené entity pro danou objednávku.

1. V poli **Zvolte typ entity** vyberte **přidat strukturu** a pak vyberte **Další**. Struktura je nutná pro přidání podentit, jako je velikost a množství.

    ![Přidání struktury k entitě](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. V poli **vytvořit entitu počítače** , kterou jste získali v poli **Struktura** přidejte a `Size` pak vyberte Enter.
1. Chcete-li přidat **funkci**, vyberte `+` v oblasti **funkce** položku a pak vyberte možnost **vytvořit nový seznam frází**.

1. V **seznamu vytvořit novou frázi** zadejte název `SizeFeature` a zadejte hodnoty: `small` , `medium` , a `large` . Když pole **návrhy** vyplní, vyberte `extra large` a `xl` . Vyberte **Hotovo** a vytvořte nový seznam frází.

    Tato funkce seznamu frází pomáhá `Size` podentitě najít slova týkající se velikosti tím, že jim poskytne ukázková slova. Tento seznam nemusí zahrnovat slovo každé velikosti, ale měl by obsahovat slova, která se očekávají pro indikaci velikosti.

    ![Vytvoření funkce pro podentitu Size](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. V okně **vytvořit entitu počítače** , vyberte **vytvořit** a dokončete vytváření `Size` subentity.

    `Order`Entita s `Size` entitou je vytvořena, ale v `Order` utterance byla použita pouze entita. `Size`V příkladu utterance je nutné označit text entity.

1. Ve stejném příkladu utterance popisek dílčí entity, **Size** a `large` to tak, že vyberete slovo a pak v rozevíracím seznamu vyberete entitu **Velikost** .

    ![Označte entitu Size pro text v utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Čára je v textu plná, protože se shoduje jak s popiskem, tak s předpověďí, protože text byl _explicitně_ označený.

1. Označte `Order` entitu ve zbývající projevy spolu s entitou Size. Hranaté závorky v textu označují označenou `Order` entitu a `Size` entitu v rámci.

    |Pořadí příkladu projevy|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![V všech zbývajících příkladech projevy udělejte entitu a subentity.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Jak se zachází s implicitními daty, jako je například písmeno, které `a` implikuje jeden Pizza? Nebo chybějící `pickup` a `delivery` k označení, kde se očekává Pizza? Nebo chybějící velikost pro indikaci výchozí velikosti malých nebo velkých? Zvažte možnost zpracovat implicitní zpracování dat jako součást vašich obchodních pravidel v klientské aplikaci místo nebo kromě LUIS.

1. Pokud chcete aplikaci naučit, vyberte **vlak**. Školení aplikuje změny, jako jsou nové entity a označené projevy, na aktivní model.

1. Po školení přidejte do záměru nový příklad utterance, abyste zjistili, jak dobře LUIS rozumí entitě učené počítačem.

    |Pořadí příkladu utterance|
    |--|
    |`pickup XL meat lovers pizza`|

    Celková nejvyšší entita `Order` je označená a `Size` subentita je také označena tečkami.

    ![Nový příklad utterance s entitou](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Tečkovaná čára označuje předpověď.

1. Pokud chcete změnit předpověď na entitu s popiskem, vyberte řádek a pak vyberte **Potvrdit entitu předpovědi**.

    ![Přijměte předpověď výběrem možnosti potvrdit předpověď entity.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    V tomto okamžiku je entita získaná počítačem funkční, protože by mohla najít entitu v rámci nového ukázkového utteranceu. Když přidáte příklad projevy, pokud entita není předpovězená správně, označte entitu a subentity. Pokud je entita předpokládaná správně, nezapomeňte předpovědi potvrdit.


<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="add-subentity-with-feature-of-prebuilt-entity"></a>Přidat subentitu s funkcí předem připravené entity

Informace o objednávkách by měly obsahovat také počet položek v pořadí, například kolik pizzas. Chcete-li extrahovat tato data, je nutné přidat novou podentitu počítačového učení `Order` a, že subentita potřebuje požadovanou funkci předem připraveného čísla. Když použijete funkci předem sestavené entity na předem sestavené číslo, entita najde a extrahuje čísla, ať už text je číslice, `2` nebo text `two` .

## <a name="add-prebuilt-number-entity-to-app"></a>Přidání předem připravené entity čísla do aplikace
Informace o objednávkách by měly také zahrnovat, kolik položek je v pořadí, například kolik pizzas. Chcete-li extrahovat tato data, je nutné přidat novou podsoučást získanou počítačem `Order` a tato součást vyžaduje požadovanou funkci předem připraveného čísla. Po omezení entity na předem sestavené číslo entita najde a extrahuje čísla, ať už text je číslice, `2` nebo text `two` .

Začněte tím, že do aplikace přidáte předem vytvořenou číselnou entitu.

1. V nabídce vlevo vyberte **entity** a pak vyberte **+ Přidat předem vytvořenou entitu**.

1. V poli **Přidat předem připravené entity** vyhledejte a vyberte **číslo** a potom vyberte **Hotovo**.

    ![Přidat předem vytvořenou entitu](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Předem vytvořená entita se přidá do aplikace, ale ještě není funkcí.

## <a name="create-subentity-entity-with-required-feature-to-help-extract-data"></a>Vytvořit entitu subentity s požadovanou funkcí pro extrakci dat

`Order`Entita by měla mít `Quantity` subentitu k určení, kolik položek je v pořadí. Množství by mělo používat požadovanou funkci předem vytvořeného čísla, aby extrahovaná data byla okamžitě k dispozici klientské aplikaci podle názvu.

Požadovaná funkce se použije jako shoda textu, buď s přesnou shodou (jako je například entita seznamu), nebo prostřednictvím regulárních výrazů (jako je například entita regulárního výrazu nebo předem sestavená entita).

Použitím entity nenáročné na počítač jako funkce se extrahuje jenom text, který se shoduje.

1. Vyberte **entity a pak** vyberte `Order` entitu.
1. Vyberte **+ Přidat entitu** a potom zadejte název a `Quantity` potom vyberte Enter, aby se nová podentita přidala k `Order` entitě.
1. Po úspěšném oznámení klikněte v **Rozšířené možnosti**na tužku omezení.
1. V rozevíracím seznamu vyberte předem vytvořené číslo.

    ![Vytvoří entitu množství s předdefinovaným číslem jako omezením.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    `Quantity`Entita se použije, když text odpovídá předdefinované číselné entitě.

    Entita s požadovanou funkcí je vytvořena, ale ještě nebyla použita na příklad projevy.

    > [!NOTE]
    > Subentita může být vnořená v rámci subentity až do 5 úrovní. I když se to v tomto článku nezobrazuje, je k dispozici z portálu a rozhraní API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Příklad popisku utterance na učení LUIS o entitě

1. V levém navigačním panelu vyberte **záměry** a pak vyberte záměr **OrderPizza** . Tři čísla v následujících projevy jsou označená, ale jsou vizuálně pod `Order` řádkem entity. Tato nižší úroveň znamená, že se entity nacházejí, ale nepovažují se za součást `Order` entity.

    ![Předem sestavené číslo je nalezeno, ale není považováno za neberoucí se k tomu mimo entitu Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Označte čísla s `Quantity` entitou tak, že na `2` příkladu utterance vyberete `Quantity` ze seznamu. Označte `6` a `1` ve stejném příkladu utterance.

    ![Text popisku s entitou množství](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Výuka aplikace pro použití změn entit v aplikaci

Vyberte **vlak** , pomocí kterého se aplikace naučíte těmito novými projevy. Po školení `Quantity` je subentita v entitě správně předpovězena `Order` . Tato správná předpověď je označena jako plná čára.

![Projděte si aplikaci a pak si prohlédněte příklad projevy.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

V tomto okamžiku má objednávka několik detailů, které je možné extrahovat (velikost, množství a text objednávky celkem). Existuje další úprava `Order` entity, například Pizza toppings, typ crust a vedlejší objednávky. Každý z nich by měl být vytvořen jako subentity `Order` entity.

## <a name="test-the-app-to-validate-the-changes"></a>Otestování aplikace, aby se ověřily změny

Otestujte aplikaci pomocí interaktivního **testovacího** panelu. Tento proces vám umožní zadat nový utterance a potom zobrazit výsledky předpovědi a zjistit, jak dobře aktivní a školená aplikace funguje. Předpověď záměru by měla být poměrně důvěrná (vyšší než 70%) a extrakce entit by měla vybrat alespoň `Order` entitu. Podrobnosti o entitě Order mohou chybět, protože 5 projevy není dostatečné pro zpracování každého případu.

1. V horní navigaci vyberte **Test** (Testovat).
1. Zadejte utterance `deliver a medium veggie pizza` a vyberte Enter. Aktivní model předpovídá správný záměr s více než 70% jistotou.

    ![Zadejte nový utterance k otestování záměru.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Kliknutím na **zkontrolovat** Zobrazte entitu předpovědi.

    ![Podívejte se na předpovědi entit na interaktivním panelu Test.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Velikost byla správně zjištěna. Pamatujte, že příklad projevy v `OrderPizza` záměru neobsahuje příklad `medium` jako velikost, ale použijte funkci `SizeFeature` seznamu frází, která obsahuje střední hodnotu.

    Množství není správně předpovězeno. Tuto možnost můžete opravit v klientské aplikaci ve výchozí velikosti na jednu (1), pokud LUIS předpověď nevrátí žádnou velikost.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejít na konec adresy URL v adresním řádku a nahradit _YOUR_QUERY_HERE_ stejným dotazem, jako jste zadali na interaktivním panelu testů.

    `deliver a medium veggie pizza`

    Poslední parametr QueryString je `query` , **dotaz**utterance.

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
