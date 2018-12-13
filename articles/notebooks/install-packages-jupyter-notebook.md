---
title: Instalovat balíčky v Jupyter notebook v Azure
description: Jak nainstalovat Python, R, a F# balíčky z v rámci poznámkového bloku Jupyter běžící v Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed57cb458cc1f71c5d6fd2475c54cc712257dd84
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084868"
---
# <a name="install-packages-from-within-a-notebook"></a>Instalace balíčků z v rámci poznámkového bloku

Přestože lze nakonfigurovat [prostředí pro poznámkový blok na úrovni projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), můžete chtít nainstalovat balíčky přímo v rámci jednotlivých poznámkového bloku.

Balíčky nainstalované z poznámkového bloku platí pouze pro aktuální relaci serveru. Instalace balíčků se jako trvalý, po vypnutí serveru.

## <a name="python"></a>Python

Balíčky pythonu můžete nainstalovat pomocí pip nebo conda pomocí příkazů v buňkách kódu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Pokud výstup příkazu znamená, že tento požadavek již není splněna, pak poznámkových bloků Azure mohou zahrnovat balíček ve výchozím nastavení. Balíček může se taky nainstalovat přes [krok instalace prostředí projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Balíčky jazyka r můžete nainstalovat pomocí CRAN nebo Github pomocí `install.packages` funkce do buňky kódu:

```r
install.packages("package_name")
```

Můžete také nainstalovat předprodejních verzí a další balíčky pro vývoj z Githubu pomocí devtools knihovny:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Balíčky v F# si můžete nainstalovat pomocí [nuget.org](https://www.nuget.org) voláním závislostí stáhnout správce z v rámci buňky kódu. Nejdřív načtěte stáhnout správce:

```fsharp
#load "Paket.fsx"
```

Nainstalujte balíčky:

```fsharp
Paket.Package
[ "MathNet.Numerics"
"MathNet.Numerics.FSharp"
]
```

## <a name="next-steps"></a>Další postup

- [Postupy: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: zobrazení prezentace](present-jupyter-notebooks-slideshow.md)
