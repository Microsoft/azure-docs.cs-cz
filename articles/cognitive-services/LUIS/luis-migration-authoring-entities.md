---
title: Migrace na verzi V3 – entita strojového učení
description: Vytváření obsahu V3 poskytuje jeden nový typ entity, entitu strojového učení, společně s možností přidávat relace do entity strojového učení a dalších entit nebo funkcí aplikace.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91324684"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrace na entitu pro vytváření obsahu V3

Vytváření obsahu V3 poskytuje jeden nový typ entity, entitu strojového učení, společně s možností přidávat relace do entity strojového učení a dalších entit nebo funkcí aplikace.

## <a name="entities-are-decomposable-in-v3"></a>Entity jsou v v3.

Entity vytvořené pomocí rozhraní API pro vytváření obsahu v3, a to buď pomocí [rozhraní API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) nebo s portálem, umožňují sestavit model vrstvené entity s nadřazenými a podřízenými objekty. Nadřazený objekt je známý jako **entita strojového učení** a podřízené objekty jsou označovány jako **subentity** pro danou entitu počítač s učením.

Každá podentita je také entitou strojového učení, ale s přidanými možnostmi konfigurace funkcí.

* **Požadované funkce** jsou pravidla, která zaručují, že entita je extrahována, když odpovídá funkci. Pravidlo je definováno podle požadované funkce pro model:
    * [Předem vytvořená entita](luis-reference-prebuilt-entities.md)
    * [Entiay regulárního výrazu](reference-entity-regular-expression.md)
    * [Seznam entit](reference-entity-list.md)

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
    * Role – role se dají použít jenom pro entitu strojového učení (nadřazená). Role se nedají použít u subentit.
    * Dávkové testy a vzory, které používají hierarchické a složené entity

Při návrhu plánu migrace ponechte čas na kontrolu konečných entit strojového učení po migraci všech hierarchických a složených entit. I když bude přímá migrace fungovat, po provedení změny a kontrole výsledků testu dávky a kódu JSON předpovědi může více Unified JSON vést k provádění změn, takže konečné informace doručené do aplikace na straně klienta jsou uspořádány jinak. To se podobá refaktorování kódu a měla by být zpracována stejným procesem revize, jakou vaše organizace používá.

Pokud pro model v2 nepoužijete testy pro dávku a v rámci migrace migrujete testy dávky na model v3, nebudete moct ověřit, jak bude migrace ovlivnit výsledky předpovědi koncového bodu.

## <a name="migrating-from-v2-entities"></a>Migrace z entit v2

Jak se chystáte přejít k modelu vytváření obsahu v3, měli byste zvážit, jak přejít na entitu strojového učení a jejích podentit a funkcí.

V následující tabulce jsou poznámy entity, které je třeba migrovat z verze V2 na návrh entity v3.

