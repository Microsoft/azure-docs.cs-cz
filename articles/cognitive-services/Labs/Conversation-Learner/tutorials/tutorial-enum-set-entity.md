---
title: Kdy používat entity ENUM a nastavovat akce entit s modelem Conversation Learner – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se, že je vhodné používat entity ENUM a nastavovat akce entit s modelem Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ROBOTS: NOINDEX
ms.openlocfilehash: 5443b97febd6bf3831690531bceb540181e7676c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706981"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Kdy použít entity ENUM a nastavit akce entit

Tento kurz vysvětluje, kdy byste měli používat entity ENUM (výčet) a SET_ENTITY akce.

## <a name="video"></a>Video

[![Nastavit kurz pro sadu entit Preview](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Co je zahrnuto

V tomto kurzu se zavedou dvě nové funkce. Nový typ entity s názvem ENUM (Short pro výčet) a nový typ akce s názvem SET_ENTITY, který může odkazovat na jednu z těchto hodnot výčtu, a jak název implikuje, nastaví entitu na tuto hodnotu. Jak se dozvíte níže, tyto nové funkce se společně použijí a my vám vysvětlíme, co jsou a jak je používat níže. Než se dostanete k podrobnostem, je důležité pochopit, k jakým potížím se tyto funkce pomůžou vyřešit.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problém

V konverzacích jsou případy, kde význam slov závisí na kontextu.  Popsána klíčová slova se běžně učí a extrahují pomocí služby porozumění jazyku, ale v těchto případech se nemusí být schopni naučit používat příklady s popisky.

Představte si, že jste overhear část konverzace mezi lidmi a vy uslyšíte jenom slovo "Ano". Nevíte, že "Ano" souhlasí s tím, že jste před tím, než jste si poznamenali dotaz předtím, nevěděli nebo potvrdili. Otázka, na kterou jste požádáni, je kontextem, což dává smysl k odpovědi. Podobně vzhledem k tomu, že "Ano" představuje běžnou reakci na mnoho různých otázek, které se nedají zjistit, protože byste se [](04-introduction-to-entities.md) mohli seznámit s vlastními školenými entitami, protože se tak naučíte označovat každé "Ano" jako tuto entitu.

### <a name="example"></a>Příklad

Pojďme dál objasnit s následujícím příkladem:

Dole Líbí se vám Azure Cognitive Services?
Uživatel: Ano, robot: Líbí se vám zmrzlina?
Uživatel: Ano

V předchozích kurzech jsme se podívali na [vlastní výukové](04-introduction-to-entities.md) entity a vaši úvodní myšlenku mohli vytvořit entitu s názvem "likesCogServices" a označit jako tuto entitu první "Ano".  Systém však také označí druhý "Ano". Po pokusu o opravu popisku druhé "Ano" na "likesIceCream" by se vytvořil konflikt dvou stejných vstupů "Ano", což znamená, že bude zablokováno.

V těchto případech je potřeba použít entity ENUM a SET_ENTITY akce.

## <a name="when-to-use-enums-or-setentity-actions"></a>Kdy použít výčty nebo SET_ENTITY akce

Pomocí těchto pravidel můžete zjistit, kdy používat entity ENUM a akce SET_ENTITY:

- Zjišťování nebo nastavení entity je závislé na kontextu.
- Počet možných hodnot je pevný (Ano a ne, by to byly dvě hodnoty).

Jinými slovy, můžete je použít pro všechny výzvy zavřít a ukončit, jako jsou například potvrzovací dotazy, které mají vždycky za následek Ano nebo ne.

> [!NOTE]
> V současné době máme omezení až o 5 hodnot na jednu entitu výčtu. Každá hodnota používá jeden z slotů v aktuálním limitu 64. Viz [CL-Values-a-hranice](../cl-values-and-boundaries.md)

Příklad: Dole Je vaše objednávka správná?
Uživatel: Ano

Když jsou možné hodnoty entity otevřené – ukončené a neopravené, bude nutné použít alternativní funkci, jako je [očekávaná entita](05-expected-entity.md).

Příklad: Dole Jak se jmenuješ?
Uživatel: Matný robot: Jaká je vaše oblíbená barva?
Uživatel: Stříbrný

Tyto výzvy se považují za otevřené – ukončené, protože by mohly být zodpovězeny pomocí libovolných hodnot.

## <a name="what"></a>Co

### <a name="enum-entities"></a>Vyčíslit entity

Entity ENUM se vytvoří stejně jako ostatní entity. Podobně jako u "programových" entit nemůžete označit slova jako tyto entity. Místo toho je třeba je nastavit prostřednictvím kódu nebo SET_ENTITY akcí.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Nastavení akcí entit

Jak je uvedeno výše, akce "nastavit entitu" jednoduše nastavují entitu na známou hodnotu výčtu. Stejné výsledky můžete dosáhnout vytvořením akce zpětného volání rozhraní API a nastavením entity na hodnotu pomocí Správce paměti. Například `memory.Set(entityName, entityValue)`. Tento kód musí být zapsán a vytvořit tyto akce by se stal zdlouhavým a těžko se správou, takže Conversation Learner má zvláštní akce usnadňující tuto práci a automaticky generuje tyto akce při jejich použití. Jejich použití jako nezávislá akce zachovává možnost jejich vytváření bez párování s ostatními akcemi nebo kódem v robotu.

- Akce nastavit entity lze vytvořit pouze při odkazování na hodnotu entity Enum, takže je nutné nejprve vytvořit entitu Enum.
- Akce nastavení entit jsou také "nečekají", protože nemají žádný viditelný výstup a musí následovat po akci "počkat", kterou může uživatel zobrazit.
- Akce nastavení entit jsou neměnné, což znamená, že je po vytvoření nemůžete upravovat.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatické generování akcí

Pokud ve vašem modelu existuje entita výčtu, Conversation Learner vytvoří zástupné akce pro každou z možných hodnot a zpřístupní je k výběru během školení. Po výběru se akce automaticky vytvoří.

Například pokud vytvořím entitu enum s hodnotami "Ano" a "ne":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

I bez explicitního vytváření akcí pro tento nový výčet byste viděli dvě nové akce, které jsou k dispozici během školení:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Vytvoření robota s použitím těchto nových funkcí

### <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

Vytvoříme robota pro simulaci rychlého řazení potravin. Budou mít diskrétní hodnoty pro velikosti nápojů a Fries (malé a střední/velké) a dotazy na potvrzení s odpovědí Ano/ne. Obě tyto entity splní dvě pravidla uvedená v závislosti na kontextových odpovědích a pevné hodnoty.

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webovém uživatelském rozhraní klikněte na importovat.
2. Vyberte kurz s názvem kurz-výčet-set-entity.

Tím přejdete na stránku Správa modelů.
Všimněte si, že model již obsahuje několik entit výčtu a akce nastavení entit.

### <a name="create-the-first-dialog"></a>Vytvoření prvního dialogového okna

1. V levém navigačním panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Jako typ uživatele v, "Dobrý den" bych chtěl objednat koks a Fries prosím.
3. Klikněte na tlačítko "akce skóre".

   > Uživatel nezadal velikost pro nápoj nebo Fries, takže je musíme položit.

4. Vyberte akci s odpovědí: "Jakou velikost má nápoj?"
5. Jako typ uživatele v, Velká
6. Klikněte na tlačítko "akce skóre".
7. Vyberte akci SET_ENTITY-"drinkSize: CELOU
8. Vyberte akci s odpovědí: "Jakou velikost Fries chcete?"
9. Jako typ použití v se "um" nastaví jako střední.
10. Klikněte na tlačítko "akce skóre".
11. Vyberte akci SET_ENTITY-"friesSize: ÚROVNĚ
12. Vyberte akci s odpovědí: "Chcete mít nějaké koření?"
13. Jako typ použití v použijte Ano
14. Klikněte na tlačítko "akce skóre".
15. Vyberte akci SET_ENTITY-"condimentsConfirmation: ODPOVÍ
16. Vyberte akci s odpovědí: "OK, mám objednávku pro velké nápoje a střední friesy. Je to správné? "
17. Jako typ použití v použijte Ano
18. Klikněte na tlačítko "akce skóre".
19. Vyberte akci SET_ENTITY-"orderConfirmation: ODPOVÍ
20. Vyberte akci s odpovědí: "OK číslo vaší objednávky je 58. Počkejte prosím. "
21. Kliknutím na Uložit zavřete dialogové okno.

Právě jste vytvořili první dialog pomocí entit ENUM a akcí SET_ENTITY. Můžete vytvořit mnoho dalších kombinací uživatele, které určují částečné informace, nebo experimentovat s dalšími typy otázek s ukončenou platností.

> [!NOTE]
> Během školení se zobrazí zástupné symboly pro SET_ENTITY akce, jako je například
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> ale při vytváření dialogových oken protokolu nebo při použití nasazených uživatelů roboty se tyto neuvidí.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Alternativní vstupy](./10-alternative-inputs.md)
