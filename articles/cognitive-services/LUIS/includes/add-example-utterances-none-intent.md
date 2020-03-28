---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279717"
---
Klientská aplikace potřebuje vědět, pokud utterance není smysluplné nebo vhodné pro aplikaci. **Žádný** záměr je přidán do každé aplikace jako součást procesu vytváření k určení, pokud utterance by neměla být zodpovězena klientskou aplikací.

Pokud služba LUIS vrátí **žádný** záměr pro utterance, klientská aplikace se může zeptat, pokud uživatel chce ukončit konverzaci nebo poskytnout další pokyny pro pokračování konverzace.

Pokud ponecháte **žádný** záměr prázdný, utterance, které by měly být předpovězeny mimo doménu předmětu bude předpovězena v jednom z existujících záměrů domény předmětu. Výsledkem je, že klientská aplikace, jako je například chatovací robot, bude provádět nesprávné operace na základě nesprávné předpovědi.

1. Na levém panelu vyberte **Intents** (Záměry).

1. Vyberte záměr **None** (Žádný). Přidejte tři projevy, které může uživatel zadat, ale nejsou relevantní pro vaši aplikaci pro objednávání pizzy:

    |`None`příklad projevy|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Tyto příklady by neměly používat slova, která `pizza` `cheese`očekáváte v doméně subjektu, například , , `crust`. `pickup` `deliver`