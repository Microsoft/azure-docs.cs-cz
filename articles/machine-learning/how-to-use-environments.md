---
title: Vytvoření opakovaně použitelných prostředí ML
titleSuffix: Azure Machine Learning
description: Vytvářejte a spravujte prostředí pro školení a nasazení modelu. Spravujte balíčky Pythonu a další nastavení pro prostředí.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536348"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Opakované použití prostředí pro školení a nasazení pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit a spravovat [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning . Pomocí prostředí můžete sledovat a reprodukovat závislosti softwaru vašich projektů ve vývoji.

Správa závislostí softwaru je běžnou úlohou vývojářů. Chcete zajistit, aby sestavení byla reprodukovatelná bez rozsáhlé ruční konfigurace softwaru. Třída Azure `Environment` Machine Learning účty pro řešení místního vývoje, jako je pip a Conda a poskytuje řešení pro místní i distribuovaný vývoj cloudu.

Příklady v tomto článku ukazují, jak:

* Vytvořte prostředí a zadejte závislosti balíčků.
* Načíst a aktualizovat prostředí.
* Použijte prostředí pro školení.
* Použití prostředí pro nasazení webové služby.

Podrobný přehled fungování prostředí v Azure Machine Learning najdete v [tématu Co jsou prostředí ML?](concept-environments.md).

## <a name="prerequisites"></a>Požadavky

