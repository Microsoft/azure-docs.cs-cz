---
title: Co je výpočetní instance Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Seznamte se s výpočetní instancí Azure Machine Learning, plně spravovanou cloudovou pracovní stanicí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283924"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co je výpočetní instance Azure Machine Learning?

Výpočetní instance Azure Machine Learning (preview) je plně spravovaná cloudová pracovní stanice pro datové vědce. 

Výpočetní instance usnadňují zahájení vývoje Azure Machine Learning a poskytují možnosti správy a připravenosti podniku pro správce IT.  

Použijte výpočetní instanci jako plně nakonfigurované a spravované vývojové prostředí v cloudu.

Výpočetní instance se obvykle používají jako vývojová prostředí.  Lze je také použít jako výpočetní cíl pro školení a odvození pro vývoj a testování.  Pro velké úkoly výpočetní [cluster Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) s možnostmi škálování s více uzlovými daty je lepší volbou výpočetního cíle.


## <a name="why-use-a-compute-instance"></a>Proč použít výpočetní instanci?

Výpočetní instance je plně spravovaná cloudová pracovní stanice optimalizovaná pro vývojové prostředí strojového učení. Poskytuje následující výhody:

|Klíčové výhody||
|----|----|
|Produktivita|Datoví vědci mohou vytvářet a nasazovat modely pomocí integrovaných notebooků a následujících nástrojů ve svém webovém prohlížeči:<br/>- Jupyter (Jupyter)<br/>- JupyterLab<br/>- RStudio|
|Spravované & zabezpečené|Snižte nároky na zabezpečení a přidejte dodržování předpisů na podnikové zabezpečení. Výpočetní instance poskytují robustní zásady správy a zabezpečené síťové konfigurace, jako jsou:<br/><br/>- Automatické zřizování ze šablon Správce prostředků nebo sady Azure Machine Learning SDK<br/>- [Řízení přístupu na základě rolí (RBAC)](/azure/role-based-access-control/overview)<br/>- [Podpora virtuální sítě](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH politika povolit / zakázat Přístup SSH|
|Předkonfigurované&nbsp;nebo&nbsp;ML|Ušetřete čas na úlohách nastavení s předem nakonfigurovanými a aktuálními balíčky ML, rámci hloubkového učení, ovladači GPU.|
|Plně přizpůsobitelné|Široká podpora pro typy virtuálních počítačů Azure, včetně GPU a trvalé hojné přizpůsobení, jako je instalace balíčků a ovladačů, činí pokročilé scénáře hračkou. |

## <a name="tools-and-environments"></a><a name="contents"></a>Nástroje a prostředí

Výpočetní instance Azure Machine Learning umožňuje vytvářet, trénovat a nasazovat modely v plně integrovaném prostředí poznámkového bloku ve vašem pracovním prostoru.


Tyto nástroje a prostředí jsou nainstalovány na výpočetní instanci: 

|Obecné nástroje & prostředí|Podrobnosti|
|----|:----:|
|Ovladače|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Knihovna Intel MPI||
|Azure CLI ||
|Ukázky Azure Machine Learning ||
|Modul EDAT Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** nástroje & prostředí|Podrobnosti|
|----|:----:|
|RStudio Server Open Source Edition||
|Jádro R||
|Azure Machine Learning SDK pro R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Ukázky SDK|

|**Nástroje PYTHON** & prostředí|Podrobnosti|
|----|----|
|Anaconda Python||
|Jupyter a rozšíření||
|Jupyterlab a rozšíření||
|Visual Studio Code ||
[Sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>od PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Další balíčky PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda balíčky|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Balíčky pro hloubkové vzdělávání|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Balíčky ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Ukázky Pythonu Azure Machine Learning & R.||

Všechny balíčky Pythonu se nainstalované v **pythonu 3.6 – azureml** prostředí.  

Výpočetní instance se obvykle používají jako vývojová prostředí.  Lze je také použít jako výpočetní cíl pro školení a odvození pro vývoj a testování.  Pro velké úkoly výpočetní [cluster Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) s možnostmi škálování s více uzlovými daty je lepší volbou výpočetního cíle.

### <a name="installing-packages"></a>Instalace balíčků

Balíčky můžete instalovat přímo do notebooku Jupyter nebo Rstudia:

* RStudio Použijte kartu **Balíčky** vpravo dole nebo kartu **Console** v levém horním rohu.  
* Python: Přidejte instalační kód a spusťte v buňce notebooku Jupyter.

Nebo můžete přistupovat k oknu terminálu libovolným z těchto způsobů:

* RStudio: Vyberte kartu **Terminál** vlevo nahoře.
* Jupyter Lab: Vyberte dlaždici **Terminál** pod **nadpisem Ostatní** na kartě Spouštěč.
* Jupyter: Vyberte **nový>terminál vpravo** nahoře na kartě Soubory.
* SSH do stroje.  Pak nainstalujte balíčky Pythonu do prostředí **Pythonu 3.6 – AzureML.**  Nainstalujte balíčky R do prostředí **R.**

## <a name="accessing-files"></a>Přístup k souborům

Poznámkové bloky a skripty R se ukládají ve výchozím účtu úložiště vašeho pracovního prostoru ve sdílené složce Azure.  Tyto soubory jsou umístěny pod adresářem "Uživatelské soubory". Toto úložiště usnadňuje sdílení poznámkových bloků mezi výpočetními instancemi. Účet úložiště také udržuje vaše poznámkové bloky bezpečně zachovány při zastavení nebo odstranění výpočetní instance.

Účet sdílení souborů Azure vašeho pracovního prostoru se namontuje jako jednotka na výpočetní instanci. Tato jednotka je výchozím pracovním adresářem pro Jupyter, Jupyter Labs a RStudio.

Soubory ve sdílené složce jsou přístupné ze všech výpočetních instancí ve stejném pracovním prostoru. Všechny změny těchto souborů v instanci výpočetních prostředků budou spolehlivě zachovány zpět do sdílené složky.

Můžete také klonovat nejnovější ukázky Azure Machine Learning do složky v adresáři uživatelských souborů ve sdílené složce pracovního prostoru.

Psaní malých souborů může být pomalejší na síťových jednotkách než zápis do samotného virtuálního počítači.  Pokud píšete mnoho malých souborů, zkuste použít adresář přímo `/tmp` na výpočetní instanci, jako je například adresář. Upozorňujeme, že tyto soubory nebudou přístupné z jiných výpočetních instancí v pracovním prostoru.

## <a name="managing-a-compute-instance"></a>Správa výpočetní instance

Ve svém pracovním prostoru ve studiu Azure Machine Learning vyberte **Compute**a nahoře vyberte **Compute Instance** .

![Správa výpočetní instance](./media/concept-compute-instance/manage-compute-instance.png)

Můžete provést následující akce:

* Vytvořit výpočetní instanci Zadejte název, typ virtuálního počítače Azure včetně GPU (upozorňujeme, že typ virtuálního počítače nelze po vytvoření změnit), povolit nebo zakázat přístup SSH a volitelně nakonfigurovat nastavení virtuální sítě. Instanci můžete také vytvořit přímo z integrovaných poznámkových bloků, portálu Azure, šablony Správce prostředků nebo sady Azure Machine Learning SDK. Vyhrazená jádra podle kvóty oblasti, která platí pro vytváření výpočetních instancí, jsou jednotná a sdílená s kvótou výpočetního clusteru Azure Machine Learning.
* Aktualizace karty výpočetních instancí
* Spuštění, zastavení a restartování instance výpočetních prostředků
* Odstranění výpočetní instance

Pro každou výpočetní instanci ve vašem pracovním prostoru můžete:

* Přístup K Jupyter, JupyterLab, RStudio na výpočetní instanci
* SSH do výpočetní instance. Přístup SSH je ve výchozím nastavení zakázán, ale lze jej povolit v době vytvoření výpočetní instance. Přístup SSH je prostřednictvím mechanismu veřejného a soukromého klíče. Na kartě budou podrobnosti o připojení SSH, jako je IP adresa, uživatelské jméno a číslo portu.
* Získejte podrobnosti o konkrétní výpočetní instanci, jako je IP adresa a oblast.

[RBAC](/azure/role-based-access-control/overview) umožňuje řídit, kteří uživatelé v pracovním prostoru můžete vytvořit, odstranit, spustit, zastavit, restartovat výpočetní instance. Všichni uživatelé v roli přispěvatele a vlastníka pracovního prostoru můžou vytvářet, odstraňovat, spouštět, zastavovat a restartovat výpočetní instance v celém pracovním prostoru. Však pouze tvůrce konkrétní výpočetní instance je povolen přístup Jupyter, JupyterLab a RStudio na této výpočetní instance. Tvůrce instance výpočetní ho compute má výpočetní instanci, která je pro ně vyhrazena, má root přístup a může terminálovat prostřednictvím Jupyteru. Instance výpočetních prostředků bude mít přihlášení jednoho uživatele uživatele tvůrce a všechny akce budou používat identitu tohoto uživatele pro RBAC a přiřazení spuštění experimentu. Přístup SSH je řízen prostřednictvím mechanismu veřejného a soukromého klíče.

Můžete také vytvořit instanci
* Přímo z integrovaného notebooku
* Na portálu Azure
* Ze šablony Správce prostředků Azure
* S Azure Machine Learning SDK

Vyhrazená jádra podle kvóty oblasti, která platí pro vytváření výpočetních instancí, jsou jednotná a sdílená s kvótou clusteru školení Azure Machine Learning. 

## <a name="compute-target"></a>Cílový výpočetní objekt

Výpočetní instance se dá použít jako [trénovací výpočetní cíl](concept-compute-target.md#train) podobný výpočetním výpočetním clusterům Azure Machine Learning. Zřízení virtuálního virtuálního provozu s více GPU pro spuštění distribuovaných úloh školení pomocí odhadů TensorFlow/PyTorch. Můžete také vytvořit konfiguraci spuštění a použít ji ke spuštění experimentu na výpočetní instanci. Výpočetní instance můžete použít jako cíl nasazení místního inferencingu pro scénáře testování a ladění.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Co se stalo s virtuálním počítačem s notebookem?

Výpočetní instance nahrazují virtuální počítač poznámkového bloku.  

Všechny soubory poznámkového bloku uložené ve sdílené složce pracovního prostoru a data v úložištích dat pracovního prostoru budou přístupné z instance výpočetního prostředí. Všechny vlastní balíčky dříve nainstalované na virtuálním počítači poznámkového bloku však bude nutné znovu nainstalovat na výpočetní instanci. Omezení kvót, která platí pro vytváření výpočetních clusterů, se budou vztahovat i na vytváření výpočetních instancí. 

Nové virtuální počítačky poznámkového bloku nelze vytvořit. Stále však můžete přistupovat a používat virtuální počítače poznámkového bloku, které jste vytvořili, s plnou funkčností. Výpočetní instance lze vytvořit ve stejném pracovním prostoru jako existující virtuální počítače poznámkového bloku. 


## <a name="next-steps"></a>Další kroky

 * [Kurz: Trénování prvního modelu ML](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.
