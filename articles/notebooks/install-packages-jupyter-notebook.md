---
title: Instalace balíčků v poznámkových blocích Jupyter Azure Notebooks – Preview
description: 'Naučte se instalovat balíčky v Pythonu, R a F # z Jupyter poznámkového bloku běžícího na Azure.'
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831212"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Instalace balíčků z aplikace Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I když můžete nakonfigurovat [prostředí pro svůj Poznámkový blok na úrovni projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), můžete chtít nainstalovat balíčky přímo v jednotlivém poznámkovém bloku.

Balíčky nainstalované z poznámkového bloku se vztahují jenom na aktuální relaci serveru. Instalace balíčku se po vypnutí serveru neuloží.

## <a name="python"></a>Python

Balíčky v Pythonu se dají nainstalovat pomocí příkazu PIP nebo Conda, a to pomocí příkazů v buňkách kódu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Pokud výstup příkazu indikuje, že požadavek je již splněn, pak Azure Notebooks může zahrnout balíček ve výchozím nastavení. Balíček může být také nainstalovaný v [kroku nastavení prostředí projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Balíčky v R můžete nainstalovat z CRAN nebo GitHubu pomocí `install.packages` funkce v buňce kódu:

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

Balíčky v jazyce F # lze nainstalovat z [NuGet.org](https://www.nuget.org) voláním správce závislostí paket z buněk kódu. Nejdřív načtěte paket Manager:

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

Pak načtěte generátor paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Otevřete knihovnu:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Další kroky

- [Postupy: konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: zobrazení prezentace](present-jupyter-notebooks-slideshow.md)
