---
title: Rozdělení adresáře obrázků
titleSuffix: Azure Machine Learning
description: Naučte se používat modul pro model image skóre v Azure Machine Learning k vygenerování předpovědi pomocí modelu vyškolených imagí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 63ae7115f905523a3aac131fd7e77b56eb695243
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890257"
---
# <a name="split-image-directory"></a>Rozdělení adresáře obrázků

Toto téma popisuje, jak použít modul adresáře rozdělené bitové kopie v Návrháři Azure Machine Learning k rozdělení imagí adresáře obrázků do dvou různých sad.

Tento modul je zvláště užitečný v případě, že potřebujete oddělit obrazová data do sad pro školení a testování. 

## <a name="how-to-configure-split-image-directory"></a>Jak nakonfigurovat adresář rozdělené bitové kopie

1. Přidejte do svého kanálu modul **adresáře rozdělené bitové kopie** . Tento modul najdete pod kategorií ' Počítačové zpracování obrazu/Image Data Transformation '.

2. Připojte ho k modulu, jehož výstupem je adresář imagí.

3. Vstupní **zlomek obrázků v prvním výstupu** , který určuje procentuální hodnotu dat, která se mají vložit do levého rozdělení, ve výchozím nastavení 0,9.


## <a name="technical-notes"></a>Technické poznámky

### <a name="expected-inputs"></a>Očekávané vstupy

| Název                  | Typ           | Description              |
| --------------------- | -------------- | ------------------------ |
| Adresář vstupní bitové kopie | ImageDirectory | Adresář obrázku, který se má rozdělit |

### <a name="module-parameters"></a>Parametry modulu

| Název                                   | Typ  | Rozsah | Volitelné | Description                            | Výchozí |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Zlomek obrázků v prvním výstupu | Float | 0-1   | Vyžadováno | Zlomek obrázků v prvním výstupu | 0,9     |

### <a name="outputs"></a>Výstupy

| Název                    | Typ           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Výstupní image directory1 | ImageDirectory | Adresář imagí obsahující vybrané image |
| Výstupní image directory2 | ImageDirectory | Adresář imagí, který obsahuje všechny ostatní image |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

