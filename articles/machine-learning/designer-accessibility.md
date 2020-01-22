---
title: Použití funkcí usnadnění v Návrháři
titleSuffix: Azure Machine Learning
description: Přečtěte si o funkcích, které jsou k dispozici v návrháři, klávesových zkratek a obrazovkách čtečky
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 7d5c8047fea8e9081de08b582c537711714ea463
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314228"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>Použití klávesnice k použití návrháře Azure Machine Learning

Naučte se používat čtečku klávesnice a obrazovky k používání návrháře Azure Machine Learning. Seznam klávesových zkratek, které fungují všude v Azure Portal, najdete v tématu [klávesové zkratky v Azure Portal](../azure-portal/azure-portal-keyboard-shortcuts.md)

Tento pracovní postup byl testován pomocí programu [Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) a [JAWS](https://www.freedomscientific.com/products/software/jaws/), ale měl by fungovat s dalšími standardními čtečkami obrazovky.

## <a name="navigate-the-pipeline-graph"></a>Navigace v grafu kanálu

Graf kanálu je uspořádaný jako seznam seznamů. Seznam modulů nejvyšší úrovně obsahuje celý modul v kanálu. Každá položka v seznamu modul obsahuje seznam připojení, který popisuje všechna jeho připojení. 

1. V seznamu modul použijte klávesu šipka k přepínání modulů.
1. Pomocí karty otevřete seznam připojení pro cílový modul.
1. K přepínání mezi porty připojení pro modul použijte klávesu šipka.
1. K přechodu na cílový modul použijte "G".

## <a name="edit-the-pipeline-graph"></a>Úprava grafu kanálu

### <a name="add-a-module-to-the-graph"></a>Přidání modulu do grafu

1. Stisknutím kombinace kláves CTRL + F6 přepnete fokus z plátna do stromu modulu.
1. Vyhledá požadovaný modul ve stromu modulu pomocí standardního ovládacího prvku TreeView.

### <a name="edit-a-module"></a>Upravit modul

Připojení modulu k jinému modulu:

1. Pokud chcete otevřít pomocníka připojení, použijte Ctrl + Shift + H při cílení na modul v seznamu modul.
1. Upravte porty připojení pro modul.

Úprava vlastností modulu:

1. Pro otevření vlastností modulu použijte CTRL + SHIFT + E při cílení na modul.
1. Upravte vlastnosti modulu.

## <a name="navigation-shortcuts"></a>Navigační zástupci

| Stisknutí kláves | Popis |
|-|-|
| Ctrl+F6 | Přepnout fokus mezi plátnem a stromem modulu |
| CTRL + F1   | Při zaměření na uzel ve stromu modulů otevřít informační kartu |
| CTRL + SHIFT + H | Otevřete pomocníka připojení, pokud se fokus nachází na uzlu. |
| CTRL + SHIFT + E | Otevřete vlastnosti modulu, když je fokus na uzlu. |
| CTRL + G | Přesunout fokus na první neúspěšný uzel, pokud selhalo spuštění kanálu |

## <a name="action-shortcuts"></a>Zástupci akcí

Použijte následující klávesové zkratky s přístupovým klíčem. Další informace o přístupových klíčích najdete v tématu https://en.wikipedia.org/wiki/Access_key.

| Stisknutí kláves | Akce |
|-|-|
| Přístupový klíč + R | Spuštění |
| Přístupový klíč + P | Publikování |
| Přístupová klávesa + C | Klon |
| Přístupová klávesa + D | Nasazení |
| Přístupový klíč + I | Vytvořit nebo aktualizovat kanál odvození |
| Přístupový klíč + B | Vytvořit nebo aktualizovat kanál odvození dávky |
| Přístupový klíč + K | Otevřete rozevírací seznam vytvořit kanál odvození. |
| Přístupový klíč + U | Otevření rozevíracího seznamu pro odvození kanálu aktualizace |
| Přístupový klíč + M | Otevřít rozevírací seznam další (...) |

## <a name="next-steps"></a>Další kroky

- [Zapnutí vysokého kontrastu nebo změna motivu](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Nástroje související s přístupností v Microsoftu](https://www.microsoft.com/accessibility)
