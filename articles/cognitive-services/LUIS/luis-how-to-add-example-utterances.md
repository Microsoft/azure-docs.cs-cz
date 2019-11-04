---
title: Přidat příklad projevy-LUIS
titleSuffix: Azure Cognitive Services
description: Příkladem projevy jsou textové příklady dotazů a příkazů uživatele. Chcete-li naučit Language Understanding (LUIS), musíte do záměru přidat příklad projevy.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: ed6321963422cf17c858f43593092f8ffa4a1119
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467741"
---
# <a name="add-an-entity-to-example-utterances"></a>Přidání entity do ukázkového projevy 

Příkladem projevy jsou textové příklady dotazů a příkazů uživatele. Chcete-li naučit Language Understanding (LUIS), musíte do [záměru](luis-concept-intent.md)přidat [příklad projevy](luis-concept-utterance.md) .


Obvykle nejprve do záměru přidáte příklad utterance a potom na stránce **záměry** vytvoříte entity a popisek projevy. Pokud místo toho chcete vytvořit entity jako první, přečtěte si téma [Přidání entit](luis-how-to-add-entities.md).

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="marking-entities-in-example-utterances"></a>Označení entit v příkladu projevy

Když vyberete text v příkladu utterance, který chcete označit pro entitu, zobrazí se místní nabídka místní nabídky. Pomocí této nabídky můžete vytvořit nebo vybrat entitu. 

Některé typy entit, například předem připravené entity a entity regulárních výrazů, nemohou být označeny v příkladu utterance, protože jsou označeny automaticky. 

## <a name="add-a-simple-entity"></a>Přidat jednoduchou entitu

V následujícím postupu vytvoříte a označíte vlastní entitu v rámci následujících utterance na stránce **záměry** :

```text
Are there any SQL server jobs?
```

