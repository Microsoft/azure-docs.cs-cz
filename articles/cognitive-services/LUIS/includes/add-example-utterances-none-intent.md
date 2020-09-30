---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.openlocfilehash: 1509d175979bb65c467424db5de967f56825a3f9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545466"
---
Klientská aplikace musí znát, jestli utterance není smysluplná nebo vhodná pro aplikaci. Záměr **none** se do každé aplikace přidá jako součást procesu vytváření, aby se zjistilo, jestli klientská aplikace nemůže odpovědět na utterance.

Pokud LUIS vrátí záměr **none** pro utterance, může klientská aplikace požádat, jestli chce uživatel ukončit konverzaci, nebo dát další pokyny pro pokračování v konverzaci.

Pokud necháte žádný záměr **none** prázdný, utterance, který by měl být předpovězen mimo doménu subjektu, bude předpovězen v jedné z existujících záměrů domény předmětu. Výsledkem je, že klientská aplikace, jako je například robota v chatu, provede nesprávné operace na základě nesprávné předpovědi.

1. Na levém panelu vyberte **Intents** (Záměry).

1. Vyberte záměr **None** (Žádný). Přidejte tři projevy, které může uživatel zadat, ale nesouvisejí s vaší aplikací pro objednávání pizza:

    |`None` Příklad projevy|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Tyto příklady nemůžou používat slova, která očekáváte v doméně předmětu `pizza` , například, `cheese` , `crust` , `pickup` `deliver` .