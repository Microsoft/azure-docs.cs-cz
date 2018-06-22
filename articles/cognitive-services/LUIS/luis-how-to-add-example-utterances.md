---
title: Přidat příklad utterances v aplikacích LEOŠ | Microsoft Docs
titleSuffix: Azure
description: Informace o postupu přidání utterances v aplikacích jazyka Principy (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 74a4b77bd9823e5462eecd438cf4c1d863e79892
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300634"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Přidat příklad utterances a popisek s entitami

Příklad utterances jsou příklady text otázky uživatelského nebo příkazy. Pokud chcete naučit jazyk Principy (LEOŠ), je potřeba přidat [příklad utterances](luis-concept-utterance.md) k [záměr](luis-concept-intent.md).

Obecně platí nejprve přidat utterance příklad záměrem a pak vytvořit entity a utterances popisek na stránce záměrné. Pokud byste místo entity nejprve vytvořte, přečtěte si téma [přidat entity](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Přidat utterance
Na stránku záměrné zadejte příslušné příklad utterance, očekávat od uživatelů, jako například `book 2 adult business tickets to Paris tomorrow on Air France` do textového pole pod záměrné název a potom stiskněte klávesu Enter. 
 
>[!NOTE]
>LEOŠ převede všechny utterances na malá písmena.

![Stránka Podrobnosti snímek záměry, s utterance zvýrazněná](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Utterances se přidají do seznamu utterances pro aktuální záměr. 

## <a name="ignoring-words-and-punctuation"></a>Bez ohledu na slova a interpunkce
Pokud chcete ignorovat konkrétní slova nebo interpunkční znaménka v příkladu utterance, použijte [vzor](luis-concept-patterns.md#pattern-syntax) s _Ignorovat_ syntaxe. 

## <a name="add-simple-entity-label"></a>Přidání popisku jednoduché entity
V následujícím postupu vytvoření a označovat vlastní entity v rámci následující utterance na stránce záměrné:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Vyberte "Vzduchem Francie" v utterance a označte jej jako jednoduchý entitu.

    > [!NOTE]
    > Při výběru slova je označit jako entity:
    > * U jednoho slova vyberte možnost ho. 
    > * Pro sadu dvou nebo více slov vyberte na začátku a na konci sady.

2. V rozevíracím seznamu entity, který se zobrazí můžete vybrat existující entitu nebo přidat novou entitu. Chcete-li přidat novou entitu, zadejte její název do textového pole a pak vyberte **vytvořit novou entitu**. 
 
    ![Stránka Podrobnosti snímek záměry, s jednoduché entity označování zvýrazněnou možností](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. V **jaký typ entity chcete vytvořit?** automaticky otevírané okno dialogové okno, ověřte název entity a vyberte typ jednoduchého entity a potom vyberte **provádí**.

    ![Obrázek dialogového okna potvrzení](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    V tématu [extrakce dat](luis-concept-data-extraction.md#simple-entity-data) Další informace o extrahování jednoduché entity z koncového bodu odpověď na dotaz JSON. Zkuste jednoduché entity [rychlý Start](luis-quickstart-primary-and-secondary-data.md) Další informace o tom, jak používat jednoduché entity.


## <a name="add-list-entity-and-label"></a>Přidání entity seznamu a popisku
Seznam entity představují sadu pevné, uzavřené (přesný text odpovídá) související slov ve vašem systému. 

Seznam entity nápojů, může mít dva normalizovaný hodnoty: horních a soda pop. Každý normalizovaný název má synonyma. Pro horních jsou synonyma nestrukturované H20 plynu. Pro soda pop jsou synonyma výsledek, coly, zázvor. Nemusíte vědět všechny hodnoty, když vytvoříte entity. Můžete přidat více po zkontrolování utterances reálný uživatel s synonyma.

|Normalizovaný název|Synonyma|
|--|--|
|Vodohospodářství|H20 plynu, dvojrozměrném|
|Soda pop|Výsledek, coly, zázvor|

Při vytváření nové entity seznamu na stránce záměrné, dělají dvě věci, které nemusí být samozřejmé. Nejdřív vytvoříte nový seznam tak, že přidáte první položku seznamu. Druhý první položku seznamu jmenuje s slovo nebo frázi, kterou jste vybrali utterance. Když chcete změnit to později na stránce entity, je rychlejší a vyberte utterance, který má slovo, které chcete použít pro název položky seznamu.

Například pokud jste chtěli vytvořit seznam typů nápoj a vybrané slovo `h2o` z utterance vytvořit entitu, v seznamu by mít jednu položku, jejíž název byl h20. Pokud byste chtěli více obecný název, měli byste vybrat utterance, který používá více obecný název. 

1. V utterance, vyberte slovo, které představuje první položku v seznamu, a potom do textového pole zadejte název seznamu a pak vyberte **vytvořit novou entitu**.   

    ![Stránka Podrobnosti snímek záměry, se vytvořit novou entitu zvýrazněná](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. V **jaký typ entity chcete vytvořit?** dialogové okno pole, přidat synonyma této položky seznamu. Pro položku horních v seznamu nápoj přidat `h20`, `perrier`, a `waters`a vyberte **provádí**. Všimněte si, že je "vod" přidat, protože synonyma seznamu budou odpovídat tokenu na úrovni. V anglické jazykové verzi, že úroveň je na úrovni aplikace word tak "vod" by odpovídat "horních" Pokud nebylo v seznamu. 

    ![Snímek obrazovky co typ entity má dialogové okno vytvořit](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Tento seznam nápojů má jenom jeden nápoj typu horních. Můžete přidat další typy nápoj označování jiných utterances nebo úpravou entita z **entity** v levém navigačním panelu. [Úpravy](luis-how-to-add-entities.md#add-list-entities) entity, které poskytuje možnosti zadávání další položky s odpovídající synonyma nebo [import](luis-how-to-add-entities.md#import-list-entity-values) seznamu. 

    V tématu [extrakce dat](luis-concept-data-extraction.md#list-entity-data) Další informace o extrahování seznamu entity z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intent-and-list-entity.md) Další informace o tom, jak používat seznamu entity.

## <a name="add-synonyms-to-the-list-entity"></a>Přidat synonyma do seznamu entity 
Výběrem slovo nebo frázi v utterance přidáte synonymum do seznamu entity. Pokud máte nápoj seznamu entity a chcete přidat `agua` jako synonymum pro horních, postupujte podle kroků:

V utterance, vyberte synonyma word, jako například `aqua` pro horních, pak vyberte název entity seznamu v rozevíracím seznamu, jako například **nepijí**, pak vyberte **nastavit jako synonymum**, pak vyberte v seznamu Položka je totožná s, například **horních**.

![Stránka Podrobnosti snímek záměry, se vytvořit nové synonymum zvýrazněná](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Vytvořit novou položku entity seznam
Vytvořte novou položku pro entitu existující seznamu výběrem slovo nebo frázi v utterance. Pokud máte nápoj seznamu a chcete přidat `tea` jako nová položka, postupujte podle kroků:

V utterance, vyberte word pro novou položku seznamu, jako například `tea`, vyberte v rozevíracím seznamu, jako název entity seznamu **nepijí**, pak vyberte **vytvořit nové synonymum**. 

![Snímek obrazovky přidání nové položky seznamu](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

Slovo teď modře zvýrazněný. Pokud je ukazatel myši nad slovo, značku zobrazí název položky seznamu, jako je například čaj.

![Snímek obrazovky nová značka položky seznamu](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Zalomení entity v složené popisek
Složené entity jsou vytvořené pomocí **entity**. Složenou entitu nelze vytvořit ze záměru stránky. Po vytvoření složeného entita může obtékat entity v utterance na stránce záměrné. 

Za předpokladu, že utterance, `book 2 tickets from Seattle to Cairo`, složené utterance může vrátit informace entity počtu lístků (2), původu (Praha) a cíl (Káhiře) umístění v entitě jednu nadřazenou položku. 

Postupujte podle těchto [kroky](luis-how-to-add-entities.md#add-prebuilt-entity) přidat **číslo** předem entity. Po vytvoření entity `2` utterance je modrá, uvede je s popiskem entity. Předkompilované entity jsou označeny LEOŠ. Nelze přidat nebo odebrat popisek předem entita z jednoho utterance. Pouze můžete přidat nebo odebrat všechny předem popisky přidáním nebo odebráním předem entity z aplikace.

Postupujte podle těchto [kroky](#add-hierarchical-entity-and-label) k vytvoření **umístění** hierarchické entity. Označení původu a cílového umístění v utterance příklad. 

Před zabalením entity v složenou entitu, zajistěte, aby všechny, které jsou vyznačené podřízených entit modře, což znamená, že jste utterance popisem.

1. Zabalit do složené jednotlivých entit, vyberte první s popiskem entity v utterance složené entity. V příkladu utterance `book 2 tickets from Seattle to Cairo`, první entita je číslo 2. Rozevíracím seznamu zobrazí možnosti pro tento výběr.

    ![Snímek obrazovky s číslo vybrané a zvýrazněnou možností rozevíracího seznamu](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Vyberte **zabalení složené entity** z rozevíracího seznamu. 

    ![Snímek obrazovky možností rozevíracího seznamu pro zabalení složené entity s Wrap v složené entity zvýrazněná](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Vyberte poslední slovo složené entity. V utterance tohoto příkladu vyberte "Location::Destination" (představující Káhiře). Zelená řádku pod všechna slova, zahrnuje nonentity slova do utterance, které jsou složené.

    ![Stránka záměr BookFlight – snímek obrazovky s číslem zvýrazněná](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. V rozevíracím seznamu vyberte název složený entity. V tomto příkladu je **TicketOrder**.

    ![Snímek zabalení slova s složené entity s názvem složené entity zvýrazněných v rozevíracím seznamu](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Když správně zalomen entity zelená řádku je pod celou frázi.

    ![Snímek obrazovky utterance se zvýrazněnou složené entity](./media/luis-how-to-add-example-utterances/wrap-5.png)

    V tématu [extrakce dat](luis-concept-data-extraction.md#composite-entity-data) Další informace o extrahování složené entity z koncového bodu odpověď na dotaz JSON. Zkuste složené entity [kurzu](luis-tutorial-composite-entity.md) Další informace o tom, jak použít složené entity.

## <a name="add-hierarchical-entity-and-label"></a>Přidat hierarchické entity a popisku
Hierarchická entita je kategorii kontextově učit a koncepčně související entity. V následujícím příkladu obsahuje entity původní a cílové umístění. 

V utterance `Book 2 tickets from Seattle to Cairo`, Seattle je umístění, původu a Káhiře je cílové umístění. Každé umístění je kontextově jiné a učit z pořadí aplikace word a vybrané aplikace word utterance.

1. Na stránce záměrné v utterance, vyberte "Seattle" a pak zadejte název entity se umístění a potom vyberte **vytvořit novou entitu**.

    ![Dialogové okno snímek obrazovky z vytvořit hierarchické Entity označování](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. V dialogovém okně automaticky otevírané okno Vyberte hierarchické pro **typ Entity**, pak přidejte `Origin` a `Destination` jako podřízené objekty a potom vyberte **provádí**.

    ![Stránka Podrobnosti snímek tříd Intent, s entitou ToLocation zvýrazněná](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. Aplikace word v utterance byl označený verzí hierarchické nadřazená entita. Budete muset přiřadit slovo podřízené entity. Vraťte se do utterance na stránce záměrné. Vyberte slovo, pak v rozevíracím seznamu vyberte název entity, kterou jste vytvořili a postupujte podle v nabídce napravo a zvolte správný podřízené entity.

    ![Stránka Podrobnosti snímek tříd Intent, s entitou ToLocation zvýrazněná](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Podřízené názvy entit musí být jedinečný mezi všechny entity v jediné aplikaci. Dvě různé hierarchické entity nesmí obsahovat podřízených entit se stejným názvem. 

    V tématu [extrakce dat](luis-concept-data-extraction.md#hierarchical-entity-data) Další informace o extrahování hierarchické entity z koncového bodu odpověď na dotaz JSON. Zkuste hierarchické entity [rychlý Start](luis-quickstart-intent-and-hier-entity.md) Další informace o tom, jak používat hierarchické entity.


## <a name="remove-entity-labels-from-utterances"></a>Odebrání utterances entity popisky
Naučili počítač entity popisky můžete odebrat ze utterance na stránce záměrné. Pokud entita není naučili počítače, nelze ji odebrat ze utterance. Pokud potřebujete k odebrání utterance na jiný počítač naučili entitu, budete muset odstranit entitu z celé aplikace. 

K odebrání počítače naučili entity štítek utterance, vyberte v utterance entity. Potom vyberte **odebrat štítek** v rozevíracím seznamu entity, který se zobrazí.

![Stránka Podrobnosti snímek záměry, s popiskem odebrat zvýrazněná](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Přidání popisku předem entity
Pokud přidáte do vaší aplikace LEOŠ předem entity, nemusíte popisek utterances se tyto entity. Další informace o předem entity a postupy pro jejich přidávání, najdete v části [přidat entity](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Přidání entity štítku regulární výraz
Pokud přidáte do vaší aplikace LEOŠ entity regulární výraz, nemusíte popisek utterances se tyto entity. Další informace o regulární výraz entity a postupy pro jejich přidávání, najdete v části [přidat entity](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Vytvořit vzor z utterance
V tématu [přidat vzor z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Přidání pattern.any entity štítku
Pokud přidáte do vaší aplikace LEOŠ pattern.any entity, nelze popisku utterances se tyto entity. Jsou platné v vzory. Další informace o pattern.any entity a postupy pro jejich přidávání, najdete v části [přidat entity](luis-how-to-add-entities.md#add-patternany-entities).

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
## <a name="train-your-app-after-changing-model-with-utterances"></a>Po změně model s utterances cvičení vaší aplikace
Po přidat, upravit nebo odebrat utterances, [cvičení](luis-how-to-train.md) a [publikování](PublishApp.md) aplikace se změny ovlivňují dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

Po označování utterances v vaše záměry, nyní můžete vytvořit [složené entity](luis-how-to-add-entities.md).
