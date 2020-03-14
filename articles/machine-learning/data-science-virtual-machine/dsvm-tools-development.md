---
title: Vývojářské nástroje
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a integrovanými vývojovými prostředími dostupnými na Data Science Virtual Machine.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283755"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Vývojové nástroje na Data Science Virtual Machine Azure

Sada Data Science Virtual Machine (DSVM) má několik oblíbených nástrojů v vysoce produktivním integrovaném vývojovém prostředí (IDE). Tady je několik nástrojů, které jsou k dispozici na datové VĚDY.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows: Visual Studio 2017, Windows 2019 (Preview): Visual Studio 2019      |
| Typické použití      | Vývoj softwaru    |
| Jak je nakonfigurovaná a nainstalovaná na DSVM?      | Úlohy datových věd (nástroje Python a R), úlohy Azure (Hadoop, Data Lake), Node. js, SQL Server Tools, [Azure Machine Learning pro Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Graficky otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start** . Vyhledejte programy (klávesu S logem Windows + S) a potom **Visual Studio**. Odtud můžete vytvářet projekty v jazycích, jako C#je, Python, R a Node. js.   |
| Související nástroje na DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Může se zobrazit zpráva, že vypršela platnost vašeho zkušebního období. Zadejte svoje přihlašovací údaje účtu Microsoft. Nebo vytvořte nový bezplatný účet, abyste získali přístup k Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE pro obecné účely      |
| Podporované verze DSVM      | Windows, Linux     |
| Typické použití      | Editor kódu a integrace Gitu   |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) ve Windows, zástupce na ploše nebo terminálu (`code`) v systému Linux    |
| Související nástroje na DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Podporované verze DSVM      | Windows, Linux      |
| Typické použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files\RStudio\bin\rstudio.exe`) ve Windows, zástupce na ploše (`/usr/bin/rstudio`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Co je to?   | Rozhraní IDE klienta pro jazyk R   |
| Co je to?   | Webové rozhraní IDE pro R    |
| Podporované verze DSVM      | Linux      |
| Typické použití      |  Vývoj v jazyce R     |
| Jak použít a spustit      | Povolte službu pomocí nástroje _systemctl Enable RStudio-Server_a pak službu spusťte pomocí _systemctl Start RStudio-Server_. Pak se přihlaste k serveru RStudio na adrese http:\//Your-VM-IP: 8787.       |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Julia   |
| Podporované verze DSVM      | Windows, Linux      |
| Typické použití      |  Helena vývoj     |
| Jak použít a spustit      | Zástupce na ploše (`C:\JuliaPro-0.5.1.1\Juno.bat`) ve Windows, zástupce na ploše (`/opt/JuliaPro-VERSION/Juno`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Co je to?   | Klient IDE pro jazyk Python    |
| Podporované verze DSVM      | Windows 2019 (Preview), Linux      |
| Typické použití      |  Vývoj v Pythonu     |
| Jak použít a spustit      | Zástupce na ploše (`C:\Program Files\tk`) ve Windows. Zástupce na ploše (`/usr/bin/pycharm`) v systému Linux      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
