---
title: Vývojové nástroje
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a integrovanými vývojovými prostředími dostupnými na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, nástroje pro datové vědy, datové vědy pro Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 62eb5f72d4b4395602b2665c0d1b3da4f6bb459b
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950203"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Vývojové nástroje na Data Science Virtual Machine Azure

Sada Data Science Virtual Machine (DSVM) má několik oblíbených nástrojů v vysoce produktivním integrovaném vývojovém prostředí (IDE). Tady je několik nástrojů, které jsou k dispozici na DSVM.

## <a name="visual-studio-2017"></a>Visual Studio 2017

|    |           |
| ------------- | ------------- |
| Co to je?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Vývoj softwaru    |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      | Úlohy datových věd (nástroje Python a R), úlohy Azure (Hadoop, Data Lake), Node. js, SQL Server Tools, [Azure Machine Learning pro Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Související nástroje na DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co to je?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Editor kódu a integrace Gitu   |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) ve Windows, zástupce na ploše nebo terminálu (`code`) v systému Linux    |
| Související nástroje na DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Co to je?   | Rozhraní IDE klienta pro jazyk R   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files\RStudio\bin\rstudio.exe`) v systému Windows, zástupce na ploše (`/usr/bin/rstudio`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>Server RStudio 

|    |           |
| ------------- | ------------- |
| Co to je?   | Rozhraní IDE klienta pro jazyk R   |
| Co to je?   | Webové prostředí IDE pro R    |
| Podporované verze DSVM      | Linux      |
| Typická použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Povolte službu pomocí nástroje _systemctl Enable RStudio-Server_a pak službu spusťte pomocí _systemctl Start RStudio-Server_. Pak se přihlaste k serveru RStudio na adrese http: \//Your-VM-IP: 8787.       |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co to je?   | Rozhraní IDE klienta pro jazyk Helena   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj Helena     |
| Jak použít a spustit      | Zástupce na ploše (`C:\JuliaPro-0.5.1.1\Juno.bat`) v systému Windows, zástupce na ploše (`/opt/JuliaPro-VERSION/Juno`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Co to je?   | Integrované vývojové prostředí (IDE) klienta jazyka Python    |
| Podporované verze DSVM      | Linux      |
| Typická použití      |  Vývoj v Pythonu     |
| Jak použít a spustit      | Zástupce na ploše (`/usr/bin/pycharm`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Co to je?   | Interaktivní vizualizace dat a nástroj BI    |
| Podporované verze DSVM      | Windows  |
| Typická použití      |  Vizualizace dat a sestavování řídicích panelů   |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

