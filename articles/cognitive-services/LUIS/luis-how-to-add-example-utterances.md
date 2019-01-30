---
title: Přidat ukázkové promluvy
titleSuffix: Language Understanding - Azure Cognitive Services
description: Příklad projevy jsou příkladem text otázky uživatele nebo příkazy. Představuje Language Understanding (LUIS), že je potřeba přidat příklad projevy záměru.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 2da704f4b0d7ed0d7038b5ef9b183d9b60f5e639
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223150"
---
# <a name="add-an-entity-to-example-utterances"></a>Přidání entity do příklad projevy 

Příklad projevy jsou příkladem text otázky uživatele nebo příkazy. Představuje Language Understanding (LUIS), budete muset přidat [příklad projevy](luis-concept-utterance.md) do [záměr](luis-concept-intent.md).

Obvykle nejprve přidat příkladu utterance záměru a pak na stránce záměru vytváření entit a projevy popisek. Pokud byste raději entity nejprve vytvoříte, přečtěte si téma [přidat entity](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Označení entity v příkladu projevy

Při výběru textu v příkladu utterance označit pro entitu, se zobrazí místní nabídky v místě. Pomocí této nabídky k vytvoření nebo výběr entity. 

Některé typy entit, jako je například předem připravených entit a entit regulární výraz nelze v příkladu utterance bez příznaku, protože jsou automaticky označené. 

## <a name="add-a-simple-entity"></a>Přidat jednoduchou entitu

V následujícím postupu vytvoříte a označit vlastní entity v rámci následující utterance na stránce záměru:

```text
Does John Smith work in Seattle?
```

1. Vyberte `Seattle` v utterance a označte jej jako jednoduchou entitu.

    [![Snímek obrazovky s výběrem textu v utterance pro jednoduchou entitu](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > Při výběru slova ke značce jako entity:
    > * Pro jednoho slova stačí vyberte ji. 
    > * Pro sadu dvou nebo více slov vyberte na začátku a na konci sady.

1. V rozevíracím seznamu pole entity, které se zobrazí můžete vybrat existující entity nebo přidat novou entitu. Chcete-li přidat novou entitu, zadejte jeho název do textového pole a pak vyberte **vytvořit novou entitu**. 

    ![Snímek obrazovky zadání názvu entity](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. V **jaký typ entity chcete vytvořit?** v místním okně, ověřte název entity a vyberte **jednoduché** typu entity a pak vyberte **provádí**.

    A [seznam frází](luis-concept-feature.md) se běžně používá pro zvýšení signál jednoduchou entitu.

## <a name="add-a-list-entity"></a>Přidat seznam entit

Seznam entit reprezentaci sady pevné, uzavřené (přesný text odpovídá) souvisejících slov ve vašem systému. 

Pro seznam oddělení vaší společnosti, můžete mít normalizované hodnoty: `Accounting` a `Human Resources`. Každý normalizovaný název má synonym. Pro oddělení můžete tato synonyma zahrnout všechny oddělení zkratky, čísla nebo slang. Není nutné znát všechny hodnoty při vytvoření entity. Můžete přidat více až po kontrole projevy uživatelů s synonym.

1. V seznamu utterance příklad pro konkrétní utterance vyberte slovo nebo slovní spojení, které chcete do nového seznamu. Potom zadejte název seznamu v horní textového pole a pak vyberte **vytvořit novou entitu**.   

    ![Snímek obrazovky zadání názvu entity seznamu](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. V **jaký typ entity chcete vytvořit?** v místním okně, název entity a vyberte **seznamu** jako typ. Přidání synonym této položky seznamu a pak vyberte **provádí**. 

    ![Snímek obrazovky zadání synonyma seznam entit](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Označování jiných projevy, nebo úpravou entity z můžete přidat další položky seznamu nebo další položku synonyma **entity** v levém navigačním panelu. [Úpravy](luis-how-to-add-entities.md#add-list-entities) entity, které vám dává možnosti zadávání další položky s odpovídající synonymům nebo import seznamu. 

## <a name="add-composite-entity"></a>Přidání složené entity

Složený entity jsou vytvořeny z existujících **entity** do nadřazená entita. 

Za předpokladu, že utterance, `Does John Smith work in Seattle?`, složený utterance může vrátit informace o entitě zaměstnance název a umístění v objektu jednu nadřazenou položku. 

Jméno zaměstnance, Jan Macek, je využitím předem připravených [personName](luis-reference-prebuilt-person.md) entity. Umístění, Seattle, je jednoduchý vlastní entity. Jakmile se tyto dvě entity jsou vytvořeny a označené v utterance příklad, tyto entity mohou být zabaleny do složeného entity. 

1. Chcete-li zabalit do složeného jednotlivými entitami, vyberte **první** označené jako entity (nejvíce vlevo) v utterance složený entity. Rozevíracím seznamu zobrazí možnosti pro tento výběr.

1. Vyberte **zabalení složený entity** z rozevíracího seznamu. 

    ![Snímek obrazovky vyberte "Zalamování řádků v entitě složené"](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. Vyberte poslední slovo složený entity (nejvíce vpravo). Všimněte si, že zelená čára následuje složený entity.

1. Zadejte název složený entity v rozevíracím seznamu.

    ![Snímek obrazovky zadejte název složený entity v rozevíracím seznamu](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    Při zabalení entity správně, je zelenou čáru pod celou frázi.

1. Ověření složeného entity podrobnosti na **jaký typ entity chcete vytvořit?** automaticky otevírané okno pole vyberte **provádí**.

    ![Snímek obrazovky Entity podrobnosti místní](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Složený entity, zobrazí se i modré zvýraznění pro jednotlivé entity a zelená podtržení celý složené entity. 

    ![Stránce s podrobnostmi o snímek obrazovky s příkazy, pomocí složeného entity](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Přidání hierarchické entity

Hierarchické entity je kategorie kontextově zkušenosti a související entity. V následujícím příkladu obsahuje původní a cílové umístění. 

V utterance `Move John Smith from Seattle to Cairo`, Seattle, je původní umístění a Cairo je cílové umístění. Každé umístění je kontextově různých a zkušenosti z pořadí slov a požadované aplikace word v utterance.

1. Na stránce záměru v utterance, vyberte `Seattle`, zadejte název entity `Location`a pak stiskněte Enter na klávesnici.

    ![Dialogové okno snímek obrazovky z vytvořit hierarchické Entity štítkování](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. V **jaký typ entity chcete vytvořit?** v místním okně, vyberte _hierarchické_ pro **typ Entity**, pak přidejte `Origin` a `Destination` jako podřízené položky, a pak vyberte **provádí**.

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, s entitou ToLocation zvýrazněnou](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. Aplikace word v utterance byla označena hierarchické nadřazená entita. Musíte přiřadit slovo podřízené entity. Na stránce s podrobnostmi záměru vrátit utterance. Vybrat slovo, pak v rozevíracím seznamu vyberte název entity, které jste vytvořili a postupujte podle nabídce napravo a zvolte správné podřízené entity.

    ![Stránce s podrobnostmi o snímek obrazovky s příkazy, které je potřeba přiřadit slovo podřízené entity](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >Podřízené entity názvy musí být jedinečný ve všech entit v jediné aplikaci. Dva různé hierarchické entity nemůže obsahovat podřízené entity se stejným názvem. 

## <a name="entity-status-predictions"></a>Predikce stavu entity

Při zadání nového utterance na portálu služby LUIS, pravděpodobně utterance entity předpovědi chyb. Chyba předpovědi je rozdíl mezi jak entita s názvem ve srovnání s jak LUIS upozornilo na entitu. 

Tento rozdíl je vizuálně reprezentována na portálu služby LUIS s červenou vlnovkou v utterance. Červené podtržení může objevit v závorkách pro entitu nebo mimo hranaté závorky. 

![Snímek obrazovky Entity stav predikcí nesrovnalosti](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Vyberte slova, která jsou podtrženy červeně utterance. 

Zobrazí pole entity **stav Entity** se červený vykřičník Pokud dojde k neshodě předpovědi. Chcete-li zobrazit stav Entity s informacemi o rozdílech mezi s popiskem a prediktivní entity, vyberte **stav Entity** vyberte položku na pravé straně.

![Snímek obrazovky s výběrem správné položky k opravě předpovědi nesrovnalosti](./media/luis-how-to-add-example-utterances/entity-status.png)

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

## <a name="other-actions"></a>Další akce

Můžete provádět akce na příkladu projevy jako vybranou skupinu nebo jednotlivé položky. Skupiny vybrané příklad projevy změnit v kontextové nabídce nad seznamem. Jednotlivé položky může použít místní nabídku výše v seznamu a jednotlivé kontextové tři tečky na konci každého řádku utterance. 

### <a name="remove-entity-labels-from-utterances"></a>Odebrání popisků entity z projevy

Zjištěné počítače entity popisky můžete odebrat z utterance na stránce záměru. Pokud subjektem není zjištěné počítače, nelze odebrat ze utterance. Pokud potřebujete k odebrání utterance na jiné počítače zjistili entitu, musíte odstranit entitu z celé aplikace. 

Odebrání popisku se naučili počítač entity ze utterance, vyberte entitu utterance. Potom vyberte **odebrat popisek** v rozevíracím seznamu pole entity, které se zobrazí.

![Stránce s podrobnostmi o snímek obrazovky záměrů, s popiskem odebrat zvýrazněnou](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>Přidání popisku předem připravených entit

Když přidáte do aplikace LUIS předem připravených entit, není nutné projevy značky s těmito entitami. Další informace o tom předem připravených entit a jak je přidat, naleznete v tématu [přidat entity](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Přidání popisku entity regulárního výrazu

Pokud regulární výraz entity, které přidáte do aplikace LUIS, není nutné projevy značky s těmito entitami. Další informace o regulární výraz entity a jak je přidat, naleznete v tématu [přidat entity](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Ze utterance společně tvoří masku

Zobrazit [přidat vzorek z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Přidání pattern.any entity

Pokud přidáte pattern.any entity do aplikace LUIS, nelze označit projevy s těmito entitami. Jsou platné ve vzorech. Další informace o pattern.any entity a jak je přidat, naleznete v tématu [přidat entity](luis-how-to-add-entities.md#add-patternany-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Po změně modelu s projevy trénování vaší aplikace

Po přidání, úprava nebo odebrání projevy, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

Po označování projevy v vaše záměry, teď můžete vytvářet [složený entity](luis-how-to-add-entities.md).
