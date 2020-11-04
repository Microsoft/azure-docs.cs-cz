---
title: Sdílené fondy
titleSuffix: Azure Data Science Virtual Machine
description: Naučte se vytvářet & nasazovat sdílený fond pro datové vědy Virtual Machines (DSVMs) jako sdílený prostředek pro tým.
keywords: obsáhlý Learning, AI, nástroje pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 789c6c36def21bfe1c2acc8797c1847455a5c86c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324399"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Vytvoření sdíleného fondu Virtual Machines pro datové vědy

V tomto článku se dozvíte, jak vytvořit sdílený fond pro datové vědy Virtual Machines (DSVMs) pro tým. Výhody použití sdíleného fondu zahrnují lepší využití prostředků, jednodušší sdílení a spolupráci a efektivnější správu prostředků DSVM.

K vytvoření fondu DSVMs můžete použít spoustu metod a technologií. Tento článek se zaměřuje na fondy pro interaktivní virtuální počítače (VM). Alternativní spravovaná výpočetní infrastruktura je Azure Machine Learning výpočetní prostředí. Další informace najdete v tématu [Vytvoření výpočetního clusteru](../how-to-create-attach-compute-cluster.md).

## <a name="interactive-vm-pool"></a>Interaktivní fond virtuálních počítačů

Fond interaktivních virtuálních počítačů, které jsou sdíleny celým týmem AI nebo pro datové vědy, umožňuje uživatelům přihlásit se k dostupné instanci DSVM místo toho, aby měli vyhrazenou instanci pro každou skupinu uživatelů. Tato instalace umožňuje lepší dostupnost a efektivnější využití prostředků.

K vytvoření interaktivního fondu virtuálních počítačů použijete technologii [Azure Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) . Sady škálování můžete použít k vytvoření a správě skupiny identických virtuálních počítačů s vyrovnáváním zatížení a automatického škálování.

Uživatel se přihlásí k IP adrese nebo adrese DNS hlavního fondu. Sada škálování automaticky směruje relaci k dostupnému DSVM v sadě škálování. Vzhledem k tomu, že uživatelé chtějí konzistentní a známé prostředí bez ohledu na virtuální počítač, ke kterému se přihlásí, všechny instance virtuálního počítače v sadě škálování připojí sdílenou síťovou jednotku, třeba sdílenou složku služby soubory Azure nebo sdílenou složku NFS (Network File System). Sdílený pracovní prostor uživatele je obvykle uložen ve sdíleném úložišti souborů, které je připojeno ke každé z těchto instancí.

Můžete najít ukázkovou Azure Resource Manager šablonu, která vytvoří škálu s Ubuntu instancemi DSVM na [GitHubu](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ukázku [souboru parametrů](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) pro šablonu Azure Resource Manager najdete ve stejném umístění.

Můžete vytvořit sadu škálování ze šablony Azure Resource Manager zadáním hodnot pro soubor parametrů v rozhraní příkazového řádku Azure CLI:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

V předchozích příkazech se předpokládá, že máte následující:

* Kopie souboru parametrů s hodnotami zadanými pro vaši instanci sady škálování.
* Počet instancí virtuálních počítačů.
* Ukazatele na sdílenou složku Azure Files.
* Přihlašovací údaje účtu úložiště, který se připojí ke každému virtuálnímu počítači.

K souboru parametrů se v příkazech odkazuje místně. Můžete také předávat parametry, které jsou vložené nebo se na ně dotazovat ve skriptu.  

Předchozí šablona umožňuje, aby se protokol SSH a JupyterHub z front-endové škály nastavily na back-end fond Ubuntu DSVMs. Jako uživatel se k virtuálnímu počítači přihlašujete pomocí Secure Shell (SSH) nebo v JupyterHub běžným způsobem. Vzhledem k tomu, že instance virtuálních počítačů je možné dynamicky škálovat směrem nahoru nebo dolů, musí být všechny stavy uložené v připojené sdílené složce Azure Files. Stejný přístup můžete použít k vytvoření fondu sady Windows DSVMs.

[Skript, který připojuje sdílenou složku služby soubory Azure,](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) je také k dispozici v úložišti Azure DataScienceVM na GitHubu. Skript připojí sdílenou složku souborů Azure v zadaném přípojném bodu v souboru parametrů. Skript také vytvoří v domovském adresáři počátečního uživatele předběžné odkazy na připojenou jednotku. Adresář notebooků konkrétního uživatele ve sdílené složce služby soubory Azure je podmíněně propojený s `$HOME/notebooks/remote` adresářem, aby uživatelé mohli přistupovat k Jupyter notebookům a jejich spouštění a ukládat je. Stejnou konvenci můžete použít při vytváření dalších uživatelů na virtuálním počítači, aby odkazovaly na pracovní prostor Jupyter jednotlivých uživatelů na sdílenou složku služby soubory Azure.

Sada škálování virtuálních počítačů podporuje automatické škálování. Můžete nastavit pravidla, kdy vytvořit další instance a kdy se má škálovat instance. Můžete například snížit kapacitu na nulové instance a ušetřit tak náklady na cloudové využití hardwaru v případě, že se virtuální počítače vůbec nepoužívají. Stránky dokumentace sady Virtual Machine Scale Sets poskytují podrobné kroky pro automatické [škálování](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení společné identity](dsvm-common-identity.md)
* [Bezpečné uložení přihlašovacích údajů pro přístup k prostředkům cloudu](dsvm-secure-access-keys.md)