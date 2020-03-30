---
title: Sdílené fondy
titleSuffix: Azure Data Science Virtual Machine
description: Zjistěte, jak vytvořit & nasadit sdílený fond virtuálních počítačů pro datové vědy (DSVM) jako sdílený prostředek pro tým.
keywords: hluboké učení, AI, nástroje pro datovou vědu, virtuální stroj pro datové vědy, geoprostorová analytika, proces vědecké analýzy týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477336"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Vytvoření sdíleného fondu virtuálních počítačů pro datové vědy

V tomto článku se dozvíte, jak vytvořit sdílený fond virtuálních počítačů pro datové vědy (DSVM) pro tým. Mezi výhody použití sdíleného fondu patří lepší využití prostředků, snadnější sdílení a spolupráce a efektivnější správa prostředků DSVM.

K vytvoření fondu dsvmů můžete použít mnoho metod a technologií. Tento článek se zaměřuje na fondy pro interaktivní virtuální počítače (VMs). Alternativní spravovanou výpočetní infrastrukturou je Azure Machine Learning Compute. Další informace naleznete v tématu [Nastavení výpočetních cílů](../how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Fond interaktivních virtuálních her

Fond interaktivních virtuálních počítačů, které jsou sdíleny celým týmem ai/datové vědy umožňuje uživatelům přihlásit se k dostupné instanci DSVM namísto vyhrazené instance pro každou sadu uživatelů. Toto nastavení umožňuje lepší dostupnost a efektivnější využití prostředků.

Pomocí technologie [škálovacích sad virtuálních strojů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) se vytvoří interaktivní fond virtuálních her. Škálovací sady můžete použít k vytvoření a správě skupiny identických virtuálních a automatických škálování virtuálních mích.

Uživatel se přihlásí k adrese IP nebo DNS hlavního fondu. Škálovací sada automaticky směruje relaci do dostupného dsvm v škálovací sadě. Vzhledem k tomu, že uživatelé chtějí konzistentní a známé prostředí bez ohledu na virtuální počítač, ke kterým se přihlašují, všechny instance virtuálního počítače ve škálovací sadě připojí sdílenou síťovou jednotku, jako je sdílená složka Azure Files nebo sdílená síťová síťová síťová síťová síťová složka. Sdílený pracovní prostor uživatele je obvykle uložen ve sdíleném úložišti souborů, které je připojeno ke každé instancí.

Najdete ukázkovou šablonu Azure Resource Manageru, která vytvoří škálovací sadu s instancemi Ubuntu DSVM na [GitHubu](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ukázku [souboru parametrů](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) pro šablonu Azure Resource Manager uvidíte ve stejném umístění.

Škálovací sadu můžete vytvořit ze šablony Azure Resource Manager zadáním hodnot pro soubor parametrů v rozhraní příkazového řádku Azure:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Předchozí příkazy předpokládají, že máte:

* Kopie souboru parametrů s hodnotami určenými pro vaši instanci škálovací sady.
* Počet instancí virtuálních virtuálních lidí.
* Ukazatele na sdílené složky Azure Files.
* Pověření pro účet úložiště, který bude připojen na každém virtuálním počítači.

Soubor parametrů je odkazován místně v příkazech. Můžete také předat parametry vsazené nebo výzvu pro ně ve skriptu.  

Předchozí šablona umožňuje Port SSH a JupyterHub z front-end škálovací sady do back-endového fondu Ubuntu DSVMs. Jako uživatel se přihlásíte k virtuálnímu počítači na zabezpečeném prostředí (SSH) nebo na JupyterHub normálním způsobem. Vzhledem k tomu, že instance virtuálních počítače lze škálovat nahoru nebo dolů dynamicky, musí být uložen libovolný stav ve sdílené složce připojené soubory Azure. Stejný přístup můžete použít k vytvoření fondu windows DSVMs.

[Skript, který připojí sdílenou složku Azure Files,](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) je taky dostupný v úložišti Azure DataScienceVM na GitHubu. Skript připojí sdílené složky Azure Files v zadaném přípojném bodu v souboru parametrů. Skript také vytvoří měkké odkazy na připojenou jednotku v domovském adresáři počátečního uživatele. Adresář poznámkového bloku specifický pro uživatele ve sdílené `$HOME/notebooks/remote` složce Soubory Azure je měkký propojený s adresářem, takže uživatelé mohou přistupovat ke svým poznámkovým blokům Jupyter, spouštět je a ukládat. Stejnou konvenci můžete použít při vytváření dalších uživatelů na virtuálním počítači, aby se každý uživatelský pracovní prostor Jupyter ubírá na sdílenou složku Azure Files.

Škálovací sady virtuálních strojů podporují automatické škálování. Můžete nastavit pravidla o tom, kdy vytvořit další instance a kdy škálovat instance. Můžete například škálovat na nulu, abyste ušetřili náklady na využití cloudového hardwaru, když se virtuální počítače vůbec nepoužívají. Stránky dokumentace škálovacísady virtuálních strojů poskytují podrobné kroky pro [automatické škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Další kroky

* [Nastavení společné identity](dsvm-common-identity.md)
* [Bezpečné ukládání přihlašovacích údajů pro přístup ke cloudovým prostředkům](dsvm-secure-access-keys.md)
