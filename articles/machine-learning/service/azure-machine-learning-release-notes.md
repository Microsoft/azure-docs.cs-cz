---
title: Co je nového ve verzi?
titleSuffix: Azure Machine Learning service
description: Přečtěte si o nejnovějších aktualizacích služby Azure Machine Learning a strojovém učení a sadách Python SDK pro přípravu dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78d16e8e6fc43644cdb318f8e402c2e8bbe0363e
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772498"
---
# <a name="azure-machine-learning-service-release-notes"></a>Zpráva k vydání verze služby Azure Machine Learning

V tomto článku najdete další informace o vydaných verzích služby Azure Machine Learning.  Úplný referenční obsah sady SDK najdete na referenční stránce Azure Machine Learning [**hlavní sadě SDK pro Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) . 

Zobrazit [seznam známých problémů](resource-known-issues.md) Další informace o známých chyb a jejich řešení.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Sada SDK Azure Machine Learning pro Python v 1.0.60

+ **Nové funkce**
  + Byl představen objekt DataSet, který odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Soubory mohou být libovolného formátu. Soubor DataSet poskytuje možnost stahovat soubory nebo je připojit k výpočetnímu prostředí. Pokud se chcete dozvědět o datové sadě, https://aka.ms/file-dataset navštivte prosím.
  + Přidání podpory kanálu YAML pro krok PythonScript, krok adla, krok datacihly, DataTransferStep a AzureBatch

+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + AutoArima je teď navržený kanál jenom pro náhled.
    + Vylepšené zasílání zpráv o chybách pro prognózování.
    + Vylepšené protokolování pomocí vlastních výjimek namísto obecného v úlohách prognózy.
    + Odebrání kontroly na max_concurrent_iterations je menší než celkový počet iterací.
    + Modely AutoML nyní vracejí AutoMLExceptions
    + Tato verze vylepšuje výkon spuštění automatizovaného místního spuštění machine learningu.
  + **azureml-core**
    + Zaveďte `Dataset.get_all()` , který vrátí `TabularDataset` slovník a `FileDataset` objekty s klíčem podle názvu registrace. 
    
    ```py 
    workspace = Workspace.from_config() 
    all_datasets = Dataset.get_all(workspace) 
    mydata = all_datasets['my-data'] 
    ```
    
    + Zaveďte `parition_format` jako argument `Dataset.Tabular.from_delimited_files` do `Dataset.Tabular.from_parquet.files`a. Informace o oddílu každé cesty k datům budou extrahovány do sloupců v závislosti na zadaném formátu. {column_name} vytvoří řetězcový sloupec a {column_name: RRRR/MM/DD/HH/MM/SS} vytvoří sloupec data a času, kde ' yyyy ', ' MM ', ' DD ', ' HH ', ' mm ' a ' ss ' slouží k extrakci roku, měsíce, dne, hodin, minut a sekund pro typ DateTime. Partition_format by měl začít od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu. /USA/2019/01/01/data.csv ', kde je oddíl podle země a čas, partition_format = '/{Country}/{PartitionDate: RRRR/MM/DD}/data. csv ' vytvoří řetězcový sloupec ' země ' s hodnotou ' USA ' a sloupec DateTime ' PartitionDate ' s hodnotou ' 2019-01-01 '.
    + `to_csv_files`metody `to_parquet_files` a byly přidány do `TabularDataset`. Tyto metody umožňují převod mezi `TabularDataset` `FileDataset` a a pomocí převodu dat do souborů v zadaném formátu.
    + Při ukládání souboru Dockerfile generovaného modelem. Package () se automaticky přihlaste k registru základní image.
    + ' gpu_support ' už není potřeba; AzureML nyní automaticky detekuje a používá rozšíření Docker, když je k dispozici. V budoucí verzi se odebere.
    + Přidala se podpora pro vytváření, aktualizaci a používání PipelineDrafts.
    + Tato verze vylepšuje výkon spuštění automatizovaného místního spuštění machine learningu.
    + Uživatelé můžou zadávat dotazy na metriky z historie spuštění podle názvu.
    + Vylepšené protokolování pomocí vlastních výjimek namísto obecného v úlohách prognózy.
  + **azureml-explain-model**
    + Přidání parametru feature_maps do nového MimicWrapperu, který umožňuje uživatelům získat hrubou Vysvětlení funkcí.
    + Nahrávání datových sad je teď ve výchozím nastavení vypnuté pro nahrání vysvětlení a dá se znovu povolit s upload_datasets = true.
    + Přidání parametrů filtrování "is_law" do seznamu vysvětlení a funkce stažení.
    + Přidá metodu `get_raw_explanation(feature_maps)` do globálních i lokálních objektů vysvětlení.
    + Přidání kontroly verze do lightgbm s vytištěným upozorněním, pokud je nižší než podporovaná verze
    + Optimalizované využití paměti při dávkování vysvětlení
    + Modely AutoML nyní vracejí AutoMLExceptions
  + **azureml-pipeline-core**
    + Přidání podpory pro vytváření, aktualizaci a používání PipelineDrafts-lze použít k údržbě proměnlivých definic kanálu a jejich interaktivnímu používání.
  + **azureml-train-automl**
    + Vytvořená funkce pro instalaci konkrétních verzí pytorch v 1.1.0, CUDA Toolkit 9,0, pytorch-transformátorů, které jsou nutné k povolení BERT/XLNet ve vzdáleném prostředí Python runtime.
  + **azureml-train-core**
    + Prvotní selhání některých chyb definice prostoru s parametry přímo v sadě SDK namísto na straně serveru.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Sada Azure Machine Learning data PREP SDK v 1.1.14
+ **Opravy chyb a vylepšení**
  + Umožnění psaní do ADLS/ADLSGen2 s použitím nezpracovaných cest a přihlašovacích údajů.
  + Opravili jsme chybu, `include_path=True` která způsobila `read_parquet`nefunkčnost.
  + Opravila `to_pandas_dataframe()` se chyba způsobená výjimkou: Neplatná hodnota vlastnosti: hostSecret.
  + Opravili jsme chybu, kdy se soubory v DBFS v režimu Spark nepodařilo přečíst.
  
## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Sada SDK Azure Machine Learning pro Python v 1.0.57
+ **Nové funkce**
  + Povoleno `TabularDataset` , aby je bylo možné spotřebovat pomocí AutomatedML. Pokud se chcete dozvědět `TabularDataset`víc, navštivte https://aka.ms/azureml/howto/createdatasets prosím.
  
