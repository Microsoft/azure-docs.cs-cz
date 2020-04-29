---
title: Migrace na počítač se zjištěnou entitou V3
titleSuffix: Azure Cognitive Services
description: Vytváření obsahu V3 poskytuje jeden nový typ entity, počítačově získanou entitu a možnost přidávat relace do uživatelsky získané entity a dalších entit nebo funkcí aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75563831"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrace na entitu pro vytváření obsahu V3

Vytváření obsahu V3 poskytuje jeden nový typ entity, počítačově získanou entitu a možnost přidávat relace do uživatelsky získané entity a dalších entit nebo funkcí aplikace.

## <a name="entities-are-decomposable-in-v3"></a>Entity jsou v v3.

Entity vytvořené pomocí rozhraní API pro vytváření obsahu v3, a to buď pomocí [rozhraní API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) nebo na [portálu Preview](https://preview.luis.ai/), umožňují sestavit model vrstvené entity s nadřazenými a podřízenými objekty. Nadřazený objekt je známý jako **entita získaná počítačem** a podřízené položky se označují jako **dílčí součásti** entity získané počítačem.

Každá podsoučást je také entitou získanou počítačem, ale s přidanými možnostmi konfigurace omezení a popisovačů.

* **Omezení** jsou přesná pravidla pro párování textu, která zajišťují, že se entita extrahuje, když odpovídá pravidlu. Pravidlo je definováno přesnou shodnou textovou entitou, aktuálně: [předem vytvořenou entitou](luis-reference-prebuilt-entities.md), [entitou regulárního výrazu](reference-entity-regular-expression.md)nebo [entitou seznamu](reference-entity-list.md).
* **Popisovače** jsou [funkce](luis-concept-feature.md), jako jsou například seznamy frází nebo entity, které se používají ke silným označení entity.

Vytváření obsahu V3 poskytuje jeden nový typ entity, počítačově získanou entitu a možnost přidávat relace do uživatelsky získané entity a dalších entit nebo funkcí aplikace.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Postup při porovnání těchto nových vztahů s vytvářením v2

V2 vytváření obsahu poskytuje hierarchické a složené entity spolu s rolemi a funkcemi pro splnění této stejné úlohy. Vzhledem k tomu, že entity, funkce a role přímo vzájemně nesouvisejí, bylo obtížné pochopit, jak LUIS předpokládalo vztahy během předpovědi.

V případě verze v3 je vztah explicitní a navržený autory aplikace. To vám umožní jako autora aplikace:

* Vizuálně vidíte, jak LUIS předpověď těchto vztahů, v příkladu projevy
* Otestujte tyto vztahy buď pomocí [interaktivního testovacího podokna](luis-interactive-test.md) , nebo na koncovém bodu.
* Tyto relace použijte v klientské aplikaci prostřednictvím dobře strukturovaného, pojmenovaného, vnořeného [objektu. JSON.](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Plánování

Při migraci zvažte následující skutečnosti v plánu migrace:

* Zálohujte aplikaci LUIS a proveďte migraci na samostatné aplikaci. K dispozici je aplikace V2 a V3 ve stejnou dobu, která umožňuje ověřit požadované změny a dopad na výsledky předpovědi.
* Zachytit metriky úspěšnosti aktuální předpovědi
* Zachytit aktuální informace o řídicím panelu jako snímek stavu aplikace
* Kontrola existujících záměrů, entit, seznamů frází, vzorů a dávkových testů
* Následující prvky lze migrovat **bez změny**:
    * Záměry
    * Entity
        * Entiay regulárního výrazu
        * Entita seznamu
    * Funkce
        * Seznam frází
* Následující prvky musí být migrovány **se změnami**:
    * Entity
        * Hierarchická entita
        * Složená entita
    * Role – role se dají použít jenom u entity, které se naučila počítačová (nadřazená). Role nelze použít pro dílčí součásti
    * Dávkové testy a vzory, které používají hierarchické a složené entity

Při návrhu plánu migrace ponechte čas na kontrolu finálních entit zjištěných počítačem, až budou všechny hierarchické a složené entity migrovány. I když bude přímá migrace fungovat, po provedení změny a kontrole výsledků testu dávky a kódu JSON předpovědi může více Unified JSON vést k provádění změn, takže konečné informace doručené do aplikace na straně klienta jsou uspořádány jinak. To se podobá refaktorování kódu a měla by být zpracována stejným procesem revize, jakou vaše organizace používá.

Pokud pro model v2 nepoužijete testy pro dávku a v rámci migrace migrujete testy dávky na model v3, nebudete moct ověřit, jak bude migrace ovlivnit výsledky předpovědi koncového bodu.

## <a name="migrating-from-v2-entities"></a>Migrace z entit v2

Jak se chystáte přejít k modelu vytváření obsahu v3, měli byste zvážit, jak přejít na počítač se naučilou entitou a její dílčí komponenty, včetně omezení a popisovačů.

V následující tabulce jsou poznámy entity, které je třeba migrovat z verze V2 na návrh entity v3.

|Typ entity pro vytváření obsahu V2|Typ entity pro vytváření obsahu V3|Příklad|
|--|--|--|
|Složená entita|Entita zjištěná počítačem|[Víc se uč](#migrate-v2-composite-entity)|
|Hierarchická entita|Role entity naučila počítač|[Víc se uč](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrace složené entity v2

Každý podřízený objekt v2 složený z nich by měl být reprezentován podkomponentou této entity, která se naučila počítač. Pokud je složený podřízený předem sestavený, regulární výraz nebo entitu seznamu, měl by být použit jako **omezení** u dílčí komponenty představující podřízenou položku.

Co je potřeba zvážit při plánování migrace složené entity na konkrétní počítačově učenou entitu:
* Podřízené entity se nedají používat ve vzorcích.
* Podřízené entity už nejsou sdílené.
* Podřízené entity musí být označeny, pokud se používají pro nestrojové učení.

### <a name="existing-descriptors"></a>Existující popisovače

Libovolný seznam frází, který se používá ke zvýšení množství slov v složené entitě, by měl být použit jako popisovač pro entitu (nadřazenou) (nadřazenou), entitu (podřízenou) nebo záměr (Pokud se seznam frází vztahuje pouze na jeden záměr). Naplánujte Přidání popisovače k entitě, kterou by se mělo zvýšit. Nepřiřazujte popisovač obecně na počítačově získanou (nadřazenou) entitu, pokud bude mnohem významně zvyšovat předpověď dílčí komponenty (podřízená součást).

### <a name="new-descriptors"></a>Nové popisovače

V části vytváření obsahu V3 přidejte krok plánování pro vyhodnocení entit jako možných popisovačů pro všechny entity a záměry.

### <a name="example-entity"></a>Příklad entity

Tato entita je pouze příkladem. Vaše vlastní migrace entit může vyžadovat další okolnosti.

Při úpravách Pizza `order` , které používají:, zvažte složený v2.
* předem vytvořená datetimeV2 pro čas doručení
* seznam frází pro zvýšení určitých slov, jako jsou Pizza, výseč, crust a Topping
* Uveďte entitu pro detekci toppings, jako jsou žampiony, olivy, pepperoni.

Příklad utterance pro tuto entitu:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Následující tabulka ukazuje migraci:

|Modely v2|Modely V3|
|--|--|
|Nadřazená entita součásti s názvem`Order`|Entita získaná nadřazeným počítačem s názvem`Order`|
|DatetimeV2 sestavené jako podřízené|* Migruje předem vytvořenou entitu na novou aplikaci.<br>* Do nadřazeného objektu přidejte omezení pro předem sestavené datetimeV2.|
|Entita podřízeného seznamu pro toppings|* Migrujte entitu seznamu do nové aplikace.<br>* Pak přidejte omezení pro nadřazenou entitu seznamu.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrovat hierarchickou entitu v2

V rámci vytváření obsahu V2 se hierarchická entita poskytla před rolemi existujícími v LUIS. Oba sloužily stejný účel extrakce entit na základě použití kontextu. Pokud máte hierarchické entity, můžete je představit jako jednoduché entity s rolemi.

Při vytváření obsahu V3:
* Roli lze použít u entity, kterou se naučila počítačová (nadřazená).
* Roli nelze použít pro žádné dílčí součásti.

Tato entita je pouze příkladem. Vaše vlastní migrace entit může vyžadovat další okolnosti.

Zvažte hierarchickou entitu v2 pro úpravu Pizza `order`:
* kde každá podřízená položka určuje buď původní Topping, nebo konečný Topping

Příklad utterance pro tuto entitu:

`Change the topping from mushrooms to olives`

Následující tabulka ukazuje migraci:

|Modely v2|Modely V3|
|--|--|
|Nadřazená entita součásti s názvem`Order`|Entita získaná nadřazeným počítačem s názvem`Order`|
|Podřízená entita s původní a konečnou pizzaou Topping|* Do `Order` každého Topping přidejte roli.|

## <a name="next-steps"></a>Další kroky

* [Materiály pro vývojáře](developer-reference-resource.md)
