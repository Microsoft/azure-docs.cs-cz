---
title: Fondy virtuálních počítačů vědy dat – Azure | Dokumentace Microsoftu
description: Fondy virtuálních počítačů datové vědy nasadit jako sdílený prostředek pro tým
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy, vědecké zpracování týmových dat
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: gokuma
ms.openlocfilehash: 648b0818f07aca8763fa4af01380076ae307b0a2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408948"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Vytvoření sdíleného fondu z virtuálního počítače pro datové vědy

Tento článek popisuje, jak může vytvořit sdílený fond z virtuálního počítače pro datové vědy (datové) pro tým používat. Mezi výhody používání sdíleného fondu jsou lepší využití prostředků usnadnění sdílení a spolupráci a efektivnější správu DSVM prostředků. 

Mnoho metod a technologií můžete použít k vytvoření fondu datové. Tento článek se zaměřuje na fondy pro interaktivní virtuální počítače. Alternativní spravovanou výpočetní infrastruktury je Azure Machine Learning Compute. Zobrazit [nastavení cílových výpočetních prostředí](../service/how-to-set-up-training-targets.md#amlcompute) Další informace.

## <a name="interactive-vm-pool"></a>Interaktivní fond virtuálních počítačů

Fond interaktivní virtuálních počítačů, které jsou sdíleny celý tým AI/datové vědy umožňuje uživatelům přihlášení na dostupnou instanci DSVM nemuseli vyhrazenou instanci pro každou sadu uživatelů. Tato nastavení pomůžou s lepší dostupnost a efektivnější využití prostředků. 

Technologie, které použijete k vytvoření interaktivní fond virtuálních počítačů je [škálovací sady virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Škálovací sady můžete vytvářet a spravovat skupiny identických, s vyrovnáváním zatížení a automatické škálování virtuálních počítačů. 

Uživatel přihlásí do hlavního fondu adres IP nebo DNS. Škálovací sady automaticky trasy relace k dispozici DSVM ve škálovací sadě. Vzhledem k tomu, že uživatelé mají podobné prostředí bez ohledu na to, virtuální počítač jste přihlášení k, všechny instance virtuálních počítačů ve škálovací sadě připojit sdílené síťové jednotce, jako jsou sdílené složky služby soubory Azure nebo do sdílené složky systému souborů NFS. Sdílené pracovního prostoru uživatele se obvykle ukládají na sdílené úložiště, které je připojen na jednotlivých instancích. 

Můžete najít Ukázka šablony Azure Resource Manageru, která vytvoří škálovací sadu s Ubuntu DSVM instancí na [Githubu](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Vzorek [soubor parametrů](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) pro Azure Resource Manageru šablony je ve stejném umístění. 

Můžete vytvořit škálovací sadu pomocí šablony Azure Resource Manageru tak, že zadáte hodnoty pro soubor parametrů v rozhraní příkazového řádku Azure. 

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

Předchozí šablona umožňuje SSH a port JupyterHub z front-endu škálovací sady na back endovém fondu nástroje datové Ubuntu. Jako uživatel jenom přihlášení k virtuálnímu počítači na SSH nebo JupyterHub běžným způsobem. Protože instance virtuálních počítačů je možné škálovat nebo dolů dynamicky, jakýkoli stav musí být uložené v připojené sdílet soubory Azure. Stejný přístup můžete použít k vytvoření fondu datové Windows. 

[Skript, který se připojí sdílenou složku služby soubory Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) je dostupná také v úložišti Azure DataScienceVM v Githubu. Tento skript připojí sdílenou složku služby soubory Azure v zadané přípojného bodu v souboru parametrů. Skript také vytvoří obnovitelně odkazy na připojenou jednotku v počátečním uživatelským domovského adresáře. Adresáře Poznámkový blok specifické pro uživatele v rámci sdílenou složku služby soubory Azure je obnovitelné propojený `$HOME/notebooks/remote` adresáře tak, aby uživatelé přístup, spuštění a uložit jejich poznámkové bloky Jupyter. Při vytvoření dalších uživatelů na virtuální počítač, aby každý uživatel Jupyter prostoru přejděte sdílenou složku služby soubory Azure, můžete použít stejné konvence. 

Virtuální počítač škálovací sady podporují automatické škálování. Pravidla můžete nastavit na kdy vytvořit další instance a kdy vertikálně snížit kapacitu instancí. Například můžete vertikálně snížit kapacitu na nula instance ušetříte na náklady na využití cloudu hardware, když virtuální počítače nejsou vůbec nepoužívá. Stránky dokumentace ze škálovací sady virtuálních počítačů poskytuje podrobné pokyny k [automatické škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Další postup

* [Nastavte si společnou identitu](dsvm-common-identity.md)
* [Bezpečně ukládat přihlašovací údaje pro přístup ke cloudovým prostředkům](dsvm-secure-access-keys.md)















