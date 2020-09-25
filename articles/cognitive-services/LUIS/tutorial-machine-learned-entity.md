---
title: 'Kurz: extrakce strukturovaných dat pomocí entity Machine-Learning – LUIS'
description: Extrahujte strukturovaná data z utterance pomocí entity Machine-Learning. Chcete-li zvýšit přesnost extrakce, přidejte dílčí entity s funkcemi.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: 01262ee0271849793c4393b1ea8e18c4179ad4e0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334718"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Kurz: extrakce strukturovaných dat ze utterance uživatelů pomocí entit strojového učení v Language Understanding (LUIS)

V tomto kurzu extrahujete strukturovaná data z utterance pomocí entity Machine-Learning.

Entita strojového učení podporuje [koncept dekompozice modelu](luis-concept-model.md#v3-authoring-model-decomposition) tím, že poskytuje entity subentity s [funkcemi](luis-concept-feature.md).

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Přidat entitu Machine-Learning
> * Přidat subentitu a funkci
> * Výuka, testování, publikování aplikace
> * Získat předpověď entity z koncového bodu

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Proč používat entitu strojového učení?

Tento kurz přidá entitu strojového učení pro extrakci dat z utterance uživatele.

Entita definuje data, která se mají extrahovat v rámci utterance. To zahrnuje poskytnutí názvu dat, typu (Pokud je to možné), jakéhokoli rozlišení dat v případě nejednoznačnosti a přesného textu, který tvoří data.

Aby bylo možné definovat data, je třeba:
* Vytvoření entity
* Označte text v rámci příkladu projevy, který představuje entitu. Tyto příklady s popisky LUIS, co je entita a kde se dají najít v utterance.

## <a name="entity-decomposability-is-important"></a>Devytváření entit je důležité.

Devytváření entit je důležité pro předpověď záměrů a pro extrakci dat s entitou.

Začněte s entitou strojového učení, což je začátek a entita nejvyšší úrovně pro extrakci dat. Pak rozložíte entitu na subentity.

I když si nejste jistí, jak vám entita při zahájení aplikace požadujete, doporučujeme začít s entitou strojového učení a pak je rozložit s podentitami v době, kdy je vaše aplikace vyspělá.

V tomto kurzu vytvoříte entitu strojového učení, která bude představovat objednávku pro aplikaci Pizza. Entita extrahuje text související s objednávkami, velikost vybírá a množství.

Utterance `Please deliver one large cheese pizza to me` by měla být extrahována `one large cheese pizza` jako objednávka a pak také extrahovat `1` pro množství a `large` velikost.

## <a name="download-json-file-for-app"></a>Stáhnout soubor JSON pro aplikaci

Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>Importovat soubor JSON pro aplikaci

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Vytvořit entitu počítačového učení

K extrakci podrobností o pizzaém pořadí vytvořte entitu Machine-Learning nejvyšší úrovně `Order` .

1. Na stránce **záměry** vyberte záměr **OrderPizza** .

1. V seznamu ukázkový projevy vyberte následující utterance.

    |Pořadí příkladu utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Začněte výběr těsně před textem, který je nejvíce vlevo `pickup` (#1), a pak jít hned za text, který je nejvíce vpravo, `anchovies` (#2 – tím se ukončí proces označování). Zobrazí se místní nabídka. V automaticky otevíraném okně zadejte název entity jako `Order` (#3). Pak vyberte `Order Create new entity` ze seznamu (#4).

    ![Popisek začátek a konec textu pro úplné pořadí](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Entita nebude vždy celá utterance. V tomto konkrétním případě `pickup` indikuje, jak má být objednávka přijata. Z koncepční perspektivy `pickup` by měla být součástí označené entity pro danou objednávku.

1. V poli **Zvolte typ entity** vyberte **přidat strukturu** a pak vyberte **Další**. Struktura je nutná pro přidání podentit, jako je velikost a množství.

    ![Snímek obrazovky zobrazuje okno zvolit typ entity s zaškrtnutou možností přidat strukturu.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. V poli **Přidat dílčí entity (volitelné)** vyberte **+** na `Order` řádku, pak přidat `Size` a `Quantity` jako subentity a pak vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se zvýrazněným oknem přidat podentity (volitelné) s vybranými subentitami.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Upravit podentity pro zlepšení extrakce

V předchozích krocích vytvoříte entitu a subentitu. Pro zlepšení extrakce přidejte funkce do subentit.

### <a name="improve-size-extraction-with-phrase-list"></a>Zlepšit extrakci velikosti se seznamem frází

1. V nabídce vlevo vyberte **entity** a pak vyberte **objednat** entitu.

1. Na kartě **schéma a funkce** vyberte **Velikost** subentity a pak vyberte **+ Přidat funkci**.

1. V rozevírací nabídce vyberte **vytvořit nový seznam frází** .

1. V **seznamu vytvořit novou frázi** zadejte název `SizePhraselist` a zadejte hodnoty: `small` , `medium` , a `large` . Když pole **návrhy** vyplní, vyberte `extra large` a `xl` . Vyberte **vytvořit** a vytvořte nový seznam frází.

    Tato funkce seznamu frází pomáhá `Size` podentitě najít slova týkající se velikosti tím, že jim poskytne ukázková slova. Tento seznam frází nemusí zahrnovat každé slovo velikosti, ale měl by obsahovat slova, která se očekávají pro indikaci velikosti.

### <a name="add-sizelist-entity"></a>Přidat entitu SizeList

Přidání seznamu známých velikostí, které klientská aplikace rozpozná, pomůže také extrakci.

1. V nabídce vlevo vyberte **entity** a pak vyberte **+ vytvořit**.

1. Nastavte název entity jako `SizeListentity` a nastavte typ jako **seznam** , aby bylo možné ho snadno identifikovat ve srovnání s `SizePhraselist` vytvořeným v předchozí části.

1. Přidejte velikosti, které klientská aplikace očekává: `Small` , `Medium` , `Large` a `XLarge` pak přidejte synonyma pro každou z nich. Synonyma by měla být termínem, který uživatel zadá do robota chatu. Entita se extrahuje entitou seznamu, pokud odpovídá přesně normalizované hodnotě nebo synonymům.

    |Normalizovaná hodnota|Synonyma|
    |--|--|
    |Malá|SM, SML, malý, nejmenší|
    |Střední|MD, MDM, Regular, Average, prostřední|
    |Velká|LG, LRG, velký|
    |XLarge|XL, největší, Obří|


    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se zobrazí okno SizeList a položky seznamu s vybranou položkou XLarge.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Přidat funkci entity SizeList

1. Vyberte **entity** z levé nabídky a vraťte se do seznamu entit.

1. Ze seznamu entit vyberte **pořadí** .

1. Na kartě **schéma a funkce** vyberte entitu **Velikost** a pak vyberte **+ Přidat funkci**.

1. V rozevíracím seznamu vyberte **@ SizeListentity** .

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Přidat předem sestavenou číselnou entitu

Přidání předdefinované číselné entity také pomůže extrakci.

1. V nabídce vlevo vyberte **entity** a pak vyberte **Přidat předem vytvořenou entitu**.

1. V seznamu vyberte **číslo** a potom vyberte **Hotovo**.

1. Vyberte **entity** z levé nabídky a vraťte se do seznamu entit.

### <a name="add-feature-of-prebuilt-number-entity"></a>Přidat funkci předem připravené číselné entity

1. Ze seznamu entit vyberte **pořadí** .

1. Na kartě **schéma a funkce** vyberte entitu **množství** a pak vyberte **+ Přidat funkci**.

1. V rozevíracím seznamu vyberte **@ Number** .

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Konfigurovat požadované funkce

Na stránce podrobností entity pro entitu **objednávky** vyberte hvězdičku, `*` pro funkci **@ SizeList** i pro funkci **@ Number** . Hvězdička se zobrazí ve stejném popisku jako název funkce.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky ukazuje @SizeList funkci s hvězdičkou a vyžaduje upozornění.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Příklad popisku projevy

Je vytvořená entita strojového učení a její subentity mají funkce. Aby bylo možné dokončit vylepšení extrakce, musí být příklad projevy označen subentitami.

1. V levém navigačním panelu vyberte **záměry** a pak vyberte záměr **OrderPizza** .

1. Pokud chcete otevřít **paletu entit**, vyberte **@** symbol na kontextovém panelu nástrojů.

1. Vyberte jednotlivé řádky entity v paletě a potom pomocí kurzoru palety vyberte entitu v každém příkladu utterance. Až budete hotovi, seznam entit by měl vypadat jako na následujícím obrázku.

    > [!div class="mx-imgBorder"]
    > ![Částečný snímek obrazovky s konfigurací požadované funkce](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Trénování aplikace

Pokud chcete aplikaci naučit, vyberte **vlak**. Školení aplikuje změny, jako jsou nové entity a označené projevy, na aktivní model.

## <a name="add-a-new-example-utterance"></a>Přidat nový příklad utterance

1. Po školení přidejte do záměru nový příklad utterance, `OrderPizza` abyste zjistili, jak dobře Luis rozumí entitě strojového učení.

    |Pořadí příkladu utterance|
    |--|
    |`I need a large pepperoni pizza`|

    Celková nejvyšší entita `Order` je označená a `Size` subentita je také označena tečkami.

    > [!div class="mx-imgBorder"]
    > ![Částečný snímek obrazovky s novým příkladem utterance s předpovídat entitou](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Tečkovaná čára indikuje předpověď na základě aktuálně školené aplikace.

1. Chcete-li změnit předpověď na entitu s popiskem, zaškrtněte políčko značka zaškrtnutí na stejném řádku.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky ukazuje příklad utterance se zvýrazněnou značkou zaškrtnutí.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    V tomto okamžiku entita strojového učení funguje, protože může najít entitu v rámci nového ukázkového utteranceu. Když přidáte příklad projevy, pokud entita není předpovězená správně, označte entitu a subentity. Pokud je entita předpokládaná správně, nezapomeňte předpovědi potvrdit.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Výuka aplikace pro použití změn entit v aplikaci

Vyberte **vlak** , pomocí kterého se aplikace naučí Tato nová utterance.

V tomto okamžiku má objednávka několik detailů, které je možné extrahovat (velikost, množství a text objednávky celkem). Existuje další úprava `Order` entity, například Pizza toppings, typ crust a vedlejší objednávky. Každý z nich by měl být vytvořen jako subentity `Order` entity.

## <a name="test-the-app-to-validate-the-changes"></a>Otestování aplikace, aby se ověřily změny

Otestujte aplikaci pomocí interaktivního **testovacího** panelu. Tento proces vám umožní zadat nový utterance a potom zobrazit výsledky předpovědi a zjistit, jak dobře aktivní a školená aplikace funguje. Předpověď záměru by měla být poměrně důvěrná (vyšší než 60%) a extrakce entit by měla vybrat alespoň `Order` entitu. Podrobnosti o entitě Order mohou chybět, protože tyto pár projevy nestačí pro zpracování každého případu.

1. V horní navigaci vyberte **Test** (Testovat).
1. Zadejte utterance `2 small cheese pizzas for pickup` a vyberte Enter. Aktivní model předpovídá správný záměr s více než 60% jistotou.


1. Kliknutím na **zkontrolovat** Zobrazte entitu předpovědi.

    > [!div class="mx-imgBorder"]
    > ![Částečný snímek obrazovky zobrazení entity předpovědi na interaktivním panelu testu](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

Abyste mohli přijmout předpověď služby LUIS v chatbotu nebo jiné klientské aplikaci, musíte aplikaci publikovat do koncového bodu.

1. V pravém horním navigačním panelu vyberte **publikovat** .

    ![Snímek obrazovky s tlačítkem publikovat do koncového bodu LUIS v pravé horní nabídce](./media/howto-publish/publish-button.png)

1. Vyberte **produkční** slot, vyberte **změnit nastavení**, vyberte **Analýza mínění**a potom vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s LUIS publikováním do koncového bodu](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Vyberte odkaz **přístup k adresám URL koncových bodů** v oznámení, abyste přešli na stránku **prostředků Azure** . Adresa URL koncového bodu je uvedena jako **vzorový dotaz**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejít na konec adresy URL v adresním řádku a nahradit _YOUR_QUERY_HERE_ stejným dotazem, jako jste zadali na interaktivním panelu testů.

    `2 small cheese pizzas for pickup`

    Poslední parametr QueryString je `query` , **dotaz**utterance.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
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
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
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

V tomto kurzu aplikace používá entitu strojového učení k nalezení záměru utterance uživatele a extrakci podrobností z tohoto utterance. Pomocí entity strojového učení lze rozložit podrobnosti o entitě.

> [!div class="nextstepaction"]
> [Přidání předem připravené entity klíčové fráze](luis-quickstart-intent-and-key-phrase.md)
