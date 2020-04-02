---
title: 'Kurz: Extrahujte strukturovaná data pomocí entity naučené strojem – LUIS'
description: Extrahujte strukturovaná data z utterance pomocí entity učení počítače. Chcete-li zvýšit přesnost extrakce, přidejte dílčí součásti s popisovači a vazbami.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 52bf2fb0b9f37e0c731a46c0aaf8b6c5e7f0e911
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545855"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Kurz: Extrahujte strukturovaná data z promluvy uživatele pomocí strojově získaných entit v jazyce Language Understanding (LUIS)

V tomto kurzu extrahovat strukturovaná data z utterance pomocí entity naučil počítače.

Počítačově naváděná entita podporuje [koncept rozkladu modelu](luis-concept-model.md#v3-authoring-model-decomposition) tím, že poskytuje entity dílčích komponent s jejich deskriptory a omezeními.

**V tomto kurzu se dozvíte, jak:**

> [!div class="checklist"]
> * Importovat ukázkové aplikace
> * Přidat entitu naučenou strojem
> * Přidat dílčí součást
> * Přidání popisovače dílčí součásti
> * Přidat omezení dílčí součásti
> * Trénovat aplikaci
> * Testovací aplikace
> * Publikování aplikace
> * Získat předpověď entity z koncového bodu

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Proč používat strojově naučenou entitu?

Tento kurz přidá počítačově naučenou entitu pro extrahování dat z utterance.

Entita definuje data extrahovat z v rámci utterance. To zahrnuje poskytnutí názvu dat, typu (pokud je to možné), libovolného rozlišení dat, pokud existuje nejednoznačnost, a přesného textu, který data tvoří.

Chcete-li definovat entitu, musíte vytvořit entitu a poté označit text představující entitu v příkladových projevech v rámci všech záměrů. Tyto příklady označené luis, co je entita a kde lze nalézt v utterance.

## <a name="entity-decomposability-is-important"></a>Rozložitelnost entity je důležitá

Rozložitelnost entity je důležitá jak pro předpověď záměru, tak pro extrakci dat s entitou.

Začněte s entitou naučenou počítačem, což je počáteční a nejvyšší úroveň pro extrakci dat. Potom rozložte entitu na součásti potřebné klientskou aplikací.

I když možná nevíte, jak podrobné chcete, aby vaše entita při spuštění aplikace, osvědčeným postupem je začít s počítačem naučil entity, pak rozložit s dílčími součástmi, jak vaše aplikace zraje.

V tomto vytvořit strojově naučený entity představují objednávku pro aplikaci pizza. Objednávka by měla mít všechny části, které jsou nezbytné k fullfil pořadí. Chcete-li začít, entita extrahuje text související s objednávkou, vysune velikost a množství.

Utterance for `Please deliver one large cheese pizza to me` by `one large cheese pizza` měl extrahovat `1` jako `large`pořadí, pak také extrahovat a .

Existuje další rozklad, který můžete přidat, například vytváření dílčích složek pro zálivky nebo kůru. Po tomto kurzu byste měli mít jistotu, `Order` že přidáte tyto dílčí součásti do existující entity.

## <a name="import-example-json-to-begin-app"></a>Import příkladu json pro spuštění aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Popisek textu jako entit v ukázkových projevech

Chcete-li extrahovat podrobnosti o objednávce `Order` pizzy, vytvořte entitu nejvyšší úrovně, která se naučila stroj.

1. Na stránce **Záměry** vyberte záměr **OrderPizza.**

1. V seznamu ukázkových projevů vyberte následující utterance.

    |Promluva příkladu objednávky|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Začněte vybírat těsně před textem `pickup` (#1), který je nejvíce vlevo, `anchovies` a pak přejděte těsně za text zcela vpravo (#2 - tím proces označování končí). Zobrazí se rozbalovací nabídka. Do automaticky otevíraného pole zadejte `Order` název entity jako (#3). Pak `Order - Create new entity` vyberte ze seznamu (#4).

    ![Popisek začátku a konce textu pro úplnou objednávku](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Entita nebude vždy celý utterance. V tomto konkrétním `pickup` případě označuje, jak má být objednávka přijata. Z koncepčního `pickup` hlediska by měla být součástí označené entity pro objednávku.

1. V poli **Zvolit typ entity** vyberte Přidat **strukturu** a pak vyberte **Další**. Struktura je nutné přidat dílčí součásti, jako je velikost a množství.

    ![Přidat strukturu do entity](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. V poli **Vytvořit počítač naučil entity,** v `Size` poli **Struktura,** přidejte pak vyberte Enter.
1. Chcete-li přidat **popisovač** `+` , vyberte v oblasti **Popisovače** a pak vyberte **Vytvořit nový seznam frází**.

1. Do pole **Vytvořit nový seznam frází** zadejte název `SizeDescriptor` a `small` `medium`zadejte `large`hodnoty: , a . Když se pole **Návrhy** `extra large`vyplní, vyberte a . `xl` Chcete-li vytvořit nový seznam frází, vyberte **Hotovo.**

    Tento popisovač seznamu frází pomáhá podkomponentě `Size` najít slova související s velikostí tím, že jí poskytuje ukázková slova. Tento seznam nemusí obsahovat slovo o každé velikosti, ale měl by obsahovat slova, u kterých se očekává, že označují velikost.

    ![Vytvoření popisovače pro dílčí komponentu velikosti](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. V okně **Vytvořit počítač naučil entity,** vyberte `Size` **Vytvořit** pro dokončení vytváření dílčí součásti.

    Entita `Order` `Size` s komponentou je `Order` vytvořena, ale pouze entita byla použita pro utterance. Text `Size` entity je třeba označit v příkladu utterance.

1. Ve stejném příkladu utterance **Size** označte `large` dílčí komponentu Velikost výběrem slova a výběrem entity **Velikost** z rozevíracího seznamu.

    ![Označte entitu velikosti pro text v utterance.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Řádek je pod textem plný, protože popisky i předpověď odpovídají, protože jste _text explicitně_ označili.

1. Označte entitu `Order` ve zbývajících projevech spolu s entitou velikosti. Hranaté závorky v `Order` textu označují `Size` označenou entitu a entitu uvnitř.

    |Příklad promluv y pořadí|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Vytvořte entitu a dílčí součásti ve všech zbývajících ukázkových projevech.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Jak zacházíte s implikovanými údaji, jako je dopis, `a` který naznačuje jednu pizzu? Nebo nedostatek `pickup` a `delivery` uvést, kde se očekává pizza? Nebo nedostatek velikosti, která označuje výchozí velikost malé nebo velké? Zvažte zpracování implicitní zpracování dat jako součást obchodních pravidel v klientské aplikaci namísto nebo vedle LUIS.

1. Chcete-li aplikaci trénovat, vyberte **možnost Vlak**. Školení platí změny, jako jsou nové entity a popisované projevy, aktivní model.

1. Po školení přidejte nový příklad utterance k záměru zjistit, jak dobře LUIS chápe entity naučil stroj.

    |Promluva příkladu objednávky|
    |--|
    |`pickup XL meat lovers pizza`|

    Celková horní `Order` entita je `Size` označena a dílčí součást je také označena tečkovanými čarami.

    ![Nový příklad utterance předpověděl s entitou](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Tečkovaná čára označuje předpověď.

1. Chcete-li změnit předpověď na entitu s popiskem, vyberte řádek a pak vyberte **Potvrdit předpovědi entit**.

    ![Přijmout předpověď výběrem Potvrdit předpověď entity.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    V tomto okamžiku počítač naučil entity funguje, protože můžete najít entitu v rámci nového příkladu utterance. Při přidávání ukázkových promluv, pokud entita není předpovězena správně, označte entitu a dílčí součásti popiskem. Pokud je entita předpovězena správně, ujistěte se, že potvrzení předpovědi.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Přidání předem sestavené číslo pomoci extrahovat data

Informace o objednávce by měly také obsahovat počet položek v pořadí, například kolik pizz. Chcete-li extrahovat tato data, je třeba `Order` přidat novou počítačovou podsoučást a tato součást potřebuje omezení předem sestaveného čísla. Omezením entity na předem sestavené číslo entita vyhledá a extrahuje `2`čísla bez `two`ohledu na to, zda se jedná o číslici nebo text .

Začněte přidáním předem sestavené číselné entity do aplikace.

1. V levé nabídce vyberte **Entity** a pak vyberte **+ Přidat předem vytvořenou entitu**.

1. V poli **Přidat předem sestavené entity** vyhledejte a vyberte **číslo** a pak vyberte **Hotovo**.

    ![Přidat předem vytvořenou entitu](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Předem sestavená entita se přidá do aplikace, ale ještě není omezením.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Vytvoření entity podsoučásti s vazbou, která pomáhá extrahovat data

Entita `Order` by `Quantity` měla mít dílčí součást k určení, kolik položky je v pořadí. Množství by mělo být omezeno na číslo, aby extrahovaná data byla okamžitě dostupná klientské aplikaci podle názvu.

Omezení se použije jako shoda textu, buď s přesnou shodou (například entitou seznamu), nebo prostřednictvím regulárních výrazů (například entity regulárních výrazů nebo předem sestavené entity).

Pomocí omezení je extrahován pouze text, který odpovídá tomuto omezení.

1. Vyberte **entity** a `Order` vyberte entitu.
1. Vyberte **+ Přidat komponentu** a pak zadejte název `Quantity` `Order` a pak vyberte Enter a přidejte novou dílčí součást do entity.
1. Po oznámení o úspěchu vyberte v rozšířené **možnosti**tužku omezení.
1. V rozevíracím seznamu vyberte předem sestavené číslo.

    ![Vytvořte entitu množství s předem sestaveným číslem jako omezením.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Entita se `Quantity` použije, když text odpovídá předem sestavené číselné entitě.

    Entita s omezením je vytvořena, ale ještě není použita pro ukázkové projevy.

    > [!NOTE]
    > Dílčí součást může být vnořena do podsoučásti až do 5 úrovní. I když to není zobrazeno v tomto článku, je k dispozici z portálu a rozhraní API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Popisek příklad utterance učit LUIS o entitě

1. Vyberte **záměry** z levé navigace a vyberte záměr **OrderPizza.** Tři čísla v následujících projevech jsou označeny, `Order` ale jsou vizuálně pod čárou entity. Tato nižší úroveň znamená, že entity jsou nalezeny, ale nejsou považovány za součást `Order` účetní jednotky.

    ![Předem sestavené číslo je nalezeno, ale dosud není považováno za jiné než entitu Objednávka.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Označte čísla `Quantity` entitou `2` výběrem promluvy v `Quantity` příkladu a výběrem ze seznamu. Označte `6` `1` a ve stejném příkladu utterance.

    ![Popisek textu entitou množství](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Trénování aplikace k použití změn entity v aplikaci

Chcete-li trénovat aplikaci pomocí těchto nových promluv, vyberte **možnost Trénovat** aplikaci. Po trénování je `Quantity` dílčí součást `Order` správně předpovězena v součásti. Tato správná předpověď je indikována plnou čárou.

![Trénování aplikace pak zkontrolujte příklad projevy.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

V tomto okamžiku má objednávka některé podrobnosti, které lze extrahovat (velikost, množství a celkový text objednávky). Tam je další rafinace subjektu, `Order` jako je pizza zálivky, typ kůry, a vedlejší objednávky. Každá z nich by měla být `Order` vytvořena jako dílčí součásti entity.

## <a name="test-the-app-to-validate-the-changes"></a>Testování aplikace k ověření změn

Otestujte aplikaci pomocí interaktivního **testovacího** panelu. Tento proces umožňuje zadat nový utterance pak zobrazit výsledky předpovědi zobrazíte, jak dobře aktivní a trénované aplikace funguje. Predikce záměru by měla být poměrně sebevědomá (nad 70%) a těžba subjektu by `Order` měla vyzvednout alespoň účetní jednotku. Podrobnosti entity objednávky může chybět, protože 5 projevy nestačí ke zpracování každého případu.

1. V horní navigaci vyberte **Test** (Testovat).
1. Zadejte utterance `deliver a medium veggie pizza` a vyberte Enter. Aktivní model předpověděl správný záměr s více než 70% spolehlivostí.

    ![Zadejte nový utterance otestovat záměr.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Chcete-li zobrazit předpovědi entit, vyberte **možnost Zkontrolovat.**

    ![Zobrazení předpovědí entit v interaktivním testovacím panelu.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Velikost byla správně identifikována. Nezapomeňte, že příklad projevy `OrderPizza` v záměru nemají `medium` příklad jako velikost, ale použít popisovač seznamu `SizeDescriptor` frází, který obsahuje médium.

    Množství není správně předpovězeno. Můžete opravit v klientské aplikaci výchozí velikost na jeden (1), pokud žádná velikost je vrácena v předpověď LUIS.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Získání záměru a predikce entity z koncového bodu HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adresním řádku a nahraďte _YOUR_QUERY_HERE_ stejným dotazem, jako jste zadali do interaktivního testovacího panelu.

    `deliver a medium veggie pizza`

    Poslední parametr querystring `query`je , **utterance dotazu**.

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

* [Výuka - záměry](luis-quickstart-intents-only.md)
* [Koncept - koncepční](luis-concept-entity-types.md) informace subjektů
* [Koncept - obsahuje](luis-concept-feature.md) koncepční informace
* [Jak trénovat](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Jak testovat na portálu LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu aplikace používá počítačově naučené entity najít záměr promluvy uživatele a extrahovat podrobnosti z tohoto utterance. Pomocí entity naučené strojem můžete rozložit podrobnosti entity.

> [!div class="nextstepaction"]
> [Přidání předem připravené entity klíčové fráze](luis-quickstart-intent-and-key-phrase.md)
