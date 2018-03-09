---
title: "Nástroje pro vývoj vědecké účely virtuálního počítače dat – Azure | Microsoft Docs"
description: "Datové vědy virtuální počítače nástroje pro vývoj."
keywords: "datové vědy nástroje, datové vědy virtuálního počítače, nástroje pro vědecké zpracování dat, vědecké zpracování dat linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 0d7ee9a54c2c11f814ce041c5c7e5bd9f2e605e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Nástroje pro vývoj na Data virtuálního počítače vědecké účely

Virtuální počítač vědecké účely dat (DSVM) poskytuje produktivní prostředí pro váš vývojový tím, že sdružování několik oblíbených nástroje a rozhraní IDE. Tady jsou některé nástroje, které jsou dostupné na DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Co je to?   | Obecné účely IDE      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Vývoj softwaru    |
| Jak je ho nakonfigurovaná a nainstalovaná na DSVM?      | Data vědecké účely zatížení (Python a R tools), úloha Azure (Hadoop, Data Lake), Node.js, nástroje SQL Server, [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Na DSVM souvisejících nástrojích      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Co je to?   | Obecné účely IDE      |
| Podporované DSVM verze      | Windows, Linux     |
| Typické použití      | Editor kódu a integrace Gitu   |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) v systému Windows, zástupce na ploše nebo Terminálové (`code`) v systému Linux    |
| Na DSVM souvisejících nástrojích      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Rstudia plochy 
|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro R    |
| Podporované DSVM verze      | Windows, Linux      |
| Typické použití      |  Vývoj R     |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\RStudio\bin\rstudio.exe`) v systému Windows, zástupce na ploše (`/usr/bin/rstudio`) v systému Linux      |
| Na DSVM souvisejících nástrojích      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 
|    |           |
| ------------- | ------------- |
| Co je to?   | Webové rozhraní IDE pro R    |
| Podporované DSVM verze      | Linux      |
| Typické použití      |  Vývoj R     |
| Jak se použít nebo ji spustit?      | Povolit službu s _systemctl povolit serveru rstudia_, spusťte službu s _systemctl spustit server rstudia_. Můžete pak přihlášení k serveru Rstudia v http://your-vm-ip:8787.       |
| Na DSVM souvisejících nástrojích      |   Visual Studio 2017, Visual Studio kód, Rstudia plochy      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Dita   |
| Podporované DSVM verze      | Windows, Linux      |
| Typické použití      |  Dita vývoj     |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`C:\JuliaPro-0.5.1.1\Juno.bat`) v systému Windows, zástupce na ploše (`/opt/JuliaPro-VERSION/Juno`) v systému Linux      |
| Na DSVM souvisejících nástrojích      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Python    |
| Podporované DSVM verze      | Linux      |
| Typické použití      |  Vývoj R     |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`/usr/bin/pycharm`) v systému Linux      |
| Na DSVM souvisejících nástrojích      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Nástroj BI a interaktivní vizualizaci dat    |
| Podporované DSVM verze      | Windows  |
| Typické použití      |  Vizualizace dat a vytváření řídicích panelů   |
| Jak se použít nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Na DSVM souvisejících nástrojích      |   Visual Studio 2017, Visual Studio Code, Juno      |

