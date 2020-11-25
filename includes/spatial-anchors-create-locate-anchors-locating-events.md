---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993102"
---
Po vytvoření sledovacího procesu se `AnchorLocated` událost aktivuje pro všechny požadované kotvy. Tato událost je aktivována při umístění kotvy nebo v případě, že kotvu nelze nalézt. Pokud k této situaci dojde, bude důvod uveden ve stavu. Po zpracování, nalezení nebo nenalezení všech kotev sledovacího procesu se `LocateAnchorsCompleted` událost aktivuje. U každého sledovacího procesu je povolený limit 35 identifikátorů. 
