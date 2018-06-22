---
title: Data virtuálního počítače vědecké účely fondy - Azure | Microsoft Docs
description: Nasazení fondy virtuálních počítačů, dat vědecké účely jako sdílený prostředek pro týmu
keywords: přímý učení AI datové vědy nástroje, data vědecké účely virtuální počítač, geoprostorové analýzy, proces team dat vědecké účely
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309394"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Vytvoření sdílené fondu virtuálních počítačů Data vědecké účely

Tento článek popisuje, jak můžete vytvořit sdílený fond datové vědy virtuálních počítačů (DSVMs) o týmu používat. Výhody použití sdílenému fondu jsou lepší využití prostředků, usnadnění sdílení a spolupráce a efektivnější správy DSVM prostředků. 

Můžete vytvořit fond DSVMs mnoho metody a technologie. Tento článek se zaměřuje na fondy pro dávkové zpracování a interaktivní virtuálních počítačů.

## <a name="batch-processing-pool"></a>Zpracování dávky fondu
Pokud chcete nastavit fond DSVMs především ke spuštění úloh v dávce do režimu offline, můžete použít [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) nebo [Azure Batch](https://docs.microsoft.com/azure/batch/) služby. Tento článek se zaměřuje na Azure Batch AI.

Ubuntu edice DSVM je podporovaný jako jednu z imagí v Azure Batch AI. V rozhraní příkazového řádku Azure nebo Python SDK, kde můžete vytvořit cluster Azure Batch AI, můžete zadat `image` parametr a nastavte ji na `UbuntuDSVM`. Můžete určit, jaký druh zpracování uzly má: bázi GPU instancí versus jen procesoru instancí, počet procesorů a paměti z [celý choice instancí virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) dostupné v Azure. 

Při použití bitovou kopii Ubuntu DSVM v Batch AI s bázi GPU uzly jsou předinstalována potřebné ovladače grafického procesoru a přímý učení architektury. Předinstalace uloží mnoho času při přípravě uzlů batch. Ve skutečnosti, pokud vyvíjíte na Ubuntu DSVM interaktivně, můžete si všimnout, že uzly Batch AI jsou přesně stejné nastavení a konfiguraci prostředí. 

Obvykle při vytváření clusteru s podporou Batch AI můžete také vytvořit sdílené složce, která je připojena všemi uzly. Sdílené složky se používá pro vstupní a výstupní data, jakož i ukládání dávkové úlohy kód nebo skripty. 

Po vytvoření clusteru s podporou Batch AI, můžete použít stejné rozhraní příkazového řádku nebo Python SDK k odesílání úloh do spustit. Platíte jenom čas, kdy se používá ke spuštění úlohy batch. 

Další informace naleznete v tématu:
* Podrobný návod k používání [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) ke správě Batch AI
* Podrobný návod k používání [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) ke správě Batch AI
* [Batch AI recepty](https://github.com/Azure/BatchAI) která ukazují, jak používat různé AI a přímý učení rozhraní s Batch AI

## <a name="interactive-vm-pool"></a>Interaktivní fondu virtuálních počítačů

Fond interaktivní virtuálních počítačů, které jsou sdíleny celý tým vědecké účely AI nebo dat umožňuje uživatelům přihlášení k dostupné instanci DSVM místo nutnosti vyhrazenou instanci pro každou sadu uživatelů. Tento instalační program pomáhá s lepší dostupnost a efektivnější využití prostředků. 

Je technologie, kterou použijete k vytvoření fondu virtuálních počítačů interaktivní [sady škálování virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Můžete vytvořit a spravovat skupiny identické, Vyrovnávání zatížení sítě a automatické škálování virtuálních počítačů sady škálování. 

Uživatel se přihlásí do hlavního fondu DNS nebo IP adresu. Měřítko nastavit automaticky trasy relace na k dispozici DSVM v sadě škálování. Vzhledem k tomu, že uživatelé mají podobné prostředí bez ohledu na virtuální počítač se přihlašují k, všechny instance virtuálního počítače ve škálovací sadě připojit sdílené síťové jednotce, jako je Azure Files sdílené složky nebo sdílené složky NFS. Sdílené prostoru uživatele se obvykle ukládají na sdílená úložiště, které je připojena v jednotlivých instancí. 

Můžete najít šablonu Azure Resource Manager ukázka, která vytvoří nastavit s Ubuntu DSVM instance škále [Githubu](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ukázka [soubor parametrů](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) pro Azure Resource Manager šablona je ve stejném umístění. 

Můžete vytvořit sad z šablony Azure Resource Manager zadáním hodnot parametru souboru v rozhraní příkazového řádku Azure škálování. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Předchozí příkazů se předpokládá, že máte:
* Kopírování souboru parametrů s hodnoty zadané pro vaše instance škálovací sady.
* Počet instancí virtuálního počítače.
* Ukazatelé na soubory Azure sdílet.
* Přihlašovací údaje pro účet úložiště, který se připojí na každý virtuální počítač. 

Soubor parametrů v příkazech odkazuje místně. Můžete také předat parametry vložené nebo řádku u nich ve vašem skriptu.  

Šablona v předchozím příkladu umožňuje SSH a port JupyterHub z front-endu měřítka nastavena na fond back-end Ubuntu DSVMs. Jako uživatel je právě přihlásit k virtuálnímu počítači na SSH nebo JupyterHub běžným způsobem. Protože instance virtuálních počítačů můžete škálovat nebo dolů dynamicky jakýkoli stav musí být uloženy ve připojené soubory Azure sdílet. Vytvoření fondu Windows DSVMs můžete použít ve stejný přístup. 

[Skript, který připojí sdílenou složku Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) je také k dispozici v úložišti Azure DataScienceVM v Githubu. Skript připojí sdílenou složku Azure soubory v zadané přípojný bod v souboru parametrů. Skript také vytvoří logicky odkazy na připojenou jednotku v počátečním uživatelským domovský adresář. Adresář poznámkového bloku specifický pro uživatele ve sdílené složce souborů Azure je logicky propojené s `$HOME/notebooks/remote` adresář, aby mohli uživatelé přístup, spuštění a uložit jejich poznámkové bloky Jupyter. Můžete použít stejné konvence při vytvoření dalších uživatelů ve virtuálním počítači tak, aby odkazoval prostoru Jupyter každý uživatel ke sdílené složce souborů Azure. 

Podpora automatického škálování sadách škálování virtuálních počítačů. Pravidla můžete nastavit na kdy vytvořit další instance a kdy škálovat dolů instancí. Například můžete škálovat na nula instance, které chcete uložit v cloudu hardwaru nákladům, když virtuální počítače nejsou vůbec použita. Na stránkách dokumentace sady škálování virtuálního počítače poskytují podrobné pokyny pro [automatické škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Další postup

* [Nastavit společnou identitu](dsvm-common-identity.md)
* [Bezpečně uložit přihlašovací údaje pro přístup k prostředkům cloudu](dsvm-secure-access-keys.md)















