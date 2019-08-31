---
title: Sdílené fondy
titleSuffix: Azure Data Science Virtual Machine
description: Nasazení fondů DSVMs jako sdíleného prostředku pro tým
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 929040b5dc8650a757fb9c0da58cb82a76a72b5b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195628"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Vytvoření sdíleného fondu z virtuálního počítače pro datové vědy

V tomto článku se dozvíte, jak vytvořit sdílený fond pro datové vědy Virtual Machines (DSVMs) pro tým. Výhody použití sdíleného fondu zahrnují lepší využití prostředků, jednodušší sdílení a spolupráci a efektivnější správu prostředků DSVM.

Mnoho metod a technologií můžete použít k vytvoření fondu datové. Tento článek se zaměřuje na fondy pro interaktivní virtuální počítače (VM). Alternativní spravovaná výpočetní infrastruktura je Azure Machine Learning výpočetní prostředí. Další informace najdete v tématu [nastavení cílových výpočetních](../service/how-to-set-up-training-targets.md#amlcompute)prostředků.

## <a name="interactive-vm-pool"></a>Interaktivní fond virtuálních počítačů

Fond interaktivní virtuálních počítačů, které jsou sdíleny celý tým AI/datové vědy umožňuje uživatelům přihlášení na dostupnou instanci DSVM nemuseli vyhrazenou instanci pro každou sadu uživatelů. Tato instalace umožňuje lepší dostupnost a efektivnější využití prostředků.

K vytvoření interaktivního fondu virtuálních počítačů použijete technologii [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) . Škálovací sady můžete vytvářet a spravovat skupiny identických, s vyrovnáváním zatížení a automatické škálování virtuálních počítačů.

Uživatel přihlásí do hlavního fondu adres IP nebo DNS. Škálovací sady automaticky trasy relace k dispozici DSVM ve škálovací sadě. Vzhledem k tomu, že uživatelé chtějí konzistentní a známé prostředí bez ohledu na virtuální počítač, ke kterému se přihlásí, všechny instance virtuálního počítače v sadě škálování připojí sdílenou síťovou jednotku, třeba sdílenou složku služby soubory Azure nebo sdílenou složku NFS (Network File System). Sdílené pracovního prostoru uživatele se obvykle ukládají na sdílené úložiště, které je připojen na jednotlivých instancích.

Můžete najít Ukázka šablony Azure Resource Manageru, která vytvoří škálovací sadu s Ubuntu DSVM instancí na [Githubu](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ukázku [souboru parametrů](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) pro šablonu Azure Resource Manager najdete ve stejném umístění.

Můžete vytvořit sadu škálování ze šablony Azure Resource Manager zadáním hodnot pro soubor parametrů v rozhraní příkazového řádku Azure CLI:

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Výše uvedených příkazů se předpokládá, že máte:
* Zkopírujte soubor parametrů s hodnotami určenými pro vaše instance škálovací sady.
* Počet instancí virtuálních počítačů.
* Sdílet odkazy na soubory Azure.
* Přihlašovací údaje pro účet úložiště, který se připojí na každém virtuálním počítači.

Soubor parametrů v příkazech odkazuje místně. Můžete také předat parametry vložené nebo řádku pro ně ve skriptu.  

Předchozí šablona umožňuje SSH a port JupyterHub z front-endu škálovací sady na back endovém fondu nástroje datové Ubuntu. Jako uživatel se k virtuálnímu počítači přihlašujete pomocí Secure Shell (SSH) nebo v JupyterHub běžným způsobem. Vzhledem k tomu, že instance virtuálních počítačů je možné dynamicky škálovat směrem nahoru nebo dolů, musí být všechny stavy uložené v připojené sdílené složce Azure Files. Stejný přístup můžete použít k vytvoření fondu datové Windows.

[Skript, který se připojí sdílenou složku služby soubory Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) je dostupná také v úložišti Azure DataScienceVM v Githubu. Tento skript připojí sdílenou složku služby soubory Azure v zadané přípojného bodu v souboru parametrů. Skript také vytvoří obnovitelně odkazy na připojenou jednotku v počátečním uživatelským domovského adresáře. Adresář notebooků konkrétního uživatele ve sdílené složce služby soubory Azure je podmíněně propojený `$HOME/notebooks/remote` s adresářem, aby uživatelé mohli přistupovat k Jupyter notebookům a jejich spouštění a ukládat je. Při vytvoření dalších uživatelů na virtuální počítač, aby každý uživatel Jupyter prostoru přejděte sdílenou složku služby soubory Azure, můžete použít stejné konvence.

Virtuální počítač škálovací sady podporují automatické škálování. Můžete nastavit pravidla, kdy vytvořit další instance a kdy se má škálovat instance. Například můžete vertikálně snížit kapacitu na nula instance ušetříte na náklady na využití cloudu hardware, když virtuální počítače nejsou vůbec nepoužívá. Stránky dokumentace ze škálovací sady virtuálních počítačů poskytuje podrobné pokyny k [automatické škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Další postup

* [Nastavte si společnou identitu](dsvm-common-identity.md)
* [Bezpečně ukládat přihlašovací údaje pro přístup ke cloudovým prostředkům](dsvm-secure-access-keys.md)















