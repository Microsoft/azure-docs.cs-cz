---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994485"
---
Adresa URL, která se používá k upozornění na zákazníka (pomocí žádosti POST) o následujících událostech:

- Změna stavu indexování: 
    - Vlastnosti:    
    
        |Název|Description|
        |---|---|
        |id|ID videa|
        |state|Stav videa|  
    - Příklad: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&State = zpracovaná
- Osoba identifikovaná ve videu:
  - Vlastnosti
    
      |Název|Description|
      |---|---|
      |id| ID videa|
      |faceId|ID obličeje, které se zobrazí v indexu videa|
      |knownPersonId|Jedinečné ID osoby v rámci modelu obličeje|
      |personName|Jméno osoby|
        
    - Příklad: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&FaceID = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&person = Inigo_Montoya 
