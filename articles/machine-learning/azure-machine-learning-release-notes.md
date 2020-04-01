---
title: Co je nového ve vydání?
titleSuffix: Azure Machine Learning
description: Přečtěte si o nejnovějších aktualizacích Azure Machine Learning a sadaí Python s dsad Pythonu pro strojové učení a přípravu dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: b55c351927a56afce697d07f41bfbe668144d68d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475518"
---
# <a name="azure-machine-learning-release-notes"></a>Poznámky k verzi Azure Machine Learning

V tomto článku se dozvíte o vydáních Azure Machine Learning.  Úplný referenční obsah sady SDK najdete na hlavní referenční stránce sady Azure Machine Learning [**pro python.**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Podívejte se [na seznam známých problémů,](resource-known-issues.md) kde se dozvíte o známých chybách a řešeních.

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK pro Python v1.2.0

+ **Změny způsobující chyby**
  + Drop podpora python 2.7

+ **Opravy a vylepšení chyb**
  + **azurové cli-ml**
    + Přidá "--subscription-id" `az ml model/computetarget/service` k příkazům v příkazech příkazu příkazu.
    + Přidání podpory pro předávání vault_url, key_name a key_version spravovaného zákazníkem (CMK) pro nasazení ACI
  + **azureml-automl-core** 
    + Povolena přizpůsobená imputace s konstantní hodnotou pro úlohy prognózy dat X i y.
    + Opraven problém se zobrazováním chybových zpráv uživateli.    
  + **azureml-automl-runtime**
    + Opraven problém s prognózováním na datových sadách, obsahujícízrna pouze s jedním řádkem
    + Snížena velikost paměti vyžadovaná úlohami prognózy.
    + Pokud má sloupec čas nesprávný formát, byly přidány lepší chybové zprávy.
    + Povolena přizpůsobená imputace s konstantní hodnotou pro úlohy prognózy dat X i y.
  + **azureml-core**
    + Přidejte podporu pro načítání ServicePrincipal z proměnných prostředí: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID a AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Byl zaveden nový `support_multi_line` `Dataset.Tabular.from_delimited_files`parametr :`support_multi_line=False`Ve výchozím nastavení ( ), všechny konce řádků, včetně těch v hodnotách ukótovaných polí, budou interpretovány jako konec záznamu. Čtení dat tímto způsobem je rychlejší a optimalizovanější pro paralelní spuštění na více jádrech procesoru. Může však vést k tichému vytvoření více záznamů s nesprávně zarovnanými hodnotami polí. To by mělo `True` být nastaveno na soubor, kdy je známo, že soubory s oddělenými omezeními obsahují konce řádků v uvozovkách.
    + Přidána možnost registrace ADLS Gen2 v cli Azure Machine Learning
    + Přejmenován parametr 'fine_grain_timestamp' na 'timestamp' a parametr 'coarse_grain_timestamp' na 'partition_timestamp' pro metodu with_timestamp_columns() v TabularDataset, aby lépe odrážel použití parametrů.
    + Maximální délka názvu experimentu byla zvýšena na 255.
  + **azureml-interpret**
    + Aktualizováno azureml-interpretovat interpretovat-komunita 0,7.*
  + **azureml-sdk**
    + Přechod na závislosti s kompatibilní verzí Tilde pro podporu oprav v předběžných verzích a stabilních verzích.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK pro Python v1.1.5

+ **Vyřazení funkce**
  + **Python 2.7**
    + Poslední verze pro podporu pythonu 2.7

+ **Změny způsobující chyby**
  + **Sémantická správa verzí 2.0.0**
    + Počínaje verzí 1.1 Azure ML Python SDK přijímá sémantické versioning 2.0.0. [Další informace najdete tady](https://semver.org/). Všechny následující verze budou následovat nové schéma číslování a sémantické verze smlouvy. 

+ **Opravy a vylepšení chyb**
  + **azurové cli-ml**
    + Změňte název příkazu příkazu koncového bodu cli z 'az ml koncového bodu aks' na 'az ml koncový bod v reálném čase' pro konzistenci.
    + aktualizace pokynů k instalaci příkazu k příkazu k příkazu CLI pro stabilní a experimentální pobočku
    + Profilování jedné instance bylo opraveno, aby se vytvořilo doporučení, a bylo zpřístupněno v základní sdk.
  + **azureml-automl-core**
    + Povoleno odvození dávkového režimu (po více řádcích jednou) pro modely ONNX s automatickým ml
    + Zlepšila se zjišťování četnosti souborů údajů, chyběly údaje nebo obsahovaly nepravidelné datové body
    + Přidána možnost odstranit datové body, které nesplňují dominantní frekvenci.
    + Změněn vstup konstruktoru tak, aby seznam možností použít možnosti imputace pro odpovídající sloupce.
    + Protokolování chyb bylo vylepšeno.
  + **azureml-automl-runtime**
    + Opraven problém s chybou, která se objevila v testovací sadě
    + Byl odstraněn požadavek na y_query během vyhodnocování na službě prognózy
    + Opraven problém s prognózováním, když datová sada obsahuje krátká zrna s dlouhými časovými mezerami.
    + Byl opraven problém, když je zapnuta funkce horizont max a sloupec data obsahuje data ve formě řetězců. Byly přidány správné konverzní a chybové zprávy, pokud převod k dnešnímu dni není možný
    + Použití nativnínumpy a scipy pro serializaci a deserializaci mezilehlých dat pro FileCacheStore (používá se pro místní spuštění AutoML)
    + Opravena chyba, kdy se neúspěšné podřízené běhy mohly zaseknout ve stavu Spuštění.
    + Zvýšená rychlost featurization.
    + Opravena kontrola frekvence během bodování, nyní prognostické úkoly nevyžadují přísnou rovnocennost frekvence mezi vlakem a testovací sadou.
    + Změněn vstup konstruktoru tak, aby seznam možností použít možnosti imputace pro odpovídající sloupce.
    + Opraveny chyby související s výběrem typu zpoždění.
    + Opravena neklasifikovaná chyba naváděná na datových sadách, která měla zrna s jedním řádkem
    + Opraven problém s pomalostí detekce frekvence.
    + Opravuje chybu při zpracování výjimek AutoML, která způsobila skutečný důvod selhání školení, které má být nahrazeno chybou AttributeError.
  + **azureml-cli-společné**
    + Profilování jedné instance bylo opraveno, aby se vytvořilo doporučení, a bylo zpřístupněno v základní sdk.
  + **azureml-contrib-mir**
    + Přidá funkce ve třídě MirWebservice k načtení přístupového tokenu.
    + Použít token auth pro MirWebservice ve výchozím nastavení během MirWebservice.run() volání - Pouze aktualizovat, pokud volání selže
    + Nasazení webové služby Mir nyní vyžaduje správné Skus [Standard_DS2_v2, Standard_F16, Standard_A2_v2] namísto [Ds2v2, A2v2 a F16] v uvedeném pořadí.
  + **azureml-contrib-pipeline-steps**
    + Volitelný parametr side_inputs přidán do ParallelRunStep. Tento parametr lze použít k připojení složky na kontejneru. Aktuálně podporované typy jsou DataReference a PipelineData.
    + Parametry předané v ParallelRunConfig lze přepsat předáváním parametrů kanálu nyní. Nové parametry kanálu podporované aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count a aml_process_count_per_node jsou již součástí dřívější verze).
  + **azureml-core**
    + Nasazená webová služba AzureML `INFO` se teď bude ve výchozím nastavení protokolovat. To lze řídit nastavením proměnné `AZUREML_LOG_LEVEL` prostředí v nasazené službě.
    + Sada Python sdk používá službu zjišťování k použití koncového bodu rozhraní API namísto "kanálů".
    + Přepnutí na nové trasy ve všech hovorech sady SDK
    + Změní směrování volání služby ModelManagementService na novou jednotnou strukturu.
      + Metoda aktualizace pracovního prostoru byla veřejně dostupná.
      + Byl přidán parametr image_build_compute v metodě aktualizace pracovního prostoru, který uživateli umožňuje aktualizovat výpočetní prostředky pro sestavení bitové kopie.
    +  Přidány zprávy o vyřazení do starého pracovního postupu profilování. Pevné limity procesoru a paměti profilování
    + Přidáno RSection jako součást prostředí pro spuštění úloh R
    +  Přidáno `Dataset.mount` ověření pro zvýšení chyby, pokud zdroj datové sady není přístupný nebo neobsahuje žádná data.
    + Přidáno `--grant-workspace-msi-access` jako další parametr pro rozhraní příkazového řádku úložiště dat pro registraci kontejneru objektů blob Azure, který vám umožní zaregistrovat kontejner objektů blob, který je za virtuální sítí
    + Profilování jedné instance bylo opraveno, aby se vytvořilo doporučení, a bylo zpřístupněno v základní sdk.
    + Opraven problém v aks.py _deploy
    + Ověří integritu nahraných modelů, aby se zabránilo selhání tichého úložiště.
    + Uživatel nyní může zadat hodnotu pro auth klíč při generování klíčů pro webové služby.
    + Opravena chyba, kdy velká písmena nelze použít jako vstupní název datové sady
  + **azureml-výchozí hodnoty**
    + `azureml-dataprep`bude nyní nainstalován jako `azureml-defaults`součást aplikace . Již není nutné instalovat dataprep[pojistka] ručně na výpočetní cíle pro připojení datových sad.
  + **azureml-interpret**
    + Aktualizováno azureml-interpretovat interpretovat-komunita 0,6.*
    + Aktualizováno azureml-interpret, který závisí na komunitě interpretů 0.5.0
    + Přidány výjimky ve stylu azureml do azureml-interpretu
    + Opravena serializace DeepScoringExplainer pro modely Keras
  + **azureml-mlflow**
    + Přidání podpory pro suverénní cloudy pro azureml.mlflow
  + **azureml-potrubí-jádro**
    + Poznámkový blok pro vyhodnocování dávky kanálu teď používá ParallelRunStep
    + Opravena chyba, kdy mohly být výsledky PythonScriptStep nesprávně znovu použity i přes změnu seznamu argumentů
    + Přidána možnost nastavit typ sloupců při volání metod parse_* na`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Přesunuto `AutoMLStep` do `azureml-pipeline-steps` balíčku. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.
    + Přidánpříklad dokumentace pro datovou sadu jako vstup PythonScriptStep
  + **azureml-tensorboard**
    + aktualizováno azureml-tensorboard pro podporu tendence 2.0
    + Zobrazit správné číslo portu při použití vlastního portu Tensorboard v instanci výpočetního výkonu
  + **azureml-train-automl-client**
    + Byl opraven problém, kdy některé balíčky mohly být nainstalovány v nesprávných verzích ve vzdálených spuštěních.
    + opraven problém s přepsáním FeaturizationConfig, který filtruje vlastní konfiguraci featurization.
  + **azureml-train-automl-runtime**
    + Opraven problém s detekcí frekvence ve vzdálených spuštěních
    + Přesunuto `AutoMLStep` v `azureml-pipeline-steps` balíčku. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Podpora PyTorch verze 1.4 v PyTorch Odhad
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK pro Python v1.1.2rc0 (předběžná verze)

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + Povoleno odvození dávkového režimu (po více řádcích jednou) pro modely ONNX s automatickým ml
    + Zlepšila se zjišťování četnosti souborů údajů, chyběly údaje nebo obsahovaly nepravidelné datové body
    + Přidána možnost odstranit datové body, které nesplňují dominantní frequrncy.
  + **azureml-automl-runtime**
    + Opraven problém s chybou, která se objevila v testovací sadě
    + Byl odstraněn požadavek na y_query během vyhodnocování na službě prognózy
  + **azureml-contrib-mir**
    + Přidá funkce ve třídě MirWebservice k načtení přístupového tokenu.
  + **azureml-core**
    + Nasazená webová služba AzureML `INFO` se teď bude ve výchozím nastavení protokolovat. To lze řídit nastavením proměnné `AZUREML_LOG_LEVEL` prostředí v nasazené službě.
    + Oprava je na `Dataset.get_all` vrácení všech datových sad registrovaných v pracovním prostoru.
    + Vylepšete chybovou zprávu, `path` když je argumentu rozhraní API pro vytváření datových sad předán neplatný typ.
    + Sada Python sdk používá službu zjišťování k použití koncového bodu rozhraní API namísto "kanálů".
    + Přepnutí na nové trasy ve všech hovorech sady SDK
    + Změní směrování volání služby ModelManagementService na novou jednotnou strukturu.
      + Metoda aktualizace pracovního prostoru byla veřejně dostupná.
      + Byl přidán parametr image_build_compute v metodě aktualizace pracovního prostoru, který uživateli umožňuje aktualizovat výpočetní prostředky pro sestavení bitové kopie.
    +  Přidány zprávy o vyřazení do starého pracovního postupu profilování. Pevné limity procesoru a paměti profilování
  + **azureml-interpret**
    + aktualizovat azureml-interpretovat interpretovat-komunita 0,6.*
  + **azureml-mlflow**
    + Přidání podpory pro suverénní cloudy pro azureml.mlflow
  + **azureml-pipeline-steps**
    + Přesunuto `AutoMLStep` do `azureml-pipeline-steps package`. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.
  + **azureml-train-automl-client**
    + Byl opraven problém, kdy některé balíčky mohly být nainstalovány v nesprávných verzích ve vzdálených spuštěních.
  + **azureml-train-automl-runtime**
    + Opraven problém s detekcí frekvence ve vzdálených spuštěních
    + Přesunuto `AutoMLStep` do `azureml-pipeline-steps package`. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Přesunuto `AutoMLStep` do `azureml-pipeline-steps package`. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK pro Python v1.1.1rc0 (předběžná verze)

+ **Opravy a vylepšení chyb**
  + **azurové cli-ml**
    + Profilování jedné instance bylo opraveno, aby se vytvořilo doporučení, a bylo zpřístupněno v základní sdk.
  + **azureml-automl-core**
    + Protokolování chyb bylo vylepšeno.
  + **azureml-automl-runtime**
    + Opraven problém s prognózováním, když datová sada obsahuje krátká zrna s dlouhými časovými mezerami.
    + Byl opraven problém, když je zapnuta funkce horizont max a sloupec data obsahuje data ve formě řetězců. Přidali jsme správnou konverzi a rozumnou chybu, pokud konverze k dnešnímu dni není možná
    + Použití nativnínumpy a scipy pro serializaci a deserializaci mezilehlých dat pro FileCacheStore (používá se pro místní spuštění AutoML)
    + Opravena chyba, kdy se neúspěšné podřízené běhy mohly zaseknout ve stavu Spuštění.
  + **azureml-cli-společné**
    + Profilování jedné instance bylo opraveno, aby se vytvořilo doporučení, a bylo zpřístupněno v základní sdk.
  + **azureml-core**
    + Přidáno `--grant-workspace-msi-access` jako další parametr pro rozhraní příkazového řádku úložiště dat pro registraci kontejneru objektů blob Azure, který vám umožní zaregistrovat kontejner objektů blob, který je za virtuální sítí
    + Profilování jedné instance bylo opraveno, aby se vytvořilo doporučení, a bylo zpřístupněno v základní sdk.
    + Opraven problém v aks.py _deploy
    + Ověří integritu nahraných modelů, aby se zabránilo selhání tichého úložiště.
  + **azureml-interpret**
    + přidány výjimky ve stylu azureml do azureml-interpretu
    + opravena Serializace DeepScoringExplainer pro modely Keras
  + **azureml-potrubí-jádro**
    + Poznámkový blok pro vyhodnocování dávky kanálu teď používá ParallelRunStep
  + **azureml-pipeline-steps**
    + Přesunuto `AutoMLStep` v `azureml-pipeline-steps` balíčku. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.
  + **azureml-contrib-pipeline-steps**
    + Volitelný parametr side_inputs přidán do ParallelRunStep. Tento parametr lze použít k připojení složky na kontejneru. Aktuálně podporované typy jsou DataReference a PipelineData.
  + **azureml-tensorboard**
    + aktualizováno azureml-tensorboard pro podporu tendence 2.0
  + **azureml-train-automl-client**
    + opraven problém s přepsáním FeaturizationConfig, který filtruje vlastní konfiguraci featurization.
  + **azureml-train-automl-runtime**
    + Přesunuto `AutoMLStep` v `azureml-pipeline-steps` balíčku. Zastarala `AutoMLStep` uvnitř `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Podpora PyTorch verze 1.4 v PyTorch Odhad
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK pro Python v1.1.0rc0 (předběžná verze)

+ **Změny způsobující chyby**
  + **Sémantická správa verzí 2.0.0**
    + Počínaje verzí 1.1 Azure ML Python SDK přijímá sémantické versioning 2.0.0. [Další informace najdete tady](https://semver.org/). Všechny následující verze budou následovat nové schéma číslování a sémantické verze smlouvy. 
  
+ **Opravy a vylepšení chyb**
  + **azureml-automl-runtime**
    + Zvýšená rychlost featurization.
    + Opravena kontrola frekvence během bodování, nyní v prognostických úkolech nepožadujeme přísnou rovnocennost frekvence mezi vlakem a testovací sadou.
  + **azureml-core**
    + Uživatel nyní může zadat hodnotu pro auth klíč při generování klíčů pro webové služby.
  + **azureml-interpret**
    + Aktualizováno azureml-interpret, který závisí na komunitě interpretů 0.5.0
  + **azureml-potrubí-jádro**
    + Opravena chyba, kdy mohly být výsledky PythonScriptStep nesprávně znovu použity i přes změnu seznamu argumentů
  + **azureml-pipeline-steps**
    + Přidánpříklad dokumentace pro datovou sadu jako vstup PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Parametry předané v ParallelRunConfig lze přepsat předáváním parametrů kanálu nyní. Nové parametry kanálu podporované aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count a aml_process_count_per_node jsou již součástí dřívější verze).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK pro Python v1.0.85

+ **Nové funkce**
  + **azureml-core**
    + Získání aktuálního základního využití a omezení kvót pro prostředky AmlCompute v daném pracovním prostoru a předplatném
  
  + **azureml-contrib-pipeline-steps**
    + Umožněte uživateli předat tabulkovou datovou sadu jako zprostředkující výsledek z předchozího kroku parallelrunstepu

+ **Opravy a vylepšení chyb**
  + **azureml-automl-runtime**
    + Byl odebrán požadavek y_query sloupce v požadavku na nasazenou službu prognózy. 
    + "y_query" byla odebrána z části žádosti o doručení notebooku Orange Juice společnosti Dominick.
    + Opravena chyba, která bránila prognózám na nasazených modelech a pracovala na datových sadách se sloupci data času.
    + Přidán akorelace Matthews koeficient jako klasifikace metriky, pro binární a vícetřídní klasifikace.
  + **azureml-contrib-interpret**
    + Odstraněné text explainers z azureml-contrib-interpret jako vysvětlení textu byla přesunuta do repo interpret-text, který bude brzy vydán.
  + **azureml-core**
    + Dataset: použití pro soubor datové sady již závisí na numpy a pandy, které mají být instalovány v python env.
    + Změněno LocalWebservice.wait_for_deployment() zkontrolovat stav místního kontejneru Dockeru před pokusem o ping jeho koncový bod stavu, což výrazně snižuje dobu, která trvá nahlásit selhání nasazení.
    + Opravena inicializace interní vlastnosti použité v localWebservice.reload() při vytvoření objektu služby z existujícího nasazení pomocí konstruktoru LocalWebservice().
    + Upravená chybová zpráva pro objasnění.
    + Do služby AksWebservice byla přidána nová metoda s názvem get_access_token(), která vrátí objekt AksServiceAccessToken, který obsahuje přístupový token, aktualizaci po časovém razítku, vypršení platnosti časového razítka a typu tokenu. 
    + Zastaralé existující get_token() metoda v AksWebservice jako nová metoda vrátí všechny informace, které tato metoda vrátí.
    + Upravený výstup příkazu get-access-token služby az ml. Přejmenován token pro accessToken a refreshBy to refreshAfter. Přidány vlastnosti expiryOn a tokenType.
    + Pevná get_active_runs
  + **azureml-explain-model**
    + aktualizovány na 0.33.0 a interpretovat komunitu na 0,4.*
  + **azureml-interpret**
    + aktualizovány na 0.33.0 a interpretovat komunitu na 0,4.*
  + **azureml-train-automl-runtime**
    + Přidán akorelace Matthews koeficient jako klasifikace metriky, pro binární a vícetřídní klasifikace.
    + Zavržení příznaku předzpracování z kódu a nahrazené featurization -featurization je ve výchozím nastavení zapnuto

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK pro Python v1.0.83

+ **Nové funkce**
  + Datová sada: `on_error` Přidejte dvě možnosti a `out_of_range_datetime` pro `to_pandas_dataframe` selhání, `None`pokud data mají chybové hodnoty namísto jejich vyplnění .
  + Pracovní prostor: `hbi_workspace` Přidán příznak pracovních prostorů s citlivými daty, který umožňuje další šifrování a zakáže pokročilou diagnostiku v pracovních prostorech. Přidali jsme také podporu pro uvedení vlastní klíče pro přidružené `cmk_keyvault` Cosmos DB instance zadáním a `resource_cmk_uri` parametry při vytváření pracovního prostoru, který vytvoří instanci Cosmos DB ve vašem předplatném při zřizování pracovního prostoru. [Více se dočtete zde.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Opravy a vylepšení chyb**
  + **azureml-automl-runtime**
    + Opravena regrese, která způsobovala vyvolání chyby Typu Error při spuštění automatického ml v Pythonu pod 3.5.4.
  + **azureml-core**
    + Opravena `datastore.upload_files` chyba, kde relativní cesta, `./` která nezačínala, nebyla schopna být použita.
    + Přidány zprávy o vyřazení pro všechny kódové cesty třídy Image
    + Opravena konstrukce adresy URL správy modelů pro oblast Mooncake.
    + Opraven problém, kdy modely používající source_dir nemohly být zabaleny pro funkce Azure.    
    + Přidána možnost [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) pro nabízení bitové kopie do registru kontejnerů pracovního prostoru AzureML
    + Byla aktualizována sada SDK tak, aby používala novou knihovnu tokenů v azure synapse způsobem kompatibilním se zpět.
  + **azureml-interpret**
    + Opravena chyba, kdy none byl vrácen, když žádné vysvětlení byly k dispozici ke stažení. Nyní vyvolá výjimku, odpovídající chování jinde.
  + **azureml-pipeline-steps**
    + Nepovolené předávání `DatasetConsumptionConfig`s `Estimator`parametru společnosti , `inputs` pokud `Estimator` `EstimatorStep`bude použita v .
  + **azureml-sdk**
    + Do balíčku azureml-sdk byl přidán klient AutoML, který umožňuje odeslání vzdálených spuštění automatického mlb bez instalace úplného balíčku AutoML.
  + **azureml-train-automl-client**
    + Opraveno zarovnání na výstupu konzoly pro automatické spuštění ml
    + Opravena chyba, kdy mohla být na vzdáleném amlcomputeu nainstalována nesprávná verze pand.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK pro Python v1.0.81

+ **Opravy a vylepšení chyb**
  + **azureml-contrib-interpret**
    + odložit závislost shap interpretovat-komunitu z azureml-interpret
  + **azureml-core**
    + Výpočetní cíl lze nyní zadat jako parametr pro odpovídající objekty konfigurace nasazení. Toto je konkrétně název výpočetního cíle k nasazení, nikoli objekt sdk.
    + Přidáno CreatedBy informace model a service objekty. Lze přistupovat <var>prostřednictvím .created_by
    + Opraven soubor ContainerImage.run(), který správně nenastavoval port HTTP kontejneru Dockeru.
    + Vytvořit `azureml-dataprep` volitelný `az ml dataset register` pro příkaz cli
    + Opravena chyba, kdy `TabularDataset.to_pandas_dataframe` by nesprávně spadala zpět do alternativní čtečky a vytiskla upozornění.
  + **azureml-explain-model**
    + odložit závislost shap interpretovat-komunitu z azureml-interpret
  + **azureml-potrubí-jádro**
    + Byl přidán `NotebookRunnerStep`nový krok kanálu , který spustí místní poznámkový blok jako krok v kanálu.
    + Odebrané zastaralé get_all funkce pro publikované kanály, plány a koncové body pipelineendpoints
  + **azureml-train-automl-client**
    + Spuštění vyřazení data_script jako vstup do automatického ml.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK pro Python v1.0.79

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + Odebrán featurizationConfig, který má být zaznamenán
      + Aktualizováno protokolování pro protokolování "auto"/"off"/"customized" pouze.
  + **azureml-automl-runtime**
    + Přidána podpora pro pandy. Seriály a pandy. Kategorické pro detekci datového typu sloupce. Dříve podporované pouze numpy.ndarray
      + Přidány související změny kódu pro správné zpracování kategorického typu dtype.
    + Bylo vylepšeno rozhraní funkce prognózy: parametr y_pred byl proveden jako volitelný. - Docstrings byly vylepšeny.
  + **azureml-contrib-datová sada**
    + Opravena chyba, kdy nebylo možné připojit označené datové sady.
  + **azureml-core**
    + Oprava chyby pro . `Environment.from_existing_conda_environment(name, conda_environment_name)` Uživatel může vytvořit instanci prostředí, která je přesnou replikou místního prostředí
    + Ve výchozím nastavení byly `include_boundary=True` změněny metody datových sad souvisejících s časovými řadami.
  + **azureml-train-automl-client**
    + Opraven problém, kdy se výsledky ověření nevytisknou, když je výstup prezentace nastaven na hodnotu false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK pro Python v1.0.76

+ **Změny způsobující chyby**
  + Azureml-Train-AutoML problémy s upgradem
    + Upgrade na azureml-train-automl>=1.0.76 z azureml-train-automl<1.0.76 může způsobit částečné instalace, což způsobí, že některé automl importy nezdaří. Chcete-li tento problém vyřešit, https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdmůžete spustit instalační skript na adrese . Nebo pokud používáte pip přímo, můžete:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + nebo můžete před upgradem odinstalovat starou verzi
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Opravy a vylepšení chyb**
  + **azureml-automl-runtime**
    + AutoML bude nyní brát v úvahu true i false třídy při výpočtu průměrné skalární metriky pro binární klasifikace úkoly.
    + Přesunutý kód strojového učení a školení v AzureML-AutoML-Core do nového balíčku AzureML-AutoML-Runtime.
  + **azureml-contrib-datová sada**
    + Při `to_pandas_dataframe` volání na značené datové sady s možností stahování můžete nyní určit, zda chcete přepsat existující soubory nebo ne.
    + Při `keep_columns` volání `drop_columns` nebo výsledkem časové řady, popisek nebo obrázek sloupec vynechán, odpovídající možnosti budou vynechány pro datovou sadu také.
    + Opraven problém se zavaděcem pytorch pro úlohu detekce objektů.
  + **azureml-contrib-interpret**
    + Odstraněno vysvětlení dashboard widget z azureml-contrib-interpret, změnil balíček odkazovat na nový v interpret_community
    + Aktualizovaná verze interpretační komunity na 0.2.0
  + **azureml-core**
    + Zlepšit výkon `workspace.datasets`.
    + Přidána možnost registrace Azure SQL Database Datastore pomocí uživatelského jména a ověřování hesla
    + Oprava pro načítání RunConfigurations z relativních cest.
    + Při `keep_columns` volání `drop_columns` nebo výsledkem je sloupec časové řady vynechána, odpovídající možnosti budou vynechány pro datovou sadu také.
  + **azureml-interpret**
    + aktualizovaná verze interpretační komunity na 0.2.0
  + **azureml-pipeline-steps**
    + Zdokumentované podporované `runconfig_pipeline_params` hodnoty pro kroky kanálu azure machine learningu.
  + **azureml-potrubí-jádro**
    + Přidána možnost CLI pro stažení výstupu ve formátu json pro příkazy Pipeline.
  + **azureml-vlak-automl**
    + Rozdělení AzureML-Train-AutoML na 2 balíčky, klientský balíček AzureML-Train-AutoML-Client a tréninkový balíček ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Byl přidán tenký klient pro odesílání experimentů AutoML, aniž by bylo nutné instalovat závislosti strojového učení místně.
    + Opraveno protokolování automaticky zjištěných prodlev, velikostí valivých oken a maximálních horizontů ve vzdálených jízdách.
  + **azureml-train-automl-runtime**
    + Byl přidán nový balíček AutoML pro izolujeme součásti strojového učení a runtime od klienta.
  + **azureml-contrib-train-rl**
    + Přidána podpora výztuže učení v SDK.
    + Přidána podpora AmlWindowsCompute v RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK pro Python v1.0.74

  + **Funkce náhledu**
    + **azureml-contrib-datová sada**
      + Po importu azureml-contrib-dataset, `Dataset.Labeled.from_json_lines` můžete `._Labeled` volat místo k vytvoření popiskované datové sady.
      + Při `to_pandas_dataframe` volání na značené datové sady s možností stahování můžete nyní určit, zda chcete přepsat existující soubory nebo ne.
      + Při `keep_columns` volání `drop_columns` nebo výsledkem časové řady, popisek nebo sloupec obrázku je vynechán, odpovídající možnosti budou vynechány pro datovou sadu také.
      + Opraveny problémy s nakladačem PyTorch při volání `dataset.to_torchvision()`.

+ **Opravy a vylepšení chyb**
  + **azurové cli-ml**
    + Do zaokreslování náhledu cli bylo přidáno profilování modelu.
    + Opravy narušující změny ve službě Azure Storage, které způsobují selhání příkazového příkazu AzureML.
    + Přidán typ provyrovnávání zatížení do MLC pro typy AKS
  + **azureml-automl-core**
    + Opraven problém s detekcí maximálního horizontu v časových řadách, které měly chybějící hodnoty a více zrn.
    + Opraven problém s chybami během generování rozdělení křížového ověřování.
    + Nahraďte tento oddíl zprávou ve formátu markdown, která se zobrazí v poznámkách k verzi: -Vylepšené zpracování krátkých zrn v datových sadách prognóz.
    + Opraven problém s maskováním některých informací o uživateli během protokolování. -Vylepšené protokolování chyb během prognostických běhů.
    + Přidání psutil jako conda závislost na automaticky generované yml nasazení souboru.
  + **azureml-contrib-mir**
    + Opravy narušující změny ve službě Azure Storage, které způsobují selhání příkazového příkazu AzureML.
  + **azureml-core**
    + Opravuje chybu, která způsobila, že modely nasazené ve funkcích Azure vytvořily 500s.
    + Byl opraven problém, kdy nebyl soubor amlignore použit na snímcích.
    + Přidáno nové rozhraní API amlcompute.get_active_runs, který vrací generátor pro spuštění a frontové spuštění na daném amlcompute.
    + Přidán typ provyrovnávání zatížení do mlc pro typy AKS.
    + Přidán parametr append_prefix bool pro download_files v run.py a download_artifacts_from_prefix v artifacts_client. Tento příznak se používá k selektivnímu sloučení cesty původu souboru, takže do output_directory je přidán pouze název souboru nebo složky
    + Opravte problém s `run_config.yml` rekonstrukcí s využitím datové sady.
    + Při `keep_columns` volání `drop_columns` nebo výsledkem je vynechánsloupec časové řady, odpovídající možnosti budou vynechány pro datovou sadu také.
  + **azureml-interpret**
    + Byla aktualizována verze interpret-community na 0.1.0.3
  + **azureml-vlak-automl**
    + Byl opraven problém, kdy automl_step nemusely tisknout problémy s ověřením.
    + Opravenregister_model úspěšné, i když v prostředí modelu chybí závislosti místně.
    + Byl opraven problém, kdy některé vzdálené spuštění nebyly povoleny dockeru.
    + Přidejte protokolování výjimky, která způsobuje předčasné selhání místního spuštění.
  + **azureml-train-core**
    + Zvažte resume_from spuštění při výpočtu automatické hyperparametrikuladění nejlepší podřízené spuštění.
  + **azureml-potrubí-jádro**
    + Zpracování pevných parametrů ve konstrukci argumentů potrubí.
    + Byl přidán popis potrubí a parametr yaml typu kroku.
    + Nový formát yaml pro krok Pipeline a přidáno upozornění na vyřazení pro starý formát.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webové prostředí

Vstupní stránka pracovního [https://ml.azure.com](https://ml.azure.com) prostoru pro spolupráci byla vylepšena a přejmenována na Studio Azure Machine Learning (preview).

Ze studia můžete trénovat, testovat, nasazovat a spravovat prostředky Azure Machine Learning, jako jsou datové sady, kanály, modely, koncové body a další.

Přístup k následujícím webovým vývojovým nástrojům ze studia:

| Webový nástroj | Popis | Edice |
|-|-|-|
| Virtuální počítač notebooku(náhled) | Plně spravovaná cloudová pracovní stanice | Základní & podnik |
| [Automatizované strojové učení](tutorial-first-experiment-automated-ml.md) (náhled) | Žádné prostředí kódu pro automatizaci vývoje modelu strojového učení | Enterprise |
| [Návrhář](concept-designer.md) (náhled) | Drag-and-drop strojového učení modelovací nástroj dříve známý jako návrhář | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Vylepšení návrháře Azure Machine Learning

+ Dříve známé jako vizuální rozhraní 
+    11 nových [modulů](algorithm-module-reference/module-reference.md) včetně doporučujících, klasifikátorů a školicích nástrojů, včetně inženýringu funkcí, křížového ověřování a transformace dat.

### <a name="r-sdk"></a>R SDK 
 
Datoví vědci a vývojáři ai používají [Azure Machine Learning SDK pro R](tutorial-1st-r-experiment.md) k vytváření a spouštění pracovních postupů strojového učení pomocí Azure Machine Learning.

Sada Azure Machine Learning SDK `reticulate` for R používá balíček k vytvoření vazby na sadu Python SDK. Vazbou přímo do Pythonu umožňuje sada SDK pro R přístup k základním objektům a metodám implementovaným v pythonu SDK z libovolného prostředí R, které zvolíte.

Mezi hlavní možnosti sady SDK patří:

+    Správa cloudových prostředků pro monitorování, protokolování a organizaci experimentů strojového učení
+    Trénování modelů pomocí cloudových prostředků, včetně školení modelu s akcelerací GPU.
+    Nasaďte své modely jako webové služby na Azure Container Instances (ACI) a Azure Kubernetes Service (AKS).

Kompletní dokumentaci naleznete na [webových stránkách balíčku.](https://azure.github.io/azureml-sdk-for-r)

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integrace Azure Machine Learning s Grid událostí 

Azure Machine Learning je teď poskytovatel prostředků pro Event Grid, můžete nakonfigurovat události strojového učení prostřednictvím portálu Azure nebo Azure CLI. Uživatelé mohou vytvářet události pro dokončení spuštění, registraci modelu, nasazení modelu a datový posun. Tyto události mohou být směrovány na obslužné rutiny událostí podporované event grid pro spotřebu. Další podrobnosti najdete [v tématu schéma](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)událostí strojového učení , [koncepty](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) a [články s kurzem.](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid)

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK pro Python v1.0.72

+ **Nové funkce**
  + Přidané datové sady monitoruje prostřednictvím [**balíčku azureml-datadrift,**](https://docs.microsoft.com/python/api/azureml-datadrift) což umožňuje sledování datových sad časových řad pro posun dat nebo jiné statistické změny v průběhu času. Výstrahy a události mohou být spuštěny, pokud je zjištěn posun nebo jsou splněny jiné podmínky na datech. Podrobnosti naleznete v [naší dokumentaci.](https://aka.ms/datadrift)
  + Oznámení dvou nových edic (označované také jako skladová položka zaměnitelně) v Azure Machine Learning. V této verzi teď můžete vytvořit pracovní prostor Basic nebo Enterprise Azure Machine Learning. Všechny existující pracovní prostory budou výchozí pro základní edici a můžete přejít na portál Azure nebo do studia a kdykoli upgradovat pracovní prostor. Pracovní prostor Basic nebo Enterprise můžete vytvořit z portálu Azure. Přečtěte si [prosím naši dokumentaci,](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) abyste se dozvěděli více. Z sady SDK lze edici pracovního prostoru určit pomocí vlastnosti "sku" objektu pracovního prostoru.
  + Také jsme provedli vylepšení Azure Machine Learning Compute – teď můžete zobrazit metriky pro vaše clustery (jako jsou celkové uzly, spuštěné uzly, celková kvóta jádra) ve službě Azure Monitor, kromě zobrazení diagnostických protokolů pro ladění. Kromě toho můžete také zobrazit aktuálně spuštěné nebo zařazené spuštěné spuštění v clusteru a podrobnosti, jako jsou IP adresy různých uzlů v clusteru. Můžete zobrazit buď na portálu nebo pomocí odpovídajícífunkce v sdk nebo CLI.

  + **Funkce náhledu**
    + Vydáváme podporu náhledu pro šifrování disku místního ssd disku v Azure Machine Learning Compute. Požádejte o lístek technické podpory, aby vaše předplatné bylo na seznamu povolených, aby tuto funkci používalo.
    + Veřejná preview dávkové inference Azure Machine Learning. Azure Machine Learning Batch Inference cílí na velké odvozené úlohy, které nejsou citlivé na čas. Dávkové odvození poskytuje nákladově efektivní odvození výpočetní škálování, s bezkonkurenční propustnost pro asynchronní aplikace. Je optimalizován pro odvození s vysokou propustností, fire-and-forget přes velké kolekce dat.
    + [**azureml-contrib-datová sada**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Povolené funkce pro datovou sadu s popiskem
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

+ **Opravy a vylepšení chyb**
  + **azurové cli-ml**
    + CLI nyní podporuje balení modelu.
    + Přidáno cli datové sady. Další informace:`az ml dataset --help`
    + Přidána podpora pro nasazení a balení podporovaných modelů (ONNX, scikit-learn a TensorFlow) bez instance InferenceConfig.
    + Přidán příznak přepsání pro nasazení služby (ACI a AKS) v sadách SDK a CLI. Pokud je k dispozici, přepíše existující službu, pokud služba s názvem již existuje. Pokud služba neexistuje, vytvoří novou službu.
    + Modely lze registrovat se dvěma novými frameworky, Onnx a Tensorflow. - Registrace modelu přijímá ukázková vstupní data, ukázková výstupní data a konfiguraci prostředků pro model.
  + **azureml-automl-core**
    + Školení iterace by spustit v podřízeném procesu pouze v případě, že jsou nastavena omezení za běhu.
    + Přidáno svodidla pro prognózování úkolů, ke kontrole, zda zadaný max_horizon způsobí problém s pamětí na daném počítači, nebo ne. Pokud ano, zobrazí se zpráva o svodidlu.
    + Přidána podpora pro komplexní frekvence, jako jsou 2 roky a 1 měsíc. -Přidána srozumitelná chybová zpráva, pokud nelze určit frekvenci.
    + Přidání výchozích hodnot azureml k automaticky generovanému conda env k vyřešení selhání nasazení modelu
    + Povolit přechodná data v Kanálu Azure Machine Learning, který `AutoMLStep`se má převést na tabulkovou datovou sadu a použít v .
    + Implementovaná aktualizace účelu sloupce pro streamování.
    + Implementovaná aktualizace parametrů transformátoru pro Imputer a HashOneHotEncoder pro streamování.
    + Do chybových zpráv ověření byla přidána aktuální velikost dat a minimální požadovaná velikost dat.
    + Byla aktualizována minimální požadovaná velikost dat pro křížové ověření, aby byly zaručeny minimálně dva vzorky v každém záhybu ověření.
  + **azureml-cli-společné**
    + CLI nyní podporuje balení modelu.
    + Modely lze registrovat se dvěma novými frameworky, Onnx a Tensorflow.
    + Registrace modelu přijímá ukázková vstupní data, ukázková výstupní data a konfiguraci prostředků pro model.
  + **azureml-contrib-gbdt**
    + opraven uvolňovací kanál pro notebook
    + Přidáno upozornění pro výpočetní cíl bez AmlCompute, který nepodporujeme
    + Přidán odhad LightGMB do balíčku azureml-contrib-gbdt
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI nyní podporuje balení modelu.
    + Přidejte upozornění na vyřazení pro zastaralá úložiště API datových sad. Viz Oznámení o změně https://aka.ms/tabular-datasetrozhraní API datové sady na adrese .
    + Změňte [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) na vrácení názvu a verze registrace, pokud je datová sada registrována.
    + Oprava chyby, kterou nelze opakovaně použít k odeslání experimentu.
    + Datové sady načtené během spuštění budou sledovány a lze je zobrazit [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) na stránce podrobností o spuštění nebo voláním po dokončení spuštění.
    + Povolit přechodná data v Kanálu Azure Machine Learning, který [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)se má převést na tabulkovou datovou sadu a použít v .
    + Přidána podpora pro nasazení a balení podporovaných modelů (ONNX, scikit-learn a TensorFlow) bez instance InferenceConfig.
    + Přidán příznak přepsání pro nasazení služby (ACI a AKS) v sadách SDK a CLI. Pokud je k dispozici, přepíše existující službu, pokud služba s názvem již existuje. Pokud služba neexistuje, vytvoří novou službu.
    +  Modely lze registrovat se dvěma novými frameworky, Onnx a Tensorflow. Registrace modelu přijímá ukázková vstupní data, ukázková výstupní data a konfiguraci prostředků pro model.
    + Přidáno nové úložiště dat pro Azure Database for MySQL. Byl přidán příklad pro použití Azure Database for MySQL v DataTransferStep v kanálech Azure Machine Learning.
    + Přidána funkce pro přidání a odebrání značek z experimentů Přidána funkce pro odstranění značek z běhů
    + Přidán příznak přepsání pro nasazení služby (ACI a AKS) v sadách SDK a CLI. Pokud je k dispozici, přepíše existující službu, pokud služba s názvem již existuje. Pokud služba neexistuje, vytvoří novou službu.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Přestěhoval `azureml-contrib-datadrift` se z do`azureml-datadrift`
    + Přidána podpora pro sledování datových souborů časových řad pro drift a další statistická opatření
    + Nové `create_from_model()` metody `create_from_dataset()` a [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) do třídy. Metoda `create()` bude zastaralá.
    + Úpravy vizualizací v Pythonu a uI ve studiu Azure Machine Learning.
    + Podporujte týdenní a měsíční plánování monitorování, kromě denního sledování datových sad.
    + Podpora zpětného vyplňování metrik monitorování dat k analýze historických dat pro monitorování datových sad.
    + Různé opravy chyb
  + [**azureml-potrubí-jádro**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep už není potřeba k odeslání kanálu Azure `yaml` Machine Learning, který běží ze souboru kanálu.
  + [**azureml-vlak-automl**](/python/api/azureml-train-automl-runtime/)
    + Přidání výchozích hodnot azureml k automaticky generovanému conda env k vyřešení selhání nasazení modelu
    + Automatické školení vzdálené ho teď zahrnuje azureml-defaults, které umožňují opakované použití trénovacího programu pro odvození.
  + **azureml-train-core**
    + Přidána podpora PyTorch [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) 1.3 v odhadu

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Vizuální rozhraní (náhled)

+ Vizuální rozhraní (preview) Azure Machine Learning bylo přepracováno tak, aby běželo na [kanálech Azure Machine Learning](concept-ml-pipelines.md). Kanály (dříve označované jako experimenty) vytvořené ve vizuálním rozhraní jsou teď plně integrované s hlavním prostředím Azure Machine Learning.
  + Jednotné zkušenosti se správou s datovými zdroji SDK
  + Správa verzí a sledování pro modely vizuálního rozhraní, kanály a koncové body
  + Přepracované uživatelské rozhraní
  + Přidané nasazení dávkového odvození
  + Přidána podpora služby Azure Kubernetes Service (AKS) pro odvozené výpočetní cíle
  + Nový pracovní postup vytváření kanálu pythonu
  + Nová [vstupní stránka](https://ml.azure.com) pro vizuální nástroje pro vytváření

+ **Nové moduly**
  + Použít operaci matematiky
  + Použít transformaci SQL
  + Hodnoty oříznutí
  + Sumarizovat data
  + Import z databáze SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK pro Python v1.0.69

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + Omezení modelu vysvětlení nejlépe spustit spíše než výpočetní vysvětlení pro každý běh. Provedení této změny chování pro místní, vzdálené a ADB.
    + Přidána podpora pro vysvětlení modelu na vyžádání pro ui
    + Přidánpsutil jako závislost `automl` a zahrnuty psutil jako conda závislost v amlcompute.
    + Opraven problém s heuristickými prodlevami a velikostmi valivých oken na datových sadách prognóz, z nichž některé řady mohou způsobit lineární chyby algebry
      + Přidán otisku pro heuristicky stanovené parametry v předpovědích.
  + **azureml-contrib-datadrift**
    + Přidána ochrana při vytváření výstupních metrik, pokud posun úrovně datové sady není v první části.
  + **azureml-contrib-interpret**
    + balíček azureml-contrib-explain-model byl přejmenován na azureml-contrib-interpret
  + **azureml-core**
    + Do zrušení registrace datových sad bylo přidáno rozhraní API. `dataset.unregister_all_versions()`
    + balíček azureml-contrib-explain-model byl přejmenován na azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Do zrušení registrace datových sad bylo přidáno rozhraní API. Dataset. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Přidáno rozhraní API datové sady pro kontrolu změněného času dat. `dataset.data_changed_time`.
    + Možnost spotřebovávat `FileDataset` `TabularDataset` a `PythonScriptStep` `EstimatorStep`jako `HyperDriveStep` vstupy do kanálu , a v Azure Machine Learning pipeline
    + `FileDataset.mount` Výkon byl vylepšen u složek s velkým počtem souborů
    + Možnost využívat [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) a [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) jako vstupy do [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)a [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) v kanálu Azure Machine Learning.
    + Výkon FileDataset. [mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) byl vylepšen pro složky s velkým počtem souborů
    + Byla přidána adresa URL ke známým chybám v podrobnostech spuštění.
    + Opravena chyba v run.get_metrics kde by požadavky selhaly, pokud by spuštění mělo příliš mnoho dětí
    + Opravena chyba v [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) kde by požadavky selhaly, pokud by spuštění mělo příliš mnoho dětí
    + Přidána podpora ověřování v clusteru Arcadia.
    + Vytvoření objektu Experiment získá nebo vytvoří experiment v pracovním prostoru Azure Machine Learning pro sledování historie spuštění. ID experimentu a archivovaný čas jsou naplněny v objektu Experiment při vytváření. Příklad: experiment = Experiment (pracovní prostor, "Nový experiment") experiment_id = experiment.id archive() a reactivate() jsou funkce, které lze volat na experiment skrýt a obnovit experiment z zobrazeného v uživatelském jazyku nebo vráceny ve výchozím nastavení v experimentech volání do seznamu. Pokud je nový experiment vytvořen se stejným názvem jako archivovaný experiment, můžete archivovaný experiment přejmenovat při opětovné aktivaci předáním nového názvu. Může existovat pouze jeden aktivní experiment s daným názvem. Příklad: experiment1 = Experiment(pracovní prostor, "Aktivní experiment") experiment1.archive() # Vytvořte nový aktivní experiment se stejným názvem jako archivovaný. experiment2. = Experiment (pracovní prostor, "Aktivní experiment") experiment1.reactivate (new_name="Předchozí aktivní experiment") Statický seznam metod () v experimentu může mít filtr názvů a filtr ViewType. Hodnoty ViewType jsou "ACTIVE_ONLY", "ARCHIVED_ONLY" a "ALL"Příklad: archived_experiments = Experiment.list(pracovní prostor, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(pracovní prostor, name="První experiment", view_type="ALL")
    + Podpora pomocí prostředí pro nasazení modelu a aktualizace služby
  + **azureml-datadrift**
    + Atribut show třídy DataDriftDector už nepodporuje volitelný argument "with_details". Atribut show bude prezentovat pouze koeficient posunu dat a příspěvek posunu dat sloupců prvků.
    + Změny chování atributu DataDriftDetector "get_output":
      + Vstupní parametr start_time, end_time jsou volitelné místo povinné;
      + Vstupní specifické start_time a/nebo end_time s konkrétní run_id ve stejném vyvolání způsobí výjimku chyby hodnoty, protože se vzájemně vylučují
      + Podle konkrétních start_time a/nebo end_time bude vráceny pouze výsledky plánovaných spuštění;
      + Parametr "daily_latest_only" se zastaral.
    + Podpora načítání výstupů datového posunu založeného na datové sadě.
  + **azureml-explain-model**
    + Přejmenuje balíček AzureML-explain-model na AzureML-interpret, takže starý balíček pro zpětnou kompatibilitu pro tuto chvíli
    + opravena `automl` chyba s nezpracovanými vysvětleními nastavenými na úlohu klasifikace namísto regrese ve výchozím nastavení při stahování z explanationclientu
    + Přidat podporu `ScoringExplainer` pro vytvoření přímo pomocí`MimicWrapper`
  + **azureml-potrubí-jádro**
    + Zlepšený výkon pro vytváření velkých kanálů
  + **azureml-train-core**
    + Přidána podpora TensorFlow 2.0 v TensorFlow Estimator
  + **azureml-vlak-automl**
    + Vytvoření [objektu Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) získá nebo vytvoří experiment v pracovním prostoru Azure Machine Learning pro sledování historie spuštění. ID experimentu a archivovaný čas jsou naplněny v objektu Experiment při vytváření. Příklad:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) a [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) jsou funkce, které lze například vyvolat při experimentu, který má skrýt a obnovit zobrazení experimentu v uživatelském jazyku nebo vrácený ve výchozím nastavení ve volání na seznam experimentů. Pokud je nový experiment vytvořen se stejným názvem jako archivovaný experiment, můžete archivovaný experiment přejmenovat při opětovné aktivaci předáním nového názvu. Může existovat pouze jeden aktivní experiment s daným názvem. Příklad:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Statický [seznam metod()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) v experimentu může mít filtr názvů a filtr ViewType. Hodnoty ViewType jsou "ACTIVE_ONLY", "ARCHIVED_ONLY" a "ALL". Příklad:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Podpora pomocí prostředí pro nasazení modelu a aktualizace služby.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Atribut show třídy [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) už nepodporuje volitelný argument "with_details". Atribut show bude prezentovat pouze koeficient posunu dat a příspěvek posunu dat sloupců prvků.
    + DataDriftDetector funkce [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) změny chování:
      + Vstupní parametr start_time, end_time jsou volitelné místo povinné;
      + Vstupní konkrétní start_time a/nebo end_time s konkrétním run_id ve stejném vyvolání bude mít za následek výjimku chyby hodnoty, protože se vzájemně vylučují;
      + Podle konkrétních start_time a/nebo end_time bude vráceny pouze výsledky plánovaných spuštění;
      + Parametr "daily_latest_only" se zastaral.
    + Podpora načítání výstupů datového posunu založeného na datové sadě.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Přejmenuje balíček AzureML-explain-model na AzureML-interpret, takže starý balíček pro zpětnou kompatibilitu pro tuto chvíli.
    + opravena chyba AutoML s nezpracovanými vysvětleními nastavenými na úlohu klasifikace namísto regrese ve výchozím nastavení při stahování z explanationclientu.
    + Přidat podporu pro [ScoringExplainer,](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) které mají být vytvořeny přímo pomocí [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-potrubí-jádro](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Zlepšený výkon pro vytváření velkých kanálů.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Přidána podpora TensorFlow 2.0 v [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-vlak-automl](/python/api/azureml-train-automl-runtime/)**
    + Nadřazené spuštění již nebude sem po selhání iterace instalace, protože orchestrace se o něj již postará.
    + Přidána podpora místního dockeru a místního conda pro experimenty AutoML
    + Přidána podpora místního dockeru a místního conda pro experimenty AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nové webové prostředí (preview) pro pracovní prostory Azure Machine Learning

Karta Experiment na [novém portálu pracovního prostoru](https://ml.azure.com) byla aktualizována, aby vědci mohli experimenty sledovat výkonnějším způsobem. Můžete prozkoumat následující funkce:
+ Experimentujte metadata pro snadné filtrování a řazení seznamu experimentů
+ Zjednodušené a výkonné stránky s podrobnostmi o experimentu, které vám umožní vizualizovat a porovnávat vaše běhy
+ Nový návrh pro spuštění stránek podrobností, abyste pochopili a sledovali vaše tréninkové běhy

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK pro Python v1.0.65

  + **Nové funkce**
    + Přidáno kurátorské prostředí. Tato prostředí byla předem nakonfigurována s knihovnami pro běžné úlohy strojového učení a byla předbudována a uložena do mezipaměti jako image Dockeru pro rychlejší spuštění. Ve výchozím nastavení se zobrazují v seznamu prostředí pracovního prostoru s předponou "AzureML".
    + Přidáno kurátorské prostředí. Tato prostředí byla předem nakonfigurována s knihovnami pro běžné úlohy strojového učení a byla předbudována a uložena do mezipaměti jako image Dockeru pro rychlejší spuštění. Ve výchozím nastavení se zobrazují v seznamu prostředí [pracovního prostoru](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)s předponou "AzureML".

  + **azureml-vlak-automl**
  + **[azureml-vlak-automl](/python/api/azureml-train-automl-runtime/)**
    + Přidána podpora konverze ONNX pro ADB a HDI

+ **Funkce náhledu**
  + **azureml-vlak-automl**
  + **[azureml-vlak-automl](/python/api/azureml-train-automl-runtime/)**
    + Podporované BERT a BiLSTM jako text featurizer (pouze náhled)
    + Podporované přizpůsobení featurization pro parametry sloupce a transformátoru (pouze náhled)
    + Podporované nezpracovaná vysvětlení, když uživatel povolí vysvětlení modelu během trénování (pouze náhled)
    + Přidánprorok `timeseries` pro prognózování jako trénovatelný potrubí (pouze náhled)

  + **azureml-contrib-datadrift**
    + Balíčky přemístěné z azureml-contrib-datadrift na azureml-datadrift; balíček `contrib` bude odstraněn v budoucí verzi

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + Představený FeaturizationConfig do automatických konfigurace a automatického nastavení MLBaseSettings
    + Představený FeaturizationConfig do [automatických konfigurace a](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) automatického nastavení MLBaseSettings
      + Přepsat účel sloupce pro featurizaci s daným sloupcem a typem prvku
      + Přepsat parametry transformátoru
    + Přidána zpráva o vyřazení pro explain_model() a retrieve_model_explanations()
    + Přidáno Prorok jako trénovatelný potrubí (pouze náhled)
    + Přidána zpráva o vyřazení pro explain_model() a retrieve_model_explanations().
    + Přidán prorok jako trénovatelný kanál (pouze náhled).
    + Přidána podpora pro automatickou detekci cílových prodlev, velikost i maximální horizont oken. Pokud je jedna z target_lags, target_rolling_window_size nebo max_horizon nastavena na "auto", heuristiky se použijí k odhadu hodnoty odpovídajícího parametru na základě trénovacích dat.
    + Opravena prognóza v případě, kdy datová sada obsahuje jeden sloupec zrnitosti, toto zrnitost je číselného typu a mezi vlakovou a testovací sadou je mezera
    + Opravena chybová zpráva o duplicitním indexu ve vzdáleném spuštění v úlohách prognózy
    + Opravena prognóza v případě, kdy datová sada obsahuje jeden sloupec zrnitosti, toto zrnitost je číselného typu a mezi trainem a testovací sadou je mezera.
    + Opravena chybová zpráva o duplicitním indexu ve vzdáleném spuštění v úlohách prognózy.
    + Přidáno svodidla pro kontrolu, zda je datová sada nevyvážená nebo ne. Pokud ano, zpráva o svodidla by byla zapsána do konzole.
  + **azureml-core**
    + Přidána možnost načíst adresu URL SAS do modelu v úložišti prostřednictvím objektu modelu. Ex: model.get_sas_url()
    + Zavést `run.get_details()['datasets']` získat datové sady spojené s odeslané spuštění
    + Přidejte `Dataset.Tabular.from_json_lines_files` rozhraní API pro vytvoření tabulardataset ze souborů JSON Lines. Chcete-li se dozvědět o těchto tabulkových datech https://aka.ms/azureml-data v souborech JSON Lines na TabularDataset, navštivte dokumentaci.
    + Přidána další pole velikosti virtuálního počítače (disk operačního systému, počet gpu) do funkce supported_vmsizes ()
    + Přidána další pole do funkce list_nodes () pro zobrazení spuštění, soukromé a veřejné IP adresy, portu atd.
    + Možnost zadat nové pole během zřizování clusteru -- remotelogin_port_public_access, které lze nastavit na povoleno nebo zakázáno v závislosti na tom, zda chcete ponechat port SSH otevřený nebo zavřený v době vytvoření clusteru. Pokud jej nezadáte, služba bude chytře otevřít nebo zavřít port v závislosti na tom, zda nasazujete cluster u virtuální sítě.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Přidána možnost načíst adresu URL SAS do modelu v úložišti prostřednictvím objektu modelu. Ex: model. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Zavést běh. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datové sady'] získat datové sady spojené s odeslaným spuštěním
    + Přidat `Dataset.Tabular`rozhraní API . [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) k vytvoření Tabulárnídatové sady ze souborů JSON Lines. Chcete-li se dozvědět o těchto tabulkových datech https://aka.ms/azureml-data v souborech JSON Lines na TabularDataset, navštivte dokumentaci.
    + Přidána další pole velikosti virtuálního počítače (disk operačního systému, počet GPU) do funkce [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Přidána další pole do funkce [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) pro zobrazení spuštění, soukromé a veřejné IP adresy, portu atd.
    + Možnost zadat nové pole během [zřizování clusteru,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` které lze nastavit na povoleno nebo zakázáno v závislosti na tom, zda chcete ponechat port SSH otevřený nebo zavřený v době vytvoření clusteru. Pokud jej nezadáte, služba bude chytře otevřít nebo zavřít port v závislosti na tom, zda nasazujete cluster u virtuální sítě.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Vylepšená dokumentace pro výstupy vysvětlení ve scénáři klasifikace.
    + Přidána možnost nahrát předpokládané hodnoty y na vysvětlení příkladů hodnocení. Odemkne užitečnější vizualizace.
    + Přidána vlastnost explainer do MimicWrapper, aby bylo možné získat základní MimicExplainer.
  + **azureml-potrubí-jádro**
    + Přidán notebook popisující modul, modulVersion a ModuleStep
  + **azureml-pipeline-steps**
    + Přidán rscriptstep pro podporu skriptu R spuštěného prostřednictvím kanálu AML.
    + Opravena analýza parametrů metadat v AzureBatchStep, což způsobilo chybovou zprávu "přiřazení pro parametr SubscriptionId není zadáno."
  + **azureml-vlak-automl**
    + Podporované training_data, validation_data, label_column_name weight_column_name jako formát pro zadávání dat
    + Přidána zpráva o vyřazení pro explain_model() a retrieve_model_explanations()
  + **[azureml-potrubí-jádro](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Přidán [poznámkový blok](https://aka.ms/pl-modulestep) popisující [modul](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) a [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Přidán [rscriptstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) pro podporu skriptu R spuštěného prostřednictvím kanálu AML.
    + Opravena analýza parametrů metadat v [AzureBatchStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) což způsobilo chybovou zprávu "přiřazení pro parametr SubscriptionId není zadán".
  + **[azureml-vlak-automl](/python/api/azureml-train-automl-runtime/)**
    + Jako formát pro zadávání dat weight_column_name podporované training_data, validation_data, label_column_name weight_column_name.
    + Přidána zpráva o vyřazení pro [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) a [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK pro Python v1.0.62

+ **Nové funkce**
  + Představil `timeseries` vlastnost na TabularDataset. Tato vlastnost umožňuje snadné filtrování časových razítek na datech TabularDataset, jako je například převzetí všech dat mezi rozsahem času nebo nejnovějšími daty. Chcete-li se `timeseries` o tom dozvědět vlastnost https://aka.ms/azureml-data na TabularDataset, navštivte dokumentaci nebo https://aka.ms/azureml-tsd-notebook příklad poznámkového bloku.
  + Povolené školení s TabularDataset a FileDataset. https://aka.ms/dataset-tutorial Navštivte prosím ukázkový poznámkový blok.

  + **azureml-train-core**
      + `Nccl` Přidána `Gloo` a podpora v pytorchovém odhadu

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + Zastaralé nastavení AutoML 'lag_length' a LaggingTransformer.
    + Opraveno správné ověření vstupních dat, pokud jsou zadána ve formátu Toku dat
    + Upraveno fit_pipeline.py generovat graf json a nahrát na artefakty.
    + Vykreslen graf pod `userrun` `Cytoscape`pomocí .
  + **azureml-core**
    + Znovu se vrátil i zpracování výjimek v kódu ADB a provést změny podle nového zpracování chyb
    + Přidáno automatické ověřování MSI pro virtuální počítače s notebooky.
    + Opravuje chybu, kde by mohly být nahrány poškozené nebo prázdné modely z důvodu neúspěšných opakování.
    + Opravena chyba, `DataReference` kdy `DataReference` se změnil název při změně `as_upload` `as_download`režimu `as_mount`(např. při volání , nebo ).
    + Make `mount_point` `target_path` a `FileDataset.mount` volitelné `FileDataset.download`pro a .
    + Výjimka, že sloupec časového razítka nelze nalézt, bude vyvolána, pokud je čas seriály související rozhraní API volána bez sloupce jemného časového razítka nebo jsou vynechány přiřazené sloupce časového razítka.
    + Sloupce časových seriálů by měly být přiřazeny ke sloupci, jehož typ je Datum, jinak se očekává výjimka.
    + Sloupce časových seriálů přiřazující rozhraní API with_timestamp_columns mohou trvat název sloupce žádné hodnoty fine/coarse, což vymaže dříve přiřazené sloupce časových razítek.
    + Výjimka bude vyvolána, když je vynechán buď hrubý zrno nebo jemně odstupňovaný sloupec časového razítka s indikací pro uživatele, že uvolnění lze provést buď po vyloučení sloupce časového razítka v seznamu uvolnění nebo volání with_time_stamp s hodnotou None pro uvolnění sloupců časového razítka
    + Výjimka bude vyvolána, pokud buď hrubé zrno nebo jemně odstupňované sloupec časového razítka není zahrnuta v seznamu zachovat sloupce s označením pro uživatele, že zachování lze provést buď po zahrnutí sloupce časového razítka v seznamu zachovat sloupec nebo volání with_time_stamp s žádnou hodnotou uvolnit sloupce časového razítka.
    + Přidáno protokolování pro velikost registrovaného modelu.
  + **azureml-explain-model**
    + Opraveno upozornění vytištěné na konzoli, když není nainstalován balíček pythonu "packaging": "Pomocí starší než podporované verze lightgbm upgradujte na verzi větší než 2.2.1"
    + Opraveno vysvětlení modelu stahování s rážováním pro globální vysvětlení s mnoha funkcemi
    + Opravena mimika explainer chybí inicializace příklady na výstupu vysvětlení
    + Opravena neměnná chyba na nastavených vlastnostech při nahrávání s vysvětlujícím klientem pomocí dvou různých typů modelů
    + Přidána get_raw param k vysvětlivce .explainer .explain(), takže jeden vysvětlivací bodovací příkaz může vrátit inženýrské i nezpracované hodnoty.
  + **azureml-vlak-automl**
    + Zavedená veřejná api od AutoML pro `automl` podpůrná vysvětlení z vysvětlení Sady SDK – novější způsob podpory vysvětlení automatického ml oddělením automatického ml featurization a vysvětlení sdk – integrovaná podpora nezpracovaných vysvětlení z azureml vysvětlujíspozministryně SDK pro modely AutoML.
    + Odebrání výchozích hodnot azureml ze vzdálených školicích prostředí.
    + Změněno výchozí umístění úložiště mezipaměti z FileCacheStore založené na AzureFileCacheStore jeden pro AutoML na cestě kódu Azure Databricks.
    + Opraveno správné ověření vstupních dat, pokud jsou zadána ve formátu Toku dat
  + **azureml-train-core**
    + Vrátit source_directory_data_store odsuzování.
    + Přidána možnost přepsat verze nainstalovaných balíčků azureml.
    + Přidána podpora `environment_definition` dockerfile v parametru v odhadech.
    + Zjednodušené distribuované parametry školení v odhadech.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nové webové prostředí (preview) pro pracovní prostory Azure Machine Learning
Nové webové prostředí umožňuje datovým vědcům a datovým inženýrům dokončit jejich životní cyklus strojového učení od přípravy a vizualizace dat až po školení a nasazení modelů na jednom místě.

![Ui pracovního prostoru Azure Machine Learning (preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Klíčové vlastnosti:**

Pomocí tohoto nového rozhraní Azure Machine Learning teď můžete:
+ Správa poznámkových bloků nebo propojení s Jupyterem
+ [Spuštění automatizovaných experimentů ML](tutorial-first-experiment-automated-ml.md)
+ [Vytváření datových sad z místních souborů, datových úložišť & webových souborů](how-to-create-register-datasets.md)
+ Prozkoumejte & připravit datové sady pro vytvoření modelu
+ Sledování posunu dat pro vaše modely
+ Zobrazení posledních zdrojů z řídicího panelu

V době vydání této verze jsou podporovány následující prohlížeče: Chrome, Firefox, Safari a Microsoft Edge Preview.

**Známé problémy:**

1. Aktualizujte svůj prohlížeč, pokud vidíte "Něco se pokazilo! Při probíhajícím nasazení došlo k chybě při načítání souborů bloků dat.

1. V poznámkových blocích a souborech nelze soubor odstranit ani přejmenovat. Během public preview můžete použít Jupyter UI nebo terminál u notebooku ve virtuálním počítači notebooku k provádění operací aktualizačních souborů. Vzhledem k tomu, že se jedná o připojený síťový systém souborů, všechny změny provedené na virtuálním počítači poznámkového bloku se okamžitě projeví v pracovním prostoru poznámkového bloku.

1. Chcete-li SSH do virtuálního počítači notebooku:
   1. Najděte klíče SSH, které byly vytvořeny během nastavení virtuálního počítače. Nebo najděte klíče v pracovním prostoru Azure Machine Learning > otevřete kartu Výpočetní > vyhledejte virtuální počítač poznámkového bloku v seznamu > otevřete jeho vlastnosti : zkopírujte klíče z dialogového okna.
   1. Importujte tyto veřejné a soukromé klíče SSH do místního počítače.
   1. Použijte je k SSH do virtuálního počítači notebooku.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK pro Python v1.0.60

+ **Nové funkce**
  + Zavedena FileDataset, která odkazuje na jeden nebo více souborů ve vašem datastores nebo veřejné adresy URL. Soubory mohou mít libovolný formát. FileDataset vám poskytuje možnost stahovat nebo připojovat soubory k výpočtu. Chcete-li se dozvědět https://aka.ms/file-datasetvíce o FileDataset, navštivte .
  + Přidána podpora kanálu Yaml pro krok PythonScript, Krok Adla, Krok Databricks, DataTransferStep a AzureBatch Step

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + AutoArima je nyní navrhovaný kanál pouze pro náhled.
    + Vylepšené hlášení chyb pro prognózování.
    + Protokolování bylo vylepšeno pomocí vlastních výjimek namísto obecných v úlohách prognózy.
    + Byla odebrána kontrola max_concurrent_iterations být menší než celkový počet iterací.
    + Modely automatického mlsu nyní vracejí výjimky automatického mld
    + Tato verze zlepšuje výkon provádění místních spuštění automatizovaného strojového učení.
  + **azureml-core**
    + Zavést Dataset.get_all(pracovní prostor), který `TabularDataset` vrátí `FileDataset` slovník a objekty zaklíčové jejich název registrace.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Zavést `parition_format` jako `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`argument a . Informace o oddílu každé datové cesty budou extrahovány do sloupců na základě zadaného formátu. '{column_name}' vytvoří sloupec řetězce a '{column_name:yyyy/MM/dd/HH/mm/ss}' vytvoří sloupec datetime, kde 'yyy', 'MM', 'dd', 'HH', 'mm' a 'ss' se používají k extrahování rok, měsíc, den, hodina, minuta a druhý pro typ datetime. Partition_format by měla začínat od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu '.. /USA/2019/01/01/data.csv' kde oddíl je podle země a času, partition_format='/{Země}/{PartitionDate:yyyy/MM/dd}/data.csv' vytvoří řetězec sloupec 'Země' s hodnotou 'USA' a datetime sloupec 'PartitionDate' s hodnotou '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Zavést `partition_format` jako `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`argument a . Informace o oddílu každé datové cesty budou extrahovány do sloupců na základě zadaného formátu. '{column_name}' vytvoří sloupec řetězce a '{column_name:yyyy/MM/dd/HH/mm/ss}' vytvoří sloupec datetime, kde 'yyy', 'MM', 'dd', 'HH', 'mm' a 'ss' se používají k extrahování rok, měsíc, den, hodina, minuta a druhý pro typ datetime. Partition_format by měla začínat od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu '.. /USA/2019/01/01/data.csv' kde oddíl je podle země a času, partition_format='/{Země}/{PartitionDate:yyyy/MM/dd}/data.csv' vytvoří řetězec sloupec 'Země' s hodnotou 'USA' a datetime sloupec 'PartitionDate' s hodnotou '2019-01-01'.
    + `to_csv_files`a `to_parquet_files` metody byly `TabularDataset`přidány do . Tyto metody umožňují `TabularDataset` převod `FileDataset` mezi a a a převodem dat do souborů zadaného formátu.
    + Automaticky se přihlašovat do registru základní image při ukládání dockerfile generované Model.package().
    + "gpu_support" již není nutné; AML nyní automaticky detekuje a používá rozšíření nvidia docker, když je k dispozici. Bude odstraněn v budoucí verzi.
    + Přidána podpora pro vytváření, aktualizaci a používání PipelineDrafts.
    + Tato verze zlepšuje výkon provádění místních spuštění automatizovaného strojového učení.
    + Uživatelé mohou dotazovat metriky z historie spuštění podle názvu.
    + Protokolování bylo vylepšeno pomocí vlastních výjimek namísto obecných v úlohách prognózy.
  + **azureml-explain-model**
    + Do nového mimikovata, což uživatelům umožňuje získat vysvětlení nezpracovaných funkcí, byl přidán feature_maps parametr.
    + Nahrávání datových sad je nyní ve výchozím nastavení vypnuto pro odeslání vysvětlení a může být znovu povoleno s upload_datasets=True
    + Přidány parametry filtrování "is_law" do seznamu vysvětlení a funkcí stahování.
    + Přidá `get_raw_explanation(feature_maps)` metodu do globálních i místních vysvětlení objektů.
    + Přidána kontrola verze pro lightgbm s vytištěným upozorněním, pokud je níže podporovaná verze
    + Optimalizované využití paměti při dávkování vysvětlení
    + Modely automatického mlsu nyní vracejí výjimky automatického mld
  + **azureml-potrubí-jádro**
    + Přidána podpora pro vytváření, aktualizaci a používání PipelineDrafts – lze použít k udržování proměnlivých definic kanálu a jejich interaktivním spuštění
  + **azureml-vlak-automl**
    + Vytvořeno funkce pro instalaci specifických verzí gpu-schopný chrlič :::no-loc text="cuda"::: pytorch v1.1.0, toolkit 9.0, pytorch-transformers, který je nutný k povolení BERT / XLNet ve vzdáleném prostředí python runtime.
  + **azureml-train-core**
    + Předčasné selhání některých chyb definice hyperparametrické oblasti přímo v sdk namísto na straně serveru.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Opravy a vylepšení chyb**
  + Povoleno zápisu do ADLS/ADLSGen2 pomocí nezpracovaná cesta a pověření.
  + Opravena chyba, `include_path=True` která způsobovala, že nefungovala pro `read_parquet`.
  + Opravena `to_pandas_dataframe()` chyba způsobená výjimkou "Neplatná hodnota vlastnosti: hostSecret".
  + Opravena chyba, kdy nebylo možné číst soubory na DBFS v režimu Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK pro Python v1.0.57
+ **Nové funkce**
  + Povoleno `TabularDataset` spotřebovávat AutomatedML. Chcete-li `TabularDataset`se dozvědět více o , navštivte https://aka.ms/azureml/howto/createdatasets.

+ **Opravy a vylepšení chyb**
  + **klient automl-client-core-nativeclient**
    + Opravena chyba, která byla vyvolána, když jsou k dispozici trénovací a/nebo ověřovací štítky (y a y_valid) ve formě datového rámce pand, ale ne jako numpy array.
    + Aktualizované rozhraní vytvořit `RawDataContext` pouze vyžadovat data `AutoMLBaseSettings` a objekt.
    +  Povolit uživatelům automatického mlsu přetáhnout tréninkové řady, které nejsou dostatečně dlouhé při prognózování. - Povolit uživatelům Automatické ho mlsu vypustit zrna z testovací sady, která neexistuje v sadě školení při prognózování.
  + **azurové cli-ml**
    + Nyní můžete aktualizovat certifikát TLS/SSL pro koncový bod vyhodnocování nasazený v clusteru AKS pro certifikát generovaný společností Microsoft i pro certifikát zákazníka.
  + **azureml-automl-core**
    + Byl opraven problém v automatické míchané, kde řádky s chybějícími popisky nebyly správně odebrány.
    + Vylepšeno protokolování chyb v automatické mlze; úplné chybové zprávy budou nyní vždy zapsány do souboru protokolu.
    + Aplikace AutoML aktualizovala připnutí `azureml-explain-model`balíčku `azureml-dataprep`tak, aby zahrnovalo `azureml-defaults`, a . AutoML již nebude upozorňováno na `azureml-train-automl` neshody balíčků (s výjimkou balíčku).
    + Byl opraven `timeseries` problém, kdy rozdělení cv mají nestejnou velikost, což způsobuje selhání výpočtu přihrádky.
    + Při spuštění iterace souboru pro typ školení křížového ověření, pokud jsme skončili se stahováním modelů trénovaných na celé datové sadě, měli jsme nesoulad mezi vahy modelu a modely, které byly přiváděny do hlasovacího souboru.
    + Opravena chyba, která byla vyvolána, když jsou k dispozici trénovací a/nebo ověřovací štítky (y a y_valid) ve formě datového rámce pand, ale ne jako numpy array.
    + Opraven problém s úlohami prognózy, když bylo v logických sloupcích vstupních tabulek zjištěno žádné.
    + Povolit uživatelům automatického mlsu přetáhnout tréninkové řady, které nejsou dostatečně dlouhé při prognózování. - Povolit uživatelům Automatické ho mlsu vypustit zrna z testovací sady, která neexistuje v sadě školení při prognózování.
  + **azureml-core**
    + Opraven problém s řazením parametrů blob_cache_timeout.
    + Přidány externí typy uložení a transformace typů výjimek na systémové chyby.
    + Přidána podpora tajných kódů trezoru klíčů pro vzdálené spuštění. Přidejte třídu azureml.core.keyvault.Keyvault, která přidá, získá a zobrazí tajné kódy z trezoru keyvault přidruženého k vašemu pracovnímu prostoru. Podporované operace jsou:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret (název, hodnota)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(název)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Další metody pro získání výchozí keyvault a získat tajné klíče během vzdáleného spuštění:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(název)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Přidány další parametry přepsání pro příkaz příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu
    + Zlepšit spolehlivost volání rozhraní API se rozšiřuje opakování na běžné výjimky knihovny požadavků.
    + Přidejte podporu pro odesílání spuštění z odeslané spuštění.
    + Opravena expirace problému tokenu SAS v FileWatcher, který způsobil, že soubory přestaly být nahrávány po vypršení platnosti počátečního tokenu.
    + Podporovaný import souborů HTTP csv/tsv v datové sadě Python SDK.
    + Zastaralá metoda Workspace.setup(). Varovná zpráva zobrazená uživatelům navrhuje místo toho použít create(nebo get()/from_config().
    + Added Environment.add_private_pip_wheel(), který umožňuje nahrávání `whl`soukromých vlastních balíčků pythonu do pracovního prostoru a jejich bezpečné použití k vytváření/zhmotnění prostředí.
    + Nyní můžete aktualizovat certifikát TLS/SSL pro koncový bod vyhodnocování nasazený v clusteru AKS pro certifikát generovaný společností Microsoft i pro certifikát zákazníka.
  + **azureml-explain-model**
    + Přidán parametr pro přidání ID modelu k vysvětlení při nahrávání.
    + Přidáno `is_raw` označování vysvětlení v paměti a nahrávání.
    + Přidána podpora pytorch a testy pro balíček azureml-explain-model.
  + **azureml-opendatasets**
    + Podpora detekce a protokolování automatického testovacího prostředí.
    + Přidány třídy, aby si americké populace podle kraje a zip.
  + **azureml-potrubí-jádro**
    + Přidána vlastnost label pro definice vstupního a výstupního portu.
  + **azureml-telemetrie**
    + Opravena nesprávná konfigurace telemetrie.
  + **azureml-vlak-automl**
    + Opravena chyba, kdy při selhání nastavení nebyla chyba přihlášena do pole "chyby" pro spuštění nastavení, a proto nebyla uložena v nadřazeném spuštění "chyby".
    + Byl opraven problém v automatické míchané, kde řádky s chybějícími popisky nebyly správně odebrány.
    + Povolit uživatelům automatického mlsu přetáhnout tréninkové řady, které nejsou dostatečně dlouhé při prognózování.
    + Povolit uživatelům automatického mlsu vypustit zrna z testovací sady, které neexistují v sadě školení při prognózování.
    + Nyní AutoMLStep `automl` prochází config na back-end, aby se zabránilo problémům na změny nebo dodatky nových parametrů konfigurace.
    + AutoML Datové svodidla je nyní ve verzi Public Preview. Uživateli se po trénování zobrazí sestava Svozíčkem dat (pro úlohy klasifikace/regrese) a také k ní bude mít přístup prostřednictvím rozhraní SDK API.
  + **azureml-train-core**
    + Přidána podpora hořáku 1.2 v PyTorch Estimator.
  + **azureml-widgety**
    + Vylepšené maticové grafy záměny pro klasifikaci.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Nové funkce**
  + Seznamy řetězců lze nyní předat jako `read_*` vstup metodám.

+ **Opravy a vylepšení chyb**
  + Výkon `read_parquet` programu se výrazně zlepšil při spuštění ve Sparku.
  + Byl opraven `column_type_builder` problém, kdy se nezdařilv případě jednoho sloupce s nejednoznačnými formáty kalendářních dat.

### <a name="azure-portal"></a>portál Azure
+ **Funkce náhledu**
  + Datový proud protokolu a výstupního souboru je nyní k dispozici pro stránky podrobností o spuštění. Soubory budou streamovat aktualizace v reálném čase, když je zapnutý přepínač náhledu.
  + Možnost nastavit kvótu na úrovni pracovního prostoru je vydána ve verzi Preview. AmlCompute kvóty jsou přiděleny na úrovni předplatného, ale nyní umožňují distribuovat tuto kvótu mezi pracovní prostory a přidělit ji pro spravedlivé sdílení a zásadsprávné ho. Stačí kliknout na okno **Použití a kvóty** na levém navigačním panelu pracovního prostoru a vybrat kartu **Konfigurovat kvóty.**

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK pro Python v1.0.55

+ **Nové funkce**
  + Ověřování založené na tokenech je nyní podporováno pro volání ke koncovému bodu vyhodnocování nasazenému v AKS. Budeme i nadále podporovat aktuální ověřování na základě klíče a uživatelé mohou používat jeden z těchto mechanismů ověřování najednou.
  + Možnost zaregistrovat úložiště objektů blob, které je za virtuální síť (VNet) jako úložiště dat.

+ **Opravy a vylepšení chyb**
  + **azureml-automl-core**
    + Opravuje chybu, kdy velikost ověření pro rozdělení životopisu je malá a výsledkem je chybné předpovídané vs. skutečné grafy pro regresi a prognózování.
    + Protokolování úloh prognózy ve vzdálených spuštěních bylo vylepšeno, nyní je uživateli poskytnuta komplexní chybová zpráva, pokud se spuštění nezdařilo.
    + Opraveny chyby, `Timeseries` pokud je příznak předzpracování true.
    + Některé chybové zprávy o ověření dat prognózy byly akcenovatelnější.
    + Snížená spotřeba paměti automatického ml spouštění přetažením a/nebo opožděné načítání datových sad, zejména mezi procesem paws
  + **azureml-contrib-explain-model**
    + Přidán příznak model_task pro explainery, který umožňuje uživateli přepsat výchozí logiku automatického odvození pro typ modelu
    + Widget změny: Automaticky `contrib`nainstaluje `nbextension` s , žádné další instalaci / povolit - podpora vysvětlení jen globální funkce význam (např Permutative)
    + Dashboard změny: - Box obrázky a housle `beeswarm` pozemky kromě obrázku na `beeswarm` souhrnné stránce - Mnohem rychlejší rerendering pozemku na 'Top -k' posuvník změna - užitečná zpráva vysvětlující, jak top-k je počítán - Užitečné přizpůsobitelné zprávy místo grafů, když data nejsou k dispozici
  + **azureml-core**
    + Přidána metoda Model.package() k vytvoření imitek Docker u Dockerfiles, které zapouzdřují modely a jejich závislosti.
    + Byly aktualizovány místní webové služby, aby přijímalo inferenceConfigs obsahující objekty prostředí.
    + Opraven model.register() vyrábějící neplatné modely, když '. (pro aktuální adresář) je předán jako model_path parametr.
    + Přidat Run.submit_child, funkce zrcadlí Experiment.submit při zadávání spustit jako nadřazený odeslaného podřízeného spuštění.
    + Podpora možností konfigurace z Model.register v Souboru Run.register_model.
    + Možnost spouštět úlohy JAR v existujícím clusteru.
    + Nyní podporuje instance_pool_id a cluster_log_dbfs_path parametry.
    + Přidána podpora pro použití objektu prostředí při nasazování modelu do webové služby. Objekt prostředí lze nyní poskytnout jako součást inferenceConfig objektu.
    + Přidat appinsifht mapování pro nové oblasti - centralus - westus - northcentralus
    + Přidána dokumentace pro všechny atributy ve všech třídách Datastore.
    + Byl přidán `Datastore.register_azure_blob_container`parametr blob_cache_timeout do .
    + Přidány metody save_to_directory a load_from_directory na azureml.core.environment.Environment.
    + Do rozhraní příkazu CLI byly přidány příkazy "az ml environment download" a "az ml environment register".
    + Přidána metoda Environment.add_private_pip_wheel.
  + **azureml-explain-model**
    + Přidáno sledování datové sady do vysvětlení pomocí služby Dataset (náhled).
    + Při streamování globálních vysvětlení se snížila výchozí velikost dávky z 10 000 až 100.
    + Přidán příznak model_task pro explainery, aby uživatel mohl přepsat výchozí logiku automatického odvození pro typ modelu.
  + **azureml-mlflow**
    + Opravena chyba v mlflow.azureml.build_image kde jsou vnořené adresáře ignorovány.
  + **azureml-pipeline-steps**
    + Přidána možnost spouštět úlohy JAR v existujícím clusteru Azure Databricks.
    + Přidána podpora instance_pool_id a cluster_log_dbfs_path parametry pro Krok DatabricksStep.
    + Přidána podpora parametrů kanálu v kroku DatabricksStep.
  + **azureml-vlak-automl**
    + Přidáno `docstrings` pro soubory související se souborem.
    + Byly aktualizovány dokumenty na `max_cores_per_iteration` vhodnější jazyk pro`max_concurrent_iterations`
    + Protokolování úloh prognózy ve vzdálených spuštěních bylo vylepšeno, nyní je uživateli poskytnuta komplexní chybová zpráva, pokud se spuštění nezdařilo.
    + Z poznámkového `automlstep` bloku kanálu byla odebrána get_data.
    + Byla `dataprep` spuštěna podpora v souboru `automlstep`.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Nové funkce**
  + Nyní můžete požádat o provedení konkrétních inspektorů (např. histogram, bodový graf atd.) na konkrétních sloupcích.
  + Byl přidán argument parallelize do . `append_columns` Pokud True, data budou načtena do paměti, ale spuštění bude spuštěno paralelně; Pokud False, spuštění bude streamování, ale jednovláknové.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK pro Python v1.0.53

+ **Nové funkce**
  + Automatizované machine learning nyní podporuje školení modelů ONNX na vzdáleném výpočetním cíli
  + Azure Machine Learning teď poskytuje možnost pokračovat v trénování z předchozího spuštění, kontrolníbod nebo soubory modelu.
    + Naučte se [používat odhady k obnovení tréninku z předchozího spuštění.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Opravy a vylepšení chyb**
  + **klient automl-client-core-nativeclient**
    + Opravte chybu při ztrátě typů sloupců po transformaci (propojená chyba);
    + Povolit y_query být typ objektu obsahující None (y) na začátku (#459519).
  + **azurové cli-ml**
    + Příkazy rozhraní příkazového řádku "nasazení modelu" a "aktualizace služby" nyní přijímají parametry, konfigurační soubory nebo jejich kombinaci. Parametry mají přednost před atributy v souborech.
    + Popis modelu lze nyní aktualizovat po registraci
  + **azureml-automl-core**
    + Aktualizujte závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + Přidání podpory pro odhady NimbusML & potrubí, které se má použít v rámci odhadů Automatické ml.
    + Oprava chyby ve výběrovém řízení Ensemble, která zbytečně rostla výsledný soubor, i když skóre zůstalo konstantní.
    + Povolte opakované použití některých featurizations přes Rozdělení cv pro prognózování úkolů. To urychluje run-time instalace spustit zhruba faktor n_cross_validations pro nákladné featurizations jako lagy a kolejových oken.
    + Řešení problému, pokud je čas mimo pandy podporované časový rozsah. Nyní navěšujeme DataException, pokud je čas menší než pd. Timestamp.min nebo větší než pd. Časové razítko.max
    + Prognózování nyní umožňuje různé frekvence ve vlakovém a testovacím množině, pokud mohou být vyrovnány. Například "čtvrtletní počínaje v lednu" a na "čtvrtletní od října" může být zarovnán.
    + Vlastnost "parametry" byla přidána do TimeSeriesTransformer.
    + Odeberte staré třídy výjimek.
    + V úlohách prognózy `target_lags` nyní parametr přijímá jednu celou hodnotu nebo seznam celá čísla. Pokud bylo k dispozici celé číslo, bude vytvořena pouze jedna prodleva. Pokud je k dispozici seznam, budou odebrány jedinečné hodnoty zpoždění. target_lags=[1, 2, 2, 4] vytvoří zpoždění jednoho, 2 a 4 období.
    + Opravte chybu o ztrátě typů sloupců po transformaci (propojená chyba);
    + V `model.forecast(X, y_query)`aplikaci povolte y_query typ objektu obsahující žádný (y) na začátku (#459519).
    + Přidání očekávaných `automl` hodnot do výstupu
  + **azureml-contrib-datadrift**
    +  Vylepšení ukázkového poznámkového bloku včetně přepnutí na azureml-opendatasets namísto azureml-contrib opendatasets a vylepšení výkonu při obohacování dat
  + **azureml-contrib-explain-model**
    + Opraven argument transformace pro lime vysvětlující pro důležitost nezpracovaných funkcí v balíčku azureml-contrib-explain-model
    + Přidány segmentace do vysvětlení image v explaineru image pro balíček AzureML-contrib-explain-model
    + Přidat scipy řídké podporu pro LimeExplainer
    + Přidáno `batch_size` napodobovat `include_local=False`explainer, když , pro streamování globální vysvětlení v dávkách ke zlepšení doby provádění DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Oprava pro volání set_featurizer_timeseries_params(): změna typu hodnoty dict `timeseries` a kontrola null - Přidat poznámkový blok pro featurizer
    + Aktualizujte závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
  + **azureml-core**
    + Přidána možnost připojit úložiště dat DBFS do příkazového příkazu k infrastrukturu AzureML
    + Opravena chyba s nahráním datastore, `target_path` kde je vytvořena prázdná složka, pokud byla spuštěna`/`
    + Opraven `deepcopy` problém v serviceprincipalauthentication.
    + Do příkazového příkazu cli byly přidány příkazy "az ml environment show" a "az ml environment list".
    + Prostředí nyní podporují určení base_dockerfile jako alternativu k již sestavené base_image.
    + Nepoužívané nastavení RunConfiguration auto_prepare_environment bylo označeno jako zastaralé.
    + Popis modelu lze nyní aktualizovat po registraci
    + Oprava: Model a image delete nyní poskytuje další informace o načítání upstream objekty, které jsou závislé na nich, pokud odstranění selže z důvodu upstream závislost.
    + Opravena chyba, která vytiskla prázdnou dobu trvání pro nasazení, ke kterým dochází při vytváření pracovního prostoru pro některá prostředí.
    + Vylepšený pracovní prostor vytváří výjimky selhání. Taková, že uživatelé nevidí "Nelze vytvořit pracovní prostor. Nelze najít..." jako zprávu a místo toho vidět skutečné selhání vytvoření.
    + Přidejte podporu pro ověřování tokenů ve webových službách AKS.
    + Přidejte `get_token()` `Webservice` metodu k objektům.
    + Přidána podpora cli pro správu datových sad strojového učení.
    + `Datastore.register_azure_blob_container`nyní volitelně `blob_cache_timeout` přebírá hodnotu (v sekundách), která konfiguruje parametry připojení blobfuse tak, aby umožňovaly vypršení platnosti mezipaměti pro toto úložiště dat. Výchozí hodnota není žádný časový limit, tj. Většina úloh bude upřednostňovat toto nastavení, ale některé úlohy je třeba číst více dat z velké datové sady, než se vejde na jejich uzly. Pro tyto úlohy ladění tohoto parametru jim pomůže uspět. Při ladění tohoto parametru dbát opatrnosti: nastavení příliš nízké hodnoty může mít za následek nízký výkon, protože data použitá v epochě mohou vypršet před dalším použitím. To znamená, že všechna čtení budou provedena z úložiště objektů blob (tj. sítě) spíše než místní mezipaměti, což negativně ovlivňuje dobu školení.
    + Popis modelu lze nyní správně aktualizovat po registraci
    + Odstranění modelu a obrázku nyní poskytuje další informace o objektech proti proudu, které jsou na nich závislé, což způsobí selhání odstranění
    + Zlepšete využití prostředků vzdálených spuštění pomocí azureml.mlflow.
  + **azureml-explain-model**
    + Opraven argument transformace pro lime vysvětlující pro důležitost nezpracovaných funkcí v balíčku azureml-contrib-explain-model
    + přidat scipy řídkou podporu pro LimeExplainer
    + přidán tvar lineární explainer obálka, stejně jako další úroveň tabulkového explainer pro vysvětlení lineární modely
    + pro mimikovat explainer v knihovně vysvětlit model, opravena chyba při include_local= False pro vstup řídké data
    + přidání očekávaných `automl` hodnot do výstupu
    + opravena důležitost funkce permutace, když byl zadán argument transformace, aby se získal význam nezpracovaných prvků
    + přidáno `batch_size` napodobovat `include_local=False`explainer, když , pro streamování globální vysvětlení v dávkách ke zlepšení doby provádění DecisionTreeExplainableModel
    + pro knihovnu vysvětlitelnosti modelu, pevné blackbox explainers, kde pandas dataframe vstup je vyžadován pro předpověď
    + Opravena chyba, kdy `explanation.expected_values` se někdy vrátil float spíše než seznam s plovákem v něm.
  + **azureml-mlflow**
    + Zlepšete výkon mlflow.set_experiment(experiment_name)
    + Oprava chyby při používání InteractiveLoginAuthentication pro mlflow tracking_uri
    + Zlepšete využití prostředků vzdálených spuštění pomocí azureml.mlflow.
    + Zlepšete dokumentaci balíčku azureml-mlflow
    + Oprava chyba, kde mlflow.log_artifacts ("my_dir") by uložit artefakty pod "my_dir/<artefakt cesty>" namísto "<artefakt-cesty>"
  + **azureml-opendatasets**
    + Pin `pyarrow` `opendatasets` na staré verze (<0.14.0) z důvodu problému s pamětí nově zavedené tam.
    + Přesuňte azureml-contrib-opendatasets do azureml-opendatasets.
    + Umožněte registraci otevřených tříd datových sad do pracovního prostoru Azure Machine Learning a bezproblémově využijte možnosti datové sady AML.
    + Výrazně zlepšete výkon NoaaIsdWeather obohaťte výkon ve verzi bez SPARK.
  + **azureml-pipeline-steps**
    + DBFS Datastore je nyní podporován pro vstupy a výstupy v DatabricksStep.
    + Aktualizovaná dokumentace pro krok Azure Batch s ohledem na vstupy/výstupy.
    + V AzureBatchStep *změněna delete_batch_job_after_finish* výchozí hodnotu na *true*.
  + **azureml-telemetrie**
    +  Přesuňte azureml-contrib-opendatasets do azureml-opendatasets.
    + Umožněte registraci otevřených tříd datových sad do pracovního prostoru Azure Machine Learning a bezproblémově využijte možnosti datové sady AML.
    + Výrazně zlepšete výkon NoaaIsdWeather obohaťte výkon ve verzi bez SPARK.
  + **azureml-vlak-automl**
    + Aktualizovaná dokumentace k get_output tak, aby odrážela skutečný návratový typ a poskytla další poznámky k načítání vlastností klíče.
    + Aktualizujte závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + přidání očekávaných `automl` hodnot do výstupu
  + **azureml-train-core**
    + Řetězce jsou nyní přijímány jako výpočetní cíl pro automatické ladění hyperparametrů
    + Nepoužívané nastavení RunConfiguration auto_prepare_environment bylo označeno jako zastaralé.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Nové funkce**
  + Přidána podpora pro čtení souboru přímo z http nebo https url.

+ **Opravy a vylepšení chyb**
  + Vylepšená chybová zpráva při pokusu o čtení datové sady parket ze vzdáleného zdroje (který není aktuálně podporován).
  + Opravena chyba při zápisu do formátu parketového souboru v ADLS Gen 2 a aktualizaci názvu kontejneru ADLS Gen 2 v cestě.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuální rozhraní
+ **Funkce náhledu**
  + Přidán modul "Spustit skript R" ve vizuálním rozhraní.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK pro Python v1.0.48

+ **Nové funkce**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** je nyní k dispozici jako **azureml-opendatasets**. Starý balíček může stále fungovat, ale doporučujeme používat **azureml-opendatasets** kupředu pro bohatší možnosti a vylepšení.
    + Tento nový balíček umožňuje zaregistrovat otevřené datové sady jako datovou sadu v pracovním prostoru Azure Machine Learning a využít jakékoli funkce, které dataset nabízí.
    + Zahrnuje také stávající funkce, jako je využívání otevřených datových sad, jako jsou datové rámce Pandas / SPARK a připojení k umístění pro některé datové sady, jako je počasí.

+ **Funkce náhledu**
    + HyperDriveConfig nyní může přijímat objekt kanálu jako parametr pro podporu hyperparameter tuning pomocí kanálu.

+ **Opravy a vylepšení chyb**
  + **azureml-vlak-automl**
    + Opravena chyba o ztrátě typů sloupců po transformaci.
    + Opravena chyba, která y_query umožnila být typem objektu obsahujícím none(s) na začátku.
    + Opravena chyba ve výběrovém řízení Ensemble, která zbytečně rostla výsledný soubor, i když skóre zůstalo konstantní.
    + Opraven problém s nastavením whitelist_models a blacklist_models v automatickém mlkroku.
    + Opraven problém, který zabránil použití předběžného zpracování, když by automatické ml byly použity v kontextu Azure ML pipelines.
  + **azureml-opendatasets**
    + Přesunuté azureml-contrib-opendatasets do azureml-opendatasets.
    + Povolené otevřené třídy datových sad se mají zaregistrovat do pracovního prostoru Azure Machine Learning a bezproblémově využívat možnosti datové sady AML.
    + Vylepšený NoaaIsdWeather výrazně obohacuje výkon ve verzi bez SPARK.
  + **azureml-explain-model**
    + Aktualizovaná online dokumentace pro objekty interpretovatelnosti.
    + Přidáno `batch_size` napodobovat `include_local=False`explainer, když , pro streamování globální vysvětlení v dávkách ke zlepšení doby provádění DecisionTreeExplainableModel pro model explainability knihovny.
    + Opraven problém, `explanation.expected_values` kdy někdy vracel float spíše než seznam s plovoucí maješkou.
    + Přidány očekávané `automl` hodnoty pro výstup pro miminální explainer v knihovně vysvětlit model.
    + Opravena důležitost funkce permutace, když byl zadán argument transformace, aby se získal důležitost nezpracovaných prvků.
  + **azureml-core**
    + Přidána možnost připojit úložiště dat DBFS v příkazovém příkazu KONT AzureML.
    + Byl opraven problém s nahráváním úložiště dat, kde je vytvořena prázdná složka, pokud `target_path` byla spuštěna s `/`aplikací .
    + Povolené porovnání dvou datových sad.
    + Model a image delete nyní poskytuje další informace o načítání upstream objekty, které jsou závislé na nich, pokud odstranění selže z důvodu upstream závislost.
    + Zastaralé nepoužívané nastavení RunConfiguration v auto_prepare_environment.
  + **azureml-mlflow**
    + Vylepšené využití prostředků vzdálených spuštění, které používají azureml.mlflow.
    + Vylepšena dokumentace balíčku azureml-mlflow.
    + Opraven problém, kdy mlflow.log_artifacts("my_dir") ukláněl artefakty pod "my_dir/cesty artefaktů" namísto "cesty artefaktů".
  + **azureml-potrubí-jádro**
    + Parametr hash_paths pro všechny kroky kanálu je zastaralé a budou odebrány v budoucnu. Ve výchozím nastavení je obsah source_directory zahashován (s výjimkou souborů uvedených v .amlignore nebo .gitignore)
    + Pokračování zlepšování modulu a moduluStep pro podporu modulů specifických pro výpočetní typ, příprava na integraci RunConfiguration a další změny pro odemknutí využití modulu specifického pro výpočetní typ v kanálech.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Vylepšená dokumentace s ohledem na vstupy/výstupy.
    + AzureBatchStep: Změněno delete_batch_job_after_finish výchozí hodnotu na true.
  + **azureml-train-core**
    + Řetězce jsou nyní přijímány jako výpočetní cíl pro automatické hyperparametrické ladění.
    + Zastaralé nepoužívané nastavení RunConfiguration v auto_prepare_environment.
    + Zastaralé `conda_dependencies_file_path` parametry `pip_requirements_file_path` a ve prospěch `conda_dependencies_file` `pip_requirements_file` a respektive.
  + **azureml-opendatasets**
    + Výrazně zlepšete výkon NoaaIsdWeather obohaťte výkon ve verzi bez SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **Nové funkce**
 + Objekty toku dat mohou být nyní iterovány a vytvářejí sekvenci záznamů. Viz dokumentace `Dataflow.to_record_iterator`pro .
  + Objekty toku dat mohou být nyní iterovány a vytvářejí sekvenci záznamů. Viz dokumentace `Dataflow.to_record_iterator`pro .

+ **Opravy a vylepšení chyb**
 + Zvýšena robustnost sady DataPrep SDK.
 + Vylepšené zpracování pandy DataFrames s non-string indexy sloupců.
 + Vylepšen výkon `to_pandas_dataframe` v datových sadách.
 + Opravena chyba, při které se při spuštění v prostředí s více uzny nezdařilo spuštění datových sad Spark.
  + Zvýšena robustnost sady DataPrep SDK.
  + Vylepšené zpracování pandy DataFrames s non-string indexy sloupců.
  + Vylepšen výkon `to_pandas_dataframe` v datových sadách.
  + Opravena chyba, při které se při spuštění v prostředí s více uzny nezdařilo spuštění datových sad Spark.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

Vrátili jsme změnu, která zlepšila výkon, protože způsobovala problémy některým zákazníkům, kteří používají Azure Databricks. Pokud jste se setkali s problémem na Azure Databricks, můžete upgradovat na verzi 1.1.7 pomocí jedné z níže uvedených metod:
1. Spusťte tento skript pro upgrade:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Znovu vytvořte cluster, který nainstaluje nejnovější verzi sady Data Prep SDK.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK pro Python v1.0.45

+ **Nové funkce**
  + Přidat náhradní model rozhodovacího stromu, který napodobuje vysvětlivací balíček v balíčku azureml-explain-model
  + Možnost zadat verzi CUDA, která má být nainstalována na snímcích odvození. Podpora cuda 9.0, 9.1 a 10.0.
  + Informace o inacích tréninkové základny Azure ML jsou teď dostupné v [úložišti GitHub v kontejnerech Azure ML](https://github.com/Azure/AzureML-Containers) a [v DockerHubu.](https://hub.docker.com/_/microsoft-azureml)
  + Přidána podpora vykreslování cose, pro plán kanálu. Spusťte "az ml pipeline -h" a dozvíte se více
  + Do konfigurace nasazení webové služby AKS a rozhraní CLI byl přidán vlastní parametr oboru názvů Kubernetes.
  + Zastaralé hash_paths parametr pro všechny kroky kanálu
  + Model.register nyní podporuje registraci více jednotlivých souborů `child_paths` jako jeden model s použitím parametru.

+ **Funkce náhledu**
    + Vysvětlivky skóre nyní volitelně uložit conda a pip informace pro spolehlivější serializace a deserializace.
    + Oprava chyby pro automatický volič funkcí.
    + Aktualizováno mlflow.azureml.build_image na nové rozhraní API, opravené chyby vystavené nové implementace.

+ **Opravy a vylepšení chyb**
  + Odebrána `paramiko` závislost z azureml-core. Přidána upozornění na vyřazení pro starší metody připojení výpočetního cíle.
  + Zlepšete výkon create_children
  + V napodobovacím vysvětlujícím u binárního třídění opravte pořadí pravděpodobností při použití pravděpodobnosti učitele pro změnu hodnoty tvaru.
  + Vylepšené zpracování chyb a zpráva pro automatizované strojové učení.
  + Opraven problém s časovým výtažkem iterace pro automatizované strojové učení.
  + Vylepšen výkon transformace časových řad pro automatizované strojové učení.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **Nové funkce**
  + Přidány souhrnné funkce pro`SummaryFunction.TOPVALUES`nejvyšší hodnoty`SummaryFunction.BOTTOMVALUES`( ) a nejnižší hodnoty ( ).

+ **Opravy a vylepšení chyb**
  + Výrazně zlepšila `read_pandas_dataframe`výkon .
  + Opravena chyba, `get_profile()` která by způsobovala selhání toku dat směřujícího na binární soubory.
  + Vystaveno `set_diagnostics_collection()` umožnit programové povolení nebo zakázání kolekce telemetrie.
  + Změněno chování `get_profile()`společnosti . Hodnoty NaN jsou nyní ignorovány pro Min, Mean, Std a Sum, které se zarovnají s chováním pand.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK pro Python v1.0.43

+ **Nové funkce**
  + Azure Machine Learning teď poskytuje prvotřídní podporu pro populární strojové učení a rámec pro analýzu dat Scikit-learn. Pomocí [ `SKLearn` odhadu](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)mohou uživatelé snadno trénovat a nasazovat modely Scikit-learn.
    + Naučte se, jak [spustit hyperparametrizování pomocí Scikit-learn pomocí HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Přidána podpora pro vytváření ModuleStep v kanálech spolu s modulem a moduleversion tříd pro správu opakovaně použitelných výpočetních jednotek.
  + Webové služby ACI nyní podporují trvalé scoring_uri prostřednictvím aktualizací. scoring_uri se změní z IP na reqdn. Popisek názvu DNS pro fqdn lze nakonfigurovat nastavením dns_name_label na deploy_configuration.
  + Automatizované strojové učení nové funkce:
    + STL featurizer pro prognózování
    + Clustering KMeans je povolen pro zametání funkcí
  + AmlCompute Kvóta schválení právě stal se rychleji! Nyní jsme zautomatizovali proces schvalování vašich žádostí o kvótu v rámci prahové hodnoty. Další informace o tom, jak kvóty fungují, naleznete v informacích o [tom, jak spravovat kvóty](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Funkce náhledu**
    + Integrace se sledováním [MLflow](https://mlflow.org) 1.0.0 prostřednictvím balíčku azureml-mlflow[(ukázkové poznámkové bloky).](https://aka.ms/azureml-mlflow-examples)
    + Odeslat Jupyter notebook jako běh. [Referenční dokumentace rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Public Preview of [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) prostřednictvím balíčku azureml-contrib-datadrift[(ukázkové poznámkové bloky).](https://aka.ms/azureml-datadrift-example) Datový drift je jedním z hlavních důvodů, proč přesnost modelu v průběhu času snižuje. Stává se to, když se data obsluhovaná k modelu v produkčním prostředí liší od dat, na kterých byl model trénován. Detektor driftu AML data pomáhá zákazníkům sledovat posun dat a odesílá výstrahu vždy, když je detekován posun.

+ **Změny způsobující chyby**

+ **Opravy a vylepšení chyb**
  + Funkce RunConfiguration načítá a ukládá podporu určující úplnou cestu k souboru s úplnou zpětnou kompatibilitou pro předchozí chování.
  + Do mezipaměti bylo přidáno ověřování ServicePrincipalAuthentication, které je ve výchozím nastavení vypnuto.
  + Povolte protokolování více parcel pod stejným názvem metriky.
  + Třída modelu je nyní správně importovatelná z azureml.core (`from azureml.core import Model`).
  + V krocích `hash_path` kanálu je nyní parametr zastaralá. Nové chování je hash dokončení source_directory, s výjimkou souborů uvedených v .amlignore nebo .gitignore.
  + V balíčcích `get_all` potrubí, různé a `get_all_*` metody byly `list` zastaralé `list_*`ve prospěch a , resp.
  + azureml.core.get_run již nevyžaduje, aby byly třídy importovány před vrácením původního typu spuštění.
  + Byl opraven problém, kdy některá volání aktualizace WebService Update nespustila aktualizaci.
  + Vyhodnocování časový čas na aks webové služby by měla být mezi 5ms a 300000ms. Max dovolil scoring_timeout_ms pro skórování žádostí byl narazil od 1 min do 5 min.
  + LocalWebservice objekty `scoring_uri` `swagger_uri` nyní mají a vlastnosti.
  + Přesunuté výstupy vytvoření adresáře a výstupy adresáře nahrát z uživatelského procesu. Povoleno spuštění historie sady SDK pro spuštění v každém uživatelském procesu. To by mělo vyřešit některé problémy se synchronizací dochází k distribuované školení spuštění.
  + Název protokolu azureml napsaného z názvu uživatelského procesu bude nyní obsahovat název procesu (pouze pro distribuované školení) a PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Opravy a vylepšení chyb**
  + U interpretovaných hodnot datetime, které mají dvoumístný formát roku, byl rozsah platných let aktualizován tak, aby odpovídal funkci Windows May Release. Rozsah byl změněn z 1930-2029 na 1950-2049.
  + Při čtení v souboru a nastavení `handleQuotedLineBreaks=True`, `\r` bude považován za nový řádek.
  + Opravena chyba, `read_pandas_dataframe` která v některých případech způsobila selhání.
  + Zlepšený výkon . `get_profile`
  + Vylepšené chybové zprávy.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **Nové funkce**
  + Nyní můžete použít následující funkce jazyka výrazu extrahovat a analyzovat datetime hodnoty do nových sloupců.
    + `RegEx.extract_record()`extrahuje datetime prvky do nového sloupce.
    + `create_datetime()`vytvoří objekty datetime ze samostatných prvků datetime.
  + Při `get_profile()`volání můžete nyní vidět, že kvantilové sloupce jsou označeny jako (est.), které jasně označují, že hodnoty jsou aproximace.
  + Teď můžete používat ** globbing při čtení z úložiště objektů blob Azure.
    + Např.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Opravy chyb**
  + Opravena chyba související se čtením souboru parket ze vzdáleného zdroje (objekt blob Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK pro Python v1.0.39
+ **Změny**
  + Spustit možnost konfigurace auto_prepare_environment se zastaralá, přičemž automatická příprava se stává výchozí.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **Nové funkce**
  + Přidána podpora pro čtení z databáze PostgresSQL, a to buď voláním read_postgresql nebo pomocí Datastore.
    + Podívejte se na příklady v návodech:
      + [Poznámkový blok pro ingestování dat](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Poznámkový blok úložiště dat](https://aka.ms/aml-data-prep-datastore-nb)

+ **Opravy a vylepšení chyb**
  + Opraveny problémy s převodem typu sloupce:
  + Nyní správně převede logický nebo číselný sloupec na logický sloupec.
  + Nyní se nezdaří při pokusu o nastavení sloupce data typu data.
  + Vylepšené typy JoinType a doprovodná referenční dokumentace. Při spojování dvou toků dat můžete nyní zadat jeden z těchto typů spojení:
    + ŽÁDNÝ, MATCH, VNITŘNÍ, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Vylepšené odvození datového typu pro rozpoznání více formátů kalendářních dat.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>portál Azure

Na webu Azure Portal teď můžete:
+ Vytváření a spouštění automatizovaných experimentů ML
+ Vytvořte virtuální počítač s poznámkovým blokem a vyzkoušejte ukázkové poznámkové bloky Jupyter nebo vlastní.
+ Zbrusu nový oddíl pro vytváření (preview) v pracovním prostoru Azure Machine Learning, který zahrnuje automatizované strojové učení, vizuální rozhraní a hostované virtuální počítače
    + Automatické vytvoření modelu pomocí automatizovaného strojového učení
    + Spuštění experimentů pomocí přetažení mažení vizuálního rozhraní.
    + Vytvořte virtuální počítač s poznámkovým blokem, který bude zkoumat data, vytvářet modely a nasazovat služby.
+ Aktualizace živého grafu a metrik v přehledech spuštění a spuštění stránek podrobností
+ Byl aktualizován prohlížeč souborů pro protokoly, výstupy a snímky na stránkách podrobností spustit.
+ Nové a vylepšené prostředí pro vytváření sestav na kartě Experimenty
+ Přidána možnost stáhnout soubor config.json ze stránky Přehled pracovního prostoru Azure Machine Learning.
+ Podporujte vytváření pracovních prostorů Azure Machine Learning z pracovního prostoru Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK pro Python v1.0.33
+ **Nové funkce**
  + Metoda _Workspace.create_ nyní přijímá výchozí konfigurace clusteru pro clustery CPU a GPU.
  + Pokud se vytvoření pracovního prostoru nezdaří, budou vyčištěny závislé prostředky.
  + Výchozí skladová položka registru kontejneru Azure byla přepnuta na základní.
  + Azure Container Registry se vytvoří líně, když je potřeba pro spuštění nebo vytvoření image.
  + Podpora prostředí pro tréninkové běhy.

### <a name="notebook-virtual-machine"></a>Virtuální počítač pro notebooky 

Použijte virtuální počítač s poznámkovým blokem jako zabezpečené hostitelské prostředí připravené pro podniky pro poznámkové bloky Jupyter, ve kterých můžete programovat experimenty strojového učení, nasazovat modely jako koncové body webu a provádět všechny ostatní operace podporované sadou Azure Machine Learning SDK pomocí Pythonu.Poskytuje několik možností:
+ [Rychle svižte předkonfigurovaný virtuální počítač](tutorial-1st-experiment-sdk-setup.md) s notebookem, který má nejnovější verzi sady Azure Machine Learning SDK a související balíčky.
+ Přístup je zabezpečený pomocí osvědčených technologií, jako je HTTPS, ověřování a autorizace služby Azure Active Directory.
+ Spolehlivé cloudové úložiště poznámkových bloků a kódu v účtu úložiště objektů blob Azure Machine Learning Workspace.Virtuální počítač poznámkového bloku můžete bezpečně odstranit, aniž byste přišli o práci.
+ Předinstalované ukázkové poznámkové bloky k prozkoumání a experimentování s funkcemi Azure Machine Learning.
+ Úplné možnosti přizpůsobení virtuálních počítačů Azure, libovolný typ virtuálního počítače, všechny balíčky, všechny ovladače. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK pro Python v1.0.33 vydané.

+ Modely s akcelerací hardwaru Azure ML na [FPGA](how-to-deploy-fpga-web-service.md) je obecně k dispozici.
  + Nyní můžete [použít balíček azureml-accel-models](how-to-deploy-fpga-web-service.md) k:
    + Trénování hmotností podporované hluboké neuronové sítě (ResNet 50, ResNet 152, DenseNet-121, VGG-16 a SSD-VGG)
    + Použití přenosu učení s podporovaným DNN
    + Registrace modelu se službou správy modelů a kontejnerize modelu
    + Nasazení modelu do virtuálního počítače Azure pomocí FPGA v clusteru služby Azure Kubernetes Service (AKS)
  + Nasazení kontejneru na serverové zařízení [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Skóre dat s gRPC koncovýbod s touto [ukázkou](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizované strojové učení

+ Funkce zametání povolit :::no-loc text="featurizers"::: dynamické přidávání pro optimalizaci výkonu. Novinka: :::no-loc text="featurizers":::pracovní vkládání, váha důkazů, cílové kódování, kódování textu, vzdálenost clusteru
+ Inteligentní životopis pro zpracování vlaku / platných děl uvnitř automatizovaných ML
+ Několik změn optimalizace paměti a zlepšení výkonu za běhu
+ Zlepšení výkonu v vysvětlení modelu
+ Převod modelu ONNX pro místní běh
+ Přidána podpora podvzorkování
+ Inteligentní zastavení, pokud nejsou definována žádná kritéria ukončení
+ Skládané soubory

+ Prognózování časové řady
  + Nová funkce předpovědní prognózy
  + Nyní můžete použít křížové ověření postupného původu na datech časových řad
  + Pro konfiguraci zpoždění časových řad byly přidány nové funkce
  + Pro podporu agregačních funkcí z rolovacího okna byly přidány nové funkce
  + Nová detekce svátků a featurizer, když je kód země definován v nastavení experimentu

+ Azure Databricks
  + Povolené prognózy časových řad a schopnost vysvětlitelnost/interpretabilitu modelu
  + Nyní můžete zrušit a pokračovat (pokračovat) automatizované experimenty ML
  + Přidána podpora pro vícejádrové zpracování

### <a name="mlops"></a>MlOps
+ **Ladění & místního nasazení pro kontejnery vyhodnocování**<br/> Nyní můžete nasadit model ML místně a iterate rychle na vyhodnocovací soubor a závislosti zajistit, že se chovají podle očekávání.

+ **Zavedena InferenceConfig & Model.deploy()**<br/> Nasazení modelu nyní podporuje určení zdrojové složky se vstupním skriptem, stejně jako RunConfig.  Nasazení modelu bylo navíc zjednodušeno na jeden příkaz.

+ **Sledování odkazů na Git**<br/> Zákazníci již nějakou dobu požadují základní možnosti integrace Gitu, protože pomáhají udržovat komplexní auditní stopu. Implementovali jsme sledování napříč hlavními entitami v Azure ML pro metadata související s Gitem (úložiště, potvrzení, čistý stav). Tyto informace budou shromažďovány automaticky sadou SDK a CLI.

+ **Služba ověření profilování modelu &**<br/> Zákazníci si často stěžují na obtížnost správné velikosti výpočetních prostředků spojených s jejich službou odvození. S naší službou profilování modelů může zákazník poskytnout ukázkové vstupy a budeme profilovat napříč 16 různými konfiguracemi CPU / paměti, abychom určili optimální velikost pro nasazení.

+ **Přineste si vlastní základní obrázek pro odvození**<br/> Další častou stížností byly potíže s přechodem od experimentování k odvození závislostí na sdílení RE. Díky naší nové funkci sdílení základních obrázků můžete nyní znovu použít své základní obrázky experimentování, závislosti a vše pro odvození. To by mělo urychlit nasazení a snížit mezeru z vnitřní do vnější smyčky.

+ **Vylepšené zkušenosti s generací schématu Swagger**<br/> Naše předchozí metoda generování chvástání byla náchylná k chybám a nemožná k automatizaci. Máme nový in-line způsob generování schémat anaparování z libovolné funkce Pythonu prostřednictvím dekoratérů. Máme open sourced tento kód a náš protokol generování schématu není spojen s platformou Azure ML.

+ **Rozhraní příkazového příkazu Azure ML je obecně dostupné (GA)**<br/> Modely lze nyní nasadit pomocí jediného příkazu příkazu příkazu příkazu. Máme společnou zpětnou vazbu od zákazníků, že nikdo nenasazuje model ML z notebooku Jupyter. [**Referenční dokumentace vyvázacího mise**](https://aka.ms/azmlcli) byla aktualizována.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK pro Python v1.0.30 vydané.

Byl [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) zaveden přidat novou verzi publikovaného kanálu při zachování stejného koncového bodu.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Poznámka: Data Prep Python SDK `numpy` `pandas` se již nebude instalovat a balíčky. Viz [aktualizované pokyny k instalaci](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nové funkce**
  + Nyní můžete použít transformaci Pivot.
    + Návod, jak průvodce: [Kontingenční poznámkový blok](https://aka.ms/aml-data-prep-pivot-nb)
  + Nyní můžete používat regulární výrazy v nativních funkcích.
    + Příklady:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Nyní můžete `to_upper`  používat `to_lower`  a funkce v jazyce výrazu.
  + Nyní můžete zobrazit počet jedinečných hodnot každého sloupce v datovém profilu.
  + Pro některé běžně používané kroky čtečky, nyní `infer_column_types` můžete předat v argumentu. Pokud je nastavena na `True`, Data Prep se pokusí zjistit a automaticky převést typy sloupců.
    + `inference_arguments`nyní zastaralá.
  + Nyní můžete `Dataflow.shape`volat .

+ **Opravy a vylepšení chyb**
  + `keep_columns` Nyní přijímá další volitelný `validate_column_exists`argument , který `keep_columns` kontroluje, zda výsledek bude obsahovat všechny sloupce.
  + Všechny kroky čtečky (které čtou ze `verify_exists`souboru) nyní přijímají další volitelný argument .
  + Zlepšený výkon čtení z datového rámce pandas a získávání datových profilů.
  + Opravena chyba, při které se pomocí jediného indexu nezdařilo vytvoření jednoho kroku z toku dat.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>portál Azure
  + Nyní můžete znovu odeslat existující skript spustit na existující vzdálený výpočetní cluster.
  + Nyní můžete spustit publikovaný kanál s novými parametry na kartě Kanály.
  + Podrobnosti spuštění nyní podporuje nový prohlížeč souborů snímek. Můžete zobrazit snímek adresáře při odeslání konkrétního spuštění. Můžete si také stáhnout poznámkový blok, který byl odeslán ke spuštění spuštění.
  + Teď můžete zrušit nadřazené spuštění z portálu Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK pro Python v1.0.23

+ **Nové funkce**
  + Sada Azure Machine Learning SDK teď podporuje Python 3.7.
  + Odhady DNN Azure Machine Learning teď poskytují integrovanou podporu více verzí. Například `TensorFlow`  odhad nyní přijímá `framework_version` parametr a uživatelé mohou zadat verzi 1.10 nebo "1.12". Seznam verzí podporovaných aktuální verzí sady SDK `get_supported_versions()` zobrazíte požadovanou třídu `TensorFlow.get_supported_versions()`rozhraní (například ).
  Seznam verzí podporovaných nejnovější verzí sady SDK naleznete v [dokumentaci k programu DNN Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **Nové funkce**
  + Pomocí read_* transformací můžete číst více zdrojů Datastore/DataPath/DataReference.
  + Můžete provést následující operace na sloupcích vytvořit nový sloupec: dělení, podlaha, modulo, moc, délka.
  + Příprava dat je teď součástí diagnostické sady Azure ML a ve výchozím nastavení bude protokolovat diagnostické informace.
    + Chcete-li tuto hodnotu vypnout, nastavte tuto proměnnou prostředí na hodnotu true: DISABLE_DPREP_LOGGER

+ **Opravy a vylepšení chyb**
  + Vylepšená dokumentace kódu pro běžně používané třídy a funkce.
  + Opravena chyba v auto_read_file, které se nepodařilo číst soubory aplikace Excel.
  + Přidána možnost přepsat složku v read_pandas_dataframe.
  + Vylepšený výkon instalace závislostí dotnetcore2 a přidána podpora pro Fedoru 27 / 28 a Ubuntu 1804.
  + Vylepšen výkon čtení z objektů Blob Azure.
  + Detekce typu sloupce nyní podporuje sloupce typu Dlouhý.
  + Opravena chyba, kdy byly některé hodnoty kalendářních dat zobrazeny jako časová razítka namísto objektů datetime pythonu.
  + Opravena chyba, kdy byly některé počty typů zobrazeny jako dvojníky namísto celá čísla.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK pro Python v1.0.21

+ **Nové funkce**
  + Metoda *azureml.core.Run.create_children* umožňuje vytvoření více podřízených spuštění s jednou latencí.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Změny způsobující chyby**
  + Koncept balíčku Přípravy dat byl zastarala a již není podporována. Namísto zachování více toků dat v jednom balíčku, můžete zachovat Dataflows jednotlivě.
    + Návod: Otevření [a ukládání datového toku notebook](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nové funkce**
  + Příprava dat nyní dokáže rozpoznat sloupce, které odpovídají určitému sémantickému typu, a odpovídajícím způsobem rozdělit. STypes aktuálně podporovány patří: e-mailová adresa, zeměpisné souřadnice (zeměpisná šířka & zeměpisná délka), IPv4 a IPv6 adresy, americké telefonní číslo a PSČ USA.
    + Návod, jak průvodce: [Sémantické typy notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep nyní podporuje následující operace pro generování výsledného sloupce ze dvou číselných sloupců: odečíst, násobit, rozdělit a modulo.
  + Můžete volat `verify_has_data()` na Dataflow zkontrolovat, zda dataflow by vyrábět záznamy, pokud je spuštěn.

+ **Opravy a vylepšení chyb**
  + Nyní můžete zadat počet přihrádek, které se mají použít v histogramu pro profily číselných sloupců.
  + Transformace `read_pandas_dataframe` nyní vyžaduje, aby datový rámec měl názvy sloupců typu řetězec nebo bajt.
  + Opravena chyba `fill_nulls` v transformaci, kde hodnoty nebyly správně vyplněny, pokud sloupec chyběl.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK pro Python v1.0.18

 + **Změny**
   + Balíček azureml-tensorboard nahrazuje azureml-contrib-tensorboard.
   + V této verzi můžete nastavit uživatelský účet ve spravovaném výpočetním clusteru (amlcompute) při jeho vytváření. To lze provést předáním těchto vlastností v konfiguraci zřizování. Další podrobnosti naleznete v [referenční dokumentaci sady SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nové funkce**
  + Nyní podporuje přidání dvou číselných sloupců pro generování výsledného sloupce pomocí jazyka výrazu.

+ **Opravy a vylepšení chyb**
  + Vylepšena dokumentace a kontrola parametrů pro random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Oprava chyby**
  + Opraven problém s ověřováním hlavního povinného servisu, který byl způsoben změnou rozhraní API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK pro Python v1.0.17

+ **Nové funkce**

  + Azure Machine Learning teď poskytuje prvotřídní podporu pro populární Framework Chainer DNN. Pomocí [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) třídy mohou uživatelé snadno trénovat a nasazovat modely Chainer.
    + Naučte se [spouštět distribuované školení pomocí ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Naučte se [spouštět hyperparametrizování pomocí chaineru pomocí hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines přidali možnost aktivovat spuštění kanálu na základě úprav úložiště dat. [Poznámkový blok plánu](https://aka.ms/pl-schedule) kanálu se aktualizuje tak, aby tuto funkci prezentoval.

+ **Opravy a vylepšení chyb**
  + Přidali jsme podporu v kanálech Azure Machine Learning pro nastavení vlastnosti source_directory_data_store na požadované úložiště dat (například úložiště objektů blob) na [RunConfigurations,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) které jsou dodávány do [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Ve výchozím nastavení kroky použít Úložiště souborů Azure jako záložní úložiště dat, které může narazit na omezení problémy při velkém počtu kroků jsou prováděny souběžně.

### <a name="azure-portal"></a>portál Azure

+ **Nové funkce**
  + Nové prostředí editoru přetáhněte tabulky pro sestavy. Uživatelé mohou přetáhnout sloupec ze studny do oblasti tabulky, kde se zobrazí náhled tabulky. Sloupce lze přeuspořádat.
  + Prohlížeč souborů nové protokoly
  + Odkazy na experimentování běží, výpočetní prostředky, modely, image a nasazení z karty aktivity

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Nové funkce**
  + Data Prep nyní podporuje zápis datových proudů souborů z toku dat. Také poskytuje možnost manipulovat s názvy datových proudů souborů k vytvoření nových názvů souborů.
    + Návod: Práce [s poznámkovým blokem Datových proudů souborů](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Opravy a vylepšení chyb**
  + Zlepšený výkon t-Digest u velkých datových sad.
  + Data Prep nyní podporuje čtení dat z DataPath.
  + Jedno horké kódování nyní funguje na logických a číselných sloupcích.
  + Další opravy různé chyby.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK pro Python v1.0.15

+ **Nové funkce**
  + Azure Machine Learning Pipelines přidali AzureBatchStep[(poznámkový blok),](https://aka.ms/pl-azbatch)HyperDriveStep (poznámkový blok) a funkce plánování podle času ([poznámkový blok).](https://aka.ms/pl-schedule)
  +  DataTranferStep aktualizován pro práci s Azure SQL Server a Azure databáze pro PostgreSQL[(notebook).](https://aka.ms/pl-data-trans)

+ **Změny**
  + Zastaralá `PublishedPipeline.get_published_pipeline` ve prospěch `PublishedPipeline.get`.
  + Zastaralá `Schedule.get_schedule` ve prospěch `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nové funkce**
  + Data Prep teď podporuje čtení z databáze Azure SQL pomocí DataStore.

+ **Změny**
  + Byl vylepšen výkon paměti určitých operací u velkých dat.
  + `read_pandas_dataframe()`nyní `temp_folder` vyžaduje, aby byla specifikována.
  + Vlastnost `name` byla `ColumnProfile` zastaralá - místo `column_name` toho použijte.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK pro Python v1.0.10

+ **Změny**:
  + Azure ML SDK už nemá balíčky azure-cli jako závislost. Konkrétně azure-cli-core a azure-cli-profile závislosti byly odebrány z azureml-core. Jedná se o změny ovlivňující uživatelem:
      + Pokud provádíte "az login" a potom pomocí azureml-sdk, sada SDK provede přihlášení kódu prohlížeče nebo zařízení ještě jednou. Nebude používat žádné přihlašovací údaje stavu vytvořeného "az login".
    + Pro ověřování v příkazovém příkazovém příkazu Azure, jako je například použití "az login", použijte _třídu azureml.core.authentication.AzureCliAuthentication._ Pro ověřování v příkazovém příkazovém příkazovém popřípadě Azure _nainstalujte azure-cli_ v prostředí Pythonu, kde jste nainstalovali azureml-sdk.
    + Pokud provádíte "az login" pomocí instančního objektu pro automatizaci, doporučujeme použít _třídu azureml.core.authentication.ServicePrincipalAuthentication,_ protože azureml-sdk nebude používat stav pověření vytvořený rozhraním příkazového příkazu azure.

+ **Opravy chyb**: Tato verze většinou obsahuje drobné opravy chyb

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Opravy chyb**
  + Vylepšen výkon získávání datových profilů.
  + Opraveny drobné chyby související s hlášením chyb.

### <a name="azure-portal-new-features"></a>Portál Azure: nové funkce
+ Nové prostředí pro přetahování při vytváření grafů pro sestavy. Uživatelé mohou přetáhnout sloupec nebo atribut ze studny do oblasti grafu, kde systém automaticky vybere vhodný typ grafu pro uživatele na základě typu dat. Uživatelé mohou změnit typ grafu na jiné příslušné typy nebo přidat další atributy.

    Podporované typy grafů:
    - Spojnicový graf
    - Histogram
    - Skládaný pruhový graf
    - Obrázek rámečku
    - Bodový graf
    - Bublina Plot
+ Portál nyní dynamicky generuje sestavy pro experimenty. Když uživatel odešle spuštění experimentu, sestava bude automaticky generována s protokolovanými metrikami a grafy, které umožní porovnání mezi různými spuštěními.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK pro Python v1.0.8

+ **Opravy chyb**: Tato verze většinou obsahuje drobné opravy chyb

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Nové funkce**
  + Vylepšení úložiště dat (dokumentováno v [návodu k datům)](https://aka.ms/aml-data-prep-datastore-nb)
    + Přidána možnost číst a zapisovat do Azure File Share a ADLS Datastores v škálování.
    + Při použití DataStores Data Prep nyní podporuje použití ověřování instančního objektu namísto interaktivního ověřování.
    + Přidána podpora pro wasb a wasbs urls.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Opravy chyb**
  + Opravena chyba se čtením z veřejných čitelných kontejnerů objektů Blob Azure na Sparku

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK pro Python v1.0.6
+ **Opravy chyb**: Tato verze většinou obsahuje drobné opravy chyb

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Nové funkce**
  + `to_bool`funkce nyní umožňuje převod neodpovídajících hodnot na chybové hodnoty. Toto je nové výchozí chování `to_bool` `set_column_types`neshody pro a , zatímco předchozí výchozí chování bylo převést neodpovídající hodnoty na False.
  + Při `to_pandas_dataframe`volání existuje nová možnost interpretovat hodnoty null/chybějící v číselných sloupcích jako NaN.
  + Přidána možnost zkontrolovat návratový typ některých výrazů, aby byla zajištěna konzistence typu a selhání brzy.
  + Nyní můžete `parse_json` volat analyzovat hodnoty ve sloupci jako objekty JSON a rozbalit je do více sloupců.

+ **Opravy chyb**
  + Opravena chyba, `set_column_types` která havarovala v Pythonu 3.5.2.
  + Opravena chyba, která se zhroutila při připojování k Datastore pomocí bitové kopie AML.

+ **Aktualizace**
  * [Příklad poznámkových bloků](https://aka.ms/aml-data-prep-notebooks) pro kurzy začínáme, případové studie a návody.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Všeobecná dostupnost

Azure Machine Learning je teď obecně dostupný.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
V této verzi oznamujeme nové spravované výpočetní prostředí prostřednictvím [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). Tento výpočetní cíl nahrazuje výpočetní prostředky Azure Batch AI pro Azure Machine Learning.

Tento výpočetní cíl:
+ Používá se pro trénink modelu a odvození/vyhodnocování dávek.
+ Je jedno-až více uzlů výpočetní
+ Má správa clusteru a plánování úloh pro uživatele
+ Automatické škálování ve výchozím nastavení
+ Podpora prostředků CPU i GPU
+ Umožňuje použití virtuálních zařízení s nízkou prioritou pro snížení nákladů

Azure Machine Learning Compute se dá vytvořit v Pythonu pomocí portálu Azure nebo cli. Musí být vytvořen v oblasti pracovního prostoru a nelze jej připojit k žádnému jinému pracovnímu prostoru. Tento výpočetní cíl používá kontejner Dockeru pro vaše spuštění a balíčky závislostí replikovat stejné prostředí ve všech uzlech.

> [!Warning]
> Doporučujeme vytvořit nový pracovní prostor pro použití Azure Machine Learning Compute. Existuje vzdálená pravděpodobnost, že uživatelé, kteří se pokoušejí vytvořit Azure Machine Learning Compute z existujícího pracovního prostoru, se mohou zobrazit jako chyba. Existující výpočetní prostředky v pracovním prostoru by měly nadále fungovat bez e-ovlivnily.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK pro Python v1.0.2
+ **Změny způsobující chyby**
  + V této verzi odstraňujeme podporu pro vytváření virtuálního počítače z Azure Machine Learning. Pořád můžete připojit existující cloudový virtuální počítač nebo vzdálený místní server.
  + Také odstraňujeme podporu pro BatchAI, které by teď měly být podporované prostřednictvím Azure Machine Learning Compute.

+ **Nový**
  + Pro kanály strojového učení:
    + [Krok odhadu](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [Krok HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [Krok MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aktualizováno**
  + Pro kanály strojového učení:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) nyní přijímá runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) nyní kopíruje do a ze zdroje dat SQL
    + Naplánování funkčnosti sady SDK k vytvoření a aktualizaci plánů pro spuštění publikovaných kanálů

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Změny způsobující chyby**
  * `SummaryFunction.N`byl přejmenován `SummaryFunction.Count`na .

+ **Opravy chyb**
  * Použijte nejnovější AML Spustit token při čtení a zápisu do datových úložišť na vzdálené spuštění. Dříve, pokud je spuštěný token AML aktualizován v Pythonu, modul runtime Data Prep nebude aktualizován aktualizovaným tokenem spuštění AML.
  * Další jasnější chybové zprávy
  * to_spark_dataframe() se již nezhroutí, když Spark používá `Kryo` serializaci
  * Inspektor počtu hodnot nyní může zobrazit více než 1000 jedinečných hodnot
  * Náhodné rozdělení již selže, pokud původní tok dat nemá název

+ **Více informací**
  * [Sada SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty a poznámkové bloky
+ Kanály ML
  + Nové a aktualizované poznámkové bloky pro začínání s kanály, dávkovým vymezením a příklady přenosu stylů:https://aka.ms/aml-pipeline-notebooks
  + Přečtěte si, jak [vytvořit první kanál.](how-to-create-your-first-pipeline.md)
  + Zjistěte, jak [spustit predikce dávek pomocí kanálů](how-to-use-parallel-run-step.md)
+ Cíl výpočetních prostředků Azure Machine Learning
  + [Ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) se teď aktualizují, aby používaly nové spravované výpočetní prostředky.
  + [Další informace o tomto výpočetním](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portál Azure: nové funkce
+ Vytvořte a spravujte typy [výpočetních prostředků Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) na portálu.
+ Monitorování využití kvóty a [kvóta požadavků](how-to-manage-quotas.md) pro Azure Machine Learning Compute.
+ Zobrazení stavu výpočetního clusteru Azure Machine Learning compute v reálném čase.
+ Byla přidána podpora virtuálních sítí pro azure machine learning compute a vytvoření služby Azure Kubernetes Service.
+ Znovu spusťte publikované kanály s existujícími parametry.
+ Nové [automatizované grafy strojového učení](how-to-understand-automated-ml.md) pro klasifikační modely (výtah, zisky, kalibrace, graf důležitosti funkce s vysvětlitelností modelu) a regresní modely (reziduální a funkční graf s vysvětlitelností modelu).
+ Kanály si můžete prohlédnout na webu Azure Portal.




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK pro Python v0.1.80

+ **Změny způsobující chyby**
  * obor názvů *azureml.train.widgets* byl přesunut na *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* zavrhuje následující třídy - *azureml.core.compute.BatchAICompute* a *azureml.core.compute.DSVMCompute*. Druhá třída bude odebrána v následujících verzích. Třída AmlCompute má nyní jednodušší definici a jednoduše potřebuje vm_size a max_nodes a při odeslání úlohy automaticky změní velikost clusteru z 0 na max_nodes. Naše [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) byly aktualizovány s těmito informacemi a měl by vám poskytnout příklady použití. Doufáme, že se vám toto zjednodušení a spousta dalších zajímavých funkcí přijít v pozdější verzi!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1

Další informace o sdk aplikace Data Prep najdete v [referenčních dokumentech](https://aka.ms/data-prep-sdk).
+ **Nové funkce**
   * Vytvoření nového cli DataPrep pro spouštění balíčků DataPrep a zobrazení datového profilu pro datovou sadu nebo tok dat
   * Přepracované rozhraní API SetColumnType pro zlepšení použitelnosti
   * Přejmenováno na smart_read_file na auto_read_file
   * Nyní zahrnuje zkosení a špičatost v datovém profilu
   * Může vzorek s stratifikovaným vzorkem
   * Umí číst ze zip souborů, které obsahují soubory CSV
   * Může rozdělit datové sady řádek-moudrý s Random Split (například do testovacích vlakových souprav)
   * Může získat všechny datové typy sloupců z toku dat nebo datového profilu voláním`.dtypes`
   * Počet řádků lze získat z toku dat nebo datového profilu voláním`.row_count`

+ **Opravy chyb**
   * Pevná dlouhá až dvojitá konverze
   * Opravena vyhodnocení po každém přidání sloupce
   * Byl opraven problém s fuzzygroupingem, kdy v některých případech nezjišťoval skupiny
   * Pevná funkce řazení pro respektování pořadí řazení ve více sloupcích
   * Pevné a/nebo výrazy podobné `pandas` tomu, jak je zpracovává
   * Opraveno čtení z dráhy dbfs
   * Chybové zprávy srozumitelnější
   * Nyní již selže při čtení na vzdálený výpočetní cíl pomocí tokenu AML
   * Nyní již selže na LinuxU DSVM
   * Nyní již dojde k chybě, když jsou hodnoty bez řetězce v predikáty řetězců
   * Nyní zpracovává chyby kontrolního výrazu, když by měl dojít ke správnému selhání toku dat
   * Nyní podporuje umístění úložišť připojených k dbutilům na Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>portál Azure
Portál Azure pro Azure Machine Learning obsahuje následující aktualizace:
  * Nová karta **Pipelines** pro publikované kanály.
  * Přidána podpora pro připojení existujícího clusteru HDInsight jako výpočetního cíle.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK pro Python v0.1.74

+ **Změny způsobující chyby**
  * *Workspace.compute_targets, úložiště dat, experimenty, obrázky, modely a *webové služby* jsou vlastnosti namísto metod. Nahraďte například *soubor emWork.compute_targets()* *souborem Workspace.compute_targets*.
  * *Run.get_context* zavrhuje *Soubor Run.get_submitted_run*. Druhá metoda bude odstraněna v následujících verzích.
  * *Třída PipelineData* nyní očekává objekt úložiště dat jako parametr, nikoli datastore_name. Podobně *Pipeline* přijímá default_datastore, nikoli default_datastore_name.

+ **Nové funkce**
  * Ukázkový [poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) Azure Machine Learning Pipelines teď používá kroky MPI.
  * Widget RunDetails pro poznámkové bloky Jupyter umění vizualizaci kanálu.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0

+ **Nové funkce**
  * Počet typů přidaný do datového profilu
  * Počet hodnot a histogram je nyní k dispozici
  * Další percentily v datovém profilu
  * Medián je k dispozici v souhrnu
  * Python 3.7 je nyní podporován
  * Když uložíte tok dat, který obsahuje úložiště dat do balíčku DataPrep, informace o úložišti dat budou trvalé jako součást balíčku DataPrep
  * Zápis do úložiště dat je nyní podporován

+ **Opravena chyba**
  * 64bitové nepodepsané přetečení celého čísla jsou nyní správně zpracovány v Systému Linux
  * Opraven nesprávný textový popisek pro soubory ve formátu prostého textu v smart_read
  * Typ sloupce řetězce se nyní zobrazuje v zobrazení metrik
  * Počet typů je nyní opraven tak, aby zobrazoval Hodnoty Namapované na jeden FieldType namísto jednotlivých
  * Write_to_csv již neselže, pokud je cesta k dispozici jako řetězec
  * Při použití funkce Nahradit se ponechání prázdného pole "najít" již nesplní

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK pro Python v0.1.68

+ **Nové funkce**
  * Podpora více klientů při vytváření nového pracovního prostoru.

+ **Opraveny chyby**
  * Při nasazování webové služby již není nutné připínat verzi knihovny pynacl.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **Nové funkce**
  * Přidána metoda transform_partition_with_file(script_path), která umožňuje uživatelům předat cestu k provedení souboru Pythonu

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK pro Python v0.1.65
[Verze 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) obsahuje nové funkce, další dokumentaci, opravy chyb a další [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks).

Podívejte se [na seznam známých problémů,](resource-known-issues.md) kde se dozvíte o známých chybách a řešeních.

+ **Změny způsobující chyby**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Návratový slovník Workspace.web_services, dříve vrácený seznam. Viz dokumentace k rozhraní api [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API.

  * Automatizované strojové učení odstranilo normalizovanou střední čtvercovou chybu z primárních metrik.

+ **Hyperpohon**
  * Různé hyperdrive opravy chyb pro Bayesian, Vylepšení výkonu pro získání metriky volání.
  * Tentenzorflow 1,10 upgrade z 1,9
  * Optimalizace obrázků Dockeru pro studený start.
  * Úlohy nyní hlásí správný stav i v případě, že se ukončí s jiným kódem chyby než 0.
  * Ověření atributu RunConfig v sadě SDK.
  * Objekt spuštění hyperdrauše podporuje zrušení podobné běžnému spuštění: není třeba předávat žádné parametry.
  * Vylepšení widgetů pro udržování stavu rozevíracích hodnot pro distribuované spuštění a spuštění HyperDrive.
  * TensorBoard a další soubory protokolu podporují pevnou hodnotu parametru serveru.
  * Podpora mpi technologie Intel(R) na straně služby.
  * Oprava ladění parametrů pro distribuovanou opravu spuštění během ověřování v BatchAI.
  * Správce kontextu nyní identifikuje primární instanci.

+ **Prostředí portálu Azure**
  * log_table() a log_row() jsou podporovány v podrobnostech spustit.
  * Automaticky vytvářet grafy pro tabulky a řádky s 1, 2 nebo 3 číselnými sloupci a volitelným kategorickým sloupcem.

+ **Automatizované strojové učení**
  * Vylepšené zpracování chyb a dokumentace
  * Opraveny problémy s výkonem načítání vlastností.
  * Opraven problém s pokračováním v běhu.
  * Opraveny :::no-loc text="ensembling"::: problémy s iterací.
  * Opravena tréninková chyba na MAC OS.
  * Vzorkování makro průměrné křivky PR/ROC ve vlastním ověřovacím scénáři.
  * Odebrána další logika indexu.
  * Byl odebrán filtr z rozhraní API get_output.

+ **Kanály**
  * Přidána metoda Pipeline.publish() publikovat kanál přímo, aniž by bylo nutné spuštění spustit jako první.
  * Přidána metoda PipelineRun.get_pipeline_runs() pro načtení spuštění kanálu, které byly generovány z publikovaného kanálu.

+ **Projekt Brainwave**
  * Byla aktualizována podpora nových modelů AI dostupných na FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
[Verze 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) obsahuje následující funkce a opravy chyb:

+ **Nové funkce**
  * Podpora jednohorkého kódování
  * Podpora kvantilové transformace

+ **Opravena chyba:**
  * Funguje s jakoukoli verzí Tornado, není třeba downgrade verze Tornado
  * Hodnota se počítá pro všechny hodnoty, nejen pro první tři.

## <a name="2018-09-public-preview-refresh"></a>2018-09 (Veřejná aktualizace náhledu)

Nová, obnovená verze Azure Machine Learning: Přečtěte si další informace o této verzi:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Další kroky

Přečtěte si přehled služby [Azure Machine Learning](overview-what-is-azure-ml.md).