+ **Opravy chyb a vylepšení**
  + **automl-Client-Core-nativeclient**
    + Opravili jsme chybu, která se vyvolala při výuce a/nebo ověřovací popisky (y a y_valid), a to ve formě PANDAS dataframe, ale ne jako pole numpy.
    + Rozhraní bylo aktualizováno pro vytvoření `RawDataContext` , aby vyžadovalo pouze data `AutoMLBaseSettings` a objekt.
    +  Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování. – Povolí uživatelům AutoML odstranit zrna ze sady testů, které neexistují v školicí sadě při prognózování.
  + **azure-cli-ml**
    + Certifikát SSL pro vyhodnocování koncového bodu, který je nasazený v clusteru AKS, teď můžete aktualizovat pro Microsoft vygenerovaný i certifikát zákazníka.
  + **AzureML-automl – jádro**
    + Opravili jsme problém v AutoML, kde se řádky s chybějícími popisky neodebraly správně.
    + Vylepšené protokolování chyb v AutoML; všechny chybové zprávy se teď vždycky zapisují do souboru protokolu.
    + AutoML aktualizovala své připnutí balíčku tak `azureml-defaults`, `azureml-explain-model`aby zahrnovala, a `azureml-dataprep`. AutoML už nebude upozorňovat na neshody balíčků (s výjimkou `azureml-train-automl` balíčku).
    + Opravili jsme problém v časové řady, kde CV odděluje nestejnou velikost způsobující selhání výpočtu bin.
    + Pokud se při spuštění iterace kompletu pro školicí typ pro křížové ověření objevilo potíže při stahování modelů vyškolených na celou datovou sadu, máme nekonzistenci mezi závažím modelu a modely, které byly dodány do hlasování. stromů.
    + Opravili jsme chybu, která se vyvolala při výuce a/nebo ověřovací popisky (y a y_valid), a to ve formě PANDAS dataframe, ale ne jako pole numpy.
    + Opravili jsme problém s prognózou úkolů v případě, že v logických sloupcích vstupních tabulek nebyl žádný nalezen.
    + Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování. – Povolí uživatelům AutoML odstranit zrna ze sady testů, které neexistují v školicí sadě při prognózování.
  + **azureml-core**
    + Vyřešil se problém s řazením parametrů blob_cache_timeout.
    + Do systémových chyb byly přidány typy výjimek, které se vejdou a transformují.
    + Přidání podpory pro Key Vault tajných kódů pro vzdálené běhy Přidejte do trezoru klíčů přidruženého k pracovnímu prostoru třídu AzureML. Core. datatrezor. Podporované operace:
      + AzureML. Core. Workspace. Workspace. Get _default_keyvault ()
      + AzureML. Core. klíčů trezor. set _secret (název; hodnota)
      + AzureML. Core. trezor. set _secrets (secrets_dict)
      + AzureML. Core. klíčů trezor. Get _secret (název)
      + AzureML. Core. klíčů trezor. Get _secrets (secrets_list)
      + AzureML. Core. klíčů trezor. list_secrets ()
    + Další metody získání výchozího trezoru klíčů a získání tajných kódů při vzdáleném spuštění:
      + AzureML. Core. Workspace. Workspace. Get _default_keyvault ()
      + AzureML. Core. Run. Run. Get _secret (název)
      + AzureML. Core. Run. Run. Get _secrets (secrets_list)
    + Přidání dalších parametrů přepsání do příkazu příkazového řádku pro odeslání – HyperDrive
    + Zlepšení spolehlivosti volání rozhraní API při opakovaných pokusech o výjimky knihovny pro běžné požadavky.
    + Přidání podpory pro odeslání spuštění z odeslaného běhu.
    + Opravili jsme problém s tokenem SAS vypršení platnosti ve sledovacím procesu, který způsobil, že se soubory zastavily po vypršení platnosti počátečního tokenu.
    + Podporuje se import souborů HTTP CSV/TSV v sadě DataSet Python SDK.
    + Zastaralá metoda pracovního prostoru. Setup (). Zpráva s upozorněním, která se zobrazuje uživatelům, navrhuje použít místo toho Create () nebo Get ()/from_config ().
    + Přidání prostředí. Přidejte _private_pip_wheel (), které umožňuje nahrávání privátních vlastních balíčků Pythonu (. WHL) do pracovního prostoru a jejich bezpečné použití pro sestavení nebo vyhodnotit prostředí.
    + Certifikát SSL pro vyhodnocování koncového bodu, který je nasazený v clusteru AKS, teď můžete aktualizovat pro Microsoft vygenerovaný i certifikát zákazníka.
  + **azureml-explain-model**
    + Přidání parametru pro přidání ID modelu do vysvětlení při nahrávání.
    + Přidání `is_raw` značek k vysvětlení v paměti a nahrání.
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
    + Nyní AutoMLStep prostřednictvím automl config do back-endu, aby se předešlo jakýmkoli problémům při změnách nebo přidávání nových parametrů konfigurace.
    + AutoML data Guardrail je teď ve verzi Public Preview. Po školení se uživateli zobrazí sestava Guardrail dat (pro úlohy klasifikace/regrese) a také k ní bude mít přístup prostřednictvím rozhraní SDK API.
  + **azureml-train-core**
    + Přidala se podpora Torch 1,2 v PyTorch Estimator.
  + **AzureML – widgety**
    + Vylepšené maticové grafy pro školení klasifikace.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Sada Azure Machine Learning data PREP SDK v 1.1.12
+ **Nové funkce**
  + Seznamy řetězců lze nyní předat jako vstup do `read_*` metod.

+ **Opravy chyb a vylepšení**
  + Výkon aplikace `read_parquet` byl výrazně vylepšen při spuštění ve Sparku.
  + Opravili jsme problém `column_type_builder` , kdy se nezdařil v případě jednoho sloupce s nejednoznačnými formáty data.

### <a name="azure-portal"></a>Azure Portal
+ **Funkce Preview**
  + Streamování protokolů a výstupních souborů je teď k dispozici na stránkách s podrobnostmi o spuštění. Když je zapnutý přepínač Preview, soubory se budou v reálném čase aktualizovat.
  + Možnost nastavit kvótu na úrovni pracovního prostoru je vydaná ve verzi Preview. Kvóty AmlCompute se přidělují na úrovni předplatného, ale teď vám umožňuje distribuovat tuto kvótu mezi pracovními prostory a přidělit je pro spravedlivé sdílení a zásady správného řízení. Stačí kliknout na okno **používání a kvóty** v levém navigačním panelu pracovního prostoru a vybrat kartu **Konfigurace kvót** . Všimněte si, že musíte být správcem předplatného, aby bylo možné nastavit kvóty na úrovni pracovního prostoru, protože se jedná o operaci mezi pracovními prostory.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Sada SDK Azure Machine Learning pro Python v 1.0.55

+ **Nové funkce**
  + Ověřování na základě tokenu se teď podporuje pro volání na koncový bod nasazené na AKS. Budeme dál podporovat aktuální ověřování založené na klíčích a uživatelé můžou používat jeden z těchto ověřovacích mechanismů současně.
  + Možnost Registrovat úložiště objektů blob, které je za virtuální sítí (VNet) jako úložiště dat.
  
