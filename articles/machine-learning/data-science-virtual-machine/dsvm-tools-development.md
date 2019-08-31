---
title: Vývojové nástroje
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a integrovanými vývojovými prostředími dostupnými na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 6ff4d92cb3730716c532332bf426132fcbb8e122
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191948"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Vývojové nástroje na Data Science Virtual Machine Azure

Sada Data Science Virtual Machine (DSVM) má několik oblíbených nástrojů v vysoce produktivním integrovaném vývojovém prostředí (IDE). Tady je několik nástrojů, které jsou k dispozici na datové VĚDY.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows      |
| Typické použití      | Vývoj softwaru    |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      | Úloha datové vědy (Python nebo R tools), úloha Azure (Hadoop, Data Lake), Node.js, nástroje SQL Server, [Azure Machine Learning pro Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak použít a spustit      | Zástupce na ploše`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| Související nástroje na DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows, Linux     |
| Typické použití      | Editor kódu a integrace Gitu   |
| Jak použít a spustit      | Zástupce na ploše`C:\Program Files (x86)\Microsoft VS Code\Code.exe`() v systému Windows, zástupce na ploše`code`nebo terminálu () v systému Linux    |
| Související nástroje na DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Podporované verze DSVM      | Windows, Linux      |
| Typické použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Zástupce na ploše`C:\Program Files\RStudio\bin\rstudio.exe`() ve Windows, zástupce na`/usr/bin/rstudio`ploše () v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Co je to?   | Webové rozhraní IDE pro R    |
| Podporované verze DSVM      | Linux      |
| Typické použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Povolte službu pomocí nástroje _systemctl Enable RStudio-Server_a pak službu spusťte pomocí _systemctl Start RStudio-Server_. Pak se přihlaste k serveru RStudio na\/adrese http:/your-VM-IP: 8787.       |
| Související nástroje na DSVM      |   Visual Studio 2019, Visual Studio Code RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Julia   |
| Podporované verze DSVM      | Windows, Linux      |
| Typické použití      |  Helena vývoj     |
| Jak použít a spustit      | Zástupce na ploše`C:\JuliaPro-0.5.1.1\Juno.bat`() ve Windows, zástupce na`/opt/JuliaPro-VERSION/Juno`ploše () v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Python    |
| Podporované verze DSVM      | Linux      |
| Typické použití      |  Vývoj v jazyce Python     |
| Jak použít a spustit      | Zástupce na ploše`/usr/bin/pycharm`() v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroj BI    |
| Podporované verze DSVM      | Windows  |
| Typické použití      |  Vizualizace dat a sestavování řídicích panelů   |
| Jak použít a spustit      | Zástupce na ploše`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Související nástroje na DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

