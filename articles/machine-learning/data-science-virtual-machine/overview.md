---
title: Co je virtuální počítač Azure Data Science
titleSuffix: Azure Data Science Virtual Machine
description: Důležité analytické scénáře a komponenty pro virtuální počítače pro datové vědy pro Windows a Linux
keywords: nástroje pro datové vědy, virtuální počítač pro datové vědy, datové vědy pro linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a5fbcc1eef8717fdb1aa7f914c3e0ba6594fc27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281795"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co je virtuální počítač Azure Data Science pro Linux a Windows?

Virtuální počítač pro datové vědy (DSVM) je přizpůsobená image virtuálního počítače na cloudové platformě Azure vytvořené speciálně pro zpracování datové vědy. Má mnoho populárních nástrojů pro datovou vědu předinstalovaných a předkonfigurovaných pro jumpstart budování inteligentních aplikací pro pokročilé analýzy. 

DSVM je k dispozici na adrese:
+ **Windows Server 2019**
+ **Ubuntu 18.04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS a CentOS 7.4


> [!NOTE]
> Všechny nástroje virtuálních zařízení pro hloubkové učení byly přeloženy do virtuálního počítače pro datové vědy. 


## <a name="why-choose-the-dsvm"></a>Proč si vybrat DSVM?
Cílem virtuálního počítače pro datové vědy je poskytnout odborníkům na data všech úrovní dovedností a napříč průmyslovými odvětvími prostředí bez tření, které je bez tření. Místo zavedení srovnatelné pracovní prostor na vlastní pěst, můžete zřídit DSVM. Tato volba vám může ušetřit dny nebo dokonce _týdny_ na procesech instalace, konfigurace a správy balíčků. Po přidělení virtuálního počítače pro datové vědy můžete okamžitě začít pracovat na svém projektu datové vědy.

## <a name="sample-use-cases"></a>Ukázkové případy použití

Níže ilustrujeme některé běžné případy použití pro zákazníky DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Přesouvání úloh datové vědy do cloudu

DSVM poskytuje základní konfiguraci pro týmy datové vědy, které chtějí nahradit své místní plochy spravovanou cloudovou plochou, což zajišťuje, že všichni datoví vědci v týmu mají konzistentní nastavení, pomocí kterého můžete ověřit experimenty a podpořit spolupráci. Snižuje také náklady snížením zátěže sysadmin. Toto snížení zátěže šetří čas potřebný k vyhodnocení, instalaci a údržbě softwarových balíčků pro pokročilé analýzy.

### <a name="data-science-training-and-education"></a>Školení a vzdělávání v oblasti datové vědy
Podnikoví školitelé a pedagogové, kteří vyučují kurzy datových věd, obvykle poskytují image virtuálního počítače. Obrázek zajišťuje, že studenti mají konzistentní nastavení a že ukázky fungují předvídatelně. 

DSVM vytváří prostředí na vyžádání s konzistentní nastavení, které usnadňuje podporu a problémy s nekompatibilitou. Užitečný je zejména v případech, kdy je potřeba taková prostředí vytvářet často, například pro kratší školení.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Přizpůsobivá kapacita na vyžádání pro velké projekty
Hackathony/soutěže datové vědy nebo rozsáhlé modelování a zkoumání dat vyžadují škálovnou kapacitu hardwaru, obvykle na krátkou dobu. DSVM může pomoci replikovat prostředí datové vědy rychle na vyžádání, na vertikálně naváděných serverů, které umožňují experimenty, které mohou spustit vysoce výkonné výpočetní prostředky.

### <a name="custom-compute-power-for-azure-notebooks"></a>Vlastní výpočetní výkon pro poznámkové bloky Azure
[Poznámkové bloky Azure](../../notebooks/azure-notebooks-overview.md) je bezplatná hostovaná služba pro vývoj, spouštění a sdílení poznámkových bloků Jupyter v cloudu bez instalace. Úroveň bezplatné služby je omezena na 4 GB paměti a 1 GB dat. 

Chcete-li uvolnit všechna omezení, můžete připojit projekt poznámkových bloků k dsvm nebo jinému virtuálnímu počítači spuštěnému na serveru Jupyter. Pokud se k poznámkovým blokům Azure přihlásíte pomocí účtu pomocí Služby Azure Active Directory (například firemní účet), poznámkové bloky automaticky zobrazí dsvmve všechna předplatná přidružená k tomuto účtu. K [poznámkovým blokům Azure můžete připojit DSVM](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) a rozšířit tak dostupný výpočetní výkon.

### <a name="short-term-experimentation-and-evaluation"></a>Krátkodobé experimenty a vyhodnocování
Pomocí dsvm můžete vyhodnotit nebo se naučit nové nástroje pro datové [vědy](./tools-included.md), zejména procházením některých našich publikovaných [ukázek a návodů](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Hluboké učení s GPU
V DSVM vaše trénovací modely můžete použít algoritmy hlubokého učení na hardware, který je založen na grafických procesorů (GPU). Díky využití možností škálování virtuálních počítačů platformy Azure vám DSVM pomůže používat hardware založený na GPU v cloudu podle vašich potřeb. Virtuální počítače založené na GPU můžete přepnout na virtuální počítače založené na GPU, když trénujete velké modely nebo když potřebujete vysokorychlostní výpočty při zachování stejného disku operačního systému. Můžete zvolit libovolný virtuální počítač s podporou n gpu s dsvm. Upozorňujeme, že bezplatné účty Azure nepodporují virtuální počítače s povoleným gpu.

Edice Systému DSVM pro Windows Server 2016 je předinstalována s ovladači GPU, architekturami a verzemi GPU architektur hlubokého učení. Na edici Linux je povoleno hluboké učení na GPU jak na Zařízeních CentOS, tak na Ubuntu DSVM. 

Můžete také nasadit Ubuntu, CentOS nebo Windows 2016 edici DSVM do virtuálního počítače Azure, který není založený na GPU. V tomto případě všechny hluboké učení rozhraní se vrátí do režimu procesoru.
 
[Přečtěte si další informace o dostupných architekturách hloubkového učení a umělou ai.](dsvm-tools-deep-learning-frameworks.md)

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Co je součástí DSVM?

Podívejte se na úplný seznam nástrojů na obou Windows a Linux DSVM je [zde](tools-included.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v těchto článcích:

+ Windows:
  + [Nastavení DSVM s Windows](provision-vm.md)
  + [Deset věcí, které můžete dělat na Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Nastavení Linuxu DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Nastavení Linuxu DSVM (CentOS)](linux-dsvm-intro.md)
  + [Datová věda na Linuxu DSVM](linux-dsvm-walkthrough.md)
