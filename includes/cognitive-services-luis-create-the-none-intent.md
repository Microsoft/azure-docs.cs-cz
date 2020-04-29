---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175315"
---
Klientská aplikace musí znát, jestli utterance není smysluplná nebo vhodná pro aplikaci. V rámci procesu vytváření se do každé aplikace přidá záměr **none** , který určí, jestli klientská aplikace nemůže na utterance odpovědět.

Pokud LUIS vrátí záměr **none** pro utterance, může klientská aplikace požádat, jestli chce uživatel ukončit konverzaci, nebo dát další pokyny pro pokračování v konverzaci. 

> [!CAUTION] 
> Nenechávejte záměr **none** prázdný. 

1. Na levém panelu vyberte **Intents** (Záměry).

2. Vyberte záměr **None** (Žádný). Přidejte tři projevy, které uživatel může zadat, ale není relevantní pro vaši aplikaci lidských zdrojů:

    | Ukázkové promluvy|
    |--|
    |Barking dogs are annoying (Štěkající psi jsou otravní)|
    |Order a pizza for me (Objednej mi pizzu)|
    |Penguins in the ocean (Tučňáci v oceánech)|
