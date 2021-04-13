---
title: 'ML Studio (klasický): migrace na Azure Machine Learning – provedení skriptu R'
description: Opětovné sestavení studia (Classic) spustí moduly skriptu R, které budou spouštěny na Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 18d9013b1a5afcf57b54cffc01a15948382210bb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311035"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrace spouštění modulů skriptu R v studiu (Classic)

V tomto článku se dozvíte, jak v Azure Machine Learning znovu sestavit Studio (Classic) **spustit modul skriptu jazyka R** .

Další informace o migraci z studia (Classic) najdete v [článku Přehled migrace](migrate-overview.md).

## <a name="execute-r-script"></a>Spouštění skriptů R

Azure Machine Learning Designer nyní běží na Linux. Studio (Classic) běží na Windows. Vzhledem k tomu, že dojde ke změně platformy, je nutné během migrace upravit **skript Execute R** , jinak se kanál nezdaří.

Chcete-li migrovat modul **spuštění skriptu jazyka R** z aplikace Studio (Classic), je nutné nahradit `maml.mapInputPort` `maml.mapOutputPort` rozhraní a standardními funkcemi.

Následující tabulka shrnuje změny modulu skriptu jazyka R:

|Funkce|Studio (Classic)|Návrhář služby Azure Machine Learning|
|---|---|---|
|Skriptovací rozhraní|`maml.mapInputPort` a `maml.mapOutputPort`|Rozhraní funkce|
|Platforma|Windows|Linux|
|Přístup k Internetu |No|Yes|
|Memory (Paměť)|14 GB|Závislá na skladové SKU COMPUTE|

### <a name="how-to-update-the-r-script-interface"></a>Postup aktualizace rozhraní skriptu jazyka R

Tady je obsah ukázkového modulu **spuštění skriptu jazyka R** v nástroji Studio (Classic):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Zde je aktualizovaný obsah v návrháři. Všimněte si, že `maml.mapInputPort` a byly `maml.mapOutputPort` nahrazeny standardním rozhraním Function `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Další informace najdete v referenčních informacích k [modulu skriptu jazyka R pro spuštění](../algorithm-module-reference/execute-r-script.md)návrháře.

### <a name="install-r-packages-from-the-internet"></a>Instalace balíčků R z Internetu

Azure Machine Learning Designer umožňuje instalovat balíčky přímo z CRAN.

Toto je vylepšení přes Studio (Classic). Vzhledem k tomu, že Studio (Classic) běží v prostředí izolovaného prostoru (sandbox) bez přístupu k Internetu, museli byste nahrávat skripty do sady zip pro instalaci dalších balíčků. 

Použijte následující kód k instalaci balíčků CRAN do modulu **spuštění skriptu jazyka R** v Návrháři:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak migrovat moduly spouštěné skriptem R na Azure Machine Learning.

Podívejte se na další články v řadě migrace pro Studio (Classic):

1. [Přehled migrace](migrate-overview.md)
1. [Migrujte datovou sadu](migrate-register-dataset.md).
1. [Znovu sestavte školicí kanál studia (Classic)](migrate-rebuild-experiment.md).
1. [Opětovné sestavení webové služby studia (Classic)](migrate-rebuild-web-service.md).
1. [Integrujte webovou službu Azure Machine Learning s klientskými aplikacemi](migrate-rebuild-integrate-with-client-app.md).
1. **Migrujte moduly spouštěné pomocí skriptu R**.