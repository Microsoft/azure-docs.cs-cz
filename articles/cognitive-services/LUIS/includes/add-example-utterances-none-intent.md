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
ms.openlocfilehash: c81aaa02af0895e6c3a851fe15273d85de9b55bc
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262740"
---
Klientská aplikace musí znát, jestli utterance není smysluplná nebo vhodná pro aplikaci. Záměr **none** se do každé aplikace přidá jako součást procesu vytváření, aby se zjistilo, jestli klientská aplikace nemůže odpovědět na utterance.

Pokud LUIS vrátí záměr **none** pro utterance, může klientská aplikace požádat, jestli chce uživatel ukončit konverzaci, nebo dát další pokyny pro pokračování v konverzaci. 

Pokud necháte žádný záměr **none** prázdný, utterance, který by měl být předpovězen mimo doménu subjektu, bude předpovězen v jedné z existujících záměrů domény předmětu. Výsledkem je, že klientská aplikace, jako je například robota v chatu, provede nesprávné operace na základě nesprávné předpovědi. 

1. Na levém panelu vyberte **Intents** (Záměry).

1. Vyberte záměr **None** (Žádný). Přidejte tři projevy, které může uživatel zadat, ale nesouvisejí s vaší aplikací pro objednávání pizza:

    |`None` příklad projevy|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Tyto příklady nemůžou používat slova, která očekáváte v doméně předmětu, jako je `pizza`, `cheese`, `crust``pickup` `deliver`.