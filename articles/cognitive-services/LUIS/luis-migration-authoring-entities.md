---
title: Migrace do entity učení počítače V3
titleSuffix: Azure Cognitive Services
description: Vytváření V3 poskytuje jeden nový typ entity, počítačově nabyté entity, spolu se schopností přidat vztahy do počítače naučil entity a další entity nebo funkce aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563831"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrace do entity vytváření V3

Vytváření V3 poskytuje jeden nový typ entity, počítačově nabyté entity, spolu se schopností přidat vztahy do počítače naučil entity a další entity nebo funkce aplikace.

## <a name="entities-are-decomposable-in-v3"></a>Entity jsou rozložitelné ve V3

Entity vytvořené pomocí vytváření v3 API, pomocí [api nebo](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) s [portálem náhledu](https://preview.luis.ai/), umožňují vytvořit model entit s vrstvami s nadřazeným a podřízenými objekty. Nadřazený je známý jako **entita naučil stroj** a podřízené objekty jsou označovány jako **dílčí součásti** počítače naučil entity.

Každá dílčí součást je také počítačově nabytou entitou, ale s přidanými možnostmi konfigurace omezení a popisovačů.

* **Omezení** jsou přesně pravidla pro porovnávání textu, která zaručují, že entita je extrahována, pokud odpovídá pravidlu. Pravidlo je definováno entitou přesného párování textu, aktuálně: [předem sestavenou entitou](luis-reference-prebuilt-entities.md), [entitou regulárního výrazu](reference-entity-regular-expression.md)nebo [entitou seznamu](reference-entity-list.md).
* **Deskriptory** jsou [funkce](luis-concept-feature.md), jako jsou seznamy frází nebo entity, které se používají k silnému označení entity.

Vytváření V3 poskytuje jeden nový typ entity, počítačově nabyté entity, spolu se schopností přidat vztahy do počítače naučil entity a další entity nebo funkce aplikace.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Jak se tyto nové vztahy v porovnání s V2 authoring

Vytváření v2 poskytlo hierarchické a složené entity spolu s rolemi a funkcemi k provedení stejného úkolu. Vzhledem k tomu, že entity, funkce a role nebyly explicitně vzájemně propojeny, bylo obtížné pochopit, jak LUIS implikoval vztahy během předpovědi.

S V3 vztah je explicitní a navržený autory aplikace. To vám jako autorovi aplikace umožňuje:

* Vizuálně zobrazit, jak LUIS předpovídá tyto vztahy, v příkladu projevy
* Testování těchto vztahů s [interaktivním testovacím podoknem](luis-interactive-test.md) nebo v koncovém bodě
* Pomocí těchto relací v klientské aplikaci pomocí dobře strukturovaného, pojmenovaného [objektu JSON](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Plánování

Při migraci zvažte v plánu migrace následující:

* Zálohujte aplikaci LUIS a provádějte migraci v samostatné aplikaci. S V2 a V3 aplikace k dispozici ve stejnou dobu umožňuje ověřit požadované změny a dopad na výsledky předpovědi.
* Zachycení aktuálních metrik úspěšnosti předpovědi
* Zachycení aktuálních informací řídicího panelu jako snímku stavu aplikace
* Kontrola existujících záměrů, entit, seznamů frází, vzorů a dávkových testů
* Následující prvky lze migrovat **beze změny**:
    * Záměry
    * Entity
        * Entiay regulárního výrazu
        * Entita seznamu
    * Funkce
        * Seznam frází
* Následující prvky je třeba migrovat se **změnami**:
    * Entity
        * Hierarchická entita
        * Složená entita
    * Role – role lze použít pouze u počítačem naučené (nadřazené) entity. Role nelze použít u dílčích součástí.
    * Dávkové testy a vzorky, které používají hierarchické a složené entity

Při navrhování plánu migrace ponechte čas na kontrolu konečných entit získaných počítačem po migraci všech hierarchických a složených entit. Zatímco přímá migrace bude fungovat, po provedené změny a zkontrolujte výsledky dávkových testů a předpověď JSON, jednotnější JSON může vést k provádění změn, takže konečné informace dodané do aplikace na straně klienta je uspořádánjinak. To je podobné refaktoringkódu kódu a by měly být zpracovány se stejným procesem kontroly vaše organizace má na místě.

Pokud nemáte dávkové testy na místě pro váš model V2 a migrovat dávkové testy do modelu V3 jako součást migrace, nebudete moci ověřit, jak migrace bude mít vliv na výsledky předpovědi koncového bodu.

## <a name="migrating-from-v2-entities"></a>Migrace z entit V2

Při přechodu na model vytváření V3 byste měli zvážit, jak přejít na entitu naučnou počítačem a její dílčí součásti včetně omezení a popisovačů.

V následující tabulce je uveden název entity, které musí migrovat z v2 na návrh entity V3.

|Typ vývojové entity V2|Typ vývojové entity V3|Příklad|
|--|--|--|
|Složená entita|Strojově načený subjekt|[Víc se uč](#migrate-v2-composite-entity)|
|Hierarchická entita|Role entity naučené strojem|[Víc se uč](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrace složené entity V2

Každá podřízená součást kompozitu V2 by měla být reprezentována podsložkou entity získané strojem V3. Pokud je složený podřízený objekt předem sestavený, regulární výraz nebo entita seznamu, měla by být použita jako **omezení** pro podsložku představující podřízenou součást.

Důležité informace při plánování migrace složené entity do počítače učené entity:
* Podřízené entity nelze použít ve vzorcích
* Podřízené entity již nejsou sdíleny.
* Podřízené entity musí být označeny, pokud se dříve nenaučily stroje.

### <a name="existing-descriptors"></a>Existující popisovače

Jakýkoli seznam frází použitý k posílení slov ve složené entitě by měl být použit jako popisovač pro počítačově naváděnou (nadřazenou) entitu, entitu podsoučásti (podřízenou) nebo záměr (pokud se seznam frází vztahuje pouze na jeden záměr). Naplánujte přidání popisovače do entity, kterou by měl nejvíce zvýšit. Nepřidávejte popisovač obecně do počítače učeného (nadřazeného) entity, pokud nejvíce výrazně zvýší předpověď dílčí součásti (podřízené).

### <a name="new-descriptors"></a>Nové deskriptory

Ve vytváření V3 přidejte krok plánování k vyhodnocení entit jako možných popisovačů pro všechny entity a záměry.

### <a name="example-entity"></a>Příklad entity

Tato entita je pouze příklad. Migrace vlastní entity může vyžadovat další důležité informace.

Zvažte kompozit V2 `order` pro úpravu pizzy, která používá:
* předem předbudová datetimeV2 pro dodací lhůtu
* seznam frází pro zvýšení určitých slov, jako je pizza, koláč, kůra a poleva
* seznam entity pro detekci zálivky, jako jsou houby, olivy, pepperoni.

Příkladem utterance pro tuto entitu je:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Následující tabulka ukazuje migraci:

|Modely V2|Modely V3|
|--|--|
|Nadřazená entita - komponenta s názvem`Order`|Nadřazená entita se učením počítače s názvem`Order`|
|Dítě - Předbudované datetimeV2|* Migrujte předem vytvořenou entitu do nové aplikace.<br>* Přidat omezení na nadřazené pro předem předdefinované datetimeV2.|
|Podřízený - entita seznamu pro zálivky|* Migrovat seznam entity do nové aplikace.<br>* Pak přidejte omezení na nadřazenou entitu seznamu.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrace hierarchické entity V2

Ve vytváření V2 byla poskytnuta hierarchická entita před existujícími rolemi v LUIS. Oba sloužily stejnému účelu extrahování entit na základě použití kontextu. Pokud máte hierarchické entity, můžete si je myslet jako jednoduché entity s rolemi.

Ve v3 authoring:
* Roli lze použít na počítačově načesanou (nadřazenou) entitu.
* Roli nelze použít pro žádné dílčí součásti.

Tato entita je pouze příklad. Migrace vlastní entity může vyžadovat další důležité informace.

Zvažte hierarchickou entitu V2 pro úpravu pizzy `order`:
* kde každé dítě určí buď originální polevu, nebo konečnou

Příkladem utterance pro tuto entitu je:

`Change the topping from mushrooms to olives`

Následující tabulka ukazuje migraci:

|Modely V2|Modely V3|
|--|--|
|Nadřazená entita - komponenta s názvem`Order`|Nadřazená entita se učením počítače s názvem`Order`|
|Podřízená - hierarchická entita s originální a konečnou pizzovou polevou|* Přidat `Order` roli pro každou polevu.|

## <a name="next-steps"></a>Další kroky

* [Zdroje pro vývojáře](developer-reference-resource.md)
