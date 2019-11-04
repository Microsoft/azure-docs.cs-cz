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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487599"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entity a jejich účel v LUIS

Hlavním účelem entit je poskytnout aplikaci klienta předvídatelné extrakci dat. _Volitelným_a sekundárním účelem je zvýšit předpověď záměru pomocí popisovačů. 

Existují dva typy entit: 

* strojové učení-z kontextu
* nenáročné na počítač – pro přesné shody textu

Vždy začněte s entitou získanou počítačem, protože poskytuje nejširší škálu voleb pro extrakci dat.

## <a name="entity-compared-to-intent"></a>Entita v porovnání s záměrem

Entita představuje koncept dat uvnitř utterance, který chcete extrahovat. 

Vezměte v úvahu následující 3 projevy:

|Promluva|Extrahovaná data|Vysvětlení|
|--|--|--|
|`Help`|-|Nic k extrakci.|
|`Send Bob a present`|Bob, přítomen|Bob je pro dokončení úkolu jednoznačně důležité. K dispozici může být dostatek informací, nebo robot může potřebovat objasnit, co se vyskytuje, na základě následné otázky.|
|`Send Bob a box of chocolates.`|Dvě důležité části dat, Bob a pole čokolády jsou důležité k dokončení požadavku uživatele.|

Utterance může zahrnovat mnoho entit nebo žádný vůbec. Klientská aplikace _může_ potřebovat entitu k provedení jejího úkolu. 

Porovnáním je _vyžadovaná_ předpověď záměru pro utterance a představuje celý utterance. LUIS vyžaduje, aby byl v záměru obsažen příklad projevy. Pokud primární záměr utterance není důležitý pro klientskou aplikaci, přidejte všechny projevyy do záměru None. 

Pokud najdete později v životním cyklu aplikace, chcete projevy přerušit, můžete to snadno udělat. To může být při vytváření projevy organizovat, nebo může být možné použít předpokládaný záměr v klientské aplikaci. 

Neexistuje žádný požadavek na použití prediktivního záměru v klientské aplikaci, ale je vrácen jako součást odpovědi koncového bodu předpovědi.

## <a name="entities-represent-data"></a>Entity reprezentují data

Entity jsou data, která chcete z utterance vyžádat. Může to být název, datum, název produktu nebo libovolná skupina slov. 

|Promluva|Entita|Data|
|--|--|--|
|Koupit 3 lístky do Praha|Předem připravené číslo<br>Umístění. cíl|3<br>New York|
|Koupit lístek od Praha do Brna v 5. březnu|Umístění. Origin<br>Umístění. cíl<br>Předem sestavený datetimeV2|New York<br>Londýn<br>5\. března 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entity jsou volitelné, ale důrazně se doporučují.

I když jsou požadované záměry, jsou entity volitelné. Nemusíte vytvářet entity pro každý koncept ve vaší aplikaci, ale jenom pro ty, které vyžaduje, aby klientská aplikace provedla akci. 

Pokud vaše projevy neobsahuje podrobnosti o tom, že robot potřebuje pokračovat, nemusíte je přidávat. V případě, že vaše aplikace bude vyspělá, můžete je přidat později. 

Pokud si nejste jistí, jak byste tyto informace použili, přidejte několik běžných předem vytvořených entit, jako je [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinální](luis-reference-prebuilt-ordinal.md)číslo, [e-mail](luis-reference-prebuilt-email.md)a [telefonní číslo](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Entity návrhu pro rozložení

Začněte vytvářet návrh entit pomocí počítače, který se naučil. Díky tomu je možné v průběhu času snadno rozšířit návrh a změny vaší entity. Přidejte **dílčí součásti** (podřízené entity) s **omezeními** a **popisovači** k dokončení návrhu entity. 

Návrh pro dekompozici umožňuje, aby LUIS vrátilo v klientské aplikaci hlubokou míru překladu entit. Díky tomu se klientská aplikace může soustředit na obchodní pravidla a ponechá překlad dat na LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Entity, které se naučily počítačem, jsou primárními kolekcemi dat.

Entity, které se naučily počítačem, jsou datovou jednotkou nejvyšší úrovně. Dílčí komponenty jsou podřízené entity entit strojového učení. 

**Omezení** jsou přesné texty vyhovující entitám, které používají pravidla pro identifikaci a extrahování dat. **Popisovače** jsou funkce aplikované na zvýšení relevance slov nebo frází pro předpověď.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy entit

Vyberte entitu na základě toho, jak by měla být data extrahována a jak by měla být reprezentována po extrakci.

|typ entity|Účel|
|--|--|
|[**Strojové učení**](#composite-entity)|Nadřazené seskupení entit bez ohledu na typ entity. Entity, které se naučily počítačem, se seznámí z kontextu v utterance. Díky tomu je variace umístění v příkladu projevy významná. |
|[**Seznamu**](#list-entity)|Seznam položek a jejich synonym, které byly extrahovány s **přesnou shodou textu**|
|[**Vzor. any**](#patternany-entity)|Entita, kde je obtížné určit konec entity |
|[**Předem připravených**](#prebuilt-entity)|Vyškolený pro extrakci konkrétního druhu dat, jako je adresa URL nebo e-mail. Některé z těchto předem vytvořených entit jsou definované v otevřeném zdrojovém projektu pro [rozpoznávání – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Pokud vaše konkrétní jazyková verze nebo entita není aktuálně podporována, přispívat k projektu.|
|[**Regulární výraz**](#regular-expression-entity)|Používá regulární výraz pro **přesnější shodu textu**.|

### <a name="entity-role-defines-context"></a>Role entity definuje kontext

Role entity je pojmenovaný alias na základě kontextu v rámci utterance. Příkladem je utterance pro rezervaci letu, který má dvě umístění, počátek a cíl.

`Book a flight from Seattle to Cairo`

Musí být extrahovány dva příklady `location` entity. Klientská aplikace musí znát typ umístění pro každý, aby bylo možné dokončit nákup lístku. `location` entita potřebuje dvě role `origin` a `destination` musí být označené v příkladu projevy. 

Pokud LUIS nalezne `location`, ale nemůže určit roli, entita umístění se pořád vrátí. Klientská aplikace by musela postupovat podle otázky, aby určila typ umístění, které uživatel chtěl. 

V utterance může existovat více entit a lze je extrahovat bez použití rolí. Pokud kontext věty označuje hodnotu entity, měla by se použít role.

Pokud utterance obsahuje seznam umístění, `I want to travel to Seattle, Cairo, and London.`, jedná se o seznam, kde každá položka nemá další význam. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Pokud potřebujete více než maximální počet entit 

Pokud potřebujete víc, než je limit, obraťte se na podporu. Provedete to tak, že shromáždíte podrobné informace o vašem systému, přejdete na web [Luis](luis-reference-regions.md#luis-website) a pak vyberete **Podpora**. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>Stav předpovědi entity

Portál LUIS se zobrazí, když entita ve vzorovém utterance má jinou předpověď entit než vybraná entita. Toto jiné skóre vychází z aktuálně vyškolených modelů. 

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty dobré [projevy](luis-concept-utterance.md). 

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .
