---
title: Rozšíření experimentu pomocí R
titleSuffix: Azure Machine Learning Studio
description: Jak rozšiřovat funkce Azure Machine Learning Studio pomocí jazyka R s použitím modulu spustit skript jazyka R došlo k chybě.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 98f5922cc329aedc8deeffbcb042bddf90405883
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497143"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Azure Machine Learning Studio: Rozšíření experimentu pomocí R 
Funkce Azure Machine Learning Studio pomocí jazyka R můžete rozšířit pomocí [spustit skript jazyka R] [ execute-r-script] modulu.

Tento modul přijímá více vstupních datových sad a vrací jednu datovou sadu jako výstup. Můžete zadat do skriptu jazyka R **skript jazyka R** parametr [spustit skript jazyka R] [ execute-r-script] modulu.

Každý vstupnímu portu modulu přistupujete pomocí kódu podobného následujícímu:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Výpis všech aktuálně nainstalované balíčky
Můžete změnit seznam nainstalovaných balíčků. Seznam aktuálně nainstalovaných balíčků můžete najít v [balíčky R podporované nástroji Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Také můžete získat kompletní a aktuální seznam nainstalovaných balíčků tak, že zadáte následující kód do [spustit skript jazyka R] [ execute-r-script] modul:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Tím se odešle seznam balíčků na výstupní port modulu [spustit skript jazyka R] [ execute-r-script] modulu.
Chcete-li zobrazit seznam balíčků, připojte převáděcího modulu [převést na sdílený svazek clusteru] [ convert-to-csv] na levém výstupu [spustit skript jazyka R] [ execute-r-script] modulu Spusťte experiment, pak kliknutím na výstup u převáděcího modulu a vyberte **Stáhnout**. 

![Stáhněte si výstup modulu "Převést na sdílený svazek clusteru"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Import balíčků
Můžete importovat balíčky, které nejsou nainstalovány pomocí následujících příkazů v [spustit skript jazyka R] [ execute-r-script] modul:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

kde `my_favorite_package.zip` soubor obsahuje vašeho balíčku.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
