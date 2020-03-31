---
title: Vývojářské nástroje
titleSuffix: Azure Data Science Virtual Machine
description: Seznamte se s nástroji a integrovanými vývojovými prostředími dostupnými ve virtuálním počítači pro datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282677"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Vývojové nástroje na virtuálním počítači Azure Data Science

Virtuální počítač pro datové vědy (DSVM) spojuje několik oblíbených nástrojů ve vysoce produktivním integrovaném vývojovém prostředí (IDE). Zde jsou některé nástroje, které jsou k dispozici na DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Co je to?   | IDE pro obecné účely      |
| Podporované verze DSVM      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Typická použití      | Vývoj softwaru    |
| Jak je konfigurován a nainstalován na DSVM?      | Úlohy datové vědy (nástroje Pythona a R), úlohy Azure (Hadoop, Datové jezero), Node.js, sql serverové nástroje, [Azure Machine Learning for Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Jak ji používat a spouštět      | Zástupce na`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`ploše ( ). Graficky otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start.** Vyhledejte programy (klávesa s logem Windows+S), následované **visual studio**. Odtud můžete vytvářet projekty v jazycích, jako je C#, Python, R a Node.js.   |
| Související nástroje na DSVM      |     Visual Studio Kód, RStudio, Juno  |

> [!NOTE]
> Může se zobrazit zpráva, že platnost zkušební doby vypršela. Zadejte přihlašovací údaje k účtu Microsoft. Nebo vytvořte nový bezplatný účet pro získání přístupu ke komunitě Sady Visual Studio.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Co je to?   | IDE pro obecné účely      |
| Podporované verze DSVM      | Windows, Linux     |
| Typická použití      | Editor kódu a integrace Gitu   |
| Jak ji používat a spouštět      | Zástupce na`C:\Program Files (x86)\Microsoft VS Code\Code.exe`ploše ( ) v`code`systému Windows, zástupce na ploše nebo terminálu ( ) v Linuxu    |
| Související nástroje na DSVM      |     Vizuální Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Co je to?   | IDE klienta pro jazyk R   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj R     |
| Jak ji používat a spouštět      | Zástupce na`C:\Program Files\RStudio\bin\rstudio.exe`ploše ( )`/usr/bin/rstudio`v systému Windows, zástupce na ploše ( ) na Linuxu      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Kód, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Co je to?   | IDE klienta pro jazyk R   |
| Co je to?   | Webové ide pro R    |
| Podporované verze DSVM      | Linux      |
| Typická použití      |  Vývoj R     |
| Jak ji používat a spouštět      | Povolte službu s _systemctl povolit rstudio-server_a spusťte službu s _systemctl start rstudio-server_. Pak se přihlaste na\/RStudio Server na http: /your-vm-ip:8787.       |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Co je to?   | IDE klienta pro jazyk Julia   |
| Podporované verze DSVM      | Windows, Linux      |
| Typická použití      |  Vývoj Julia     |
| Jak ji používat a spouštět      | Zástupce na`C:\JuliaPro-0.5.1.1\Juno.bat`ploše ( )`/opt/JuliaPro-VERSION/Juno`v systému Windows, zástupce na ploše ( ) na Linuxu      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Kód, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Co je to?   | IDE klienta pro jazyk Pythonu    |
| Podporované verze DSVM      | Windows 2019, Linux      |
| Typická použití      |  Vývoj Pythonu     |
| Jak ji používat a spouštět      | Zástupce na`C:\Program Files\tk`ploše ( ) v systému Windows. Zástupce na`/usr/bin/pycharm`ploše ( ) na Linuxu      |
| Související nástroje na DSVM      |   Visual Studio, Visual Studio Kód, RStudio      |
