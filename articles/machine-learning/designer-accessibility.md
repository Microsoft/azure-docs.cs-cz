---
title: Použití funkcí usnadnění v návrháři (náhled)
titleSuffix: Azure Machine Learning
description: Přečtěte si o klávesových zkratkách a funkcích usnadnění čtečky obrazovky dostupných v návrháři.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366195"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Použití návrháře Azure Machine Learning pomocí klávesnice (preview)

Naučte se používat klávesnici a čtečku obrazovky k použití návrháře Azure Machine Learning. Seznam klávesových zkratek, které fungují všude na webu Azure Portal, najdete [v tématu Klávesové zkratky na webu Azure Portal.](../azure-portal/azure-portal-keyboard-shortcuts.md)

Tento pracovní postup byl testován pomocí [předčítání](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) a [jaws](https://www.freedomscientific.com/products/software/jaws/), ale měl by fungovat s jinými standardními programy pro čtení z obrazovky.

## <a name="navigate-the-pipeline-graph"></a>Navigace v grafu kanálu

Graf potrubí je uspořádán jako vnořený seznam. Vnější seznam je seznam modulů, který popisuje všechny moduly v grafu potrubí. Vnitřní seznam je seznam připojení, který popisuje všechna připojení určitého modulu.  

1. V seznamu modulů přepínejte pomocí klávesy se šipkou.
1. Karta slouží k otevření seznamu připojení pro cílový modul.
1. Pomocí klávesy se šipkou můžete přepínat mezi porty připojení modulu.
1. Použijte "G" pro přejděte do cílového modulu.

## <a name="edit-the-pipeline-graph"></a>Úprava grafu potrubí

### <a name="add-a-module-to-the-graph"></a>Přidání modulu do grafu

1. Pomocí kombinace kláves Ctrl+F6 můžete přepnout fokus z plátna do stromu modulu.
1. Najděte požadovaný modul ve stromu modulu pomocí standardního ovládacího prvku treeview.

### <a name="edit-a-module"></a>Úprava modulu

Připojení modulu k jinému modulu:

1. Při cílení na modul v seznamu modulů použijte kombinaci kláves Ctrl + Shift + H k otevření pomocníka pro připojení.
1. Upravte porty připojení modulu.

Nastavení vlastností modulu:

1. Při cílení na modul použijte kombinaci kláves Ctrl + Shift + E k otevření vlastností modulu.
1. Upravte vlastnosti modulu.

## <a name="navigation-shortcuts"></a>Navigační zkratky

| Stisknutí klávesy | Popis |
|-|-|
| Ctrl+F6 | Přepínat fokus mezi plátnem a stromem modulu |
| Ctrl + F1   | Otevření informační karty při zaostřování na uzel ve stromu modulů |
| Ctrl + Shift + H | Otevření pomocné hodu připojení při fokusu na uzlu |
| Ctrl + Shift + E | Otevření vlastností modulu při zaostření na uzlu |
| Ctrl + G | Přesunutí fokusu na první neúspěšný uzel, pokud se spuštění kanálu nezdařilo |

## <a name="action-shortcuts"></a>Zkratky akcí

Pomocí přístupové klávesy použijte následující klávesy. Další informace o přístupových https://en.wikipedia.org/wiki/Access_keyklíčích naleznete v tématu .

| Stisknutí klávesy | Akce |
|-|-|
| Přístupový klíč + R | Spusťte |
| Přístupový klíč + P | Publikování |
| Přístupový klíč + C | Klonování |
| Přístupový klíč + D | Nasazení |
| Přístupový klíč + I | Vytvořit nebo aktualizovat kanál odvození |
| Přístupový klíč + B | Vytvořit nebo aktualizovat kanál odvození dávky |
| Přístupový klíč + K | Otevřete rozevírací seznam Vytvořit kanál odvození. |
| Přístupový klíč + U | Otevřete rozbalovací seznam Aktualizace odvození |
| Přístupový klíč + M | Otevřít další(...) rozevírací informace |

## <a name="next-steps"></a>Další kroky

- [Zapnutí vysokého kontrastu nebo změna motivu](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Nástroje související s usnadněním přístupu ve společnosti Microsoft](https://www.microsoft.com/accessibility)
