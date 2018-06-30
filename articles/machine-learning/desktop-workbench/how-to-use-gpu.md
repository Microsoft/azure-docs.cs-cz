---
title: Jak používat GPU pro Azure Machine Learning | Microsoft Docs
description: Tento článek popisuje, jak používat grafické zpracování jednotky (GPU) ke cvičení hluboké neuronové sítě v nástroji Azure Machine Learning Workbench.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 852f514a36ea640f478c5cc5ebbb137ca962703a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115928"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Jak používat GPU v Azure Machine Learning
Grafické zpracování jednotky (GPU) se často používá ke zpracování výpočetně náročné úlohy, které obvykle může dojít při tréninku určité modely hluboké neuronové sítě. Pomocí grafickými procesory, můžete zkrátit dobu školení modelů výrazně. V tomto dokumentu, zjistíte, jak nakonfigurovat Azure ML Workbench používat [DSVM (datové vědy virtuální počítač)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) vybaven grafickými procesory jako cíl provádění. 

## <a name="prerequisites"></a>Požadavky
- Chcete-li krok tímto průvodcem postupy, musíte první [nainstalovat Azure ML Workbench](../service/quickstart-installation.md).
- Musíte mít přístup k počítačům, které jsou vybaveny NVidia grafickými procesory.
    - Skripty můžete spustit přímo v místním počítači (Windows nebo systému macOS) s grafickými procesory.
    - Můžete také spouštět skripty ve kontejner Docker na počítači s grafickými procesory.

## <a name="execute-in-local-environment-with-gpus"></a>Spustit v _místní_ prostředí s grafickými procesory
Můžete nainstalovat Azure ML Workbench na počítače vybavené grafickými procesory a spustit proti _místní_ prostředí. To může být:
- Fyzický počítač Windows nebo systému macOS.
- Virtuální počítač s Windows (virtuální počítač) jako je Windows DSVM zřídí pomocí šablony virtuálních počítačů Azure NC-series.

V tomto případě nejsou nutná v Azure ML Workbench žádná speciální konfigurace. Ujistěte se, že máte všechny potřebné ovladače, sadách a grafický procesor s podporou strojového učení knihovny nainstalované místně. Jednoduše spustit proti _místní_ prostředí, kde můžete modul Python runtime přímý přístup k místním GPU hardwaru.

1. Otevřete AML Workbench. přejděte na **soubor** a **spusťte příkazový řádek**. 
2. Z příkazového řádku nainstalujte rozhraní framework grafický procesor s podporou hloubkové learning například Microsoft kognitivní Toolkit, TensorFlow a atd. Příklad:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.5 with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

3. Napište kód Python, který využívá hloubkového učení knihovny.
4. Zvolte _místní_ jako výpočetní prostředí a spouštění kódu vytvořeného Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Spustit v _docker_ prostředí na virtuální počítač s Linuxem s grafickými procesory
Azure ML Workbench také podporují spouštění v Docker Linux virtuální počítač Azure. Zde máte skvělou možnost spustit výpočetně náročné úlohy na části výkonné virtuálního hardwaru a platit pouze pro použití tak, že vypnete, po dokončení. I když v zásadě je možné použít grafickými procesory z jakéhokoli Linux virtuálního počítače, na základě Ubuntu DSVM se dodává s požadované ovladače CUDA a knihovny předinstalovaným, mnohem snadněji instalace. Použijte následujících kroků:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Vytvoření virtuálního počítače na základě Ubuntu Linux Data vědecké účely v Azure
1. Otevřete webový prohlížeč a přejděte na [portálu Azure](https://portal.azure.com)

2. Vyberte **+ nový** na levé straně na portálu.

3. Vyhledejte "Datové vědy virtuálního počítače pro Linux (Ubuntu)" na webu Marketplace.

4. Klikněte na tlačítko **vytvořit** vytvořit Ubuntu DSVM.

5. Vyplňte **Základy** formuláře se požadované informace.
Při výběru umístění pro virtuální počítač, Všimněte si, že virtuální grafický procesor počítače jsou k dispozici pouze v určité oblasti, například **jihu USA**. V tématu [výpočetní produkty podle oblasti](https://azure.microsoft.com/regions/services/).
Kliknutím na tlačítko OK uložte **Základy** informace.

6. Zvolte velikost virtuálního počítače. Vyberte jednu z velikosti předponu NC virtuálních počítačů, které jsou vybaveny čipy NVidia GPU.  Klikněte na tlačítko **Zobrazit vše** chcete zobrazit úplný seznam podle potřeby. Další informace o [vybavený GPU virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Dokončit zbývající nastavení a zkontrolovat informace o nákupu. Kliknutím na nákup vytvořte virtuální počítač. Poznamenejte si adresu IP přidělené k virtuálnímu počítači. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Vytvoření nového projektu v Azure ML Workbench 
Můžete použít _klasifikace MNIST pomocí TensorFlow_ příkladu nebo _klasifikace MNIST datovou sadu s CNTK_ příklad.

### <a name="create-a-new-compute-target"></a>Vytvořit nový výpočetní cíl
Spusťte příkazový řádek z Azure ML Workbench. Zadejte následující příkaz. Zástupný text pomocí příkladu níže nahraďte vlastními hodnotami pro název, IP adresa, uživatelské jméno a heslo. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Konfigurace Azure ML Workbench, aby přístup GPU
Přejděte zpět na projekt a otevřete **zobrazení souboru**a přístupů **aktualizovat** tlačítko. Nyní uvidíte dva nové soubory konfigurace `my_dsvm.compute` a `my_dsvm.runconfig`.
 
Otevřete `my_dsvm.compute`. Změna `baseDockerImage` k `microsoft/mmlspark:plus-gpu-0.7.9` a přidejte nový řádek `nvidiaDocker: true`. Takže soubor by měl mít tyto dva řádky:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Nyní otevřete `my_dsvm.runconfig`, změňte `Framework` z hodnoty `PySpark` k `Python`. Pokud nevidíte tohoto řádku, přidat, protože by se výchozí hodnota `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Referenční dokumentace hloubkové Learning Framework 
Otevřete `conda_dependencies.yml` souboru a zajistěte, aby používáte verzi GPU hloubkového učení balíčků Python framework. Ukázkové projekty seznam verzí procesoru, proto musíte k provedení této změny.

Příklad TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Příklad kognitivní nástrojů Microsoft:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.5 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

### <a name="execute"></a>Spuštění
Nyní jste připraveni ke spuštění skriptů jazyka Python. Je bylo možné spouštět v rámci Azure ML Workbench pomocí `my_dsvm` kontextu, nebo můžete spustit z příkazového řádku:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Pokud chcete ověřit, jestli se používá na grafický procesor, vyhledejte ve výstupu spuštění zobrazit přibližně takto:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Blahopřejeme! Váš skript právě orientovat power GPU prostřednictvím kontejner Docker!

## <a name="next-steps"></a>Další postup
Zobrazit příklad pomocí grafického procesoru pro urychlení školení hluboké neuronové sítě v Azure ML galerie.
