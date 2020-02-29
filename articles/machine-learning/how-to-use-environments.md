---
title: Vytváření opakovaně použitelných prostředí ML
titleSuffix: Azure Machine Learning
description: Vytvářejte a spravujte prostředí pro školení a nasazení modelů. Spravujte balíčky Pythonu a další nastavení pro prostředí.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 0cb76884fd46a45bb45fa3e29a03a6f9dbd0250b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920293"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Opětovné použití prostředí pro školení a nasazení pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvářet a spravovat Azure Machine Learning [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Prostředí můžete použít ke sledování a reprodukování závislostí softwaru projektů při jejich vývoje.

Správa závislostí softwaru je běžným úkolem pro vývojáře. Chcete zajistit, aby sestavení byla reprodukovatelná bez obsáhlé ruční konfigurace softwaru. Účty Azure Machine Learning `Environment` třídy pro místní vývojová řešení, jako jsou PIP a Conda, a nabízí řešení pro místní i distribuovaný vývoj v cloudu.

Příklady v tomto článku ukazují, jak:

* Vytvořte prostředí a určete závislosti balíčku.
* Načítají a aktualizují prostředí.
* Použijte prostředí pro školení.
* Pro nasazení webové služby použijte prostředí.

Základní informace o tom, jak prostředí pracují v Azure Machine Learning, najdete v tématu [co jsou to prostředí ml](concept-environments.md).

## <a name="prerequisites"></a>Předpoklady

