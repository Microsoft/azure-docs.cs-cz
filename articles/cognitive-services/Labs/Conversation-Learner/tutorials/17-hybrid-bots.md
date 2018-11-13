---
title: Jak používat Learner konverzace s další robotem vytváření technologií – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití Learner konverzace s další robotem vytváření technologie.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: 82b0d1494e983d033889d588bcb73d0364fcfc89
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256285"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Jak používat Learner konverzace s další robotem vytváření technologie

Tento kurz se zaměřuje na použití Learner konverzace s další bot vytváření technologií a jak paměti (nebo stav) je možné sdílet mezi tyto technologie 

## <a name="video"></a>Video

[![Kurz 15 Preview](https://aka.ms/cl-tutorial-15-preview)](https://aka.ms/blis-tutorial-15)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje použití emulátoru bot k vytvoření protokolu dialogová okna, ne protokolu dialogové okno webového uživatelského rozhraní.  

Tento kurz vyžaduje, že hybridní kurz bot běží:

    npm run tutorial-hybrid

## <a name="details"></a>Podrobnosti

Learner konverzace je v ovládacím prvku, všechny stavy vzhledem k relaci konverzace Learner musí být uložen ve Správci paměti Learner konverzace. To je nezbytné, machine learning používá stav a zjistěte, jak Centrum umožňující prosazovat konverzace. Externí stavu mohou být předány do konverzace Learner v OnSessionStartCallback, která je volána po zahájení relace. Interní může být vrácen out OnSessionEndCallback při ukončení relace.

Konverzace Learner můžete téměř představit jako volání funkce, která přebírá některé počáteční stav a vrací hodnoty.

V tomto příkladu vytvoříte hybridní robota pomocí dvou různých systémech:
1. Model Learner konverzace <br />
Konverzace learner modelu používá k určení akce dalšího robota podle aktuální relaci.
Tato část robota přijímá jednu část počáteční stav `isOpen` (který určuje, zda úložiště je otevřeno nebo Uzavřeno) a vrátí jinou část stavu `purchaseItem` (název položky uživatel koupí.)

2. Odpovídající text <br />
Jednoduše prohledá příchozí text pro konkrétní řetězce a odpovídá.
Tato část robota spravuje roboty jiné mechanismy úložiště a je zodpovědný za spouštění relace CL. Konkrétně spravuje tří proměnných: `usingConversationLearner`, `storeIsOpen`, a `purchaseItem`.

Začněme tím, že a podívejte se na model používaný v této ukázce.

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na kurz-15hybridní.

## <a name="entities"></a>Entity

Otevřete stránku entity a Všimněte si, že dvě entity: `isOpen` a `purchaseItem`

Chcete-li pochopit, jak se používají tyto entity, otevřete soubor: `C:\<installedpath\>\src\demos\tutorialHybrid.ts` se podívat zpětná volání.

Všimněte si, že kód v `OnSessionStartCallback` kopíruje hodnotu `storeIsOpen` ze služby storage konverzace BotBuilder jako hodnotu `isOpen` entity tak, aby byl k dispozici Learner konverzace. Viz následující kód:

![](../media/tutorial17_sessionstart.PNG)

Obdobně kód v `OnSessionEndCallback` (je-li relace bylo ukončeno z důvodu zjištěná aktivity a nikoli pouze vypršení časového limitu) zkopírujete příslušnou hodnotu entity `purchaseItem` out do úložiště BotBuilder `purchaseItem`. Viz následující kód:

![](../media/tutorial17_sessionend.PNG)

Nyní Pojďme se podívat na akce.

## <a name="actions"></a>Akce

Všimněte si, že model má čtyři akce.

Odpovídající pravidla pro akce, které jsou následující:

- Pokud `isOpen` sada entit, požádá robota "Co chcete koupit?" a uložit v `puchaseItem` slot
- Pokud `isOpen` není nastaven, bude robot sdělení "Jsem je nám líto, můžeme mít zavřeno"
- Jsou dvě akce typu `END_SESSION`.
- Akce END_SESSION označuje ConversationLearner, konverzace byla dokončena.

### <a name="overall-bot-logic"></a>Celkové logiky robota

Nejprve se zobrazí, že pokud státu Bot `usingConversationLearner` byl nastaven příznak, jsme předá řízení Learner konverzace. V opačném případě předáme ovládacího prvku na něco jiného.  V tomto příkladu zobrazujeme odpovídající prostého textu, ale může to být jiná technologie robota, LUIS, včetně nástroje QnA maker a dokonce i jiná instance Learner konverzace.

Potřebujeme způsob, jak uživatele k otevření a zavření úložiště, takže provedeme řetězec porovnání s "Otevřít úložiště" a "Zavřít" a nastavte příznak "storeIsOpen".

V dalším kroku budeme potřebovat způsob, jak aktivovat předání řízení prostřednictvím našeho modelu Learner konverzace. Když budeme odpovídat na řetězec ", kde jsme postupujte takto:
- Nastavte `usingConversationLearner` příznak v bodu robotů také paměti.
- Volání metody "StartSession" na náš Model Learner konverzace.  Tím se aktivuje "onSessionStartCallback" který inicializuje `isOpen` hodnota entity

Níže jsou:

![](../media/tutorial17_useConversationLearner.PNG)

Provedeme také text odpovídá "Dějepis", který se zobrazí tento poslední položky nákupní.
Nakonec pokud se zadá cokoli jiného, zobrazíme dostupné uživatelských příkazů

## <a name="train-dialog"></a>Dialogové okno trénování

Pro účely tohoto kurzu je model už předem vytrénovaných.  Otestujeme, úplné bot posoudit účinek parametru spuštění a ukončení relace zpětná volání v praxi.

## <a name="testing-the-bot"></a>Testování robota

Na rozdíl od jednoho modelu robotů konverzace Štíhlejší nebude možné otestovat v uživatelském rozhraní Learner konverzace jako můžete jenom zobrazit, co se zpracovává souborem modelu Learner konverzace.

### <a name="install-the-bot-framework-emulator"></a>Nainstalujte si emulátor Bot framework

- Přejděte do [ (Nastavení)https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) (Integrace a služby).
- Stáhněte si a nainstalujte si emulátor.

### <a name="configure-the-emulator"></a>Konfigurace v emulátoru

- Otevřete emulátor a ujistěte se, že adresa URL je cílen na verzi, po který váš robot běží na stejný port. Pravděpodobně: `http://localhost:3978/api/messages`

### <a name="test"></a>Test 

#### <a name="scenario-1-store-is-closed"></a>Scénář 1: Store je zavřený.
1. Zadejte "kde". To se zpracovává souborem odpovídající text a poskytnou kontrolu na model Learner konverzace.
2. Zadejte "hello".  Protože `isOpen` hodnota není nastavená, bude robot sdělení "Jsem je nám líto, jsme už uzavřený" a ukončení relace.

#### <a name="scenario-2-store-is-open"></a>Scénář 2: Store je otevřený
3. Zadejte "Otevřít úložiště".  Tato možnost nastaví `isOpen` na hodnotu true.
4. Zadejte "kde".
5. Zadejte "hello".  Protože `isOpen` je hodnota nastavena na hodnotu true, robota bude sdělení "Co chcete koupit?"
6. Zadejte "předsedat". "židle" se uloží do CL paměti jako entita `purchaseItem`. Zpětné volání end relace je vyvolán která kopíruje tuto hodnotu úložiště konverzace.
7. Zadejte "historie".  Robot bude sdělení "Jste zakoupili židle" jako to byla vaše poslední `purchaseItem`.

## <a name="conclusion"></a>Závěr

Co jste se naučili výše je třeba možné kombinovat Learner konverzace s jinou technologii robota
