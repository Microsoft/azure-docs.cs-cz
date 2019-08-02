---
title: Jak používat Conversation Learner s dalšími technologiemi pro vytváření robotů – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat Conversation Learner s dalšími technologiemi pro vytváření robotů.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c964c62c34f952a547d077e93e7bb4d0eb7b192d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703673"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Jak používat Conversation Learner s dalšími technologiemi pro vytváření robotů

V tomto kurzu se dozvíte, jak používat Conversation Learner s dalšími technologiemi pro vytváření robotů a jak je možné sdílet paměť (nebo stav) mezi těmito technologiemi. 

## <a name="video"></a>Video

[![Kurz hybridního roboty ve verzi Preview](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje použití emulátoru bot k vytváření dialogových oken protokolu, nikoli webového uživatelského rozhraní dialogu protokolu. Další informace o nastavení emulátoru robot Framework je k dispozici [zde](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Tento kurz vyžaduje, aby robot hybridního kurzu běžel:

    npm run tutorial-hybrid

## <a name="details"></a>Podrobnosti

I když je ovládací prvek Conversation Learner, musí být všechny stavy relativní vzhledem k relaci Conversation Learner uloženy ve Správci paměti Conversation Learner. To je nezbytné, protože Machine Learning používá stav k určení toho, jak se má konverzace řídit. Externí stav lze předat do Conversation Learner v OnSessionStartCallback, který je volán při zahájení relace. Vnitřní stav může být vrácen OnSessionEndCallback při ukončení relace.

Můžete si skoro představit Conversation Learner jako volání funkce, které přebírá nějaký počáteční stav a vrací hodnoty.

V tomto příkladu vytvoříte hybridní robot pomocí dvou různých systémů:
1. Conversation Learner model <br/>
    K určení další akce robota v závislosti na aktuální relaci používá model Naučte se v konverzaci. Tato část robota používá jednu část počátečního stavu `isOpen` (která označuje, jestli je úložiště otevřené nebo uzavřené), a vrátí další stav `purchaseItem` (název položky, kterou uživatel zakoupí).

2. Shoda textu <br />
    Jednoduše prohledá příchozí text pro konkrétní řetězce a odpoví. Tato část robota spravuje jiné mechanismy úložiště roboty a zodpovídá za spuštění relace CL. Konkrétně spravuje tři proměnné: `usingConversationLearner`, `storeIsOpen` `purchaseItem`a.

Pojďme se podívat na model použitý v této ukázce.

### <a name="open-the-demo"></a>Otevřete ukázku.

Ve webovém uživatelském rozhraní klikněte na "Import kurzů" a vyberte model s názvem "tutorial-16-HybridBot".

## <a name="entities"></a>Entity

Otevřete stránku entity a Všimněte si dvou entit: `isOpen` a`purchaseItem`

Chcete-li pochopit, jak jsou tyto entity použity, otevřete `C:\<installedpath>\src\demos\tutorialHybrid.ts` soubor: Chcete-li se podívat na zpětná volání.

Všimněte si, že kód `OnSessionStartCallback` v části kopíruje `storeIsOpen` hodnotu z úložiště konverzace BotBuilder `isOpen` jako hodnotu entity, takže je k dispozici pro Conversation Learner. Podívejte se na následující kód:

![](../media/tutorial17_sessionstart.PNG)

Podobně, kód v `OnSessionEndCallback` (Pokud byla relace ukončena z důvodu zjištěné aktivity a nikoli jenom s časovým limitem), kopíruje hodnotu entity `purchaseItem` na BotBuilder Storage. `purchaseItem` Podívejte se na následující kód:

![](../media/tutorial17_sessionend.PNG)

Teď se podíváme na akce.

## <a name="actions"></a>Akce

Všimněte si, že model obsahuje čtyři akce.

Zamýšlená pravidla pro akce jsou následující:

- Pokud je `isOpen` entita nastavená, robot si vyzve "co byste chtěli koupit?" a uložte ho do `puchaseItem` slotu.
- Pokud `isOpen` není nastavená, robot se zaznamená, že jsme se zavřeli.
- Další dvě akce jsou typu `END_SESSION`.
- Akce END_SESSION označuje ConversationLearner, že se konverzace dokončila.

### <a name="overall-bot-logic"></a>Celková logika robota

Nejprve vidíte, že pokud byl `usingConversationLearner` příznak stavu robota nastaven, předá řízení Conversation Learner. V opačném případě předejte řízení na něco jiného.  V tomto příkladu se zobrazuje jednoduché porovnání textu, ale může to být jakákoli jiná technologie robotů, včetně LUIS, QnA maker a jiné instance Conversation Learner.

Potřebujeme, aby uživatel otevřel a zavřel úložiště, a proto porovnáme řetězec s "otevřeným" a "zavřením úložištěm" a nastavili jste příznak "storeIsOpen".

Dále potřebujeme způsob, jak aktivovat řízení pro náš Conversation Learner model. Když se porovnáváme s řetězcem "koupit", provedeme následující:
- `usingConversationLearner` Nastavte příznak v paměti robota.
- V našem modelu Conversation Learner volejte metodu "StartSession".  Tím se aktivuje "onSessionStartCallback", která inicializuje `isOpen` hodnotu entity

Viz níže:

![](../media/tutorial17_useConversationLearner.PNG)

Také se shoduje text s historií, která zobrazí poslední položku nákupu.
Nakonec, pokud je zadán cokoli jiného, zobrazíme dostupné uživatelské příkazy.

## <a name="train-dialog"></a>Dialogové okno výuka

Pro tento kurz je model již předem vyškolen.  Vyzkoušíme celý robot, aby se zobrazil účinek zpětných volání počátečního a koncového relace v praxi.

## <a name="testing-the-bot"></a>Testování robota

Na rozdíl od roboty modelu s jedním konverzacím nebudete schopni testovat tuto možnost v uživatelském rozhraní Conversation Learner, protože může zobrazit pouze to, co je zpracováváno modelem Conversation Learner.

### <a name="install-the-bot-framework-emulator"></a>Instalace emulátoru rozhraní bot Framework

- Přejděte do [ (Nastavení)https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Integrace a služby).
- Stáhněte a nainstalujte emulátor.

### <a name="configure-the-emulator"></a>Konfigurace emulátoru

- Otevřete emulátor a ujistěte se, že adresa URL cílí na stejný port, na kterém je robot spuštěný. Pracuje`http://localhost:3978/api/messages`

### <a name="test"></a>Test 

#### <a name="scenario-1-store-is-closed"></a>Scénář 1: Úložiště je zavřené.
1. Zadejte ' koupit '. Tato akce je zpracována v porovnání s textem a udělí řízení Conversation Learner modelu.
2. Zadejte text Hello.  Vzhledem `isOpen` k tomu, že hodnota není nastavená, robot bude znám "" jsem líto, ale ukončil (a) ukončit relaci.

#### <a name="scenario-2-store-is-open"></a>Scénář 2: Úložiště je otevřené.
1. Zadejte Open Store.  Tím se nastaví `isOpen` hodnota true.
1. Zadejte ' koupit '.
1. Zadejte text Hello.  Vzhledem `isOpen` k tomu, že hodnota je nastavená na true, robot bude vyslovit "co byste chtěli koupit?"
1. Zadejte "židle". "židle" se uloží do paměti CL jako entita `purchaseItem`. Volání koncové relace je vyvoláno, které zkopíruje tuto hodnotu do obchodu konverzace.
1. Zadejte ' historie '.  Robot bude říct, že jste jeho koupili jako poslední `purchaseItem`.

## <a name="conclusion"></a>Závěr

Čím výše jste se naučili, měli byste být schopni kombinovat Conversation Learner s dalšími technologiemi pro vytváření robotů.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Větvení a vrácení zpět](./17-branch-undo.md)
