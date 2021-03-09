---
title: Zjištění posunu dat u datových sad (Preview)
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak nastavit detekci posunu dat ve službě Azure Learning. Můžete vytvářet monitory datových sad (Preview), monitorovat posun dat a nastavit výstrahy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 5a3d16445c5a4276f07f4ed502b9830a10c4ff72
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518904"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Zjištění posunu dat (Preview) u datových sad

Naučte se monitorovat posun dat a nastavovat upozornění, když je posun vysoké úrovně.  

S Azure Machine Learning monitory datových sad (Preview) můžete:
* **Analyzujte posun dat** , abyste pochopili, jak se v průběhu času mění.
* **Monitorujte data modelu** pro rozdíly mezi školením a obsluhou datových sad.  Začněte [shromažďováním dat modelu z nasazených modelů](how-to-enable-data-collection.md).
* **Monitorujte nová data** pro rozdíly mezi jednotlivými základními a cílovými datovou sadou.
* **Profilování funkcí v datech** vám umožní sledovat, jak se statistické vlastnosti mění v čase.
* **Nastavte výstrahy na posun dat** a včasné výstrahy na potenciální problémy. 
* **[Vytvořit novou verzi datové sady] (postupy-verze-Track** -datové sady, když určíte, že data jsou příliš velká.

K vytvoření monitorování se používá [datová sada Azure Machine Learning](how-to-create-register-datasets.md) . Datová sada musí zahrnovat sloupec časového razítka.

Metriky pro posun dat můžete zobrazit pomocí sady Python SDK nebo v Azure Machine Learning Studiu.  K dispozici jsou další metriky a přehledy prostřednictvím prostředku [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) přidruženého k pracovnímu prostoru Azure Machine Learning.

> [!IMPORTANT]
> Detekce posunu dat pro datové sady je aktuálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

K vytváření a práci s monitory datových sad potřebujete:
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
* [Nainstalovaná sada Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/install), která zahrnuje balíček AzureML-DataSet Sets.
* Strukturovaná (tabulková) data s časovým razítkem zadaným v cestě k souboru, názvu souboru nebo sloupci v datech.

## <a name="what-is-data-drift"></a>Co je posun dat?

Posun dat je jedním z hlavních důvodů, které se v průběhu času snižují přesností.  U modelů strojového učení je posun dat změnou v modelu vstupní data, která vede k modelování snížení výkonu.  Posun dat sledování pomáhá detekovat tyto problémy s výkonem modelu.

Mezi příčiny posunu dat patří:

- Vytvoří nadřazený proces změny, jako je například vyměněný senzor, který změní měrné jednotky z palce na centimetry. 
- Problémy s kvalitou dat, jako je například přerušený senzor, vždy čtou 0.
- Přirozený posun dat, jako je například střední změna teploty v průběhu období.
- Změna v souvislosti mezi funkcemi nebo covariate Shift. 

Azure Machine Learning zjednodušuje detekci posunu tím, že počítá jednu metriku, která abstrakcí složitost datových sad, které jsou porovnávány.  Tyto datové sady můžou mít stovky funkcí a desítky tisíc řádků. Po zjištění posunu můžete přejít k podrobnostem o tom, které funkce způsobují posun.  Pak provedete kontrolu metrik na úrovni funkcí pro ladění a izolujete původní příčinu pro posun.

Tento přístup k hornímu okraji usnadňuje monitorování dat namísto tradičních technik založených na pravidlech. Techniky založené na pravidlech, jako je povolený rozsah dat nebo povolené jedinečné hodnoty, můžou být časově náročné a náchylné k chybám.

V Azure Machine Learning používáte k detekci a upozornění na posun dat monitorování datových sad.
  
### <a name="dataset-monitors"></a>Monitory datových sad 

Pomocí monitoru datových sad můžete:

* Detekce a upozornění na posun dat pro nová data v datové sadě.
* Analyzujte historická data za účelem posunu.
* Profilovat nová data v průběhu času.

Algoritmus posunu dat poskytuje celkové množství změn v datech a uvádí, které funkce jsou zodpovědné za další šetření. Monitory datových sad vytvoří řadu dalších metrik pomocí profilování nových dat v `timeseries` datové sadě. 

Vlastní upozorňování je možné nastavit na všech metrikách vygenerovaných monitorováním prostřednictvím [Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Monitory datových sad lze použít k rychlému zachycení problémů s daty a ke zkrácení času pro ladění problému tím, že identifikují pravděpodobná příčiny.  

V koncepčním případě existují tři primární scénáře nastavení monitorování datových sad v Azure Machine Learning.

Scénář | Popis
---|---
Sledování dat v modelu, který slouží k posunu od školicích dat | Výsledky z tohoto scénáře je možné interpretovat jako monitorování proxy serveru pro přesnost modelu. vzhledem k tomu, že se v případě, že se obsluhují data ze školicích dat, snižuje přesnost modelu.
Sledujte datovou sadu časových řad pro posun od předchozího časového období. | Tento scénář je obecnější a dá se použít k monitorování datových sad, které jsou v nadřazeném nebo podřízeném sestavení modelu.  Cílová datová sada musí mít sloupec časového razítka. Základní datovou sadu může být libovolná tabulková sada, která má funkce společné s cílovou datovou sadou.
Proveďte analýzu minulých dat. | Tento scénář se dá použít k pochopení historických dat a informování o rozhodnutích v nastavení pro monitorování datových sad.

Monitory datových sad závisí na následujících službách Azure.

|Služba Azure  |Description  |
|---------|---------|
| *Integrován* | Při načítání školicích dat a porovnávání dat pro školení modelů posun používá Machine Learning datové sady.  Generování profilu dat se používá ke generování některých hlášených metrik, jako jsou minimální, maximální a jedinečné hodnoty, počet jedinečných hodnot. |
| *Kanál a výpočetní prostředí pro AzureML* | Úloha výpočtu posunu je hostovaná v kanálu AzureML.  Úloha se aktivuje na vyžádání nebo podle plánu, aby běžela na výpočetním prostředí nakonfigurovaném v době vytváření odchodu sledování.
| *Application Insights*| Posun vygeneruje metriky pro Application Insights patřící do pracovního prostoru Machine Learning.
| *Azure Blob Storage*| Posun vygeneruje metriky ve formátu JSON do úložiště objektů BLOB v Azure.

### <a name="baseline-and-target-datasets"></a>Základní a cílové datové sady 

Můžete monitorovat datové [sady Azure Machine Learning](how-to-create-register-datasets.md) pro posun dat. Když vytváříte monitorování datových sad, budete odkazovat na vaše:
* Základní datová sada – obvykle pro školicí datovou sadu pro model.
* Cílová datová sada – obvykle vstupní data modelu – je porovnána v průběhu času na základní datovou sadu. Toto porovnání znamená, že vaše cílová datová sada musí mít zadaný sloupec časového razítka.

Monitor bude porovnávat základní a cílové datové sady.

## <a name="create-target-dataset"></a>Vytvořit cílovou datovou sadu

Cílová datová sada musí mít `timeseries` nastavenou vlastnost zadáním sloupce časového razítka buď ze sloupce v datech, nebo z virtuálního sloupce odvozeného ze vzor cesty souborů. Vytvořte datovou sadu pomocí časového razítka prostřednictvím [sady Python SDK](#sdk-dataset) nebo [Azure Machine Learning studia](#studio-dataset). Aby bylo možné přidat vlastnost do datové sady, je nutné zadat sloupec reprezentující "časové razítko" `timeseries` . Pokud jsou vaše data rozdělená do struktury složek s časovými údaji, jako je {RRRR/MM/DD}, vytvořte virtuální sloupec pomocí nastavení vzoru cesty a nastavte ho jako časové razítko oddílu, aby se zlepšila důležitost funkcí časové řady.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

[`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)Metoda třídy [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) definuje sloupec časového razítka pro datovou sadu.

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

> [!TIP]
> Úplný příklad použití `timeseries` vlastnosti datových sad naleznete v dokumentaci k [ukázkovému poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) nebo [sadě SDK datových sad](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Pokud vytvoříte datovou sadu pomocí nástroje Azure Machine Learning Studio, ujistěte se, že cesta k datům obsahuje informace o časovém razítku, zahrňte všechny podsložky s daty a nastavte formát oddílu.

V následujícím příkladu jsou pořízena všechna data v podsložce *NoaaIsdFlorida/2019* a formát oddílu určuje rok, měsíc a den časového razítka.

[![Formát oddílu](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

V nastavení **schématu** zadejte sloupec časového razítka z virtuálního nebo reálného sloupce v zadané datové sadě:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Nastavit časové razítko":::

Pokud jsou data rozdělená do oddílů podle data, jak je uvedeno v tomto případě, můžete také zadat partition_timestamp.  To umožňuje efektivnější zpracování dat.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Časové razítko oddílu":::

---

## <a name="create-dataset-monitor"></a>Vytvořit monitorování datových sad

Vytvořte monitorování datových sad pro detekci a upozorňování na posun dat pro novou datovou sadu.  Použijte buď [Python SDK](#sdk-monitor) , nebo [Azure Machine Learning Studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Úplné podrobnosti najdete v [referenční dokumentaci k Python SDK na posunu dat](/python/api/azureml-datadrift/azureml.datadrift) . 

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

> [!TIP]
> Úplný příklad nastavení `timeseries` datové sady a datového posunu dat najdete v našem [poznámkovém bloku](https://aka.ms/datadrift-notebook).


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Přejděte na [domovskou stránku studia](https://ml.azure.com).
1. Na levé straně vyberte kartu **datové sady** . 
1. Vyberte **monitorování datových sad**.
   ![Seznam monitorování](./media/how-to-monitor-datasets/monitor-list.png)

1. Klikněte na tlačítko **+ vytvořit monitorování** a pokračujte v průvodci kliknutím na tlačítko **Další**.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Průvodce vytvořením monitorování":::

* **Vyberte cílovou datovou sadu**.  Cílová datová sada je tabulková sada se zadaným sloupcem časového razítka, která se bude analyzovat pro posun dat. Cílová datová sada musí mít funkce společné se základní datovou sadou a měla by být `timeseries` datová sada, ke které se připojí nová data. Historická data v cílové datové sadě můžete analyzovat, nebo je možné monitorovat nová data.

* **Vyberte základní datovou sadu.**  Vyberte tabelární datovou sadu, která se má použít jako základ pro porovnání cílové datové sady v průběhu času.  Základní datová sada musí mít funkce společné s cílovou datovou sadou.  Vyberte časový rozsah pro použití řezu cílové datové sady nebo zadejte samostatnou datovou sadu, kterou chcete použít jako základ.

* **Nastavení monitorování**.  Tato nastavení se dají vytvořit v případě naplánovaných kanálů monitorování datových sad, které se vytvoří. 

    | Nastavení | Popis | Tipy | Měnitelné | 
    | ------- | ----------- | ---- | ------- |
    | Name | Název monitorování datové sady | | No |
    | Funkce | Seznam funkcí, které se budou analyzovat pro posun dat v průběhu času. | Nastavte na výstupní funkce modelu pro měření posunu konceptu. Nepoužívejte funkce, které se přirozeně odunášeny v průběhu času (měsíc, rok, index atd.). Po úpravě seznamu funkcí můžete zpětně naplnit a sledovat sledování posunu dat. | Yes | 
    | Cílový výpočetní objekt | Pokud chcete spustit úlohy monitorování datových sad, Azure Machine Learning výpočetní cíl. | | Yes | 
    | Povolit | Povolí nebo zakáže plán na kanálu monitorování datových sad. | Zakažte plán k analýze historických dat s nastavením obnovení. Dá se povolit po vytvoření monitoru datové sady. | Yes | 
    | Frekvence | Frekvence, která se použije k naplánování úlohy kanálu a k analýze historických dat, pokud se spustí zpětná výplň. Mezi možnosti patří denní, týdenní nebo měsíční. | Každé spuštění porovná data v cílové datové sadě podle četnosti: <li>Denně: porovnat poslední dokončený den v cílové datové sadě se směrným plánem <li>Týdně: porovnat poslední dokončený týden (pondělí-neděle) v cílové datové sadě se směrným plánem <li>Month: Compare poslední dokončený měsíc v cílové datové sadě se směrným plánem | No | 
    | Latence | Čas, který je v hodinách, trvá pro doručení dat do datové sady. Pokud například trvá tři dny, než dorazí data do databáze SQL DB pro zapouzdření datové sady, nastavte latenci na 72. | Po vytvoření monitorování datové sady nelze změnit. | No | 
    | e-mailové adresy, | E-mailové adresy pro výstrahy na základě porušení procentuální prahové hodnoty posunu dat | E-maily se odesílají prostřednictvím Azure Monitor. | Yes | 
    | Prahová hodnota | Procentuální prahová hodnota posunu dat pro e-mailové upozornění. | Další výstrahy a události můžete nastavit u mnoha dalších metrik v přidruženém prostředku Application Insights pracovního prostoru. | Yes |

Po dokončení průvodce se v seznamu zobrazí výsledné monitorování datových sad. Vyberte ji a přejdete na stránku podrobností tohoto monitorování.

---

## <a name="understand-data-drift-results"></a>Vysvětlení výsledků posunu dat

V této části se dozvíte, jaké výsledky monitorují datovou sadu , které najdete na stránce pro  /  **monitorování datové sady datových** sad v Azure studiu.  Můžete aktualizovat nastavení a analyzovat existující data pro konkrétní časové období na této stránce.  

Seznamte se s přehledem vysoké úrovně, který je na velikosti posunu dat, a zvýrazněné funkce, které se mají dále prozkoumat.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Přehled posunu":::


| Metric | Popis | 
| ------ | ----------- | 
| Velikost posunu dat | Procentuální poměr mezi základní a cílovou datovou sadou v průběhu času. V rozsahu od 0 do 100, 0 označuje identické datové sady a 100 znamená, že model posunu dat Azure Machine Learning může zcela sdělit dvě datové sady od sebe. V důsledku technik strojového učení, který se používá ke generování této velikosti, se očekává šum v přesném měřeném procentu. | 
| Funkce horního posunu | Zobrazuje funkce z datové sady, které jsou ve většině dolů, a proto přispívají k metrikě velikosti posunu. V důsledku covariate Shift není nutné základní distribuce funkce nutně měnit, aby měla poměrně vysokou důležitost funkcí. |
| Prahová hodnota | Velikost posunu dat nad rámec nastavené prahové hodnoty spustí výstrahy. Tato možnost se dá nakonfigurovat v nastavení monitorování. | 

### <a name="drift-magnitude-trend"></a>Trend velikosti posunu

Podívejte se, jak se datová sada liší od cílové datové sady v zadaném časovém období.  Čím blíž k 100%, tím více dvou datových sad se liší.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend velikosti posunu":::

### <a name="drift-magnitude-by-features"></a>Posunování velikosti podle funkcí

Tato část obsahuje přehled na úrovni funkcí v rámci změny distribuce vybrané funkce a dalších statistik v průběhu času.

Cílová datová sada je také profilovaná v průběhu času. Statistická vzdálenost mezi distribucí standardních hodnot jednotlivých funkcí je porovnána s cílovou datovou sadou v průběhu času.  V koncepčním případě je to podobné jako velikost posunu dat.  Tato statistická vzdálenost je však určena pro jednotlivé funkce, nikoli pro všechny funkce. K dispozici jsou také minimální, maximální a střední hodnota.

V Azure Machine Learning Studiu kliknutím na pruh v grafu zobrazíte podrobnosti o úrovni funkcí tohoto data. Ve výchozím nastavení se zobrazí distribuce datové sady standardních hodnot a poslední distribuce stejné funkce.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Posunování velikosti podle funkcí":::

Tyto metriky lze také načíst v sadě Python SDK prostřednictvím `get_metrics()` metody `DataDriftDetector` objektu.

### <a name="feature-details"></a>Podrobnosti o funkci

Nakonec se posuňte dolů a zobrazte podrobnosti o jednotlivých funkcích.  Pomocí rozevíracích seznamů nad grafem vyberte funkci a dále vyberte metriku, kterou chcete zobrazit.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Graf číselné funkce a porovnání":::

Metriky v grafu závisí na typu funkce.

* Číselné funkce

    | Metric | Popis |  
    | ------ | ----------- |  
    | Wasserstein vzdálenost | Minimální množství práce k transformaci distribuce směrného plánu do cílové distribuce. |
    | Střední hodnota | Průměrná hodnota funkce |
    | Min. hodnota | Minimální hodnota funkce |
    | Max. hodnota | Maximální hodnota funkce |

* Funkce kategorií
    
    | Metric | Popis |  
    | ------ | ----------- |  
    | Euclidian vzdálenost     |  Vypočítáno pro kategorií sloupce. Euclideaná vzdálenost se počítá na dvou vektorech vygenerovaných z empirické distribuce stejného kategorií sloupce ze dvou datových sad. 0 znamená, že v empirických distribucích není žádný rozdíl.  Tím větší je odchylka od 0, více tohoto sloupce se posune. Trendy je možné pozorovat z grafu časové řady této metriky a mohou být užitečné při zjištění, že se funkce posunuje.  |
    | Jedinečné hodnoty | Počet jedinečných hodnot (mohutnosti) funkce |

V tomto grafu vyberte jedno datum pro porovnání distribuce funkcí mezi cílem a tímto datem zobrazené funkce. U číselných funkcí zobrazuje dvě distribuce pravděpodobnosti.  Pokud je funkce numerická, zobrazí se pruhový graf.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Vyberte datum, které se má porovnat s cílem.":::

## <a name="metrics-alerts-and-events"></a>Metriky, výstrahy a události

Do prostředku [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) přidruženého k pracovnímu prostoru Machine Learning se dají dotázat metriky. Máte přístup ke všem funkcím aplikace Application Insights, včetně nastavení vlastních pravidel výstrah a skupin akcí, které aktivují akci, jako je například E-mail/SMS/Push/Voice nebo funkce Azure Functions. Podrobnosti najdete v dokumentaci k kompletním Application Insights. 

Začněte tím, že přejdete na [Azure Portal](https://portal.azure.com) a vyberete stránku s **přehledem** pracovního prostoru.  Přidružený prostředek Application Insights je na pravé straně:

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

Omezení a známé problémy pro sledování posunu dat:

* Časový rozsah při analýze historických dat je omezen na 31 intervalů nastavení četnosti monitorování. 
* Omezení 200 funkcí, pokud není zadán seznam funkcí (všechny používané funkce).
* Velikost výpočetní kapacity musí být pro zpracování dat dostatečně velká.
* Zajistěte, aby datová sada měla data v rámci počátečního a koncového data daného spuštění monitorování.
* Monitory datové sady budou fungovat jenom u datových sad, které obsahují 50 nebo více řádků.
* Sloupce nebo funkce v datové sadě jsou klasifikovány jako kategorií nebo číselné na základě podmínek v následující tabulce. Pokud tato funkce nesplňuje tyto podmínky – například sloupec typu řetězec s >100 jedinečnými hodnotami, funkce je vyřazena z našeho algoritmu pro posun dat, ale je stále profilovaná. 

    | Typ funkce | Datový typ | Podmínka | Omezení | 
    | ------------ | --------- | --------- | ----------- |
    | Kategorické | String, bool, int, float | Počet jedinečných hodnot ve funkci je menší než 100 a menší než 5% počtu řádků. | Hodnota null se považuje za svou vlastní kategorii. | 
    | Číselné | int, float | Hodnoty ve funkci jsou číselného datového typu a nesplňují podmínky pro funkci kategorií. | Funkce byla vynechána, pokud >15% hodnot mají hodnotu null. | 

* Když jste vytvořili monitor pro posun dat, ale nevidíte data na stránce **monitorování datových sad** v Azure Machine Learning studiu, zkuste následující.

    1. Ověřte, zda jste v horní části stránky vybrali rozsah dat vpravo.  
    1. Na kartě **monitorování datových sad** vyberte odkaz experiment pro kontrolu stavu spuštění.  Tento odkaz je na pravé straně tabulky.
    1. Pokud je spuštění úspěšně dokončeno, zkontrolujte protokoly ovladačů, abyste viděli, kolik metrik bylo vygenerováno, nebo zda jsou k dispozici nějaké zprávy upozornění.  Po kliknutí na experimentu Najděte na kartě **výstup + protokoly** protokoly ovladačů.

* Pokud funkce sady SDK `backfill()` negeneruje očekávaný výstup, může to být způsobeno problémem ověřování.  Když vytvoříte výpočetní prostředky, které se budou předávat do této funkce, nepoužívejte `Run.get_context().experiment.workspace.compute_targets` .  Místo toho použijte [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) jako následující k vytvoření výpočtů, které předáte do této `backfill()` funkce: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* Z kolekce dat modelu může trvat až (obvykle méně než) 10 minut, než se data dorazí do účtu BLOB Storage. V případě skriptu nebo poznámkového bloku počkejte 10 minut, aby se zajistilo, že se buňky níže spustí.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Další kroky

* Přejděte na [Azure Machine Learning studia](https://ml.azure.com) nebo [poznámkového bloku Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) a nastavte monitor DataSet.
* Přečtěte si, jak nastavit posun dat na [modelech nasazených do služby Azure Kubernetes](./how-to-enable-data-collection.md).
* Nastavte monitorování posunu datové sady pomocí [Event gridu](how-to-use-event-grid.md).