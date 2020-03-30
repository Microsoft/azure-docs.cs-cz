---
title: Instalace balíčků v poznámkových blocích Jupyter – Azure Notebooks Preview
description: Zjistěte, jak nainstalovat balíčky Pythonu, R a F# z poznámkového bloku Jupyter spuštěného v Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646224"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instalace balíčků z náhledu poznámkových bloků Azure

Přestože můžete nakonfigurovat [prostředí pro poznámkový blok na úrovni projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), můžete nainstalovat balíčky přímo do jednotlivého poznámkového bloku.

Balíčky nainstalované z poznámkového bloku se vztahují pouze na aktuální relaci serveru. Instalace balíčků nejsou po vypnutí serveru trvalé.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Balíčky v Pythonu lze instalovat pomocí pipnebo conda pomocí příkazů v buňkách kódu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Pokud výstup příkazu označuje, že požadavek je již splněna, pak Poznámkové bloky Azure může obsahovat balíček ve výchozím nastavení. Balíček může být také nainstalován prostřednictvím [kroku nastavení prostředí projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Balíčky v R lze nainstalovat z `install.packages` CRAN nebo GitHub pomocí funkce v buňce kódu:

```r
install.packages("package_name")
```

Můžete také nainstalovat předběžné verze a další vývojové balíčky z GitHubu pomocí knihovny devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Balíčky v F# lze nainstalovat z [nuget.org](https://www.nuget.org) voláním Správce závislostí paketu z buněk kódu. Nejprve načtěte správce paketu:

```fsharp
#load "Paket.fsx"
```

Pak nainstalujte balíčky:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Poté načtěte generátor Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Otevřete knihovnu:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Další kroky

- [Postup: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postup: Prezentace prezentace](present-jupyter-notebooks-slideshow.md)
