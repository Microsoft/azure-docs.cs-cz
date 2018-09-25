---
title: Jak používat GPU pro službu Azure Machine Learning | Dokumentace Microsoftu
description: Tento článek popisuje, jak použít grafické zpracování jednotek (GPU) k trénování neuronových sítí v Azure Machine Learning Workbench.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 8e8944a9bdfa2b75f6442e03d45dfb6977c2a122
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974353"
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Jak používat GPU ve službě Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Grafického procesoru (GPU) se často používá ke zpracování výpočetně náročné úlohy, ke kterým lze obvykle dochází při tréninku modelů určité hluboké neuronové sítě. S využitím GPU, můžete výrazně zkrátit čas trénování modelů. V tomto dokumentu se dozvíte, jak nakonfigurovat aplikaci Azure ML Workbench používat [DSVM (virtuální počítač datové vědy)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) vybavené GPU jako cíl spuštění. 

## <a name="prerequisites"></a>Požadavky
- Pro jednotlivé kroky v této příručce s postupy, musíte si nejdřív [nainstalujte aplikaci Azure ML Workbench](quickstart-installation.md).
- Musíte mít přístup k počítačům s grafickými procesory NVidia.
    - Skripty můžete spustit přímo v místním počítači (Windows nebo macOS) s grafickými procesory.
    - Můžete také spouštět skripty v kontejneru Dockeru na počítači s Linuxem s grafickými procesory...

## <a name="execute-in-local-environment-with-gpus"></a>Spouštění v _místní_ prostředí s grafickými procesory
Můžete nainstalovat aplikaci Azure ML Workbench na počítači s grafickými procesory a spouštějte _místní_ prostředí. To může být:
- Fyzický počítač Windows nebo macOS.
- Windows virtuální počítač (VM) jako je například Windows DSVM zřízené s využitím šablony virtuální počítače Azure NC-series.

V takovém případě nejsou nutná v aplikaci Azure ML Workbench žádná zvláštní konfigurace. Ujistěte se, že máte všechny potřebné ovladače, sady nástrojů a s podporou grafického procesoru strojového učení knihovny nainstalovaný místně. Jednoduše spouštějte _místní_ prostředí, kde můžete modulu runtime Pythonu přímý přístup k místním hardwaru GPU.

1. Otevřete aplikaci Workbench AML. přejděte na **souboru** a **otevřít příkazový řádek**. 
2. Z příkazového řádku nainstalujte obsáhlý learning s podporou grafického procesoru framework, jako je například Microsoft Cognitive Toolkit, TensorFlow a atd. Příklad:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.5 with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

3. Zápis kódu v Pythonu, která využívá obsáhlý learning knihovny.
4. Zvolte _místní_ jako výpočetní prostředí a spuštění kódu Pythonu.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Spouštění v _docker_ prostředí na Linuxovém virtuálním počítači s grafickými procesory
Azure ML Workbench také podporují spouštění v Dockeru ve virtuálním počítači Azure s Linuxem. Tady máte skvělou možností, jak spouštět výkonné virtuální hardwarová součást výpočetně náročné úlohy a Plaťte jenom za využití tím, že vypíná, až budete hotovi. I v zásadě je možné použít GPU na libovolný virtuální počítač s Linuxem, datové VĚDY založeného na Ubuntu dodává s požadované ovladače CUDA a knihoven, které jsou předem nainstalované, který usnadňuje nastavení. Postupujte podle následujících kroků:

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Vytvoření virtuálního počítače založeného na Ubuntu Linuxu datové vědy v Azure
1. Otevřete webový prohlížeč a přejděte [webu Azure portal](https://portal.azure.com)

2. Vyberte **+ nová** na levé straně na portálu.

3. Vyhledejte "Virtuální počítač pro datové vědy pro Linux (Ubuntu)" na webu Marketplace.

4. Klikněte na tlačítko **vytvořit** k vytvoření DSVM Ubuntu.

5. Vyplňte **Základy** ve formuláři požadované informace.
Při výběru umístění pro váš virtuální počítač, mějte na paměti, že virtuální počítače GPU jsou k dispozici pouze v určitých oblastech Azure, například **střed USA – jih**. Zobrazit [compute dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).
Kliknutím na OK uložte **Základy** informace.

6. Zvolte velikost virtuálního počítače. Vyberte jednu z velikostí s předponou síťového adaptéru virtuálních počítačů, které jsou vybaveny čipy grafický procesor NVidia.  Klikněte na tlačítko **zobrazení všech** zobrazíte úplný seznam podle potřeby. Další informace o [vybavené GPU virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

7. Dokončete zbývající nastavení a zkontrolovat informace o nákupu. Klikněte na možnost nákupu vytvoření virtuálního počítače. Poznamenejte si IP adresa přidělená k virtuálnímu počítači. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Vytvoření nového projektu v aplikaci Azure ML Workbench 
Můžete použít _klasifikace mnist ručně pomocí TensorFlow_ například nebo _klasifikace mnist ručně datovou sadu s CNTK_ příklad.

### <a name="create-a-new-compute-target"></a>Vytvořit nový cíl Compute
Spusťte příkazový řádek z Azure ML Workbench. Zadejte následující příkaz. Zástupný text jako v příkladu níže nahraďte vlastními hodnotami pro název, IP adresu, uživatelské jméno a heslo. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Konfigurace Azure ML Workbench, aby přístup GPU
Vraťte se do projektu a otevřete **zobrazení souboru**a klikněte **aktualizovat** tlačítko. Teď vidíte dva nové soubory konfiguračních `my_dsvm.compute` a `my_dsvm.runconfig`.
 
Otevřít `my_dsvm.compute`. Změnit `baseDockerImage` k `microsoft/mmlspark:plus-gpu-0.9.9` a přidejte nový řádek `nvidiaDocker: true`. Proto tento soubor by měl obsahovat následující dva řádky:
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
Nyní otevřete `my_dsvm.runconfig`, změňte `Framework` hodnotu `PySpark` k `Python`. Pokud nevidíte tento řádek, ho přidat, protože výchozí hodnota bude `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Referenční dokumentace rozhraní hloubkového učení 
Otevřít `conda_dependencies.yml` souboru a ujistěte se, že používáte verzi GPU hloubkového učení framework balíčky Pythonu. Ukázkové projekty seznam verzí procesoru, je třeba provést tuto změnu.

Příklad TensorFlow: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Příklad pro Microsoft Cognitive Toolkit.
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.5 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl
```

### <a name="execute"></a>Spuštění
Nyní jste připraveni ke spuštění skriptů v Pythonu. Můžete je spustit v Azure ML Workbench pomocí `my_dsvm` kontextu, nebo můžete spustit z příkazového řádku:
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Pokud chcete ověřit, že se používá GPU, prohlédněte si výstup spuštění zobrazíte přibližně takto:

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Blahopřejeme! Váš skript právě využily výkon GPU až kontejneru Dockeru.

## <a name="next-steps"></a>Další postup
Viz příklad použití GPU pro zrychlení hluboké neuronové sítě školení v galerii Azure ML.
