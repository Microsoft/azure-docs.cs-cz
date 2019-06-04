---
title: Kdy použít výčet entit a akce nastavit ENTITY s modelem Learner konverzace - služeb Azure Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, kdy je vhodné používat výčet entity a akce nastavit ENTITY s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1393d0bd1c31a2c9c24652e260ef7f3182d91367
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476491"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>Kdy použít výčet entit a nastavit ENTITY akcí

Tento kurz vysvětluje, kdy byste měli použít výčet (výčtu) entity a SET_ENTITY akce.

## <a name="video"></a>Video

[![Sada entit kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Probíraná témata

Tento kurz vás seznámí dvě nové funkce. Nový typ entity s názvem VÝČTU (zkratka pro výčet) a SET_ENTITY, která může odkazovat na jeden z těchto hodnot výčtu a jak již název napovídá, nastaví entitu na tuto hodnotu s názvem nového typu akce. Dozvíte se, níže, tyto nové funkce se používají společně a vysvětlíme, co představují a jak se dají použít následující. Než jsme se ponoříte do detailů, je důležité pochopit, jaký problém vyřešit tyto funkce.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problém

Existují případy v konverzacích, kde význam slova závisí na kontextu.  Obvykle s popiskem klíčová slova jsou zjistili a extrahovat pomocí služby language understanding, ale v těchto případech nemusí být možné další s popiskem příklady použití těchto systémech.

Představte si overhear součástí konverzaci mezi lidmi poblíž a uslyšíte slovo "Ano". Byste vlastně nevěděli o "Ano" vstupem do nebo potvrzení od vás žádnou odpověď na otázku požádáni před ní. Otázka před je kontext, který poskytuje význam k odpovědi. Podobně od "Ano" je běžné odpovědí na mnoha různých dotazy se tím, že poskytuje příklady, jako byste to udělali s [vlastní Trénovaného](04-introduction-to-entities.md) entity vzhledem k tomu, pak se by se naučíte, jak označit každý "Ano" jako entity.

### <a name="example"></a>Příklad:

Pojďme se dále upřesnit v následujícím příkladu:

Bot: Například Azure Cognitive Services?
Uživatel: Ano Bot: Jako ice cream?
Uživatel: Ano

V předchozích kurzech jsme se podívali na [vlastní Trénovaného](04-introduction-to-entities.md) entit a počáteční tím může být vytvoření entity s názvem "likesCogServices" a označovat první "Ano" jako tuto entitu.  Nicméně systém by také popisek druhý "Ano". Když jsme se pokusili o opravte popisek z druhého "Ano" k "likesIceCream", vytvoříme by pak ke konfliktu dvou vstupů stejné "Ano" význam různé věci a by zablokované.

Je v těchto případech, které je třeba použít výčet entit a SET_ENTITY akce.

## <a name="when-to-use-enums-or-setentity-actions"></a>Kdy použít výčty nebo SET_ENTITY akce

Pomocí těchto pravidel níže vědět, kdy použít výčet entit a SET_ENTITY akce:

- Nastavení entity nebo zjišťování je závislá na kontextu
- Počet možných hodnot vyřešen (Ano a ne by dvě hodnoty)

Jinými slovy je použijte pro zobrazování výzev skončila zavřít například potvrzení dotazy, které vždy způsobit Yes nebo No.

> [!NOTE]
> Aktuálně máme omezení hodnot až 5 na výčtu entity. Každá hodnota používá jeden ze slotů aktuální limit 64. Zobrazit [cl hodnoty a hranice](../cl-values-and-boundaries.md)

Příklad: Bot: Vaše objednávka správnost?
Uživatel: Ano

Když jsou možné hodnoty entity otevřený a neopraveno, potřebujete použít alternativní funkce, jako [očekává entity](05-expected-entity.md).

Příklad: Bot: Jak se jmenuješ?
Uživatel: Matt Bot: Co je Oblíbené barvu?
Uživatel: Stříbrný

Tyto výzvy jsou považovány za otevřený, protože by mohly být zodpovězeny libovolné hodnoty.

## <a name="what"></a>Co

### <a name="enum-entities"></a>VÝČET entity

VÝČET entity jsou vytvořeny stejně jako jiné entity. Podobně jako u "programový" entit, slova nelze označit jako takové instituci. Místo toho je nutné je nastavit prostřednictvím kódu nebo SET_ENTITY akce.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Nastavení akce Entity

Jak je uvedeno výše, akce "Nastavit Entity" entity jednoduše nastavit na hodnotu výčtu známé. Vytvoření akce zpětného volání rozhraní API a nastavit entity na hodnotu pomocí Správce paměti může dosáhnete stejných výsledků. Například `memory.Set(entityName, entityValue)`. Tento kód napsat a vytvořit tyto akce by se mohla stát únavné a náročná na správu – tak konverzace Learner má zvláštní akce, které usnadňují tuto práci a automaticky vygenerovat tyto akce, když se používají. Možnost vytvořit tyto bez se spolu s ostatními akce nebo kód svého robota jako nezávislé akce s zachová.

- Akce entity sady lze vytvořit pouze k odkazování na hodnotu výčtu entity, proto je nutné nejprve vytvořit entitu výčtu.
- Sada entit akce jsou také "bez await" protože mít žádné viditelné výstup a musí následovat "Počkejte" akce, které vidí uživatel.
- Sada entit akce jsou neměnný, což znamená, že nejde upravovat po jeho vytvoření.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Generování automatických akcí

Pokud výčtu entity v modelu existuje, konverzace Learner vytvoříte zástupný symbol akcí jednotlivých možných hodnot a zpřístupnit je vybrat během cvičení. Při výběru bude akce automaticky vytvoří za vás.

Pokud vytvořím entity výčet s hodnotami "Ano" nebo "Ne":

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

I bez explicitního vytváření akce pro tento nový výčet uvidíte dvě nové akce, které jsou k dispozici během cvičení:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Vytváření robotů pomocí těchto nových funkcí

### <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

Vytvoříme robota k simulaci rychlého občerstvení řazení. Bude mít diskrétní hodnoty pro velikostí nápojů a fries (malé nebo střední nebo velké) a potvrzení otázky, které se Ano / žádné odpovědi. Oba z těchto entit vyhovovat dvě pravidla nad odpovědi závislé na kontextu a pevné hodnoty.

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webovém uživatelském rozhraní klikněte na "Import"
2. Vyberte tento kurz s názvem "Kurzu výčtu-sady-Entity"

Tím přejdete na stránku pro správu modelu.
Všimněte si, že model již obsahuje několik entit výčtu a nastaví akce entity.

### <a name="create-the-first-dialog"></a>Vytvoření prvního dialogového okna

1. V levém navigačním panelu klikněte na tlačítko "Dialogů Train" a pak klikněte na tlačítko "Dialogové okno Nový Train".
2. Jako typ uživatele v "Dobrý den, chci pořadí koksu a fries prosím".
3. Klikněte na tlačítko "Skóre akce"

   > Uživatel nebyl zadán velikosti nápoje nebo fries, takže potřebujeme žádostí.

4. Výběr akce s odpovědí: "Jaké velikosti nápoje způsobem?"
5. Jako typ uživatele v "velké (large)
6. Klikněte na tlačítko "Skóre akce"
7. Vyberte akci SET_ENTITY – "drinkSize: VELKÉ (LARGE)
8. Výběr akce s odpovědí: "Jak velký fries přejete?"
9. Jako typ použití v "Um, ujistěte se, ty střední.
10. Klikněte na tlačítko "Skóre akce"
11. Vyberte akci SET_ENTITY – "friesSize: STŘEDNÍ"
12. Výběr akce s odpovědí: "Chcete všechny produkty koření?"
13. Jako typ použití v "Ano"
14. Klikněte na tlačítko "Skóre akce"
15. Vyberte akci SET_ENTITY – "condimentsConfirmation: ANO,
16. Výběr akce s odpovědí: "Ok, mám objednávky pro velké nápoje a střední fries. Je to správné?"
17. Jako typ použití v "Ano"
18. Klikněte na tlačítko "Skóre akce"
19. Vyberte akci SET_ENTITY – "orderConfirmation: ANO,
20. Výběr akce s odpovědí: "Ok, pořadové číslo je 58. Počkejte prosím tam."
21. Klikněte na tlačítko "Save (Uložit) zavřete dialogové okno

Právě jste vytvořili první dialogové okno pomocí VÝČTU entit a SET_ENTITY akce. Můžete vytvořit další kombinace uživatelského zadání částečné informace nebo experimentování s jinými typy skončila zavřít dotazy.

> [!NOTE]
> Během cvičení se zobrazí zástupné symboly pro SET_ENTITY akce, jako
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> ale při vytváření protokolu dialogová okna nebo pomocí nasazení robotů uživatelé neuvidí tyto.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Alternativní vstupy](./10-alternative-inputs.md)
