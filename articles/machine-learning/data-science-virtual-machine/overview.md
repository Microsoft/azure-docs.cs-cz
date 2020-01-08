---
title: Co je Azure Data Science Virtual Machine
description: Důležité analytické scénáře a komponenty pro virtuální počítače pro datové vědy pro Windows a Linux
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a63087620d50336c67472348da3b7f37fb380635
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611964"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co je Azure Data Science Virtual Machine pro Linux a Windows?

Data Science Virtual Machine (DSVM) je přizpůsobená image virtuálního počítače na cloudové platformě Azure sestavená speciálně pro účely datové vědy. Má spoustu oblíbených nástrojů pro datové vědy, které jsou předinstalované a předem nakonfigurované tak, aby rychlé zprovoznění vytváření inteligentních aplikací pro pokročilou analýzu. 

DSVM je k dispozici na:
+ **Windows Server 2019 (Preview)**
+ **Ubuntu 18,04 LTS (Preview)**
+ Windows Server 2016
+ Ubuntu 16,04 LTS a CentOS 7,4


> [!NOTE]
> Všechny nástroje virtuálních počítačů pro obsáhlý Learning byly přeložené do Data Science Virtual Machine. 


## <a name="why-choose-the-dsvm"></a>Proč zvolit DSVM?
Cílem Data Science Virtual Machine je poskytnout odborníky na data ze všech úrovní dovedností a v rámci odvětví s bezproblémovým a předem nakonfigurovaným prostředím pro datové vědy. Místo toho, abyste si přihlásili srovnatelný pracovní prostor sami, můžete zřídit DSVM. Tato volba může ušetřit dny nebo dokonce _týdny_ v procesech instalace, konfigurace a správy balíčků. Po přidělení virtuálního počítače pro datové vědy můžete okamžitě začít pracovat na svém projektu datové vědy.

## <a name="sample-use-cases"></a>Příklady případů použití

Níže uvádíme některé běžné případy použití pro DSVM zákazníky.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Přesun úloh pro datové vědy do cloudu

DSVM poskytuje základní konfiguraci pro týmy pro datové vědy, které chtějí nahradit své místní plochy pomocí spravovaného cloudového desktopu a zajistit tak, že všichni odborníci na data v týmu mají konzistentní nastavení, které umožňuje ověřovat experimenty a propagovat spolupráci. Také snižuje náklady snížením zatížení sysadmin. Toto omezení režie šetří dobu potřebnou k vyhodnocení, instalaci a údržbě softwarových balíčků pro pokročilou analýzu.

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy
Firemní učitelé a pedagogy, kteří učí třídy pro datové vědy, obvykle poskytují image virtuálního počítače. Bitová kopie zajistí, že studenti mají konzistentní nastavení a že ukázky fungují podle předpovědi. 

DSVM vytvoří prostředí na vyžádání s konzistentním nastavením, které usnadňuje problémy s podporou a nekompatibilitou. Užitečný je zejména v případech, kdy je potřeba taková prostředí vytvářet často, například pro kratší školení.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Přizpůsobivá kapacita na vyžádání pro velké projekty
Datové vědy hackathony/soutěže nebo rozsáhlé modelování dat a průzkumy vyžadují kapacitu hardwaru škálované na více instancí, obvykle pro krátkou dobu trvání. DSVM může rychle replikovat prostředí pro datové vědy na vyžádání na serverech se škálováním na více systémů, které umožňují experimenty s vysokými výpočetními prostředky, které se dají spustit.

### <a name="custom-compute-power-for-azure-notebooks"></a>Vlastní výpočetní výkon pro Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) je bezplatná hostované služba pro vývoj, spouštění a sdílení poznámkových bloků Jupyter v cloudu bez instalace. Úroveň služby Free je omezená na 4 GB paměti a 1 GB dat. 

Pokud chcete uvolnit všechna omezení, můžete k DSVM nebo jakémukoli jinému VIRTUÁLNÍmu počítači běžícímu na serveru Jupyter připojit projekt poznámkových bloků. Pokud se k Azure Notebooks přihlašujete pomocí účtu pomocí Azure Active Directory (například podnikového účtu), poznámkové bloky se automaticky zobrazí DSVMs v jakýchkoli předplatných přidružených k tomuto účtu. K rozšíření dostupného výpočetního výkonu můžete k [Azure Notebooks připojit DSVM](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) .

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimenty a vyhodnocování
DSVM můžete použít k vyhodnocení nebo získání nových [nástrojů](./tools-included.md)pro datové vědy, zejména Projděte si některé z našich publikovaných [ukázek a návodů](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Obsáhlý Learning s grafickými procesory
Ve DSVM můžou vaše školicí modely používat algoritmy hloubkového učení na hardwaru, který je založený na procesorech GPU (Graphics Processing units). Díky možnostem škálování virtuálního počítače na platformě Azure vám DSVM pomůže používat v cloudu hardware využívající GPU podle vašich potřeb. Při výuce velkých modelů můžete přepnout na virtuální počítač s grafickým procesorem nebo když budete potřebovat vysokorychlostní výpočty a zároveň zachováte stejný disk s operačním systémem. Můžete zvolit jakoukoli SKU virtuálních počítačů s povoleným grafickým procesorem řady N s DSVM. Upozorňujeme prosím, že bezplatné účty Azure nepodporují SKU virtuálních počítačů s povoleným GPU.

Edice DSVM systému Windows Server 2016 je součástí předinstalovaného s ovladači GRAFICKÉho učení, architekturami a verzemi GPU pro hloubkové učení. V edici Linux je obsáhlý Learning na GPU povolený jak na CentOS, tak na Ubuntu DSVMs. 

Edici DSVM, CentOS nebo Windows 2016 můžete nasadit taky na virtuální počítač Azure, který není založený na GPU. V takovém případě se všechny architektury hloubkového učení vrátí do režimu CPU.
 
[Přečtěte si další informace o dostupných architekturách hloubkového učení a AI](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co je součástí DSVM?

Úplný seznam nástrojů najdete [tady](tools-included.md): DSVM Windows i Linux.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

+ Windows:
  + [Nastavení DSVM s Windows](provision-vm.md)
  + [10 věcí, které můžete provádět na DSVM Windows](vm-do-ten-things.md)

+ Linux:
  + [Nastavení DSVM pro Linux (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Nastavení DSVM pro Linux (CentOS)](linux-dsvm-intro.md)
  + [Datové vědy na DSVM pro Linux](linux-dsvm-walkthrough.md)