* Sada [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Pracovní [prostor Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Vytvoření prostředí

V následujících částech se zabývá několika způsoby vytvoření prostředí pro experimenty.

### <a name="use-a-curated-environment"></a>Použití kurátorovaného prostředí

Můžete vybrat jedno z kurátorských prostředí, které chcete začít: 

* Prostředí _AzureML-Minimal_ obsahuje minimální sadu balíčků, které umožňují sledování spuštění a nahrávání prostředků. Můžete jej použít jako výchozí bod pro vaše vlastní prostředí.

* Prostředí _AzureML-Tutorial_ obsahuje běžné balíčky datových věd. Tyto balíčky zahrnují Scikit-Learn, Pandy, Matplotlib a větší sadu balíčků azureml-sdk.

Kurátorská prostředí jsou zálohována image dockeru v mezipaměti. Tato podpora snižuje náklady na přípravu spuštění.

Pomocí `Environment.get` metody vyberte jedno z kurátorských prostředí:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Kurátorská prostředí a jejich balíčky můžete uvést pomocí následujícího kódu:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Nezačínejte svůj vlastní název prostředí s předponou _AzureML._ Tato předpona je vyhrazena pro kurátorská prostředí.

### <a name="instantiate-an-environment-object"></a>Vytvoření instance objektu prostředí

Chcete-li ručně vytvořit prostředí, importujte třídu `Environment` ze sady SDK. Potom použijte následující kód k vytvoření instance objektu prostředí.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Použití souborů specifikací Conda a pip

Můžete také vytvořit prostředí ze specifikace Conda nebo pip požadavky souboru. Použijte [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) metodu [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) nebo metodu. Do argumentu metody uveďte název prostředí a cestu k souboru, který chcete.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Použití existujících prostředí Conda

Pokud máte existující prostředí Conda v místním počítači, můžete službu použít k vytvoření objektu prostředí. Pomocí této strategie můžete znovu použít místní interaktivní prostředí při vzdáleném spuštění.

Následující kód vytvoří objekt prostředí z existujícího `mycondaenv`prostředí Conda . Používá metodu. [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Automatické vytváření prostředí

Automaticky vytvořte prostředí odesláním tréninkového běhu. Odeslat spustit pomocí `submit()` metody. Při odeslání školení spustit, budování nového prostředí může trvat několik minut. Doba trvání sestavení závisí na velikosti požadovaných závislostí. 

Pokud před odesláním spuštění nezadáte prostředí v konfiguraci spuštění, vytvoří se pro vás výchozí prostředí.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Podobně pokud použijete [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objekt pro školení, můžete přímo odeslat instanci odhadu jako spustit bez určení prostředí. Objekt `Estimator` již zapouzdřuje prostředí a výpočetní cíl.

## <a name="add-packages-to-an-environment"></a>Přidání balíčků do prostředí

Přidejte balíčky do prostředí pomocí Conda, pip nebo soukromé soubory kol. Zadejte každou závislost balíčku [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) pomocí třídy. Přidejte jej do `PythonSection`prostředí .

### <a name="conda-and-pip-packages"></a>Balíčky Conda a pip

Pokud je balíček k dispozici v úložišti balíčků Conda, doporučujeme použít instalaci Conda spíše než instalaci pipu. Balíčky Conda se obvykle docházejí s předem sestavenými binárními soubory, díky kterým je instalace spolehlivější.

Následující příklad přidá do prostředí. Přidává verzi 1.17.0 `numpy`z . Přidává také `pillow` balíček, `myenv`. Příklad používá [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) metodu [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) a metodu, resp.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Pokud použijete stejnou definici prostředí pro jiné spuštění, služba Azure Machine Learning znovu použije bitovou kopii vašeho prostředí uloženou v mezipaměti. Pokud vytvoříte prostředí s nepřipnutým balíčkem závislost, například ```numpy```, že prostředí bude nadále používat verzi balíčku _nainstalovanou v době vytvoření prostředí_. Také jakékoli budoucí prostředí s odpovídající definicí bude nadále používat starou verzi. Další informace naleznete v [tématu Vytváření prostředí, ukládání do mezipaměti a opakované použití](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Soukromé soubory kol

Soubory soukromých kol pip uvidíte tak, že je nejprve nahrajete do úložiště pracovního prostoru. Nahrát pomocí statické [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) metody. Potom zachytíte adresu URL úložiště `add_pip_package()` a předajíte adresu URL metodě.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Správa prostředí

Spravujte prostředí, abyste je mohli aktualizovat, sledovat a znovu používat napříč výpočetními cíli a s ostatními uživateli pracovního prostoru.

### <a name="register-environments"></a>Zaregistrovat prostředí

Prostředí se automaticky zaregistruje do vašeho pracovního prostoru při odeslání spuštění nebo nasazení webové služby. Můžete také ručně zaregistrovat prostředí [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) pomocí metody. Tato operace vytvoří prostředí do entity, která je sledována a verzí v cloudu. Entitu lze sdílet mezi uživateli pracovního prostoru.

Následující kód registruje `myenv` prostředí `ws` do pracovního prostoru.

```python
myenv.register(workspace=ws)
```

Při prvním použití prostředí v školení nebo nasazení, je registrována v pracovním prostoru. Pak je sestavený a nasazený na výpočetní cíl. Služba ukládá do mezipaměti prostředí. Opětovné použití prostředí uloženého v mezipaměti trvá mnohem méně času než použití nové služby nebo služby, která byla aktualizována.

### <a name="get-existing-environments"></a>Získejte existující prostředí

Třída `Environment` nabízí metody, které umožňují načíst existující prostředí ve vašem pracovním prostoru. Prostředí můžete načíst podle názvu, jako seznam nebo podle konkrétního tréninkového běhu. Tyto informace jsou užitečné pro řešení potíží, auditování a reprodukovatelnost.

#### <a name="view-a-list-of-environments"></a>Zobrazení seznamu prostředí

Zobrazení prostředí v pracovním prostoru pomocí [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) třídy. Pak vyberte prostředí, které chcete znovu použít.

#### <a name="get-an-environment-by-name"></a>Získání prostředí podle názvu

Můžete také získat konkrétní prostředí podle názvu a verze. Následující kód používá [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metodu `1` k `myenv` načtení `ws` verze prostředí v pracovním prostoru.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Trénování prostředí specifického pro běh

Chcete-li získat prostředí, které bylo použito pro konkrétní [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) spuštění po `Run` dokončení školení, použijte metodu ve třídě.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualizace existujícího prostředí

Řekněme, že změníte existující prostředí, například přidáním balíčku Pythonu. Nová verze prostředí je pak vytvořen a odeslat spustit, nasadit model nebo ručně zaregistrovat prostředí. Správa verzí umožňuje zobrazit změny prostředí v průběhu času.

Chcete-li aktualizovat verzi balíčku Pythonu v existujícím prostředí, zadejte číslo verze pro tento balíček. Pokud nepoužijete přesné číslo verze, Azure Machine Learning znovu použije existující prostředí s jeho původní verze balíčku.

### <a name="debug-the-image-build"></a>Ladění sestavení bitové kopie

Následující příklad používá [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) metodu k ručnímu vytvoření prostředí jako image Dockeru. Monitoruje výstupní protokoly z image [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)sestavení pomocí . Vytvořená bitová kopie se pak zobrazí v instanci registru kontejnerů Azure v pracovním prostoru. Tyto informace jsou užitečné pro ladění.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Povolení Dockeru

 Třída [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) Azure Machine `Environment` Learning umožňuje jemně přizpůsobit a řídit hostovaný operační systém, na kterém spustíte školení.

Když povolíte Docker, služba vytvoří image Dockeru. Vytvoří také prostředí Pythonu, které používá vaše specifikace v rámci tohoto kontejneru Dockeru. Tato funkce poskytuje další izolaci a reprodukovatelnost pro vaše spuštění školení.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Ve výchozím nastavení se nově vytvořená image Dockeru zobrazí v registru kontejneru, který je přidružen k pracovnímu prostoru.  Název úložiště má formu *azureml/azureml_\<uuid\>*. Jedinečná část názvu *(uuid)* odpovídá hash, který je vypočítán z konfigurace prostředí. Tato korespondence umožňuje službě určit, zda obrázek pro dané prostředí již existuje pro opakované použití.

Kromě toho služba automaticky používá jeden ze [základních bitových kopií](https://github.com/Azure/AzureML-Containers)založených na Ubuntu Linux . Nainstaluje zadané balíčky Pythonu. Základní bitová kopie má verze procesoru a GPU. Azure Machine Learning automaticky zjistí, kterou verzi použít.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Můžete také zadat vlastní Dockerfile. Je nejjednodušší začít z jedné ze základních ibi ```FROM``` Azure Machine Learning pomocí příkazu Docker a pak přidat vlastní kroky. Tento přístup použijte, pokud potřebujete nainstalovat balíčky mimo Python jako závislosti.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Použití závislostí spravovaných uživateli

V některých situacích může vaše vlastní základní image již obsahovat prostředí Pythonu s balíčky, které chcete použít.

Ve výchozím nastavení služba Azure Machine Learning vytvoří prostředí Conda se závislostmi, které jste zadali, a spustí spuštění v tomto prostředí namísto použití všech knihoven Pythonu, které jste nainstalovali na základní bitové kopii. 

Chcete-li použít vlastní nainstalované `Environment.python.user_managed_dependencies = True`balíčky, nastavte parametr . Ujistěte se, že základní bitová kopie obsahuje interpret Pythonu a má balíčky, které váš školicí skript potřebuje.

Chcete-li například spustit v základním prostředí Miniconda, který má nainstalovaný balíček NumPy, nejprve zadejte Dockerfile s krokem k instalaci balíčku. Potom nastavte závislosti spravované uživatelem na `True`. 

Můžete také určit cestu k určitému interpretu Pythonu `Environment.python.interpreter_path` v rámci obrázku nastavením proměnné.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Použití prostředí pro školení

Chcete-li odeslat trénovací běh, musíte kombinovat vaše prostředí, [výpočetní cíl](concept-compute-target.md)a váš tréninkový skript Pythonu do konfigurace spuštění. Tato konfigurace je objekt obálky, který se používá pro odesílání spustí.

Při odeslání tréninkového běhu může sestavení nového prostředí trvat několik minut. Doba trvání závisí na velikosti požadovaných závislostí. Prostředí jsou ukládány do mezipaměti službou. Takže tak dlouho, dokud definice prostředí zůstane beze změny, vynaloží celý čas nastavení pouze jednou.

Následující místní skript spustit příklad ukazuje, kde byste použít [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) jako objekt obálky.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Chcete-li zakázat historii spuštění nebo spouštět snímky, použijte nastavení v části `ScriptRunConfig.run_config.history`.

Pokud nezadáte prostředí v konfiguraci spuštění, pak služba vytvoří výchozí prostředí při odeslání spustit.

### <a name="use-an-estimator-for-training"></a>Použití odhadu pro školení

Pokud použijete [odhad](how-to-train-ml-models.md) pro školení, pak můžete odeslat instanci odhadu přímo. Již zapouzdřuje prostředí a výpočetní cíl.

Následující kód používá odhad pro trénování jednoho uzlu. Běží na vzdáleném výpočetním počítači `scikit-learn` pro model. Předpokládá, že jste dříve vytvořili `compute_target`cílový objekt výpočetního `ds`prostředí a objekt úložiště dat .

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Použití prostředí pro nasazení webových služeb

Prostředí můžete použít při nasazení modelu jako webové služby. Tato funkce umožňuje reprodukovatelný, připojený pracovní postup. V tomto pracovním postupu můžete trénovat, testovat a nasazovat model pomocí stejných knihoven v tréninkových výpočetních výpočtech i ve výpočtu odvození.

Chcete-li nasadit webovou službu, zkombinujte prostředí, odvozené výpočetní [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)prostředky, skript hodnocení a registrovaný model v objektu nasazení . Další informace naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

V tomto příkladu předpokládejme, že jste dokončili tréninkový běh. Teď chcete nasadit tento model do Azure Container Instances. Při vytváření webové služby modelu a vyhodnocovací soubory se namontují na image a azure machine learning inference zásobníku se přidá do image.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Ukázkové poznámkové bloky

Tento [příklad poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) rozšiřuje koncepty a metody uvedené v tomto článku.

[Nasazení modelu pomocí vlastní základní image Dockeru](how-to-deploy-custom-docker-image.md) ukazuje, jak nasadit model pomocí vlastní základní image Dockeru.

Tento [příklad poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) ukazuje, jak nasadit model Spark jako webovou službu.

## <a name="create-and-manage-environments-with-the-cli"></a>Vytváření a správa prostředí pomocí cli

Nastavení [příkazu KRu azure machine learningu](reference-azure-machine-learning-cli.md) odráží většinu funkcí sady Python SDK. Můžete ji použít k vytvoření a správě prostředí. Příkazy, které popisujeme v této části ukazují základní funkce.

Následující příkaz přilechrá k souborům pro výchozí definici prostředí v zadaném adresáři. Tyto soubory jsou JSON soubory. Fungují jako odpovídající třídy v sdk. Soubory můžete použít k vytvoření nových prostředí, která mají vlastní nastavení. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Chcete-li zaregistrovat prostředí ze zadaného adresáře, spusťte následující příkaz.

```azurecli-interactive
az ml environment register -d myenvdir
```

Spuštěním následujícího příkazu zobrazíte seznam všech registrovaných prostředí.

```azurecli-interactive
az ml environment list
```

Stáhněte si registrované prostředí pomocí následujícího příkazu.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Další kroky

* Pokud chcete použít spravovaný výpočetní cíl k trénování modelu, [přečtěte si informace o kurzu: Trénování modelu](tutorial-train-models-with-aml.md).
* Po vytvoření trénovaného modelu se dozvíte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Zobrazení [ `Environment` odkazu sady Class SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Další informace o konceptech a metodách popsaných v tomto článku naleznete v [příkladu poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
