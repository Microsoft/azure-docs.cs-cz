---
title: Instalovat balíčky v Jupyter notebook v Azure
description: Jak nainstalovat Python, R, a F# balíčky z v rámci poznámkového bloku Jupyter běžící v Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b0881cb6dac9ec83d2126942c758508e760f9c83
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274424"
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

Balíčky v R můžete nainstalovat z Cran nebo GitHubu pomocí `install.packages` funkce v buňce kódu:

```r
install.packages("package_name")
```

Předprodejní verze a další vývojové balíčky můžete také nainstalovat z GitHubu pomocí knihovny devtools:

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

Pak načtěte generátor paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Otevřete knihovnou:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Další postup

- [Postup: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: Prezentace prezentace](present-jupyter-notebooks-slideshow.md)
