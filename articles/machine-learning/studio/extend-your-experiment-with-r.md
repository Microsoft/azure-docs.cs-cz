---
title: Rozšíření experimentu pomocí R
titleSuffix: ML Studio (classic) - Azure
description: Postup rozšiřování funkcí Azure Machine Learning Studio (Classic) prostřednictvím jazyka R pomocí modulu spuštění skriptu jazyka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218037"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (Classic): prodlužte experiment pomocí R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Můžete roztáhnout funkce Azure Machine Learning Studio (Classic) prostřednictvím jazyka R pomocí modulu [spuštění skriptu jazyka r][execute-r-script] .

Tento modul přijímá více vstupních datových sad a jako výstup poskytuje jedinou datovou sadu. Skript R můžete zadat do parametru skriptu jazyka **r** v modulu [spouštění skriptu jazyka r][execute-r-script] .

Ke každému vstupnímu portu modulu přistupujete pomocí kódu podobného následujícímu:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Výpis všech aktuálně nainstalovaných balíčků
Seznam nainstalovaných balíčků se může změnit. Seznam aktuálně nainstalovaných balíčků najdete v [balíčcích R, které podporuje Azure Machine Learning Studio (Classic)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Úplný aktuální seznam nainstalovaných balíčků můžete získat tak, že do modulu [spuštění skriptu jazyka R][execute-r-script] zadáte následující kód:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Tím se seznam balíčků pošle na výstupní port modulu [spouštěného skriptu jazyka R][execute-r-script] .
Pokud chcete zobrazit seznam balíčků, připojte modul převodu, jako je třeba [převést na sdílený svazek clusteru][convert-to-csv] , na levý výstup modulu Run [R Script][execute-r-script] , spusťte experiment, potom klikněte na výstup modulu pro převod a vyberte **Stáhnout**. 

![Stáhnout výstup modulu převést do sdíleného svazku clusteru](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Import balíčků
Balíčky, které ještě nejsou nainstalované, můžete importovat pomocí následujících příkazů v modulu [spuštění skriptu jazyka R][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

kde `my_favorite_package.zip` soubor obsahuje váš balíček.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
