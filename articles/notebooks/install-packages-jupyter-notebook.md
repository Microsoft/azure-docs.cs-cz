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
ms.openlocfilehash: 504158f248cde3a399475cdec99de3e6a4ebfcc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598013"
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

Balíčky jazyka r můžete nainstalovat pomocí CRAN nebo GitHub pomocí `install.packages` funkce do buňky kódu:

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

- [Postup: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postup: K dispozici prezentace](present-jupyter-notebooks-slideshow.md)
