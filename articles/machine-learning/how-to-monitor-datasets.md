---
title: Analýza a sledování posunu dat u datových sad (Preview)
titleSuffix: Azure Machine Learning
description: Vytvářejte Azure Machine Learning monitory datových sad (Preview), monitorujte posun dat v datových sadách a nastavte výstrahy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 15cfa56f718290af3ae5fb87aadab70016cc8594
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430238"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Zjištění posunu dat (Preview) u datových sad
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit Azure Machine Learning monitorování datových sad (Preview), monitorovat posun dat a statistické změny v datových sadách a nastavit výstrahy.

Pomocí Azure Machine Learning monitorování datových sad můžete:
* **Analyzujte posun dat** , abyste pochopili, jak se v průběhu času mění.
* **Monitorujte data modelu** pro rozdíly mezi školením a obsluhou datových sad.
* **Monitorujte nová data** pro rozdíly mezi jednotlivými základními a cílovými datovou sadou.
* **Profilování funkcí v datech** vám umožní sledovat, jak se statistické vlastnosti mění v čase.
* **Nastavte výstrahy na posun dat** a včasné výstrahy na potenciální problémy. 

Metriky a přehledy jsou dostupné prostřednictvím prostředku [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) přidruženého k pracovnímu prostoru Azure Machine Learning.

> [!Important]
> Mějte na paměti, že při sledování přenosů dat pomocí sady SDK je k dispozici ve všech edicích, zatímco monitorování se dokončí přes Studio na webu pouze edice Enterprise.

## <a name="prerequisites"></a>Požadavky

K vytváření a práci s monitory datových sad potřebujete:
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet, ještě než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček AzureML-DataSet Sets.
* Strukturovaná (tabulková) data s časovým razítkem zadaným v cestě k souboru, názvu souboru nebo sloupci v datech.

## <a name="what-is-data-drift"></a>Co je posun dat?

V kontextu strojového učení je posun dat změnou v modelu vstupní data, která vede k modelování snížení výkonu. Je jedním z hlavních důvodů, které se v průběhu času snižují přesností, a díky tomu sledování posunu dat pomáhá detekovat problémy s výkonem modelu.

Mezi příčiny posunu dat patří: 

- Vytvoří nadřazený proces změny, jako je například vyměněný senzor, který změní měrné jednotky z palce na centimetry. 
- Problémy s kvalitou dat, jako je například přerušený senzor, vždy čtou 0.
- Přirozený posun dat, jako je například střední změna teploty v průběhu období.
- Změna v souvislosti mezi funkcemi nebo covariate Shift. 

Pomocí Azure Machine Learningch monitorování datových sad můžete nastavit výstrahy, které pomáhají při detekci přenosu dat v datových sadách v průběhu času. 

### <a name="dataset-monitors"></a>Monitory datových sad 

