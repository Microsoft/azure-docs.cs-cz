---
title: Co je nového ve verzi?
titleSuffix: Azure Machine Learning
description: Seznamte se s nejnovějšími aktualizacemi pro Azure Machine Learning a sady SDK pro strojové učení a přípravu dat v Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ffd459980c4750295e1045c3f8c1bb7fc18a25df
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689364"
---
# <a name="azure-machine-learning-release-notes"></a>Poznámky k verzi Azure Machine Learning

V tomto článku se dozvíte o Azure Machine Learning verzích.  Úplný referenční obsah sady SDK najdete na referenční stránce Azure Machine Learning [**hlavní sadě SDK pro Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .

Zobrazit [seznam známých problémů](resource-known-issues.md) Další informace o známých chyb a jejich řešení.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Sada SDK Azure Machine Learning pro Python v 1.0.83

+ **Nové funkce**
  + Datová sada: Přidejte dvě možnosti `on_error` a `out_of_range_datetime`, aby `to_pandas_dataframe` selžou, pokud data obsahují chybové hodnoty namísto jejich naplňování `None`.

+ **Opravy chyb a vylepšení**
  + **AzureML-automl-runtime**
    + Opravili jsme regresi, která způsobila vyvolání TypeError při spuštění AutoML ve verzích Pythonu pod 3.5.4.
  + **azureml-core**
    + Opravená chyba v `datastore.upload_files`, kde není možné použít relativní cestu, která nespustila s `./`.
    + Přidání zpráv o zastaralosti pro všechny třídy imagí cest
    + Pevná Správa modelů konstrukce URL pro oblast Mooncake
    + Opravili jsme problém, kdy se pro Azure Functions nedaly použít modely s source_dir.    
    + Přidání možnosti do [prostředí. build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) pro nahrání image do registru kontejneru pracovního prostoru AzureML
    + Sada SDK se aktualizovala tak, aby používala novou knihovnu tokenů v Azure synapse s nekompatibilním způsobem.
  + **AzureML-interpretace**
    + Opravila se chyba, kde nebyla vrácena žádná vysvětlení, pokud nebyla k dispozici žádná vysvětlení ke stažení. Nyní vyvolá výjimku, která odpovídá chování jinde.
  + **azureml-pipeline-steps**
    + Pokud se `Estimator` použije v `EstimatorStep`, nepovolí předávání `DatasetConsumptionConfig`s `Estimator`parametru `inputs`.
  + **AzureML-SDK**
    + Přidání klienta AutoML do balíčku AzureML-SDK, který umožňuje odeslání vzdálených AutoMLch testů bez instalace úplného balíčku AutoML.
  + **AzureML-vlak-automl-Client**
    + Opravené zarovnání pro výstup konzoly pro automl běhy
    + Opravili jsme chybu, při které může být na vzdáleném amlcompute nainstalovaná nesprávná verze PANDAS.


## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Sada SDK Azure Machine Learning pro Python v 1.0.81

+ **Opravy chyb a vylepšení**
  + **AzureML-contrib – interpretace**
    + odložit závislost Shap na interpretaci z AzureML-interpretace
  + **azureml-core**
    + Cíl výpočtů se teď dá zadat jako parametr pro odpovídající objekty konfigurace nasazení. To je konkrétně název výpočetního cíle pro nasazení, nikoli objekt sady SDK.
    + Přidání informací CreatedBy do modelu a objektů služby. Je možné, že je k dispozici prostřednictvím <var>. created_by
    + Opravený ContainerImage. Run (), který nesprávně nastavil port HTTP kontejneru Docker.
    + Nastavit `azureml-dataprep` jako volitelné pro příkaz `az ml dataset register` CLI
  + **azureml-dataprep**
    + Opravili jsme chybu, kdy se TabularDataset. to_pandas_dataframe nesprávně vraťte do alternativního čtecího zařízení a vytiskněte upozornění.
  + **azureml-explain-model**
    + odložit závislost Shap na interpretaci z AzureML-interpretace
  + **azureml-pipeline-core**
    + Přidali jsme nový krok `NotebookRunnerStep`kanálu, aby se místní Poznámkový blok spustil jako krok v kanálu.
    + Odebrání zastaralých funkcí get_all pro PublishedPipelines, plány a PipelineEndpoints
  + **AzureML-vlak-automl-Client**
    + Začalo vyřazení data_script jako vstupu do AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Sada SDK Azure Machine Learning pro Python v 1.0.79

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + Odebrané featurizationConfig k zaznamenání
      + Aktualizované protokolování do protokolu "auto"/"vypnuto"/"přizpůsobené".
  + **AzureML-automl-runtime**
    + Byla přidána podpora pro PANDAS. Řady a PANDAS. Kategorií pro detekci datového typu sloupce. Dřív podporovaná numpy. ndarray
      + Přidání souvisejících změn kódu ke správnému zpracování dtype kategorií.
    + Bylo vylepšeno rozhraní funkce FORECAST: parametr y_pred byl nepovinný. – Docstrings se vylepšily.
  + **AzureML-contrib-DataSet**
    + Opravili jsme chybu, kdy se nepovedlo připojit k datovým sadám s popisky.
  + **azureml-core**
    + Oprava chyby pro `Environment.from_existing_conda_environment(name, conda_environment_name)`. Uživatel může vytvořit instanci prostředí, která je přesnou replikou místního prostředí.
    + Změny metod datových sad souvisejících s časovou řadou se změnily na `include_boundary=True` ve výchozím nastavení.
  + **AzureML-vlak-automl-Client**
    + Opravili jsme problém, kdy se výsledky ověřování netisknou, pokud je hodnota zobrazit výstup nastavená na false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Sada SDK Azure Machine Learning pro Python v 1.0.76

+ **Rozbíjející změny v**
  + AutoML problémy s upgradem pro AzureML
    + Upgrade na AzureML-vlak-automl > = 1.0.76 z AzureML-vlak-automl < 1.0.76 může způsobit částečné instalace, což způsobí selhání některých automl importů. Chcete-li tento problém vyřešit, můžete spustit instalační skript, který najdete na adrese https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Nebo pokud používáte PIP přímo, můžete:
      + "instalace PIP--upgrade AzureML-výukový-automl"
      + "instalace PIP--Ignore-installed-installed-automl-Client"
    + nebo můžete před upgradem odinstalovat starou verzi.
      + "PIP Uninstall AzureML-vlak-automl"
      + "PIP Install AzureML-vlak-automl"

+ **Opravy chyb a vylepšení**
  + **AzureML-automl-runtime**
    + AutoML bude nyní brát v úvahu třídy true i false při výpočtu průměrných skalárních metrik pro binární úlohy klasifikace.
    + Strojové učení a školicí kód v procesu AzureML-AutoML-Core byly přesunuty do nového balíčku AzureML-AutoML-runtime.
  + **AzureML-contrib-DataSet**
    + Při volání `to_pandas_dataframe` u s popsánou datovou sadou s možností stažení teď můžete určit, jestli se mají stávající soubory přepsat nebo ne.
    + Při volání `keep_columns` nebo `drop_columns`, které vedou k vyřazení sloupce časové řady, Label nebo Image, se pro datovou sadu zahodí i odpovídající funkce.
    + Opravili jsme problém s zavaděčem pytorch pro úlohu detekce objektu.
  + **AzureML-contrib – interpretace**
    + Odebrali jsme widget na řídicím panelu vysvětlení z AzureML-contrib-interpretované, změněného balíčku, který odkazuje na nový v interpret_community
    + Aktualizovaná verze interpretu – komunita na 0.2.0
  + **azureml-core**
    + Zvyšte výkon `workspace.datasets`.
    + Přidání možnosti registrace Azure SQL Database úložiště dat pomocí ověřování uživatelského jména a hesla
    + Oprava pro načtení RunConfigurations z relativních cest
    + Při volání `keep_columns` nebo `drop_columns`, které vedou k vyřazení sloupce časové řady, se pro datovou sadu zahodí i odpovídající funkce.
  + **AzureML-interpretace**
    + aktualizovaná verze interpretu – komunita na 0.2.0
  + **azureml-pipeline-steps**
    + Dokumentované podporované hodnoty pro kroky `runconfig_pipeline_params` pro kanál služby Azure Machine Learning.
  + **azureml-pipeline-core**
    + Přidání možnosti CLI pro stažení výstupu ve formátu JSON pro příkazy kanálu.
  + **azureml-train-automl**
    + Rozdělení AzureML-vlak-AutoML na 2 balíčky, klientský balíček AzureML-vlak-AutoML-Client a a ML školicí balíček AzureML-vlak-AutoML-runtime
  + **AzureML-vlak-automl-Client**
    + Přidali jsme tenký klient pro odesílání AutoML experimentů, aniž byste museli instalovat žádné závislosti ve strojovém učení místně.
    + Pevné protokolování automaticky zjištěných prodlevy, velikostí oken a maximálního počtu horizontů ve vzdálených spuštěních.
  + **AzureML-vlak-automl-runtime**
    + Přidání nového balíčku AutoML k izolaci strojového učení a běhových komponent z klienta.
  + **AzureML-contrib-výuka-RL**
    + Přidala se podpora výuky pro zvýšení podpory v sadě SDK.
    + Přidala se podpora AmlWindowsCompute v sadě RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Sada SDK Azure Machine Learning pro Python v 1.0.74

  + **Funkce ve verzi Preview**
    + **AzureML-contrib-DataSet**
      + Po importu AzureML-contrib-DataSet můžete volat `Dataset.Labeled.from_json_lines` místo `._Labeled` vytvořit datovou sadu s popisky.
      + Při volání `to_pandas_dataframe` u s popsánou datovou sadou s možností stažení teď můžete určit, jestli se mají stávající soubory přepsat nebo ne.
      + Při volání `keep_columns` nebo `drop_columns`, které vedou k vyřazení sloupce časové řady, popisku nebo obrázku, se pro datovou sadu zahodí i odpovídající funkce.
      + Opravili jsme problémy s PyTorch zavaděčem při volání `dataset.to_torchvision()`.

+ **Opravy chyb a vylepšení**
  + **azure-cli-ml**
    + Přidání profilování modelu do rozhraní příkazového řádku Preview
    + Opravuje zásadní změnu v Azure Storage způsobující selhání AzureML CLI.
    + Přidání typu Load Balancer do MLC pro AKS typy
  + **AzureML-automl – jádro**
    + Opravili jsme problém s detekcí maximálního horizontu v časové řadě, pro které chybí hodnoty a násobky zrn.
    + Opravili jsme problém s chybami při generování rozdělení vzájemného ověření.
    + Nahraďte tuto část zprávou ve formátu Markdownu, která se zobrazí v poznámkách k verzi: – vylepšené zpracování krátkých zrn v sadách dat prognózy.
    + Opravili jsme problém s maskou některých informací o uživatelích během protokolování. – Vylepšené protokolování chyb během spuštění prognózování.
    + Přidání psutil jako závislosti conda do automaticky generovaného souboru YML nasazení.
  + **AzureML-contrib-Mir**
    + Opravuje zásadní změnu v Azure Storage způsobující selhání AzureML CLI.
  + **azureml-core**
    + Opravuje chybu, která způsobila, že modely nasazené v Azure Functions vytvořily 500.
    + Opravili jsme problém, kdy se soubor amlignore na snímcích nepoužil.
    + Bylo přidáno nové rozhraní API amlcompute. get_active_runs, které vrací generátor pro spuštění a spuštění ve frontě na daném amlcompute.
    + Do MLC pro typy AKS byl přidán typ Load Balancer.
    + Byl přidán append_prefix parametr bool pro download_files v run.py a download_artifacts_from_prefix v artifacts_client. Tento příznak se používá k selektivnímu navýšení cesty k původnímu souboru, takže se do output_directory přidá jenom název souboru nebo složky.
    + Opravte problém deserializace pro `run_config.yml` s využitím datové sady.
    + Při volání `keep_columns` nebo `drop_columns`, které vedou k vyřazení sloupce časové řady, se pro datovou sadu zahodí i odpovídající funkce.
  + **AzureML-interpretace**
    + Aktualizovaná verze interpretu – komunita 0.1.0.3
  + **azureml-train-automl**
    + Opravili jsme problém, kdy automl_step nemusí tisknout problémy při ověřování.
    + Opravená register_model úspěšná i v případě, že v prostředí modelu chybí závislosti místně.
    + Opravili jsme problém, kdy některé vzdálené běhy nepovolily Docker.
    + Přidejte protokolování výjimky, která způsobuje, že místní spuštění selže předčasně.
  + **azureml-train-core**
    + Vezměte v úvahu resume_from běhy při výpočtu automatizovaného ladění parametrů nejlepšího podřízeného běhu.
  + **azureml-pipeline-core**
    + Pevné zpracování parametrů v konstrukci argumentů kanálu.
    + Byl přidán parametr popis kanálu a typ kroku YAML.
    + Nový formát YAML pro krok kanálu a přidání upozornění na zastaralost pro starý formát.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webové prostředí

Cílová stránka pracovního prostoru pro spolupráci na [https://ml.azure.com](https://ml.azure.com) byla vylepšena a byla upravena jako Azure Machine Learning Studio (Preview).

Z studia můžete provádět výukové, testovací, nasazování a správu Azure Machine Learningch prostředků, jako jsou datové sady, kanály, modely, koncové body a další.

Z studia získáte přístup k následujícím webovým nástrojům pro vytváření obsahu:

| Webový nástroj | Popis | Edice |
|-|-|-|
| Virtuální počítač poznámkového bloku (Preview) | Plně spravovaná cloudová pracovní stanice | Basic & Enterprise |
| [Automatizované Machine Learning](tutorial-first-experiment-automated-ml.md) (Preview) | Neexistují žádné zkušenosti s kódem pro automatizaci vývoje modelů strojového učení. | Enterprise |
| [Návrhář](concept-designer.md) (Preview) | Nástroj pro modelování strojového učení, který se dřív jmenoval jako Návrhář | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Vylepšení návrháře Azure Machine Learning

+ Dříve označované jako vizuální rozhraní 
+   11 nových [modulů](algorithm-module-reference/module-reference.md) , včetně doporučení, klasifikátorů a školicích nástrojů, včetně techniků, ověřování a transformace dat.

### <a name="r-sdk"></a>R SDK 
 
Odborníci přes data a vývojáři AI používají [Azure Machine Learning SDK pro R](tutorial-1st-r-experiment.md) k sestavování a spouštění pracovních postupů strojového učení pomocí Azure Machine Learning.

Sada Azure Machine Learning SDK pro R používá balíček `reticulate` k vytvoření vazby na sadu Python SDK. Pomocí vazby přímo na Python umožňuje sada SDK pro R přístup k základním objektům a metodám implementovaným v sadě Python SDK z libovolného prostředí jazyka R, které zvolíte.

Mezi hlavní funkce sady SDK patří:

+   Spravujte cloudové prostředky pro sledování, protokolování a organizování experimentů ve strojovém učení.
+   Výukové modely s využitím cloudových prostředků, včetně školení modelů pomocí GPU
+   Nasaďte modely jako WebServices na Azure Container Instances (ACI) a Azure Kubernetes Service (AKS).

Úplnou dokumentaci najdete na [webu balíčku](https://azure.github.io/azureml-sdk-for-r) .

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integrace s Event Grid 

Azure Machine Learning je teď poskytovatel prostředků pro Event Grid, můžete nakonfigurovat události strojového učení prostřednictvím rozhraní příkazového řádku Azure Portal nebo Azure CLI. Uživatelé můžou vytvářet události pro dokončení běhu, registraci modelů, nasazení modelů a rozpoznání posunu dat. Tyto události mohou být směrovány do obslužných rutin událostí podporovaných Event Grid pro spotřebu. Další podrobnosti najdete v článku věnovaném [schématu](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)událostí machine learningu, [konceptům](https://docs.microsoft.com/azure/machine-learning/service/concept-event-grid-integration) a [kurzům](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-event-grid) .

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Sada SDK Azure Machine Learning pro Python v 1.0.72

+ **Nové funkce**
  + Přidali jsme monitorování datových sad prostřednictvím balíčku [**AzureML-** ](https://docs.microsoft.com/python/api/azureml-datadrift) datamarketo, což umožňuje monitorovat datové sady časových řad pro posun dat nebo jiné statistické změny v průběhu času. Výstrahy a události mohou být aktivovány, pokud je zjištěn posun nebo jsou splněny jiné podmínky pro data. Podrobnosti najdete v [naší dokumentaci](https://aka.ms/datadrift) .
  + Oznamujeme dvě nové edice (označované také jako zaměnitelné SKU) v Azure Machine Learning. V této verzi teď můžete vytvořit pracovní prostor Basic nebo Enterprise Azure Machine Learning. Všechny existující pracovní prostory budou ve výchozím nastavení pro edici Basic a můžete přejít na Azure Portal nebo do studia a kdykoli upgradovat pracovní prostor. Z Azure Portal můžete vytvořit pracovní prostor Basic nebo Enterprise. Další informace najdete v [naší dokumentaci](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) . V sadě SDK se dá edice pracovního prostoru určit pomocí vlastnosti SKU objektu pracovního prostoru.
  + Provedli jsme také vylepšení Azure Machine Learning COMPUTE – nyní můžete zobrazit metriky pro vaše clustery (jako celkem uzlů, spuštěné uzly, celková základní kvóta) v Azure Monitor, kromě zobrazení diagnostických protokolů pro ladění. Kromě toho můžete také zobrazit aktuálně spuštěné nebo zařazené běhy na clusteru a podrobnosti, jako jsou například IP adresy různých uzlů v clusteru. Můžete je zobrazit buď na portálu, nebo pomocí odpovídajících funkcí v sadě SDK nebo rozhraní příkazového řádku.

  + **Funkce ve verzi Preview**
    + Ve službě Azure Machine Learning COMPUTE vydáváme podporu pro šifrování disků místního disku SSD. Pokud chcete používat tuto funkci, vyvolejte prosím lístek technické podpory, abyste získali předplatné, které je na seznamu povolených.
    + Public Preview Azure Machine Learning odvození dávky. Azure Machine Learning odvození dávky cílí na velké odvozené úlohy, které nerozlišují čas. Odvození dávky poskytuje nákladově efektivní výpočetní škálování pro odvození, s využitím neparalelní propustnosti pro asynchronní aplikace. Je optimalizovaný pro účely vysoké propustnosti, zaznamenání a zapomenout na odvození přes velké kolekce dat.
    + [**AzureML-contrib-DataSet**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Povolené funkce pro datovou sadu s popisky
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Opravy chyb a vylepšení**
  + **azure-cli-ml**
    + Rozhraní příkazového řádku teď podporuje balení modelu.
    + Byl přidán objekt CLI datové sady. Další informace: `az ml dataset --help`
    + Přidání podpory pro nasazení a sbalení podporovaných modelů (ONNX, scikit-učení a TensorFlow) bez instance InferenceConfig
    + Přidání příznaku přepsání pro nasazení služby (ACI a AKS) v sadě SDK a rozhraní příkazového řádku. Pokud je tato akce k dispozici, přepíše existující službu, pokud již existuje služba s názvem. Pokud služba neexistuje, vytvoří novou službu.
    + Modely lze registrovat pomocí dvou nových rozhraní Onnx a Tensorflow. -Registrace modelu akceptuje ukázková vstupní data, ukázkové výstupní data a konfiguraci prostředků pro model.
  + **AzureML-automl – jádro**
    + Školení iterace se spustí v podřízeném procesu jenom v případě, že jsou nastavená omezení za běhu.
    + Přidali jsme guardrail pro úlohy předpovědi, abyste zkontrolovali, jestli zadané max_horizon způsobí problémy s pamětí v daném počítači nebo ne. Pokud bude, zobrazí se zpráva guardrail.
    + Přidání podpory pro komplexní frekvence, jako je 2 roky a 1 měsíc. – Byla přidána srozumitelná chybová zpráva, pokud nelze určit četnost.
    + Pokud chcete vyřešit selhání nasazení modelu, přidejte do automaticky generovaného conda ENV výchozí hodnoty AzureML.
    + Povolí převod mezilehlých dat v kanálu Azure Machine Learning na tabulkovou sadu dat a používá se v `AutoMLStep`.
    + Implementovala se aktualizace účelu sloupce pro streamování.
    + Implementuje se aktualizace parametru transformátoru pro Imputac a HashOneHotEncoder pro streamování.
    + Byla přidána velikost aktuální velikosti dat a minimální požadovaná velikost dat pro chybové zprávy ověřování.
    + Aktualizovala se minimální požadovaná velikost dat pro křížové ověření, aby se zajistilo minimum dvou vzorků v každé přeložení pro ověření.
  + **AzureML-CLI – společné**
    + Rozhraní příkazového řádku teď podporuje balení modelu.
    + Modely lze registrovat pomocí dvou nových rozhraní Onnx a Tensorflow.
    + Registrace modelu přijímá ukázková vstupní data, ukázková výstupní data a konfiguraci prostředků pro model.
  + **AzureML-contrib-gbdt**
    + Opravili jsme kanál verze pro Poznámkový blok.
    + Bylo přidáno upozornění pro AmlCompute výpočetní cíl, který nepodporujeme.
    + Přidání LightGMB Estimator do balíčku AzureML-contrib-gbdt
  + [**AzureML-Core**](https://docs.microsoft.com/python/api/azureml-core)
    + Rozhraní příkazového řádku teď podporuje balení modelu.
    + Přidejte upozornění na zastaralost pro vystaralá rozhraní API datové sady. Viz upozornění na změnu rozhraní API datové sady na https://aka.ms/tabular-dataset.
    + Změňte [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) a vraťte název a verzi registrace, pokud je datová sada zaregistrovaná.
    + Oprava chyby, která ScriptRunConfig s datovou sadou jako argumentu se opakovaně nedá použít k odeslání experimentu.
    + Datové sady načtené během spuštění budou sledovány a lze je zobrazit na stránce Podrobnosti o spuštění nebo voláním [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) po dokončení spuštění.
    + Povolí převod mezilehlých dat v kanálu Azure Machine Learning na tabulkovou sadu dat a používá se v [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep).
    + Přidání podpory pro nasazení a sbalení podporovaných modelů (ONNX, scikit-učení a TensorFlow) bez instance InferenceConfig
    + Přidání příznaku přepsání pro nasazení služby (ACI a AKS) v sadě SDK a rozhraní příkazového řádku. Pokud je tato akce k dispozici, přepíše existující službu, pokud již existuje služba s názvem. Pokud služba neexistuje, vytvoří novou službu.
    +  Modely lze registrovat pomocí dvou nových rozhraní Onnx a Tensorflow. Registrace modelu přijímá ukázková vstupní data, ukázková výstupní data a konfiguraci prostředků pro model.
    + Bylo přidáno nové úložiště dat pro Azure Database for MySQL. Byl přidán příklad pro použití Azure Database for MySQL v DataTransferStep v kanálu Azure Machine Learning.
    + Přidání funkce pro přidání a odebrání značek z experimentů, které přidaly funkce pro odebrání značek z spuštění
    + Přidání příznaku přepsání pro nasazení služby (ACI a AKS) v sadě SDK a rozhraní příkazového řádku. Pokud je tato akce k dispozici, přepíše existující službu, pokud již existuje služba s názvem. Pokud služba neexistuje, vytvoří novou službu.
  + [**AzureML-dataunášená**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Přesunuto z `azureml-contrib-datadrift` do `azureml-datadrift`
    + Přidání podpory pro datové sady časových řad monitorování pro posun a další statistické míry
    + Nové metody `create_from_model()` a `create_from_dataset()` do třídy [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) . Metoda `create()` bude zastaralá.
    + Úpravy vizualizací v Pythonu a uživatelském rozhraní v Azure Machine Learning Studiu.
    + Podpora denního a měsíčního plánování monitorování, a to i denně pro monitorování datových sad.
    + Podpora doplňování metrik sledování dat pro analýzu historických dat pro monitorování datových sad
    + Různé opravy chyb
  + [**AzureML-kanál – jádro**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + k odeslání Azure Machine Learningého kanálu ze souboru `yaml` kanálu už není potřeba AzureML-dataprep.
  + [**AzureML-vlak – automl**](/python/api/azureml-train-automl-runtime/)
    + Pokud chcete vyřešit selhání nasazení modelu, přidejte do automaticky generovaného conda ENV výchozí hodnoty AzureML.
    + AutoML vzdálené školení teď zahrnuje AzureML – výchozí nastavení, které umožňuje znovu použít školicí obálku pro odvození.
  + **azureml-train-core**
    + Přidání podpory PyTorch 1,3 v [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) Estimator

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Vizuální rozhraní (Preview)

+ Rozhraní Azure Machine Learningho vizuálu (Preview) se převedlo na běh [Azure Machine Learning kanálů](concept-ml-pipelines.md). Kanály (dříve označované jako experimenty) vytvořené v rámci vizuálního rozhraní jsou teď plně integrované se základními Azure Machine Learning prostředími.
  + Jednotné prostředí pro správu pomocí prostředků sady SDK
  + Správa verzí a sledování modelů, kanálů a koncových bodů pro vizuální rozhraní
  + Přepracované uživatelské rozhraní
  + Přidání nasazení odvozeného z dávky
  + Přidání podpory služby Azure Kubernetes (AKS) pro cílení na odvození výpočtů
  + Nový pracovní postup vytváření kanálů v Pythonu – krok
  + Nová [cílová stránka](https://ml.azure.com) pro nástroje pro tvorbu vizuálů

+ **Nové moduly**
  + Použít matematickou operaci
  + Použít transformaci SQL
  + Oříznout hodnoty
  + Shrnout data
  + Import z databáze SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Sada SDK Azure Machine Learning pro Python v 1.0.69

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + Omezení modelových vysvětlení tak, aby se místo výpočetních vysvětlení pro každé spuštění co nejlépe spouštělo. Změna chování místních, vzdálených a ADB.
    + Přidání podpory pro vysvětlení modelu na vyžádání pro uživatelské rozhraní
    + Přidání psutil jako závislosti `automl` a zahrnutí psutil jako závislosti conda v amlcompute.
    + Opravili jsme problém s heuristickými prodlevy a velikostmi oken pro prognózování dat, jejichž série může způsobit chyby lineárního algebraický.
      + Přidání tisku pro heuristicky určené parametry při spuštění prognózování.
  + **AzureML-contrib – dataunášená**
    + Přidání ochrany při vytváření výstupních metrik, pokud posun úrovně DataSet není v první části.
  + **AzureML-contrib – interpretace**
    + balíček AzureML-contrib-vysvětlit-model byl přejmenován na AzureML-contrib-Interpret
  + **azureml-core**
    + Bylo přidáno rozhraní API pro zrušení registrace datových sad. `dataset.unregister_all_versions()`
    + balíček AzureML-contrib-vysvětlit-model byl přejmenován na AzureML-contrib-interpretace.
  + **[AzureML-Core](https://docs.microsoft.com/python/api/azureml-core)**
    + Bylo přidáno rozhraní API pro zrušení registrace datových sad. integrován. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Bylo přidáno rozhraní API datové sady pro kontrolu času změny dat. `dataset.data_changed_time`.
    + Schopnost využívat `FileDataset` a `TabularDataset` jako vstupy pro `PythonScriptStep`, `EstimatorStep`a `HyperDriveStep` v kanálu Azure Machine Learning
    + Vylepšený výkon `FileDataset.mount` pro složky s velkým počtem souborů
    + Schopnost využívat [datovou sadu](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) a [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) jako vstupy pro [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)a [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) v kanálu Azure Machine Learning.
    + Výkon objektu DataSet. [připojení ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) bylo vylepšeno pro složky s velkým počtem souborů.
    + Přidání adresy URL ke známým doporučením chyb v podrobnostech o spuštění.
    + Opravili jsme chybu v běhu. get_metrics, kde se žádosti selžou, pokud by běželo příliš mnoho podřízených objektů.
    + Opravili jsme chybu v [běhu. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) , kde se žádosti selžou, pokud by běželo příliš mnoho podřízených objektů.
    + Přidala se podpora pro ověřování v clusteru Arcadia.
    + Vytvoření objektu experimentu Získá nebo vytvoří experiment v pracovním prostoru Azure Machine Learning pro sledování historie spuštění. ID experimentu a archivovaný čas se naplní v objektu experiment při vytváření. Příklad: experiment = experiment (pracovní prostor, "nový experiment") experiment_id = experiment.id Archive () a reactivate () jsou funkce, které lze volat v experimentu pro skrytí a obnovení experimentu v uživatelském rozhraní nebo při volání funkce ve výchozím nastavení. k vypsání experimentů. Pokud se vytvoří nový experiment se stejným názvem jako archivovaný experiment, můžete při opětovné aktivaci přejmenovat archivovaný experiment tak, že předáte nový název. Může existovat pouze jeden aktivní experiment se zadaným názvem. Příklad: experiment1 = experiment (pracovní prostor, "aktivní Experimenta") experiment1. Archive () # vytvoří nový aktivní experiment se stejným názvem jako Archivovaná. experiment2. = Experiment (pracovní prostor, "aktivní experiment") experiment1. reactivate (new_name = "předchozí aktivní experiment"). seznam statických metod () při experimentování může pobírat filtr názvů a filtr element ViewType. Element ViewType hodnoty jsou "ACTIVE_ONLY", "ARCHIVED_ONLY" a "ALL". Příklad: archived_experiments = experiment. list (pracovní prostor, view_type = "ARCHIVED_ONLY") all_first_experiments = experiment. list (pracovní prostor, název = "první experiment", view_type = "ALL")
    + Podpora použití prostředí pro nasazení modelů a aktualizace služby
  + **AzureML-dataunášená**
    + Atribut show třídy DataDriftDector nepodporuje nepovinný argument with_details. Atribut show bude prezentovat pouze koeficient posunu dat a příspěvek na posun dat pro sloupce funkcí.
    + Změny chování atributu DataDriftDetector get_output:
      + Vstupní parametr start_time, end_time jsou volitelné místo povinné;
      + Zadání specifických start_time a/nebo end_time pomocí konkrétního run_id ve stejném vyvolání způsobí výjimku chyby, protože se vzájemně vylučují.
      + Po zadání specifických start_time a/nebo end_time se vrátí jenom výsledky plánovaných spuštění.
      + Parametr ' daily_latest_only ' je zastaralý.
    + Podpora načítání výstupů pro posun dat na základě datové sady
  + **azureml-explain-model**
    + Přejmenuje balíček AzureML-vysvětlit-model na AzureML-interpretace, takže starý balíček zůstane pro zpětnou kompatibilitu nyní.
    + Opravená `automl` chyba s nezpracovanými vysvětlivkami nastavenými na úlohu klasifikace místo regrese ve výchozím nastavení při stahování z ExplanationClient
    + Přidání podpory `ScoringExplainer` k přímému vytváření pomocí `MimicWrapper`
  + **azureml-pipeline-core**
    + Vylepšený výkon pro vytváření velkých kanálů
  + **azureml-train-core**
    + Přidání podpory TensorFlow 2,0 v TensorFlow Estimator
  + **azureml-train-automl**
    + Vytvoření objektu [experimentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) Získá nebo vytvoří experiment v pracovním prostoru Azure Machine Learning pro sledování historie spuštění. ID experimentu a archivovaný čas se naplní v objektu experiment při vytváření. Příklad:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archiv ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) a [reactivate ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) jsou funkce, které lze volat v experimentu pro skrytí a obnovení experimentu v rámci zobrazení v uživatelském rozhraní nebo ve výchozím nastavení ve volání na seznam experimentů. Pokud se vytvoří nový experiment se stejným názvem jako archivovaný experiment, můžete při opětovné aktivaci přejmenovat archivovaný experiment tak, že předáte nový název. Může existovat pouze jeden aktivní experiment se zadaným názvem. Příklad:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Seznam statických metod [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) při experimentování může pořizovat filtr názvů a filtr element ViewType. Hodnoty element ViewType jsou "ACTIVE_ONLY", "ARCHIVED_ONLY" a "ALL". Příklad:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Podpora použití prostředí pro nasazení modelů a aktualizace služby.
  + **[AzureML-dataunášená](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Atribut show třídy [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) nepodporuje nepovinný argument with_details. Atribut show bude prezentovat pouze koeficient posunu dat a příspěvek na posun dat pro sloupce funkcí.
    + DataDriftDetector funkce [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) změny chování:
      + Vstupní parametr start_time, end_time jsou volitelné místo povinné;
      + Zadání specifických start_time a/nebo end_time pomocí konkrétního run_id ve stejném vyvolání způsobí výjimku chyby, protože se vzájemně vylučují.
      + Po zadání specifických start_time a/nebo end_time se vrátí jenom výsledky plánovaných spuštění.
      + Parametr ' daily_latest_only ' je zastaralý.
    + Podpora načítání výstupů pro posun dat na základě datové sady
  + **[AzureML-vysvětlit-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Přejmenuje balíček AzureML-vysvětlit-model na AzureML-interpretace, takže starý balíček zůstane pro zpětnou kompatibilitu nyní.
    + Opravená chyba AutoML s nezpracovanými vysvětlivkami nastavenými na úlohu klasifikace místo regrese ve výchozím nastavení při stahování z ExplanationClient.
    + Přidání podpory pro [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) , která se má vytvořit přímo pomocí [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[AzureML-kanál – jádro](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Vylepšený výkon pro vytváření velkých kanálů.
  + **[AzureML-vlak – jádro](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Přidala se podpora TensorFlow 2,0 v [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Nadřazený běh již nebude při iteraci instalace úspěšný, protože orchestrace ho již zajímá.
    + Přidání podpory Local-Docker a Local-conda pro experimenty AutoML
    + Přidání místních Docker a podpory místních conda pro experimenty AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nové webové prostředí (Preview) pro Azure Machine Learning pracovní prostory

Karta experiment v [novém portálu pracovního prostoru](https://ml.azure.com) se aktualizovala, takže vědečtí data mohou sledovat experimenty způsobem. Můžete prozkoumat následující funkce:
+ Experimentování metadat pro snadné filtrování a řazení seznamu experimentů
+ Zjednodušené a výkonné stránky s podrobnostmi o experimentech, které umožňují vizualizovat a porovnávat vaše běhy
+ Nový návrh ke spuštění stránek s podrobnostmi pro pochopení a monitorování vašich školicích běhů

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Sada SDK Azure Machine Learning pro Python v 1.0.65

  + **Nové funkce**
    + Přidání prostředí, která jsou s ním uspořádaná Tato prostředí jsou předem nakonfigurovaná s knihovnami pro běžné úkoly strojového učení a byla předem sestavena a ukládána do mezipaměti jako image Docker pro rychlejší provádění. Ve výchozím nastavení se zobrazí v seznamu prostředí v pracovním prostoru s předponou "AzureML".
    + Přidání prostředí, která jsou s ním uspořádaná Tato prostředí jsou předem nakonfigurovaná s knihovnami pro běžné úkoly strojového učení a byla předem sestavena a ukládána do mezipaměti jako image Docker pro rychlejší provádění. Ve výchozím nastavení se zobrazí v seznamu prostředí v [pracovním prostoru](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)s předponou "AzureML".

  + **azureml-train-automl**
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Přidání podpory převodu ONNX pro ADB a HDI

+ **Funkce ve verzi Preview**
  + **azureml-train-automl**
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Podporované BERT a BiLSTM jako text featurizer (jenom ve verzi Preview)
    + Podporované přizpůsobení featurization pro účely sloupce a transformaci parametrů (jenom ve verzi Preview)
    + Podporovaná neupravená vysvětlení, když uživatel během školení povolí vysvětlení modelu (jenom náhled)
    + Přidání Prophet pro prognózování `timeseries` jako kanálu pro vlak (jenom pro náhled)

  + **AzureML-contrib – dataunášená**
    + Balíčky přemístěné z AzureML-contrib-disčárky do AzureML-; balíček `contrib` se v budoucí verzi odebere.

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + Zavedení FeaturizationConfig do AutoMLConfig a AutoMLBaseSettings
    + Zavedení FeaturizationConfig do [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) a AutoMLBaseSettings
      + Přepsat účel sloupce pro Featurization s daným typem sloupce a funkce
      + Přepsat parametry transformátoru
    + Přidání zprávy o zastaralosti pro explain_model () a retrieve_model_explanations ()
    + Přidání Prophet jako kanálu pro vlak (jenom pro náhled)
    + Přidání zprávy o zastaralosti pro explain_model () a retrieve_model_explanations ()
    + Přidání Prophet jako kanálu pro vlak (jenom pro náhled)
    + Byla přidána podpora pro automatické zjišťování cílových prodlevy, velikost okna a maximální horizont. Pokud je jedna z target_lags target_rolling_window_size nebo max_horizon nastavená na auto, použijí se heuristiky k odhadu hodnoty odpovídajícího parametru na základě školicích dat.
    + V případě pevného prognózování v případě, kdy datová sada obsahuje jeden sloupec zrnitosti, je tato zrnitosti číselného typu a mezi vlakem a testovací sadou existuje mezera.
    + Opravili jsme chybovou zprávu o duplicitním indexu ve vzdáleném spuštění při prognózování úloh.
    + Fixní prognóza v případě, kdy datová sada obsahuje jeden sloupec zrnitosti, je tato zrnitosti číselného typu a mezi vlakem a testovací sadou existuje mezera.
    + Opravili jsme chybovou zprávu o duplicitním indexu ve vzdáleném spuštění při prognózování úloh.
    + Přidání guardrail pro kontrolu, zda je datová sada nevyvážená nebo ne. V takovém případě se do konzoly zapíše zpráva guardrail.
  + **azureml-core**
    + Přidání možnosti načíst adresu URL SAS pro model v úložišti prostřednictvím objektu modelu. Ex: model. get_sas_url ()
    + Zavést `run.get_details()['datasets']` k získání datových sad přidružených k odeslanému běhu
    + Přidejte `Dataset.Tabular.from_json_lines_files` API k vytvoření TabularDataset ze souborů JSON řádků. Další informace o těchto tabelárních datech v souborech řádků JSON na TabularDataset najdete v dokumentaci https://aka.ms/azureml-data.
    + Přidání dalších polí velikosti virtuálního počítače (disk s operačním systémem, počet GPU) do funkce supported_vmsizes ()
    + Do funkce list_nodes () byla přidána další pole pro zobrazení spuštění, soukromé a veřejné IP adresy, portu atd.
    + Možnost zadat nové pole během zřizování clusteru – remotelogin_port_public_access, která se dá zapnout nebo vypnout v závislosti na tom, jestli se má port SSH nechat otevřený nebo zavřený v době vytváření clusteru. Pokud ho nezadáte, služba bude port inteligentně otevřít nebo Zavřít v závislosti na tom, jestli nasazujete cluster uvnitř virtuální sítě.
  + **azureml-explain-model**
  + **[AzureML-Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Přidání možnosti načíst adresu URL SAS pro model v úložišti prostřednictvím objektu modelu. Ex: model. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Zaveďte běh. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' DataSets '] pro získání datových sad přidružených k odeslanému běhu
    + Přidejte `Dataset.Tabular`API. [from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) pro vytvoření TabularDataset ze souborů JSON řádků. Další informace o těchto tabelárních datech v souborech řádků JSON na TabularDataset najdete v dokumentaci https://aka.ms/azureml-data.
    + Přidání dalších polí velikosti virtuálního počítače (disk s operačním systémem, počet GPU) do funkce [supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Do funkce [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) byla přidána další pole pro zobrazení spuštění, soukromé a veřejné IP adresy, portu atd.
    + Možnost zadat nové pole během [zřizování](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) clusteru `--remotelogin_port_public_access`, která se dá nastavit na povoleno nebo zakázat v závislosti na tom, jestli se má port SSH nechat otevřený nebo zavřený v době vytváření clusteru. Pokud ho nezadáte, služba bude port inteligentně otevřít nebo Zavřít v závislosti na tom, jestli nasazujete cluster uvnitř virtuální sítě.
  + **[AzureML-vysvětlit-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Vylepšená dokumentace pro vysvětlení výstupů ve scénáři klasifikace.
    + Byla přidána možnost Odeslat předpovězené hodnoty y na vysvětlení pro příklady vyhodnocení. Odemkne užitečnější vizualizace.
    + Přidala se vlastnost vysvětlujícího nástroje do MimicWrapper, která umožňuje získat základní MimicExplainer.
  + **azureml-pipeline-core**
    + Přidání poznámkového bloku pro popis modulu, ModuleVersion a ModuleStep
  + **azureml-pipeline-steps**
    + Přidání RScriptStep pro podporu skriptu jazyka R spouštěného prostřednictvím kanálu AML.
    + Pevné parametry metadat pro analýzu v AzureBatchStep, které způsobily chybovou zprávu "přiřazení pro parametr SubscriptionId není zadáno."
  + **azureml-train-automl**
    + Podporované training_data, validation_data, label_column_name weight_column_name jako formát vstupu dat
    + Přidání zprávy o zastaralosti pro explain_model () a retrieve_model_explanations ()
  + **[AzureML-kanál – jádro](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Přidal se [Poznámkový blok](https://aka.ms/pl-modulestep) popisující [modul](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) a [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[AzureML-Pipeline – kroky](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Přidání [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) pro podporu skriptu jazyka R spouštěného prostřednictvím kanálu AML.
    + Opravili jsme parametry metadat při analýze v [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) , což způsobilo, že přiřazení pro parametr SubscriptionId není zadané.
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Podporované training_data, validation_data, label_column_name weight_column_name jako formát vstupu dat.
    + Přidání zprávy o zastaralosti pro [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) a [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Sada SDK Azure Machine Learning pro Python v 1.0.62

+ **Nové funkce**
  + Do TabularDataset se zavedla vlastnost `timeseries`. Tento vlastnost umožňuje snadné filtrování časových razítek u dat a TabularDataset, jako je například pořízení všech dat mezi časovým obdobím nebo nejnovějšími daty. Další informace o tomto `timeseries` vlastností na TabularDataset najdete v dokumentaci k ukázkovému poznámkovém bloku https://aka.ms/azureml-data pro dokumentaci nebo https://aka.ms/azureml-tsd-notebook.
  + Bylo povoleno školení s TabularDataset a datovou sadou. Příklad poznámkového bloku najdete https://aka.ms/dataset-tutorial.

  + **azureml-train-core**
    + Přidání podpory `Nccl` a `Gloo` v PyTorch Estimator

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + Nastavení AutoML ' lag_length ' a LaggingTransformer se zastaralo.
    + Oprava správného ověření vstupních dat, jsou-li zadána ve formátu toku dat
    + Upravení fit_pipeline. py pro generování JSON grafu a nahrání do artefaktů.
    + Vykresluje se graf pod `userrun` pomocí `Cytoscape`.
  + **azureml-core**
    + Přečtěte si přečtěte si zpracování výjimek v kódu ADB a udělejte změny jako při zpracování nových chyb.
    + Bylo přidáno automatické ověřování MSI pro virtuální počítače poznámkových bloků.
    + Opravuje chybu, kde by mohly být nahrány poškozené nebo prázdné modely z důvodu neúspěšných pokusů o selhání.
    + Byla opravena chyba, kdy se `DataReference` název změní při změně režimu `DataReference` (například při volání `as_upload`, `as_download`nebo `as_mount`).
    + Nastavit `mount_point` a `target_path` volitelné pro `FileDataset.mount` a `FileDataset.download`.
    + Výjimka, že sloupec časového razítka nejde najít, se zavolá, pokud se rozhraní API s časovými sériovými metodami volá bez dodaného sloupce časového razítka, nebo jsou vyřazené sloupce přiřazených časových razítek
    + Sloupce s časovými intervaly by měly být přiřazeny sloupci, jehož typ je datum, jinak se očekává výjimka.
    + Sloupce s časovými intervaly přiřazení rozhraní API with_timestamp_columns můžou mít neomezenou hodnotu None nebo hrubě vyhodnotit název sloupce časového razítka. tím se vymažou dříve přiřazené sloupce časových razítek.
    + Výjimka bude vyvolána, pokud je sloupec hrubých zrn nebo jemného sloupce časového razítka vyřazený s označením pro uživatele, který vyřadíte, a to po vyřazení seznamu nebo volání with_time_stamp s hodnotou None do časového razítka vydání verze. sloupcích
    + Výjimka se vygeneruje, když se ve sloupci zachovat seznam sloupců s označením pro uživatele, který zadáte jako časový razítko, v seznamu ponechat seznam sloupců, a v with_time_stamp volání bez hodnota pro sloupce časového razítka verze
    + Bylo přidáno protokolování pro velikost registrovaného modelu.
  + **azureml-explain-model**
    + Pevné upozornění vytištěné na konzolu, když není nainstalované balíček Pythonu pro sbalení: "použití starší verze než podporované verze lightgbm, upgrade na verzi větší než 2.2.1"
    + Základní vysvětlení modelu stažení s horizontálního dělení pro globální vysvětlení s mnoha funkcemi
    + Vysvětlené nevyřešené příklady inicializace pro vysvětlení výstupu
    + Pevná neproměnlivá Chyba při nastavování vlastností při nahrávání s vysvětlením klienta pomocí dvou různých typů modelů
    + Do bodování bodování byl přidán get_raw param. vysvětlit (), takže jeden vysvětlující bodování může vracet jak provedenou, tak nezpracované hodnoty.
  + **azureml-train-automl**
    + Zavedli jsme veřejná rozhraní API z AutoML pro podporu vysvětlení z `automl` vysvětlit sadu SDK – novější způsob podpory vysvětlení AutoML pomocí odí AutoML featurization a vysvětlení nezpracovaného vysvětlení v integrované sadě SDK z pro modely AutoML.
    + Odebírají se výchozí hodnoty ze vzdálených školicích prostředí.
    + Umístění úložiště výchozí mezipaměti bylo změněno z FileCacheStore na jednu až AzureFileCacheStore jedno pro AutoML v Azure Databricks cestě kódu.
    + Oprava správného ověření vstupních dat, jsou-li zadána ve formátu toku dat
  + **azureml-train-core**
    + Vrácení source_directory_data_store se zrušilo.
    + Byla přidána možnost přepsat verze balíčku po instalaci.
    + Do parametru `environment_definition` v odhady byla přidána podpora souboru Dockerfile.
    + Zjednodušené parametry distribuovaného školení v odhady.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nové webové prostředí (Preview) pro Azure Machine Learning pracovní prostory
Nové webové prostředí umožňují datovým specialistům a datovým technikům dokončit celý životní cyklus strojového učení z připravujete a vizualizovat data pro školení a nasazení modelů v jednom umístění.

![Uživatelské rozhraní pro Azure Machine Learning pracovní prostor (Preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Klíčové funkce:**

Pomocí tohoto nového Azure Machine Learning rozhraní teď můžete:
+ Správa vašich poznámkových bloků nebo odkazování na Jupyter
+ [Spouštění automatizovaných experimentů ML](tutorial-first-experiment-automated-ml.md)
+ [Vytváření datových sad z místních souborů, úložiště dat, & webových souborů](how-to-create-register-datasets.md)
+ Prozkoumejte & příprava datových sad pro vytváření modelů
+ Sledování posunu dat pro vaše modely
+ Zobrazit poslední prostředky z řídicího panelu

V době této verze se podporují následující prohlížeče: Chrome, Firefox, Safari a Microsoft Edge Preview.

**Známé problémy:**

1. Pokud se zobrazí zpráva něco se pokazilo, aktualizujte si prohlížeč. Při načítání souborů bloků dat došlo k chybě, když probíhá nasazení.

1. Nelze odstranit ani přejmenovat soubor v poznámkových blocích a souborech. Během Public Preview můžete k provádění operací s aktualizacemi souboru použít uživatelské rozhraní Jupyter nebo terminál na virtuálním počítači poznámkového bloku. Vzhledem k tomu, že se jedná o připojený souborový systém souborů, všechny změny provedené na virtuálním počítači poznámkového bloku se v pracovním prostoru poznámkového bloku hned

1. Do virtuálního počítače poznámkového bloku přes SSH:
   1. Vyhledejte klíče SSH, které byly vytvořeny při instalaci virtuálního počítače. Nebo Najděte klíče v pracovním prostoru Azure Machine Learning > Otevřete kartu COMPUTE > v seznamu vyberte virtuální počítač Poznámkový blok, > otevřete jeho vlastnosti: Zkopírujte klíče z dialogového okna.
   1. Importujte tyto veřejné a soukromé klíče SSH do svého místního počítače.
   1. Použijte je pro SSH do virtuálního počítače poznámkového bloku.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Sada SDK Azure Machine Learning pro Python v 1.0.60

+ **Nové funkce**
  + Byl představen objekt DataSet, který odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Soubory mohou být libovolného formátu. Soubor DataSet poskytuje možnost stahovat soubory nebo je připojit k výpočetnímu prostředí. Pokud se chcete dozvědět o datové sadě, navštivte prosím https://aka.ms/file-dataset.
  + Přidání podpory kanálu YAML pro krok PythonScript, krok adla, krok datacihly, DataTransferStep a AzureBatch

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + AutoArima je teď navržený kanál jenom pro náhled.
    + Vylepšené zasílání zpráv o chybách pro prognózování.
    + Vylepšené protokolování pomocí vlastních výjimek namísto obecného v úlohách prognózy.
    + Odebrání kontroly max_concurrent_iterations bylo zrušeno, aby bylo menší než celkový počet iterací.
    + Modely AutoML nyní vracejí AutoMLExceptions
    + Tato verze vylepšuje výkon spuštění automatizovaného místního spuštění machine learningu.
  + **azureml-core**
    + Zaveďte datovou sadu. get_all (pracovní prostor), která vrátí slovník `TabularDataset` a `FileDataset` objektů pomocí názvu registrace.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Do `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_parquet.files`zaveďte `parition_format` jako argument. Informace o oddílu každé cesty k datům budou extrahovány do sloupců v závislosti na zadaném formátu. {column_name} vytvoří sloupec řetězců a {column_name: RRRR/MM/DD/HH/MM/SS} vytvoří sloupec data a času, kde ' yyyy ', ' MM ', ' DD ', ' HH ', ' mm ' a ' ss ' slouží k extrakci roku, měsíce, dne, hodin, minut a sekund pro typ DateTime. Partition_format by měl začít od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu. /USA/2019/01/01/data.csv, kde je oddíl podle země a času, partition_format = '/{Country}/{PartitionDate: RRRR/MM/DD}/data. csv ' vytvoří řetězcový sloupec ' Country ' s hodnotou ' USA ' a sloupec DateTime ' PartitionDate ' s hodnotou ' 2019-01-01 '.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Do `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_parquet.files`zaveďte `partition_format` jako argument. Informace o oddílu každé cesty k datům budou extrahovány do sloupců v závislosti na zadaném formátu. {column_name} vytvoří sloupec řetězců a {column_name: RRRR/MM/DD/HH/MM/SS} vytvoří sloupec data a času, kde ' yyyy ', ' MM ', ' DD ', ' HH ', ' mm ' a ' ss ' slouží k extrakci roku, měsíce, dne, hodin, minut a sekund pro typ DateTime. Partition_format by měl začít od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu. /USA/2019/01/01/data.csv, kde je oddíl podle země a času, partition_format = '/{Country}/{PartitionDate: RRRR/MM/DD}/data. csv ' vytvoří řetězcový sloupec ' Country ' s hodnotou ' USA ' a sloupec DateTime ' PartitionDate ' s hodnotou ' 2019-01-01 '.
    + do `TabularDataset`byly přidány metody `to_csv_files` a `to_parquet_files`. Tyto metody umožňují převod mezi `TabularDataset` a `FileDataset` převodem dat do souborů v zadaném formátu.
    + Při ukládání souboru Dockerfile generovaného modelem. Package () se automaticky přihlaste k registru základní image.
    + gpu_support už není potřeba; AML nyní automaticky detekuje a používá rozšíření Docker, když je k dispozici. V budoucí verzi se odebere.
    + Přidání podpory pro vytváření, aktualizaci a používání PipelineDrafts.
    + Tato verze vylepšuje výkon spuštění automatizovaného místního spuštění machine learningu.
    + Uživatelé můžou zadávat dotazy na metriky z historie spuštění podle názvu.
    + Vylepšené protokolování pomocí vlastních výjimek namísto obecného v úlohách prognózy.
  + **azureml-explain-model**
    + Přidání parametru feature_maps do nového MimicWrapper a umožnění uživatelům získat nezpracované Vysvětlení funkcí.
    + Nahrávání datových sad je teď ve výchozím nastavení vypnuté pro nahrání vysvětlení a dá se znovu povolit s upload_datasets = true.
    + Přidání parametrů filtrování "is_law" do seznamu vysvětlení a funkce stažení.
    + Přidá metodu `get_raw_explanation(feature_maps)` do globálních i lokálních objektů vysvětlení.
    + Přidání kontroly verze do lightgbm s vytištěným upozorněním, pokud je nižší než podporovaná verze
    + Optimalizované využití paměti při dávkování vysvětlení
    + Modely AutoML nyní vracejí AutoMLExceptions
  + **azureml-pipeline-core**
    + Přidání podpory pro vytváření, aktualizaci a používání PipelineDrafts-lze použít k údržbě proměnlivých definic kanálu a jejich interaktivnímu používání.
  + **azureml-train-automl**
    + Vytvořená funkce pro instalaci konkrétních verzí pytorch v 1.1.0 s grafickým procesorem, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-transformes, které jsou nutné k povolení BERT/XLNet ve vzdáleném prostředí Python runtime.
  + **azureml-train-core**
    + Prvotní selhání některých chyb definice prostoru s parametry přímo v sadě SDK namísto na straně serveru.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Sada Azure Machine Learning data PREP SDK v 1.1.14
+ **Opravy chyb a vylepšení**
  + Umožnění psaní do ADLS/ADLSGen2 s použitím nezpracovaných cest a přihlašovacích údajů.
  + Opravili jsme chybu, která způsobila, že `include_path=True` nefungují pro `read_parquet`.
  + Opravená `to_pandas_dataframe()` selhání způsobená výjimkou: Neplatná hodnota vlastnosti: hostSecret.
  + Opravili jsme chybu, kdy se soubory v DBFS v režimu Spark nepodařilo přečíst.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Sada SDK Azure Machine Learning pro Python v 1.0.57
+ **Nové funkce**
  + Povolené `TabularDataset`, které má AutomatedML spotřebovat. Pokud se chcete dozvědět víc o `TabularDataset`, navštivte prosím https://aka.ms/azureml/howto/createdatasets.

+ **Opravy chyb a vylepšení**
  + **automl-Client-Core-nativeclient**
    + Opravili jsme chybu, která se vyvolala při výuce a/nebo ověřovací popisky (y a y_valid), a to ve formě PANDAS dataframe, ale ne jako numpy Array.
    + Bylo aktualizováno rozhraní, aby se vytvořilo `RawDataContext`, aby vyžadovalo pouze data a objekt `AutoMLBaseSettings`.
    +  Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování. – Povolí uživatelům AutoML odstranit zrna ze sady testů, která neexistuje v školicí sadě při prognózování.
  + **azure-cli-ml**
    + Certifikát SSL pro vyhodnocování koncového bodu, který je nasazený v clusteru AKS, teď můžete aktualizovat pro Microsoft vygenerovaný i certifikát zákazníka.
  + **AzureML-automl – jádro**
    + Opravili jsme problém v AutoML, kde se řádky s chybějícími popisky neodebraly správně.
    + Vylepšené protokolování chyb v AutoML; všechny chybové zprávy se teď vždycky zapisují do souboru protokolu.
    + AutoML aktualizovala své připnutí balíčku tak, aby zahrnovala `azureml-defaults`, `azureml-explain-model`a `azureml-dataprep`. AutoML už nebude upozorňovat na neshody balíčku (s výjimkou `azureml-train-automl` balíčku).
    + Opravili jsme problém v `timeseries`, kde se CV oddělí nestejnou velikostí, což způsobuje selhání výpočtu bin.
    + Pokud se při spuštění iterace kompletu pro školicí typ pro křížové ověření objevilo potíže při stahování modelů vyškolených na celou datovou sadu, máme nekonzistenci mezi závažím modelu a modely, které byly dodány do hlasování. stromů.
    + Opravili jsme chybu, která se vyvolala při výuce a/nebo ověřovací popisky (y a y_valid), a to ve formě PANDAS dataframe, ale ne jako numpy Array.
    + Opravili jsme problém s prognózou úkolů v případě, že v logických sloupcích vstupních tabulek nebyl žádný nalezen.
    + Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování. – Povolí uživatelům AutoML odstranit zrna ze sady testů, která neexistuje v školicí sadě při prognózování.
  + **azureml-core**
    + Opravili jsme problém s řazením parametrů blob_cache_timeout.
    + Do systémových chyb byly přidány typy výjimek, které se vejdou a transformují.
    + Přidání podpory pro Key Vault tajných kódů pro vzdálené běhy Přidejte do trezoru klíčů přidruženého k vašemu pracovnímu prostoru třídu AzureML. Core. trezor. Podporované operace:
      + AzureML. Core. Workspace. Workspace. get_default_keyvault ()
      + AzureML. Core. trezor. set_secret (název, hodnota)
      + AzureML. Core. klíčů trezor. trezor. set_secrets (secrets_dict)
      + AzureML. Core. klíčů trezor. trezor. get_secret (název)
      + AzureML. Core. klíčů trezor. trezor. get_secrets (secrets_list)
      + AzureML. Core. klíčů trezor. list_secrets ()
    + Další metody získání výchozího trezoru klíčů a získání tajných kódů při vzdáleném spuštění:
      + AzureML. Core. Workspace. Workspace. get_default_keyvault ()
      + AzureML. Core. Run. Run. get_secret (název)
      + AzureML. Core. Run. Run. get_secrets (secrets_list)
    + Přidání dalších parametrů přepsání do příkazu příkazového řádku pro odeslání – HyperDrive
    + Zlepšení spolehlivosti volání rozhraní API při opakovaných pokusech o výjimky knihovny pro běžné požadavky.
    + Přidání podpory pro odeslání spuštění z odeslaného běhu.
    + Opravili jsme problém s tokenem SAS s vypršenou platností ve sledovacím procesu, což způsobilo, že se soubory zastavily po vypršení platnosti počátečního tokenu
    + Podporuje se import souborů HTTP CSV/TSV v sadě DataSet Python SDK.
    + Zastaralá metoda pracovního prostoru. Setup (). Zpráva s upozorněním, která se zobrazuje uživatelům, navrhuje místo toho použití Create () nebo Get ()/from_config ().
    + Bylo přidáno prostředí. add_private_pip_wheel (), které umožňuje nahrávání privátních vlastních balíčků Python `whl`do pracovního prostoru a jejich bezpečné použití pro sestavení nebo vyhodnotit prostředí.
    + Certifikát SSL pro vyhodnocování koncového bodu, který je nasazený v clusteru AKS, teď můžete aktualizovat pro Microsoft vygenerovaný i certifikát zákazníka.
  + **azureml-explain-model**
    + Přidání parametru pro přidání ID modelu do vysvětlení při nahrávání.
    + Přidání značek `is_raw` k vysvětlení v paměti a nahrání.
    + Přidala se podpora a testy pytorch pro balíček pro AzureML-vysvětlující-model.
  + **azureml-opendatasets**
    + Podporuje detekci a protokolování automatického testovacího prostředí.
    + Přidali jsme třídy, které nám pomohou načíst populace podle země a PSČ.
  + **azureml-pipeline-core**
    + Přidala se vlastnost Label na vstupní a výstupní definice portů.
  + **AzureML-telemetrie**
    + Opravila se nesprávná konfigurace telemetrie.
  + **azureml-train-automl**
    + Opravili jsme chybu, když při instalaci došlo k chybě, při chybě se nepovedlo přihlásit pole chyby pro spuštění instalačního programu, takže se neuložilo v nadřazeném spuštění "chyby".
    + Opravili jsme problém v AutoML, kde se řádky s chybějícími popisky neodebraly správně.
    + Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování.
    + Povolí uživatelům AutoML odstranit zrna ze sady testů, které neexistují v školicí sadě při prognózování.
    + Nyní AutoMLStep projde do back-endu `automl` config, aby se předešlo jakýmkoli problémům při změnách nebo přidávání nových parametrů konfigurace.
    + AutoML data Guardrail je teď ve verzi Public Preview. Po školení se uživateli zobrazí sestava Guardrail dat (pro úlohy klasifikace/regrese) a také k ní bude mít přístup prostřednictvím rozhraní SDK API.
  + **azureml-train-core**
    + Přidala se podpora Torch 1,2 v PyTorch Estimator.
  + **AzureML – widgety**
    + Vylepšené maticové grafy pro školení klasifikace.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Sada Azure Machine Learning data PREP SDK v 1.1.12
+ **Nové funkce**
  + Seznamy řetězců se teď dají předat jako vstup `read_*` metodám.

+ **Opravy chyb a vylepšení**
  + Výkon `read_parquet` byl významně vylepšen při spuštění ve Sparku.
  + Opravili jsme problém, kdy se `column_type_builder` v případě jednoho sloupce s nejednoznačnými formáty data nezdařila.

### <a name="azure-portal"></a>Portál Azure
+ **Funkce Preview**
  + Streamování protokolů a výstupních souborů je teď k dispozici na stránkách s podrobnostmi o spuštění. Když je zapnutý přepínač Preview, soubory se budou v reálném čase aktualizovat.
  + Možnost nastavit kvótu na úrovni pracovního prostoru je vydaná ve verzi Preview. Kvóty AmlCompute se přidělují na úrovni předplatného, ale teď vám umožňuje distribuovat tuto kvótu mezi pracovními prostory a přidělit je pro spravedlivé sdílení a zásady správného řízení. Stačí kliknout na okno **používání a kvóty** v levém navigačním panelu pracovního prostoru a vybrat kartu **Konfigurace kvót** . Nezapomeňte, že musíte být správcem předplatného, abyste mohli nastavit kvóty na úrovni pracovního prostoru, protože se jedná o operaci mezi pracovními prostory.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Sada SDK Azure Machine Learning pro Python v 1.0.55

+ **Nové funkce**
  + Ověřování na základě tokenu se teď podporuje pro volání na koncový bod nasazené na AKS. Budeme dál podporovat aktuální ověřování založené na klíčích a uživatelé můžou používat jeden z těchto ověřovacích mechanismů současně.
  + Možnost Registrovat úložiště objektů blob, které je za virtuální sítí (VNet) jako úložiště dat.

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + Opravuje chybu, při které je velikost ověřování pro rozdělení CV malá a výsledkem je špatný odhad vs. opravdové grafy pro regresi a předpovědi.
    + V případě, že došlo k chybě při spuštění, je k dispozici protokolování úloh prognózování na vzdálené běhy. nyní se uživateli zobrazí komplexní chybová zpráva.
    + Opravené chyby `Timeseries`, pokud je příznak předběžného zpracování true.
    + Provedli jsme některé chybové zprávy při ověřování dat předpovědi, které jsou nenapadnutelné.
    + Snížená spotřeba paměti AutoML se spouští vyřazením a/nebo opožděným načítáním datových sad, zejména mezi vytvářením procesů.
  + **AzureML-contrib-vysvětlený model**
    + Přidání příznaku model_task pro vysvětlení, aby uživatel mohl přepsat výchozí automatickou logiku odvození pro typ modelu
    + Změny widgetů: automaticky se nainstalují pomocí `contrib`, žádné další `nbextension` vysvětlení instalace/povolení-podpora jenom s důležitou funkcí důležitost (například Permutative).
    + Změny řídicího panelu:-box vykresluje a Violin se kromě `beeswarm`ho vykreslení na stránce souhrnu – mnohem rychlejší převykreslování `beeswarm` grafu na stránce se změnou horního okraje – užitečná zpráva s vysvětlením, jak se počítá shora k vypočítaným chybovým zprávám, když se data neposkytují
  + **azureml-core**
    + Přidání metody model. Package () pro vytváření imagí Docker a fázemi, které zapouzdřují modely a jejich závislosti.
    + Místní služby WebService se aktualizovaly, aby přijímaly InferenceConfigs obsahující objekty prostředí.
    + Pevný model. Register () vytvářející neplatné modely, pokud '. ' (pro aktuální adresář) se předává jako parametr model_path.
    + Přidat rutinu run. submit_child, funkce, které experimentují experimenty. odeslat při určení spuštění jako nadřazeného objektu odeslaného podřízeného spuštění.
    + Podpora možností konfigurace z modelu. Zaregistrujte se v běhu. register_model.
    + Možnost spouštět v existujícím clusteru úlohy JAR.
    + Nyní podporuje parametry instance_pool_id a cluster_log_dbfs_path.
    + Přidání podpory pro použití objektu prostředí při nasazování modelu na webovou službu. Objekt prostředí se teď dá zadat jako součást objektu InferenceConfig.
    + Přidání mapování appinsifht pro nové oblasti – centralus-westus-northcentralus
    + Přidali jsme dokumentaci pro všechny atributy ve všech třídách úložiště dat.
    + Přidání parametru blob_cache_timeout do `Datastore.register_azure_blob_container`
    + Do AzureML. Core. Environment. Environment se přidaly metody save_to_directory a load_from_directory.
    + Do CLI se přidaly příkazy AZ ml Environment download a AZ ml Environment Register.
    + Přidala se metoda prostředí. add_private_pip_wheel.
  + **azureml-explain-model**
    + Přidání sledování datové sady k vysvětlení pomocí služby DataSet (Preview).
    + Při streamování globálních vysvětlení z 10 000 na 100 se snížila výchozí velikost dávky.
    + Přidání příznaku model_task pro vysvětlení, aby uživatel mohl přepsat výchozí automatickou logiku odvození pro typ modelu.
  + **azureml-mlflow**
    + Opravená chyba v mlflow. AzureML. build_image, kde jsou vnořené adresáře ignorovány.
  + **azureml-pipeline-steps**
    + Přidání možnosti spouštět úlohy JAR v existujícím clusteru Azure Databricks.
    + Přidání podpory instance_pool_id a parametrů cluster_log_dbfs_path pro krok DatabricksStep
    + Přidání podpory pro parametry kanálu v kroku DatabricksStep
  + **azureml-train-automl**
    + Přidáno `docstrings` souborů souvisejících s kompletem.
    + Aktualizace dokumentů na vhodnější jazyk pro `max_cores_per_iteration` a `max_concurrent_iterations`
    + V případě, že došlo k chybě při spuštění, je k dispozici protokolování úloh prognózování na vzdálené běhy. nyní se uživateli zobrazí komplexní chybová zpráva.
    + Z kanálu `automlstep` poznámkového bloku se odebraly get_data.
    + Začalo podpora `dataprep` v `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Sada Azure Machine Learning data PREP SDK v 1.1.10

+ **Nové funkce**
  + Nyní můžete požádat o spuštění konkrétních kontrolorů (například histogram, bodový graf atd.) pro konkrétní sloupce.
  + Do `append_columns`byl přidán argument paralelizovat. Pokud má hodnotu true, data se načtou do paměti, ale spuštění se spustí paralelně. Pokud je hodnota false, bude spuštění streamování, ale s jedním vláknem.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Sada SDK Azure Machine Learning pro Python v 1.0.53

+ **Nové funkce**
  + Automatizované Machine Learning nyní podporuje školicí modely ONNX na vzdáleném cíli výpočtů.
  + Azure Machine Learning teď nabízí možnost pokračovat v školení z předchozích souborů běhu, kontrolního bodu nebo souborů modelu.
    + Naučte se [používat odhady k obnovení školení z předchozího běhu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) .

+ **Opravy chyb a vylepšení**
  + **automl-Client-Core-nativeclient**
    + Opravte chybu týkající se typů ztrátě sloupců po transformaci (propojená chyba);
    + Povolí y_query jako typ objektu, který neobsahuje žádné (s) na začátku (#459519).
  + **azure-cli-ml**
    + Příkazy rozhraní příkazového řádku "nasadit model" a "aktualizace služby" nyní přijímají parametry, konfigurační soubory nebo kombinaci těchto dvou. Parametry mají přednost před atributy v souborech.
    + Popis modelu se teď dá aktualizovat po registraci.
  + **AzureML-automl – jádro**
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + Přidání podpory pro kanály NimbusML odhady & pro použití v rámci AutoML odhady.
    + Oprava chyby v postupu výběru kompletu, která nemusela zbytečně rozšiřovat výsledný komplet, i když skóre zůstávala konstantní.
    + Povolí opětovné použití některých featurizations u rozdělení CV pro úlohy předpovědi. Tím se zrychlí spuštění instalačního programu zhruba n_cross_validations pro nákladné featurizations, jako je prodlevy a valení okna.
    + Řešení problému, pokud je čas mimo PANDAS podporovaný časový rozsah. V případě, že je čas menší než PD, teď vyvoláme výjimku. Timestamp. min nebo větší než PD Timestamp. max
    + Prognózování teď umožňuje různé frekvence v vlakových a testovacích sadách, pokud je jde zarovnávat. Například "čtvrtletní zahájení v lednu" a "čtvrtletní začátek v říjnu" lze zarovnávat.
    + Do TimeSeriesTransformer se přidala vlastnost Parameters.
    + Odeberte staré třídy výjimek.
    + V úkolech prognózování parametr `target_lags` nyní přijímá jednu celočíselnou hodnotu nebo seznam celých čísel. Pokud bylo zadáno celé číslo, bude vytvořena pouze jedna prodleva. Pokud je k dispozici seznam, budou provedeny jedinečné hodnoty prodlevy. target_lags = [1, 2, 2, 4] vytvoří prodlevy jednu, 2 a 4 tečky.
    + Opravte chybu týkající se ztráty typů sloupců po transformaci (propojená chyba);
    + V `model.forecast(X, y_query)`povolte, aby y_query jako typ objektu, který neobsahuje žádné (s) na začátku (#459519).
    + Přidání očekávaných hodnot do výstupu `automl`
  + **AzureML-contrib – dataunášená**
    +  Vylepšení ukázkového poznámkového bloku, včetně přepnutí do programu AzureML-opendatasets namísto funkcí AzureML-contrib-opendatasets a zlepšení výkonu při rozšiřování dat
  + **AzureML-contrib-vysvětlený model**
    + Pevné transformace argumentu pro nástroj pro vysvětlení VÁPNa pro nezpracovaný význam funkcí v balíčku AzureML-contrib-vysvětlit-model
    + Přidání segmentů k vysvětlení obrázků v popisu obrázku pro balíček AzureML-contrib-vysvětlit-model
    + Přidání podpory zhuštění scipy pro LimeExplainer
    + Přidání `batch_size` pro napodobování vysvětlení při `include_local=False`pro streamování globálních vysvětlení v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel
  + **AzureML-contrib-featureengineering**
    + Oprava pro volání set_featurizer_timeseries_params (): Změna typu hodnoty dict – a prázdná check – přidat Poznámkový blok pro `timeseries` featurizer
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
  + **azureml-core**
    + Přidání možnosti připojit úložiště DBFS v rozhraní příkazového řádku AzureML
    + Opravili jsme chybu pomocí nahrávání úložiště dat, kde se vytvoří prázdná složka, pokud `target_path` spuštěná s `/`
    + Opravený `deepcopy` problém v ServicePrincipalAuthentication.
    + Do CLI se přidaly příkazy AZ ml Environment show a AZ ml Environment list.
    + Prostředí teď podporují určení base_dockerfile jako alternativu již sestaveného base_image.
    + Nepoužívané auto_prepare_environment nastavení RunConfiguration bylo označeno jako zastaralé.
    + Popis modelu se teď dá aktualizovat po registraci.
    + Opravu chyb: model a image Delete teď poskytují další informace o načítání nadřazených objektů, které jsou na nich závislé, pokud odstranění selhalo z důvodu nadřazené závislosti.
    + Opravili jsme chybu, která vytiskla prázdné trvání pro nasazení, ke kterým dochází při vytváření pracovního prostoru pro některá prostředí.
    + Vylepšené výjimky při vytváření pracovních prostorů Tato možnost se uživatelům nezobrazí "nelze vytvořit pracovní prostor". Nejde najít... jako zpráva a místo toho se zobrazí skutečná Chyba při vytváření.
    + Přidání podpory pro ověřování tokenu v AKS WebServices.
    + Přidejte `get_token()` metodu pro `Webservice` objektů.
    + Přidala se podpora rozhraní příkazového řádku pro správu datových sad Machine Learning.
    + `Datastore.register_azure_blob_container` nyní převezme hodnotu `blob_cache_timeout` (v sekundách), která nakonfiguruje parametry připojení blobfuse pro povolení vypršení platnosti mezipaměti pro toto úložiště dat. Výchozí hodnota není žádný časový limit, tj. když je objekt BLOB načtený, zůstane v místní mezipaměti, dokud není úloha dokončena. Většina úloh bude preferovat toto nastavení, ale některé úlohy potřebují číst více dat z velké datové sady, než se vejde do jejich uzlů. Pro tyto úlohy ladění tohoto parametru pomůže úspěšně. Při vyladění tohoto parametru se ujistěte, že nastavení příliš nízké hodnoty může mít za následek špatný výkon, protože data používaná v epocha můžou vypršet, než se znovu použijí. To znamená, že všechny čtení se budou provádět ze služby Blob Storage (tj. sítě) místo z místní mezipaměti, což negativně ovlivní dobu školení.
    + Popis modelu se teď dá po registraci správně aktualizovat.
    + Odstranění modelu a obrázku nyní poskytuje další informace o nadřazených objektech, které jsou na nich závislé, což způsobí selhání odstranění.
    + Vylepšete využití prostředků ve vzdálených spuštěních pomocí AzureML. mlflow.
  + **azureml-explain-model**
    + Pevné transformace argumentu pro nástroj pro vysvětlení VÁPNa pro nezpracovaný význam funkcí v balíčku AzureML-contrib-vysvětlit-model
    + Přidání podpory zhuštění scipy pro LimeExplainer
    + přidaný tvar lineárního vysvětlujícího pobalení a také další úroveň pro tabulkový popis vysvětlující lineární modely
    + pro napodobení napodobení v knihovně modelů Vysvětlete chybu, když include_local = false pro zhuštěné datové vstupy
    + Přidání očekávaných hodnot do výstupu `automl`
    + pevná funkce permutace důležitost při zadání argumentu Transformers pro získání důležité funkce
    + Přidání `batch_size` pro napodobování vysvětlení při `include_local=False`pro streamování globálních vysvětlení v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel
    + v případě knihovny pro vysvětlení modelu jsou k disblackbox vysvětlující, kde je pro předpověď vyžadován vstup PANDAS dataframe.
    + Opravili jsme chybu, kdy `explanation.expected_values` někdy vracet místo seznamu s plovoucí desetinnou čárkou.
  + **azureml-mlflow**
    + Zvýšení výkonu mlflow. set_experiment (experiment_name)
    + Oprava chyby při použití InteractiveLoginAuthentication pro mlflow tracking_uri
    + Vylepšete využití prostředků ve vzdálených spuštěních pomocí AzureML. mlflow.
    + Zlepšení dokumentace k balíčku AzureML-mlflow
    + Oprava chyby, kde mlflow. log_artifacts ("my_dir") by ukládala artefakty v rámci my_dir/< artefakt-Paths > namísto "< artefakt-Paths >"
  + **azureml-opendatasets**
    + Připnout `pyarrow` z `opendatasets` na staré verze (< 0.14.0) z důvodu chyby nově zavedené paměti.
    + Přesuňte AzureML-contrib-opendatasets do AzureML-opendatasets.
    + Povolí registraci tříd otevřených datových sad do pracovního prostoru Azure Machine Learning a bezproblémové využití AML datových sad funkcí.
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.
  + **azureml-pipeline-steps**
    + Úložiště dat DBFS se teď podporuje pro vstupy a výstupy v DatabricksStep.
    + Aktualizovaná dokumentace pro Azure Batch krok s ohledem na vstupy a výstupy.
    + V AzureBatchStep se změnila *delete_batch_job_after_finish* výchozí hodnota na *true (pravda*).
  + **AzureML-telemetrie**
    +  Přesuňte AzureML-contrib-opendatasets do AzureML-opendatasets.
    + Povolí registraci tříd otevřených datových sad do pracovního prostoru Azure Machine Learning a bezproblémové využití AML datových sad funkcí.
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.
  + **azureml-train-automl**
    + Aktualizovala se dokumentace na get_output, aby odrážela skutečný návratový typ a poskytovala další poznámky k načítání vlastností klíče.
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + Přidání očekávaných hodnot do výstupu `automl`
  + **azureml-train-core**
    + Řetězce se teď akceptují jako cíl COMPUTE pro automatizované ladění parametrů.
    + Nepoužívané auto_prepare_environment nastavení RunConfiguration bylo označeno jako zastaralé.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Sada Azure Machine Learning data PREP SDK v 1.1.9

+ **Nové funkce**
  + Přidání podpory pro čtení souboru přímo z adresy URL protokolu HTTP nebo HTTPS.

+ **Opravy chyb a vylepšení**
  + Vylepšená chybová zpráva při pokusu o čtení datové sady Parquet ze vzdáleného zdroje (což není aktuálně podporováno).
  + Opravili jsme chybu při zápisu do formátu souboru Parquet v ADLS Gen 2 a aktualizaci názvu kontejneru ADLS Gen 2 v cestě.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuální rozhraní
+ **Funkce ve verzi Preview**
  + Do vizuálního rozhraní se přidal modul spuštění skriptu jazyka R.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Sada SDK Azure Machine Learning pro Python v 1.0.48

+ **Nové funkce**
  + **azureml-opendatasets**
    + příkaz **AzureML-contrib-opendatasets** je nyní k dispozici jako **AzureML-opendatasets**. Starý balíček může i nadále fungovat, ale doporučujeme, abyste pro rozsáhlejší možnosti a vylepšení používali **opendatasets** .
    + Tento nový balíček umožňuje registrovat otevřené datové sady jako datovou sadu v pracovním prostoru Azure Machine Learning a využít jakékoli funkce, které datové sady nabízí.
    + Zahrnuje taky existující funkce, jako je například využívání otevřených datových sad jako PANDAS/SPARK dataframes, a spojení umístění pro určitou datovou sadu, jako je počasí.

+ **Funkce ve verzi Preview**
    + HyperDriveConfig může nyní přijímat objekt kanálu jako parametr pro podporu ladění parametrů pomocí kanálu.

+ **Opravy chyb a vylepšení**
  + **azureml-train-automl**
    + Opravili jsme chybu týkající se ztráty typů sloupců po transformaci.
    + Opravili jsme chybu, aby bylo možné y_query typ objektu, který neobsahuje žádné (y) na začátku.
    + Opravili jsme problém v postupu výběru kompletu, který nemusel nutně rozšiřovat výsledný komplet, i když skóre zůstávala konstantní.
    + Opravili jsme problém s nastavením whitelist_models a blacklist_models v AutoMLStep.
    + Opravili jsme problém, který zabránil použití předběžného zpracování, pokud by se AutoML použil v kontextu kanálů Azure ML.
  + **azureml-opendatasets**
    + Byl přesunut příkaz AzureML-contrib-opendatasets do procesu AzureML-opendatasets.
    + Povolené otevřené třídy datové sady, které se mají registrovat do Azure Machine Learningho pracovního prostoru a využívat funkce AML DataSet bez problémů.
    + Vylepšený výkon NoaaIsdWeather obohacen v jiné verzi než SPARK.
  + **azureml-explain-model**
    + Aktualizovaná online dokumentace pro objekty interpretace.
    + Přidání `batch_size` pro napodobování vysvětlení při `include_local=False`, pro streamování globálních vysvětlení v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel pro knihovnu pro vysvětlení modelu.
    + Opravili jsme problém, kdy `explanation.expected_values` někdy vracet místo seznamu s plovoucí desetinnou čárkou.
    + Přidání očekávaných hodnot do výstupního `automl` pro podrobnější vysvětlení v knihovně modelů vysvětlit.
    + Pevná funkce permutace důležitost při zadání argumentu Transformers, který má za problém získat důležitost nezpracovaných funkcí
  + **azureml-core**
    + Do příkazového řádku AzureML se přidala možnost připojit úložiště DBFS.
    + Opravili jsme problém s nahráváním úložiště dat, kde se vytvoří prázdná složka, pokud `target_path` spuštěná s `/`.
    + Je povolené porovnání dvou datových sad.
    + Odstranění modelu a obrázku nyní poskytuje další informace o načítání nadřazených objektů, které jsou na nich závislé, pokud odstranění selhalo z důvodu nadřazené závislosti.
    + Nepoužívané nastavení RunConfiguration se v auto_prepare_environment zastaralo.
  + **azureml-mlflow**
    + Vylepšené využití prostředků ve vzdálených spuštěních, které používají AzureML. mlflow.
    + Vylepšili dokumentaci k balíčku AzureML-mlflow.
    + Opravili jsme problém, kdy mlflow. log_artifacts ("my_dir") uloží artefakty v rámci "my_dir/Artifact-Paths" místo "artefakt-Paths".
  + **azureml-pipeline-core**
    + Parametr hash_paths pro všechny kroky kanálu je zastaralý a v budoucnu se odebere. Ve výchozím nastavení je obsah source_directory hash (s výjimkou souborů uvedených v souboru. amlignore nebo. gitignore).
    + Pokračující zlepšování modulu a ModuleStep pro podporu modulů výpočetních typů, přípravu na RunConfiguration integraci a další změny pro odemknutí využití modulu specifického pro výpočetní typ v kanálech.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Vylepšená dokumentace s ohledem na vstupy a výstupy.
    + AzureBatchStep: výchozí hodnota delete_batch_job_after_finish hodnotu true.
  + **azureml-train-core**
    + Řetězce jsou nyní přijímány jako cíl výpočtů pro automatizované ladění parametrů.
    + Nepoužívané nastavení RunConfiguration se v auto_prepare_environment zastaralo.
    + Zastaralé parametry `conda_dependencies_file_path` a `pip_requirements_file_path` namísto `conda_dependencies_file` a `pip_requirements_file`.
  + **azureml-opendatasets**
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Sada Azure Machine Learning data PREP SDK v 1.1.8

+ **Nové funkce**
 + Objekty toku dat se teď dají iterovat a vytvářejí sekvenci záznamů. `Dataflow.to_record_iterator`najdete v dokumentaci.
  + Objekty toku dat se teď dají iterovat a vytvářejí sekvenci záznamů. `Dataflow.to_record_iterator`najdete v dokumentaci.

+ **Opravy chyb a vylepšení**
 + Zvýšila se odolnost sady SDK pro sadu pro dataprep.
 + Vylepšené zpracování pandasch datarámečcích pomocí neřetězcových indexů sloupců.
 + Zvýšil se výkon `to_pandas_dataframe` v datových sadách.
 + Opravili jsme chybu, při které se spuštění rutiny Sparku u datových sad nezdařilo v prostředí s více uzly.
  + Zvýšila se odolnost sady SDK pro sadu pro dataprep.
  + Vylepšené zpracování pandasch datarámečcích pomocí neřetězcových indexů sloupců.
  + Zvýšil se výkon `to_pandas_dataframe` v datových sadách.
  + Opravili jsme chybu, při které se spuštění rutiny Sparku u datových sad nezdařilo v prostředí s více uzly.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Sada Azure Machine Learning data PREP SDK v 1.1.7

Změnili jsme změnu, která zlepšila výkon, protože způsobila problémům pro některé zákazníky, kteří používají Azure Databricks. Pokud jste narazili na problém s Azure Databricks, můžete upgradovat na verzi 1.1.7 pomocí jedné z níže uvedených metod:
1. Spusťte tento skript k upgradu: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Znovu vytvořte cluster, který nainstaluje nejnovější verzi sady SDK pro přípravu dat.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Sada SDK Azure Machine Learning pro Python v 1.0.45

+ **Nové funkce**
  + Přidat náhradní model rozhodovacího stromu pro napodobení vysvětlení v balíčku AzureML-vysvětlit-model
  + Možnost zadat verzi CUDA, která se má nainstalovat na Image Inferencing Podpora pro CUDA 9,0, 9,1 a 10,0.
  + Informace o obrázcích Azure ML pro školení jsou nyní k dispozici v úložišti GitHub a [Dockerhubu](https://hub.docker.com/_/microsoft-azureml) [KONTEJNERů Azure ml](https://github.com/Azure/AzureML-Containers) .
  + Přidala se podpora rozhraní příkazového řádku pro plán kanálu. Pokud se chcete dozvědět víc, spusťte příkaz AZ ml Pipeline h.
  + Do konfigurace nasazení AKS webwebservice a rozhraní příkazového řádku se přidal vlastní parametr oboru názvů Kubernetes.
  + Zastaralý parametr hash_paths pro všechny kroky kanálu
  + Model. Register nyní podporuje registraci více jednotlivých souborů jako jeden model s použitím parametru `child_paths`.

+ **Funkce ve verzi Preview**
    + Vyhodnocování bodování teď můžou volitelně ukládat informace conda a PIP pro spolehlivější serializaci a deserializaci.
    + Oprava chyby pro selektor automatických funkcí
    + Aktualizace mlflow. AzureML. build_image na nové rozhraní API, opravy chyb vystavené novou implementací.

+ **Opravy chyb a vylepšení**
  + Z AzureML-Core se odebrala závislost `paramiko`. Přidání upozornění na zastarání pro starší metody připojení cíle výpočtů
  + Zvyšte výkon běhu. create_children
  + Při napodobování s binárním klasifikátorem je možné opravit pořadí pravděpodobnosti, když se pro škálování hodnot tvarů používá pravděpodobnost použití učitelů.
  + Vylepšené zpracování chyb a zpráva pro automatizované strojové učení.
  + Opravili jsme problém s časovým limitem iterace pro automatizované strojové učení.
  + Vylepšili jsme výkon transformace časových řad pro automatizované strojové učení.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Sada Azure Machine Learning data PREP SDK v 1.1.6

+ **Nové funkce**
  + Byly přidány souhrnné funkce pro nejvyšší hodnoty (`SummaryFunction.TOPVALUES`) a dolní hodnoty (`SummaryFunction.BOTTOMVALUES`).

+ **Opravy chyb a vylepšení**
  + Výrazně se vylepšil výkon `read_pandas_dataframe`.
  + Opravili jsme chybu, která by způsobila, že `get_profile()` v toku dat směřujícím do binárních souborů došlo k chybě.
  + Zpřístupnění `set_diagnostics_collection()`, které umožňuje programům povolit nebo zakázat shromažďování telemetrie.
  + Změna chování `get_profile()`. Hodnoty NaN jsou nyní ignorovány pro funkce min, střed_hodn, STD a Sum, které odpovídají chování PANDAS.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Sada SDK Azure Machine Learning pro Python v 1.0.43

+ **Nové funkce**
  + Azure Machine Learning teď nabízí špičkovou podporu pro oblíbené služby Machine Learning a data Analysis Framework Scikit-učení. Pomocí [`SKLearn` Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)můžou uživatelé snadno naučit a nasazovat modely Scikit-učení.
    + Naučte se [spouštět ladění parametrů pomocí Scikit – Naučte se pomocí Hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Přidání podpory pro vytváření ModuleStep v kanálech spolu s třídami Module a ModuleVersion pro správu opakovaně použitelných výpočetních jednotek.
  + ACI WebServices teď podporují trvalé scoring_uri prostřednictvím aktualizací. Scoring_uri se změní z IP adresy na plně kvalifikovaný název domény. Popisek názvu DNS pro plně kvalifikovaný název domény je možné nakonfigurovat nastavením dns_name_label v deploy_configuration.
  + Automatizované nové funkce strojového učení:
    + STL featurizer pro prognózování
    + Clustering KMeans je povolený pro rozmazání funkcí.
  + AmlCompute se schvalování kvót jenom rychleji. Nyní jsme automatizovaný proces schvalování vašich žádostí o kvótu v rámci prahové hodnoty. Další informace o tom, jak kvóty fungují, najdete v tématu [Správa kvót](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Funkce ve verzi Preview**
    + Integrace se sledováním [MLflow](https://mlflow.org) 1.0.0 prostřednictvím balíčku AzureML-MLflow ([ukázkových poznámkových bloků](https://aka.ms/azureml-mlflow-examples)).
    + Odešle Poznámkový blok Jupyter jako běh. [Referenční dokumentace k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Public Preview [detektoru pro přenos dat](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) prostřednictvím balíčku AzureML-contrib-dis ([ukázkových poznámkových bloků](https://aka.ms/azureml-datadrift-example)). Posun dat je jedním z hlavních důvodů, kdy přesnost modelu se v průběhu času snižuje. K tomu dochází, když se data obsluhovaná modelem v produkčním prostředí liší od dat, na která byl model vyškolený. AML data unášený detektor pomáhá zákazníkovi monitorovat posun dat a odesílat výstrahu vždy, když se zjistí posun.

+ **Rozbíjející změny v**

+ **Opravy chyb a vylepšení**
  + RunConfiguration Load and Save podporuje zadání úplné cesty k souboru s úplným zpětným kompatibilitou pro předchozí chování.
  + Přidání do mezipaměti v ServicePrincipalAuthentication je ve výchozím nastavení vypnuté.
  + Povolí protokolování více zobrazovaných souborů pod stejným názvem metriky.
  + Třída modelu je nyní správně importovaná z AzureML. Core (`from azureml.core import Model`).
  + V krocích kanálu je `hash_path` parametr teď zastaralý. Novým chováním je dokončení source_directory hash, s výjimkou souborů uvedených v souboru. amlignore nebo. gitignore.
  + V balíčcích kanálu se různé `get_all` a `get_all_*` metody zastaraly namísto `list` a `list_*`, v uvedeném pořadí.
  + modul AzureML. Core. get_run již nevyžaduje Import tříd před vrácením původního typu spuštění.
  + Opravili jsme problém, kdy některá volání aktualizace WebService neaktivovala aktualizaci.
  + Časový limit vyhodnocování u AKS WebServices by měl být mezi 5ms a 300000ms. Maximální povolený počet scoring_timeout_ms pro požadavky na bodování byl předaný z 1 min na 5 min.
  + Objekty LocalWebservice nyní mají vlastnosti `scoring_uri` a `swagger_uri`.
  + Z procesu uživatele se přesunuly vytvoření adresáře výstupů a výstupy adresáře pro odeslání. Byla povolena sada SDK historie spuštění v každém procesu uživatele. To by mělo vyřešit některé problémy synchronizace, ke kterým dochází v distribuovaném školicím běhu.
  + Název procesu AzureML napsaný z názvu uživatelského procesu teď bude zahrnovat název procesu (jenom pro distribuované školení) a PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Sada Azure Machine Learning data PREP SDK v 1.1.5

+ **Opravy chyb a vylepšení**
  + Pro interpretované hodnoty DateTime, které mají formát roku se dvěma číslicemi, se rozsah platných roků aktualizoval tak, aby se shodoval se systémem Windows. Rozsah byl změněn z 1930-2029 na 1950-2049.
  + Při čtení souboru a nastavení `handleQuotedLineBreaks=True``\r` bude považován za nový řádek.
  + Opravili jsme chybu, která způsobila selhání `read_pandas_dataframe` v některých případech.
  + Vylepšený výkon `get_profile`.
  + Vylepšené chybové zprávy.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Sada Azure Machine Learning data PREP SDK v 1.1.4

+ **Nové funkce**
  + Nyní můžete použít následující funkce jazyka výrazů k extrakci a analyzování hodnot data a času na nové sloupce.
    + `RegEx.extract_record()` extrahuje prvky DateTime do nového sloupce.
    + `create_datetime()` vytvoří objekty DateTime z oddělených elementů DateTime.
  + Při volání `get_profile()`nyní můžete vidět, že sloupce Quantile jsou označeny jako (EST.), aby jasně označovaly, že hodnoty jsou aproximace.
  + Při čtení z Azure Blob Storage teď můžete použít * * expanzi názvů.
    + např. `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Opravy chyb**
  + Opravili jsme chybu související s čtením souboru Parquet ze vzdáleného zdroje (Azure BLOB).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Sada SDK Azure Machine Learning pro Python v 1.0.39
+ **Provedeny**
  + Možnost spuštění auto_prepare_environment konfigurace je zastaralá a automatické připravování se stane výchozím nastavením.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Sada Azure Machine Learning data PREP SDK v 1.1.3

+ **Nové funkce**
  + Byla přidána podpora pro čtení z databáze PostgresSQL, a to buď voláním read_postgresql nebo pomocí úložiště dat.
    + Podívejte se na příklady v tématu návody:
      + [Poznámkový blok pro přijímání dat](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Poznámkový blok úložiště dat](https://aka.ms/aml-data-prep-datastore-nb)

+ **Opravy chyb a vylepšení**
  + Opravené problémy s převodem typu sloupce:
  + Nyní správně převede logický nebo číselný sloupec na logický sloupec.
  + Nyní neselže při pokusu o nastavení sloupce data na typ Date.
  + Vylepšené typy JoinType a doprovodná Referenční dokumentace. Při spojování dvou toků teď můžete zadat jeden z těchto typů spojení:
    + NONE, MATCH, VNITŘNÍ, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Vylepšený datový typ Inferencing pro rozpoznání dalších formátů data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portál Azure

V Azure Portal teď můžete:
+ Vytváření a spouštění automatizovaných experimentů ML
+ Vytvořte virtuální počítač poznámkového bloku, abyste si vyzkoušeli ukázkové poznámkové bloky Jupyter nebo vaše vlastní.
+ Zcela nový oddíl pro vytváření obsahu (Preview) v pracovním prostoru Azure Machine Learning, který zahrnuje automatizované Machine Learning, vizuální rozhraní a hostované virtuální počítače poznámkových bloků
    + Automatické vytvoření modelu pomocí automatizovaného strojového učení
    + Použití vizuálního rozhraní pro přetahování myší ke spouštění experimentů
    + Vytvořte virtuální počítač poznámkového bloku pro zkoumání dat, vytváření modelů a nasazování služeb.
+ Aktualizace živého grafu a metriky v sestavách spuštění a na stránkách podrobností
+ Byl aktualizován prohlížeč souborů pro protokoly, výstupy a snímky na stránkách podrobností o spuštění.
+ Nové a vylepšené prostředí pro vytváření sestav na kartě experimenty.
+ Byla přidána možnost stáhnout soubor config. JSON ze stránky přehled v pracovním prostoru Azure Machine Learning.
+ Podpora Azure Machine Learning vytváření pracovních prostorů z pracovního prostoru Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Sada SDK Azure Machine Learning pro Python v 1.0.33
+ **Nové funkce**
  + Metoda _Workspace. Create_ nyní přijímá výchozí konfiguraci clusteru pro clustery CPU a GPU.
  + Pokud se vytvoření pracovního prostoru nepovede, vyčistí se závislé prostředky.
  + Výchozí Azure Container Registry SKU byl přepnut na Basic.
  + Pokud je to potřeba pro spuštění nebo vytvoření bitové kopie, vytvoří se Azure Container Registry laxně vytvářená.
  + Podpora prostředí pro školicí běhy.

### <a name="notebook-virtual-machine"></a>Virtuální počítač poznámkového bloku 

Použijte virtuální počítač s poznámkovým blokem jako zabezpečené hostitelské prostředí pro Jupyter poznámkových bloků, ve kterém můžete programovat experimenty strojového učení, nasadit modely jako koncové body webu a provádět všechny ostatní operace podporované Azure Machine Learning SDK pomocí Pythonu. Nabízí několik možností:
+ [Rychle aktivujte předkonfigurovaných virtuálních počítačů s poznámkovým blokem](tutorial-1st-experiment-sdk-setup.md) , který má nejnovější verzi sady Azure Machine Learning SDK a související balíčky.
+ Přístup je zabezpečený prostřednictvím prověřených technologií, jako je protokol HTTPS, Azure Active Directory ověřování a autorizace.
+ Spolehlivé cloudové úložiště poznámkových bloků a kódu v účtu služby pracovní prostor Azure Machine Learning BLOB Storage. Virtuální počítač poznámkového bloku můžete bezpečně odstranit, aniž byste ztratili práci.
+ Předinstalované ukázkové poznámkové bloky k prozkoumávání a experimentování s funkcemi Azure Machine Learning.
+ Úplné možnosti přizpůsobení virtuálních počítačů Azure, všech typů virtuálních počítačů, všech balíčků a všech ovladačů. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Sada Azure Machine Learning SDK pro Python v 1.0.33 vydaná.

+ Azure ML Modely s hardwarovou akcelerací v [FPGA](how-to-deploy-fpga-web-service.md) je všeobecně dostupná.
  + Nyní můžete [použít balíček AzureML-akcelerace-Models](how-to-deploy-fpga-web-service.md) k těmto akcím:
    + Výuka vah podporované neuronové sítě (ResNet 50, ResNet 152, DenseNet-121, VGG-16 a SSD-VGG)
    + Využijte přenosové učení s podporovaným DNN
    + Zaregistrujte model pomocí služby Správa modelů Service a kontejnerizace model
    + Nasazení modelu do virtuálního počítače Azure s FPGA v clusteru Azure Kubernetes Service (AKS)
  + Nasazení kontejneru na zařízení [Azure Data box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) serveru
  + Určení skóre dat pomocí koncového bodu gRPC pomocí této [ukázky](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizované strojové učení

+ Funkce, která umožňuje dynamické přidávání :::no-loc text="featurizers"::: pro optimalizaci výkonu. Nové :::no-loc text="featurizers":::: vkládání pracovních verzí, váha legitimace, cílové kódování, kódování textu v cíli, vzdálenost clusteru
+ Inteligentní CV pro zpracování rozdělených a platných rozdělení v rámci automatizovaného ML
+ Několik změn optimalizace paměti a vylepšení výkonu za běhu
+ Zvýšení výkonu v vysvětlení modelu
+ Převod modelu ONNX pro místní běh
+ Přidání podpory subvzorkování
+ Inteligentní zastavování, když nejsou definovaná žádná kritéria ukončení
+ Skládané komplety

+ Prognózování časových řad
  + Nová funkce prognózy předpovědi
  + V datech časové řady teď můžete použít křížové ověření původu.
  + Nově přidaná funkce pro konfiguraci časové řady prodlevy
  + Přidala se nová funkce pro podporu agregačních funkcí kumulativního okna.
  + Nové zjišťování svátků a featurizer, když je v nastavení experimentů definovaný kód země

+ Azure Databricks
  + Povolení prognózy časových řad a funkce explainabilty/interpretace modelu
  + Nyní můžete zrušit a obnovit (pokračovat) automatizované experimenty ML.
  + Přidání podpory pro dvoujádrové zpracování

### <a name="mlops"></a>MLOps
+ **Místní nasazení & ladění pro kontejnery bodování**<br/> Nyní můžete nasadit model ML místně a rychle iterovat na základě souboru bodování a závislostí, abyste se ujistili, že se chovají podle očekávání.

+ **Představený model InferenceConfig &. deploy ()**<br/> Nasazení modelu teď podporuje určení zdrojové složky pomocí vstupního skriptu, který je stejný jako RunConfig.  Nasazení modelu se navíc zjednodušilo na jediný příkaz.

+ **Sledování odkazů Gitu**<br/> Zákazníci si na určitou dobu vyžádali základní možnosti integrace Gitu, protože pomáhají udržovat záznam kompletního auditu. Implementovali jsme sledování mezi hlavními entitami v Azure ML pro metadata týkající se Gitu (úložiště, potvrzení, vyčištění stavu). Tyto informace budou automaticky shromažďovány sadou SDK a rozhraní příkazového řádku.

+ **Profilace modelu & ověřovací služba**<br/> Zákazníci se často setkávají s obtížností pro správnou velikost výpočtů přidružených ke své odvozené službě. Díky naší službě profilace modelu může zákazník poskytovat ukázkové vstupy a pro určení optimální velikosti pro nasazení bude profilovat napříč 16 různými konfiguracemi CPU a paměti.

+ **Přineste si vlastní základní image pro odvození**<br/> Další běžnou stížností bylo obtížnost při přesunu z experimentu na odvozené závislosti při OPAKOVANÉm sdílení. Díky naší nové funkci pro sdílení imagí teď můžete pro odvození znovu použít základní image, závislosti a vše experimentování. To by mělo zrychlit nasazení a snížit mezeru od vnitřní k vnější smyčce.

+ **Vylepšené prostředí pro generování schématu Swagger**<br/> Naše předchozí metoda generování Swagger byla náchylná k chybám a neznemožňuje automatizaci. Máme nový online způsob generování schémat Swagger z jakékoli funkce Pythonu prostřednictvím dekoratéry. Otevřeli jsme tento kód a náš protokol pro generování schématu není spojený s platformou Azure ML.

+ **Azure ML CLI je všeobecně dostupná (GA).**<br/> Modely se teď dají nasadit pomocí jednoho příkazu CLI. Máme běžné názory zákazníků, které neobsahují žádný model ML z Jupyter poznámkového bloku. [**Referenční dokumentace k**](https://aka.ms/azmlcli) rozhraní příkazového řádku byla aktualizována.


## <a name="2019-04-22"></a>2019-04-22

Sada Azure Machine Learning SDK pro Python v 1.0.30 vydaná.

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) byl zaveden k přidání nové verze publikovaného kanálu při zachování stejného koncového bodu.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Sada Azure Machine Learning data PREP SDK v 1.1.2

Poznámka: sada SDK pro přípravu dat už nebude instalovat balíčky `numpy` a `pandas`. Viz [aktualizované pokyny k instalaci](https://aka.ms/aml-data-prep-installation).

+ **Nové funkce**
  + Nyní můžete použít transformaci pivotu.
    + Návod: podrobný [Poznámkový blok](https://aka.ms/aml-data-prep-pivot-nb)
  + Nyní můžete použít regulární výrazy v nativních funkcích.
    + Příklady:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Nyní můžete použít `to_upper` a `to_lower` funkcí v jazyce Expression.
  + V datovém profilu teď můžete zobrazit počet jedinečných hodnot každého sloupce.
  + U některých běžně používaných kroků čtečky teď můžete předat `infer_column_types` argument. Pokud je nastavená na `True`, prostředí pro přípravu dat se pokusí rozpoznat a automaticky převést typy sloupců.
    + `inference_arguments` je nyní zastaralé.
  + Nyní můžete volat `Dataflow.shape`.

+ **Opravy chyb a vylepšení**
  + `keep_columns` nyní přijímá další volitelný argument `validate_column_exists`, který kontroluje, zda výsledek `keep_columns` bude obsahovat všechny sloupce.
  + Všechny kroky čtecího zařízení (které se čtou ze souboru) nyní přijímají další volitelný argument `verify_exists`.
  + Vylepšený výkon při čtení z PANDAS dataframe a získávání profilů dat.
  + Opravili jsme chybu, kdy došlo k chybě průřezu jednoho kroku z toku dat s jedním indexem.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portál Azure
  + Existující skript teď můžete znovu odeslat v existujícím vzdáleném výpočetním clusteru.
  + Nyní můžete spustit publikovaný kanál s novými parametry na kartě kanály.
  + Podrobnosti o spuštění teď podporují nový prohlížeč souborů snímků. Po odeslání konkrétního běhu můžete zobrazit snímek adresáře. Můžete si také stáhnout Poznámkový blok, který byl odeslán za účelem spuštění spuštění.
  + Nyní můžete zrušit nadřazená spuštění z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Sada SDK Azure Machine Learning pro Python v 1.0.23

+ **Nové funkce**
  + Sada Azure Machine Learning SDK teď podporuje Python 3,7.
  + Azure Machine Learning DNN odhady teď nabízí integrovanou podporu více verzí. Například `TensorFlow` Estimator nyní přijímá parametr `framework_version` a uživatelé mohou zadat verzi 1,10 nebo 1,12. Seznam verzí podporovaných vaší aktuální verzí sady SDK volání `get_supported_versions()` na požadované třídě rozhraní (například `TensorFlow.get_supported_versions()`).
  Seznam verzí podporovaných nejnovější verzí sady SDK najdete v [dokumentaci k DNN Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Sada Azure Machine Learning data PREP SDK v 1.1.1

+ **Nové funkce**
  + Pomocí transformací read_ * můžete číst více zdrojů DataStore/DataPath/DataReference sources.
  + Pokud chcete vytvořit nový sloupec, můžete na sloupcích provádět následující operace: dělení, podlah, modulo, napájení, délka.
  + Příprava dat je teď součástí sady diagnostiky Azure ML a ve výchozím nastavení bude protokolovat diagnostické informace.
    + Pro vypnutí nastavte tuto proměnnou prostředí na true: DISABLE_DPREP_LOGGER

+ **Opravy chyb a vylepšení**
  + Vylepšená dokumentace kódu pro běžně používané třídy a funkce.
  + Opravili jsme chybu v auto_read_file, že se nepodařilo přečíst soubory aplikace Excel.
  + Přidání možnosti pro přepsání složky v read_pandas_dataframe.
  + Vylepšený výkon instalace závislostí dotnetcore2 a přidání podpory pro Fedora 27/28 a Ubuntu 1804.
  + Vylepšili jsme výkon čtení z objektů blob Azure.
  + Detekce typu sloupce teď podporuje sloupce typu Long.
  + Byla opravena chyba, kdy byly některé hodnoty data zobrazeny jako časová razítka místo objektů DateTime v jazyce Python.
  + Byla opravena chyba, kdy byly některé počty typů zobrazeny jako dvojité místo celých čísel.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Sada SDK Azure Machine Learning pro Python v 1.0.21

+ **Nové funkce**
  + Metoda *AzureML. Core. Run. create_children* umožňuje vytvoření více podřízených spuštění s jedním voláním s nízkou latencí.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Sada Azure Machine Learning data PREP SDK v 1.1.0

+ **Rozbíjející změny v**
  + Koncept balíčku pro přípravu dat je zastaralý a už není podporovaný. Místo uchování více toků dat v jednom balíčku můžete uchovávat pouze jednotlivé toky dat.
    + Návod: [otevření a uložení poznámkového bloku toku](https://aka.ms/aml-data-prep-open-save-dataflows-nb) dat

+ **Nové funkce**
  + Příprava dat teď může rozpoznat sloupce, které odpovídají konkrétnímu sémantickému typu, a odpovídajícím způsobem je rozdělit. STypes aktuálně podporuje: e-mailová adresa, geografické souřadnice (zeměpisná šířka & zeměpisná délka), adresy IPv4 a IPv6, telefonní číslo USA a PSČ USA.
    + Návod: [Poznámkový blok sémantických typů](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Příprava dat teď podporuje následující operace pro vygenerování výsledného sloupce ze dvou číselných sloupců: odečíst, vynásobení, dělení a modulo.
  + Můžete volat `verify_has_data()` v toku dat a ověřit, zda by tok dat při spuštění vytvářely záznamy.

+ **Opravy chyb a vylepšení**
  + Nyní můžete zadat počet přihrádek, které se mají použít v histogramu pro profily číselných sloupců.
  + `read_pandas_dataframe` transformaci teď vyžaduje, aby měl datový rámec názvy sloupců typu String nebo Byte.
  + Opravili jsme chybu v `fill_nulls` transformaci, kde hodnoty nebyly správně vyplněny, pokud sloupec chybí.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Sada SDK Azure Machine Learning pro Python v 1.0.18

 + **Provedeny**
   + Balíček AzureML-tensorboard nahrazuje. AzureML-contrib-tensorboard.
   + V této verzi můžete při vytváření amlcompute (Managed Compute Cluster) nastavit uživatelský účet. To lze provést předáním těchto vlastností v konfiguraci zřizování. Další podrobnosti najdete v [referenční dokumentaci k sadě SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Sada Azure Machine Learning data PREP SDK v 1.0.17

+ **Nové funkce**
  + Nyní podporuje přidání dvou číselných sloupců pro vygenerování výsledného sloupce pomocí jazyka výrazů.

+ **Opravy chyb a vylepšení**
  + Vylepšili jsme dokumentaci a kontrolu parametrů random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Sada Azure Machine Learning data PREP SDK v 1.0.16

+ **Oprava chyby**
  + Opravili jsme problém s ověřováním instančního objektu, který způsobila změna rozhraní API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Sada SDK Azure Machine Learning pro Python v 1.0.17

+ **Nové funkce**

  + Azure Machine Learning nyní poskytuje prvotřídní podporu pro oblíbený řetěz DNN Framework. Použití [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) třídy uživatelé můžou snadno naučit a nasazovat modely řetězení.
    + Naučte se [spouštět distribuované školení pomocí ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb) .
    + Naučte se [spouštět ladění parametrů pomocí chainer pomocí Hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) .
  + Přidání kanálů Azure Machine Learning možnost aktivovat spuštění kanálu na základě úprav úložiště dat. [Poznámkový blok plánu](https://aka.ms/pl-schedule) kanálu se aktualizuje a prezentuje tuto funkci.

+ **Opravy chyb a vylepšení**
  + Přidali jsme podporu Azure Machine Learningch kanálů pro nastavení vlastnosti source_directory_data_store na požadované úložiště dat (například úložiště objektů BLOB) na [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , které jsou k [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)dodány. Ve výchozím nastavení se jako záložní úložiště dat používají úložiště souborů Azure, které může narazit na problémy s omezením při současném spuštění velkého počtu kroků.

### <a name="azure-portal"></a>Portál Azure

+ **Nové funkce**
  + Nové možnosti editoru tabulek přetahování pro sestavy. Uživatelé mohou sloupec přetáhnout ze seznamu i do oblasti tabulky, kde se zobrazí náhled tabulky. Sloupce lze změnit jejich uspořádání.
  + Nový prohlížeč souborů protokolů
  + Odkazy na spuštění, výpočty, modely, image a nasazení z karty aktivity

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Sada Azure Machine Learning data PREP SDK v 1.0.15

+ **Nové funkce**
  + Příprava dat teď podporuje vytváření datových proudů souborů z datového toku. Také poskytuje možnost manipulovat s názvy datových proudů souborů a vytvořit nové názvy souborů.
    + Návod: [práce s poznámkovým blokem souborové streamy](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Opravy chyb a vylepšení**
  + Vylepšený výkon t-Digest u velkých datových sad.
  + Příprava dat teď podporuje čtení dat z datové cesty.
  + Jedno horké kódování teď funguje na logických a číselných sloupcích.
  + Další různé opravy chyb.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Sada SDK Azure Machine Learning pro Python v 1.0.15

+ **Nové funkce**
  + Kanály Azure Machine Learning přidali AzureBatchStep ([Poznámkový](https://aka.ms/pl-azbatch)blok), HyperDriveStep (Poznámkový blok) a funkce plánování založené na[čase (Poznámkový blok)](https://aka.ms/pl-schedule).
  +  DataTranferStep aktualizace pro práci s Azure SQL Server a Azure Database for PostgreSQL ([Poznámkový blok](https://aka.ms/pl-data-trans)).

+ **Provedeny**
  + Nepoužívané `PublishedPipeline.get_published_pipeline` ve prospěch `PublishedPipeline.get`.
  + Nepoužívané `Schedule.get_schedule` ve prospěch `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Sada Azure Machine Learning data PREP SDK v 1.0.12

+ **Nové funkce**
  + Data PREP teď podporuje čtení z databáze SQL Azure pomocí úložiště dat.

+ **Provedeny**
  + Vylepšený výkon u velkých objemů paměti u různých operací.
  + `read_pandas_dataframe()` nyní vyžaduje, aby bylo zadáno `temp_folder`.
  + Vlastnost `name` v `ColumnProfile` byla zastaralá – použijte místo toho `column_name`.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Sada SDK Azure Machine Learning pro Python v 1.0.10

+ **Změny**:
  + Sada Azure ML SDK už nemá jako závislost balíčky Azure-CLI. Konkrétně byly ze služby AzureML-Core odebrány závislosti v profilech Azure-CLI-Core a Azure-CLI-Profile. Změny dopadu na uživatele:
    + Pokud provádíte příkazu AZ login a potom pomocí nástroje AzureML-SDK, bude sada SDK protokolovat kód prohlížeče nebo zařízení ještě příště. Nepoužije žádný stav přihlašovacích údajů vytvořený příkazem AZ Login.
    + Pro ověřování pomocí rozhraní příkazového řádku Azure, například pomocí příkazu AZ Login, použijte třídu _AzureML. Core. Authentication. AzureCliAuthentication_ . V případě ověřování Azure CLI do prostředí Python _nainstalujte Azure-CLI_ do prostředí Pythonu, kde máte nainstalovanou službu AzureML-SDK.
    + Pokud pomocí instančního objektu pro automatizaci zadáváte "AZ login", doporučujeme použít třídu _AzureML. Core. Authentication. ServicePrincipalAuthentication_ , protože AzureML-SDK nepoužívá stav přihlašovacích údajů vytvořený pomocí Azure CLI.

+ **Opravy chyb**: Tato verze většinou obsahuje drobné opravy chyb.

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Sada Azure Machine Learning data PREP SDK v 1.0.8

+ **Opravy chyb**
  + Vylepšili jsme výkon při získávání datových profilů.
  + Opravené drobné chyby související s hlášením chyb.

### <a name="azure-portal-new-features"></a>Azure portal: nové funkce
+ Nové prostředí pro vytváření grafů pro sestavy. Uživatelé mohou přetáhnout sloupec nebo atribut ze seznamu do oblasti grafu, kde systém automaticky vybere vhodný typ grafu pro uživatele na základě typu dat. Uživatelé mohou změnit typ grafu na jiné vhodné typy nebo přidat další atributy.

    Podporované typy grafů:
    - Spojnicový graf
    - Histogram
    - Skládaný pruhový graf
    - Krabicový diagram
    - Bodové vykreslení
    - Bublinový graf
+ Portál nyní dynamicky generuje sestavy pro experimenty. Když uživatel odešle běh do experimentu, automaticky se vygeneruje sestava s protokolovanými metrikami a grafy, aby se povolilo porovnání v různých spuštěních.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Sada SDK Azure Machine Learning pro Python v 1.0.8

+ **Opravy chyb**: Tato verze většinou obsahuje drobné opravy chyb.

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Sada Azure Machine Learning data PREP SDK v 1.0.7

+ **Nové funkce**
  + Vylepšení úložiště dat (popsaná v tématu [Průvodce úložištěm dat](https://aka.ms/aml-data-prep-datastore-nb))
    + Přidání funkce pro čtení a zápis do sdílené složky Azure a úložiště dat ADLS při škálování.
    + Při použití úložiště dat teď služba data PREP podporuje ověřování instančního objektu místo interaktivního ověřování.
    + Přidání podpory pro adresy URL wasb a wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Sada Azure Machine Learning data PREP SDK v 1.0.6

+ **Opravy chyb**
  + Opravená chyba při čtení z veřejných čitelných kontejnerů objektů blob Azure v Sparku

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Sada SDK Azure Machine Learning pro Python v 1.0.6
+ **Opravy chyb**: Tato verze většinou obsahuje drobné opravy chyb.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Sada Azure Machine Learning data PREP SDK v 1.0.4

+ **Nové funkce**
  + funkce `to_bool` nyní umožňuje převést neshodné hodnoty na chybové hodnoty. Toto je nové výchozí chování při neshodě `to_bool` a `set_column_types`, zatímco předchozí výchozí chování převedlo neshodné hodnoty na false.
  + Při volání `to_pandas_dataframe`je k dispozici nová možnost pro interpretaci hodnot null a chybějících v číselných sloupcích jako NaN.
  + Byla přidána možnost zkontrolovat návratový typ některých výrazů, aby byla zajištěna konzistence typů a předčasné selhání.
  + Nyní můžete volat `parse_json` pro analýzu hodnot ve sloupci jako objektů JSON a jejich rozbalení do více sloupců.

+ **Opravy chyb**
  + Opravili jsme chybu, která selhala `set_column_types` v Pythonu 3.5.2.
  + Opravili jsme chybu, která selhala při připojování k úložišti dat pomocí Image AML.

+ **Aktualizace**
  * [Příklady poznámkových bloků](https://aka.ms/aml-data-prep-notebooks) pro úvodní kurzy, případové studie a návody.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Obecná dostupnost

Azure Machine Learning je teď všeobecně dostupná.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
V této verzi oznamujeme nové spravované výpočetní prostředí prostřednictvím [výpočetní technologie Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute). Tento cíl výpočtů nahrazuje Azure Batch AI COMPUTE pro Azure Machine Learning.

Tento cíl výpočtů:
+ Se používá pro školení modelů a odvozování a bodování dávky.
+ Výpočetní prostředí pro více uzlů
+ Provádí správu clusteru a plánování úloh pro uživatele.
+ Automatické škálování ve výchozím nastavení
+ Podpora pro prostředky CPU a GPU
+ Povoluje použití virtuálních počítačů s nízkou prioritou pro snížené náklady.

Azure Machine Learning Compute je možné vytvořit v Pythonu pomocí webu Azure portal nebo rozhraní příkazového řádku. Musí být vytvořené v oblasti vašeho pracovního prostoru a nemůže být připojen k jinému pracovnímu prostoru. Tento cíl výpočtů používá kontejner Docker pro váš běh a zabalí vaše závislosti pro replikaci stejného prostředí ve všech uzlech.

> [!Warning]
> Doporučujeme vytvořit nový pracovní prostor používat Azure Machine Learning Compute. Může se vzdálená stát, které uživatelům pokusu o vytvoření aplikace Azure Machine Learning Compute z existujícího pracovního prostoru může zobrazit chyba. By měla fungovat to neovlivní existující výpočetních prostředků ve vašem pracovním prostoru.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning sady SDK pro Python v1.0.2
+ **Rozbíjející změny v**
  + V této verzi jsme se odebrání podpory pro vytvoření virtuálního počítače ze služby Azure Machine Learning. Stále můžete připojit existující cloud virtuálního počítače nebo na vzdáleném místním serverem.
  + Podpora pro BatchAI, které by měl být podporovány prostřednictvím Azure Machine Learning Compute nyní také odebíráme.

+ **Nové**
  + Pro strojové učení kanály:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aktualizovat**
  + Pro strojové učení kanály:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) nyní přijímá runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) nyní zkopíruje do a ze zdroje dat SQL
    + Naplánovat funkce v sadě SDK vytvářet a aktualizovat plány pro spouštění publikovaných kanály

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Sada SDK v0.5.2 pro přípravu dat Azure Machine Learning
+ **Rozbíjející změny v**
  * `SummaryFunction.N` byla přejmenována na `SummaryFunction.Count`.

+ **Opravy chyb**
  * Při čtení a zápisu do úložiště dat ve vzdálených spuštěních použijte nejnovější AML tokenu spuštění. Dříve Pokud AML spustit Token se aktualizuje v Pythonu, modul runtime přípravy dat nebude aktualizována aktualizované AML spustit Token.
  * Další jasnější chybové zprávy
  * to_spark_dataframe () přestane selhat, pokud Spark používá `Kryo` serializaci.
  * Počet hodnot inspektoru teď můžete zobrazit více než 1 000 jedinečných hodnot
  * Náhodné rozdělení už selže, pokud původní tok dat nemá název

+ **Další informace**
  * [Sada SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentace a poznámkové bloky
+ Kanály ML
  + Nové a aktualizované poznámkových bloků pro zahájení práce s kanály, rozsahu služby batch a styl přenos příklady: https://aka.ms/aml-pipeline-notebooks
  + Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md)
  + Zjistěte, jak [spouštění predikcí služby batch pomocí kanálů](how-to-run-batch-predictions.md)
+ Azure Machine Learning cíl výpočtů
  + [Ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) se teď aktualizují tak, aby používaly nový spravovaný výpočetní výkon.
  + [Další informace o tomto výpočetní](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure portal: nové funkce
+ Vytvoření a správa [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) typy na portálu.
+ Monitorování využití kvóty a [žádost o kvótu](how-to-manage-quotas.md) pro Azure Machine Learning Compute.
+ Zobrazit Azure Machine Learning stav výpočetního clusteru v reálném čase.
+ Vytvoření Azure Machine Learning Compute a službě Azure Kubernetes Service byla přidána podpora virtuální sítě.
+ Znovu spusťte publikované kanály se stávajícími parametry.
+ Nové [automatizované machine learning grafy](how-to-understand-automated-ml.md) modelů klasifikace (výtah, zisky, kalibrací, funkce význam graf s modelem explainability) a regresních modelů (zbytky a funkce význam grafu s modelem explainability).
+ Kanály lze zobrazit na webu Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning sady SDK pro Python v0.1.80

+ **Rozbíjející změny v**
  * *azureml.Train.widgets* obor názvů se přesunula do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* zastarání následující třídy – *azureml.core.compute.BatchAICompute* a *azureml.core.compute.DSVMCompute*. Tato třída se odebere v dalších verzích. Třída AmlCompute má nyní jednodušší definici a jednoduše potřebuje vm_size a max_nodes a automatické škálování clusteru od 0 do max_nodes, když je úloha odeslána. Naše [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) byly s těmito informacemi aktualizované a měly by vám poskytnout příklady využití. Věříme, že je třeba toto zjednodušení a spoustu další zajímavé funkce, které chcete se dělí na novější verzi!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Sada SDK v0.5.1 pro přípravu dat Azure Machine Learning

Další informace o sadě SDK pro Data Prep načtením [referenční dokumentace](https://aka.ms/data-prep-sdk).
+ **Nové funkce**
   * Vytvoření nového příkazového řádku přípravy a spouštění balíčků přípravy a zobrazení data profilu pro datovou sadu nebo toku dat
   * Přepracované rozhraní API SetColumnType použitelnosti
   * Přejmenované smart_read_file k auto_read_file
   * Nyní zahrnuje Nerovnoměrná distribuce a míra fluktuace v profil dat
   * Vzorkovat s povoleným vzorkováním vrstveného
   * Může číst ze souborů zip, které obsahují soubory CSV
   * Může rozdělit datové sady na řádek s náhodným rozdělením (například do sady test-vlak).
   * Může získat všechny datové typy sloupců z datového toku nebo datového profilu voláním `.dtypes`
   * Může získat počet řádků z datového toku nebo datového profilu voláním `.row_count`

+ **Opravy chyb**
   * Pevně long double převodu
   * Oprava vyhodnocení po přidání sloupce
   * Opravili jsme problém s FuzzyGrouping, kde ji nebude skupiny v některých případech rozpoznat
   * Opravené seřadit funkce dodržovat pořadí řazení více sloupců
   * Pevné a/nebo výrazy, které se mají podobat tomu, jak je `pandas` zpracovávat
   * Oprava čtení z dbfs cesty
   * Provedené pomohou lépe porozumět chybové zprávy
   * Po přečtení na vzdáleném cílovém cíli s použitím tokenu AML se teď už nestane
   * Teď už na Linuxu DSVM selže
   * Teď už dojde k chybě hodnoty jiné než řetězec jsou v predikátech řetězec
   * Nyní zpracovává kontrolního výrazu chyby při by měl správně selhání toku dat
   * Teď podporuje umístění dbutils připojené úložiště v Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Portál Azure
Azure Portal pro Azure Machine Learning má následující aktualizace:
  * Nový **kanály** kartu pro publikované kanály.
  * Přidání podpory pro připojení k existujícímu clusteru HDInsight jako cílové výpočetní prostředí.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning sady SDK pro Python v0.1.74

+ **Rozbíjející změny v**
  * \* Pracovní prostor. compute_targets, úložiště dat, experimenty, obrázky, modely a *WebServices* jsou vlastnosti namísto metod. Nahraďte třeba *Workspace.compute_targets()* s *Workspace.compute_targets*.
  * *Run.get_context* zastarání *Run.get_submitted_run*. Druhá metoda odebere v dalších verzích.
  * *PipelineData* třídy nyní očekává, že objekt úložiště dat jako parametr spíše než datastore_name. Obdobně *kanálu* přijímá default_datastore spíše než default_datastore_name.

+ **Nové funkce**
  * Kanály Azure Machine Learning [ukázkový poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) teď používá MPI kroky.
  * RunDetails widget pro poznámkové bloky Jupyter se aktualizuje a zobrazí vizualizaci kanálu.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Sada SDK v0.4.0 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  * Počet typů přidat Data profilu
  * Počet hodnot a Histogram je nyní k dispozici
  * Další percentily v profil dat
  * Je k dispozici v Summarize Medián
  * Python 3.7 se teď podporuje.
  * Při uložení toku dat, která obsahuje úložišť do balíčku přípravy informace úložiště dat bude trvalé jako součást balíčku přípravy
  * Zápis do úložiště dat se teď podporuje.

+ **Chyba opravená**
  * 64. v systému Linux jsou nyní správně zpracovány bitové unsigned integer přetečení.
  * Oprava nesprávné textový popisek pro soubory ve formátu prostého textu v smart_read
  * Typ sloupce řetězec se zobrazí v zobrazení metrik
  * Počet typů je teď pevně zobrazíte ValueKinds namapován na jeden typ pole namísto jednotlivé značky
  * Write_to_csv už selže, pokud je poskytnuta cesta jako řetězec
  * Při použití nahradit, byste museli opustit "najít" prázdné už neselže

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning sady SDK pro Python v0.1.68

+ **Nové funkce**
  * Podpora více tenantů při vytváření nového pracovního prostoru

+ **Opravy chyb**
  * Při nasazování webové služby už nemusíte připnout verzi knihovny pynacl.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Sada SDK v0.3.0 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  * Přidat metodu transform_partition_with_file(script_path), což umožňuje uživatelům a zajistěte tak předání cestu k souboru Pythonu ke spuštění

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning sady SDK pro Python v0.1.65
[Verze 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) obsahuje nové funkce, další dokumentaci, opravy chyb a další [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks).

Zobrazit [seznam známých problémů](resource-known-issues.md) Další informace o známých chyb a jejich řešení.

+ **Rozbíjející změny v**
  * Workspace.experiments Workspace.models, Workspace.compute_targets, Workspace.images Workspace.web_services návratový slovníku. Tím se předtím vrátila seznam. Zobrazit [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentace k rozhraní API.

  * Automatizované Machine Learning odebrán směrodatná normalizované chyba primární metriky.

+ **HyperDrive**
  * Různé opravy chyb HyperDrive Bayesova, vylepšení výkonu pro získání metriky volání.
  * Tensorflow 1.10 upgrade z 1.9
  * Optimalizovat image dockeru pro úplné spuštění.
  * Úloha nyní hlásí správný stav, i když ukončí s kódem chyby jinou než 0.
  * RunConfig atributu validation v sadě SDK.
  * HyperDrive spustit objekt podporuje Storno podobný regulární spuštění: nemusíte předávat žádné parametry.
  * Widget vylepšení pro udržování stavu rozevírací seznam hodnot pro distribuované spuštění a HyperDrive spuštění.
  * TensorBoard a dalších protokolů, které podporují soubory vyřešili serveru parametr.
  * Podpora MPI Intel(R) na straně služby.
  * Opravu parametr ladění pro distribuované spuštění oprava během ověřování v BatchAI.
  * Správce kontextu nyní identifikuje primární instance.

+ **Azure portal ještě neznáte**
  * log_table() a log_row() jsou podporovány v podrobnostech o spuštění.
  * Automaticky vytvoří grafy pro tabulky a řádky s 1, 2 nebo 3 číselnými sloupci a volitelným sloupcem kategorií.

+ **Automatizované Machine Learning**
  * Vylepšené zpracování chyb a dokumentace
  * Načtení vlastnosti spuštění opravili problémy s výkonem.
  * Oprava pokračovat v běhu problém.
  * Vyřešené problémy s iterací :::no-loc text="ensembling":::.
  * Oprava školení Změ chyb v systému MAC OS.
  * Převzorkování – makro průměrné žádosti o přijetí změn a křivka ROC ve scénáři vlastní ověřování.
  * Odebrat další index logiku.
  * Odebrali jsme filtr, z get_output rozhraní API.

+ **Kanály**
  * Přidat metodu Pipeline.publish() do kanálu publikovat přímo, bez nutnosti spuštění se spouští jako první.
  * Přidala se metoda PipelineRun. get_pipeline_runs () pro načtení spuštění kanálu, která byla vygenerována z publikovaného kanálu.

+ **Project Brainwave**
  * Aktualizovanou podporu nových modelů AI k dispozici na FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Sada SDK v0.2.0 pro přípravu dat Azure Machine Learning
[0.2.0 verze](https://pypi.org/project/azureml-dataprep/0.2.0/) obsahuje následující funkce a opravy chyb:

+ **Nové funkce**
  * Podpora pro jeden horkou kódování
  * Podpora pro transformaci quantile

+ **Chyba opravená:**
  * Funguje s jinou verzí tornádu není nutné přejít na nižší verzi tornádu verzi
  * Četnost hodnot pro všechny hodnoty, ne jenom první tři

## <a name="2018-09-public-preview-refresh"></a>2018-09 (aktualizace verze public preview)

Nová aktualizovaná verze Azure Machine Learning: Přečtěte si další informace o této verzi: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Další kroky

Přečtěte si přehled služby [Azure Machine Learning](overview-what-is-azure-ml.md).
