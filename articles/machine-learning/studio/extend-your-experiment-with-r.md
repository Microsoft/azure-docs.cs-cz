---
title: Rozšíření experimentu pomocí R
titleSuffix: ML Studio (classic) - Azure
description: Jak rozšířit funkce Azure Machine Learning Studio (klasické) prostřednictvím jazyka R pomocí modulu Spouštění jazyka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218037"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klasika): Rozšiřte experiment s R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Můžete rozšířit funkce Azure Machine Learning Studio (klasické) prostřednictvím jazyka R pomocí modulu [Spouštění jazyka R.][execute-r-script]

Tento modul přijímá více vstupních datových sad a poskytuje jednu datovou sadu jako výstup. Do parametru **Skript R** modulu Spustit [skript R][execute-r-script] můžete zadat skript R.

Ke každému vstupnímu portu modulu se přistupuje pomocí kódu podobného následujícímu:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Výpis všech aktuálně nainstalovaných balíčků
Seznam nainstalovaných balíčků se může změnit. Seznam aktuálně nainstalovaných balíčků najdete v [balíčcích R podporovaných aplikací Azure Machine Learning Studio (klasické).](https://msdn.microsoft.com/library/azure/mt741980.aspx)

Můžete také získat kompletní, aktuální seznam nainstalovaných balíčků zadáním následujícího kódu do modulu [Spustit Skript R:][execute-r-script]

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Tím odešlete seznam balíčků na výstupní port modulu [Execute R Script.][execute-r-script]
Chcete-li zobrazit seznam balíčků, připojte konverzní modul, například [Převést na CSV,][convert-to-csv] k levému výstupu modulu [Spustit skript R,][execute-r-script] spusťte experiment, potom klepněte na výstup konverzního modulu a vyberte **stáhnout**. 

![Stáhnout výstup modulu "Převést na CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Import balíčků
Balíčky, které ještě nejsou nainstalovány, můžete importovat pomocí následujících příkazů v modulu [Spustit skript R:][execute-r-script]

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

kde `my_favorite_package.zip` soubor obsahuje váš balíček.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
