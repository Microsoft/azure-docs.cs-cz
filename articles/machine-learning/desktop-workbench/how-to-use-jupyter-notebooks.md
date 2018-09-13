---
title: Použití aplikace Jupyter notebook v Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Příručka pro pomocí poznámkového bloku Jupyter funkce Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 712cdaa65487620b2f8af4a0ad57c01c24b9a965
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643600"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Použití poznámkových bloků Jupyter v aplikaci Azure Machine Learning Workbench

Azure Machine Learning Workbench podporuje interaktivní datové vědy experimentování díky své integraci s poznámkovými bloky Jupyter. Tento článek popisuje, jak efektivně využívat tuto funkci chcete zvýšit rychlost a kvalitu vaší interaktivní datové vědy experimentování.

## <a name="prerequisites"></a>Požadavky
- [Vytvoření účtů služby Azure Machine Learning a nainstalujte aplikaci Azure Machine Learning Workbench](../service/quickstart-installation.md).
- Znáte [Poznámkový blok Jupyter](http://jupyter.org/). Tento článek není o tom, jak použít Jupyter.

## <a name="jupyter-notebook-architecture"></a>Architektura Poznámkový blok Jupyter
Architektura Poznámkový blok Jupyter na vysoké úrovni obsahuje tři komponenty. Každou lze spustit v různých výpočetních prostředích:

- **Klient**: přijímá vstup uživatele a zobrazí vykreslení výstupu.
- **Server**: webový server, který hostuje soubory Poznámkový blok (.ipynb soubory).
- **Jádra**: běhové prostředí, ve které spuštění poznámkového bloku se stane buňky.

Další informace najdete v oficiální [Jupyter dokumentaci](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Následující diagram znázorňuje, jak tato architektura jádra, klienta a serveru se mapuje na komponenty ve službě Azure Machine Learning:

![Architektura Poznámkový blok Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Jádra v aplikaci Azure Machine Learning Workbench poznámkové bloky
Můžete používat různé jádra v aplikaci Azure Machine Learning Workbench tak, že definujete spuštění konfigurace a v cílových výpočetních prostředí `aml_config` složku ve vašem projektu. Přidání nové cílové výpočetní prostředí pomocí `az ml computetarget attach` příkaz je ekvivalentem přidání nové jádra.

>[!NOTE]
>Kontrola [konfigurace služby Azure Machine Learning služby experimentování ve službě](experimentation-service-configuration.md) pro další podrobnosti o spuštění konfigurace a cílových výpočetních prostředí.

### <a name="kernel-naming-convention"></a>Zásady vytváření názvů jádra
Azure Machine Learning Workbench vygeneruje vlastní jádra Jupyter. Tyto jádrech jsou pojmenovány  *\<název projektu > \<konfigurační název běhu >*. Například, pokud máte konfigurace spuštění s názvem _docker-python_ v projektu s názvem _myIris_, Azure Machine Learning je dostupná jádra s názvem *myIris docker-python.* Nastavte spuštěného jádra v poznámkovém bloku Jupyter **jádra** nabídky v **změnu jádra** podnabídka. Název běžícího se zobrazí na pravé straně řádku nabídek.
 
Azure Machine Learning Workbench v současné době podporuje následující typy jádra.

### <a name="local-python-kernel"></a>Místní jádra Pythonu
Tato Python jádro podporuje spuštění na místních počítačích. Tato služba je integrovaná s podporou historie spuštění Azure Machine Learning. Název jádra je obvykle *my_project_name místní.*

>[!NOTE]
>Nepoužívejte jádra Python 3. Je samostatný jádra Jupyter poskytuje ve výchozím nastavení a není integrován s funkcemi Azure Machine Learning. Například `%azureml` Jupyter magic funkce vrátí chyby "nenalezeno". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Python jádra v Dockeru (místní nebo vzdálené)
Tato Python jádra běží v kontejneru Dockeru na místním počítači nebo ve vzdáleném virtuálním počítači s Linuxem (VM). Název jádra je obvykle *my_project dockeru.* Přidružené `docker.runconfig` soubor má `Framework` pole nastaveno `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Jádra PySpark v Dockeru (místní nebo vzdálené)
Tato jádra PySpark spustí skripty v kontextu Spark běžící uvnitř kontejneru Docker v místním počítači nebo na vzdáleném virtuálním počítači Linux. Název jádra je obvykle *my_project dockeru.* Přidružené `docker.runconfig` soubor má `Framework` pole nastaveno `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Jádra PySpark v clusteru Azure HDInsight
Tato jádra běží ve vzdáleném clusteru Azure HDInsight, který jste připojili jako cílové výpočetní prostředí pro váš projekt. Název jádra je obvykle *my_project my_hdi.* 

>[!IMPORTANT]
>V `.compute` souboru HDI cílové výpočetní prostředí, musíte změnit `yarnDeployMode` pole `client` (výchozí hodnota je `cluster`) použití tohoto jádra. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Spustí Jupyter server aplikace Azure Machine Learning Workbench
Z Azure Machine Learning Workbench můžete použít poznámkových bloků prostřednictvím **poznámkových bloků** kartu. _Klasifikace Iris_ ukázkový projekt obsahuje `iris.ipynb` ukázkový poznámkový blok.

![Karta poznámkové bloky](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Při otevření poznámkového bloku v Azure Machine Learning Workbench se zobrazí ve své vlastní záložky v dokumentu v **režim náhledu**. Toto je zobrazení jen pro čtení, který nevyžaduje, aby spuštěný server Jupyter a jádra.

![Ve verzi preview poznámkového bloku](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Výběr **spustit Server poznámkového bloku** tlačítko spustí Jupyter server a přepne do poznámkového bloku **režim úprav**. Vložený v aplikaci Workbench se zobrazí známé uživatelské rozhraní aplikace Jupyter Notebook. Teď můžete nastavit jádra z **jádra** nabídky a zahájit relaci interactive poznámkového bloku. 

>[!NOTE]
>Pomocí jiné místní jádra může trvat minutu nebo dvě pro spuštění, pokud ho používáte poprvé. Můžete spustit `az ml experiment prepare` příkazu z okna CLI připravte cílové výpočetní prostředí, takže spuštění jádra se mnohem rychleji, jakmile přípravu cílového výpočetního prostředí.

![Režim úprav](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Jedná se o plně interaktivní prostředí poznámkového bloku Jupyter. Všechny operace regulární Poznámkový blok a klávesové zkratky jsou podporovány z tohoto okna, s výjimkou některé operace se soubory, které lze provést prostřednictvím aplikace Workbench **poznámkových bloků** kartu a **souboru** kartu.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Jupyter server spustit z příkazového řádku
Můžete také spustit relaci Poznámkový blok zadáním `az ml notebook start` z okna příkazového řádku:
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
Výchozí prohlížeč se automaticky otevře serverem Jupyter odkazující na domovský adresář projektu. Můžete také použít adresu URL a tokenu zobrazí v okně rozhraní příkazového řádku otevřete další okno prohlížeče místně. 

![Řídicí panel Projekt](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Teď můžete vybrat `.ipynb` soubor poznámkového bloku, otevřete ho, nastavit jádra (pokud to není nastavený) a spustit interaktivní relaci.

![Řídicí panel Projekt](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Umožňuje spravovat experimenty magic příkazy

Můžete použít [magic příkazy](http://ipython.readthedocs.io/en/stable/interactive/magics.html) buněk Poznámkový blok uložte výstupy, například modely nebo datové sady a sledovat svou historii spuštění.

Chcete-li sledovat jednotlivé Poznámkový blok spustí buňky, použijte `%azureml history on` magic příkazu. Po zapnutí historii každé buňce spuštění se zobrazí jako položka v historii spuštění:

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Chcete-li vypnout sledování buňky spustit, použijte `%azureml history off` magic příkazu.

Můžete použít `%azureml upload` magic uložit model a datových souborů ze spuštění příkazu. Uložené objekty se zobrazí jako výstupy v zobrazení historie spuštění:

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Výstupy musí být uložen do složky s názvem *výstupy.*

## <a name="next-steps"></a>Další postup
- Další informace o použití aplikace Jupyter Notebook, najdete v článku [oficiální dokumentaci Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Pokud chcete získat lepší představu o prostředí pro spouštění služby experimentování ve službě Azure Machine Learning, naleznete v tématu [konfigurace služby Azure Machine Learning služby experimentování ve službě](experimentation-service-configuration.md).