+ **Opravy chyb a vylepšení**
  + **AzureML-automl – jádro**
    + Opravuje chybu, při které je velikost ověřování pro rozdělení CV malá a výsledkem je špatný odhad vs. opravdové grafy pro regresi a předpovědi.
    + V případě, že došlo k chybě při spuštění, je k dispozici protokolování úloh prognózování na vzdálené běhy. nyní se uživateli zobrazí komplexní chybová zpráva.
    + Opravené chyby časové řady, pokud je příznak předběžného zpracování true.
    + Provedli jsme některé chybové zprávy při ověřování dat předpovědi, které jsou nenapadnutelné.
    + Snížená spotřeba paměti AutoML se spouští vyřazením a/nebo opožděným načítáním datových sad, zejména mezi vytvářením procesů.
  + **AzureML-contrib-vysvětlený model**
    + Přidání příznaku model_task pro vysvětlení, aby uživatel mohl přepsat výchozí automatickou logiku odvození pro typ modelu
    + Změny widgetů: Automaticky se instaluje s contrib, žádné další vysvětlení nbextension Install/Enable-support s důležitou funkcí důležitost (například Permutative).
    + Změny řídicího panelu: – box vykresluje a Violin vykresluje kromě beeswarm vykreslení na stránce souhrnu – mnohem rychlejší převykreslování beeswarm vykreslovat při změně posuvníku nahoře – užitečná zpráva s vysvětlením, jak se počítá shora k vypočítaným chybovým zprávám v místě grafů, když nejsou k dispozici žádná data.
  + **azureml-core**
    + Přidání metody model. Package () pro vytváření imagí Docker a fázemi, které zapouzdřují modely a jejich závislosti.
    + Místní služby WebService se aktualizovaly, aby přijímaly InferenceConfigs obsahující objekty prostředí.
    + Pevný model. Register () vytvářející neplatné modely, pokud '. ' (pro aktuální adresář) se předává jako parametr model_path.
    + Přidat rutinu run. submit_child, funkce, které experimentují. odeslat při určení spustit jako nadřazeného objektu odeslaného podřízeného spuštění.
    + Podpora možností konfigurace z modelu. Zaregistrujte se v běhu. register_model.
    + Možnost spouštět v existujícím clusteru úlohy JAR.
    + Nyní podporují parametry instance_pool_id a cluster_log_dbfs_path.
    + Přidání podpory pro použití objektu prostředí při nasazování modelu na webovou službu. Objekt prostředí se teď dá zadat jako součást objektu InferenceConfig.
    + Přidání mapování appinsifht pro nové oblasti – centralus-westus-northcentralus
    + Přidali jsme dokumentaci pro všechny atributy ve všech třídách úložiště dat.
    + Do se `Datastore.register_azure_blob_container`přidal parametr blob_cache_timeout.
    + Do AzureML. Core. Environment. Environment se přidaly metody save_to_directory a load_from_directory.
    + Do CLI se přidaly příkazy AZ ml Environment download a AZ ml Environment Register.
    + Bylo přidáno prostředí. Přidejte metodu _private_pip_wheel.
  + **azureml-explain-model**
    + Přidání sledování datové sady k vysvětlení pomocí služby DataSet (Preview).
    + Při streamování globálních vysvětlení z 10 000 na 100 se snížila výchozí velikost dávky.
    + Přidání příznaku model_task pro vysvětlení, aby uživatel mohl přepsat výchozí automatickou logiku odvození pro typ modelu.
  + **azureml-mlflow**
    + Opravená chyba v mlflow. AzureML. build_image, kde jsou vnořené adresáře ignorovány.
  + **azureml-pipeline-steps**
    + Přidání možnosti spouštět úlohy JAR v existujícím clusteru Azure Databricks.
    + Přidali jsme podporu instance_pool_id a parametry cluster_log_dbfs_path pro krok DatabricksStep.
    + Přidání podpory pro parametry kanálu v kroku DatabricksStep
  + **azureml-train-automl**
    + Přidání docstrings pro soubory související s kompletem.
    + Aktualizované dokumenty do vhodnějšího jazyka pro `max_cores_per_iteration` a`max_concurrent_iterations`
    + V případě, že došlo k chybě při spuštění, je k dispozici protokolování úloh prognózování na vzdálené běhy. nyní se uživateli zobrazí komplexní chybová zpráva.
    + Z poznámkového bloku automlstep kanálu se odebraly get_data.
    + Začalo podpora dataprep v automlstep.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Sada Azure Machine Learning data PREP SDK v 1.1.10

