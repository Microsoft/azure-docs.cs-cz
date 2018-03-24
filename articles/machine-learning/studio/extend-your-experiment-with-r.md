---
title: Rozšíření experimentů pomocí R | Microsoft Docs
description: Postup rozšíření funkce Azure Machine Learning Studio prostřednictvím jazyka R pomocí modulu spuštění skriptu R došlo k chybě.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 99c57cc095c3eb017ada68417b7b36c8773ed3d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="extend-your-experiment-with-r"></a>Rozšíření experimentu pomocí R
Funkce Azure Machine Learning Studio prostřednictvím jazyka R můžete rozšířit pomocí [spustit skript jazyka R] [ execute-r-script] modulu.

Tento modul přijímá více vstupní datové sady a výsledkem jednu datovou sadu jako výstup. Můžete zadat skript jazyka R do **skript jazyka R** parametr [spustit skript jazyka R] [ execute-r-script] modulu.

Máte přístup k každý vstupní port modulu pomocí kódu podobný následujícímu:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Seznam aktuálně nainstalovaných balíčků
Seznam balíčků nainstalovaných můžete změnit. Seznam aktuálně nainstalovaných balíčků naleznete v [R balíčky nepodporuje Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Můžete také můžete získat úplný, aktuální seznam balíčků nainstalovaných tak, že zadáte následující kód do [spustit skript jazyka R] [ execute-r-script] modul:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Tím se odešle seznam balíčků do výstupní port modulu [spustit skript jazyka R] [ execute-r-script] modulu.
Chcete-li zobrazit seznam balíčků, připojte modul převod [převést na sdílený svazek clusteru] [ convert-to-csv] na levém výstupu [spustit skript jazyka R] [ execute-r-script] modulu, Spusťte experiment, pak klikněte na výstup modulu převod a vyberte **Stáhnout**. 

![Stažení výstupu modulu "Převést na CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Import balíčků
Můžete importovat balíčky, které již nejsou nainstalovány pomocí následujících příkazů v [spustit skript jazyka R] [ execute-r-script] modul:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

kde `my_favorite_package.zip` soubor obsahuje vašeho balíčku.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
