---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180041"
---
Po vytvoření `AnchorLocated` sledovacího procesu se událost aktivuje pro všechny požadované kotvy. Tato událost je aktivována při umístění kotvy nebo v případě, že kotvu nelze nalézt. Pokud k této situaci dojde, bude důvod uveden ve stavu. Po zpracování, nalezení nebo nenalezení `LocateAnchorsCompleted` všech kotev sledovacího procesu se událost aktivuje. U každého sledovacího procesu je povolený limit 35 identifikátorů. 
