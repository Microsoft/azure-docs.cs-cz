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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123773"
---
Klientská aplikace potřebuje vědět, pokud není utterance smysluplné nebo vhodný pro aplikaci. **Žádný** záměr je přidán do každé aplikaci jako součást procesu vytváření a zjistěte, pokud nejde utterance zodpoví klientská aplikace.

Služba LUIS vrátí-li **žádný** záměru pro utterance, klientské aplikace můžete požádat o Pokud chce uživatel ukončení konverzace nebo poskytnout další pokyny pro budete pokračovat v konverzaci. 

> [!CAUTION] 
> Nesmí zůstat **žádný** záměr prázdný. 

1. Na levém panelu vyberte **Intents** (Záměry).

2. Vyberte záměr **None** (Žádný). Přidejte tři projevy, že uživatel může zadat, ale nejsou relevantní pro vaši aplikaci lidských zdrojů:

    | Ukázkové promluvy|
    |--|
    |Barking dogs are annoying (Štěkající psi jsou otravní)|
    |Order a pizza for me (Objednej mi pizzu)|
    |Penguins in the ocean (Tučňáci v oceánech)|
