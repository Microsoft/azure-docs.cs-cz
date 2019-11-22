---
title: Instalovat balíčky v Jupyter notebook v Azure
description: Jak nainstalovat Python, R, a F# balíčky z v rámci poznámkového bloku Jupyter běžící v Azure.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 5d85c8e936ce7c8bf38ec7bc9c27d9066cc8b155
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277533"
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

Balíčky v R můžete nainstalovat z CRAN nebo GitHubu pomocí funkce `install.packages` v buňce kódu:

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

## <a name="next-steps"></a>Další kroky

- [Postupy: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: zobrazení prezentace](present-jupyter-notebooks-slideshow.md)
