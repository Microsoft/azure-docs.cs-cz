---
title: Datové vědy virtuálního počítače fondy - Azure | Microsoft Docs
description: Nasazení fondy vědecké účely Data virtuálního počítače jako sdílený prostředek pro tým
keywords: přímý učení AI datové vědy nástroje, data vědecké účely virtuální počítač, geoprostorové analýzy, proces team dat vědecké účely
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 625154378fecbd4d45904fa5669adb866fc9487c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Vytváření sdílenému fondu virtuálních počítačů Data vědecké účely

Tento článek popisuje, jak lze vytvořit sdílený fond z datové vědy virtuálních počítačů (DSVM) pro použití tým. Výhodou použití sdílenému fondu je lepší využití prostředků usnadňují sdílení a spolupráce a umožňují tak prostředky DSVM efektivněji spravovat. 

Existuje mnoho způsobů a různých technologií, které lze použít k vytvoření fondu DSVM.  Toto jsou základní scénáře:

* Fond pro dávkové zpracování
* Fond pro interaktivní virtuální počítače

## <a name="batch-processing-pool"></a>Dávkové zpracování fondu
Pokud chcete nastavit fond DSVM především ke spuštění úloh v dávce do režimu offline, pak můžete použít [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) služby nebo [Azure Batch](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Azure Batch AI
Ubuntu edice DSVM je podporovaný jako jednu z imagí v Azure Batch AI. V rozhraní příkazového řádku Azure nebo Python SDK, kde vytvoříte Azure Batch AI clusteru, můžete zadat ```image``` parametr a nastavte ji na ```UbuntuDSVM```. Můžete určit, jaký druh zpracování uzly má – procesoru vs bázi GPU instancí jenom instance, počtu CPU, paměť [celý choice instancí virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) dostupné v Azure. Když použijete bitovou kopii Ubuntu DSVM v Batch AI s bázi GPU uzly všechny potřebné ovladače grafického procesoru a hloubkové learning architektury jsou předinstalována ušetřit mnoho času při přípravě uzlů batch. Ve skutečnosti Pokud vyvíjíte na Ubuntu DSVM interaktivně, si všimněte, že uzly Batch AI jsou přesně stejné nastavení a konfiguraci prostředí. Obvykle při vytvoření clusteru Batch AI můžete taky vytvořit sdílené složky, která je připojena všemi uzly a používá se pro vstupní a výstupní data, jakož i ukládání kód úlohy batch / skriptů. 

Po vytvoření clusteru Batch AI můžete použít stejné rozhraní příkazového řádku nebo Python SDK k odesílání úloh do spustit. Platíte jenom čas, kdy se používá ke spuštění úlohy batch. 

#### <a name="more-information"></a>Další informace
* Podrobný návod k používání [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) pro správu Batch AI
* Podrobný návod k používání [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) pro správu Batch AI
* [Batch AI recepty](https://github.com/Azure/BatchAI) jsou k dispozici, který ukazuje, jak používat různé learning AI nebo přímým rozhraní s Batch AI.

## <a name="interactive-vm-pool"></a>Interaktivní fondu virtuálních počítačů

Fond interaktivní DSVMs, které jsou sdíleny celou AI / team vědecké účely dat umožňuje uživatelům přihlášení k dostupné instanci DSVM místo nutnosti vyhrazenou instanci pro každou sadu uživatelů. To pomáhá s lepší dostupnost a efektivnější využití prostředků. 

Technologie používá k vytvoření fondu interaktivní virtuálního počítače je [sady škálování virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS), která vám umožňuje vytvářet a spravovat skupiny identické, Vyrovnávání zatížení a automatické škálování virtuálních počítačů. Se uživatel přihlásí do hlavního fondu DNS nebo IP adresu. Měřítko nastavit automaticky trasy relace na k dispozici DSVM ve Škálovací sadě. Vzhledem k tomu, že uživatel mají podobné prostředí bez ohledu na virtuální počítač se přihlašují do, připojí všechny instance virtuálního počítače ve Škálovací sadě sdílené síťové jednotce jako soubory Azure nebo sdílené složky NFS. Sdílené prostoru uživatele se obvykle ukládají na sdílená úložiště, které je připojena v jednotlivých instancí. 

Ukázka šablony Azure Resource Manager, která vytvoří sady škálování virtuálního počítače s Ubuntu DSVMs instance naleznete na [githubu](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ukázka šablony Azure Resource Manager [soubor parametrů](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) jsou tu taky ve stejném umístění. 

Můžete vytvořit sad z šablony Azure Resource Manager zadáním vhodný hodnoty pro parametr soubor pomocí rozhraní příkazového řádku Azure škálování virtuálních počítačů. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Výše uvedených příkazů se předpokládá, že máte kopii souboru parametrů s hodnoty zadané pro vaše instance škálovací sadu virtuálních počítačů, počet instancí virtuálního počítače, ukazatelé na soubory Azure spolu s přihlašovací údaje pro účet úložiště, který se připojí na každý virtuální počítač. Soubor parametrů odkazuje místně v příkazu výše. Můžete také předat parametry vložené nebo řádku u nich ve vašem skriptu.  

Výše uvedené šablony umožňuje SSH a port Jupyterhub z front-endu škálování virtuálních počítačů na fond back-end Ubuntu DSVMs.  Jako uživatel je právě přihlásit k virtuálnímu počítači na SSH nebo JupyterHub běžným způsobem. Vzhledem k tomu, že instance virtuálních počítačů můžete škálovat nebo dolů dynamicky jakýkoli stav musí být uloženy ve připojené soubory Azure sdílet. Ve stejný přístup umožňuje vytvořit fond DSVMs systému Windows. 

[Skript, který připojí Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) je také dostupná na Githubu DataScienceVM Azure. Kromě připojení Azure soubory v zadané přípojný bod v souboru parametrů, vytvoří také další softwarové odkazy na připojenou jednotku v domovské adresáře uživatele počáteční a adresáře poznámkového bloku specifický pro uživatele v rámci sdílené soubory Azure je logicky spojen s ```$HOME/notebooks/remote``` directory povolení uživatele pro přístup, spuštění a uložit jejich poznámkové bloky Jupyter.  Stejné konvence lze použít při vytvoření dalších uživatelů ve virtuálním počítači tak, aby odkazovaly do pracovního prostoru Jupyter jednotlivých uživatelů ke sdíleným souborům Azure. 

Azure škálování virtuálních počítačů nastaví podpora automatického škálování, kde můžete nastavit pravidla, když chcete-li vytvořit další instance a v části co okolností škálování dolů instancí včetně převedení dolů nulový počet instancí pro uložení v cloudu využití náklady na hardware při virtuální počítače nejsou vůbec použita. . Na stránkách dokumentace škálovatelné sady virtuálních počítačů poskytují podrobné pokyny pro [automatické škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Další postup

* [Nastavit společné Identity](dsvm-common-identity.md)
* [Bezpečně uložit přihlašovací údaje pro přístup k prostředkům cloudu](dsvm-secure-access-keys.md)















