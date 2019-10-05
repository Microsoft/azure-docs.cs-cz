---
title: Instalace balíčků do Jupyter poznámkového bloku v Azure
description: Jak nainstalovat Python, R a F# balíčky v rámci Jupyter poznámkového bloku běžícího na Azure
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: e6582bb21cb6d383e298bdf68bdb2f565147c20a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969932"
---
# <a name="install-packages-from-within-a-notebook"></a>Instalace balíčků z poznámkového bloku

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

Balíčky v jazyce R můžete nainstalovat z CRAN nebo GitHubu pomocí funkce `install.packages` v buňce kódu:

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

Balíčky v F# nástroji je možné instalovat z [NuGet.org](https://www.nuget.org) voláním správce závislostí paket z buněk v kódu. Nejdřív načtěte paket Manager:

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

Otevřete knihovnou:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Další kroky

- [Postupy: konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: zobrazení prezentace](present-jupyter-notebooks-slideshow.md)
