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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175315"
---
Klientská aplikace potřebuje vědět, pokud utterance není smysluplné nebo vhodné pro aplikaci. **Žádný** záměr je přidán do každé aplikace jako součást procesu vytváření k určení, pokud utterance nelze odpovědět klientské aplikace.

Pokud služba LUIS vrátí **žádný** záměr pro utterance, klientská aplikace se může zeptat, pokud uživatel chce ukončit konverzaci nebo poskytnout další pokyny pro pokračování konverzace. 

> [!CAUTION] 
> Nenechávejte **žádný** záměr prázdný. 

1. Na levém panelu vyberte **Intents** (Záměry).

2. Vyberte záměr **None** (Žádný). Přidejte tři projevy, které může uživatel zadat, ale nejsou relevantní pro vaši aplikaci Lidské zdroje:

    | Ukázkové promluvy|
    |--|
    |Barking dogs are annoying (Štěkající psi jsou otravní)|
    |Order a pizza for me (Objednej mi pizzu)|
    |Penguins in the ocean (Tučňáci v oceánech)|
