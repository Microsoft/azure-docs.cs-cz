---
title: Co je instance služby COMPUTE Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Přečtěte si o Azure Machine Learning výpočetní instanci, plně spravované cloudové pracovní stanici.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 02655a3697139ae3a9c4c36b423b874b6e5d34f9
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541865"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co je instance služby COMPUTE Azure Machine Learning?

Instance COMPUTE Azure Machine Learning (Preview) je plně spravovaná cloudová pracovní stanice pro odborníky na data. 

Výpočetní instance usnadňují začátek vývoje Azure Machine Learning a také poskytují možnosti pro správu a připravenost pro správce IT.  

Použijte výpočetní instanci jako vaše plně nakonfigurované a spravované vývojové prostředí v cloudu.

Výpočetní instance se obvykle používají jako vývojová prostředí.  Můžou se také používat jako výpočetní cíl pro školení a Inferencing pro vývoj a testování.  Pro velké úlohy je [Azure Machine Learning výpočetní cluster](how-to-set-up-training-targets.md#amlcompute) s možnostmi škálování s více uzly lepší volbou cíle pro výpočty.

> [!NOTE]
> Výpočetní instance jsou v tuto chvíli dostupné jenom pro pracovní prostory s oblastí **střed USA – sever** nebo **Velká Británie – jih**, která už brzy podporuje další oblasti.
>Pokud je váš pracovní prostor v jakékoli jiné oblasti, můžete místo toho vytvořit a použít [virtuální počítač s poznámkovým blokem](concept-compute-instance.md#notebookvm) . 

## <a name="why-use-a-compute-instance"></a>Proč používat výpočetní instanci?

Výpočetní instance je plně spravovaná cloudová pracovní stanice optimalizovaná pro vývojové prostředí ve službě Machine Learning. Přináší následující výhody:

|Klíčové výhody||
|----|----|
|Produktivita|Vědečtí data mohou vytvářet a nasazovat modely pomocí integrovaných poznámkových bloků a ve svém webovém prohlížeči tyto nástroje:<br/>– Jupyter<br/>- JupyterLab<br/>– RStudio|
|Spravované & zabezpečené|Snižte nároky na zabezpečení a přidejte dodržování požadavků podnikového zabezpečení. Výpočetní instance poskytují robustní zásady správy a zabezpečené síťové konfigurace, jako jsou:<br/><br/>– Automatické zřizování z Správce prostředků šablon nebo Azure Machine Learning SDK<br/>- [řízení přístupu na základě role (RBAC)](/azure/role-based-access-control/overview)<br/>[podpora - virtuální sítě](how-to-enable-virtual-network.md#compute-instance)<br/>-Zásada SSH pro povolení nebo zakázání přístupu SSH|
|Předem nakonfigurované&nbsp;nebo&nbsp;ML|Ušetřete čas při instalaci s předem nakonfigurovanými a aktuálními balíčky ML, architekturou pro hloubkové učení a ovladači GPU.|
|Plně přizpůsobitelné|Široká podpora typů virtuálních počítačů Azure, včetně GPU a trvalého přizpůsobení nízké úrovně, jako je instalace balíčků a ovladačů, vede k pokročilým scénářům Breeze. |

## <a name="contents"></a>Nástroje a prostředí

Instance Azure Machine Learning COMPUTE vám umožní vytvářet, vyškolovat a nasazovat modely v plně integrovaném prostředí poznámkového bloku v pracovním prostoru.


Tyto nástroje a prostředí se nainstalují do výpočetní instance: 

|Obecné nástroje & prostředí|Podrobnosti|
|----|:----:|
|Ovladače|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Knihovna Intel MPI||
|Azure CLI ||
|Ukázky Azure Machine Learning ||
|Azure Machine Learning modul EDAT ||            
|Docker||
|nginx||
|NCCL 2,0 ||
|Protobuf|| 

|Prostředí pro & nástrojů **R**|Podrobnosti|
|----|:----:|
|Edice Open Source serveru RStudio||
|Jádro R||
|Sada SDK Azure Machine Learning pro R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Ukázky SDK|

|Prostředí & **Python** Tools|Podrobnosti|
|----|----|
|Anaconda Python||
|Jupyter a rozšíření||
|Jupyterlab a rozšíření||
|Visual Studio Code ||
[Azure Machine Learning sady SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>z PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Další balíčky PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Balíčky conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Balíčky pro hloubkové učení|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Balíčky ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|                           
|Ukázky Azure Machine Learning Python & R SDK||

Výpočetní instance se obvykle používají jako vývojová prostředí.  Můžou se také používat jako výpočetní cíl pro školení a Inferencing pro vývoj a testování.  Pro velké úlohy je [Azure Machine Learning výpočetní cluster](how-to-set-up-training-targets.md#amlcompute) s možnostmi škálování s více uzly lepší volbou cíle pro výpočty.


## <a name="accessing-files"></a>Přístup k souborům

Poznámkové bloky a skripty jazyka R se ukládají ve výchozím účtu úložiště vašeho pracovního prostoru ve sdílené souborové službě Azure.  Tyto soubory jsou umístěny v adresáři "uživatelské soubory". Toto úložiště usnadňuje sdílení poznámkových bloků mezi výpočetními instancemi. Účet úložiště také udržuje při zastavení nebo odstranění výpočetní instance bezpečně zachované vaše poznámkové bloky.

Účet sdílené složky Azure vašeho pracovního prostoru je připojen jako jednotka na výpočetní instanci. Tato jednotka je výchozím pracovním adresářem pro Jupyter, Jupyter Labs a RStudio.

Soubory ve sdílené složce jsou dostupné ze všech výpočetních instancí ve stejném pracovním prostoru. Všechny změny těchto souborů v instanci COMPUTE budou spolehlivě trvale zachované zpátky do sdílené složky.

Nejnovější ukázky Azure Machine Learning můžete také klonovat do složky v adresáři uživatelských souborů ve sdílené složce pracovního prostoru.

Zápis malých souborů může být pomalejší na síťových jednotkách, než je zapisuje do samotného virtuálního počítače.  Pokud píšete mnoho malých souborů, zkuste použít adresář přímo na výpočetní instanci, jako je `/tmp` adresář. Upozorňujeme, že tyto soubory nebudou přístupné z jiných výpočetních instancí v pracovním prostoru.

## <a name="managing-a-compute-instance"></a>Správa výpočetní instance

V pracovním prostoru v Azure Machine Learning Studiu vyberte **COMPUTE**a pak v horní části vyberte **výpočetní instanci** .

![Správa výpočetní instance](./media/concept-compute-instance/manage-compute-instance.png)

Můžete provést následující akce:

* Vytvořte výpočetní instanci. Zadejte název, typ virtuálního počítače Azure, včetně GPU, povolte nebo zakažte přístup SSH a nakonfigurujte nastavení virtuální sítě volitelně. Můžete také vytvořit instanci přímo z integrovaných poznámkových bloků, Azure Portal, Správce prostředků šablony nebo Azure Machine Learning SDK. Kvóta vyhrazených jader na oblast, která se vztahuje k vytváření výpočetních instancí, je jednotná a sdílená s Azure Machine Learning kvótou výpočetního clusteru.
* Aktualizovat kartu výpočetních instancí
* Spuštění, zastavení a restartování výpočetní instance
* Odstranění instance COMPUTE

Pro každou výpočetní instanci v pracovním prostoru můžete:

* Přístup k identifikátorům URI Jupyter, JupyterLab, RStudio a VS Code v instanci COMPUTE
* SSH do výpočetní instance. Přístup SSH je ve výchozím nastavení zakázán, ale lze jej povolit v době vytváření výpočetních instancí. Přístup přes SSH je prostřednictvím mechanismu veřejného a privátního klíče. Karta vám poskytne podrobnosti o připojení SSH, jako je například IP adresa, uživatelské jméno a číslo portu.
* Získejte podrobnosti o konkrétní výpočetní instanci, jako je třeba IP adresa a oblast.

[RBAC](/azure/role-based-access-control/overview) umožňuje řídit, kteří uživatelé v pracovním prostoru můžou vytvořit, odstranit, spustit, zastavit a restartovat výpočetní instanci. Všichni uživatelé v roli přispěvatel a vlastník pracovního prostoru můžou vytvářet, odstraňovat, spouštět, zastavovat a restartovat výpočetní instance v rámci pracovního prostoru. Pouze tvůrce konkrétní výpočetní instance má však povolen přístup k Jupyter, JupyterLab a RStudio této výpočetní instance. Tvůrce výpočetní instance má vyhrazenou instanci COMPUTE, má root Access a může být terminálem prostřednictvím Jupyter. Instance COMPUTE bude mít přihlášení uživatele Creator User a všechny akce budou používat identitu tohoto uživatele pro RBAC a navýšení spuštění experimentů. Přístup přes SSH je řízený pomocí mechanismu veřejného a privátního klíče.

Můžete také vytvořit instanci.
* Přímo z prostředí integrovaných poznámkových bloků
* V Azure Portal
* Z šablony Azure Resource Manager
* S Azure Machine Learning SDK

Vyhrazená kvóta jader na oblast, která platí pro vytváření výpočetních instancí, je jednotná a sdílená s Azure Machine Learningmi kvótami školicích clusterů. 

## <a name="compute-target"></a>Cílový výpočetní objekt

Výpočetní instance se dají použít jako [školicí cíl](concept-compute-target.md#train) Azure Machine Learning pro výpočetní prostředky, podobně jako clustery výpočetního školení. Zřízení virtuálního počítače s více grafickými procesory pro spouštění úloh distribuovaného školení pomocí TensorFlow/PyTorch odhady. Můžete také vytvořit konfiguraci spuštění a použít ji ke spuštění experimentu na výpočetní instanci. Výpočetní instanci můžete použít jako cíl nasazení místní Inferencing pro scénáře testování a ladění.

## <a name="notebookvm"></a>Co se stalo s virtuálním počítačem poznámkového bloku?

Výpočetní instance nahrazují virtuální počítač poznámkového bloku.  V oblastech, kde nejsou ještě výpočetní instance dostupné, můžete dál používat virtuální počítače s poznámkovým blokem s plnou funkčností a vytvářet nové virtuální počítače poznámkových bloků.

Všechny soubory poznámkových bloků uložené ve sdílené složce pracovního prostoru a data v úložištích dat pracovního prostoru budou přístupná z instance Compute. Všechny vlastní balíčky, které byly dřív nainstalované na virtuálním počítači s poznámkovým blokem, se ale musí na instanci COMPUTE znovu nainstalovat. Omezení kvót, která se vztahují na vytváření výpočetních clusterů, se budou vztahovat i na výpočetní instance. 

V oblastech, kde jsou k dispozici výpočetní instance, nelze vytvořit nové virtuální počítače poznámkového bloku. Máte ale pořád přístup k vytvořeným virtuálním počítačům s poznámkovým blokem s plnou funkčností. Výpočetní instance se dají vytvořit ve stejném pracovním prostoru jako stávající virtuální počítače s poznámkovým blokem. 


## <a name="next-steps"></a>Další kroky

 * [Kurz: analýza prvního modelu ml](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.
