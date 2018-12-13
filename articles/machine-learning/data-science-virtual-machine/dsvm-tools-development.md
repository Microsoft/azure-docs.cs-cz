---
title: Vývojové nástroje data Science virtuálního počítače – Azure | Dokumentace Microsoftu
description: Další informace o nástrojích a Integrovaná vývojová prostředí, které jsou předem nainstalované na virtuální počítač pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: ee1e7003a9f1479db7311072e29132cf491cbc88
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073750"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Nástroje pro vývoj na virtuální počítač pro datové vědy

Na Data virtuálního počítače VĚDY poskytuje produktivní prostředí pro vývoj seskupí několik oblíbených nástrojů a rozhraní IDE. Tady je několik nástrojů, které jsou k dispozici na datové VĚDY. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Co je to?   | Integrované vývojové prostředí pro obecné účely      |
| Podporované DSVM verze      | Windows      |
| Typické použití      | Vývoj softwaru    |
| Jak ho nakonfigurovat či nainstalovaná na datové VĚDY?      | Úloha datové vědy (Python nebo R tools), úloha Azure (Hadoop, Data Lake), Node.js, nástroje SQL Server, [Azure Machine Learning pro Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Související nástroje na datové VĚDY      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Co je to?   | Integrované vývojové prostředí pro obecné účely      |
| Podporované DSVM verze      | Windows, Linux     |
| Typické použití      | Editor kódu a integrace Gitu   |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) ve Windows, zástupce na ploše nebo terminálu (`code`) v systému Linux    |
| Související nástroje na datové VĚDY      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Klientské rozhraní IDE pro R    |
| Podporované DSVM verze      | Windows, Linux      |
| Typické použití      |  Vývoj v jazyce R     |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\RStudio\bin\rstudio.exe`) na Windows, zástupce na ploše (`/usr/bin/rstudio`) v Linuxu      |
| Související nástroje na datové VĚDY      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 
|    |           |
| ------------- | ------------- |
| Co je to?   | Webové rozhraní IDE pro R    |
| Podporované DSVM verze      | Linux      |
| Typické použití      |  Vývoj v jazyce R     |
| Jak používat nebo ji spustit?      | Povolit službu s _systemctl povolit rstudio serveru_, spusťte službu s _systemctl start rstudio serveru_. Potom můžete přihlásit k přihlašovací stránce RStudio serveru v http://your-vm-ip:8787.       |
| Související nástroje na datové VĚDY      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Julia   |
| Podporované DSVM verze      | Windows, Linux      |
| Typické použití      |  Helena vývoj     |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\JuliaPro-0.5.1.1\Juno.bat`) na Windows, zástupce na ploše (`/opt/JuliaPro-VERSION/Juno`) v Linuxu      |
| Související nástroje na datové VĚDY      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Python    |
| Podporované DSVM verze      | Linux      |
| Typické použití      |  Vývoj v jazyce Python     |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`/usr/bin/pycharm`) v Linuxu      |
| Související nástroje na datové VĚDY      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroje BI    |
| Podporované DSVM verze      | Windows  |
| Typické použití      |  Vizualizace dat a vytváření řídicích panelů   |
| Jak používat nebo ji spustit?      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Související nástroje na datové VĚDY      |   Visual Studio 2017, Visual Studio Code, Juno      |