|Typ entity pro vytváření obsahu V2|Typ entity pro vytváření obsahu V3|Příklad|
|--|--|--|
|Složená entita|Entita zjištěná počítačem|[Víc se uč](#migrate-v2-composite-entity)|
|Hierarchická entita|role entity strojového učení|[Víc se uč](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrace složené entity v2

Každý podřízený objekt v2 složený z nich by měl být reprezentován podentitou entity strojového učení v3. Pokud je složená podřízená položka předem sestavený, regulární výraz nebo entita seznamu, měla by být použita jako požadovaná funkce v subentitě.

Co je potřeba zvážit při plánování migrace složené entity na entitu strojového učení:
* Podřízené entity se nedají používat ve vzorcích.
* Podřízené entity už nejsou sdílené.
* Podřízené entity musí být označeny, pokud se používají pro nestrojové učení.

### <a name="existing-features"></a>Existující funkce

Libovolný seznam frází, který se používá ke zvýšení množství slov v složené entitě, by měl být použit jako součást pro entitu Machine Learning (nadřazená), entitu subentity (podřízeného) nebo záměr (Pokud se seznam frází vztahuje pouze na jeden záměr). Naplánujte přidání funkce do entity, kde by se měla výrazně zvýšit. Nepřiřazujte funkci obecně k entitě strojového učení (nadřazeným), pokud bude nejdůležitějším způsobem vyzvyšovat předpověď dílčí entity (podřízená).

### <a name="new-features"></a>Nové funkce

V části vytváření obsahu V3 přidejte krok plánování pro vyhodnocení entit jako možných funkcí pro všechny entity a záměry.

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
|Nadřazená entita součásti s názvem `Order`|Nadřazená entita-Machine-Learning s názvem `Order`|
|DatetimeV2 sestavené jako podřízené|* Migruje předem vytvořenou entitu na novou aplikaci.<br>* Do nadřazeného objektu přidejte požadovanou funkci pro předem sestavené datetimeV2.|
|Entita podřízeného seznamu pro toppings|* Migrujte entitu seznamu do nové aplikace.<br>* Potom pro entitu seznam přidejte požadovanou funkci pro nadřazenou položku.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrovat hierarchickou entitu v2

V rámci vytváření obsahu V2 se hierarchická entita poskytla před rolemi existujícími v LUIS. Oba sloužily stejný účel extrakce entit na základě použití kontextu. Pokud máte hierarchické entity, můžete je představit jako jednoduché entity s rolemi.

Při vytváření obsahu V3:
* Role se dá použít pro entitu strojového učení (nadřazená).
* Roli nelze použít pro žádné subentity.

Tato entita je pouze příkladem. Vaše vlastní migrace entit může vyžadovat další okolnosti.

Zvažte hierarchickou entitu v2 pro úpravu Pizza `order` :
* kde každá podřízená položka určuje buď původní Topping, nebo konečný Topping

Příklad utterance pro tuto entitu:

`Change the topping from mushrooms to olives`

Následující tabulka ukazuje migraci:

|Modely v2|Modely V3|
|--|--|
|Nadřazená entita součásti s názvem `Order`|Nadřazená entita-Machine-Learning s názvem `Order`|
|Podřízená entita s původní a konečnou pizzaou Topping|* Do `Order` každého Topping přidejte roli.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Omezení změn rozhraní API nahrazené požadovanou funkcí

Tato změna byla provedena v květnu 2020 na konferenci Build a vztahuje se pouze na rozhraní API pro vytváření obsahu v3, kde aplikace používá omezené funkce. Pokud migrujete z vytváření obsahu V2 na verzi V3 nebo jste nepoužívali omezené funkce v3, přeskočte tuto část.

**Funkčnost** – schopnost vyžadovat existující entitu jako funkci pro jiný model a extrahovat tento model pouze v případě, že je entita zjištěna. Funkčnost se nezměnila, ale rozhraní API a terminologie se změnily.

|Předchozí terminologie|Nová terminologie|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Automatická migrace

Od **června 19 2020** nebudete moci vytvářet omezení programově pomocí předchozího rozhraní API pro vytváření obsahu, které tuto funkci vystavilo.

Všechny existující funkce omezení budou automaticky migrovány na příznak požadované funkce. Pro rozhraní API předpovědi nejsou nutné žádné programové změny a žádné výsledné změny kvality předpovědi.

#### <a name="luis-portal-changes"></a>Změny portálu LUIS

Portál LUIS Preview odkazoval na tuto funkci jako **omezení**. Aktuální LUIS portál určuje tuto funkci jako **požadovanou funkci**.

#### <a name="previous-authoring-api"></a>Předchozí rozhraní API pro vytváření

Tato funkce se použila ve verzi Preview vytváření **[podřízeného rozhraní API entity](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** jako součást definice entity pomocí `instanceOf` vlastnosti podřízené entity:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nové rozhraní API pro vytváření obsahu

Tato funkce se teď používá s **[rozhraním API pro přidání entity funkcí](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** s `featureName` použitím `isRequired` vlastností a. Hodnota `featureName` vlastnosti je název modelu.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Další kroky

* [Materiály pro vývojáře](developer-reference-resource.md)
