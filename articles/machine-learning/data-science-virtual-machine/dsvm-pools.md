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
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: acae59922f5a46f059e19db6865491f5186139f7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103400"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Vytvoření sdíleného fondu z virtuálního počítače pro datové vědy

Tento článek popisuje, jak může vytvořit sdílený fond z virtuálního počítače pro datové vědy (datové) pro tým používat. Mezi výhody používání sdíleného fondu jsou lepší využití prostředků usnadnění sdílení a spolupráci a efektivnější správu DSVM prostředků. 

Mnoho metod a technologií můžete použít k vytvoření fondu datové. Tento článek se zaměřuje na fondy pro dávkové zpracování a interaktivní virtuálních počítačů.

## <a name="batch-processing-pool"></a>Dávkové zpracování fondu
Pokud chcete nastavit fondu datové hlavně pro spouštění úloh ve službě batch v režimu offline, můžete použít [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) nebo [Azure Batch](https://docs.microsoft.com/azure/batch/) služby. Tento článek se zaměřuje na Azure Batch AI.

Ubuntu verzi datové VĚDY je podporovaný jako některou k imagí v Azure Batch AI. V sadě Python SDK, ve kterém vytvoříte cluster Azure Batch AI, nebo rozhraní příkazového řádku Azure můžete zadat `image` parametr a nastavte ho na `UbuntuDSVM`. Můžete určit, jaký druh zpracování uzly má: založený na grafickém procesoru instancí a instancí jen procesoru, počtu procesorů a paměti [široký výběr z instancí virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) k dispozici v Azure. 

Při použití image Ubuntu DSVM v Batch AI s uzly založený na grafickém procesoru potřebné ovladače GPU a hloubkové učení architektury jsou předinstalovány. Předinstalace uloží docela dlouho při přípravě uzly služby batch. Ve skutečnosti Pokud vyvíjíte na Ubuntu DSVM interaktivně, můžete si všimnout, že uzly služby Batch AI jsou přesně stejného nastavení a konfiguraci prostředí. 

Obvykle při vytváření clusteru Batch AI, můžete také vytvořit sdílenou složku, která je připojena ve všech uzlech. Sdílené složky se používá pro vstup a výstup dat, jakož i ukládání kódu a skriptů úlohy služby batch. 

Po vytvoření clusteru Batch AI, můžete použít stejnou sadu Python SDK nebo rozhraní příkazového řádku k odesílání úloh ke spuštění. Platíte jenom čas, který se používá ke spouštění dávkových úloh. 

Další informace naleznete v tématu:
* Podrobný návod k používání [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) ke správě služby Batch AI
* Podrobný návod k používání [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) ke správě služby Batch AI
* [Batch AI recepty](https://github.com/Azure/BatchAI) , která ukazují, jak používat různé AI a hloubkového učení architektury s využitím služby Batch AI

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















