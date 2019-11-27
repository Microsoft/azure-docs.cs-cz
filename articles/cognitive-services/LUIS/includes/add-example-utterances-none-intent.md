---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325918"
---
Klientská aplikace musí znát, jestli utterance není smysluplná nebo vhodná pro aplikaci. Záměr **none** se do každé aplikace přidá jako součást procesu vytváření, aby se zjistilo, jestli klientská aplikace nemůže odpovědět na utterance.

Pokud LUIS vrátí záměr **none** pro utterance, může klientská aplikace požádat, jestli chce uživatel ukončit konverzaci, nebo dát další pokyny pro pokračování v konverzaci. 

Pokud necháte žádný záměr **none** prázdný, utterance, který by měl být předpovězen mimo doménu subjektu, bude předpovězen v jedné z existujících záměrů domény předmětu. Výsledkem je, že klientská aplikace, jako je například robota v chatu, provede nesprávné operace na základě nesprávné předpovědi. 

1. Na levém panelu vyberte **Intents** (Záměry).

1. Vyberte záměr **None** (Žádný). Přidejte tři projevy, které může uživatel zadat, ale nesouvisejí s vaší aplikací pro objednávání pizza:

    |`None` příklad projevy|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    Tyto příklady nemůžou používat slova, která očekáváte v doméně předmětu, jako je `pizza`, `cheese`, `crust``pickup` `deliver`.