1. Vyberte `SQL server` v utterance, abyste ho mohli označit jako jednoduchou entitu. V rozevíracím seznamu entita, která se zobrazí, můžete buď vybrat existující entitu, nebo přidat novou entitu. Pokud chcete přidat novou entitu, zadejte její název `Job` do textového pole a pak vyberte **vytvořit novou entitu**.

    ![Snímek obrazovky se zadáním názvu entity](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Když vybíráte slova, která chcete označit jako entity:
    > * V případě jednoho slova ho stačí vybrat. 
    > * V případě sady dvou nebo více slov vyberte první slovo a potom konečné slovo.

1. V rozevíracím seznamu **jaký typ entity chcete vytvořit?** ověřte název entity, vyberte typ **jednoduché** entity a pak vyberte možnost **Hotovo**.

    [Seznam frází](luis-concept-feature.md) se běžně používá ke zvýšení signálu jednoduché entity.

## <a name="add-a-list-entity"></a>Přidat entitu seznamu

Seznam entit představuje sadu přesně vyhovujících textů souvisejících slov v systému. 

Pro seznam oddělení společnosti můžete mít normalizované hodnoty: `Accounting` a `Human Resources`. Každý normalizovaný název má synonyma. V případě oddělení můžou tato synonyma obsahovat jakékoli akronymy oddělení, čísla nebo slangem. Při vytváření entity nemusíte znát všechny hodnoty. Po kontrole reálných uživatelských projevy pomocí synonym můžete přidat další.

1. V příkladu utterance na stránce **záměry** vyberte slovo nebo frázi, které chcete v seznamu nový. Když se zobrazí rozevírací seznam entita, do horního textového pole zadejte název nové entity seznamu a pak vyberte **vytvořit novou entitu**.   

1. V rozevíracím seznamu **jaký typ entity chcete vytvořit?** zadejte název entity a jako typ vyberte **seznam** . Přidejte synonyma této položky seznamu a potom vyberte **Hotovo**. 

    ![Snímek obrazovky se zadáním synonym entit seznamu](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Můžete přidat více položek seznamu nebo více synonym položek pomocí označení jiných projevy nebo úpravou entity z **entit** v levém navigačním panelu. [Úpravy](luis-how-to-add-entities.md#add-list-entities) entit poskytují možnosti zadávání dalších položek s odpovídajícími synonymy nebo importování seznamu. 

## <a name="add-a-composite-entity"></a>Přidání složené entity

Složené entity jsou vytvořeny z existujících **entit** pro vytvoření nadřazené entity. 

Za předpokladu, že utterance `Does John Smith work in Seattle?`, může složený utterance vracet informace o entitě názvu zaměstnance `John Smith`a `Seattle` umístění v složené entitě. Podřízené entity už musí existovat v aplikaci a musí být označené v příkladu utterance před vytvořením složené entity.

1. Pokud chcete podřízené entity zabalit do složené entity, vyberte v utterance pro složenou entitu **první** označenou entitou (vlevo-nejvíce). Zobrazí se rozevírací seznam pro zobrazení možností pro tento výběr.

1. V rozevíracím seznamu vyberte možnost **zalamovat v složené entitě** . 

1. Vyberte poslední slovo složené entity (nejvyšší oprávnění). Všimněte si, že po složené entitě následuje zelená čára. Toto je vizuální indikátor pro složenou entitu a měl by být v rámci všech slov v složené entitě z podřízené entity nejvíce vlevo až po podřízenou entitu nejvíce vpravo.

1. V rozevíracím seznamu zadejte název složené entity.

    Při správném zalamování entit je zelená čára v rámci celé fráze.

1. Ověřte podrobnosti složených entit v rozevíracím seznamu **typ entity, který chcete vytvořit?** potom vyberte **Hotovo**.

    ![Snímek obrazovky s rozbalovacími informacemi o entitě](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Složená entita se zobrazí s modrými zvýrazněními pro jednotlivé entity a zeleným podtržením pro celou složenou entitu. 

    ![Stránka s podrobnostmi o záměrech na snímku se složenou entitou](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Přidat roli entity do utterance

Role je pojmenovaný podtyp entity určený kontextem utterance. Entitu můžete označit v rámci utterance jako entitu nebo vybrat roli v této entitě. Každá entita může mít role, včetně vlastních entit, které jsou učené strojem (jednoduché entity a složené entity), se nedozvěděly na stroji (předem připravené entity, entity regulárních výrazů, seznam entit). 

Naučte [se, jak označit utterance pomocí rolí entit](tutorial-entity-roles.md) z praktického kurzu. 

## <a name="entity-status-predictions"></a>Předpovědi stavu entity

Když na portálu LUIS zadáte novou utterance, může utterance obsahovat chyby předpovědi entit. Chyba předpovědi je rozdíl mezi tím, jak je entita označena v porovnání s tím, jak LUIS předpověď entit. 

Tento rozdíl je vizuálně reprezentován na portálu LUIS s červeným podtržením v utterance. Červené podtržení se může objevit v závorkách entit nebo mimo hranaté závorky. 

![Snímek obrazovky s rozdílem v předpovědi stavu entity](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Vyberte slova podtržená červeně v utterance. 

V poli entita se zobrazí **stav entity** s červeným vykřičníkem, pokud dojde k nesouladu předpovědi. Chcete-li zobrazit stav entity s informacemi o rozdílu mezi popisky a předpovězenými entitami, vyberte možnost **stav entity** a pak vyberte položku vpravo.

![Snímek obrazovky výběru stavu entity](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

Červená čára může vypadat v následujících časech:

   * Při zadání utterance, ale před označením entity
   * Při použití popisku entity
   * Při odebrání popisku entity
   * Je-li pro tento text předpokládaný více než jeden popisek entity 

Následující řešení vám pomůžou vyřešit nesoulad mezi předpověďmi entit:

|Entita|Vizuální indikátor|Předpovědi|Řešení|
|--|--|--|--|
|Utterance zadáno, entita se ještě neoznačí.|červené podtržení|Předpověď je správná.|Označte entitu entitou s předpokládanou hodnotou.|
|Neoznačený text|červené podtržení|Nesprávná předpověď|Aktuální projevy, který používá tuto nesprávnou entitu, je nutné zkontrolovat ve všech záměrech. Aktuální projevy vyLUISi, že tento text představuje předpovězenou entitu.
|Správně označený text|modrá entita zvýraznění, červené podtržení|Nesprávná předpověď|Poskytněte více projevy s správně označenou entitou na nejrůznějších místech a použitích. Aktuální projevy nejsou dostačující pro učení LUIS, že se jedná o entitu nebo se podobné entity zobrazují ve stejném kontextu. Podobná entita by se měla zkombinovat do jedné entity, takže LUIS se nezaměňuje. Dalším řešením je přidat seznam frází, který bude zvyšovat význam slov. |
|Nesprávně označený text|modrá entita zvýraznění, červené podtržení|Správné předpovědi| Poskytněte více projevy s správně označenou entitou na nejrůznějších místech a použitích. 

## <a name="other-actions"></a>Další akce

Můžete provádět akce, například projevy jako vybranou skupinu nebo jako jednotlivou položku. Skupiny vybraných vzorů projevy změnu kontextové nabídky nad seznamem. Jednotlivé položky mohou používat místní nabídku nad seznamem a jednotlivé kontextové tři tečky na konci každého řádku utterance. 

### <a name="remove-entity-labels-from-utterances"></a>Odebrat popisky entit z projevy

Na stránce záměry můžete odebrat popisky entit učené počítačem z utterance. Pokud se entita nenaučila počítačem, nejde ji odebrat z utterance. Pokud potřebujete z utterance odebrat entitu, která není učená z počítače, je potřeba entitu odstranit z celé aplikace. 

Pokud chcete z utterance odebrat popisek entity učené počítačem, vyberte entitu v utterance. Pak v rozevíracím seznamu entita, který se zobrazí, vyberte **Odebrat popisek** .

### <a name="add-a-prebuilt-entity-label"></a>Přidat předem sestavený popisek entity

Když do aplikace LUIS přidáte předem připravené entity, nemusíte s těmito entitami označovat projevy. Další informace o předem sestavených entitách a o tom, jak je přidat, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Přidat popisek entity regulárního výrazu

Pokud přidáte entity regulárních výrazů do aplikace LUIS, není nutné označit projevy těmito entitami. Další informace o entitách regulárních výrazů a o tom, jak je přidat, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Vytvoření vzoru z utterance

Přečtěte si téma [Přidání vzoru z existující stránky utterance na záměr nebo entitu](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Přidejte vzor. libovolnou entitu

Pokud přidáte vzor. jakékoli entity do aplikace LUIS, nemůžete pomocí těchto entit označit projevy. Jsou platné pouze ve vzorcích. Další informace o vzorcích. jakékoli entity a jejich přidání najdete v tématu [Přidání entit](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Školení aplikace po změně modelu pomocí projevy

Po přidání, úpravě nebo odebrání projevy, [školení](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace pro změny, které mají vliv na dotazy koncového bodu. 

## <a name="next-steps"></a>Další kroky

Po označení projevy ve svých **záměrech**teď můžete vytvořit [složenou entitu](luis-how-to-add-entities.md).
