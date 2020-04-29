---
title: Typy entit – LUIS
titleSuffix: Azure Cognitive Services
description: 'Entity extrahují data z utterance. Typy entit poskytují předvídatelné extrakci dat. Existují dva typy entit: počítač-se naučil a počítač se naučil. Je důležité znát, ve kterém typu entity pracujete v projevy.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221026"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entity a jejich účel v LUIS

Hlavním účelem entit je poskytnout aplikaci klienta předvídatelné extrakci dat. _Volitelným_a sekundárním účelem je zvýšit předpověď záměru nebo jiných entit s popisovači.

Existují dva typy entit:

* strojové učení-z kontextu
* nenáročné na počítač – pro přesné shody textu, porovnávání vzorů nebo zjišťování podle předem připravených entit

Entity, které se naučily počítačem, poskytují nejširší škálu voleb pro extrakci dat. Entity nenáročné na počítač fungují podle shody textu a je možné je používat nezávisle nebo jako [omezení](#design-entities-for-decomposition) u entity, která se naučila počítač.

## <a name="entities-represent-data"></a>Entity reprezentují data

Entity jsou data, která chcete z utterance vyžádat, jako jsou názvy, data, názvy produktů nebo libovolná velká skupina slov. Utterance může zahrnovat mnoho entit nebo žádný vůbec. Klientská aplikace _může_ k provedení úlohy potřebovat data.

Entity musí být označeny konzistentně ve všech školicích projevy pro každý záměr v modelu.

 Můžete definovat vlastní entity nebo používat předem připravené entity k ukládání času pro běžné koncepty, jako je [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinální](luis-reference-prebuilt-ordinal.md)číslo, [e-mail](luis-reference-prebuilt-email.md)a [telefonní číslo](luis-reference-prebuilt-phonenumber.md).

|Promluva|Entita|Data|
|--|--|--|
|Koupit 3 lístky do Praha|Předem připravené číslo<br>Umístění. cíl|3<br>New York|
|Koupit lístek od Praha do Brna v 5. březnu|Umístění. Origin<br>Umístění. cíl<br>Předem sestavený datetimeV2|New York<br>Londýn<br>5. března 2018|

### <a name="entities-are-optional"></a>Entity jsou volitelné.

I když jsou požadované záměry, jsou entity volitelné. Nemusíte vytvářet entity pro každý koncept ve vaší aplikaci, ale jenom pro ty, které vyžaduje, aby klientská aplikace provedla akci.

Pokud vaše projevy data nevyžadují klientská aplikace, nemusíte přidávat entity. Jak se vyvíjí vaše aplikace a identifikují se nové potřeby dat, můžete do modelu LUIS přidat vhodné entity později.

## <a name="entity-compared-to-intent"></a>Entita v porovnání s záměrem

Entita představuje koncept dat uvnitř utterance, který chcete extrahovat.

Utterance může volitelně zahrnovat entity. Porovnáním je _vyžadovaná_ předpověď záměru pro utterance a představuje celý utterance. LUIS vyžaduje, aby byl v záměru obsažen příklad projevy.

Vezměte v úvahu následující 4 projevy:

|Promluva|Předpokládané záměr|Extrahované entity|Vysvětlení|
|--|--|--|--|
|Nápověda|Nápověda|-|Nic k extrakci.|
|Poslat něco|sendSomething|-|Nic k extrakci. Model nebyl `something` v tomto kontextu vyškolen a neexistuje žádný příjemce.|
|Poslat Bobovi a|sendSomething|`Bob`, `present`|Model byl vyučen s předem vytvořenou entitou [Person](luis-reference-prebuilt-person.md) , která extrahuje název `Bob`. K extrakci `present`se použila entita získaná počítačem.|
|Poslat Bobovi pole čokolády|sendSomething|`Bob`, `box of chocolates`|Dvě důležité části dat `Bob` a `box of chocolates`byly extrahovány entitami.|

## <a name="design-entities-for-decomposition"></a>Entity návrhu pro rozložení

Je dobrým návrhem entit, aby entita nejvyšší úrovně měla entitu získanou počítačem. To umožňuje, aby se změny návrhu vaší entity v průběhu času a používání **dílčích komponent** (podřízených entit), volitelně s **omezeními** a **popisovači**, daly rozložit na nejvyšší úrovni do částí, které klientská aplikace potřebuje.

Návrh pro dekompozici umožňuje, aby LUIS vrátilo v klientské aplikaci hlubokou míru překladu entit. Díky tomu se vaše klientská aplikace může soustředit na obchodní pravidla a ponechání rozlišení dat LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Entity, které se naučily počítačem, jsou primárními kolekcemi dat.

Entity, které se [**naučily počítačem**](tutorial-machine-learned-entity.md) , jsou datovou jednotkou nejvyšší úrovně. Dílčí komponenty jsou podřízené entity entit strojového učení.

Na základě kontextu získaného prostřednictvím školicích projevy se na počítač zavede triggery. **Omezení** jsou volitelná pravidla, která se vztahují na počítač, který se bude spouštět na základě definice přesného textu, který se v nestrojově identifikované entitě používá jako [seznam](reference-entity-list.md) nebo [regulární výraz](reference-entity-regular-expression.md). Například entita získaná `size` počítačem může mít omezení entity `sizeList` seznamu, které omezuje `size` entitu na Trigger pouze v případě, že jsou zjištěny hodnoty obsažené v `sizeList` entitě.

[**Popisovače**](luis-concept-feature.md) jsou funkce aplikované na zvýšení relevance slov nebo frází pro předpověď. Nazývají se *popisovače* , protože se používají k *popisu* záměru nebo entity. Deskriptory popisují rozlišení vlastností nebo atributů dat, jako jsou důležitá slova nebo fráze, které LUIS sleduje a učí.

Když ve své aplikaci LUIS vytvoříte funkci seznamu frází, je ve výchozím nastavení povolená globálně a v rámci všech záměrů a entit se aplikuje rovnoměrně. Pokud ale použijete seznam frází jako popisovač (funkce) pro určitou entitu (nebo *model*), bude se její obor omezit na použití jenom pro tento model a už se nepoužívá se všemi ostatními modely. Použití seznamu frází jako popisovače pro model pomáhá dekompozici tím, že pomáhá s přesností pro model, na který se aplikuje.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy entit

Vyberte entitu na základě toho, jak by měla být data extrahována a jak by měla být reprezentována po extrakci.

|Typ entity|Účel|
|--|--|
|[**Strojové učení**](tutorial-machine-learned-entity.md)|Entity, které se naučily počítačem, se seznámí z kontextu v utterance. Nadřazené seskupení entit bez ohledu na typ entity. Díky tomu je variace umístění v příkladu projevy významná. |
|[**Seznamu**](reference-entity-list.md)|Seznam položek a jejich synonym, které byly extrahovány s **přesnou shodou textu**|
|[**Vzor. any**](reference-entity-pattern-any.md)|Entita, kde je obtížné určit konec entity |
|[**Předem připravených**](luis-reference-prebuilt-entities.md)|Vyškolený pro extrakci konkrétního druhu dat, jako je adresa URL nebo e-mail. Některé z těchto předem vytvořených entit jsou definované v otevřeném zdrojovém projektu pro [rozpoznávání – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Pokud vaše konkrétní jazyková verze nebo entita není aktuálně podporována, přispívat k projektu.|
|[**Regulární výraz**](reference-entity-regular-expression.md)|Používá regulární výraz pro **přesnější shodu textu**.|

## <a name="extracting-contextually-related-data"></a>Extrahování kontextově souvisejících dat

Utterance může obsahovat dva nebo více výskytů entity, kde význam dat je založen na kontextu v rámci utterance. Příkladem je utterance pro rezervaci letu, který má dvě umístění, počátek a cíl.

`Book a flight from Seattle to Cairo`

Musí být extrahovány dva `location` příklady entity. Klientská aplikace musí znát typ umístění pro každý, aby bylo možné dokončit nákup lístku.

Existují dva způsoby, jak extrahovat kontextově související data:

 * `location` Entita je entita získaná počítačem a používá dvě entity dílčí součásti k zachycení `origin` a `destination` (preferované).
 * `location` Entita používá dvě **role** `origin` a.`destination`

V utterance může existovat více entit a lze je extrahovat bez použití dekompozice nebo rolí, pokud kontext, ve kterém se používají, nemá žádný význam. Například pokud utterance obsahuje seznam umístění, `I want to travel to Seattle, Cairo, and London.`jedná se o seznam, kde každá položka nemá další význam.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Použití entit dílčí komponenty pro entitu s podporou počítače k definování kontextu

K extrakci dat, která popisuje akci rezervace letu, a následnému rozbalení entity nejvyšší úrovně do samostatných částí, které vyžaduje klientská aplikace, můžete použít datovou [**entitu poučenou počítačem**](tutorial-machine-learned-entity.md) .

V tomto příkladu `Book a flight from Seattle to Cairo`by entita nejvyšší úrovně mohla být `travelAction` označena k extrakci. `flight from Seattle to Cairo` Pak se vytvoří dvě entity dílčí komponenty, které `origin` se `destination`zavolají a, s omezením použitým předem `geographyV2` sestavenou entitou. Ve školicích projevy, `origin` a `destination` jsou vhodně označeny.

### <a name="using-entity-role-to-define-context"></a>Použití role entity k definování kontextu

Role je pojmenovaný alias pro entitu na základě kontextu v rámci utterance. Roli lze použít pro libovolný předem sestavený nebo vlastní typ entity a používá se v obou příkladech projevy a Patterns. V tomto příkladu `location` entita potřebuje dvě role `origin` a `destination` a musí být označené v příkladu projevy.

Pokud LUIS najde, `location` ale nemůže určit roli, entita umístění se pořád vrátí. Klientská aplikace by musela postupovat podle otázky, aby určila typ umístění, které uživatel chtěl.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Pokud potřebujete více než maximální počet entit

Pokud potřebujete víc, než je limit, obraťte se na podporu. Provedete to tak, že shromáždíte podrobné informace o vašem systému, přejdete na web [Luis](luis-reference-regions.md#luis-website) a pak vyberete **Podpora**. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stav předpovědi entity

Portál LUIS se zobrazí, když entita ve vzorovém utterance má jinou předpověď entit než vybraná entita. Toto jiné skóre vychází z aktuálně vyškolených modelů.

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty dobré [projevy](luis-concept-utterance.md).

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .

Viz [kurz: extrakce strukturovaných dat ze utterance uživatelů pomocí entit strojového učení v tématu Language Understanding (Luis)](tutorial-machine-learned-entity.md) , kde se dozvíte, jak extrahovat strukturovaná data z utterance pomocí této entity, kterou se naučila počítač.
 
