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
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648297"
---
Klientská aplikace musí znát, jestli utterance není smysluplná nebo vhodná pro aplikaci. V rámci procesu vytváření se do každé aplikace přidá záměr **none** , který určí, jestli klientská aplikace nemůže na utterance odpovědět.

Pokud LUIS vrátí záměr **none** pro utterance, může klientská aplikace požádat, jestli chce uživatel ukončit konverzaci, nebo dát další pokyny pro pokračování v konverzaci. 

Pokud necháte žádný záměr **none** prázdný, utterance, který by měl být předpovězen mimo doménu subjektu, bude předpovězen v jedné z existujících záměrů domény předmětu. Výsledkem je, že klientská aplikace, jako je například robota v chatu, provede nesprávné operace na základě nesprávné předpovědi. 

1. Na levém panelu vyberte **Intents** (Záměry).

1. Vyberte záměr **None** (Žádný). Přidejte tři promluvy, které může uživatel zadat, ale které nejsou pro aplikaci relevantní:

    |`None` příklad projevy|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
