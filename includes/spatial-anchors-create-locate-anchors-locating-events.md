---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "71180041"
---
Po vytvoření sledovacího procesu se `AnchorLocated` událost aktivuje pro všechny požadované kotvy. Tato událost je aktivována při umístění kotvy nebo v případě, že kotvu nelze nalézt. Pokud k této situaci dojde, bude důvod uveden ve stavu. Po zpracování, nalezení nebo nenalezení všech kotev sledovacího procesu se `LocateAnchorsCompleted` událost aktivuje. U každého sledovacího procesu je povolený limit 35 identifikátorů. 
