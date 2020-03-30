---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541800"
---
1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com).

1. Upgradujte svůj pracovní prostor na edici Enterprise.

    Po upgradu se všechny experimenty s vizuálním rozhraním převedou na koncepty kanálu v návrháři.
    
    > [!NOTE]
    > K převodu webových služeb vizuálního rozhraní na koncové body v reálném čase není nutné upgradovat na edici Enterprise.
    
1. Přejděte do části návrháře pracovního prostoru a zobrazte seznam konceptů kanálu. 
    
    Převedené webové služby lze nalézt na přechodu na **koncové body koncových bodů** > **v reálném čase**.

1. Vyberte pracovní verzi kanálu, která ji otevře.

    Pokud během procesu převodu došlo k chybě, zobrazí se chybová zpráva s pokyny k vyřešení problému. 

### <a name="known-issues"></a>Známé problémy

 Níže jsou uvedeny známé problémy s migrací, které je třeba řešit ručně:

- **Moduly Import dat** nebo **Export dat**
        
    Pokud máte v experimentu modul **Import dat** nebo **Export dat,** je třeba aktualizovat zdroj dat tak, aby používal úložiště dat. Informace o tom, jak vytvořit úložiště dat, najdete v tématu [Jak přistupovat k datům ve službách úložiště Azure](../articles/machine-learning/how-to-access-data.md). Informace o účtu cloudového úložiště byly přidány do komentářů modulu **Import dat** nebo **Export dat** pro vaše pohodlí. 
      