Můžete vytvořit monitorování datových sad pro detekci a upozorňování na data o nových datech v datové sadě, analyzovat historická data a profilovat nová data v průběhu času. Algoritmus posunu dat poskytuje celkové množství změn v datech a uvádí, které funkce jsou zodpovědné za další šetření. Monitory datových sad vytvoří řadu dalších metrik pomocí profilování nových dat v `timeseries` datové sadě. Vlastní upozorňování je možné nastavit na všech metrikách vygenerovaných monitorováním prostřednictvím [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Monitory datových sad lze použít k rychlému zachycení problémů s daty a ke zkrácení času pro ladění problému tím, že identifikují pravděpodobná příčiny.  

V koncepčním případě existují tři primární scénáře nastavení monitorování datových sad v Azure Machine Learning.

Scénář | Description
---|---
Sledování dat modelu obsluhujících data pro zajištění posunu od školicích dat modelu | Výsledky z tohoto scénáře je možné interpretovat jako monitorování proxy serveru pro přesnost modelu. vzhledem k tomu, že se tato přesnost modelu snižuje, pokud se obsluha dat od školicích dat odkládá.
Monitorování datové sady časových řad pro posun od předchozího časového období. | Tento scénář je obecnější a dá se použít k monitorování datových sad, které jsou v nadřazeném nebo podřízeném sestavení modelu.  Cílová datová sada musí mít sloupec časového razítka, zatímco datová sada může být libovolná tabulková sada, která má funkce společné s cílovou datovou sadou.
Probíhá analýza minulých dat. | Tento scénář se dá použít k pochopení historických dat a informování o rozhodnutích v nastavení pro monitorování datových sad.

## <a name="how-dataset-can-monitor-data"></a>Jak může datová sada monitorovat data

Pomocí Azure Machine Learning se v datových sadách monitorují data na posunování dat. Chcete-li monitorovat pro posun dat, je určena datová sada, která je obvykle datovou sadou pro model. Cílová datová sada – obvykle model vstupní data – je porovnána v průběhu času na základní datovou sadu. Toto porovnání znamená, že vaše cílová datová sada musí mít zadaný sloupec časového razítka.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Nastavení `timeseries` vlastnosti v cílové sadě dat

Cílová datová sada musí mít `timeseries` nastavenou vlastnost zadáním sloupce časového razítka buď ze sloupce v datech, nebo z virtuálního sloupce odvozeného ze vzor cesty souborů. To lze provést prostřednictvím sady Python SDK nebo Azure Machine Learning studia. Aby bylo možné přidat vlastnost do datové sady, je nutné zadat sloupec, který představuje časové razítko "jemně zrnitého" `timeseries` . Pokud jsou vaše data rozdělená do struktury složek s časovými informacemi, jako je například {rrrr/MM/DD}, můžete vytvořit virtuální sloupec pomocí nastavení vzoru cesty a nastavit ho jako časové razítko "hrubých", aby se zlepšila důležitost funkcí časové řady. 

#### <a name="python-sdk"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) Metoda Class definuje sloupec časového razítka pro datovou sadu. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Úplný příklad použití `timeseries` vlastnosti datových sad naleznete v dokumentaci k [ukázkovému poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) nebo [sadě SDK datových sad](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Pokud vytvoříte datovou sadu pomocí nástroje Azure Machine Learning Studio, ujistěte se, že cesta k datům obsahuje informace o časovém razítku, zahrňte všechny podsložky s daty a nastavte formát oddílu. 

V následujícím příkladu jsou pořízena všechna data v podsložce *NoaaIsdFlorida/2019* a formát oddílu určuje rok, měsíc a den časového razítka. 

[![Formát oddílu](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

V nastavení **schématu** zadejte sloupec časového razítka z virtuálního nebo reálného sloupce v zadané datové sadě:

![Časové razítko](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Nastavení monitorování datových sad

Po vytvoření datové sady se zadaným nastavením časového razítka jste připraveni ke konfiguraci monitorování datových sad.

Různá nastavení monitorování datových sad jsou rozdělená do tří skupin: **základní informace, nastavení monitorování** a **Nastavení přeplňování**.

### <a name="basic-info"></a>Základní informace

Tato tabulka obsahuje základní nastavení použitá pro monitorování datových sad.

| Nastavení | Description | Tipy | Měnitelné | 
| ------- | ----------- | ---- | ------- | 
| Name | Název monitorování datové sady | | No |
| Základní datová sada | Tabulková datová sada, která bude použita jako základ pro porovnání cílové datové sady v průběhu času. | Základní datová sada musí mít funkce společné s cílovou datovou sadou. Obecně platí, že by měl být standardní hodnota nastavena na školicí datovou sadu modelu nebo na řez cílové datové sady. | No |
| Cílová datová sada | Sada tabulkových dat se zadaným sloupcem časového razítka, která se bude analyzovat pro posun dat | Cílová datová sada musí mít funkce společné se základní datovou sadou a měla by být `timeseries` datová sada, ke které se připojí nová data. Historická data v cílové datové sadě můžete analyzovat, nebo je možné monitorovat nová data. | No | 
| Frekvence | Frekvence, která se použije k naplánování úlohy kanálu a k analýze historických dat, pokud se spustí zpětná výplň. Mezi možnosti patří denní, týdenní nebo měsíční. | Úpravou tohoto nastavení zahrňte do směrného plánu srovnatelnou velikost dat. | No | 
| Funkce | Seznam funkcí, které se budou analyzovat pro posun dat v průběhu času. | Nastavte na výstupní funkce modelu pro měření posunu konceptu. Nezahrnují funkce, které se přirozeně mění v průběhu času (měsíc, rok, index atd.). Po úpravě seznamu funkcí můžete zpětně naplnit a sledovat sledování posunu dat. | Yes | 
| Cílový výpočetní objekt | Pokud chcete spustit úlohy monitorování datových sad, Azure Machine Learning výpočetní cíl. | | Yes | 

### <a name="monitor-settings"></a>Nastavení monitorování

Tato nastavení se dají vytvořit v případě naplánovaných kanálů monitorování datových sad, které se vytvoří. 

| Nastavení | Description | Tipy | Měnitelné | 
| ------- | ----------- | ---- | ------- |
| Povolit | Povolí nebo zakáže plán na kanálu monitorování datových sad. | Zakažte plán k analýze historických dat s nastavením obnovení. Dá se povolit po vytvoření monitoru datové sady. | Yes | 
| Latence | Čas, který je v hodinách, trvá pro doručení dat do datové sady. Pokud například trvá tři dny, než dorazí data do databáze SQL DB pro zapouzdření datové sady, nastavte latenci na 72. | Po vytvoření monitorování datové sady nelze změnit. | No | 
| E-mailové adresy | E-mailové adresy pro výstrahy na základě porušení procentuální prahové hodnoty posunu dat | E-maily se odesílají prostřednictvím Azure Monitor. | Yes | 
| Prahová hodnota | Procentuální prahová hodnota posunu dat pro e-mailové upozornění. | Další výstrahy a události můžete nastavit u mnoha dalších metrik v přidruženém prostředku Application Insights pracovního prostoru. | Yes | 

### <a name="backfill-settings"></a>Nastavení zpětných výplní

Tato nastavení slouží ke spouštění zpětných dat pro metriky pro přenos dat v minulosti.

| Nastavení | Description | Tipy |
| ------- | ----------- | ---- |
| Počáteční datum | Počáteční datum úlohy naplnění | | 
| Koncové datum | Koncové datum úlohy naplnění | Koncové datum nemůže být větší než 31 * frekvence jednotek času od počátečního data. U stávajícího monitorování datových sad může být metrika zpětně vyplněna, aby bylo možné analyzovat historická data nebo nahrazovat metriky s aktualizovanými nastaveními. |

## <a name="create-dataset-monitors"></a>Vytváření monitorování datových sad 

Pomocí Azure Machine Learning studia nebo sady Python SDK můžete vytvářet monitory datových sad pro detekci a upozorňování na data na novou datovou sadu. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Pracovní prostor, který obsahuje datovou sadu, pro kterou chcete vytvořit monitorování, musí mít funkce Enterprise Edition, aby bylo možné nastavit výstrahy na monitoru datových sad. 

Po potvrzení funkčnosti pracovního prostoru přejděte na domovskou stránku studia a na levé straně vyberte kartu datové sady. Vyberte monitorování datových sad.

![Seznam monitorování](./media/how-to-monitor-datasets/monitor-list.png)

Klikněte na tlačítko **+ vytvořit monitorování** a pokračujte v průvodci kliknutím na tlačítko **Další**.

![Tip](./media/how-to-monitor-datasets/wizard.png)

Výsledný monitor DataSet se zobrazí v seznamu. Vyberte ji a přejdete na stránku podrobností tohoto monitorování.

### <a name="from-python-sdk"></a>Z Python SDK

Úplné podrobnosti najdete v [referenční dokumentaci k Python SDK na posunu dat](/python/api/azureml-datadrift/azureml.datadrift) . 

Následující příklad ukazuje, jak vytvořit monitorování datových sad pomocí sady Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Úplný příklad nastavení `timeseries` datové sady a datového posunu dat najdete v našem [poznámkovém bloku](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Porozumění výsledkům posunu dat

Monitorování dat vytváří dvě skupiny výsledků: posunování přehledu a podrobností o funkcích. Následující animace znázorňuje dostupné grafy pro sledování posunu na základě vybrané funkce a metriky. 

![Ukázkové video](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Přehled posunu

Oddíl s **přehledem posunu** obsahuje přehledy na nejvyšší úrovni pro velikost posunu dat a o tom, které funkce se mají dále prozkoumat. 

| Metric | Popis | Tipy | 
| ------ | ----------- | ---- | 
| Velikost posunu dat | Zadáno jako procento mezi základní a cílovou datovou sadou v průběhu času. V rozsahu od 0 do 100, kde 0 označuje identické datové sady a 100 znamená, že funkce posunu dat Azure Machine Learning může zcela sdělit dvě datové sady od sebe. | V důsledku technik strojového učení, který se používá ke generování této velikosti, se očekává šum v přesném měřeném procentu. | 
| Posunovat příspěvek podle funkcí | Příspěvek každé funkce v cílové datové sadě k měřené velikosti posunu. |  V důsledku covariate Shift není nutné základní distribuce funkce nutně měnit, aby měla poměrně vysokou důležitost funkcí. | 

Následující obrázek představuje příklad grafů zobrazených Azure Machine Learning ve službě disNOAA **Studio, které** vyplývají z výplně [integrovaných dat Surface](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Data byla Navzorkovaná na `stationName contains 'FLORIDA'` , s lednem 2019 použitými jako základní datovou sadu a všemi 2019 daty použitými jako cíl.
 
![Přehled posunu](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Podrobnosti o funkci

Část **Podrobnosti o funkci** obsahuje přehledy na úrovni funkcí v rámci změny distribuce vybrané funkce a také dalších statistik v průběhu času. 

Cílová datová sada je také profilovaná v průběhu času. Statistická vzdálenost mezi distribucí standardních hodnot jednotlivých funkcí je porovnána s cílovou datovou sadou v průběhu času, která je koncepčně podobná velikosti posunu dat s výjimkou, že tato statistická vzdálenost je určena pro jednotlivé funkce. K dispozici jsou také minimální, maximální a střední hodnota. 

Pokud v Azure Machine Learning Studiu kliknete na datový bod v grafu, bude se odpovídajícím způsobem měnit distribuce zobrazené funkce. Ve výchozím nastavení se zobrazuje distribuce základní datové sady a poslední distribuce stejné funkce. 

Tyto metriky lze také načíst v sadě Python SDK prostřednictvím `get_metrics()` metody `DataDriftDetector` objektu. 

#### <a name="numeric-features"></a>Číselné funkce 

Číselné funkce jsou profilované v každém spuštění monitorování datových sad. V Azure Machine Learning Studiu jsou k dispozici následující. Pro rozdělení se zobrazí Hustota pravděpodobnosti.

| Metric | Popis |  
| ------ | ----------- |  
| Wasserstein vzdálenost | Minimální množství práce k transformaci distribuce směrného plánu do cílové distribuce. |
| Střední hodnota | Průměrná hodnota funkce |
| Min. hodnota | Minimální hodnota funkce |
| Max. hodnota | Maximální hodnota funkce |

![Podrobnosti o funkcích – číselné](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Funkce kategorií 

Číselné funkce jsou profilované v každém spuštění monitorování datových sad. V Azure Machine Learning Studiu jsou k dispozici následující. Pro distribuci se zobrazí histogram.

| Metric | Popis |  
| ------ | ----------- |  
| Euclidian vzdálenost | Geometrická vzdálenost mezi rozstupnými a cílovými distribucí |
| Jedinečné hodnoty | Počet jedinečných hodnot (mohutnosti) funkce |


![Podrobnosti o funkci kategorií](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metriky, výstrahy a události

Do prostředku [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) přidruženého k pracovnímu prostoru Machine Learning se dají dotázat metriky. Poskytuje přístup ke všem funkcím Application Insights, včetně nastavení vlastních pravidel výstrah a skupin akcí, které aktivují akci, jako je například E-mail/SMS/Push/Voice nebo funkce Azure Functions. Podrobnosti najdete v dokumentaci k kompletním Application Insights. 

Začněte tím, že přejdete na Azure Portal a vyberete stránku s **přehledem** pracovního prostoru.  Přidružený prostředek Application Insights je na pravé straně:

[![Přehled webu Azure Portal](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

V levém podokně v části monitorování vyberte protokoly (analýza):

![Přehled Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Metriky monitorování datových sad jsou uloženy jako `customMetrics` . Můžete napsat a spustit dotaz po nastavení monitorování datové sady, abyste je mohli zobrazit:

[![Dotaz na Log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Po určení metrik pro nastavení pravidel upozornění vytvořte nové pravidlo upozornění:

![Nové pravidlo výstrahy](./media/how-to-monitor-datasets/alert-rule.png)

Můžete použít existující skupinu akcí nebo vytvořit novou, abyste mohli definovat akci, která se má provést při splnění nastavených podmínek:

![Nová skupina akcí](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Řešení potíží

Omezení a známé problémy:

* Časová škála úloh zpětných výplní je omezena na 31 intervalů nastavení frekvence monitorování. 
* Omezení 200 funkcí, pokud není zadán seznam funkcí (všechny používané funkce).
* Velikost výpočetní kapacity musí být pro zpracování dat dostatečně velká. 
* Zajistěte, aby datová sada měla data v rámci počátečního a koncového data daného spuštění monitorování.
* Monitory datové sady budou fungovat jenom u datových sad, které obsahují 50 nebo více řádků. 

Sloupce nebo funkce v datové sadě jsou klasifikovány jako kategorií nebo číselné na základě podmínek v následující tabulce. Pokud tato funkce nesplňuje tyto podmínky – například sloupec typu řetězec s >100 jedinečnými hodnotami, funkce je vyřazena z našeho algoritmu pro posun dat, ale je stále profilovaná. 

| Typ funkce | Datový typ | Podmínka | Omezení | 
| ------------ | --------- | --------- | ----------- |
| Kategorické | String, bool, int, float | Počet jedinečných hodnot ve funkci je menší než 100 a menší než 5% počtu řádků. | Hodnota null se považuje za svou vlastní kategorii. | 
| Číselné | int, float | Hodnoty ve funkci jsou číselného datového typu a nesplňují podmínky pro funkci kategorií. | Funkce byla vynechána, pokud >15% hodnot mají hodnotu null. | 

## <a name="next-steps"></a>Další kroky

* Přejděte na [Azure Machine Learning studia](https://ml.azure.com) nebo [poznámkového bloku Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) a nastavte monitor DataSet.
* Přečtěte si, jak nastavit posun dat na [modelech nasazených do služby Azure Kubernetes](how-to-monitor-data-drift.md).
* Nastavte monitorování posunu datové sady pomocí [Event gridu](how-to-use-event-grid.md). 
