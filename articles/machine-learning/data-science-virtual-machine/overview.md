---
title: Co je Azure Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Přehled Azure Data Science Virtual Machine – snadné použití virtuálního počítače na cloudové platformě Azure s předinstalovanými a nakonfigurovanými nástroji a knihovnami pro účely datové vědy.
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 7bcee0f571fe42e4c00499f188e35d0e0ff2ebe7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322493"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co je Azure Data Science Virtual Machine pro Linux a Windows?

Data Science Virtual Machine (DSVM) je přizpůsobená image virtuálního počítače na cloudové platformě Azure sestavená speciálně pro účely datové vědy. Má spoustu oblíbených nástrojů pro datové vědy, které jsou předinstalované a předem nakonfigurované, aby bylo možné začít sestavovat inteligentní aplikace pro pokročilou analýzu.

DSVM je k dispozici na:

+ Windows Server 2019
+ Ubuntu 18,04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Porovnání s Azure Machine Learning

DSVM je přizpůsobená image virtuálního počítače pro datové vědy, ale [Azure Machine Learning](../overview-what-is-azure-ml.md) (AzureML) je koncová platforma, která zahrnuje:

+ Plně spravované výpočetní prostředky
  + Výpočetní instance
  + Výpočetní clustery pro úlohy distribuované v ML
  + Odvození clusterů pro bodování v reálném čase
+ Úložiště dat (například blob, ADLS Gen2, databáze SQL)
+ Experimenty sledování
+ Správa modelů
+ Notebooks
+ Prostředí (Správa závislostí conda a R)
+ Označování
+ Kanály (automatizace koncových postupů pro datové vědy)

### <a name="comparison-with-azureml-compute-instances"></a>Porovnání s výpočetními instancemi AzureML

[Azure Machine Learning výpočetní instance](../concept-compute-instance.md) jsou plně nakonfigurovaná a __spravovaná__ image virtuálního počítače, zatímco DSVM je __nespravovaný__ virtuální počítač.

Klíčové rozdíly mezi těmito dvěma nabídkami produktů jsou popsané níže:


|Příznak |Datové vědy<br>Virtuální počítač |AzureML<br>Výpočetní instance  | 
|---------|---------|---------|
| Plně spravovaná | Ne        | Ano        |
|Podpora jazyků     |  Python, R, Helena, SQL, C#,<br> Java, Node.js, F #       | Python a R        |
|Operační systém     | Ubuntu<br>Windows         |    Ubuntu     |
|Možnost předem nakonfigurovaného GPU     |  Ano       |    Ano     |
|Možnost horizontálního navýšení kapacity | Ano | Ano |
|Přístup přes SSH    | Ano        |    Ano     |
|Přístup přes protokol RDP    | Ano        |     Ne    |
|Integrované<br>Hostované poznámkové bloky     |   Ne<br>(vyžaduje další konfiguraci.)      |      Ano   |
|Integrované jednotné přihlašování     | Ne <br>(vyžaduje další konfiguraci.)         |    Ano     |
|Integrovaná spolupráce     | Ne         | Ano        |
|Předem nainstalované nástroje     |  Jupyter (Lab), RStudio Server, VSCode,<br> Visual Studio, PyCharm, Juno,<br>Power BI Desktop, SSMS, <br>Systém Microsoft Office 365, přechod na Apache       |     Jupyter (testovací prostředí)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Ukázkové případy použití

Níže uvádíme některé běžné případy použití pro DSVM zákazníky.

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimenty a vyhodnocování

DSVM můžete použít k vyhodnocení nebo získání nových [nástrojů](./tools-included.md)pro datové vědy, zejména Projděte si některé z našich publikovaných [ukázek a návodů](./dsvm-samples-and-walkthroughs.md).

### <a name="deep-learning-with-gpus"></a>Hluboké učení s grafickými procesory

Ve DSVM můžou vaše školicí modely používat algoritmy hloubkového učení na hardwaru, který je založený na procesorech GPU (Graphics Processing units). Díky možnostem škálování virtuálního počítače na platformě Azure vám DSVM pomůže používat v cloudu hardware využívající GPU podle vašich potřeb. Pokud trénujete velké modely nebo potřebujete vysokorychlostní výpočty, můžete přepnout na virtuální počítač založený na GPU a stále zůstat na stejném disku s operačním systémem. Můžete vybrat libovolnou z řad SKU virtuálních počítačů s podporou řady N Series s DSVM. Poznámka: skladové jednotky virtuálních počítačů s podporou GPU nejsou v bezplatných účtech Azure podporované.

Edice Windows DSVM jsou předinstalované s ovladači GPU, architekturami a verzemi prostředí pro hloubkové učení. V edicích pro Linux je na Ubuntu DSVMs povolený obsáhlý Learning na GPU. 

Můžete také nasadit edice Ubuntu nebo Windows DSVM na virtuální počítač Azure, který není založený na GPU. V takovém případě se všechny architektury hloubkového učení vrátí do režimu CPU.

[Přečtěte si další informace o dostupných architekturách hloubkového učení a AI](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy

Firemní školitelé a učitelé, kteří vyučují kurzy datových věd, obvykle dodají image virtuálního počítače. Tato image zajišťuje, že studenti mají konzistentní nastavení a že ukázky fungují podle předpovědi.

DSVM vytvoří prostředí na vyžádání s konzistentním nastavením, které usnadňuje problémy s podporou a nekompatibilitou. Užitečný je zejména v případech, kdy je potřeba taková prostředí vytvářet často, například pro kratší školení.


## <a name="whats-included-on-the-dsvm"></a>Co je součástí DSVM?

Úplný seznam nástrojů na DSVMs Windows i Linux najdete [tady](tools-included.md).

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

+ Windows:
  + [Nastavení DSVM s Windows](provision-vm.md)
  + [Datové vědy na DSVM Windows](vm-do-ten-things.md)

+ Linux:
  + [Nastavení DSVM pro Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Datové vědy na DSVM pro Linux](linux-dsvm-walkthrough.md)