+ **Nové funkce**
  + Nyní můžete požádat o spuštění konkrétních kontrolorů (například histogram, bodový graf atd.) pro konkrétní sloupce.
  + Do se `append_columns`přidal argument paralelizovat. Pokud má hodnotu true, data se načtou do paměti, ale spuštění se spustí paralelně. Pokud je hodnota false, bude spuštění streamování, ale s jedním vláknem.

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
    + Přidání podpory pro Nimbus ML odhady & kanálů pro použití v rámci AutoML odhady.
    + Oprava chyby v postupu výběru kompletu, která nemusela zbytečně rozšiřovat výsledný komplet, i když skóre zůstávala konstantní.
    + Povolí opětovné použití některých featurizations u rozdělení CV pro úlohy předpovědi. Tím se zrychluje spuštění instalačního programu zhruba faktorem n_cross_validations pro nákladné featurizations, jako jsou prodlevy a Windows.
    + Řešení problému, pokud je čas mimo PANDAS podporovaný časový rozsah. V případě, že je čas menší než PD, teď vyvoláme výjimku. Timestamp. min nebo větší než PD Timestamp. max
    + Prognózování teď umožňuje různé frekvence v vlakových a testovacích sadách, pokud je jde zarovnávat. Například "čtvrtletní zahájení v lednu" a "čtvrtletní začátek v říjnu" lze zarovnávat.
    + Do TimeSeriesTransformer se přidala vlastnost Parameters.
    + Odeberte staré třídy výjimek.
    + V úkolech `target_lags` prognózy teď parametr přijímá jednu celočíselnou hodnotu nebo seznam celých čísel. Pokud bylo zadáno celé číslo, bude vytvořena pouze jedna prodleva. Pokud je k dispozici seznam, budou provedeny jedinečné hodnoty prodlevy. target_lags = [1, 2, 2, 4] vytvoří prodlevy jednu, 2 a 4 tečky.
    + Opravte chybu týkající se ztráty typů sloupců po transformaci (propojená chyba);
    + V `model.forecast(X, y_query)`nástroji povolte, aby y_query bylo typu objektu, který neobsahuje žádné (#459519) na začátku ().
    + Přidání očekávaných hodnot do výstupu automl
  + **AzureML-contrib – dataunášená**
    +  Vylepšení ukázkového poznámkového bloku, včetně přepnutí do programu AzureML-opendatasets namísto funkcí AzureML-contrib-opendatasets a zlepšení výkonu při rozšiřování dat
  + **AzureML-contrib-vysvětlený model**
    + Pevné transformace argumentu pro nástroj pro vysvětlení VÁPNa pro nezpracovaný význam funkcí v balíčku AzureML-contrib-vysvětlit-model
    + Přidání segmentů k vysvětlení obrázků v popisu obrázku pro balíček AzureML-contrib-vysvětlit-model
    + Přidání podpory zhuštění scipy pro LimeExplainer
    + Přidat batch_size pro napodobení vysvětlení, když include_local = false pro globální vysvětlení streamování v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel
  + **AzureML-contrib-featureengineering**
    + Oprava pro volání set_featurizer_timeseries_params (): Změna typu hodnoty dict – a nulová check-přidat Poznámkový blok pro časové řady featurizer
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
  + **azureml-core**
    + Přidání možnosti připojit úložiště DBFS v rozhraní příkazového řádku AzureML 
    + Opravili jsme chybu pomocí nahrávání úložiště dat, kde se vytvoří prázdná složka `target_path` , pokud je spuštěná s`/`
    + Opravený problém DeepCopy v ServicePrincipalAuthentication.
    + Do CLI se přidaly příkazy AZ ml Environment show a AZ ml Environment list.
    + Prostředí teď podporují zadání base_dockerfile jako alternativy k již vytvořeným base_image.
    + Nepoužívané nastavení RunConfiguration auto_prepare_environment bylo označeno jako zastaralé.
    + Popis modelu se teď dá aktualizovat po registraci.
    + Opravu chyb Odstranění modelu a obrázku nyní poskytuje další informace o načítání nadřazených objektů, které jsou na nich závislé, pokud odstranění selhalo z důvodu nadřazené závislosti.
    + Opravili jsme chybu, která vytiskla prázdné trvání pro nasazení, ke kterým dochází při vytváření pracovního prostoru pro některá prostředí.
    + Vylepšené výjimky při vytváření pracovních prostorů Tato možnost se uživatelům nezobrazí "nelze vytvořit pracovní prostor". Nejde najít... jako zpráva a místo toho se zobrazí skutečná Chyba při vytváření.
    + Přidání podpory pro ověřování tokenu v AKS WebServices. 
    + Přidejte `get_token()` metodu do `Webservice` objektů.
    + Přidala se podpora rozhraní příkazového řádku pro správu datových sad Machine Learning.
    + `Datastore.register_azure_blob_container`teď může `blob_cache_timeout` volitelně přetrvat hodnotu (v sekundách), která nakonfiguruje parametry připojení blobfuse, aby se povolilo vypršení platnosti mezipaměti pro toto úložiště dat. Výchozí hodnota není žádný časový limit, tj. když je objekt BLOB načtený, zůstane v místní mezipaměti, dokud není úloha dokončena. Většina úloh bude preferovat toto nastavení, ale některé úlohy potřebují číst více dat z velké datové sady, než se vejde do jejich uzlů. Pro tyto úlohy ladění tohoto parametru pomůže úspěšně. Při vyladění tohoto parametru se ujistěte, že nastavení příliš nízké hodnoty může mít za následek špatný výkon, protože data používaná v epocha můžou vypršet, než se znovu použijí. To znamená, že všechny čtení se budou provádět ze služby Blob Storage (tj. sítě) místo z místní mezipaměti, což negativně ovlivní dobu školení.
    + Popis modelu se teď dá po registraci správně aktualizovat.
    + Odstranění modelu a obrázku nyní poskytuje další informace o nadřazených objektech, které jsou na nich závislé, což způsobí selhání odstranění.
    + Vylepšete využití prostředků ve vzdálených spuštěních pomocí AzureML. mlflow.
  + **azureml-explain-model**
    + Pevné transformace argumentu pro nástroj pro vysvětlení VÁPNa pro nezpracovaný význam funkcí v balíčku AzureML-contrib-vysvětlit-model
    + Přidání podpory zhuštění scipy pro LimeExplainer
    + přidala se obálka Shap lineárního vysvětlení a také další úroveň v tabulce vysvětlující vysvětlující lineární modely.
    + pro napodobení napodobení v knihovně modelů vysvětluje, že při include_local = false pro zhuštěné datové vstupy je Opravená chyba.
    + Přidání očekávaných hodnot do výstupu automl
    + pevná funkce permutace důležitost při zadání argumentu Transformers pro získání důležité funkce
    + Přidat batch_size pro napodobení vysvětlení, když include_local = false pro globální vysvětlení streamování v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel
    + v případě knihovny pro vysvětlení modelu jsou k disblackbox vysvětlující, kde je pro předpověď vyžadován vstup PANDAS dataframe.
    + Opravili jsme chybu `explanation.expected_values` , kde by někdy vracela float místo seznamu s plovoucí desetinnou čárkou.
  + **azureml-mlflow**
    + Zvýšení výkonu mlflow. set _experiment (experiment_name)
    + Oprava chyby při použití InteractiveLoginAuthentication pro mlflow tracking_uri
    + Vylepšete využití prostředků ve vzdálených spuštěních pomocí AzureML. mlflow.
    + Zlepšení dokumentace k balíčku AzureML-mlflow
    + Oprava chyby, kde mlflow. log _artifacts ("my_dir") by ukládala artefakty v rámci ">/< artefakt-Paths" místo "< artefakt-Paths >"
  + **azureml-opendatasets**
    + Připnout pyarrow z opendatasets na starší verze (< 0.14.0) z důvodu chyby nově zavedené paměti.
    +  Přesuňte AzureML-contrib-opendatasets do AzureML-opendatasets. – Povolí, aby se třídy Open DataSet zaregistrovaly do pracovního prostoru AML a mohli hladce využívat funkce AML DataSet. – Zlepšení výkonu NoaaIsdWeather obohacení ve verzi mimo SPARK
  + **azureml-pipeline-steps**
    + Úložiště dat DBFS se teď podporuje pro vstupy a výstupy v DatabricksStep.
    + Aktualizovaná dokumentace pro Azure Batch krok s ohledem na vstupy a výstupy.
    + V AzureBatchStep se změnila výchozí hodnota *delete_batch_job_after_finish* na *true*.
  + **AzureML-telemetrie**
    +  Přesuňte AzureML-contrib-opendatasets do AzureML-opendatasets. – Povolí, aby se třídy Open DataSet zaregistrovaly do pracovního prostoru AML a mohli hladce využívat funkce AML DataSet. – Zlepšení výkonu NoaaIsdWeather obohacení ve verzi mimo SPARK
  + **azureml-train-automl**
    + Aktualizovala se dokumentace na get_output, která odráží skutečný návratový typ a poskytuje další poznámky k načítání vlastností klíče.
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + Přidání očekávaných hodnot do výstupu automl
  + **azureml-train-core**
    + Řetězce se teď akceptují jako cíl COMPUTE pro automatizované ladění parametrů.
    + Nepoužívané nastavení RunConfiguration auto_prepare_environment bylo označeno jako zastaralé.

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
    + Tento nový balíček umožňuje registrovat otevřené datové sady jako datovou sadu v pracovním prostoru AML a využít jakékoli funkce, které sada dat nabízí.
    + Zahrnuje taky existující funkce, jako je například využívání otevřených datových sad jako PANDAS/SPARK dataframes, a spojení umístění pro určitou datovou sadu, jako je počasí.

+ **Funkce ve verzi Preview**
    + HyperDriveConfig může nyní přijímat objekt kanálu jako parametr pro podporu ladění parametrů pomocí kanálu.

+ **Opravy chyb a vylepšení**
  + **azureml-train-automl**
    + Opravili jsme chybu týkající se ztráty typů sloupců po transformaci.
    + Opravili jsme chybu, aby bylo možné y_query jako typ objektu, který neobsahuje žádné (y) na začátku. 
    + Opravili jsme problém v postupu výběru kompletu, který nemusel nutně rozšiřovat výsledný komplet, i když skóre zůstávala konstantní.
    + Opravili jsme problém s nastavením whitelist_models a blacklist_models v AutoMLStep.
    + Opravili jsme problém, který zabránil použití předběžného zpracování, pokud by se AutoML použil v kontextu kanálů Azure ML.
  + **azureml-opendatasets**
    + Byl přesunut příkaz AzureML-contrib-opendatasets do procesu AzureML-opendatasets.
    + Povolené otevřené třídy DataSet budou registrovány v pracovním prostoru AML a hladce využívat funkce AML DataSet.
    + Vylepšený výkon NoaaIsdWeather obohacen v jiné verzi než SPARK.
  + **azureml-explain-model**
    + Aktualizovaná online dokumentace pro objekty interpretace.
    + Přidali jsme batch_size k napodobení vysvětlujícíi, když include_local = false pro globální vysvětlení streamování v dávkách, aby se zlepšila doba provádění DecisionTreeExplainableModel.
    + Opravili jsme problém `explanation.expected_values` , kdy by někdy vracel místo seznamu s plovoucí desetinnou čárkou.
    + Přidání očekávaných hodnot do automl výstupu pro vysvětlení v knihovně vysvětlení modelu.
    + Pevná funkce permutace důležitost při zadání argumentu Transformers, který má za problém získat důležitost nezpracovaných funkcí
    + Přidali jsme batch_size k napodobení vysvětlujícíi, když include_local = false pro globální vysvětlení streamování v dávkách, aby se zlepšila doba provádění DecisionTreeExplainableModel pro knihovnu pro vysvětlení modelu.
  + **azureml-core**
    + Do příkazového řádku AzureML se přidala možnost připojit úložiště DBFS.
    + Opravili jsme problém s nahráváním úložiště dat, kde je vytvořená `target_path` prázdná složka `/`, pokud je spuštěná s.
    + Je povolené porovnání dvou datových sad.
    + Odstranění modelu a obrázku nyní poskytuje další informace o načítání nadřazených objektů, které jsou na nich závislé, pokud odstranění selhalo z důvodu nadřazené závislosti.
    + Nepoužívané nastavení RunConfiguration v auto_prepare_environment se nepoužívá.
  + **azureml-mlflow**
    + Vylepšené využití prostředků ve vzdálených spuštěních, které používají AzureML. mlflow.
    + Vylepšili dokumentaci k balíčku AzureML-mlflow.
    + Opravili jsme problém, kdy mlflow. log _artifacts ("my_dir") uloží artefakty v rámci "my_dir/artefakt-Paths" místo "artefakt-Paths".
  + **azureml-pipeline-core**
    + Parametr hash_paths pro všechny kroky kanálu je zastaralý a v budoucnu se odebere. Ve výchozím nastavení je obsah source_directory hash (s výjimkou souborů uvedených v souboru. amlignore nebo. gitignore).
    + Pokračující zlepšování modulu a ModuleStep, aby podporovalo moduly specifické pro výpočetní typ, při přípravě na integraci RunConfiguration a další změny pro odemčení jejich používání v kanálech.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Vylepšená dokumentace s ohledem na vstupy a výstupy.
    + AzureBatchStep: Výchozí hodnota delete_batch_job_after_finish se změnila na true.
  + **azureml-train-core**
    + Řetězce jsou nyní přijímány jako cíl výpočtů pro automatizované ladění parametrů.
    + Nepoužívané nastavení RunConfiguration v auto_prepare_environment se nepoužívá.
    + Zastaralé parametry `conda_dependencies_file_path` a `pip_requirements_file_path` mají přednost `conda_dependencies_file` před a `pip_requirements_file` v uvedeném pořadí.
  + **azureml-opendatasets**
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Sada Azure Machine Learning data PREP SDK v 1.1.8

+ **Nové funkce**
 + Objekty toku dat se teď dají iterovat a vytvářejí sekvenci záznamů. Viz dokumentace pro `Dataflow.to_record_iterator`.

+ **Opravy chyb a vylepšení**
 + Zvýšila se odolnost sady SDK pro sadu pro dataprep.
 + Vylepšené zpracování pandasch datarámečcích pomocí neřetězcových indexů sloupců.
 + Vylepšili jsme výkon `to_pandas_dataframe` v datových sadách.
 + Opravili jsme chybu, při které se spuštění rutiny Sparku u datových sad nezdařilo v prostředí s více uzly.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Sada Azure Machine Learning data PREP SDK v 1.1.7

Změnili jsme změnu, která zlepšila výkon, protože způsobila problémům pro některé zákazníky, kteří používají Azure Databricks. Pokud jste narazili na problém s Azure Databricks, můžete upgradovat na verzi 1.1.7 pomocí jedné z níže uvedených metod:
1. Spusťte tento skript k upgradu:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Znovu vytvořte cluster, který nainstaluje nejnovější verzi sady SDK pro přípravu dat.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Sada SDK Azure Machine Learning pro Python v 1.0.45

+ **Nové funkce**
  + Přidat náhradní model rozhodovacího stromu pro napodobení vysvětlení v balíčku AzureML-vysvětlit-model
  + Možnost zadat verzi CUDA, která se má nainstalovat na Image Inferencing Podpora pro CUDA 9,0, 9,1 a 10,0.
  + Informace o obrázcích Azure ML pro školení jsou nyní k dispozici v úložišti GitHub a [Dockerhubu](https://hub.docker.com/_/microsoft-azureml) [KONTEJNERů Azure ml](https://github.com/Azure/AzureML-Containers) .
  + Přidala se podpora rozhraní příkazového řádku pro plán kanálu. Pokud se chcete dozvědět víc, spusťte příkaz AZ ml Pipeline h.
  + Do konfigurace nasazení AKS webwebservice a rozhraní příkazového řádku se přidal vlastní parametr oboru názvů Kubernetes.
  + Zastaralá hash_paths parametr pro všechny kroky kanálu
  + Model. Register nyní podporuje registraci více jednotlivých souborů jako jeden model s použitím `child_paths` parametru.
  
+ **Funkce ve verzi Preview**
    + Vyhodnocování bodování teď můžou volitelně ukládat informace conda a PIP pro spolehlivější serializaci a deserializaci.
    + Oprava chyby pro selektor automatických funkcí
    + Aktualizovali jsme mlflow. AzureML. build_image na nové rozhraní API, opravy chyb vystavené novou implementací.

+ **Opravy chyb a vylepšení**
  + Z AzureML-Core se odebrala závislost paramiko. Přidání upozornění na zastarání pro starší metody připojení cíle výpočtů
  + Zvýšení výkonu rutiny run. create_children
  + Při napodobování s binárním klasifikátorem je možné vyřešit pořadí pravděpodobnosti, když se pro škálování hodnot Shap používá pravděpodobnost použití učitelů.
  + Vylepšené zpracování chyb a zpráva pro automatizované strojové učení. 
  + Opravili jsme problém s časovým limitem iterace pro automatizované strojové učení.
  + Vylepšili jsme výkon transformace časových řad pro automatizované strojové učení.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Sada Azure Machine Learning data PREP SDK v 1.1.6

+ **Nové funkce**
  + Přidané souhrnné funkce pro nejvyšší hodnoty (`SummaryFunction.TOPVALUES`) a dolní hodnoty (`SummaryFunction.BOTTOMVALUES`)

+ **Opravy chyb a vylepšení**
  + Významně vylepšil výkon `read_pandas_dataframe`.
  + Opravili jsme chybu, která `get_profile()` by způsobila selhání datového toku, který odkazuje na binární soubory.
  + K `set_diagnostics_collection()` dispozici, aby bylo možné program povolit nebo zakázat shromažďování telemetrie.
  + Změnilo se chování `get_profile()`. Hodnoty NaN jsou nyní ignorovány pro funkce min, střed_hodn, STD a Sum, které odpovídají chování PANDAS.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Sada SDK Azure Machine Learning pro Python v 1.0.43

+ **Nové funkce**
  + Azure Machine Learning teď nabízí špičkovou podporu pro oblíbené služby Machine Learning a data Analysis Framework Scikit-učení. Pomocí Estimator můžou uživatelé snadno naučit a nasazovat modely Scikit-učení. [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + Naučte se [spouštět ladění parametrů pomocí Scikit – Naučte se pomocí Hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Přidání podpory pro vytváření ModuleStep v kanálech spolu s třídami Module a ModuleVersion pro správu opakovaně použitelných výpočetních jednotek.
  + ACI WebServices teď podporují trvalé scoring_uri prostřednictvím aktualizací. Scoring_uri se změní z IP adresy na plně kvalifikovaný název domény. Popisek názvu DNS pro plně kvalifikovaný název domény je možné nakonfigurovat nastavením dns_name_label na deploy_configuration. 
  + Automatizované nové funkce strojového učení:
    + STL featurizer pro prognózování
    + Clustering KMeans je povolený pro rozmazání funkcí.
  + AmlCompute se schvalování kvót jenom rychleji. Nyní jsme automatizovaný proces schvalování vašich žádostí o kvótu v rámci prahové hodnoty. Další informace o tom, jak kvóty fungují, najdete v tématu [Správa kvót](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Funkce ve verzi Preview**
    + Integrace se sledováním [MLflow](https://mlflow.org) 1.0.0 prostřednictvím balíčku AzureML-MLflow ([ukázkových poznámkových bloků](https://aka.ms/azureml-mlflow-examples)).
    + Odešle Poznámkový blok Jupyter jako běh. [Referenční dokumentace k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Public Preview [detektoru pro přenos dat](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) prostřednictvím balíčku AzureML-contrib-dis ([ukázkových poznámkových bloků](https://aka.ms/azureml-datadrift-example)). Posun dat je jedním z hlavních důvodů, kdy přesnost modelu se v průběhu času snižuje. K tomu dochází, když se data obsluhovaná modelem v produkčním prostředí liší od dat, na která byl model vyškolený. AML data unášený detektor pomáhá zákazníkovi monitorovat posun dat a odesílat výstrahu vždy, když se zjistí posun. 

+ **Rozbíjející změny v**

+ **Opravy chyb a vylepšení**
  + RunConfiguration Load and Save podporuje zadání úplné cesty k souboru s úplným zpětným kompatibilitou pro předchozí chování.
  + Přidání do mezipaměti v ServicePrincipalAuthentication je ve výchozím nastavení vypnuté.
  + Povolí protokolování více zobrazovaných souborů pod stejným názvem metriky.
  + Třída modelu je nyní správně importovaná ze služby AzureML.`from azureml.core import Model`Core ().
  + V krocích `hash_path` kanálu je parametr teď zastaralý. Nové chování je source_directory hash Complete, s výjimkou souborů uvedených v souboru. amlignore nebo. gitignore.
  + V balíčcích kanálu se různé `get_all` metody `get_all_*` a metody `list` zastaraly, a to ve `list_*`prospěch a v uvedeném pořadí.
  + rutina AzureML. Core. Get _run již nevyžaduje Import tříd, než vrátí původní typ spuštění.
  + Opravili jsme problém, kdy některá volání aktualizace WebService neaktivovala aktualizaci.
  + Časový limit vyhodnocování u AKS WebServices by měl být mezi 5ms a 300000ms. Maximální povolený počet scoring_timeout_ms pro požadavky na bodování byl předaný z 1 min na 5 min.
  + Objekty LocalWebservice nyní mají `scoring_uri` a `swagger_uri` vlastnosti.
  + Z procesu uživatele se přesunuly vytvoření adresáře výstupů a výstupy adresáře pro odeslání. Byla povolena sada SDK historie spuštění v každém procesu uživatele. To by mělo vyřešit některé problémy synchronizace, ke kterým dochází v distribuovaném školicím běhu.
  + Název procesu AzureML napsaný z názvu uživatelského procesu teď bude zahrnovat název procesu (jenom pro distribuované školení) a PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Sada Azure Machine Learning data PREP SDK v 1.1.5

+ **Opravy chyb a vylepšení**
  + Pro interpretované hodnoty DateTime, které mají formát roku se dvěma číslicemi, se rozsah platných roků aktualizoval tak, aby se shodoval se systémem Windows. Rozsah byl změněn z 1930-2029 na 1950-2049.
  + Při čtení souboru a nastavení `handleQuotedLineBreaks=True` `\r` se bude považovat za nový řádek.
  + Opravili jsme chybu, `read_pandas_dataframe` která v některých případech způsobila selhání.
  + Vylepšený výkon `get_profile`.
  + Vylepšené chybové zprávy.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Sada Azure Machine Learning data PREP SDK v 1.1.4

+ **Nové funkce**
  + Nyní můžete použít následující funkce jazyka výrazů k extrakci a analyzování hodnot data a času na nové sloupce.
    + `RegEx.extract_record()`extrahuje prvky DateTime do nového sloupce.
    + `create_datetime()`vytvoří objekty DateTime z oddělených elementů DateTime.
  + Při volání `get_profile()`teď můžete vidět, že Quantile sloupce jsou označené jako (EST.), aby jasně označovaly, že se jedná o přibližné hodnoty.
  + Při čtení z Azure Blob Storage teď můžete použít * * expanzi názvů.
    + například.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Opravy chyb**
  + Opravili jsme chybu související s čtením souboru Parquet ze vzdáleného zdroje (Azure BLOB).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Sada SDK Azure Machine Learning pro Python v 1.0.39
+ **Provedeny**
  + Možnost spuštění konfigurace auto_prepare_environment je zastaralá a automatické připravování se stane výchozím nastavením.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Sada Azure Machine Learning data PREP SDK v 1.1.3

+ **Nové funkce**
  + Přidání podpory pro čtení z databáze PostgresSQL, a to buď voláním read_postgresql, nebo použitím úložiště dat.
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

### <a name="azure-portal"></a>portál Azure

V Azure Portal teď můžete:
+ Vytváření a spouštění automatizovaných experimentů ML 
+ Vytvořte virtuální počítač poznámkového bloku, abyste si vyzkoušeli ukázkové poznámkové bloky Jupyter nebo vaše vlastní.
+ Zcela nový oddíl pro vytváření obsahu (Preview) v pracovním prostoru služby Machine Learning, který zahrnuje automatizované Machine Learning, vizuální rozhraní a hostované virtuální počítače poznámkových bloků
    + Automatické vytvoření modelu pomocí automatizovaného strojového učení 
    + Použití vizuálního rozhraní pro přetahování myší ke spouštění experimentů
    + Vytvořte virtuální počítač poznámkového bloku pro zkoumání dat, vytváření modelů a nasazování služeb.
+ Aktualizace živého grafu a metriky v sestavách spuštění a na stránkách podrobností
+ Byl aktualizován prohlížeč souborů pro protokoly, výstupy a snímky na stránkách podrobností o spuštění.
+ Nové a vylepšené prostředí pro vytváření sestav na kartě experimenty. 
+ Byla přidána možnost stáhnout soubor config. JSON ze stránky přehled pracovního prostoru služby Azure Machine Learning.
+ Podpora vytvoření pracovního prostoru Machine Learning služby z pracovního prostoru Azure Databricks 

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
+ [Rychle aktivujte předem nakonfigurovaný virtuální počítač](tutorial-1st-experiment-sdk-setup.md) s poznámkovým blokem, který má nejnovější verzi sady Azure Machine Learning SDK a související balíčky.
+ Přístup je zabezpečený prostřednictvím prověřených technologií, jako je protokol HTTPS, Azure Active Directory ověřování a autorizace.
+ Spolehlivé cloudové úložiště poznámkových bloků a kódu v účtu služby pracovní prostor Azure Machine Learning BLOB Storage. Virtuální počítač poznámkového bloku můžete bezpečně odstranit, aniž byste ztratili práci.
+ Předinstalované ukázkové poznámkové bloky k prozkoumávání a experimentování s funkcemi Azure Machine Learning služby.
+ Úplné možnosti přizpůsobení virtuálních počítačů Azure, všech typů virtuálních počítačů, všech balíčků a všech ovladačů. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Sada Azure Machine Learning SDK pro Python v 1.0.33 vydaná.

+ Azure ML Modely s hardwarovou akcelerací v [FPGA](concept-accelerate-with-fpgas.md) je všeobecně dostupná.
  + Nyní můžete [použít balíček AzureML-akcelerace-Models](how-to-deploy-fpga-web-service.md) k těmto akcím:
    + Výuka vah podporované neuronové sítě (ResNet 50, ResNet 152, DenseNet-121, VGG-16 a SSD-VGG)
    + Využijte přenosové učení s podporovaným DNN
    + Zaregistrujte model pomocí služby Správa modelů Service a kontejnerizace model
    + Nasazení modelu do virtuálního počítače Azure s FPGA v clusteru Azure Kubernetes Service (AKS)
  + Nasazení kontejneru na zařízení [Azure Data box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) serveru
  + Určení skóre dat pomocí koncového bodu gRPC pomocí této [ukázky](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizované Machine Learning

+ Funkce, která umožňuje dynamické přidávání featurizers pro optimalizaci výkonu. New featurizers: vkládání pracovních verzí, váha legitimace, cílové kódování, kódování textu v cíli, vzdálenost clusteru
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

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) Bylo zavedeno přidání nové verze publikovaného kanálu se zachováním stejného koncového bodu.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Sada Azure Machine Learning data PREP SDK v 1.1.2

Poznámka: Sada SDK pro přípravu dat nebude nadále instalovat `numpy` a `pandas` balíčky. Viz [aktualizované pokyny k instalaci](https://aka.ms/aml-data-prep-installation).

+ **Nové funkce**
  + Nyní můžete použít transformaci pivotu.
    + Návod: [Kontingenční Poznámkový blok](https://aka.ms/aml-data-prep-pivot-nb)
  + Nyní můžete použít regulární výrazy v nativních funkcích.
    + Příklady:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Nyní `to_upper`můžete používat  funkce a `to_lower`vjazycevýrazů  .
  + V datovém profilu teď můžete zobrazit počet jedinečných hodnot každého sloupce.
  + U některých běžně používaných kroků čtečky teď můžete předat `infer_column_types` argument. Pokud je nastavená na `True`, aplikace data PREP se pokusí rozpoznat a automaticky převést typy sloupců.
    + `inference_arguments`je nyní zastaralé.
  + Nyní můžete zavolat `Dataflow.shape`.

+ **Opravy chyb a vylepšení**
  + `keep_columns` nyní přijímá další volitelný argument `validate_column_exists`, který kontroluje, zda `keep_columns` výsledek bude obsahovat všechny sloupce.
  + Všechny kroky čtecího zařízení (které se čtou ze souboru) nyní přijímají další `verify_exists`volitelný argument.
  + Vylepšený výkon při čtení z PANDAS dataframe a získávání profilů dat.
  + Opravili jsme chybu, kdy došlo k chybě průřezu jednoho kroku z toku dat s jedním indexem.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>portál Azure
  + Existující skript teď můžete znovu odeslat v existujícím vzdáleném výpočetním clusteru. 
  + Nyní můžete spustit publikovaný kanál s novými parametry na kartě kanály. 
  + Podrobnosti o spuštění teď podporují nový prohlížeč souborů snímků. Po odeslání konkrétního běhu můžete zobrazit snímek adresáře. Můžete si také stáhnout Poznámkový blok, který byl odeslán za účelem spuštění spuštění.
  + Nyní můžete zrušit nadřazená spuštění z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Sada SDK Azure Machine Learning pro Python v 1.0.23

+ **Nové funkce**
  + Sada Azure Machine Learning SDK teď podporuje Python 3,7.
  + Azure Machine Learning DNN odhady teď nabízí integrovanou podporu více verzí. Například `TensorFlow` `framework_version` Estimator nyní přijímá parametr a uživatelé mohou zadat verzi "1,10" nebo "1,12".   Seznam verzí podporovaných vaší aktuální verzí sady SDK je vyvolán `get_supported_versions()` na požadovanou třídu rozhraní ( `TensorFlow.get_supported_versions()`například).
  Seznam verzí podporovaných nejnovější verzí sady SDK najdete v [dokumentaci k DNN Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Sada Azure Machine Learning data PREP SDK v 1.1.1

+ **Nové funkce**
  + Pomocí transformací read_ * můžete číst více zdrojů dat, DataPath a DataReference sources.
  + Pokud chcete vytvořit nový sloupec, můžete na sloupcích provádět následující operace: dělení, podlah, modulo, napájení, délka.
  + Příprava dat je teď součástí sady diagnostiky Azure ML a ve výchozím nastavení bude protokolovat diagnostické informace.
    + Pro vypnutí nastavte tuto proměnnou prostředí na hodnotu true: DISABLE_DPREP_LOGGER

+ **Opravy chyb a vylepšení**
  + Vylepšená dokumentace kódu pro běžně používané třídy a funkce.
  + Opravili jsme chybu v auto_read_file, která selhala při čtení excelových souborů.
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
    + Návod: [Otevírá a ukládá se Poznámkový blok toku dat](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nové funkce**
  + Příprava dat teď může rozpoznat sloupce, které odpovídají konkrétnímu sémantickému typu, a odpovídajícím způsobem je rozdělit. STypes aktuálně podporuje: e-mailová adresa, geografické souřadnice (zeměpisná šířka & zeměpisná délka), adresy IPv4 a IPv6, telefonní číslo USA a PSČ USA.
    + Návod: [Poznámkový blok sémantických typů](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Příprava dat teď podporuje následující operace pro vygenerování výsledného sloupce ze dvou číselných sloupců: odečíst, vynásobení, dělení a modulo.
  + Můžete zavolat `verify_has_data()` na tok dat a ověřit, zda by tok dat při spuštění vytvářely záznamy.

+ **Opravy chyb a vylepšení**
  + Nyní můžete zadat počet přihrádek, které se mají použít v histogramu pro profily číselných sloupců.
  + `read_pandas_dataframe` Transformace teď vyžaduje, aby měl datový rámec názvy sloupců typu String nebo Byte.
  + Opravili jsme chybu v `fill_nulls` transformaci, kde nebyly správně vyplněné hodnoty, pokud sloupec chybí.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Sada SDK Azure Machine Learning pro Python v 1.0.18

 + **Provedeny**
   + Balíček AzureML-tensorboard nahrazuje. AzureML-contrib-tensorboard.
   + V této verzi můžete při vytváření amlcompute (Managed Compute Cluster) nastavit uživatelský účet. To lze provést předáním těchto vlastností v konfiguraci zřizování. Další podrobnosti najdete v [referenční dokumentaci k sadě SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Sada Azure Machine Learning data PREP SDK v 1.0.17

+ **Nové funkce**
  + Nyní podporuje přidání dvou číselných sloupců pro vygenerování výsledného sloupce pomocí jazyka výrazů.

+ **Opravy chyb a vylepšení**
  + Vylepšili jsme dokumentaci a kontrolu parametrů pro random_split.
  
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
  + Přidané kanály Azure Machine Learning aktivovat spuštění kanálu na základě úprav úložiště dat. [Poznámkový blok plánu](https://aka.ms/pl-schedule) kanálu se aktualizuje a prezentuje tuto funkci.

+ **Opravy chyb a vylepšení**
  + Přidali jsme podporu Azure Machine Learning kanálů pro nastavení vlastnosti source_directory_data_store na požadované úložiště dat (například úložiště objektů BLOB) na [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , které jsou dodány [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Ve výchozím nastavení se jako záložní úložiště dat používají úložiště souborů Azure, které může narazit na problémy s omezením při současném spuštění velkého počtu kroků.

### <a name="azure-portal"></a>portál Azure

+ **Nové funkce**
  + Nové možnosti editoru tabulek přetahování pro sestavy. Uživatelé mohou sloupec přetáhnout ze seznamu i do oblasti tabulky, kde se zobrazí náhled tabulky. Sloupce lze změnit jejich uspořádání.
  + Nový prohlížeč souborů protokolů
  + Odkazy na spuštění, výpočty, modely, image a nasazení z karty aktivity

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Sada Azure Machine Learning data PREP SDK v 1.0.15

+ **Nové funkce**
  + Příprava dat teď podporuje vytváření datových proudů souborů z datového toku. Také poskytuje možnost manipulovat s názvy datových proudů souborů a vytvořit nové názvy souborů.
    + Návod: [Práce s poznámkovým blokem Souborová streamy](https://aka.ms/aml-data-prep-file-stream-nb)

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
  + `PublishedPipeline.get_published_pipeline` Zastaralé namísto`PublishedPipeline.get`.
  + `Schedule.get_schedule` Zastaralé namísto`Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Sada Azure Machine Learning data PREP SDK v 1.0.12

+ **Nové funkce**
  + Data PREP teď podporuje čtení z databáze SQL Azure pomocí úložiště dat.
 
+ **Provedeny**
  + Vylepšený výkon u velkých objemů paměti u různých operací.
  + `read_pandas_dataframe()`Nyní je `temp_folder` nutné zadat.
  + Vlastnost on `ColumnProfile` je zastaralá – použijte `column_name` místo toho. `name`

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
    - Bodový
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
  + `to_bool`funkce teď umožňuje převést neshodné hodnoty na chybové hodnoty. Toto je nové výchozí chování při neshodě `to_bool` pro `set_column_types`a, zatímco předchozí výchozí chování převedlo neshodné hodnoty na false.
  + Při volání `to_pandas_dataframe`je k dispozici nová možnost pro interpretaci hodnoty null/chybějící v číselných sloupcích jako NaN.
  + Byla přidána možnost zkontrolovat návratový typ některých výrazů, aby byla zajištěna konzistence typů a předčasné selhání.
  + Nyní můžete zavolat `parse_json` na analyzovat hodnoty ve sloupci jako objekty JSON a rozbalovat je do více sloupců.

+ **Opravy chyb**
  + Opravili jsme chybu, která `set_column_types` byla chybou v Python 3.5.2.
  + Opravili jsme chybu, která selhala při připojování k úložišti dat pomocí Image AML.

+ **Aktualizace**
  * [Příklady poznámkových bloků](https://aka.ms/aml-data-prep-notebooks) pro úvodní kurzy, případové studie a návody.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Všeobecná dostupnost

Služba Azure Machine Learning je teď obecně dostupná.

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

+ **Nový**
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
  * Použijte nejnovější AML spustit Token při čtení a zápisu do úložiště o vzdálené spuštění. Dříve Pokud AML spustit Token se aktualizuje v Pythonu, modul runtime přípravy dat nebude aktualizována aktualizované AML spustit Token.
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
   * Může získat všechny datové typy sloupců z datového toku nebo datového profilu voláním`.dtypes`
   * Může získat počet řádků z datového toku nebo datového profilu voláním`.row_count`

+ **Opravy chyb**
   * Pevně long double převodu 
   * Oprava vyhodnocení po přidání sloupce 
   * Opravili jsme problém s FuzzyGrouping, kde ji nebude skupiny v některých případech rozpoznat
   * Opravené seřadit funkce dodržovat pořadí řazení více sloupců
   * Pevné a/nebo výrazy, které se mají podobat `pandas` způsobu jejich zpracování
   * Oprava čtení z dbfs cesty
   * Provedené pomohou lépe porozumět chybové zprávy 
   * Teď už selže při čtení na cílové vzdálené výpočetní prostředí pomocí tokenu AML
   * Teď už na Linuxu DSVM selže
   * Teď už dojde k chybě hodnoty jiné než řetězec jsou v predikátech řetězec
   * Nyní zpracovává kontrolního výrazu chyby při by měl správně selhání toku dat
   * Teď podporuje umístění dbutils připojené úložiště v Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>portál Azure 
Na webu Azure portal pro službu Azure Machine Learning má následující aktualizace:
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
  * Opravili jsme ensembling iterace problémů.
  * Oprava školení Změ chyb v systému MAC OS.
  * Převzorkování – makro průměrné žádosti o přijetí změn a křivka ROC ve scénáři vlastní ověřování.
  * Odebrat další index logiku.
  * Odebrali jsme filtr, z get_output rozhraní API.

+ **Kanály**
  * Přidat metodu Pipeline.publish() do kanálu publikovat přímo, bez nutnosti spuštění se spouští jako první.   
  * Přidala se metoda PipelineRun. Get _pipeline_runs () pro načtení spuštění kanálu, které byly vygenerovány z publikovaného kanálu.

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

Přečtěte si přehled [služby Azure Machine Learning Service](../service/overview-what-is-azure-ml.md).
