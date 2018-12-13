---
title: Paralelní simulace jazyka R ve službě Azure Batch
description: Kurz – podrobné pokyny ke spuštění finanční simulace Monte Carlo ve službě Azure Batch pomocí balíku R doAzureParallel
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 305ea27e787196f648fcb4c536f33b12c924c015
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164681"
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Kurz: Spuštění paralelní simulace jazyka R ve službě Azure Batch 

Spouštějte paralelní úlohy v jazyce R ve velkém měřítku pomocí [doAzureParallel](http://www.github.com/Azure/doAzureParallel), nenáročného balíčku v jazyce R, který umožňuje použití služby Azure Batch přímo z relace jazyka R. Balíček doAzureParallel je nástavbou oblíbených balíčků R [foreach](http://cran.r-project.org/web/packages/foreach/index.html). Balíček doAzureParallel přenáší každou iteraci do smyčky foreach a odesílá ji jako úlohu služby Azure Batch.

V tomto kurzu se dozvíte, jak nasadit fond služby Batch a spustit ve službě Azure Batch paralelní úlohu v jazyce R přímo v řešení RStudio. Získáte informace o těchto tématech:
 

> [!div class="checklist"]
> * Instalace balíčku doAzureParallel a jeho konfigurace pro přístup k účtům Batch a úložiště
> * Vytvoření fondu služby Batch jako paralelního back-endu pro relaci jazyka R
> * Spuštění ukázkové paralelní simulace v tomto fondu

## <a name="prerequisites"></a>Požadavky

* Nainstalovaná distribuce [R](https://www.r-project.org/), například [Microsoft R Open](https://mran.microsoft.com/open). Použijte jazyk R verze 3.3.1 nebo novější.

* [RStudio](https://www.rstudio.com/), a to buď komerční edice, nebo opensourcová verze [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop). 

* Účet služby Azure Batch a služby Azure Storage. Informace o vytvoření těchto účtů prostřednictvím [webu Azure Portal](quick-create-portal.md) nebo [rozhraní Azure CLI](quick-create-cli.md) najdete v rychlém startu služby Batch. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>Instalace balíčku doAzureParallel

V konzole RStudio nainstalujte [balíček doazureparallel z Githubu](http://www.github.com/Azure/doAzureParallel). Následující příkazy stáhnou a nainstalují balíček a jeho závislosti v aktuální relaci jazyka R: 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
Instalace může několik minut trvat.

Pokud chcete nakonfigurovat balíček doAzureParallel s využitím přihlašovacích údajů účtu, které jste získali předtím, vygenerujte ve svém pracovním adresáři konfigurační soubor s názvem *credentials.json*: 

```R
generateCredentialsConfig("credentials.json") 
``` 

Do tohoto souboru zadejte své názvy a klíče účtu služby Batch a účtu úložiště. Nastavení `githubAuthenticationToken` nechte beze změny.

Po dokončení bude soubor s přihlašovacími údaji vypadat zhruba takto: 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Uložte soubor. Potom spusťte následující příkaz, který nastaví tyto přihlašovací údaje do vaší aktuální relace jazyka R: 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Vytvoření fondu služby Batch 

Balíček doAzureParallel zahrnuje funkci generování fondu služby Azure Batch (clusteru) za účelem spouštění paralelních úlohy v jazyce R. V uzlech je spuštěný [virtuální počítač Azure pro datové vědy](../machine-learning/data-science-virtual-machine/overview.md) založený na Ubuntu. V této imagi je předinstalovaná distribuce Microsoft R Open a oblíbené balíčky jazyka R. Můžete zobrazit nebo přizpůsobit některá nastavení clusteru, například počet a velikost uzlů. 

Pokud chcete ve svém pracovním adresáři vygenerovat soubor JSON konfigurace clusteru, postupujte takto: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
Otevřete soubor a prohlédněte si výchozí konfiguraci, která zahrnuje 3 vyhrazené uzly a 3 uzly [s nízkou prioritu](batch-low-pri-vms.md). Tato nastavení představují jenom příklad a můžete s nimi experimentovat nebo je upravit. Vyhrazené uzly jsou rezervované pro váš fond. Uzly s nízkou prioritou pocházejí z přebytečné kapacity virtuálních počítačů v Azure a nabízejí se za nižší cenu. Pokud Azure nemá dostatek kapacity, uzly s nízkou prioritou budou nedostupné. 

Pro potřeby tohoto kurzu změňte konfiguraci následujícím způsobem:

* Položku `maxTasksPerNode` navyšte na hodnotu *2*, abyste mohli v každém uzlu využívat obě jádra.
* Položku `dedicatedNodes` nastavte na hodnotu *0*, abyste mohli vyzkoušet nové virtuální počítače s nízkou prioritou dostupné pro službu Batch. U nastavení `min` položky `lowPriorityNodes` zvolte hodnotu *5* a u nastavení `max` hodnotu *10* nebo použijte menší čísla, pokud chcete. 

U ostatních nastavení nechejte výchozí hodnoty a soubor uložte. Mělo by to vypadat nějak takto:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Teď vytvoříte cluster. Služba Batch vytvoří fond okamžitě, ale přidělení a spuštění uzlů úložiště bude několik minut trvat. Až bude cluster dostupný, zaregistrujte ho jako paralelní back-end pro svoji relaci jazyka R. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

Výstup udává počet „pracovních procesů provádění“ pro balíček doAzureParallel. Toto číslo odpovídá počtu uzlů vynásobenému hodnotou `maxTasksPerNode`. Pokud jste změnili konfiguraci clusteru podle výše uvedeného návodu, jedná se o číslo *10*. 
 
## <a name="run-a-parallel-simulation"></a>Spuštění paralelní simulace

Teď máte vytvořený cluster a můžete v zaregistrovaném paralelním back-endu (fondu služby Azure Batch) spustit smyčku foreach. Jako příklad spusťte finanční simulaci Monte Carlo, a to nejdřív v místním prostředí s využitím standardní smyčky foreach, a pak spusťte smyčku foreach ve službě Batch. Tento příklad představuje zjednodušenou verzi předpovědi cen akcií prostřednictvím simulace velkého počtu různých výsledků během 5 let.

Předpokládejme, že cena akcie společnosti Contoso Corporation roste každý den průměrně o 1,001násobek své počáteční ceny, ale má volatilitu (standardní odchylku) 0,01. Při počáteční ceně 100 USD použijte simulaci cen Monte Carlo ke zjištění ceny akcie společnosti Contoso za 5 let.

Parametry simulace Monte Carlo:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

K simulaci koncových cen definujte následující funkci:

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

Nejdřív místěn spusťte 10 000 simulací s využitím standardní smyčky foreach s klíčovým slovem `%do%`:

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


Vykreslete koncové ceny v histogramu, který znázorňuje rozložení výsledků:

```R
hist(closingPrices_s)
``` 

Výstup je podobný tomuto:

![Distribuce koncových cen](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Místní simulace se dokončí za pár sekund nebo hned:

```R
difftime(end_s, start_s) 
```

Odhadovaná doba běhu při 10 milionech výsledků v místním prostředí s využitím lineární aproximace je kolem 30 minut:

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


Teď spusťte kód s využitím smyčky `foreach` s klíčovým slovem `%dopar%`, abyste porovnali, jak dlouho trvá spuštění 10 milionů simulací v Azure. Za účelem vytvoření paralelní simulace ve službě Batch spusťte 100 iterací po 100 000 simulací:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

Simulace distribuuje úkoly do uzlů ve fondu služby Batch. Aktivitu můžete sledovat na heat mapě fondu na webu Azure Portal. Přejděte na **Účty Batch** > *muj-ucet-Batch*. Klikněte na **Fondy** > *muj-nazev-fondu*. 

![Heat mapa fondu spouštějícího paralelní úkoly jazyka R](media/tutorial-r-doazureparallel/pool.png)

Po několika minutách se simulace dokončí. Balíček automaticky sloučí výsledky a stáhne je z uzlů. Potom můžete výsledky použít ve své relaci jazyka R. 

```R
hist(closingPrices_p) 
```

Výstup je podobný tomuto:

![Distribuce koncových cen](media/tutorial-r-doazureparallel/closing-prices.png)

Jak dlouho trvala paralelní simulace? 

```R
difftime(end_p, start_p, unit = "min")  
```

Měli byste zjistit, že spuštění simulace ve fondu služby Batch vám oproti očekávané době při spuštění simulace v místním prostředí přináší výrazné zvýšení výkonu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Úloha se po dokončení automaticky odstraní. Když už cluster nebudete potřebovat, odstraňte ji voláním funkce `stopCluster` v balíčku doAzureParallel:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Další postup
V tomto kurzu jste se naučili tyto postupy:

> [!div class="checklist"]
Instalace balíčku doAzureParallel a jeho konfigurace pro přístup k účtům Batch a úložiště
> * Vytvoření fondu služby Batch jako paralelního back-endu pro relaci jazyka R
> * Spuštění ukázkové paralelní simulace v tomto fondu


Další informace o balíčku doAzureParallel najdete v dokumentaci a ukázkách na GitHubu.

> [!div class="nextstepaction"]
> [Balíček doAzureParallel](https://github.com/Azure/doAzureParallel/)




