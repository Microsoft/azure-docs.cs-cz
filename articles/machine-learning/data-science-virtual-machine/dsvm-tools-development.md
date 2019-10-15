---
title: Vývojářské nástroje
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a integrovanými vývojovými prostředími dostupnými na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 76a550e95de24bf65b9b6097dd332e535da5b1c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330722"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Vývojové nástroje na Data Science Virtual Machine Azure

Sada Data Science Virtual Machine (DSVM) má několik oblíbených nástrojů v vysoce produktivním integrovaném vývojovém prostředí (IDE). Tady je několik nástrojů, které jsou k dispozici na DSVM.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows      |
| Typická použití      | Vývoj softwaru    |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      | Úlohy datových věd (nástroje Python a R), úlohy Azure (Hadoop, Data Lake), Node. js, SQL Server Tools, [Azure Machine Learning pro Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`). Graficky otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start** . Vyhledejte programy (klávesu S logem Windows + S) a potom **Visual Studio**. Odtud můžete vytvářet projekty v jazycích, jako C#je, Python, R a Node. js.   |
| Související nástroje na DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Může se zobrazit zpráva, že platnost zkušebního období vypršela. Zadejte přihlašovací údaje pro účet Microsoft. Nebo vytvořte nový bezplatný účet pro získání přístupu k Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Editor kódu a integrace Gitu   |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) ve Windows, zástupce na ploše nebo terminálu (`code`) v systému Linux    |
| Související nástroje na DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files\RStudio\bin\rstudio.exe`) v systému Windows, zástupce na ploše (`/usr/bin/rstudio`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>Server RStudio 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Co je to?   | Webové prostředí IDE pro R    |
| Podporované verze DSVM      | Linux      |
| Typická použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Povolte službu pomocí nástroje _systemctl Enable RStudio-Server_a pak službu spusťte pomocí _systemctl Start RStudio-Server_. Pak se přihlaste k serveru RStudio na adrese http: \//Your-VM-IP: 8787.       |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk Helena   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj Helena     |
| Jak použít a spustit      | Zástupce na ploše (`C:\JuliaPro-0.5.1.1\Juno.bat`) v systému Windows, zástupce na ploše (`/opt/JuliaPro-VERSION/Juno`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>PyCharm

|    |           |
| ------------- | ------------- |
| Co je to?   | Integrované vývojové prostředí (IDE) klienta jazyka Python    |
| Podporované verze DSVM      | Linux      |
| Typická použití      |  Vývoj v Pythonu     |
| Jak použít a spustit      | Zástupce na ploše (`/usr/bin/pycharm`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Co je to?   | Interaktivní vizualizace dat a nástroj BI    |
| Podporované verze DSVM      | Windows  |
| Typická použití      |  Vizualizace dat a sestavování řídicích panelů   |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Související nástroje na DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

