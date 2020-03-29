---
title: Typy entit – LUIS
titleSuffix: Azure Cognitive Services
description: 'Entity extrahovat data z utterance. Typy entit poskytují předvídatelné extrakce dat. Existují dva typy entit: strojově naučené a nestrojově naučené. Je důležité vědět, jaký typ entity pracujete s v projevy.'
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221026"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entity a jejich účel v LUIS

Primárním účelem entit je poskytnout klientské aplikaci předvídatelnou extrakci dat. _Volitelné_, sekundární účel je zvýšit předpověď záměru nebo jiné entity s popisovače.

Existují dva typy entit:

* strojově naučený - z kontextu
* nestrojově naučené - pro přesné textové shody, porovnávání vzorů nebo detekci předem sestavenými entitami

Počítačem učesané entity poskytují nejširší škálu možností extrakce dat. Entity neučesané počítačem pracují podle párování textu a mohou být použity nezávisle nebo jako [omezení](#design-entities-for-decomposition) entity učené počítačem.

## <a name="entities-represent-data"></a>Entity představují data

Entity jsou data, která chcete získat z utterance, jako jsou názvy, data, názvy produktů nebo jakékoli významné skupiny slov. Utterance může obsahovat mnoho entit nebo vůbec žádné. Klientská aplikace _může_ potřebovat data k provedení své úlohy.

Entity musí být konzistentně označeny napříč všemi projevy školení pro každý záměr v modelu.

 Můžete definovat vlastní entity nebo použít předem sestavené entity, abyste ušetřili čas pro běžné koncepty, jako je [datetimeV2](luis-reference-prebuilt-datetimev2.md), [řadový](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md)a [telefonní číslo](luis-reference-prebuilt-phonenumber.md).

|Promluva|Entita|Data|
|--|--|--|
|Koupit 3 vstupenky do New Yorku|Předem sestavené číslo<br>Umístění.Cíl|3<br>New York|
|Koupit jízdenku z New Yorku do Londýna března 5|Umístění.Původ<br>Umístění.Cíl<br>Předbudované datetimeV2|New York<br>Londýn<br>5. března 2018|

### <a name="entities-are-optional"></a>Entity jsou volitelné

Zatímco záměry jsou požadovány, entity jsou volitelné. Není nutné vytvářet entity pro každý koncept ve vaší aplikaci, ale pouze pro ty, které jsou požadovány pro klientskou aplikaci provést akci.

Pokud vaše projevy nemají data, která klientská aplikace vyžaduje, není nutné přidávat entity. Jak se vaše aplikace vyvíjí a je identifikována nová potřeba dat, můžete později přidat příslušné entity do modelu LUIS.

## <a name="entity-compared-to-intent"></a>Entita ve srovnání se záměrem

Entita představuje koncept dat uvnitř utterance, které chcete extrahovat.

Utterance může volitelně zahrnovat entity. Pro srovnání předpověď záměru utterance je _vyžadována_ a představuje celý utterance. Služba LUIS vyžaduje příklad projevy jsou obsaženy v záměru.

Zvažte následující 4 projevy:

|Promluva|Záměr předpovídaný|Extrahované entity|Vysvětlení|
|--|--|--|--|
|Nápověda|Nápověda|-|Není co extrahovat.|
|Poslat něco|sendSomething|-|Není co extrahovat. Model nebyl trénován `something` extrahovat v tomto kontextu a není příjemce jeden.|
|Poslat Bobovi dárek|sendSomething|`Bob`, `present`|Model byl trénován s [personName](luis-reference-prebuilt-person.md) předem sestavené `Bob`entity, která extrahoval název . Strojově nabytá entita byla použita k extrakci `present`.|
|Pošlete Bobovi bonboniéru|sendSomething|`Bob`, `box of chocolates`|Dvě důležité části dat `Bob` a `box of chocolates`, byly extrahovány entitami.|

## <a name="design-entities-for-decomposition"></a>Entity návrhu pro rozklad

Je dobré vytvořit z entity nejvyšší úrovně entitu nabytou počítačem. To umožňuje změny návrhu entity v průběhu času a použití **dílčích součástí** (podřízených entit), volitelně s **omezeními** a **popisovači**, rozkládají entitu nejvyšší úrovně na součásti potřebné klientskou aplikací.

Návrh pro rozklad umožňuje LUIS vrátit hluboký stupeň řešení entity do klientské aplikace. To umožňuje klientské aplikace zaměřit se na obchodní pravidla a ponechat řešení dat luis.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Počítačem učené entity jsou primární kolekce dat

[**Entity získané počítačem**](tutorial-machine-learned-entity.md) jsou datovou jednotkou nejvyšší úrovně. Dílčí součásti jsou podřízené entity strojově učesaných entit.

Počítačově nabytá entita se aktivuje na základě kontextu naučené prostřednictvím promluv školení. Omezení jsou **volitelná** pravidla použitá pro entitu získanou počítačem, která dále omezuje aktivaci na základě definice přesného porovnávání textu entity, která nebyla naučeným strojem, jako je [list](reference-entity-list.md) nebo [regex](reference-entity-regular-expression.md). `size` Entita získaná počítačem `sizeList` může mít například `size` omezení entity seznamu, která entitu omezuje tak, aby se aktivovala pouze v případě, že dojde k hodnotám obsaženým v `sizeList` entitě.

[**Deskriptory**](luis-concept-feature.md) jsou funkce použité ke zvýšení relevance slov nebo frází pro předpověď. Nazývají se *popisovače,* protože se používají k *popisu* záměru nebo entity. Deskriptory popisují rozlišování vlastností nebo atributů dat, jako jsou důležitá slova nebo fráze, které LUIS pozoruje a učí se skrz.

Když vytvoříte funkci seznamu frází v aplikaci LUIS, je ve výchozím nastavení povolena globálně a platí rovnoměrně ve všech záměrech a entitách. Pokud však použijete seznam frází jako popisovač (funkci) entity (nebo *modelu)* získanépočítačem , pak se jeho rozsah zmenší tak, aby se vztahoval pouze na tento model, a již se nepoužívá u všech ostatních modelů. Použití seznamu frází jako popisovače modelu pomáhá rozkladu tím, že pomáhá s přesností modelu, na který je použit.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy entit

Zvolte entitu na základě toho, jak mají být data extrahována a jak by měla být reprezentována po jejich extrahování.

|Typ entity|Účel|
|--|--|
|[**Strojově naučený**](tutorial-machine-learned-entity.md)|Počítačem nabyté entity se učí z kontextu v utterance. Nadřazené seskupení entit bez ohledu na typ entity. To umožňuje variantu umístění v příkladu projevy významné. |
|[**Seznamu**](reference-entity-list.md)|Seznam položek a jejich synonyma extrahované s **přesnou textovou shodu**.|
|[**Pattern.any**](reference-entity-pattern-any.md)|Entita, kde je obtížné určit konec účetní jednotky. |
|[**Předem sestavené**](luis-reference-prebuilt-entities.md)|Již vyškoleni k extrahování konkrétnídruh dat, jako je adresa URL nebo e-mail. Některé z těchto předem vytvořených entit jsou definovány v projektu rozpoznávání textu s otevřeným [zdrojovým kódem.](https://github.com/Microsoft/Recognizers-Text) Pokud vaše konkrétní jazyková verze nebo entita není aktuálně podporována, přispějte do projektu.|
|[**Regulární výraz**](reference-entity-regular-expression.md)|Používá regulární výraz pro **přesnou shodu textu**.|

## <a name="extracting-contextually-related-data"></a>Extrahování kontextově souvisejících dat

Utterance může obsahovat dva nebo více výskytů entity, kde je význam dat založen na kontextu v utterance. Příkladem je utterance pro rezervaci letu, který má dvě umístění, původ a cíl.

`Book a flight from Seattle to Cairo`

Dva příklady `location` entity je třeba extrahovat. Klientská aplikace potřebuje znát typ umístění pro každý za účelem dokončení nákupu jízdenky.

Existují dvě metody pro extrahování kontextově souvisejících dat:

 * Entita `location` je entita nazíaná počítačem `origin` `destination` a používá dvě entity podkomponent k zachycení a (upřednostňované)
 * Entita `location` používá `origin` dvě **role**`destination`

Více entit může existovat v utterance a lze extrahovat bez použití rozkladu nebo role, pokud kontext, ve kterém jsou používány nemá žádný význam. Například pokud utterance obsahuje seznam umístění `I want to travel to Seattle, Cairo, and London.`, jedná se o seznam, kde každá položka nemá další význam.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Použití entit dílčích komponent entity naučené počítače k definování kontextu

Entitu [**naučenou počítačem**](tutorial-machine-learned-entity.md) můžete použít k extrahování dat, která popisují akci rezervace letu, a k rozkladu entity nejvyšší úrovně na samostatné součásti potřebné klientskou aplikací.

V tomto `Book a flight from Seattle to Cairo`příkladu může být `travelAction` entita nejvyšší `flight from Seattle to Cairo`úrovně označena a označena k extrahování . Poté jsou vytvořeny dvě entity `origin` dílčí `destination`chod součásti, které `geographyV2` jsou volány a , obě s vazbou použitou předem sestavenou entitou. V promluvy školení `origin` a `destination` jsou označeny odpovídajícím způsobem.

### <a name="using-entity-role-to-define-context"></a>Použití role entity k definování kontextu

Role je pojmenovaný alias entity na základě kontextu v utterance. Roli lze použít s libovolným předem sestaveným nebo vlastním typem entity a použita v ukázkových projevech i vzorech. V tomto příkladu `location` entita `origin` `destination` potřebuje dvě role a a obě musí být označeny v příkladu projevy.

Pokud služba `location` LUIS najde roli, ale nemůže určit, entita umístění je stále vrácena. Klientská aplikace bude muset navázat na otázku k určení, jaký typ umístění uživatel znamená.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Pokud potřebujete více než maximální počet entit

Pokud potřebujete více než limit, obraťte se na podporu. Chcete-li tak učinit, shromážděte podrobné informace o vašem systému, přejděte na web [LUIS](luis-reference-regions.md#luis-website) a vyberte **možnost Podpora**. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stav predikce entity

Portál LUIS zobrazuje, když entita v příkladu utterance má jinou entitu než entita, kterou jste vybrali. Toto jiné skóre je založeno na aktuálním trénovaném modelu.

## <a name="next-steps"></a>Další kroky

Naučte se koncepty o dobré [projevy](luis-concept-utterance.md).

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu [Přidání entit.](luis-how-to-add-entities.md)

Viz [kurz: Extrahovat strukturovaná data z promluvy uživatele s počítačově načené entity v jazyce porozumění (LUIS)](tutorial-machine-learned-entity.md) se dozvíte, jak extrahovat strukturovaná data z utterance pomocí entity naučil počítače.
 
