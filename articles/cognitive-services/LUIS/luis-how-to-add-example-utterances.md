---
title: Přidání projevů příklad v aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Informace o přidání projevů v aplikacích Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/10/2018
ms.author: diberry
ms.openlocfilehash: 6a3edfd426fcdce83bd60332ba2b1ff6224dae1a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645555"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Přidejte příklad projevy a popisek s entitami

Příklad projevy jsou příkladem text otázky uživatele nebo příkazy. Představuje Language Understanding (LUIS), budete muset přidat [příklad projevy](luis-concept-utterance.md) do [záměr](luis-concept-intent.md).

Obecně platí nejprve přidat příkladu utterance záměru a pak na stránce záměru vytváření entit a projevy popisek. Pokud byste raději entity nejprve vytvoříte, přečtěte si téma [přidat entity](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Přidat utterance
Na stránce záměru, zadejte příslušné příklad utterance očekáváte, že od uživatelů, jako například `book 2 adult business tickets to Paris tomorrow on Air France` do textového pole pod název záměru a potom stiskněte klávesu Enter. 
 
>[!NOTE]
>Služba LUIS převede všechny projevy na malá písmena.

![Stránce s podrobnostmi o snímek obrazovky záměrů, se zvýrazněným utterance](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Projevy se přidají do seznamu projevy pro aktuální záměr. 

## <a name="ignoring-words-and-punctuation"></a>Ignoruje slova a interpunkce
Pokud chcete ignorovat slova nebo interpunkčním znaménkem v příkladu utterance, použijte [vzor](luis-concept-patterns.md#pattern-syntax) s _Ignorovat_ syntaxe. 

## <a name="add-simple-entity-label"></a>Přidání popisku jednoduchou entitu
V následujícím postupu vytvoříte a vlastní entity v rámci následující utterance na stránce záměru popisek:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Vyberte "France vzduchu" utterance a označte jej jako jednoduchou entitu.

    > [!NOTE]
    > Při výběru slova, která chcete označit jako entity:
    > * Pro jednoho slova stačí vyberte ji. 
    > * Pro sadu dvou nebo více slov vyberte na začátku a na konci sady.

2. V rozevíracím seznamu pole entity, které se zobrazí můžete vybrat existující entity nebo přidat novou entitu. Chcete-li přidat novou entitu, zadejte jeho název do textového pole a pak vyberte **vytvořit novou entitu**. 
 
    ![Stránce s podrobnostmi o snímek obrazovky záměrů, s jednoduchou entitu označování zvýrazněnou možností](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. V **jaký typ entity chcete vytvořit?** vyskakovací dialogové okno, ověřte název entity a vyberte typ jednoduchého entity a pak vyberte **provádí**.

    ![Obrázek dialogového okna potvrzení](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#simple-entity-data) Další informace o extrahování jednoduchou entitu z koncového bodu odpověď na dotaz JSON. Vyzkoušejte jednoduchou entitu [rychlý Start](luis-quickstart-primary-and-secondary-data.md) Další informace o tom, jak použít jednoduché entity.


## <a name="add-list-entity-and-label"></a>Přidat entitou seznamu a popisku
Seznam entit reprezentaci sady pevné, uzavřené (přesný text odpovídá) souvisejících slov ve vašem systému. 

Seznam entity drinky den před začátkem, máte dvě normalizované hodnoty: vod a soda pop. Každý normalizovaný název má synonym. Pro vody jsou synonyma paušální H20, automobilový průmysl. Synonyma pro soda pop, jsou ovoce, coly, zázvor. Není nutné znát všechny hodnoty při vytvoření entity. Můžete přidat více až po kontrole projevy uživatelů s synonym.

|Normalizovaný název|Synonyma|
|--|--|
|Vodohospodářství|H20, plynu, paušální|
|Soda pop|Ovoce, coly, zázvor|

Při vytváření nové entity seznamu na stránce záměru, jsou to dvě věci, které nemusí být samozřejmé. Nejprve vytvoříte nový seznam tak, že přidáte první položky seznamu. Za druhé má název první položky seznamu s slovo nebo frázi, které jste vybrali utterance. Když tohle později na stránce entity můžete změnit, bude rychlejší vyberte utterance, který obsahuje slovo, které chcete pro název položky seznamu.

Například Kdybyste chtěli vytvořit seznam typů nápoje a jste vybrali slovo `h2o` z utterance vytvořit entitu, v seznamu by mít jednu položku, jejíž název je h20. Pokud byste chtěli více obecný název, měli byste zvolit utterance, používající více obecný název. 

1. V utterance, vyberte slovo, které je první položka v seznamu, a pak zadejte název seznam do textového pole a potom vyberte **vytvořit novou entitu**.   

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, s vytvořit nové entity zvýrazněnou](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. V **jaký typ entity chcete vytvořit?** dialogovém okně Přidat synonyma této položky seznamu. Horní položku v seznamu nápoje, přidejte `h20`, `perrier`, a `waters`a vyberte **provádí**. Všimněte si, že je "vod" přidat, protože seznam synonyma budou odpovídat tokenu úrovni. V anglické jazykové verzi, zda je úroveň na úrovni aplikace word tak "vod" nebude odpovídat "water" Pokud nebylo v seznamu. 

    ![Snímek obrazovky jaký typ entity můžete chtít vytvořit dialogové okno](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Tento seznam drinky den před začátkem má pouze jeden nápoje typ, vody. Můžete přidat další typy nápoje označování jiných projevy, nebo úpravou entity z **entity** v levém navigačním panelu. [Úpravy](luis-how-to-add-entities.md#add-list-entities) entity, které vám dává možnosti zadávání další položky s odpovídající synonymům nebo [import](luis-how-to-add-entities.md#import-list-entity-values) seznamu. 

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#list-entity-data) Další informace o extrahování seznam entit z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intent-and-list-entity.md) získat další informace o tom, jak používat seznam entit.

## <a name="add-synonyms-to-the-list-entity"></a>Přidání synonym do seznamu entit 
Přidáte synonyma do entitou seznamu tak, že vyberete slovo nebo frázi v utterance. Pokud máte nápoje seznam entit a chcete přidat `agua` jako synonymum pro vody, postupujte podle kroků:

V utterance, vyberte synonymní slovo, jako je například `aqua` vody, vyberte název seznamu entit v rozevíracím seznamu, například **nepijí**a pak vyberte **nastavit jako synonymum**, vyberte v seznamu Položka je synonymní s, jako například **vody**.

![Stránce s podrobnostmi o snímek obrazovky záměrů, s vytvořit nové zobrazení normalizovaná zvýrazněnou](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Vytvořit novou položku pro seznam entit
Vytvořte novou položku pro existující entity seznam tak, že vyberete slovo nebo frázi v utterance. Pokud jste zaměstnanec seznamu a chcete přidat `Bob Smith` jako nová položka, postupujte podle kroků:

V utterance, vyberte slovo nebo frázi pro novou položku seznamu, jako je například `Bob Smith`, vyberte v rozevíracím seznamu, jako název entity seznamu **zaměstnance**a pak vyberte **vytvoření nového zobrazení normalizovaná**. 

![Snímek obrazovky přidání nové položky seznamu](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Slovo nyní modře zvýrazněný. Pokud najedete myší aplikaci word, značky zobrazí název položky seznamu, jako je například čaje.

![Snímek obrazovky se nová značka položky seznamu](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Zabalení entity složené popisku
Složený entity jsou vytvořeny z **entity**. Nelze vytvořit složenou entitu ze záměru stránky. Po vytvoření složeného entity můžete zabalit entity do utterance na stránce záměru. 

Za předpokladu, že utterance, `book 2 tickets from Seattle to Cairo`, složený utterance může vrátit informace o entitě počtu lístků (2), původu (Praha) a cílové umístění (Cairo) v entitě jednu nadřazenou položku. 

Postupujte podle těchto [kroky](luis-how-to-add-entities.md#add-prebuilt-entity) přidáte **číslo** předem připravených entit. Po vytvoření entity `2` utterance je modrá, určující je označené entity. Předem připravených entit jsou označené službou LUIS. Nelze přidat nebo odebrat popisek předem připravených entit z jednoho utterance. Pouze můžete přidat nebo odebrat všechny předem připravených popisky přidáním nebo odebráním předem připravených entit z aplikace.

Postupujte podle těchto [kroky](#add-hierarchical-entity-and-label) k vytvoření **umístění** hierarchické entity. Popisek umístění zdroj a cíl v příkladu utterance. 

Před zabalením entity v složenou entitu, ujistěte se, že všechny podřízené entity jsou zvýrazněny modrou barvu, což znamená, že jste utterance popisem.

1. K jednotlivým entitám zabalit do složeného, vyberte první s popiskem entitu v utterance složený entity. V příkladu utterance `book 2 tickets from Seattle to Cairo`, první entita je číslo 2. Rozevíracím seznamu zobrazí možnosti pro tento výběr.

    ![Snímek obrazovky číslo vybrané a zvýrazněnou možností rozevíracího seznamu](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Vyberte **zabalení složený entity** z rozevíracího seznamu. 

    ![Snímek obrazovky s možností rozevíracího seznamu pro obtékání složenou entitu za použití Wrap složený entity zvýrazněnou](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Vyberte poslední slovo složený entity. V utterance v tomto příkladu vyberte "Location::Destination" (představující Cairo). Na zelenou čáru je teď v rámci všechna slova, včetně nonentity slova v utterance, které jsou složené.

    ![Záměr BookFlight snímek obrazovky stránky s číslem zvýrazněnou](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Z rozevíracího seznamu vyberte název složený entity. V tomto příkladu, který je **TicketOrder**.

    ![Snímek obrazovky obtékání slov s složenou entitu s názvem složený entity zvýrazněných v rozevíracím seznamu](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Při zabalení entity správně, je zelenou čáru pod celou frázi.

    ![Snímek obrazovky s složený entity zvýrazněnou utterance](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#composite-entity-data) Další informace o extrahování entita složený z koncového bodu odpověď na dotaz JSON. Zkuste složený entity [kurzu](luis-tutorial-composite-entity.md) Další informace o tom, jak použít složené entity.

## <a name="add-hierarchical-entity-and-label"></a>Přidat hierarchické entity a popisek
Hierarchické entity je kategorie kontextově zkušenosti a související entity. V následujícím příkladu obsahuje původní a cílové umístění. 

V utterance `Book 2 tickets from Seattle to Cairo`, Seattle, je původní umístění a Cairo je cílové umístění. Každé umístění je kontextově různých a zkušenosti z pořadí slov a požadované aplikace word v utterance.

1. Na stránce záměru v utterance, vyberte "Seattle" a pak zadejte název entity ' umístění a pak vyberte **vytvořit novou entitu**.

    ![Dialogové okno snímek obrazovky z vytvořit hierarchické Entity štítkování](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. V místním dialogovém okně vyberte hierarchické pro **typ Entity**, pak přidejte `Origin` a `Destination` jako podřízené prvky a pak vyberte **provádí**.

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, s entitou ToLocation zvýrazněnou](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Aplikace word v utterance byla označena hierarchické nadřazená entita. Musíte přiřadit slovo podřízené entity. Na stránce záměru vrátit utterance. Vybrat slovo, pak v rozevíracím seznamu vyberte název entity, které jste vytvořili a postupujte podle nabídce napravo a zvolte správné podřízené entity.

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, s entitou ToLocation zvýrazněnou](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Podřízené entity názvy musí být jedinečný ve všech entit v jediné aplikaci. Dva různé hierarchické entity nemůže obsahovat podřízené entity se stejným názvem. 

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#hierarchical-entity-data) Další informace o extrahování hierarchické entity z koncového bodu odpověď na dotaz JSON. Zkuste hierarchické entity [rychlý Start](luis-quickstart-intent-and-hier-entity.md) získat další informace o tom, jak používat hierarchické entity.

## <a name="entity-status-predictions"></a>Predikce stavu entity

Při zadání nového utterance na portálu služby LUIS, pravděpodobně utterance entity předpovědi chyb. Chyba předpovědi je rozdíl mezi jak entita s názvem ve srovnání s jak LUIS upozornilo na entitu. 

Tento rozdíl je vizuálně reprezentována na portálu služby LUIS s červenou vlnovkou v utterance. Červené podtržení může objevit v závorkách pro entitu nebo mimo hranaté závorky. 

![Snímek obrazovky Entity stav predikcí nesrovnalosti](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Vyberte slova, která jsou podtrženy červeně utterance. 

Zobrazí pole entity **stav Entity** se červený vykřičník Pokud dojde k neshodě předpovědi. Chcete-li zobrazit stav Entity s informacemi o rozdílech mezi s popiskem a prediktivní entity, vyberte **stav Entity** vyberte položku na pravé straně.

![Snímek obrazovky Entity stav predikcí nesrovnalosti](./media/luis-how-to-add-example-utterances/entity-status.png)

Může se zobrazit řádku červeně na některý z následujících časech:

    * Při zadání utterance, ale před je označené jako entity
    * Když entitu popisek
    * Při odebrání popisku entity
    * Pokud více než jeden popisek entity očekává se, že pro tento text 

Následující řešení vyřešit nesrovnalosti předpovědi entity:

|Entita|Vizuální označení|Předpověď|Řešení|
|--|--|--|--|
|Utterance zadali, nemá ještě Popisek entity.|červené podtržení|Predikce je správná.|Popisek entita s předpovězené hodnoty.|
|Text bez popisku|červené podtržení|Nesprávný predikcí|Aktuální projevy, které používají tuto entitu nesprávné třeba, aby ve všech záměry. Aktuální projevy mají mistaught LUIS, tento text je prediktivní entity.
|Správně označené text|entity modré zvýraznění, červené podtržení|Nesprávný predikcí|Zadejte další projevy s entitou správně označené v různých místech a použití. Aktuální projevy jsou je dostatečná představuje LUIS, že jde o entitu nebo podobné entity se zobrazí ve stejném kontextu. Podobně jako entity by měl sloučené do jedné entity, LUIS se zaměňovat. Jiným řešením je přidat seznam frázi a zvýšit tak význam slova. |
|Nesprávně označené text|entity modré zvýraznění, červené podtržení|Správné predikcí| Zadejte další projevy s entitou správně označené v různých místech a použití. 


## <a name="remove-entity-labels-from-utterances"></a>Odebrání popisků entity z projevy
Zjištěné počítače entity popisky můžete odebrat z utterance na stránce záměru. Pokud subjektem není zjištěné počítače, nelze odebrat ze utterance. Pokud potřebujete k odebrání utterance na jiné počítače zjistili entitu, musíte odstranit entitu z celé aplikace. 

Odebrání popisku se naučili počítač entity ze utterance, vyberte entitu utterance. Potom vyberte **odebrat popisek** v rozevíracím seznamu pole entity, které se zobrazí.

![Stránce s podrobnostmi o snímek obrazovky záměrů, s popiskem odebrat zvýrazněnou](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Přidání popisku předem připravených entit
Pokud přidáte do aplikace LUIS předem připravených entit, není nutné popisek projevy s těmito entitami. Další informace o tom předem připravených entit a jak je přidat, naleznete v tématu [přidat entity](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Přidání popisku entity regulárního výrazu
Pokud regulární výraz entity, které přidáte do aplikace LUIS, není nutné popisek projevy s těmito entitami. Další informace o regulární výraz entity a jak je přidat, naleznete v tématu [přidat entity](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Ze utterance společně tvoří masku
Zobrazit [přidat vzorek z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Přidání popisku pattern.any entity
Pokud přidáte pattern.any entity do aplikace LUIS, nelze označit projevy s těmito entitami. Jsou platné ve vzorech. Další informace o pattern.any entity a jak je přidat, naleznete v tématu [přidat entity](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Po změně modelu s projevy trénování vaší aplikace
Po přidání, úprava nebo odebrání projevy, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

Po označování projevy v vaše záměry, teď můžete vytvářet [složený entity](luis-how-to-add-entities.md).
