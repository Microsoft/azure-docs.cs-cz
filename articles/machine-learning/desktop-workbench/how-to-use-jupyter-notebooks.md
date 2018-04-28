---
title: Jak používat Jupyter Notebooks v nástroji Azure Machine Learning Workbench | Microsoft Docs
description: Příručka pro pomocí poznámkového bloku Jupyter funkci Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 11d295734fd6d9180468709f0a25cca48b13df4d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Použít Jupyter Notebooks v nástroji Azure Machine Learning Workbench

Azure Machine Learning Workbench podporuje interaktivní data vědecké účely experimentování díky své integraci s poznámkovými bloky Jupyter. Tento článek popisuje, jak provádět efektivní použití této funkce lze zvýšit rychlost a kvalitu vaší vědecké účely experimentování interaktivní data.

## <a name="prerequisites"></a>Požadavky
- [Vytvoření účtů Azure Machine Learning a nainstalujte Azure Machine Learning Workbench](../service/quickstart-installation.md).
- Se seznamte s [Poznámkový blok Jupyter](http://jupyter.org/). V tomto článku není o naučit se používat Jupyter.

## <a name="jupyter-notebook-architecture"></a>Architektura poznámkového bloku Jupyter
Architektura Poznámkový blok Jupyter na vysoké úrovni obsahuje tři součásti. Každý můžete spustit v různých výpočetních prostředí:

- **Klient**: vstup uživatele a zobrazí výstup se vykresluje obdrží.
- **Server**: webový server, který hostuje soubory poznámkového bloku (.ipynb soubory).
- **Jádra**: běhového prostředí, ve které provádění poznámkového bloku se stane buněk.

Další informace najdete v oficiální [Jupyter dokumentaci](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Následující diagram znázorňuje, jak tato architektura klienta, serveru a jádra mapuje komponenty v Azure Machine Learning:

![Architektura poznámkového bloku Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Jádra v Azure Machine Learning Workbench poznámkových bloků
Můžete používat různé jádra v nástroji Azure Machine Learning Workbench definováním spuštění konfigurace a výpočetní cílů v `aml_config` složku ve vašem projektu. Přidání nového cíle výpočetní vydáním `az ml computetarget attach` příkaz je ekvivalentem přidání nové jádra.

>[!NOTE]
>Zkontrolujte [konfigurace služby Azure Machine Learning experimentování](experimentation-service-configuration.md) další podrobnosti o spuštění konfigurace a výpočetní cíle.

### <a name="kernel-naming-convention"></a>Zásady vytváření názvů jádra
Azure Machine Learning Workbench generuje vlastní Jupyter jádra. Tyto jádra jsou pojmenované  *\<název projektu > \<spustit název konfigurace >*. Například, pokud máte spuštění konfigurace s názvem _docker python_ v projektu s názvem _myIris_, Azure Machine Learning s názvem jádro zpřístupňují *myIris docker-python.* Nastavit spuštěné jádra v poznámkovém bloku Jupyter **jádra** nabídky v **změnu jádra** podnabídky. Název spuštěné jádra se zobrazí na pravé straně řádku nabídek.
 
Azure Machine Learning Workbench v současné době podporuje následující typy jádra.

### <a name="local-python-kernel"></a>Místní jádra Python
Tato Python jádra podporuje spuštění na místních počítačích. Je integrován s podporou Azure Machine Learning spustit historie. Název jádra je obvykle *my_project_name místní.*

>[!NOTE]
>Nepoužívejte Python 3 jádra. Je samostatný jádra, poskytované Jupyter ve výchozím nastavení a není propojit s funkcemi Azure Machine Learning. Například `%azureml` Jupyter magic funkcí vracet "nebyl nalezen" chyby. 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python jádra v Docker (místní nebo vzdálené)
Tato Python jádra běží v kontejner Docker na místním počítači nebo ve vzdáleném Linux virtuálního počítače (VM). Název jádra je obvykle *my_project docker.* Přidruženého `docker.runconfig` soubor má `Framework` pole nastavené na `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Jádra PySpark v Docker (místní nebo vzdálené)
Tato jádra PySpark spustí skripty v kontextu Spark běžících v rámci kontejner Docker na místním počítači nebo na vzdálený virtuální počítač s Linuxem. Název jádra je obvykle *my_project docker.* Přidruženého `docker.runconfig` soubor má `Framework` pole nastavené na `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Jádra PySpark v clusteru Azure HDInsight
Tato jádra běží v vzdálený cluster Azure HDInsight, který jste připojili jako cíl výpočetní pro váš projekt. Název jádra je obvykle *my_project my_hdi.* 

>[!IMPORTANT]
>V `.compute` souboru HDI výpočetní cíl, musíte změnit `yarnDeployMode` do `client` (výchozí hodnota je `cluster`) používat tento jádra. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Spusťte Jupyter server z Azure Machine Learning Workbench
Z Azure Machine Learning Workbench, máte přístup k poznámkových bloků prostřednictvím **poznámkových bloků** kartě. _Klasifikace Iris_ obsahuje ukázkový projekt `iris.ipynb` ukázkový poznámkový blok.

![Karta poznámkových bloků](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Když v nástroji Azure Machine Learning Workbench otevřete Poznámkový blok, se zobrazí na vlastní kartě dokumentu v **režim náhledu**. Toto je jen pro čtení zobrazení, která nevyžaduje používají server Jupyter a jádra.

![Poznámkový blok preview](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Výběr **spuštění serveru poznámkového bloku** tlačítko spustí Jupyter serveru a přepínače do poznámkového bloku **režimu úprav**. Vložený v Workbench se zobrazí uživatelské rozhraní známé poznámkového bloku Jupyter. Teď můžete nastavit jádra z **jádra** nabídky a spustit relaci interaktivní poznámkového bloku. 

>[!NOTE]
>S jádra není místní může trvat minutu nebo dvě spustit, pokud používáte poprvé. Můžete provést `az ml experiment prepare` v okně příkazového řádku připravit cílový výpočetní tak jádra se mnohem rychleji spustí po výpočetní cíl je připravený příkaz.

![Režim úprav](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Toto je plně interaktivní možnosti poznámkového bloku Jupyter. Jsou podporovány všechny operace regulární Poznámkový blok a klávesové zkratky z tohoto okna, s výjimkou některé operace souborů, které se dají provádět prostřednictvím nástroje Workbench **poznámkových bloků** kartě a **souboru** kartě.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Spustí Jupyter server z příkazového řádku
Můžete také spustit relaci poznámkového bloku vydáním `az ml notebook start` z okna příkazového řádku:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Se serverem Jupyter odkazující na domovský adresář projektu automaticky otevře výchozí prohlížeč. Můžete také použít adresu URL a tokenu zobrazí v okně příkazového řádku otevřít další okna prohlížeče místně. 

![řídicího panelu Projekt](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Teď si můžete vybrat `.ipynb` soubor poznámkového bloku, otevřete ho, nastavte jádra (pokud nebylo nastaveno) a spusťte interaktivní relace.

![řídicího panelu Projekt](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Použít magic příkazy ke správě experimenty

Můžete použít [kouzelná příkazy](http://ipython.readthedocs.io/en/stable/interactive/magics.html) v rámci vaší Poznámkový blok buněk sledování vaší historie spouštění a ukládání výstupů, jako je například modely ani datové sady.

Chcete-li sledovat spustí jednotlivé Poznámkový blok buněk, použijte `%azureml history on` kouzelná příkaz. Po zapnutí historie se zobrazí jako položku v historii spouštění jednotlivých buněk spustit:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Chcete-li vypnout sledování buňky spustit, použijte `%azureml history off` kouzelná příkaz.

Můžete použít `%azureml upload` kouzelná příkazu Uložit model a datových souborů z vaší spustit. Objekty uložené objeví jako výstup v zobrazení historie spouštění:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Výstupy musí být uložena do složky s názvem *výstupy.*

## <a name="next-steps"></a>Další postup
- Naučte se používat Poznámkový blok Jupyter, najdete v článku [Jupyter oficiální dokumentaci](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Chcete-li získat lepší představu o prostředí pro spuštění experimenty Azure Machine Learning, přečtěte si téma [konfigurace služby Azure Machine Learning experimentování](experimentation-service-configuration.md).

