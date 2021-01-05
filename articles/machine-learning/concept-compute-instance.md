---
title: Co je výpočetní instance služby Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Přečtěte si o Azure Machine Learning výpočetní instanci, plně spravované cloudové pracovní stanici.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: 54da62ce961156b64c917b448557c17e7516e222
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862130"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co je výpočetní instance služby Azure Machine Learning?

Instance služby Azure Machine Learning COMPUTE je spravovaná cloudová pracovní stanice pro odborníky na data.

Výpočetní instance usnadňují začátek vývoje Azure Machine Learning a také poskytují možnosti pro správu a připravenost pro správce IT.  

Použijte výpočetní instanci jako vaše plně nakonfigurované a spravované vývojové prostředí v cloudu pro strojové učení. Můžou se taky používat jako výpočetní cíl pro školení a Inferencing pro účely vývoje a testování.  

V případě školení modelu produkčního prostředí použijte [výpočetní cluster Azure Machine Learning](how-to-create-attach-compute-cluster.md) s možnostmi škálování s více uzly. Pro nasazení modelu produkčního prostředí použijte [cluster služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Aby funkce COMPUTE instance Jupyter fungovala, ujistěte se, že komunikace webového soketu není zakázána. Ujistěte se prosím, že vaše síť povoluje připojení pomocí protokolu WebSocket k *. instances.azureml.net a *. instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Proč používat výpočetní instanci?

Výpočetní instance je plně spravovaná cloudová pracovní stanice optimalizovaná pro vývojové prostředí ve službě Machine Learning. Přináší následující výhody:

|Klíčové výhody|Popis|
|----|----|
|Produktivita|Modely můžete vytvářet a nasazovat pomocí integrovaných poznámkových bloků a následujících nástrojů v Azure Machine Learning Studiu:<br/>– Jupyter<br/>- JupyterLab<br/>-RStudio (Preview)<br/>Instance COMPUTE je plně integrovaná do Azure Machine Learningho pracovního prostoru a studia. Poznámkové bloky a data můžete sdílet s dalšími odborníky na data v pracovním prostoru.<br/> Můžete také použít [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) s výpočetními instancemi.
|Spravované & zabezpečené|Snižte nároky na zabezpečení a přidejte dodržování požadavků podnikového zabezpečení. Výpočetní instance poskytují robustní zásady správy a zabezpečené síťové konfigurace, jako jsou:<br/><br/>– Autozřizování z Správce prostředků šablon nebo Azure Machine Learning SDK<br/>- [Řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Podpora virtuální sítě](./how-to-secure-training-vnet.md#compute-instance)<br/>-Zásada SSH pro povolení nebo zakázání přístupu SSH<br/>Protokol TLS 1,2 povolen |
|Předem nakonfigurovaná &nbsp; pro &nbsp; ml|Ušetřete čas při instalaci s předem nakonfigurovanými a aktuálními balíčky ML, architekturou pro hloubkové učení a ovladači GPU.|
|Plně přizpůsobitelné|Široká podpora typů virtuálních počítačů Azure, včetně GPU a trvalého přizpůsobení nízké úrovně, jako je instalace balíčků a ovladačů, vede k pokročilým scénářům Breeze. |

[Výpočetní instanci můžete vytvořit](how-to-create-manage-compute-instance.md?tabs=python#create) sami nebo správce může [vytvořit instanci služby COMPUTE za vás](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Nástroje a prostředí

> [!IMPORTANT]
> Položky označené (Preview) v tomto článku jsou momentálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Instance Azure Machine Learning COMPUTE vám umožní vytvářet, vyškolovat a nasazovat modely v plně integrovaném prostředí poznámkového bloku v pracovním prostoru.

Můžete spustit poznámkové bloky Jupyter v [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) použití výpočetní instance jako vzdáleného serveru bez potřeby SSH. Můžete taky povolit VS Code integraci prostřednictvím [vzdáleného rozšíření SSH](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

Do své výpočetní instance můžete [nainstalovat balíčky](how-to-create-manage-compute-instance.md#install-packages) a [Přidat jádra](how-to-create-manage-compute-instance.md#add-new-kernels) .  

Následující nástroje a prostředí jsou již na výpočetní instanci nainstalovány: 

|Obecné nástroje & prostředí|Podrobnosti|
|----|:----:|
|Ovladače|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Knihovna Intel MPI||
|Azure CLI ||
|Ukázky Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|Prostředí pro & nástrojů **R**|Podrobnosti|
|----|:----:|
|RStudio server Open Source Edition (Preview)||
|Jádro R||
|Sada SDK Azure Machine Learning pro R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Ukázky sady SDK|

|Prostředí & **Python** Tools|Podrobnosti|
|----|----|
|Anaconda Python||
|Jupyter a rozšíření||
|Jupyterlab a rozšíření||
[Sada Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)</br>z PyPI|Zahrnuje většinu dalších balíčků AzureML.  Pokud chcete zobrazit úplný seznam, [otevřete okno terminálu na instanci služby COMPUTE](how-to-run-jupyter-notebooks.md#terminal) a spusťte příkaz. <br/> `conda list -n azureml_py36 azureml*` |
|Další balíčky PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Balíčky conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Balíčky pro hloubkové učení|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Balíčky ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Ukázky Azure Machine Learning Python & R SDK||

Balíčky Pythonu jsou nainstalované v prostředí **python 3,6-AzureML** .  

## <a name="accessing-files"></a>Přístup k souborům

Poznámkové bloky a skripty jazyka R se ukládají ve výchozím účtu úložiště vašeho pracovního prostoru ve sdílené souborové službě Azure.  Tyto soubory jsou umístěny v adresáři "uživatelské soubory". Toto úložiště usnadňuje sdílení poznámkových bloků mezi výpočetními instancemi. Účet úložiště také udržuje při zastavení nebo odstranění výpočetní instance bezpečně zachované vaše poznámkové bloky.

Účet sdílené složky Azure vašeho pracovního prostoru je připojen jako jednotka na výpočetní instanci. Tato jednotka je výchozím pracovním adresářem pro Jupyter, Jupyter Labs a RStudio. To znamená, že se poznámkové bloky a jiné soubory, které vytvoříte v Jupyter, JupyterLab nebo RStudio, automaticky ukládají do sdílené složky a jsou dostupné pro použití v jiných výpočetních instancích.

Soubory ve sdílené složce jsou dostupné ze všech výpočetních instancí ve stejném pracovním prostoru. Všechny změny těchto souborů v instanci COMPUTE budou spolehlivě trvale zachované zpátky do sdílené složky.

Nejnovější ukázky Azure Machine Learning můžete také klonovat do složky v adresáři uživatelských souborů ve sdílené složce pracovního prostoru.

Zápis malých souborů může být pomalejší na síťových jednotkách, než je zapisuje na samotný místní disk výpočetní instance.  Pokud píšete mnoho malých souborů, zkuste použít adresář přímo na výpočetní instanci, jako je například `/tmp` adresář. Všimněte si, že tyto soubory nebudou přístupné z jiných výpočetních instancí. 

`/tmp`Pro dočasná data můžete použít adresář v instanci služby Compute.  Nepište ale velké soubory dat na disk s operačním systémem výpočetní instance.  Místo toho použijte [úložiště dat](concept-azure-machine-learning-architecture.md#datasets-and-datastores) . Pokud jste nainstalovali rozšíření Git JupyterLab, může také vést k zpomalení výkonu výpočetní instance.

## <a name="managing-a-compute-instance"></a>Správa výpočetní instance

V pracovním prostoru v Azure Machine Learning Studiu vyberte **COMPUTE** a pak v horní části vyberte **výpočetní instanci** .

![Správa výpočetní instance](./media/concept-compute-instance/manage-compute-instance.png)

Můžete provést následující akce:

* [Vytvořte výpočetní instanci](#create). 
* Aktualizujte kartu COMPUTE Instances.
* Spusťte, zastavte a restartujte výpočetní instanci.  Za instanci platíte za každé, když je spuštěná. Pokud nepoužíváte výpočetní instanci, můžete ji zastavit, abyste snížili náklady. Zastavení výpočetní instance ho zruší. Pak ho znovu spusťte, až ho budete potřebovat. Poznámka: zastavení výpočetní instance zastaví účtování za výpočetní čas, ale pořád se vám bude účtovat disk, veřejná IP adresa a standardní nástroj pro vyrovnávání zatížení.
* Odstraňte výpočetní instanci.
* Vyfiltruje seznam výpočetních instancí, aby se zobrazily jenom ty, které jste vytvořili.

Pro každou instanci služby COMPUTE v pracovním prostoru, kterou můžete použít, můžete:

* Přístup k Jupyter, JupyterLab, RStudio instance COMPUTE
* SSH do výpočetní instance. Přístup SSH je ve výchozím nastavení zakázán, ale lze jej povolit v době vytváření výpočetních instancí. Přístup přes SSH je prostřednictvím mechanismu veřejného a privátního klíče. Karta vám poskytne podrobnosti o připojení SSH, jako je například IP adresa, uživatelské jméno a číslo portu.
* Získejte podrobnosti o konkrétní výpočetní instanci, jako je třeba IP adresa a oblast.

[Azure RBAC](../role-based-access-control/overview.md) umožňuje řídit, kteří uživatelé v pracovním prostoru můžou vytvořit, odstranit, spustit, zastavit a restartovat výpočetní instanci. Všichni uživatelé v roli přispěvatel a vlastník pracovního prostoru můžou vytvářet, odstraňovat, spouštět, zastavovat a restartovat výpočetní instance v rámci pracovního prostoru. Avšak pouze tvůrce konkrétní výpočetní instance nebo přiřazeného uživatele, pokud byl vytvořen jménem, má povolen přístup k Jupyter, JupyterLab a RStudio této výpočetní instance. Výpočetní instance je vyhrazená jednomu uživateli, který má rootový přístup, a může být terminálem přes Jupyter/JupyterLab/RStudio. Instance COMPUTE bude mít přihlášený jeden uživatel a všechny akce budou používat identitu tohoto uživatele pro Azure RBAC a připisující se ke spuštění experimentu. Přístup přes SSH je řízený pomocí mechanismu veřejného a privátního klíče.

Tyto akce lze řídit pomocí Azure RBAC:
* *Microsoft. MachineLearningServices/pracovní prostory/výpočetní výkon/čtení*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/zapisovat*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/odstranit*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/spustit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/zastavit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/restartovat/akce*

Chcete-li vytvořit výpočetní instanci, musíte mít oprávnění pro následující akce:
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/zapisovat*
* *Microsoft. MachineLearningServices/pracovní prostory/checkComputeNameAvailability/Action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Vytvoření výpočetní instance

Pokud jste připraveni spustit jeden z vašich poznámkových bloků, vytvořte v pracovním prostoru v Azure Machine Learning Studiu [novou instanci služby COMPUTE](how-to-create-attach-compute-studio.md#compute-instance) z oddílu **COMPUTE** nebo v části **poznámkové bloky** . 

Můžete také vytvořit instanci.
* Přímo z [prostředí integrovaných poznámkových bloků](tutorial-1st-experiment-sdk-setup.md#azure)
* V Azure Portal
* Z šablony Azure Resource Manager. Příklad šablony najdete v tématu [vytvoření Azure Machine Learning šablony výpočetních instancí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* S [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Z [rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Vyhrazená jádra na jednu oblast a kvótu pro rodinu virtuálních počítačů, která platí pro vytváření výpočetních instancí, jsou sjednocená a sdílená s Azure Machine Learning školením kvóty výpočetních clusterů. Zastavení výpočetní instance neuvolní kvótu, aby bylo zajištěno, že budete moci restartovat výpočetní instanci.


### <a name="create-on-behalf-of-preview"></a>Vytvořit jménem uživatele (Preview)

Jako správce můžete vytvořit instanci služby COMPUTE jménem odborníka na data a přiřadit k nim instanci s těmito možnostmi:
* [Šablona Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Podrobnosti o tom, jak najít TenantID a ObjectID potřebné v této šabloně, najdete v tématu [Vyhledání ID objektů identity pro konfiguraci ověřování](../healthcare-apis/find-identity-object-ids.md).  Tyto hodnoty můžete také najít na portálu Azure Active Directory.
* REST API

Odborník na data, který vytvoříte výpočetní instanci pro, potřebuje následující oprávnění Azure RBAC: 
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/spustit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/zastavit/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/restartovat/akce*
* *Microsoft. MachineLearningServices/pracovní prostory/výpočty/applicationaccess/Action*

Vědecký pracovník dat může spustit, zastavit a restartovat výpočetní instanci. Můžou použít výpočetní instanci pro:
* Jupyter
* JupyterLab
* RStudio
* Integrované poznámkové bloky

## <a name="compute-target"></a>Cílový výpočetní objekt

Výpočetní instance se dají použít jako [školicí cíl](concept-compute-target.md#train) Azure Machine Learning pro výpočetní prostředky, podobně jako clustery výpočetního školení. 

Výpočetní instance:
* Má frontu úloh.
* Spustí úlohy bezpečně ve virtuálním síťovém prostředí, aniž by museli podniky otevřít port SSH. Úloha se spustí v kontejnerovém prostředí a zabalí závislosti vašich modelů v kontejneru Docker.
* Může spustit více malých úloh paralelně (ve verzi Preview).  Dvě úlohy na jádro můžou běžet paralelně, zatímco zbývající úlohy jsou zařazené do fronty.
* Podpora distribuovaných školicích úloh s jedním uzlem

Výpočetní instanci můžete použít jako cíl nasazení místní Inferencing pro scénáře testování a ladění.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Co se stalo s virtuálním počítačem poznámkového bloku?

Výpočetní instance nahrazují virtuální počítač poznámkového bloku.  

Všechny soubory poznámkových bloků uložené ve sdílené složce pracovního prostoru a data v úložištích dat pracovního prostoru budou přístupná z instance Compute. Všechny vlastní balíčky, které jste dřív nainstalovali na virtuální počítač poznámkového bloku, se ale musí přeinstalovat na výpočetní instanci. Omezení kvóty, která platí pro vytváření výpočetních clusterů, se vztahují také na vytváření výpočetních instancí.

Nelze vytvořit nové virtuální počítače poznámkového bloku. Máte ale pořád přístup k vytvořeným virtuálním počítačům s poznámkovým blokem s plnou funkčností. Výpočetní instance se dají vytvořit ve stejném pracovním prostoru jako stávající virtuální počítače s poznámkovým blokem.


## <a name="next-steps"></a>Další kroky

* [Vytvoření a Správa výpočetní instance](how-to-create-manage-compute-instance.md)
* [Kurz: analýza prvního modelu ml](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.