* [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Vytvoření prostředí

V následujících oddílech se seznámíte s různými způsoby, jak můžete vytvořit prostředí pro vaše experimenty.

### <a name="use-a-curated-environment"></a>Použití spravovaného prostředí

Můžete vybrat jedno z uspořádaného prostředí, kde začínáte: 

* Prostředí _AzureML-Minimal_ obsahuje minimální sadu balíčků pro povolení sledování spuštění a nahrávání assetů. Můžete ho použít jako výchozí bod pro vlastní prostředí.

* Prostředí _AzureML-tutorial_ obsahuje běžné balíčky pro datové vědy. Tyto balíčky zahrnují Scikit-učení, PANDAS, matplotlib a větší sadu balíčků AzureML-SDK.

Podložená prostředí se zálohují pomocí imagí Docker uložených v mezipaměti. Tato možnost zálohování snižuje náklady na přípravu za běhu.

Použijte metodu `Environment.get` k výběru jednoho ze spravovaných prostředí:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Seznamovaná prostředí a jejich balíčky můžete zobrazit pomocí následujícího kódu:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Nespouštějte vlastní název prostředí s předponou _AzureML_ . Tato předpona je vyhrazena pro podané prostředí.

### <a name="instantiate-an-environment-object"></a>Vytvoření instance objektu prostředí

Chcete-li vytvořit prostředí ručně, importujte třídu `Environment` ze sady SDK. Pak použijte následující kód pro vytvoření instance objektu prostředí.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Použití souborů specifikace conda a PIP

Můžete také vytvořit prostředí ze specifikace conda nebo souboru požadavků PIP. Použijte metodu [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) nebo metodu [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) . Do argumentu metoda zahrňte název vašeho prostředí a cestu k souboru, který chcete.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Použití existujících prostředí conda

Pokud máte v místním počítači existující prostředí Conda, můžete službu použít k vytvoření objektu prostředí. Pomocí této strategie můžete své místní interaktivní prostředí použít na vzdáleném spuštění.

Následující kód vytvoří objekt prostředí z existujícího prostředí conda `mycondaenv`. Používá metodu [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Vytvářet prostředí automaticky

Automatické vytvoření prostředí tím, že odešlete školicí běh. Odešlete běh pomocí metody `submit()`. Když odešlete školicí běh, může sestavení nového prostředí trvat několik minut. Doba sestavení závisí na velikosti požadovaných závislostí. 

Pokud před odesláním spuštění neurčíte prostředí v konfiguraci spuštění, vytvoří se pro vás výchozí prostředí.

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

Podobně pokud používáte [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) objekt pro školení, můžete přímo odeslat instanci Estimator jako běh bez určení prostředí. Objekt `Estimator` již zapouzdřuje prostředí a výpočetní cíl.

## <a name="add-packages-to-an-environment"></a>Přidání balíčků do prostředí

Přidejte balíčky do prostředí pomocí souborů Conda, PIP nebo privátního kola. Určete jednotlivé závislosti balíčků pomocí [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) třídy. Přidejte ho do `PythonSection`prostředí.

### <a name="conda-and-pip-packages"></a>Balíčky conda a PIP

Pokud je balíček k dispozici v úložišti balíčků Conda, doporučujeme použít instalaci conda spíše než instalaci PIP. Balíčky conda se typicky dodávají s předem vytvořenými binárními soubory, které zajistí spolehlivější instalaci.

Následující příklad přidá do prostředí. Přidá 0.21.3 verze `scikit-learn`. Přidá také balíček `pillow` `myenv`. V příkladu se používá metoda [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) a metoda [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) , v uvedeném pořadí.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Soubory soukromých kol

Soubory privátního kolečka PIP můžete použít tak, že je napřed nahrajete do úložiště pracovního prostoru. Nahrajete je pomocí statické [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) metody. Pak zachytíte adresu URL úložiště a předáte adresu URL metodě `add_pip_package()`.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Správa prostředí

Spravujte prostředí, abyste je mohli aktualizovat, sledovat a opakovaně používat napříč výpočetními cíli a dalšími uživateli pracovního prostoru.

### <a name="register-environments"></a>Registrovat prostředí

Prostředí se do vašeho pracovního prostoru automaticky zaregistruje, když odešlete spuštění nebo nasazení webové služby. Prostředí můžete také ručně zaregistrovat pomocí metody [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Tato operace zpřístupňuje prostředí do entity, která je sledována a ve verzi cloudu. Entitu lze sdílet mezi uživateli pracovního prostoru.

Následující kód zaregistruje `myenv` prostředí do pracovního prostoru `ws`.

```python
myenv.register(workspace=ws)
```

Při prvním použití prostředí v rámci školení nebo nasazení je tato možnost zaregistrována v pracovním prostoru. Pak je sestaven a nasazen na výpočetním cíli. Služba ukládá prostředí do mezipaměti. Použití prostředí s mezipamětí trvá mnohem kratší dobu než používání nové služby nebo aktualizace, kterou jste aktualizovali.

### <a name="get-existing-environments"></a>Získat existující prostředí

Třída `Environment` nabízí metody, které umožňují načíst existující prostředí v pracovním prostoru. Prostředí můžete načíst podle názvu, jako seznamu nebo pomocí konkrétního školicího běhu. Tyto informace jsou užitečné při řešení problémů, auditování a reprodukovatelnosti.

#### <a name="view-a-list-of-environments"></a>Zobrazení seznamu prostředí

Zobrazte prostředí v pracovním prostoru pomocí třídy [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) . Pak vyberte prostředí, které chcete znovu použít.

#### <a name="get-an-environment-by-name"></a>Získat prostředí podle názvu

Konkrétní prostředí můžete také získat podle názvu a verze. Následující kód používá metodu [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) k načtení `1` verze `myenv` prostředí v pracovním prostoru `ws`.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Výuka prostředí specifického pro běh

Chcete-li získat prostředí, které bylo použito pro konkrétní spuštění po dokončení školení, použijte metodu [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) ve třídě `Run`.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualizace existujícího prostředí

Řekněme, že změníte existující prostředí, například přidáním balíčku Pythonu. Když odešlete spuštění, nasadíte model nebo ručně zaregistrujete prostředí, vytvoří se nová verze prostředí. Správa verzí umožňuje zobrazit změny prostředí v průběhu času.

Pokud chcete aktualizovat verzi balíčku Pythonu v existujícím prostředí, zadejte číslo verze tohoto balíčku. Pokud nepoužíváte přesné číslo verze, pak Azure Machine Learning znovu použít stávající prostředí s původní verzí balíčku.

### <a name="debug-the-image-build"></a>Ladění sestavení image

Následující příklad používá metodu [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) k ručnímu vytvoření prostředí jako image Docker. Monitoruje výstupní protokoly ze sestavení imagí pomocí [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Sestavená image se pak zobrazí v instanci Azure Container Registry pracovního prostoru. Tyto informace jsou užitečné pro ladění.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Povolit Docker

 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) třídy `Environment` Azure Machine Learning vám umožní přesně přizpůsobit a řídit hostovaný operační systém, na kterém spouštíte školení.

Když povolíte Docker, služba vytvoří image Docker. Vytvoří také prostředí Pythonu, které používá vaše specifikace v rámci tohoto kontejneru Docker. Tato funkce poskytuje další izolaci a reprodukovatelnost pro vaše školicí běhy.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Ve výchozím nastavení se nově vytvořená image Docker zobrazí v registru kontejneru, který je přidružený k pracovnímu prostoru.  Název úložiště má *\<\>UUID ve formátu AzureML/azureml_* . Část názvu jedinečného identifikátoru (*UUID*) odpovídá hodnotě hash vypočítané z konfigurace prostředí. Tato korespondence umožňuje službě zjistit, jestli image pro dané prostředí už existuje pro opakované použití.

Služba navíc automaticky používá jednu ze [základních imagí](https://github.com/Azure/AzureML-Containers)založených na Ubuntu Linux. Nainstaluje zadané balíčky Pythonu. Základní image má verze procesoru a GPU. Azure Machine Learning automaticky zjišťuje, která verze se má použít.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Případně můžete zadat vlastní souboru Dockerfile. Je nejjednodušší začít z jedné z Azure Machine Learning základní Image pomocí příkazu Docker ```FROM``` a pak přidat vlastní kroky. Tento postup použijte v případě, že potřebujete instalovat balíčky mimo Python jako závislosti.

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

> [!NOTE]
> Pokud zadáte `environment.python.user_managed_dependencies=False` při použití vlastní image Docker, služba vytvoří v imagi prostředí conda. Místo použití knihoven Pythonu, které jste nainstalovali na základní image, spustí spuštění v tomto prostředí. Nastavte parametr na `True`, aby používal vaše vlastní nainstalované balíčky.

## <a name="use-environments-for-training"></a>Použití prostředí pro školení

Chcete-li odeslat školicí běh, je třeba zkombinovat prostředí, [cíl výpočtů](concept-compute-target.md)a školicí skript Pythonu do konfigurace spuštění. Tato konfigurace je Obálkový objekt, který se používá pro odeslání spuštění.

Když odešlete školicí běh, může vytváření nového prostředí trvat několik minut. Doba trvání závisí na velikosti požadovaných závislostí. Služba ukládá do mezipaměti prostředí. Takže pokud definice prostředí zůstane beze změny, nebudete se zabývat úplným časem nastavení jenom jednou.

Následující příklad spuštění místního skriptu ukazuje, kde byste jako objekt obálky použili [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) .

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
> Pokud chcete zakázat historii spuštění nebo spustit snímky, použijte nastavení v části `ScriptRunConfig.run_config.history`.

Pokud v konfiguraci spuštění neurčíte prostředí, služba vytvoří při odeslání běhu výchozí prostředí.

### <a name="use-an-estimator-for-training"></a>Použití Estimator pro školení

Pokud používáte [Estimator](how-to-train-ml-models.md) pro školení, můžete instanci Estimator odeslat přímo. Už ho zapouzdřuje prostředí a výpočetní cíl.

Následující kód používá Estimator ke spuštění výukového cvičení s jedním uzlem. Spouští se na vzdálené výpočetní prostředky pro model `scikit-learn`. Předpokládá, že jste dříve vytvořili objekt cíle výpočtů, `compute_target`a objekt úložiště dat `ds`.

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

## <a name="use-environments-for-web-service-deployment"></a>Použití prostředí pro nasazení webové služby

Prostředí můžete použít při nasazení modelu jako webové služby. Tato možnost umožňuje reprodukovatelně připojenému pracovnímu postupu. V tomto pracovním postupu můžete model prosazovat, testovat a nasadit pomocí stejných knihoven ve výpočetním prostředí a ve výpočetním prostředí pro odvození.

Chcete-li nasadit webovou službu, kombinování prostředí, výpočetního skriptu, vyhodnocování a zaregistrovaného modelu do objektu nasazení, [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Další informace najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

V tomto příkladu Předpokládejme, že jste dokončili školicí běh. Nyní chcete nasadit tento model do Azure Container Instances. Při sestavování webové služby jsou soubory modelů a bodování připojené k imagi a do image se přidá zásobník odvození Azure Machine Learning.

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

## <a name="example-notebooks"></a>Příklad poznámkové bloky

Tento [ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) se rozbalí na základě konceptů a metod popsaných v tomto článku.

[Nasazení modelu pomocí vlastního základního obrázku Docker](how-to-deploy-custom-docker-image.md) ukazuje, jak nasadit model pomocí vlastní základní image Docker.

Tento [ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) ukazuje, jak nasadit model Spark jako webovou službu.

## <a name="create-and-manage-environments-with-the-cli"></a>Vytváření a Správa prostředí pomocí rozhraní příkazového řádku

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) zrcadlí většinu funkcí sady Python SDK. Můžete ho použít k vytváření a správě prostředí. Příkazy, které probereme v této části, ukazují základní funkce.

Následující příkaz vygeneruje soubory pro výchozí definici prostředí v určeném adresáři. Tyto soubory jsou soubory JSON. Fungují jako odpovídající třída v sadě SDK. Soubory můžete použít k vytvoření nových prostředí s vlastními nastaveními. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Spuštěním následujícího příkazu zaregistrujete prostředí ze zadaného adresáře.

```azurecli-interactive
az ml environment register -d myenvdir
```

Spuštěním následujícího příkazu zobrazíte seznam všech registrovaných prostředí.

```azurecli-interactive
az ml environment list
```

Zaregistrované prostředí si stáhněte pomocí následujícího příkazu.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Další kroky

* Postup použití spravovaného cílového výpočetního prostředí pro výuku modelu najdete v tématu [kurz: výuka modelu](tutorial-train-models-with-aml.md).
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Podívejte se na [odkaz`Environment` třídy SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Další informace o konceptech a metodách popsaných v tomto článku najdete v [ukázkovém poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
