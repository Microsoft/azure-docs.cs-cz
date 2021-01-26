---
title: Co je nového ve verzi?
titleSuffix: Azure Machine Learning
description: Seznamte se s nejnovějšími aktualizacemi pro Azure Machine Learning a sady SDK pro strojové učení a přípravu dat v Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 09/10/2020
ms.openlocfilehash: 6e92fb39845944898bebf6446c35f0932e13b5b8
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788871"
---
# <a name="azure-machine-learning-release-notes"></a>Poznámky k verzi Azure Machine Learning

V tomto článku se dozvíte o Azure Machine Learning verzích.  Úplný referenční obsah sady SDK najdete na referenční stránce Azure Machine Learning [**hlavní sadě SDK pro Python**](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) .

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Sada SDK Azure Machine Learning pro Python v 1.21.0
+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Pevný text v nápovědě k rozhraní příkazového řádku při použití AmlCompute s identitou UserAssigned
  + **AzureML-contrib-automl-DNN-Vision**
    + Tlačítka pro nasazení a stažení se stanou viditelnými pro spuštění AutoMLch zraku a modely se můžou nasadit nebo stáhnout podobně jako ostatní AutoML běhy. Existují dva nové soubory (scoring_file_v_1_0_0. py a conda_env_v_1_0_0. yml), které obsahují skript pro spuštění Inferencing a soubor YML pro opětovné vytvoření prostředí conda. Soubor model. PTH byl také přejmenován, aby používal příponu. pt.
  + **azureml-core**
    + Podpora MSI pro Azure-CLI-ml
    + Podpora spravované identity přiřazené uživatelem
    + V důsledku této změny by zákazníci měli být schopni poskytnout identitu přiřazenou uživateli, kterou lze použít k načtení klíče z trezoru klíčů zákazníka pro šifrování v klidovém umístění.
    +  Oprava row_count = 0 pro profil velmi velkých souborů – Oprava chyby v dvojitém převodu pro hodnoty s oddělovači s odsazením mezer
    + Odebrání experimentálního příznaku pro výstupní datovou sadu GA
    + Aktualizace dokumentace k načtení konkrétní verze modelu
    + Umožňuje aktualizovat pracovní prostor pro přístup do smíšeného režimu pro případ privátního propojení.
    + Oprava pro odebrání další registrace v úložišti dat pro funkci pokračování v běhu
    + Přidání podpory rozhraní příkazového řádku/sady SDK pro aktualizaci identity přiřazené primárnímu uživateli v pracovním prostoru
  + **azureml-interpret**
    + aktualizace AzureML-interpretace na interpretaci 0.16.0 komunity
    + optimalizace paměti pro vysvětlení klienta v AzureML-interpretace
  + **azureml-train-automl-runtime**
    + Povolené streamování pro ADB běhy
  + **azureml-train-core**
    + Oprava pro odebrání další registrace v úložišti dat pro funkci pokračování v běhu
  + **azureml-widgets**
    + Zákazníci by neměli vidět změny existující vizualizace dat pomocí widgetu a teď budou mít podporu, pokud budou volitelně používat podmíněné parametry.
    + Pomůcka pro spuštění uživatele teď obsahuje podrobné vysvětlení, proč je běh ve stavu queueed.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Sada SDK Azure Machine Learning pro Python v 1.20.0
+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + framework_version přidáno v OptimizationConfig. Použije se při registraci modelu s architekturou MULTI.
  + **AzureML-contrib – optimalizace**
    + framework_version přidáno v OptimizationConfig. Použije se při registraci modelu s architekturou MULTI.
  + **azureml-pipeline-steps**
    + Představujeme CommandStep, který by provedl příkaz ke zpracování. Příkaz může obsahovat spustitelné soubory, příkazy prostředí, skripty atd.
  + **azureml-core**
    + Vytváření pracovního prostoru teď podporuje identitu přiřazenou uživateli. Přidání podpory UAI z SDK/CLI
    + Opravený problém služby. Load () pro výběr změn v score.py v místním nasazení.
    + `run.get_details()` obsahuje další pole s názvem "použít", které zobrazuje jméno autora pro tento běh.
    + Upravená dokumentace k metodě model. Register, která uvádí, jak registrovat model přímo v běhu
    + Opravený problém při zpracování změny stavu připojení IOT-Server.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Prostředí poznámkových bloků Azure Machine Learning Studio (aktualizace z prosince)
+ **Nové funkce**
  + Hledání uživatelského názvu. Uživatelé teď můžou vyhledat všechny soubory uložené v pracovním prostoru.
  + Markdownu se souběžná podpora na každou buňku poznámkového bloku. V buňce s poznámkovým blokem teď můžou uživatelé možnost Zobrazit vykreslenou syntaxi Markdownu a Markdownu vedle sebe.
  + Stavový řádek buňky Stavový řádek označuje, ve kterém stavu je buňka kódu, zda byla buňka úspěšně spuštěna a jak dlouho trvalo spuštění. 
   
+ **Opravy chyb a vylepšení**
  + Vylepšené doby načítání stránek
  + Vyšší výkon 
  + Vylepšená rychlost a spolehlivost jádra

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Sada SDK Azure Machine Learning pro Python v 1.19.0
+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Do AutoMLStep se přidala experimentální podpora testovacích dat.
    + Byla přidána počáteční implementace základní implementace funkce pro příjem testovací sady.
    + Odkazy na skriptu sklearn. externals. joblib se přesunuly na závislé přímo na joblib.
    + Zaveďte nový typ úlohy AutoML pro "Image-instance-segmentace".
  + **azureml-automl-runtime**
    + Byla přidána počáteční implementace základní implementace funkce pro příjem testovací sady.
    + Pokud všechny řetězce v textovém sloupci mají délku přesně 1 znak, TfIdf Word-gram featurizer nebude fungovat, protože jeho provádějících tokenizaci ignoruje řetězce s méně než 2 znaky. Aktuální Změna kódu umožní AutoML zpracování tohoto případu použití.
    + Zaveďte nový typ úlohy AutoML pro "Image-instance-segmentace".
  + **AzureML-contrib-automl-DNN-NLP**
    + Počáteční PR pro nový balíček DNN-NLP
  + **AzureML-contrib-automl-DNN-Vision**
    + Zaveďte nový typ úlohy AutoML pro "Image-instance-segmentace".
  + **AzureML-contrib-automl-Pipeline – kroky**
    + Tento nový balíček zodpovídá za vytváření kroků potřebných pro mnoho modelů, scénář výuky/odvozování. – Také přesune kód pro výuku a odvození do balíčku AzureML. Train. automl. Runtime, takže případné budoucí opravy budou automaticky dostupné prostřednictvím vydaných verzí prostředí.
  + **azureml-contrib-dataset**
    + Zaveďte nový typ úlohy AutoML pro "Image-instance-segmentace".
  + **azureml-core**
    + Byla přidána počáteční implementace základní implementace funkce pro příjem testovací sady.
    + Oprava upozornění odkazy XREF pro dokumentaci v balíčku AzureML-Core
    + Opravy řetězce doc pro funkci podpory příkazu v sadě SDK
    + Přidání vlastnosti Command do RunConfiguration Tato funkce umožňuje uživatelům spustit skutečný příkaz nebo spustitelné soubory ve výpočetním prostředí prostřednictvím sady AzureML SDK.
    + Uživatelé mohou odstranit prázdný experiment s daným ID tohoto experimentu.
  + **azureml-dataprep**
    + Přidala se podpora datové sady pro Spark sestavený pomocí Scala 2,12. Tím se přidá stávající podpora 2,11.
  + **azureml-mlflow**
    + AzureML-MLflow přidává bezpečné ochranné kryty ve vzdálených skriptech, aby nedocházelo k předčasnému ukončení odeslaných běhů.
  + **azureml-pipeline-core**
    + Opravili jsme chybu v nastavení výchozího kanálu pro koncový bod kanálu vytvořený prostřednictvím uživatelského rozhraní.
  + **azureml-pipeline-steps**
    + Do AutoMLStep se přidala experimentální podpora testovacích dat.
  + **azureml-tensorboard**
    + Oprava upozornění odkazy XREF pro dokumentaci v balíčku AzureML-Core
  + **azureml-train-automl-client**
    + Do AutoMLStep se přidala experimentální podpora testovacích dat.
    + Byla přidána počáteční implementace základní implementace funkce pro příjem testovací sady.
    + Zaveďte nový typ úlohy AutoML pro "Image-instance-segmentace".
  + **azureml-train-automl-runtime**
    + Byla přidána počáteční implementace základní implementace funkce pro příjem testovací sady.
    + Opravte výpočet nezpracovaných vysvětlení pro nejlepší model AutoML, pokud jsou modely AutoML vyškoleny pomocí nastavení validation_size.
    + Odkazy na skriptu sklearn. externals. joblib se přesunuly na závislé přímo na joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric () by se teď měly dokončit rychleji.
    + Vylepšené zpracování chyb v sadě HyperDrive SDK.
    +  Zastaraly se všechny třídy Estimator a využívají ScriptRunConfig ke konfiguraci spuštění experimentů. Zastaralé třídy zahrnují:
        + MMLBaseEstimator
        + Estimator
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Zastaralé použití Nccl a Gloo jako platných vstupních typů pro třídy Estimator ve prospěch použití PyTorchConfiguration s ScriptRunConfig.
    + Zastaralé použití MPI jako platného vstupního typu pro třídy Estimator namísto použití MpiConfiguration s ScriptRunConfig.
    + Přidání vlastnosti Command do RunConfiguration Tato funkce umožňuje uživatelům spustit skutečný příkaz nebo spustitelné soubory ve výpočetním prostředí prostřednictvím sady AzureML SDK.

    +  Zastaraly se všechny třídy Estimator a využívají ScriptRunConfig ke konfiguraci spuštění experimentů. Mezi zastaralé třídy patří: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + chainer + skriptu sklearn
    + Zastaralé použití Nccl a Gloo jako platného typu vstupu pro třídy Estimator namísto použití PyTorchConfiguration s ScriptRunConfig. 
    + Zastaralé použití MPI jako platného typu vstupu pro třídy Estimator namísto použití MpiConfiguration s ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Prostředí poznámkových bloků Azure Machine Learning Studio (listopad Update)
+ **Nové funkce**
   + Nativní terminál. Uživatelé teď budou mít přístup k integrovanému terminálu i k operaci Git prostřednictvím [integrovaného terminálu.](./how-to-run-jupyter-notebooks.md#terminal)
  + Duplicitní složka 
  + Náklady na vyřazení výpočtů 
  + Offline výpočetní Pylance 

+ **Opravy chyb a vylepšení**
  + Vylepšené doby načítání stránek
  + Vyšší výkon 
  + Vylepšená rychlost a spolehlivost jádra
  + Nahrávání velkých souborů. Nyní můžete odeslat soubor >95mb

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Sada SDK Azure Machine Learning pro Python v 1.18.0
+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    +  Vylepšené zpracování krátkých časových řad tím, že umožňují jejich odsazení pomocí Gaussovského šumu.
  + **azureml-automl-runtime**
    + Vyvolat ConfigException, pokud má sloupec DateTime hodnotu OutOfBoundsDatetime
    + Vylepšené zpracování krátkých časových řad tím, že umožňují jejich odsazení pomocí Gaussovského šumu.
    + Ujistěte se, že každý textový sloupec může využít transformaci typu char-gram s rozsahem n-gram na základě délky řetězců v tomto textovém sloupci.
    + Poskytování nezpracovaných Vysvětlení funkcí pro nejlepší režim pro AutoML experimenty spuštěné v místním výpočetním prostředí uživatele
  + **azureml-core**
    + Připnout balíček: pyjwt, abyste se vyhnuli v nadcházejících verzích v nadcházejících verzích.
    + Vytvoření experimentu vrátí aktivní nebo poslední archivovaný experiment se stejným názvem, pokud takový experiment existuje nebo je nový experiment.
    + Volání get_experiment podle názvu vrátí aktivní nebo poslední archivovaný experiment s daným názvem.
    + Uživatelé nemůžou experiment přejmenovat a přitom ho znovu aktivovat.
    + Vylepšená chybová zpráva, která zahrnuje možné opravy, pokud je datová sada nesprávně předána experimentu (např. ScriptRunConfig). 
    + Vylepšená dokumentace pro `OutputDatasetConfig.register_on_complete` , aby zahrnovala chování, co se stane, když tento název už existuje.
    + Výsledkem zadání vstupních a výstupních názvů datových sad, které mají potenciál kolize se společnými proměnnými prostředí, bude nyní obsahovat upozornění.
    + Parametr s předaným účelu `grant_workspace_access` při registraci úložišť dat Nastavte ho tak, aby měl `True` přístup k datům za virtuální sítí z Machine Learning Studio.
      [Další informace](./how-to-enable-studio-virtual-network.md)
    + Rozhraní API propojené služby je upřesněné. Místo poskytnutí ID prostředku máme 3 samostatné parametry sub_id, RG a název definovaný v konfiguraci.
    + Aby mohli zákazníci povolit řešení potíží s poškozením tokenu, povolte synchronizaci tokenu pracovního prostoru jako veřejné metody.
    + Tato změna umožňuje použít jako hodnotu pro script_param prázdný řetězec.
  + **azureml-train-automl-client**
    +  Vylepšené zpracování krátkých časových řad tím, že umožňují jejich odsazení pomocí Gaussovského šumu.
  + **azureml-train-automl-runtime**
    + Vyvolat ConfigException, pokud má sloupec DateTime hodnotu OutOfBoundsDatetime
    + Přidání podpory pro poskytování nezpracovaných Vysvětlení funkcí pro nejlepší model pro AutoML experimenty spuštěné v místním výpočetním prostředí uživatele
    + Vylepšené zpracování krátkých časových řad tím, že umožňují jejich odsazení pomocí Gaussovského šumu.
  + **azureml-train-core**
    + Tato změna umožňuje použít jako hodnotu pro script_param prázdný řetězec.
  + **AzureML-vlak-restclients-HyperDrive**
    + SOUBOR READme byl změněn tak, aby nabízel více kontextu.
  + **azureml-widgets**
    + Přidejte podporu řetězců do grafu/paralelní souřadnice pro widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Popisky dat pro segmentaci instancí imagí (anotace mnohoúhelníku) (Preview)

Typ projektu segmentace instancí obrázku (poznámky mnohoúhelníku) v popiskech dat je nyní k dispozici, takže uživatelé mohou nakreslit a opatřit poznámkami mnohoúhelníky kolem obrysu objektů v obrázcích. Uživatelé budou moci přiřadit třídu a mnohoúhelník každému objektu, který je v obrázku v zájmu.

Přečtěte si další informace o [označování segmentace instancí imagí](how-to-label-images.md).



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Sada SDK Azure Machine Learning pro Python v 1.17.0
+ **nové příklady**
  + Nové úložiště s komunitou řízenými příklady je k dispozici na adrese https://github.com/Azure/azureml-examples
+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Opravili jsme problém, kdy get_output může vyvolávat XGBoostError.
  + **azureml-automl-runtime**
    + Funkce založené na čase a kalendáři, které vytvořil AutoML, teď budou mít předponu.
    + Opravili jsme IndexError, ke kterému došlo během školení StackEnsemble pro datové sady klasifikace s velkým počtem tříd a s povoleným dílčím vzorkováním.
    + Opravili jsme problém, kdy VotingRegressor předpovědi může být po remontáži modelu nepřesný.
  + **azureml-core**
    + Přidala se další podrobnosti o vztahu mezi konfigurací nasazení AKS a koncepty služby Azure Kubernetes.
    + Podpora popisků klientů prostředí. Uživatel může označovat prostředí a odkazovat na ně pomocí popisku.
  + **azureml-dataprep**
    + Lepší chybová zpráva při použití aktuálně nepodporovaného Sparku s Scala 2,12.
  + **azureml-explain-model**
    + Balíček AzureML-vysvětlit-model je oficiálně zastaralý.
  + **azureml-mlflow**
    + Vyřešila se chyba v mlflow. Projects. Run proti back-endu, kde se stav finalizace nezpracovává správně.
  + **azureml-pipeline-core**
    + Přidejte podporu pro vytvoření, výpis a získání plánu kanálu na základě jednoho koncového bodu kanálu.
    +  Vylepšená dokumentace k PipelineData.as_dataset s neplatným příkladem použití – použití PipelineData.as_dataset nesprávně bude nyní mít za následek vyValueExceptionení.
    + Poznámkový blok kanálů HyperDriveStep byl změněn tak, aby registroval nejlepší model v rámci PipelineStep přímo po spuštění HyperDriveStep.
  + **azureml-pipeline-steps**
    + Poznámkový blok kanálů HyperDriveStep byl změněn tak, aby registroval nejlepší model v rámci PipelineStep přímo po spuštění HyperDriveStep.
  + **azureml-train-automl-client**
    + Opravili jsme problém, kdy get_output může vyvolávat XGBoostError.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Prostředí poznámkových bloků Azure Machine Learning Studio (říjen Update)
+ **Nové funkce**
  + [Plná podpora virtuální sítě](./how-to-enable-studio-virtual-network.md)
  + [Detailní režim](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Ukládat poznámkové bloky CTRL + S
  + Čísla řádků

+ **Opravy chyb a vylepšení**
  + Zlepšení rychlosti a spolehlivosti jádra
  + Aktualizace uživatelského rozhraní widgetu Jupyter

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Sada SDK Azure Machine Learning pro Python v 1.16.0
+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + AKSWebservice a AKSEndpoints teď podporují omezení počtu prostředků procesoru a paměti na úrovni pod. Tato volitelná omezení se dají použít nastavením `--cpu-cores-limit` a `--memory-gb-limit` příznaky v příslušných VOLÁNÍch CLI.
  + **azureml-core**
    + Připnout hlavní verze přímých závislostí AzureML-Core
    + AKSWebservice a AKSEndpoints teď podporují omezení počtu prostředků procesoru a paměti na úrovni pod. Další informace o [Kubernetesch prostředcích a omezeních](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Aktualizace run.log_table, aby bylo možné zaprotokolovat jednotlivé řádky.
    + Přidání statické metody `Run.get(workspace, run_id)` pro načtení běhu pouze pomocí pracovního prostoru 
    + Přidání metody instance `Workspace.get_run(run_id)` pro načtení běhu v pracovním prostoru
    + Představujeme vlastnost příkazu v konfiguraci spuštění, která umožní uživatelům odeslat příkaz místo argumentů Script &.
  + **azureml-interpret**
    + pevné Vysvětlení chování příznaků is_raw klienta v AzureML-interpretace
  + **AzureML-SDK**
    + `azureml-sdk` Oficiální podpora Pythonu 3,8.
  + **azureml-train-core**
    + Přidání prostředí s TensorFlow 2,3
    + Představujeme vlastnost příkazu v konfiguraci spuštění, která umožní uživatelům odeslat příkaz místo argumentů Script &.
  + **azureml-widgets**
    + Přepracované rozhraní pro widget běhu skriptu.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Sada SDK Azure Machine Learning pro Python v 1.15.0
+ **Opravy chyb a vylepšení**
  + **azureml-contrib-interpret**
    + Vysvětlujícíka VÁPNa přesunuta z procesu AzureML-contrib-interpretace na balíček interpretství – komunita a vysvětlující obrázek odebraný z balíčku AzureML-contrib-interpretace
    + řídicí panel vizualizace odebraný z balíčku AzureML-contrib-interpretuje klienta, který se přesunul do balíčku AzureML-interpretace a je zastaralý v procesu AzureML-contrib-interpretace balíčku a poznámkových bloků, aby odrážel vylepšené rozhraní API.
    + Oprava popisů balíčků PyPI pro AzureML-interpretaci, AzureML-vysvětlující-model, AzureML-contrib-interpretace a AzureML-tensorboard
  + **azureml-contrib-notebook**
    + Připnout nbcovert závislost na < 6, aby Papermill 1. x nadále fungovalo.
  + **azureml-core**
    + Přidány parametry do konstruktoru TensorflowConfiguration a MpiConfiguration, aby bylo možné zefektivnit inicializaci atributů třídy, aniž by uživatel musel nastavit každý jednotlivý atribut. Přidala se třída PyTorchConfiguration pro konfiguraci distribuovaných úloh PyTorch v ScriptRunConfig.
    + Pokud chcete opravit chybu ověřování, připnout verzi Azure-správy-Resource.
    + Podpora Triton bez nasazení kódu
    + výstupy adresářů zadané v Run.start_logging () se teď budou sledovat při použití možnosti spustit v interaktivních scénářích. Sledované soubory budou po volání metody Run. Complete () viditelné ML Studio.
    + Kódování souboru lze nyní zadat při vytváření datové sady pomocí `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_json_lines_files` předáním `encoding` argumentu. Podporovaná kódování jsou "UTF8", "iso88591", "latin1", "ASCII", UTF16 "," platná UTF32 "," utf8bom "a" windows1252 ".
    + Oprava chyby, když se objekt prostředí nepředává konstruktoru ScriptRunConfig
    + Byla aktualizována rutina run. Cancel (), aby bylo možné zrušit místní spuštění z jiného počítače.
  + **azureml-dataprep**
    +  Problémy s časovým limitem pro připojení k pevné datové sadě
  + **azureml-explain-model**
    + Oprava popisů balíčků PyPI pro AzureML-interpretaci, AzureML-vysvětlující-model, AzureML-contrib-interpretace a AzureML-tensorboard
  + **azureml-interpret**
    + řídicí panel vizualizace odebraný z balíčku AzureML-contrib-interpretuje klienta, který se přesunul do balíčku AzureML-interpretace a je zastaralý v procesu AzureML-contrib-interpretace balíčku a poznámkových bloků, aby odrážel vylepšené rozhraní API.
    + AzureML-interpretovat aktualizovaný balíček, který závisí na interpretu 0.15.0 komunity
    + Oprava popisů balíčků PyPI pro AzureML-interpretaci, AzureML-vysvětlující-model, AzureML-contrib-interpretace a AzureML-tensorboard
  + **azureml-pipeline-core**
    +  Pevný problém s potrubím, `OutputFileDatasetConfig` kde systém může přestat reagovat, když `register_on_complete` je volána s `name` parametrem nastaveným na dříve existující název datové sady.
  + **azureml-pipeline-steps**
    + Odstranily se zastaralé poznámkové bloky datacihly.
  + **azureml-tensorboard**
    + Oprava popisů balíčků PyPI pro AzureML-interpretaci, AzureML-vysvětlující-model, AzureML-contrib-interpretace a AzureML-tensorboard
  + **azureml-train-automl-runtime**
    + řídicí panel vizualizace odebraný z balíčku AzureML-contrib-interpretuje klienta, který se přesunul do balíčku AzureML-interpretace a je zastaralý v procesu AzureML-contrib-interpretace balíčku a poznámkových bloků, aby odrážel vylepšené rozhraní API.
  + **azureml-widgets**
    + řídicí panel vizualizace odebraný z balíčku AzureML-contrib-interpretuje klienta, který se přesunul do balíčku AzureML-interpretace a je zastaralý v procesu AzureML-contrib-interpretace balíčku a poznámkových bloků, aby odrážel vylepšené rozhraní API.

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Sada SDK Azure Machine Learning pro Python v 1.14.0
+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Profilace mřížky se odebrala ze sady SDK a už není podporovaná.
  + **azureml-accel-models**
    + balíček AzureML-akcelerace-Models teď podporuje Tensorflow 2. x.
  + **azureml-automl-core**
    + Přidání zpracování chyb v get_output pro případy, kdy se místní verze PANDAS/skriptu sklearn neshodují s těmi, které se použily během školení
  + **azureml-automl-runtime**
    + Opravili jsme chybu, kdy AutoArima iterace selže s PredictionException a zprávou: během předpovědi došlo k tiché chybě.
  + **AzureML-CLI – společné**
    + Profilace mřížky se odebrala ze sady SDK a už není podporovaná.
  + **AzureML-contrib-Server**
    + Popis aktualizace stránky s přehledem balíčku pro PyPI.
  + **azureml-core**
    + Profilace mřížky se odebrala ze sady SDK a už není podporovaná.
    + Snižte počet chybových zpráv, když se nepovede načíst pracovní prostor.
    + Nezobrazovat upozornění, když se metadata načítání nezdařila
    + Nový Kusto Step a Kusto Target Compute.
    + Aktualizuje dokument pro parametr SKU. Odebere SKU ve funkci aktualizace pracovního prostoru v rozhraní příkazového řádku a v sadě SDK.
    + Popis aktualizace stránky s přehledem balíčku pro PyPI.
    + Aktualizovaná dokumentace pro prostředí AzureML.
    + Zveřejňujte nastavení spravovaných prostředků služby pro pracovní prostor AML v sadě SDK.
  + **azureml-dataprep**
    + Povolte oprávnění EXECUTE pro soubory pro připojení datové sady.
  + **azureml-mlflow**
    + Aktualizace dokumentace k MLflow a ukázek poznámkových bloků 
    + Nová podpora pro projekty MLflow pomocí back-endu
    + Podpora registru modelu MLflow
    + Přidání podpory Azure RBAC pro operace AzureML-MLflow 
    
  + **azureml-pipeline-core**
    + Vylepšili dokumentaci metod PipelineOutputFileDataset.parse_ *.
    + Nový Kusto Step a Kusto Target Compute.
    + Poskytnutá vlastnost Swaggerurl pro entitu koncového bodu kanálu (kanálu) prostřednictvím tohoto uživatele může zobrazit definici schématu pro publikovaný koncový bod kanálu.
  + **azureml-pipeline-steps**
    + Nový Kusto Step a Kusto Target Compute.
  + **azureml-telemetry**
    + Popis aktualizace stránky s přehledem balíčku pro PyPI.
  + **AzureML-vlak**
    + Popis aktualizace stránky s přehledem balíčku pro PyPI.
  + **azureml-train-automl-client**
    + Přidání zpracování chyb v get_output pro případy, kdy se místní verze PANDAS/skriptu sklearn neshodují s těmi, které se použily během školení
  + **azureml-train-core**
    + Popis aktualizace stránky s přehledem balíčku pro PyPI.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Sada SDK Azure Machine Learning pro Python v 1.13.0
+ **Funkce ve verzi Preview**
  + **AzureML-Core** Díky nové schopnosti výstupních datových sad můžete zapisovat zpátky do cloudového úložiště, včetně objektů blob, ADLS Gen 1, ADLS Gen 2 a Shared. Můžete nakonfigurovat, kde mají být výstupní data, jak výstupní data (prostřednictvím připojení nebo nahrávání) zaregistrovat výstupní data pro budoucí opakované použití a sdílení a předat mezilehlá data mezi jednotlivými kroky kanálu plynule. Tím se povoluje reprodukovatelnost, sdílení, zabrání se duplikace dat a výsledkem jsou náklady na efektivitu a produktivitu. [Naučte se používat](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py)
    
+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Přidal se soubor _requirements.txt validated_ {Platform} pro připnutí všech závislostí PIP pro AutoML.
    + Tato verze podporuje modely větší než 4 GB.
    + Upgradované závislosti AutoML: `scikit-learn` (teď 0.22.1), (teď `pandas` 0.25.1), `numpy` (teď 1.18.2).
  + **azureml-automl-runtime**
    + Nastavte horovod pro text DNN, aby se vždycky používala komprese FP16.
    + Tato verze podporuje modely větší než 4 GB.
    + Opraven problém, kdy AutoML selhává s Chyba při importu: nejde importovat název `RollingOriginValidator` .
    + Upgradované závislosti AutoML: `scikit-learn` (teď 0.22.1), (teď `pandas` 0.25.1), `numpy` (teď 1.18.2).
  + **AzureML-contrib-automl-DNN-prognózování**
    + Upgradované závislosti AutoML: `scikit-learn` (teď 0.22.1), (teď `pandas` 0.25.1), `numpy` (teď 1.18.2).
  + **azureml-contrib-fairness**
    + Zadejte krátký popis pro contrib.
  + **azureml-contrib-pipeline-steps**
    + Přidala se zpráva oznamující, že tento balíček je zastaralý a uživatel by měl místo toho použít kroky AzureML-Pipeline.
  + **azureml-core**
    + Byl přidán klíč seznamu pro pracovní prostor.
    + Přidat parametr značek v sadě SDK pracovních prostorů a CLI.
    + Opravili jsme chybu, při které odeslání podřízeného běhu s datovou sadou selže z důvodu `TypeError: can't pickle _thread.RLock objects` .
    + Přidání page_count výchozí/dokumentace pro seznam modelů ().
    + Změňte sadu CLI&SDK tak, aby převzala parametr adbworkspace a přidala pracovní prostor ADB odkaz/odpojit.
    + Opravte chybu v datové sadě. aktualizace, která způsobila aktualizaci nejnovější verze datové sady, není pro ni volána verze aktualizace datové sady. 
    + Opravte chybu v Dataset.get_by_name, která by zobrazovala značky pro nejnovější verzi datové sady i v případě, že byla načtena konkrétní starší verze.
  + **azureml-interpret**
    + Přidali jsme výstupy pravděpodobnosti do Shap vyhodnocování bodování v AzureML-interpretaci na základě parametru shap_values_output z původního vysvětlujícího.
  + **azureml-pipeline-core**
    + Vylepšená `PipelineOutputAbstractDataset.register` dokumentace.
  + **azureml-train-automl-client**
    + Upgradované závislosti AutoML: `scikit-learn` (teď 0.22.1), (teď `pandas` 0.25.1), `numpy` (teď 1.18.2).
  + **azureml-train-automl-runtime**
    + Upgradované závislosti AutoML: `scikit-learn` (teď 0.22.1), (teď `pandas` 0.25.1), `numpy` (teď 1.18.2).
  + **azureml-train-core**
    + Uživatelé musí nyní zadat platný hyperparameter_sampling ARG při vytváření HyperDriveConfig. Kromě toho byla upravena dokumentace pro HyperDriveRunConfig, která informuje uživatele o vyřazení HyperDriveRunConfig.
    + Výchozí verze PyTorch se vrátí na 1,4.
    + Přidávání imagí PyTorch 1,6 & Tensorflow 2,2 a prostředí s ním uspořádaným prostředím.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Prostředí poznámkových bloků Azure Machine Learning Studio (aktualizace srpen)
+ **Nové funkce**
  + Nová úvodní stránka Začínáme 
  
+ **Funkce ve verzi Preview**
    + Shromažďování funkcí v poznámkových blocích. Díky funkci [Shromážděte](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) uživatelé teď můžou snadno vyčistit poznámkové bloky pomocí funkce Shromážděte pomocí automatizované analýzy závislostí vašeho poznámkového bloku, který zajistí uchování základního kódu, ale odebere všechny nedůležité kousky.

+ **Opravy chyb a vylepšení**
  + Zlepšení rychlosti a spolehlivosti
  + Opravené chyby v tmavém režimu
  + Opravené chyby posunutí výstupu
  + Ukázka hledání teď vyhledá veškerý obsah všech souborů v úložišti ukázkových poznámkových bloků Azure Machine Learning.
  + Víceřádkové buňky R se teď dají spouštět.
  + Po prvním spuštění se nyní automaticky kontroluje obsah "důvěřuji obsahu tohoto souboru".
  + Vylepšené dialogové okno řešení konfliktů s novou možností "vytvořit kopii"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Sada SDK Azure Machine Learning pro Python v 1.12.0

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Přidáním parametrů image_name a image_label do model. Package () povolíte přejmenování sestavené image balíčku.
  + **azureml-automl-core**
    + AutoML vyvolá nový chybový kód z dataprep, když se při čtení upraví obsah.
  + **azureml-automl-runtime**
    + Přidání upozornění pro uživatele, když data obsahují chybějící hodnoty, ale featurization je vypnuto.
    + Pevné selhání při spuštění v případě, že data obsahují NaN a featurization, je vypnuto.
    + AutoML vyvolá nový chybový kód z dataprep, když se při čtení upraví obsah.
    + Byla aktualizována normalizace pro prognózování metrik, aby se stala zrnitou.
    + Vylepšený výpočet quantiles prognózy, pokud jsou funkce lookback zakázané.
    + Pevný způsob zpracování matrice bool při výpočtu vysvětlení po AutoML.
  + **azureml-core**
    + Nová metoda `run.get_detailed_status()` nyní zobrazuje podrobné vysvětlení aktuálního stavu spuštění. V současné době se zobrazuje pouze vysvětlení pro `Queued` stav.
    + Přidáním parametrů image_name a image_label do model. Package () povolíte přejmenování sestavené image balíčku.
    + Nová metoda `set_pip_requirements()` pro nastavení celého oddílu PIP [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py) najednou.
    + Povolit registraci přihlašovacích údajů – bez ADLS Gen2 úložiště dat
    + Vylepšená chybová zpráva při pokusu o stažení nebo připojení nesprávného typu datové sady.
    + Aktualizujte si ukázkový Poznámkový blok filtru datové sady časových řad s dalšími příklady partition_timestamp, který poskytuje optimalizaci filtru.
    + Změňte sadu SDK a rozhraní příkazového řádku tak, aby při odstraňování připojení privátního koncového bodu přijímala subscriptionId, peConnectionName a pracovní prostor jako parametry.
    + Experimentální dekoratér ukazuje název třídy pro snazší identifikaci.
    + Popisy prostředků uvnitř modelů již nejsou automaticky generovány na základě spuštění.
  + **azureml-datadrift**
    + Označte rozhraní create_from_model API v DataDriftDetector jako zastaralé.
  + **azureml-dataprep**
    + Vylepšená chybová zpráva při pokusu o stažení nebo připojení nesprávného typu datové sady.
  + **azureml-pipeline-core**
    + Opravila se chyba při deserializaci grafu kanálu, který obsahuje registrované datové sady.
  + **azureml-pipeline-steps**
    + RScriptStep podporuje RSection z AzureML. Core. Environment.
    + Z veřejného rozhraní API se odebral parametr passthru_automl_config `AutoMLStep` a převedl ho na jenom interní parametr.
  + **azureml-train-automl-client**
    + Z AutoML se odebralo místní asynchronní spravované prostředí. Všechny místní běhy se spustí v prostředí, ze kterého byl spuštěn běh.
    + Pevné problémy s snímkem při odesílání AutoMLch běhů bez uživatelem zadaných skriptů.
    + Pevné selhání při spuštění v případě, že data obsahují NaN a featurization, je vypnuto.
  + **azureml-train-automl-runtime**
    + AutoML vyvolá nový chybový kód z dataprep, když se při čtení upraví obsah.
    + Pevné problémy s snímkem při odesílání AutoMLch běhů bez uživatelem zadaných skriptů.
    + Pevné selhání při spuštění v případě, že data obsahují NaN a featurization, je vypnuto.
  + **azureml-train-core**
    + Přidání podpory pro zadání možností PIP (například--extra-index-URL) v souboru požadavků PIP předaného [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator?preserve-view=true&view=azure-ml-py) `pip_requirements_file` parametru through


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Sada SDK Azure Machine Learning pro Python v 1.11.0

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Oprava modelového rozhraní a model rozhraní není předaných v objektu Run v cestě k registraci modelu rozhraní příkazového řádku
    + Oprava příkazu CLI amlcompute identity show pro zobrazení ID tenanta a ID objektu zabezpečení 
  + **azureml-train-automl-client**
    + Přidání get_best_child () do AutoMLRun pro načtení nejlepšího podřízeného běhu pro AutoML běh bez stažení přidruženého modelu.
    + Přidaný objekt ModelProxy, který umožňuje spuštění odhadu nebo předpovědi ve vzdáleném školicím prostředí bez místního stažení modelu.
    + Neošetřené výjimky v AutoML nyní ukazují na stránku HTTP známé problémy, kde najdete další informace o chybách.
  + **azureml-core**
    + Názvy modelů můžou být 255 znaků dlouhé.
    + Environment.get_image_details () vrátil typ objektu se změnil. `DockerImageDetails` nahrazené třídou `dict` , jsou podrobnosti o imagi k dispozici z vlastností nové třídy. Změny jsou zpětně kompatibilní.
    + Oprava chyby pro Environment.from_pip_requirements () pro zachování struktury závislostí
    + Opravili jsme chybu, kdy log_list selže, pokud byly do stejného seznamu zahrnuté int a Double.
    + Při povolování privátního odkazu v existujícím pracovním prostoru Pamatujte na to, že pokud jsou k pracovnímu prostoru přidruženy výpočetní cíle, tyto cíle nebudou fungovat, pokud nejsou za stejnou virtuální sítí jako privátní koncový bod v pracovním prostoru.
    + Je `as_named_input` volitelné při použití datových sad v experimentech a přidaných `as_mount` a `as_download` do `FileDataset` . Název vstupu se automaticky vygeneruje, pokud `as_mount` nebo `as_download` se zavolá.
  + **azureml-automl-core**
    + Neošetřené výjimky v AutoML nyní ukazují na stránku HTTP známé problémy, kde najdete další informace o chybách.
    + Přidání get_best_child () do AutoMLRun pro načtení nejlepšího podřízeného běhu pro AutoML běh bez stažení přidruženého modelu.
    + Přidaný objekt ModelProxy, který umožňuje spuštění předpovědi nebo předpovědi ve vzdáleném školicím prostředí bez místního stažení modelu.
  + **azureml-pipeline-steps**
    + Přidání `enable_default_model_output` a `enable_default_metrics_output` příznaky do `AutoMLStep` . Tyto příznaky lze použít k povolení nebo zakázání výchozích výstupů.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Sada SDK Azure Machine Learning pro Python v 1.10.0

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Při použití AutoML, pokud je cesta předána do objektu AutoMLConfig a ještě neexistuje, vytvoří se automaticky.
    + Uživatelé teď můžou určit četnost časových řad pro úlohy předpovědi pomocí `freq` parametru.
  + **azureml-automl-runtime**
    + Při použití AutoML, pokud je cesta předána do objektu AutoMLConfig a ještě neexistuje, vytvoří se automaticky.
    + Uživatelé teď můžou určit četnost časových řad pro úlohy předpovědi pomocí `freq` parametru.
    + AutoML prognózování teď podporuje zkušební hodnocení, které se vztahuje na případ použití, který je delší než vstupní horizont, a známá y_pred hodnota se používá jako kontext předpovědi.
  + **azureml-core**
    + Zprávy upozornění budou vytištěny, pokud nebyly během spuštění staženy žádné soubory z úložiště dat.
    + Přidala se dokumentace pro `skip_validation` `Datastore.register_azure_sql_database method` .
    + Aby bylo možné vytvořit soukromý koncový bod, který je automaticky schválen, musí uživatelé provést upgrade na sadu SDK v 1.10.0 nebo novější. To zahrnuje prostředek poznámkového bloku, který je použitelný za virtuální sítí.
    + Vystavte NotebookInfo v odpovědi na pracovní prostor získat.
    + Změny, které mají volání vypisovat cíle výpočtů a mají při vzdáleném spuštění úspěšné získání cíle výpočtů. Funkce sady SDK pro cílení výpočetních cílů a cílových pracovních prostorů seznamu budou nyní fungovat ve vzdálených spuštěních.
    + Přidejte zprávy o zastaralosti do popisů třídy pro třídy AzureML. Core. Image.
    + Vyvolejte výjimku a vyčistěte pracovní prostor a závislé prostředky, pokud se nepovede vytvořit privátní koncový bod pracovního prostoru.
    + Podpora upgradu SKU v pracovním prostoru v metodě aktualizace pracovního prostoru
  + **azureml-datadrift**
    + Aktualizujte verzi matplotlib z 3.0.2 na 3.2.1, aby podporovala Python 3,8.
  + **azureml-dataprep**
    + Byla přidána podpora zdrojů dat webové adresy URL `Range` s `Head` požadavkem nebo. 
    + Vylepšená stabilita pro připojení a stažení datové sady souborů
  + **azureml-train-automl-client**
    + Opravili jsme problémy související s odebráním `RequirementParseError` z setuptools.
    + Použít Docker místo conda pro místní běhy odeslané pomocí "compute_target =" local ""
    + Doba trvání iterace vytištěná do konzoly byla opravena. Dříve byla doba trvání iterace někdy vytištěna jako koncový čas spuštění minus doba vytváření spuštění. Byla opravena tak, aby se rovnala doba ukončení běhu mínus počáteční čas spuštění.
    + Při použití AutoML, pokud je cesta předána do objektu AutoMLConfig a ještě neexistuje, vytvoří se automaticky.
    + Uživatelé teď můžou určit četnost časových řad pro úlohy předpovědi pomocí `freq` parametru.
  + **azureml-train-automl-runtime**
    + Vylepšený výstup z konzoly, když se nezdařila nejlepší vysvětlení modelu
    + Přejmenujte vstupní parametr na "blocked_models" pro odebrání citlivého termínu.
      + Přejmenujte vstupní parametr na "allowed_models" pro odebrání citlivého termínu.
    + Uživatelé teď můžou určit četnost časových řad pro úlohy předpovědi pomocí `freq` parametru.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Sada SDK Azure Machine Learning pro Python v 1.9.0

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Nahradil se get_model_path () pomocí proměnné prostředí AZUREML_MODEL_DIR v AutoML vygenerovaném skriptu bodování. Také byla přidána telemetrie pro sledování selhání během inicializace ().
    + Odebrala se možnost zadat `enable_cache` jako součást AutoMLConfig.
    + Opravena chyba, kdy spuštění může selhat s chybami služby během konkrétních spuštění prognóz
    + Vylepšené zpracování chyb kolem konkrétních modelů během `get_output`
    + Pevné volání fitted_model. přizpůsobit (X, y) pro klasifikaci pomocí y Transformer
    + Povolený přizpůsobený přízpůsobení předem pro úlohy předpovědi
    + Místo parametrů předpovědi ve formátu dict – se použije nová třída ForecastingParameters.
    + Vylepšená funkce pro automatickou detekci zpoždění cíle
    + Přidání omezené dostupnosti s více uzly distribuované featurization s více procesory s BERT
  + **azureml-automl-runtime**
    + Prophet nyní provádí doplňkovou modelování sezónnost namísto multiplikativní.
    + Opravili jsme problém, když krátká zrna, která mají různou četnost od těch dlouhých zrn, budou mít neúspěšná spuštění.
  + **AzureML-contrib-automl-DNN-Vision**
    + Shromažďování statistik systému/GPU a logaritmických průměrů pro školení a bodování
  + **AzureML-contrib-Mir**
    + Přidání podpory pro příznak Enable-App-Insights v ManagedInferencing
  + **azureml-core**
    + Parametr Validate pro tato rozhraní API tím, že umožňuje přeskočení ověřování, když zdroj dat není přístupný z aktuálního výpočetního prostředí.
      + TabularDataset.time_before (end_time, include_boundary = true, Validate = true)
      + TabularDataset.time_after (start_time, include_boundary = true, Validate = true)
      + TabularDataset.time_recent (time_delta, include_boundary = true, Validate = true)
      + TabularDataset.time_between (start_time, end_time, include_boundary = true, Validate = true)
    + Přidání podpory filtrování architektury pro seznam modelů a přidání ukázky NCD AutoML do poznámkového bloku zpět
    + Pro Datastore.register_azure_blob_container a Datastore.register_azure_file_share (jenom možnosti, které podporují token SAS) aktualizujeme řetězce dokumentů pro toto `sas_token` pole tak, aby zahrnovaly minimální požadavky na oprávnění pro typické scénáře čtení a zápisu.
    + Vyřazení parametrů _with_auth v ws.get_mlflow_tracking_uri ()
  + **azureml-mlflow**
    + Přidání podpory pro nasazení místních file://modelů pomocí AzureML-MLflow
    + Vyřazení parametrů _with_auth v ws.get_mlflow_tracking_uri ()
  + **azureml-opendatasets**
    + Nedávno publikované datové sady sledování Covid-19 jsou teď v sadě SDK dostupné.
  + **azureml-pipeline-core**
    + Odhlásit upozornění, pokud není součástí "AzureML-Defaults" jako součást závislosti PIP
    + Vylepšete vykreslování poznámek.
    + Přidání podpory pro konce řádků v uvozovkách při analýze souborů s oddělovači na PipelineOutputFileDataset.
    + Třída PipelineDataset je zastaralá. Další informace naleznete v tématu https://aka.ms/dataset-deprecation. Naučte se používat datovou sadu s kanálem, viz https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Aktualizace dokumentu do AzureML-kanálu – kroky.
    +  Přidání podpory v ParallelRunConfig `load_yaml()` pro uživatele k definování prostředí vložených do zbytku konfigurace nebo do samostatného souboru
  + **AzureML-vlak-automl-Client**.
    + Odebrala se možnost zadat `enable_cache` jako součást AutoMLConfig.
  + **azureml-train-automl-runtime**
    + Přidání omezené dostupnosti s více uzly distribuované featurization s více procesory s BERT.
    + Do automatizovaného spuštění strojového učení založeného na ADB se přidalo zpracování chyb pro nekompatibilní balíčky.
  + **azureml-widgets**
    + Aktualizace dokumentu na AzureML – widgety

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Sada SDK Azure Machine Learning pro Python v 1.8.0
  
  + **Funkce ve verzi Preview**
    + **AzureML-contrib – spravedlivá** `azureml-contrib-fairness` Balíček poskytuje integraci mezi open source hodnocením a nepoctivou zmírňací balíčku [Fairlearn](https://fairlearn.github.io) a Azure Machine Learning Studio. Konkrétně balíček umožňuje nahrát řídicí panely pro vyhodnocení nerovnosti modelu jako součást spuštění AzureML a zobrazí se v Azure Machine Learning Studiu.

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Podpora získávání protokolů kontejneru init.
    + Přidání nových příkazů rozhraní příkazového řádku pro správu ComputeInstance
  + **azureml-automl-core**
    + Uživatelé teď můžou povolit iteraci kompletu zásobníku pro úlohy časových řad s upozorněním, že by mohlo potenciálně overfit.
    + Přidání nového typu výjimky uživatele, která se vyvolá, pokud se obsah úložiště mezipaměti úmyslně nezměnil
  + **azureml-automl-runtime**
    + V případě, že uživatel zakáže featurization, již nebude povoleno vyrovnávání protiúčtu třídy.  
  + **AzureML-contrib-ITP**
    + Typ výpočtu CmAke se podporuje. Vlastní cluster AKS můžete připojit k pracovnímu prostoru pro školicí úlohu.
  + **azureml-contrib-notebook**
    + Vylepšení dokumentů balíčku AzureML-contrib-notebook.
  + **azureml-contrib-pipeline-steps**
    + Vylepšení dokumentu balíčku AzureML-contrib--Pipeline-Steps.
  + **azureml-core**
    + Přidat set_connection, get_connection, list_connections, delete_connection funkce pro zákazníka při práci na prostředku připojení pracovního prostoru
    + Aktualizace dokumentace k balíčku AzureML-coore/AzureML. Exceptions.
    + Aktualizace dokumentace k balíčku AzureML-Core.
    + Aktualizace dokumentu na třídu ComputeInstance
    + Vylepšení dokumentů v balíčku AzureML-Core/AzureML. Core. COMPUTE
    + Vylepšení dokumentů pro třídy související s WebService v AzureML-Core.
    + Podporuje uživatelem vybrané úložiště dat pro ukládání dat profilace.
    + Přidala se vlastnost expand a page_count pro rozhraní API seznamu modelů.
    + Opravená chyba, kdy odebrání vlastnosti přepsání způsobí, že odeslaný běh selže s chybou deserializace.
    + Pevná nekonzistentní struktura složek při stahování nebo připojování datové sady souborů odkazující na jeden soubor.
    + Načítání datové sady to_spark_dataframe souborů Parquet je teď rychlejší a podporuje všechny datové typy Parquet a Spark SQL.
    + Podpora získávání protokolů kontejneru init.
    + AutoML spuštění jsou nyní označena jako podřízené spuštění paralelního kroku spuštění.
  + **azureml-datadrift**
    + Vylepšení dokumentů balíčku AzureML-contrib-notebook.
  + **azureml-dataprep**
    + Načítání datové sady to_spark_dataframe souborů Parquet je teď rychlejší a podporuje všechny datové typy Parquet a Spark SQL.
    + Lepší zpracování paměti pro OutOfMemory problém pro to_pandas_dataframe.
  + **azureml-interpret**
    + Upgradovaná AzureML-interpretace, aby používala Interpret – komunita verze 0,12. *
  + **azureml-mlflow**
    + Vylepšení dokumentů na AzureML-mlflow.
    + Přidá podporu registru modelu AML pomocí MLFlow.
  + **azureml-opendatasets**
    + Přidání podpory pro Python 3,8
  + **azureml-pipeline-core**
    + Aktualizovali jsme `PipelineDataset` dokumentaci, aby byla jasná, že se jedná o interní třídu.
    + ParallelRunStep aktualizace pro přijetí více hodnot pro jeden argument, například: "--group_column_names", "Sloupec1", "col2", "Col3"
    + Odebral se passthru_automl_config požadavek na použití mezilehlých dat pomocí AutoMLStep v kanálech.
  + **azureml-pipeline-steps**
    + Vylepšení dokumentů balíčku AzureML-Pipeline Step.
    + Odebral se passthru_automl_config požadavek na použití mezilehlých dat pomocí AutoMLStep v kanálech.
  + **azureml-telemetry**
    + Vylepšení dokumentů na AzureML
  + **azureml-train-automl-client**
    + Opravili jsme chybu, která `experiment.submit()` byla volána dvakrát u `AutoMLConfig` objektu, výsledkem je jiné chování.
    + Uživatelé teď můžou povolit iteraci kompletu zásobníku pro úlohy časových řad s upozorněním, že by mohlo potenciálně overfit.
    + Změněné chování AutoML spuštění k vyvolání UserErrorException, pokud služba vyvolá chybu uživatele
    + Opravuje chybu, která způsobila azureml_automl. log pro negenerované nebo chybějící protokoly při provádění AutoML experimentu ve vzdáleném cílovém výpočetním prostředí.
    + U klasifikačních sad, které mají nevyvážené třídy, použijeme vyrovnávání zatížení, pokud nástroj Sweeper pro podrobnější ukázková data zjistí, že vyvážení váhy zvyšuje výkon úlohy klasifikace podle určité prahové hodnoty.
    + AutoML spuštění jsou nyní označena jako podřízené spuštění paralelního kroku spuštění.
  + **azureml-train-automl-runtime**
    + Změněné chování AutoML spuštění k vyvolání UserErrorException, pokud služba vyvolá chybu uživatele
    + AutoML spuštění jsou nyní označena jako podřízené spuštění paralelního kroku spuštění.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Sada SDK Azure Machine Learning pro Python v 1.7.0

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Odstranění profilace modelu z Mir contrib bylo dokončeno vymazáním příkazů CLI a závislostí balíčku, profilace modelu je k dispozici v jádru.
    + Upgraduje minimální verzi rozhraní příkazového řádku Azure na 2.3.0.
  + **azureml-automl-core**
    + Zpráva o výjimce v kroku featurization Step fit_transform () je způsobená vlastními parametry transformátoru.
    + Přidejte podporu pro různé jazyky pro modely Transformer pro obsáhlý Learning, jako je například BERT v automatizovaných ML.
    + Odebere zastaralou lag_length parametr z dokumentace.
    + Vylepšili jsme dokumentaci parametrů prognózování. Parametr lag_length byl zastaralý.
  + **azureml-automl-runtime**
    + Opravili jsme chybu, která se vyvolala v případě, že jeden ze sloupců kategorií je v době předpovědi/testování prázdný
    + Opravte chyby spuštění v případě, že jsou povoleny funkce lookback a data obsahují krátká zrna.
    + Opravili jsme problém s chybovou zprávou indexu s duplicitním časem, pokud prodlevy nebo systém Windows byl nastaven na hodnotu automaticky.
    + Opravili jsme problém s Prophet a ARIMA modely u datových sad, které obsahují funkce lookback.
    + Přidání podpory dat před 1677-09-21 nebo po 2262-04-11 ve sloupcích na základě data a času v úlohách prognózy. Vylepšené chybové zprávy.
    + Vylepšili jsme dokumentaci parametrů prognózování. Parametr lag_length byl zastaralý.
    + Zpráva o výjimce v kroku featurization Step fit_transform () je způsobená vlastními parametry transformátoru.
    + Přidejte podporu pro různé jazyky pro modely Transformer pro obsáhlý Learning, jako je například BERT v automatizovaných ML.
    + Operace s mezipamětí, které jsou výsledkem některých OSErrors, vyvolají chybu uživatele.
    + Přidání kontrol pro zajištění, že data o školeních a ověřování mají stejný počet a sadu sloupců
    + Opraven problém s automaticky generovaným skriptem bodování AutoML, když data obsahují uvozovky
    + Povolení vysvětlení pro AutoML Prophet a navýšení modelů, které obsahují model Prophet.
    + Nedávné problémy se zákazníky odhalily chybu živého webu, při které protokoluje zprávy společně s vyrovnáváním třídy, i když logika vyrovnávání tříd není správně povolená. Odebrání protokolů/zpráv pomocí této žádosti o přijetí změn.
  + **AzureML-CLI – společné**
    + Odstranění profilace modelu z Mir contrib bylo dokončeno vymazáním příkazů CLI a závislostí balíčku, profilace modelu je k dispozici v jádru.
  + **azureml-contrib-reinforcementlearning**
    + Nástroj zátěžového testování
  + **azureml-core**
    + Dokumentace změny Script_run_config. py
    + Opravuje chybu s tiskem výstupu rozhraní příkazového řádku Submit-Pipeline.
    + Vylepšení dokumentace k AzureML-Core/AzureML. data
    + Opravuje problém s načtením účtu úložiště pomocí příkazu HDFS getconf.
    + Vylepšená dokumentace pro register_azure_blob_container a register_azure_file_share
  + **azureml-datadrift**
    + Vylepšená implementace pro zakázání a povolení sledování posunu dat
  + **azureml-interpret**
    + V vysvětlení klienta odeberte hodnoty NaN nebo soubory INF před serializací JSON při nahrávání z artefaktů.
    + Aktualizujte na nejnovější verzi interpretu – komunita pro zlepšení chyb při globálních vysvětleních s mnoha funkcemi a třídami.
    + Přidat true_ys nepovinný parametr pro podrobnější načtení pro povolení dalších funkcí v uživatelském rozhraní studia
    + Zvýšení výkonu download_model_explanations () a list_model_explanations ()
    + Malé vylepšení poznámkových bloků, aby bylo možné pomoci s laděním
  + **azureml-opendatasets**
    + opendatasets potřebuje AzureML-dataprep verze 1.4.0 nebo vyšší. Přidání upozornění, pokud je zjištěna nižší verze
  + **azureml-pipeline-core**
    + Tato změna umožňuje uživateli při volání modulu poskytnout volitelné RunConfig k moduleVersion. Publish_python_script.
    + Možnost povolit účet uzlu může být parametrem kanálu v ParallelRunStep v AzureML. Pipeline. Steps.
  + **azureml-pipeline-steps**
    + Tato změna umožňuje uživateli při volání modulu poskytnout volitelné RunConfig k moduleVersion. Publish_python_script.
  + **azureml-train-automl-client**
    + Přidejte podporu pro různé jazyky pro modely Transformer pro obsáhlý Learning, jako je například BERT v automatizovaných ML.
    + Odebere zastaralou lag_length parametr z dokumentace.
    + Vylepšili jsme dokumentaci parametrů prognózování. Parametr lag_length byl zastaralý.
  + **azureml-train-automl-runtime**
    + Povolení vysvětlení pro AutoML Prophet a navýšení modelů, které obsahují model Prophet.
    + Aktualizace dokumentace pro balíčky AzureML-vlak-automl-*
  + **azureml-train-core**
    + Podpora TensorFlow verze 2,1 ve PyTorch Estimator
    + Vylepšení balíčku AzureML-vlak-Core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Sada SDK Azure Machine Learning pro Python v 1.6.0

+ **Nové funkce**
  + **azureml-automl-runtime**
    + Prognózování AutoML nyní podporuje prognózy zákazníků nad rámec předem stanoveného maximálního počtu bitů bez nutnosti přeškolit model. Pokud je cíl prognózy od začátku do budoucna, než je zadaný maximální horizont, funkce FORECAST () bude stále předpovědi k pozdějšímu datu pomocí režimu rekurzivních operací. Pro ilustraci nové funkce se podívejte na Poznámkový blok "Další předpověď", než je maximální hodnota horizontu v poznámkovém bloku předpověď-FORECAST-Function ve [složce](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning).
  
  + **azureml-pipeline-steps**
    + ParallelRunStep je teď vydaný a je součástí balíčku **AzureML-Pipeline Step** . Existující ParallelRunStep v balíčku **AzureML-contrib-Pipeline-Steps** je zastaralá. Změny z verze Public Preview:
      + Byl přidán `run_max_try` volitelný parametr konfigurovatelný pro řízení maximálního počtu volání metody Run pro jakoukoli danou dávku. výchozí hodnota je 3.
      + Žádné PipelineParameters se už negenerují automaticky. Následující konfigurovatelné hodnoty lze nastavit jako PipelineParameter explicitně.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Výchozí hodnota pro process_count_per_node se změnila na 1. Uživatel by měl tuto hodnotu ladit pro lepší výkon. Osvědčeným postupem je nastavit počet GPU nebo PROCESORového uzlu.
      + ParallelRunStep nevloží žádné balíčky. uživatel musí do definice prostředí zahrnout balíčky **AzureML-Core** a **AzureML-dataprep [PANDAS, zapékací]** . Pokud se vlastní image Docker používá s user_managed_dependencies pak uživatel musí na Image nainstalovat conda.
      
+ **Změny způsobující chyby**
  + **azureml-pipeline-steps**
    + Jako platný typ vstupu pro AutoMLConfig se zastaralo použití AzureML. dprep. Dataflow.
  + **azureml-train-automl-client**
    + Jako platný typ vstupu pro AutoMLConfig se zastaralo použití AzureML. dprep. Dataflow.

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Opravili jsme chybu, při které se může během `get_output` tohoto pokladeného uživatele tisknout upozornění na downgrade klienta.
    + Počítač Mac se aktualizoval tak, aby se spoléhal na cudatoolkit = 9.0, protože zatím není k dispozici ve verzi 10.
    + Odebrání omezení pro modely Prophet a xgboost při vyškolení na vzdálené výpočetní prostředky.
    + Vylepšené protokolování v AutoML
    + Vylepšili jsme zpracování chyb pro vlastní featurization v úlohách prognózování.
    + Přidání funkce, která uživatelům umožní zahrnout do vygenerování předpovědí odložení funkcí.
    + Aktualizace chybové zprávy pro správné zobrazení chyby uživatele
    + Podpora cv_split_column_names pro použití s training_data
    + Aktualizuje protokolování zprávy o výjimce a Traceback.
  + **azureml-automl-runtime**
    + Povolte guardrails pro Prognózování chybějících imputace hodnot.
    + Vylepšené protokolování v AutoML
    + Přidání podrobného zpracování chyb pro výjimky pro přípravu dat
    + Odebrání omezení pro modely phrophet a xgboost při vyškolení na vzdálené výpočetní prostředky.
    + `azureml-train-automl-runtime` a `azureml-automl-runtime` mají aktualizované závislosti pro `pytorch` , `scipy` a `cudatoolkit` . nyní podporujeme `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` a `cudatoolkit==10.1.243` .
    + Vylepšili jsme zpracování chyb pro vlastní featurization v úlohách prognózování.
    + Byl vylepšen mechanismus detekce četnosti sady dat předpovědi.
    + Opravili jsme problém s Prophetm modelu pro některé datové sady.
    + Bylo vylepšeno automatické zjišťování maximálního horizontu během prognózy.
    + Přidání funkce, která uživatelům umožní zahrnout do vygenerování předpovědí odložení funkcí.
    +  Přidá funkce ve funkci FORECAST, aby bylo možné poskytovat prognózy nad vyškolený horizont bez nutnosti přeškolit model prognózy.
    + Podpora cv_split_column_names pro použití s training_data
  + **AzureML-contrib-automl-DNN-prognózování**
    + Vylepšené protokolování v AutoML
  + **AzureML-contrib-Mir**
    + Přidání podpory pro služby systému Windows v ManagedInferencing
    + Odeberte staré pracovní postupy MIR, jako např. Attach MIR COMPUTE, SingleModelMirWebservice Class-Cleanup profilace, která je umístěná v balíčku contrib-Mir.
  + **azureml-contrib-pipeline-steps**
    + Menší oprava pro podporu YAML
    + ParallelRunStep se uvolňuje do všeobecné dostupnosti-AzureML. contrib. Pipeline. kroky mají oznámení o zastaralosti a přesun na AzureML. Pipeline. Steps
  + **azureml-contrib-reinforcementlearning**
    + Nástroj RL Load Testing
    + RL Estimator má inteligentní výchozí hodnoty.
  + **azureml-core**
    + Odeberte staré pracovní postupy MIR, jako např. Attach MIR COMPUTE, SingleModelMirWebservice Class-Cleanup profilace, která je umístěná v balíčku contrib-Mir.
    + Opravili jsme informace, které uživatel poskytl v případě selhání profilování: zahrnuté ID žádosti a znovu formulovat zprávu, aby byla smysluplnější. Přidání nového pracovního postupu profilování pro spouštěče profilace
    + Vylepšený text chyby v případě selhání spuštění datové sady
    + Byla přidána podpora rozhraní příkazového řádku privátního odkazu v pracovním prostoru
    + Přidání volitelného parametru `invalid_lines` `Dataset.Tabular.from_json_lines_files` , který umožňuje určit, jak se mají zpracovávat řádky, které obsahují neplatný formát JSON.
    + Vytváření výpočetních prostředků v další verzi budeme započítávat od spuštění. Doporučujeme vytvořit vlastní cluster Amlcompute jako trvalý výpočetní cíl a použít název clusteru jako cíl výpočtů v konfiguraci spuštění. Viz ukázkový Poznámkový blok tady: aka.ms/amlcomputenb
    + Vylepšené chybové zprávy v případě selhání spuštění datové sady
  + **azureml-dataprep**
    + Bylo vytvořeno upozornění k explicitnímu upgradu verze pyarrow.
    + Vylepšené zpracování chyb a zpráva vracené v případě selhání spuštění toku dat.
  + **azureml-interpret**
    + Aktualizace dokumentace k balíčku AzureML-interpretace.
    + Pevné balíčky a notebooky pro interpretaci, které budou kompatibilní s nejnovější aktualizací skriptu sklearn
  + **azureml-opendatasets**
    + Vrátí hodnotu None, pokud nejsou vrácena žádná data.
    + Zvyšte výkon to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Rychlá oprava pro ParallelRunStep, kde bylo přerušeno načítání z YAML
    + ParallelRunStep se uvolňuje do všeobecné dostupnosti-AzureML. contrib. Pipeline. kroky mají oznámení o vyřazení a přesun na AzureML. Pipeline. Steps – nové funkce zahrnují: 1. Datové sady jako PipelineParameter 2. Nový parametr run_max_retry 3. Konfigurovatelný název výstupního souboru append_row
  + **azureml-pipeline-steps**
    + Zastaralý tok AzureML. dprep. Dataflow jako platný typ pro vstupní data.
    + Rychlá oprava pro ParallelRunStep, kde bylo přerušeno načítání z YAML
    + ParallelRunStep se uvolňuje do všeobecné dostupnosti-AzureML. contrib. Pipeline. kroky mají oznámení o vyřazení a přesun na AzureML. Pipeline. Steps – nové funkce zahrnují:
      + Datové sady jako PipelineParameter
      + Nový parametr run_max_retry
      + Konfigurovatelný název výstupního souboru append_row
  + **azureml-telemetry**
    + Aktualizuje protokolování zprávy o výjimce a Traceback.
  + **azureml-train-automl-client**
    + Vylepšené protokolování v AutoML
    + Aktualizace chybové zprávy pro správné zobrazení chyby uživatele
    + Podpora cv_split_column_names pro použití s training_data
    + Zastaralý tok AzureML. dprep. Dataflow jako platný typ pro vstupní data.
    + Počítač Mac se aktualizoval tak, aby se spoléhal na cudatoolkit = 9.0, protože zatím není k dispozici ve verzi 10.
    + Odebrání omezení pro modely phrophet a xgboost při vyškolení na vzdálené výpočetní prostředky.
    + `azureml-train-automl-runtime` a `azureml-automl-runtime` mají aktualizované závislosti pro `pytorch` , `scipy` a `cudatoolkit` . nyní podporujeme `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` a `cudatoolkit==10.1.243` .
    + Přidání funkce, která uživatelům umožní zahrnout do vygenerování předpovědí odložení funkcí.
  + **azureml-train-automl-runtime**
    + Vylepšené protokolování v AutoML
    + Přidání podrobného zpracování chyb pro výjimky pro přípravu dat
    + Odebrání omezení pro modely phrophet a xgboost při vyškolení na vzdálené výpočetní prostředky.
    + `azureml-train-automl-runtime` a `azureml-automl-runtime` mají aktualizované závislosti pro `pytorch` , `scipy` a `cudatoolkit` . nyní podporujeme `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` a `cudatoolkit==10.1.243` .
    + Aktualizace chybové zprávy pro správné zobrazení chyby uživatele
    + Podpora cv_split_column_names pro použití s training_data
  + **azureml-train-core**
    + Přidala se nová sada HyperDrive specifických výjimek. AzureML. výuka. Hyperdrive nyní vyvolá podrobné výjimky.
  + **azureml-widgets**
    + Pomůcky AzureML se nezobrazuje v JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Sada SDK Azure Machine Learning pro Python v 1.5.0

+ **Nové funkce**
  + **Funkce ve verzi Preview**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning uvolňuje podporu ve verzi Preview pro posílení učení pomocí prostředí [Ray](https://ray.io) . `ReinforcementLearningEstimator`Umožňuje školení o posílení studijních agentů napříč GPU a výpočetními cíli procesoru v Azure Machine Learning.

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + V předchozí žádosti o přijetí změn opravuje nechtěně popsaný protokol upozornění. Protokol byl použit pro ladění a omylem byl ponechán na pozadí.
    + Oprava chyby: informování klientů o částečném selhání během profilace
  + **azureml-automl-core**
    + Zrychlete model Prophet/AutoArima v předpovědi AutoML tím, že povolíte paralelní přizpůsobení pro časové řady, kdy mají datové sady víc časových řad. Abyste tuto novou funkci využili, doporučujeme nastavit "max_cores_per_iteration =-1" (to znamená pomocí všech dostupných jader procesoru) v AutoMLConfig.
    + Oprava chyby při tisku guardrails v rozhraní konzoly
    + Opravená chybová zpráva pro experimentation_timeout_hours
    + Zastaralé modely Tensorflow pro AutoML.
  + **azureml-automl-runtime**
    + Opravená chybová zpráva pro experimentation_timeout_hours
    + Pevná neklasifikovaná výjimka při pokusu o deserializaci z úložiště mezipaměti
    + Zrychlete model Prophet/AutoArima v předpovědi AutoML tím, že povolíte paralelní přizpůsobení pro časové řady, kdy mají datové sady víc časových řad.
    + Opravili jsme prognózy s povoleným návratovým oknem u datových sad, kde sada testů/předpovědi neobsahuje jednu z zrn ze sady školení.
    + Vylepšené zpracování chybějících dat
    + Opravili jsme problém s intervaly předpovědi při prognózování u datových sad, které obsahují časové řady, které nejsou zarovnané v čase.
    + Bylo přidáno lepší ověřování datových tvarů pro úlohy předpovědi.
    + Vylepšili jsme detekci četnosti.
    + Byla vytvořena lepší chybová zpráva, pokud nelze generovat skládání mezi ověřováním pro úlohy předpovědi.
    + Opravte rozhraní konzoly pro správný tisk chybějící hodnoty guardrail.
    + Vynucování kontrol datového typu u cv_split_indices vstupu v AutoMLConfig.
  + **AzureML-CLI – společné**
    + Oprava chyby: informování klientů o částečném selhání během profilace
  + **AzureML-contrib-Mir**
    + Přidá třídu AzureML. contrib. Mir. RevisionStatus, která přenáší informace o aktuálně nasazené revizi MIR a nejnovější verzi určenou uživatelem. Tato třída je obsažena v objektu MirWebservice v atributu ' deployment_status '.
    + Povolí aktualizaci pro WebService typu MirWebservice a její podřízenou třídu SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Přidání podpory pro Ray 0.8.3
    + AmlWindowsCompute podporuje jenom soubory Azure jako připojené úložiště.
    + Přejmenování health_check_timeout na health_check_timeout_seconds
    + Opravili jste některé popisy třídy/metody.
  + **azureml-core**
    + V cloudech Azure Government a Čína se povolily převody WASB > objektů BLOB.
    + Opraví chybu, aby bylo možné použít role čtenářů k získání informací o spuštění pomocí příkazu AZ ml Run CLI.
    + Při vzdáleném spuštění služby Azure ML se vstupními datovými sadami se odebralo zbytečné protokolování.
    + RCranPackage nyní podporuje parametr Version pro verzi balíčku CRAN.
    + Oprava chyby: informování klientů o částečném selhání během profilace
    + Bylo přidáno zpracování plovoucího typu float pro AzureML-Core.
    + Povolené funkce privátního odkazu v pracovním prostoru v sadě Azure ml SDK
    + Při vytváření TabularDataset pomocí `from_delimited_files` můžete určit, zda mají být prázdné hodnoty načteny jako žádné nebo jako prázdný řetězec nastavením argumentu Boolean `empty_as_string` .
    + Bylo přidáno zpracování plovoucího zůstatku pro datové sady v evropském stylu.
    + Vylepšené chybové zprávy při selhání připojení datové sady
  + **azureml-datadrift**
    + Dotaz na výsledky datového posunu z SDK obsahoval chybu, která nelišila minimální, maximální a střední metriky funkcí a výsledkem jsou duplicitní hodnoty. Tuto chybu opravil (a) pomocí předpony cíle nebo směrného plánu na názvy metrik. Před: duplicitní minimum, maximum, střední hodnota. Po: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Vylepšete zpracování omezených prostředí Pythonu pro zápis při zajišťování závislostí .NET potřebných pro doručování dat.
    + Pevné vytváření toku dat v souboru s počátečními prázdnými záznamy.
    + Byly přidány možnosti zpracování chyb pro `to_partition_iterator` podobné `to_pandas_dataframe` .
  + **azureml-interpret**
    + Omezení délky cest s vysvětlením pro omezení pravděpodobnosti přechodu na limit Windows
    + Opravu chyb pro zhuštěná vysvětlení vytvořená s použitím lineárního modelu náhrady, který se podobá.
  + **azureml-opendatasets**
    + Oprava potíží se sloupci MNIST ručně zapsaných se analyzuje jako řetězec, který by měl být int.
  + **azureml-pipeline-core**
    + Povoluje regenerate_outputs při použití modulu, který je vložený do ModuleStep.
  + **azureml-train-automl-client**
    + Zastaralé modely Tensorflow pro AutoML.
    + Oprava uživatelů povolení výpisu seznamů nepodporovaných algoritmů v místním režimu
    + Opravy dokumentů pro AutoMLConfig.
    + Vynucování kontrol datového typu u cv_split_indices vstupu v AutoMLConfig.
    + Opravili jsme problém s AutoML běhy při selhání v show_output
  + **azureml-train-automl-runtime**
    + Oprava chyby v iteracích kompletu, která zabránila úspěšnému dokončení stahování modelu.
  + **azureml-train-core**
    + Opravte překlepy ve třídě AzureML. vlak. DNN. Nccl.
    + Podpora PyTorch verze 1,5 ve PyTorch Estimator
    + Oprava problému, který image architektury nejde načíst v Azure Government oblasti při použití odhady Framework

  
## <a name="2020-05-04"></a>2020-05-04
**Nové prostředí poznámkového bloku**

Teď můžete vytvářet, upravovat a sdílet notebooky a soubory ve strojovém učení přímo ve webovém prostředí studia Azure Machine Learning. V těchto poznámkových blocích [můžete začít používat](./how-to-run-jupyter-notebooks.md) všechny třídy a metody, které jsou k dispozici v [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) .

**Zavedly se nové funkce:**

+ Vylepšený editor (Editor Monako) používaný VS Code 
+ Vylepšení uživatelského rozhraní a uživatelského rozhraní
+ Panel nástrojů buňky
+ Nový panel nástrojů a výpočetní ovládací prvky pro Poznámkový blok
+ Stavový řádek poznámkového bloku 
+ Přepnutí vloženého jádra
+ Podpora jazyka R
+ Vylepšení usnadnění přístupu a lokalizace
+ Paleta příkazů
+ Další klávesové zkratky
+ Funkce
+ Vylepšený výkon a spolehlivost

Z studia získáte přístup k následujícím webovým nástrojům pro vytváření obsahu:
    
| Webový nástroj  |     Popis  |
|---|---|
| Notebooky Azure ML Studio   |     První vytváření obsahu pro soubory poznámkových bloků a podpora všech operací, které jsou dostupné v sadě SDK Azure ML Python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Sada SDK Azure Machine Learning pro Python v 1.4.0

+ **Nové funkce**
  + Clustery AmlCompute nyní podporují nastavení spravované identity v clusteru v době zřizování. Stačí zadat, jestli chcete použít identitu přiřazenou systémem nebo identitu přiřazenou uživatelem, a předat identityId pro druhé straně. Pak můžete nastavit oprávnění pro přístup k různým prostředkům, jako je úložiště nebo ACR, a to tak, že se identita služby COMPUTE používá pro zabezpečený přístup k datům namísto přístupu založeného na tokenu, který AmlCompute dnes využívá. Další informace o parametrech najdete v dokumentaci k sadě SDK.
  

+ **Změny způsobující chyby**
  + Clustery AmlCompute podporovaly funkci Preview kolem vytváření založené na spuštění, které plánujeme vyřadit za zastaralé během dvou týdnů. Trvalé cíle výpočetního prostředí můžete nadále vytvářet vždy, když použijete třídu Amlcompute, ale konkrétní přístup k určení identifikátoru "Amlcompute" se v blízké budoucnosti nebude podporovat. 

+ **Opravy chyb a vylepšení**
  + **azureml-automl-runtime**
    + Povolí podporu pro typ, který není typu hash, při výpočtu počtu jedinečných hodnot ve sloupci.
  + **azureml-core**
    + Lepší stabilita při čtení z Azure Blob Storage pomocí TabularDataset.
    + Vylepšená dokumentace pro `grant_workspace_msi` parametr pro `Datastore.register_azure_blob_store` .
    + Opravila se chyba s `datastore.upload` pro podporu `src_dir` argumentu končícího na `/` nebo `\` .
    + Při pokusu o nahrání do úložiště dat Azure Blob Storage, které nemá přístupový klíč nebo token SAS, se přidala chybová zpráva s akcemi.
  + **azureml-interpret**
    + Bylo přidáno horní mez velikosti souboru pro data vizualizace na nahraných vysvětleních.
  + **azureml-train-automl-client**
    + Explicitně se kontroluje label_column_name & weight_column_name parametrů pro AutoMLConfig, aby byly typu String.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep teď podporuje datovou sadu jako parametr kanálu. Uživatel může vytvořit kanál s ukázkovou datovou sadou a může změnit vstupní datovou sadu stejného typu (soubor nebo tabulkového) pro nové spuštění kanálu.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Sada SDK Azure Machine Learning pro Python v 1.3.0

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Přidání další telemetrie kolem operací po školení.
    + Zrychlí automatické školení ARIMA pomocí podmíněného součtu školení (CSS) pro řady s délkou delší než 100. Použitá délka je uložena jako konstanta ARIMA_TRIGGER_CSS_TRAINING_LENGTHa v třídě TimeSeriesInternal na/src/AzureML-automl-Core/AzureML/automl/Core/Shared/Constants.py
    + Bylo vylepšeno protokolování spuštění prognóz. teď se vám v protokolu zobrazí další informace o tom, která fáze je aktuálně spuštěná.
    + Nepovolené target_rolling_window_size být nastavené na hodnoty menší než 2
  + **azureml-automl-runtime**
    + Vylepšená chybová zpráva zobrazená při nalezení duplicitních časových razítek.
    + Nepovolené target_rolling_window_size být nastavené na hodnoty menší než 2.
    + Opravili jsme chybu imputace prodlevy. Problém byl způsoben nedostatečným počtem pozorování potřebných k sestavování řad. "De-sezónní" data se používají k výpočtu délky prodlevy pomocí částečné funkce autocorrelation (PACF).
    + Featurization přizpůsobení pro účely plánování úkolů podle featurization config. Nyní se podporuje číselná a kategorií jako účel sloupce pro úlohy předpovědi.
    + Povolilo se featurizationelné přizpůsobení drop Column pro úlohy předpovědi podle featurization config.
    + Povolilo se přizpůsobení imputace pro úlohy předpovědi podle featurization config. Nyní se podporuje imputace konstantní hodnoty pro cílový sloupec a střední, medián, most_frequent a konstantní hodnotu pro školicí data.
  + **azureml-contrib-pipeline-steps**
    + Přijměte názvy výpočetních řetězců, které se mají předat ParallelRunConfig
  + **azureml-core**
    +  Bylo přidáno rozhraní API prostředí. Clone (new_name), které vytvoří kopii objektu prostředí.
    +  Environment.docker.base_dockerfile přijímá FilePath. Pokud je možné soubor vyřešit, bude se obsah číst do base_dockerfile vlastnosti prostředí.
    + Automaticky vynulovat vzájemně se vylučující hodnoty pro base_image a base_dockerfile, když uživatel ručně nastaví hodnotu v Environment.docznačku.
    + Přidání příznaku user_managed v RSection, který označuje, jestli je prostředí spravované uživatelem nebo AzureML.
    + Datová sada: Chyba při stahování pevné datové sady, pokud cesta k datům obsahuje znaky Unicode.
    + Datová sada: Vylepšený mechanismus pro připojení k datové sadě pro zajištění minimálního požadavku na místo na disku ve službě Azure Machine Learning COMPUTE, což zabrání tomu, aby byl uzel nepoužitelný a způsobil, že se úloha zruší.
    + Datová sada: přidáme index pro sloupec časové řady, když přistupujete k datové sadě časové řady jako PANDAS dataframe, který slouží ke zrychlení přístupu k datům založeným na čase řady.  Dříve byl index dán stejným názvem jako sloupec časového razítka. matoucí uživatelé, kterým je skutečný sloupec časového razítka a index. Do indexu teď neposkytujeme žádný konkrétní název, protože by se neměl používat jako sloupec. 
    + Datová sada: problém s ověřováním s pevnou datovou sadou v svrchovaném cloudu
    + Datová sada: Opravená `Dataset.to_spark_dataframe` chyba u datových sad vytvořených z úložiště Azure PostgreSQL datastores.
  + **azureml-interpret**
    + Přidání globálních skóre do vizualizace, pokud jsou hodnoty místní důležitost zhuštěné
    + Aktualizace AzureML-interpretace, aby používala Interpret-Community 0,9. *
    + Opraven problém s vysvětlením stahování, která měla data zhuštěného vyhodnocení
    + Přidání podpory zhuštěného formátu objektu vysvětlení v AutoML
  + **azureml-pipeline-core**
    + Podpora ComputeInstance jako cíle výpočtů v kanálech
  + **azureml-train-automl-client**
    + Přidání další telemetrie kolem operací po školení.
    + Pevná regrese při prvotním zastavení
    + Zastaralý tok AzureML. dprep. Dataflow jako platný typ pro vstupní data.
    +  Výchozí doba experimentu AutoML se mění na šest dní.
  + **azureml-train-automl-runtime**
    + Přidání další telemetrie kolem operací po školení.
    + byla přidána podpora zhuštěných AutoML a koncových.
  + **azureml-opendatasets**
    + Přidala se další telemetrie pro sledování služby.
    + Povolit přední dveře pro objekt BLOB pro zvýšení stability 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Sada SDK Azure Machine Learning pro Python v 1.2.0

+ **Změny způsobující chyby**
  + Podpora přetažení pro Python 2,7

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Přidá k `az ml model/computetarget/service` příkazům v rozhraní příkazového řádku--Subscription-ID.
    + Přidání podpory pro předávání CMK (Customer-Managed Key) vault_url, key_name a key_version pro nasazení ACI
  + **azureml-automl-core** 
    + Povoleno přizpůsobené imputace s konstantní hodnotou pro úlohy vytváření předpovědí dat X a y.
    + Opravili jsme problém v se zobrazením chybových zpráv pro uživatele.    
  + **azureml-automl-runtime**
    + Opravili jsme problém s prognózou u datových sad, které obsahují zrna jenom s jedním řádkem.
    + Snížila množství paměti vyžadované úlohami předpovědi.
    + Přidání lepších chybových zpráv, pokud sloupec s časem nemá správný formát.
    + Povoleno přizpůsobené imputace s konstantní hodnotou pro úlohy vytváření předpovědí dat X a y.
  + **azureml-core**
    + Přidání podpory pro načtení ServicePrincipal z proměnných prostředí: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID a AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Zavedli jsme nový parametr `support_multi_line` na `Dataset.Tabular.from_delimited_files` : ve výchozím nastavení ( `support_multi_line=False` ) jsou všechny konce řádků, včetně těch, které se nacházejí v uvozovkách, interpretují jako přerušení záznamu. Čtení dat tímto způsobem je rychlejší a optimalizované pro paralelní spouštění na více jádrech procesoru. Může to ale vést k tichému vytváření dalších záznamů s nesprávně zarovnanými hodnotami polí. Toto nastavení by mělo být nastavené na hodnotu, `True` Pokud jsou soubory s oddělovači obsaženy v uvozovkách zalomených řádků.
    + Přidání možnosti registrace ADLS Gen2 v rozhraní příkazového řádku Azure Machine Learning
    + Přejmenujte parametr fine_grain_timestamp na timestamp a parametr coarse_grain_timestamp na partition_timestamp pro metodu with_timestamp_columns () v TabularDataset, aby lépe odrážela použití parametrů.
    + Maximální délka názvu experimentu je větší než 255.
  + **azureml-interpret**
    + Aktualizace AzureML-interpretace na interpretaci Community 0,7. *
  + **AzureML-SDK**
    + Změna na závislosti s kompatibilní verzí vlnovkou pro podporu oprav v předběžných verzích a stabilních verzích.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Sada SDK Azure Machine Learning pro Python v 1.1.5

+ **Zastaralá funkce**
  + **Python 2,7**
    + Poslední verze pro podporu Pythonu 2,7

+ **Změny způsobující chyby**
  + **Sémantika správy verzí 2.0.0**
    + Od verze 1,1 Azure ML Python SDK přijímá sémantickou verzi 2.0.0. [Další informace najdete tady](https://semver.org/). Všechny další verze budou následovat po novém schématu číslování a kontraktu sémantických verzí. 

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Pokud chcete zajistit konzistenci, změňte název příkazu rozhraní příkazového řádku pomocí příkazu AZ ml Endpoint AKS na az ml Endpoint Real time.
    + aktualizace pokynů k instalaci rozhraní příkazového řádku pro stabilní a experimentální větve CLI
    + Profilace jedné instance byla opravena, aby vytvořila doporučení a byla k dispozici v základní sadě SDK.
  + **azureml-automl-core**
    + Povolený odvození dávkového režimu (vícenásobné řádky jednou) pro AutoML ONNX modely
    + Vylepšili jsme detekci četnosti datových sad, postrádáte data nebo obsahují nepravidelné datové body.
    + Přidání možnosti odebrat datové body, které nejsou v souladu s dominantní frekvencí.
    + Změnil vstup konstruktoru tak, aby provedl seznam možností pro použití možností imputace pro odpovídající sloupce.
    + Bylo vylepšeno protokolování chyb.
  + **azureml-automl-runtime**
    + Opravili jsme problém s chybou, pokud se v sadě školení objevila tato zrnitá sada testů.
    + Při bodování služby prognózování se odebral požadavek y_query.
    + Opravili jsme problém s prognózou, pokud datová sada obsahuje krátká zrna s dlouhou časovou mezerou.
    + Opravili jsme problém, když je zapnutý automatický maximální horizont a sloupec data obsahuje kalendářní data ve formě řetězců. Byly přidány správné převody a chybové zprávy, pokud není možné převod na datum.
    + Použití nativních NumPy a SciPy pro serializaci a deserializaci mezilehlých dat pro FileCacheStore (používá se pro místní AutoML spuštění)
    + Opravili jsme chybu, při které se neúspěšná podřízená spuštění můžou zablokovat v běžícím stavu.
    + Vyšší rychlost featurization
    + Opravili jste kontrolu četnosti během bodování, teď úlohy předpovědi nevyžadují striktní rovnost frekvencí mezi vlakem a testovací sadou.
    + Změnil vstup konstruktoru tak, aby provedl seznam možností pro použití možností imputace pro odpovídající sloupce.
    + Opravily se chyby související s výběrem typu prodlevy.
    + Opravili jsme neklasifikovanou chybu vyvolanou u datových sad, které mají zrna s jedním řádkem.
    + Opravili jsme problém s pomalou frekvencí detekce frekvence.
    + Opravuje chybu v AutoML zpracování výjimek, která způsobila, že se v důsledku neúspěchu v AttributeError nahradí reálný důvod selhání školení.
  + **AzureML-CLI – společné**
    + Profilace jedné instance byla opravena, aby vytvořila doporučení a byla k dispozici v základní sadě SDK.
  + **AzureML-contrib-Mir**
    + Přidá do třídy MirWebservice funkce pro získání přístupového tokenu.
    + Při volání metody MirWebservice. Run () použijte k MirWebservice ve výchozím nastavení token auth, pokud volání selhává.
    + Nasazení webové služby Mir nyní vyžaduje správné SKU [Standard_DS2_v2, Standard_F16, Standard_A2_v2] namísto [Ds2v2, A2v2 a F16 úrovně].
  + **azureml-contrib-pipeline-steps**
    + Nepovinný parametr side_inputs přidaný do ParallelRunStep. Tento parametr lze použít k připojení složky v kontejneru. Aktuálně podporované typy jsou DataReference a PipelineData.
    + Parametry předané v ParallelRunConfig lze přepsat předáním parametrů kanálu nyní. Jsou podporovány nové parametry kanálu aml_mini_batch_size, aml_error_threshold, aml_logging_level aml_run_invocation_timeout (aml_node_count a aml_process_count_per_node jsou již součástí starší verze).
  + **azureml-core**
    + Nasazené WebServices služby AzureML se teď budou automaticky `INFO` přihlašovat. To se dá řídit nastavením `AZUREML_LOG_LEVEL` proměnné prostředí v nasazené službě.
    + Python SDK používá službu zjišťování k použití koncového bodu API namísto kanálů.
    + Proměňte nové trasy ve všech voláních sady SDK.
    + Změna směrování volání do ModelManagementService do nové sjednocené struktury.
      + Nastavila se veřejně dostupná metoda aktualizace pracovního prostoru.
      + Přidání parametru image_build_compute v metodě aktualizace pracovního prostoru, aby uživatel mohl aktualizovat výpočetní výkon pro sestavení image
    + Do starého pracovního postupu profilace se přidaly zprávy o zastaralosti. Pevné profilace omezení procesoru a paměti.
    + Přidali jsme RSection jako součást prostředí pro spouštění úloh R.
    + Bylo přidáno ověřování k `Dataset.mount` vyvolání chyby, pokud zdroj datové sady není přístupný nebo neobsahuje žádná data.
    + Přidáno `--grant-workspace-msi-access` jako další parametr pro rozhraní příkazového řádku úložiště (CLI) pro registraci kontejneru objektů blob Azure, který vám umožní zaregistrovat kontejner objektů blob, který je za virtuální sítí.
    + Profilace jedné instance byla opravena, aby vytvořila doporučení a byla k dispozici v základní sadě SDK.
    + Opravili jsme problém v aks.py _deploy.
    + Ověří integritu odesílaných modelů, aby nedocházelo k selháním v tichém úložišti.
    + Uživatel může nyní zadat hodnotu ověřovacího klíče při opětovném generování klíčů pro služby WebServices.
    + Opravila se chyba, kde velká písmena nelze použít jako vstupní název datové sady.
  + **AzureML – výchozí**
    + `azureml-dataprep` se teď nainstaluje jako součást `azureml-defaults` . Pro připojení datových sad už není nutné ručně instalovat data PREP [zapékací] na výpočetní cíle.
  + **azureml-interpret**
    + Aktualizace AzureML-interpretace na interpretaci Community 0,6. *
    + Aktualizace AzureML-interpretace závisí na interpretu 0.5.0 komunity
    + Přidání výjimek ve stylu AzureML do AzureML-interpretace
    + Pevná serializace DeepScoringExplainer pro modely keras
  + **azureml-mlflow**
    + Přidání podpory pro cloudy svrchovan do AzureML. mlflow
  + **azureml-pipeline-core**
    + Poznámkový blok kanálu dávky vyhodnocování teď používá ParallelRunStep.
    + Opravili jsme chybu, při které by se PythonScriptStep výsledky, i když se změní seznam argumentů.
    + Přidání možnosti pro nastavení typu Columns při volání metody parse_ * na `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Přesunuto `AutoMLStep` do `azureml-pipeline-steps` balíčku. Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .
    + Přidání příkladu dokumentace pro datovou sadu jako PythonScriptStep Input
  + **azureml-tensorboard**
    + aktualizace AzureML-tensorboard na podporu tensorflow 2,0
    + Při použití vlastního Tensorboard portu na výpočetní instanci zobrazit správné číslo portu
  + **azureml-train-automl-client**
    + Opravili jsme problém, kdy se některé balíčky můžou na vzdáleném spuštění nainstalovat na nesprávnou verzi.
    + opravený problém FeaturizationConfig, který filtruje vlastní konfiguraci featurization.
  + **azureml-train-automl-runtime**
    + Opravili jsme problém s detekcí četnosti ve vzdálených spuštěních.
    + Přesunuto `AutoMLStep` v `azureml-pipeline-steps` balíčku. Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Podpora PyTorch verze 1,4 ve PyTorch Estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK pro Python v 1.1.2 RC0 (Předběžná verze)

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Povolený odvození dávkového režimu (vícenásobné řádky jednou) pro AutoML ONNX modely
    + Vylepšili jsme detekci četnosti datových sad, postrádáte data nebo obsahují nepravidelné datové body.
    + Přidání možnosti odebrat datové body, které nejsou v souladu s dominantní frekvencí.
  + **azureml-automl-runtime**
    + Opravili jsme problém s chybou, pokud se v sadě školení objevila tato zrnitá sada testů.
    + Při bodování služby prognózování se odebral požadavek y_query.
  + **AzureML-contrib-Mir**
    + Přidá do třídy MirWebservice funkce pro získání přístupového tokenu.
  + **azureml-core**
    + Nasazené WebServices služby AzureML se teď budou automaticky `INFO` přihlašovat. To se dá řídit nastavením `AZUREML_LOG_LEVEL` proměnné prostředí v nasazené službě.
    + Opravte iterace na `Dataset.get_all` , aby se vracely všechny datové sady zaregistrované v pracovním prostoru.
    + Vylepšit chybovou zprávu, pokud je předán neplatný typ `path` argumentu rozhraní API pro vytváření datových sad.
    + Python SDK používá službu zjišťování k použití koncového bodu API namísto kanálů.
    + Přepnout na nové trasy ve všech voláních sady SDK
    + Změní směrování volání do ModelManagementService do nové sjednocené struktury.
      + Nastavila se veřejně dostupná metoda aktualizace pracovního prostoru.
      + Přidání parametru image_build_compute v metodě aktualizace pracovního prostoru pro povolení aktualizace výpočtů pro sestavení imagí uživatelem
    +  Do starého pracovního postupu profilace se přidaly zprávy o zastaralosti. Pevné profilace omezení procesoru a paměti
  + **azureml-interpret**
    + aktualizovat AzureML-interpretovat na interpretaci Community 0,6. *
  + **azureml-mlflow**
    + Přidání podpory pro cloudy svrchovan do AzureML. mlflow
  + **azureml-pipeline-steps**
    + Přesunuta `AutoMLStep` do `azureml-pipeline-steps package` . Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Opravili jsme problém, kdy se některé balíčky můžou na vzdáleném spuštění nainstalovat na nesprávnou verzi.
  + **azureml-train-automl-runtime**
    + Opravili jsme problém s detekcí četnosti ve vzdálených spuštěních.
    + Přesunuta `AutoMLStep` do `azureml-pipeline-steps package` . Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Přesunuta `AutoMLStep` do `azureml-pipeline-steps package` . Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK pro Python v 1.1.1 RC0 (Předběžná verze)

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Profilace jedné instance byla opravena, aby vytvořila doporučení a byla k dispozici v základní sadě SDK.
  + **azureml-automl-core**
    + Bylo vylepšeno protokolování chyb.
  + **azureml-automl-runtime**
    + Opravili jsme problém s prognózou, pokud datová sada obsahuje krátká zrna s dlouhou časovou mezerou.
    + Opravili jsme problém, když je zapnutý automatický maximální horizont a sloupec data obsahuje kalendářní data ve formě řetězců. V případě, že není možný převod na datum, jsme přidali řádný převod a rozumné chybu.
    + Použití nativních NumPy a SciPy pro serializaci a deserializaci mezilehlých dat pro FileCacheStore (používá se pro místní AutoML spuštění)
    + Opravili jsme chybu, při které se neúspěšná podřízená spuštění můžou zablokovat v běžícím stavu.
  + **AzureML-CLI – společné**
    + Profilace jedné instance byla opravena, aby vytvořila doporučení a byla k dispozici v základní sadě SDK.
  + **azureml-core**
    + Přidáno `--grant-workspace-msi-access` jako další parametr pro rozhraní příkazového řádku úložiště (CLI) pro registraci kontejneru objektů blob Azure, který vám umožní zaregistrovat kontejner objektů blob, který je za virtuální sítí.
    + Profilace jedné instance byla opravena, aby vytvořila doporučení a byla k dispozici v základní sadě SDK.
    + Opravili jsme problém v aks.py _deploy
    + Ověří integritu odesílaných modelů, aby nedocházelo k selháním v tichém úložišti.
  + **azureml-interpret**
    + přidání výjimek ve stylu AzureML do AzureML-interpretace
    + pevná serializace DeepScoringExplainer pro modely keras
  + **azureml-pipeline-core**
    + Poznámkový blok kanálu dávky vyhodnocování teď používá ParallelRunStep.
  + **azureml-pipeline-steps**
    + Přesunuto `AutoMLStep` v `azureml-pipeline-steps` balíčku. Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Nepovinný parametr side_inputs přidaný do ParallelRunStep. Tento parametr lze použít k připojení složky v kontejneru. Aktuálně podporované typy jsou DataReference a PipelineData.
  + **azureml-tensorboard**
    + aktualizace AzureML-tensorboard na podporu tensorflow 2,0
  + **azureml-train-automl-client**
    + opravený problém FeaturizationConfig, který filtruje vlastní konfiguraci featurization.
  + **azureml-train-automl-runtime**
    + Přesunuto `AutoMLStep` v `azureml-pipeline-steps` balíčku. Zastaralé v `AutoMLStep` rámci `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Podpora PyTorch verze 1,4 ve PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Sada SDK Azure Machine Learning pro Python v 1.1.0 RC0 (Předběžná verze)

+ **Změny způsobující chyby**
  + **Sémantika správy verzí 2.0.0**
    + Od verze 1,1 Azure ML Python SDK přijímá sémantickou verzi 2.0.0. [Další informace najdete tady](https://semver.org/). Všechny další verze budou následovat po novém schématu číslování a kontraktu sémantických verzí. 
  
+ **Opravy chyb a vylepšení**
  + **azureml-automl-runtime**
    + Vyšší rychlost featurization
    + Opravili jste kontrolu četnosti během bodování, teď v úlohách prognózy nepotřebujeme striktní rovnost mezi vlakem a testovací sadou.
  + **azureml-core**
    + Uživatel může nyní zadat hodnotu ověřovacího klíče při opětovném generování klíčů pro služby WebServices.
  + **azureml-interpret**
    + Aktualizace AzureML-interpretace závisí na interpretu 0.5.0 komunity
  + **azureml-pipeline-core**
    + Opravili jsme chybu, při které by se PythonScriptStep výsledky, i když se změní seznam argumentů.
  + **azureml-pipeline-steps**
    + Přidání příkladu dokumentace pro datovou sadu jako PythonScriptStep Input
  + **azureml-contrib-pipeline-steps**
    + Parametry předané v ParallelRunConfig lze přepsat předáním parametrů kanálu nyní. Jsou podporovány nové parametry kanálu aml_mini_batch_size, aml_error_threshold, aml_logging_level aml_run_invocation_timeout (aml_node_count a aml_process_count_per_node jsou již součástí starší verze).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Sada SDK Azure Machine Learning pro Python v 1.0.85

+ **Nové funkce**
  + **azureml-core**
    + Získejte aktuální základní využití a omezení kvóty pro prostředky AmlCompute v daném pracovním prostoru a předplatném.
  
  + **azureml-contrib-pipeline-steps**
    + Povolit uživateli předat tabelární datovou sadu jako mezilehlou výsledek z předchozího kroku do parallelrunstep

+ **Opravy chyb a vylepšení**
  + **azureml-automl-runtime**
    + Do nasazené služby prognózování se odebral požadavek y_query sloupce v požadavku. 
    + ' Y_query ' byl odebrán z oranžového oddílu žádosti o službu poznámkového bloku Dominick.
    + Opravili jsme chybu, která znemožňuje prognózování na nasazených modelech a pracuje na datových sadách se sloupci data a času.
    + Byl přidán korelační koeficient Matthews jako metrika klasifikace pro binární i mezitřídovou klasifikaci.
  + **azureml-contrib-interpret**
    + Odstraněné texty z modulu AzureML-contrib-interpretace textu byly přesunuty do úložiště interpretovat text, které bude brzy vydány.
  + **azureml-core**
    + Datová sada: použití pro datovou sadu již není závislé na numpy a PANDAS k instalaci v Python env.
    + Změna LocalWebservice.wait_for_deployment () pro kontrolu stavu místního kontejneru Docker předtím, než se pokusíte testovat svůj koncový bod stavu, významně zkracuje dobu potřebnou k nahlášení selhání nasazení.
    + Opravil inicializaci vnitřní vlastnosti použité v LocalWebservice. Load () při vytvoření objektu služby z existujícího nasazení pomocí konstruktoru LocalWebservice ().
    + Upravená chybová zpráva pro objasnění
    + Do AksWebservice se přidala nová metoda s názvem get_access_token (), která vrátí objekt AksServiceAccessToken, který obsahuje přístupový token, obnovování po časovém razítku, vypršení platnosti na časovém razítku a typ tokenu. 
    + Zastaralá metoda existující get_token () v AksWebservice jako nová metoda vrátí všechny informace, které vrací tato metoda.
    + Byl změněn výstup příkazu AZ ml Service Get-Access-token. Token se přejmenoval na accessToken a refreshBy na refreshAfter. Přidání vlastností expiryOn a typ TokenType
    + Pevný get_active_runs
  + **azureml-explain-model**
    + Aktualizovali jsme Shap na 0.33.0 a interpretaci komunity na 0,4. *
  + **azureml-interpret**
    + Aktualizovali jsme Shap na 0.33.0 a interpretaci komunity na 0,4. *
  + **azureml-train-automl-runtime**
    + Byl přidán korelační koeficient Matthews jako metrika klasifikace pro binární i mezitřídovou klasifikaci.
    + Vyřadit příznak předběžného zpracování z kódu a nahradit featurization-featurization je ve výchozím nastavení zapnuté.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Sada SDK Azure Machine Learning pro Python v 1.0.83

+ **Nové funkce**
  + Datová sada: Přidejte dvě možnosti `on_error` a `out_of_range_datetime` pro `to_pandas_dataframe` se nezdaří, pokud data obsahují chybové hodnoty namísto jejich naplňování `None` .
  + Pracovní prostor: Přidali jste `hbi_workspace` příznak pro pracovní prostory s citlivými daty, který umožňuje další šifrování a zakáže pokročilou diagnostiku v pracovních prostorech. Přidali jsme také podporu pro uvedení vlastních klíčů pro přidruženou instanci Cosmos DB, zadáním `cmk_keyvault` `resource_cmk_uri` parametrů a při vytváření pracovního prostoru, který ve vašem předplatném vytvoří Cosmos DB instanci při zřizování pracovního prostoru. [Další informace najdete tady.](./concept-data-encryption.md#azure-cosmos-db)

+ **Opravy chyb a vylepšení**
  + **azureml-automl-runtime**
    + Opravili jsme regresi, která způsobila vyvolání TypeError při spuštění AutoML ve verzích Pythonu pod 3.5.4.
  + **azureml-core**
    + Opravená chyba v `datastore.upload_files` byla relativní cesta, která nebyla začínat, se nedala `./` použít.
    + Přidání zpráv o zastaralosti pro všechny cesty kódu třídy obrázků
    + Pevná Správa modelů konstrukce URL pro oblast Azure Čína 21Vianet
    + Opravili jsme problém, kdy se pro Azure Functions nedaly použít modely s source_dir.    
    + Přidání možnosti pro [Environment.build_local ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) pro nahrání image do registru kontejneru pracovního prostoru AzureML
    + Sada SDK se aktualizovala tak, aby používala novou knihovnu tokenů v Azure synapse s nekompatibilním způsobem.
  + **azureml-interpret**
    + Opravila se chyba, kde nebyla vrácena žádná vysvětlení, pokud nebyla k dispozici žádná vysvětlení ke stažení. Nyní vyvolá výjimku, která odpovídá chování jinde.
  + **azureml-pipeline-steps**
    + Bylo zakázáno předávání `DatasetConsumptionConfig` `Estimator` parametru, `inputs` Když `Estimator` bude použit v `EstimatorStep` .
  + **AzureML-SDK**
    + Přidání klienta AutoML do balíčku AzureML-SDK, který umožňuje odeslání vzdálených AutoMLch testů bez instalace úplného balíčku AutoML.
  + **azureml-train-automl-client**
    + Opravené zarovnání pro výstup konzoly pro AutoML běhy
    + Opravili jsme chybu, při které může být na vzdáleném amlcompute nainstalovaná nesprávná verze PANDAS.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Sada SDK Azure Machine Learning pro Python v 1.0.81

+ **Opravy chyb a vylepšení**
  + **azureml-contrib-interpret**
    + odložit závislost Shap na interpretaci z AzureML-interpretace
  + **azureml-core**
    + Cíl výpočtů se teď dá zadat jako parametr pro odpovídající objekty konfigurace nasazení. To je konkrétně název výpočetního cíle pro nasazení, nikoli objekt sady SDK.
    + Přidání informací CreatedBy do modelu a objektů služby. Může být k dispozici through.created_by
    + Opravený ContainerImage. Run (), který nesprávně nastavil port HTTP kontejneru Docker.
    + Nastavit jako `azureml-dataprep` volitelné pro `az ml dataset register` příkaz CLI
    + Opravili jsme chybu, `TabularDataset.to_pandas_dataframe` která by se nesprávně vrátila do alternativního čtecího zařízení, a vytiskla upozornění.
  + **azureml-explain-model**
    + odložit závislost Shap na interpretaci z AzureML-interpretace
  + **azureml-pipeline-core**
    + Přidali jsme nový krok kanálu `NotebookRunnerStep` , který spustí místní Poznámkový blok jako krok v kanálu.
    + Odebrání zastaralých funkcí get_all pro PublishedPipelines, plány a PipelineEndpoints
  + **azureml-train-automl-client**
    + Začalo vyřazení data_script jako vstupu do AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Sada SDK Azure Machine Learning pro Python v 1.0.79

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Odebrané featurizationConfig k zaznamenání
      + Aktualizované protokolování do protokolu "auto"/"vypnuto"/"přizpůsobené".
  + **azureml-automl-runtime**
    + Byla přidána podpora pro PANDAS. Řady a PANDAS. Kategorií pro detekci datového typu sloupce. Dřív podporovaná numpy. ndarray
      + Přidání souvisejících změn kódu ke správnému zpracování dtype kategorií.
    + Bylo vylepšeno rozhraní funkce FORECAST: parametr y_pred byl nepovinný. – Docstrings se vylepšily.
  + **azureml-contrib-dataset**
    + Opravili jsme chybu, kdy se nepovedlo připojit k datovým sadám s popisky.
  + **azureml-core**
    + Oprava chyby pro `Environment.from_existing_conda_environment(name, conda_environment_name)` . Uživatel může vytvořit instanci prostředí, která je přesnou replikou místního prostředí.
    + Ve výchozím nastavení se změnily metody datové sady související s časovou řadou `include_boundary=True` .
  + **azureml-train-automl-client**
    + Opravili jsme problém, kdy se výsledky ověřování netisknou, pokud je hodnota zobrazit výstup nastavená na false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Sada SDK Azure Machine Learning pro Python v 1.0.76

+ **Změny způsobující chyby**
  + AutoML problémy s upgradem pro AzureML
    + Upgrade na AzureML-vlak-automl>= 1.0.76 z AzureML-vlak-automl<1.0.76 může způsobit částečné instalace, což způsobí selhání některých AutoML importů. Chcete-li tento problém vyřešit, můžete spustit instalační skript, který najdete na adrese https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Nebo pokud používáte PIP přímo, můžete:
      + "instalace PIP--upgrade AzureML-výukový-automl"
      + "instalace PIP--Ignore-installed-installed-automl-Client"
    + nebo můžete před upgradem odinstalovat starou verzi.
      + "PIP Uninstall AzureML-vlak-automl"
      + "PIP Install AzureML-vlak-automl"

+ **Opravy chyb a vylepšení**
  + **azureml-automl-runtime**
    + AutoML bude nyní brát v úvahu třídy true i false při výpočtu průměrných skalárních metrik pro binární úlohy klasifikace.
    + Strojové učení a školicí kód v procesu AzureML-AutoML-Core byly přesunuty do nového balíčku AzureML-AutoML-runtime.
  + **azureml-contrib-dataset**
    + Při volání `to_pandas_dataframe` pro datovou sadu s názvem s možností stažení teď můžete určit, jestli se mají stávající soubory přepsat nebo ne.
    + Při volání `keep_columns` nebo `drop_columns` , které vede k vyřazení sloupce časové řady, popisku nebo obrázku, jsou pro datovou sadu zahozeny i odpovídající funkce.
    + Opravili jsme problém s zavaděčem pytorch pro úlohu detekce objektu.
  + **azureml-contrib-interpret**
    + Odebrali jsme widget na řídicím panelu vysvětlení z AzureML-contrib-interpretované, změněného balíčku, který odkazuje na nový v interpret_community
    + Aktualizovaná verze interpretu – komunita na 0.2.0
  + **azureml-core**
    + Zvyšte výkon `workspace.datasets` .
    + Přidání možnosti registrace Azure SQL Database úložiště dat pomocí ověřování uživatelského jména a hesla
    + Oprava pro načtení RunConfigurations z relativních cest
    + Při volání `keep_columns` nebo `drop_columns` , které vede k vyřazení sloupce časové řady, jsou pro datovou sadu zahozeny i odpovídající funkce.
  + **azureml-interpret**
    + aktualizovaná verze interpretu – komunita na 0.2.0
  + **azureml-pipeline-steps**
    + Dokumentované podporované hodnoty pro `runconfig_pipeline_params` kroky kanálu služby Azure Machine Learning.
  + **azureml-pipeline-core**
    + Přidání možnosti CLI pro stažení výstupu ve formátu JSON pro příkazy kanálu.
  + **AzureML-vlak – automl**
    + Rozdělení AzureML-vlak-AutoML do dvou balíčků, klientského balíčku AzureML-vlak-AutoML-Client a balíčku školení na ML – balíček AzureML-vlak-AutoML-runtime
  + **azureml-train-automl-client**
    + Přidali jsme tenký klient pro odesílání AutoML experimentů, aniž byste museli instalovat žádné závislosti ve strojovém učení místně.
    + Pevné protokolování automaticky zjištěných prodlevy, velikostí oken a maximálního počtu horizontů ve vzdálených spuštěních.
  + **azureml-train-automl-runtime**
    + Přidání nového balíčku AutoML k izolaci strojového učení a běhových komponent z klienta.
  + **AzureML-contrib-výuka-RL**
    + Přidala se podpora výuky pro zvýšení podpory v sadě SDK.
    + Přidala se podpora AmlWindowsCompute v sadě RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Sada SDK Azure Machine Learning pro Python v 1.0.74

  + **Funkce ve verzi Preview**
    + **azureml-contrib-dataset**
      + Po importu AzureML-contrib-DataSet můžete zavolat `Dataset.Labeled.from_json_lines` místo `._Labeled` k vytvoření datové sady s popiskem.
      + Při volání `to_pandas_dataframe` pro datovou sadu s názvem s možností stažení teď můžete určit, jestli se mají stávající soubory přepsat nebo ne.
      + Při volání `keep_columns` nebo `drop_columns` , které vede k vyřazení sloupce časové řady, popisku nebo obrázku, jsou pro datovou sadu zahozeny i odpovídající funkce.
      + Opravili jsme problémy s PyTorch zavaděčem při volání `dataset.to_torchvision()` .

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Přidání profilování modelu do rozhraní příkazového řádku Preview
    + Opravuje zásadní změnu v Azure Storage způsobující selhání AzureML CLI.
    + Přidání typu Load Balancer do MLC pro AKS typy
  + **azureml-automl-core**
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
    + Přidání nového amlcompute.get_active_runs API, který vrací generátor pro spuštění a spuštění ve frontě na daném amlcompute.
    + Do MLC pro typy AKS byl přidán typ Load Balancer.
    + Byl přidán append_prefix parametr bool pro download_files v run.py a download_artifacts_from_prefix v artifacts_client. Tento příznak se používá k selektivnímu navýšení cesty k původnímu souboru, takže se do output_directory přidá jenom název souboru nebo složky.
    + Opravte problém deserializace pro `run_config.yml` použití s využitím datové sady.
    + Při volání `keep_columns` nebo `drop_columns` , které vede k vyřazení sloupce časové řady, jsou pro datovou sadu zahozeny i odpovídající funkce.
  + **azureml-interpret**
    + Aktualizovaná verze interpretu – komunita 0.1.0.3
  + **AzureML-vlak – automl**
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

Cílová stránka pracovního prostoru pro spolupráci se [https://ml.azure.com](https://ml.azure.com) vylepšila a změnila jako Azure Machine Learning Studio.

Z studia můžete provádět výukové, testovací, nasazování a správu Azure Machine Learningch prostředků, jako jsou datové sady, kanály, modely, koncové body a další.

Z studia získáte přístup k následujícím webovým nástrojům pro vytváření obsahu:

| Webový nástroj | Popis | 
|-|-|-|
| Virtuální počítač poznámkového bloku (Preview) | Plně spravovaná cloudová pracovní stanice | 
| [Automatizované Machine Learning](tutorial-first-experiment-automated-ml.md) (Preview) | Neexistují žádné zkušenosti s kódem pro automatizaci vývoje modelů strojového učení. | 
| [Návrhář](concept-designer.md) | Nástroj pro modelování strojového učení, který se dřív jmenoval jako vizuální rozhraní | 


### <a name="azure-machine-learning-designer-enhancements"></a>Vylepšení návrháře Azure Machine Learning

+ Dříve označované jako vizuální rozhraní 
+    11 nových [modulů](algorithm-module-reference/module-reference.md) , včetně doporučení, klasifikátorů a školicích nástrojů, včetně techniků, ověřování a transformace dat.

### <a name="r-sdk"></a>R SDK 
 
Odborníci přes data a vývojáři AI používají [Azure Machine Learning SDK pro R](https://github.com/Azure/azureml-sdk-for-r) k sestavování a spouštění pracovních postupů strojového učení pomocí Azure Machine Learning.

Sada Azure Machine Learning SDK pro R používá `reticulate` balíček k vytvoření vazby na sadu Python SDK. Pomocí vazby přímo na Python umožňuje sada SDK pro R přístup k základním objektům a metodám implementovaným v sadě Python SDK z libovolného prostředí jazyka R, které zvolíte.

Mezi hlavní funkce sady SDK patří:

+    Správa cloudových prostředků pro monitorování, protokolování a organizaci experimentů strojového učení
+    Výukové modely s využitím cloudových prostředků, včetně školení modelů pomocí GPU
+    Nasaďte modely jako WebServices na Azure Container Instances (ACI) a Azure Kubernetes Service (AKS).

Úplnou dokumentaci najdete na [webu balíčku](https://azure.github.io/azureml-sdk-for-r) .

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integrace s Event Grid 

Azure Machine Learning je teď poskytovatel prostředků pro Event Grid, můžete nakonfigurovat události strojového učení prostřednictvím rozhraní příkazového řádku Azure Portal nebo Azure CLI. Uživatelé můžou vytvářet události pro dokončení běhu, registraci modelů, nasazení modelu a rozpoznání posunu dat. Tyto události mohou být směrovány do obslužných rutin událostí podporovaných Event Grid pro spotřebu. Další podrobnosti najdete v článku věnovaném [schématu](../event-grid/event-schema-machine-learning.md) událostí Machine Learning a [kurzům](how-to-use-event-grid.md) .

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Sada SDK Azure Machine Learning pro Python v 1.0.72

+ **Nové funkce**
  + Přidali jsme monitorování datových sad prostřednictvím balíčku [**AzureML-**](/python/api/azureml-datadrift) datamarketo, což umožňuje monitorovat datové sady časových řad pro posun dat nebo jiné statistické změny v průběhu času. Výstrahy a události mohou být aktivovány, pokud je zjištěn posun nebo jsou splněny jiné podmínky pro data. Podrobnosti najdete v [naší dokumentaci](how-to-monitor-datasets.md) .
  + Oznamujeme dvě nové edice (označované také jako zaměnitelné SKU) v Azure Machine Learning. V této verzi teď můžete vytvořit pracovní prostor Basic nebo Enterprise Azure Machine Learning. Všechny existující pracovní prostory budou ve výchozím nastavení pro edici Basic a můžete přejít na Azure Portal nebo do studia a kdykoli upgradovat pracovní prostor. Z Azure Portal můžete vytvořit pracovní prostor Basic nebo Enterprise. Další informace najdete v [naší dokumentaci](./how-to-manage-workspace.md) . V sadě SDK se dá edice pracovního prostoru určit pomocí vlastnosti SKU objektu pracovního prostoru.
  + Provedli jsme také vylepšení Azure Machine Learning COMPUTE – nyní můžete zobrazit metriky pro vaše clustery (jako celkem uzlů, spuštěné uzly, celková základní kvóta) v Azure Monitor, kromě zobrazení diagnostických protokolů pro ladění. Kromě toho můžete také zobrazit aktuálně spuštěné nebo ve frontě spuštění ve vašem clusteru a podrobnosti, jako jsou například IP adresy různých uzlů v clusteru. Můžete je zobrazit buď na portálu, nebo pomocí odpovídajících funkcí v sadě SDK nebo rozhraní příkazového řádku.

  + **Funkce ve verzi Preview**
    + Ve službě Azure Machine Learning COMPUTE vydáváme podporu pro šifrování disků místního disku SSD. Vyžádejte si lístek technické podpory, který vám umožní využít tuto funkci pro vaše předplatné.
    + Public Preview Azure Machine Learning odvození dávky. Azure Machine Learning odvození dávky cílí na velké odvozené úlohy, které nerozlišují čas. Odvození dávky poskytuje nákladově efektivní výpočetní škálování pro odvození, s využitím neparalelní propustnosti pro asynchronní aplikace. Je optimalizovaný pro účely vysoké propustnosti, zaznamenání a zapomenout na odvození přes velké kolekce dat.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
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
  + **Azure-CLI-ml**
    + Rozhraní příkazového řádku teď podporuje balení modelu.
    + Byl přidán objekt CLI datové sady. Další informace: `az ml dataset --help`
    + Přidání podpory pro nasazení a sbalení podporovaných modelů (ONNX, scikit-učení a TensorFlow) bez instance InferenceConfig
    + Přidání příznaku přepsání pro nasazení služby (ACI a AKS) v sadě SDK a rozhraní příkazového řádku. Pokud je tato akce k dispozici, přepíše existující službu, pokud již existuje služba s názvem. Pokud služba neexistuje, vytvoří novou službu.
    + Modely lze registrovat pomocí dvou nových rozhraní Onnx a Tensorflow. -Registrace modelu akceptuje ukázková vstupní data, ukázkové výstupní data a konfiguraci prostředků pro model.
  + **azureml-automl-core**
    + Školení iterace se spustí v podřízeném procesu jenom v případě, že jsou nastavená omezení za běhu.
    + Přidali jsme guardrail pro úlohy předpovědi, abyste zkontrolovali, jestli zadané max_horizon způsobí problémy s pamětí v daném počítači nebo ne. Pokud bude, zobrazí se zpráva guardrail.
    + Přidání podpory pro komplexní frekvence, jako jsou dva roky a jeden měsíc. – Byla přidána srozumitelná chybová zpráva, pokud nelze určit četnost.
    + Pokud chcete vyřešit selhání nasazení modelu, přidejte do automaticky generovaného conda ENV výchozí hodnoty AzureML.
    + Povolí převod mezilehlých dat v kanálu Azure Machine Learning na tabulkovou sadu dat a používá se v `AutoMLStep` .
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
    + Bylo přidáno upozornění pro výpočetní cíl, který není AmlCompute, který nepodporujeme.
    + Přidání LightGMB Estimator do balíčku AzureML-contrib-gbdt
  + [**azureml-core**](/python/api/azureml-core)
    + Rozhraní příkazového řádku teď podporuje balení modelu.
    + Přidejte upozornění na zastaralost pro vystaralá rozhraní API datové sady. Viz upozornění na změnu rozhraní API datové sady na adrese https://aka.ms/tabular-dataset .
    + [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)Pokud je datová sada zaregistrovaná, změňte název a verzi registrace.
    + Oprava chyby, která ScriptRunConfig s datovou sadou jako argumentu se opakovaně nedá použít k odeslání experimentu.
    + Datové sady načtené během spuštění budou sledovány a lze je zobrazit na stránce Podrobnosti o spuštění nebo volání [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) po dokončení spuštění.
    + Povolí převod mezilehlých dat v kanálu Azure Machine Learning na tabulkovou sadu dat a používá se v [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Přidání podpory pro nasazení a sbalení podporovaných modelů (ONNX, scikit-učení a TensorFlow) bez instance InferenceConfig
    + Přidání příznaku přepsání pro nasazení služby (ACI a AKS) v sadě SDK a rozhraní příkazového řádku. Pokud je tato akce k dispozici, přepíše existující službu, pokud již existuje služba s názvem. Pokud služba neexistuje, vytvoří novou službu.
    +  Modely lze registrovat pomocí dvou nových rozhraní Onnx a Tensorflow. Registrace modelu přijímá ukázková vstupní data, ukázková výstupní data a konfiguraci prostředků pro model.
    + Bylo přidáno nové úložiště dat pro Azure Database for MySQL. Byl přidán příklad pro použití Azure Database for MySQL v DataTransferStep v kanálu Azure Machine Learning.
    + Přidání funkce pro přidání a odebrání značek z experimentů, které přidaly funkce pro odebrání značek z spuštění
    + Přidání příznaku přepsání pro nasazení služby (ACI a AKS) v sadě SDK a rozhraní příkazového řádku. Pokud je tato akce k dispozici, přepíše existující službu, pokud již existuje služba s názvem. Pokud služba neexistuje, vytvoří novou službu.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Přesunuto `azureml-contrib-datadrift` z `azureml-datadrift`
    + Přidání podpory pro datové sady časových řad monitorování pro posun a další statistické míry
    + Nové metody `create_from_model()` a `create_from_dataset()` [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) třídy. `create()`Metoda bude zastaralá.
    + Úpravy vizualizací v Pythonu a uživatelském rozhraní v Azure Machine Learning Studiu.
    + Podpora denního a měsíčního plánování monitorování, a to i denně pro monitorování datových sad.
    + Podpora doplňování metrik sledování dat pro analýzu historických dat pro monitorování datových sad
    + Různé opravy chyb
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + k odeslání Azure Machine Learningého kanálu ze souboru kanálu už není potřeba soubor AzureML-dataprep `yaml` .
  + [**AzureML-vlak – automl**](/python/api/azureml-train-automl-runtime/)
    + Pokud chcete vyřešit selhání nasazení modelu, přidejte do automaticky generovaného conda ENV výchozí hodnoty AzureML.
    + AutoML vzdálené školení teď zahrnuje AzureML – výchozí nastavení, které umožňuje znovu použít školicí obálku pro odvození.
  + **azureml-train-core**
    + Přidání podpory PyTorch 1,3 v [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) Estimator

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
  + Importovat z SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Sada SDK Azure Machine Learning pro Python v 1.0.69

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Omezení modelových vysvětlení tak, aby se místo výpočetních vysvětlení pro každé spuštění co nejlépe spouštělo. Změna chování místních, vzdálených a ADB.
    + Přidání podpory pro vysvětlení modelu na vyžádání pro uživatelské rozhraní
    + Přidání psutil jako závislosti `automl` a zahrnutí psutil jako závislosti conda v amlcompute.
    + Opravili jsme problém s heuristickými prodlevy a velikostmi oken pro prognózování dat, jejichž série může způsobit chyby lineárního algebraický.
      + Přidání tisku pro heuristicky určené parametry při spuštění prognózování.
  + **azureml-contrib-datadrift**
    + Přidání ochrany při vytváření výstupních metrik, pokud posun úrovně DataSet není v první části.
  + **azureml-contrib-interpret**
    + balíček AzureML-contrib-vysvětlit-model byl přejmenován na AzureML-contrib-Interpret
  + **azureml-core**
    + Bylo přidáno rozhraní API pro zrušení registrace datových sad. `dataset.unregister_all_versions()`
    + balíček AzureML-contrib-vysvětlit-model byl přejmenován na AzureML-contrib-interpretace.
  + **[azureml-core](/python/api/azureml-core)**
    + Bylo přidáno rozhraní API pro zrušení registrace datových sad. integrován. [unregister_all_versions ()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Bylo přidáno rozhraní API datové sady pro kontrolu času změny dat. `dataset.data_changed_time`.
    + Schopnost využívat `FileDataset` a `TabularDataset` jako vstupy pro `PythonScriptStep` , `EstimatorStep` a `HyperDriveStep` v Azure Machine Learning kanálu
    + Vylepšení výkonu `FileDataset.mount` pro složky s velkým počtem souborů
    + Schopnost využívat [datovou sadu](/python/api/azureml-core/azureml.data.filedataset) a [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) jako vstupy pro [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)a [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) v kanálu Azure Machine Learning.
    + Výkon objektu DataSet. [připojení ()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) bylo vylepšeno pro složky s velkým počtem souborů.
    + Přidání adresy URL ke známým doporučením chyb v podrobnostech o spuštění.
    + Opravili jsme chybu v run.get_metrics, kde se požadavky nezdařily, pokud by běželo příliš mnoho podřízených objektů.
    + Opravili jsme chybu v [Run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) , kde se požadavky nezdařily, pokud by běželo příliš mnoho podřízených objektů.
    + Přidala se podpora pro ověřování v clusteru Arcadia.
    + Vytvoření objektu experimentu Získá nebo vytvoří experiment v pracovním prostoru Azure Machine Learning pro sledování historie spuštění. ID experimentu a archivovaný čas se naplní v objektu experiment při vytváření. Příklad: experiment = experiment (pracovní prostor, "nový experiment") experiment_id = experiment.id Archive () a reactivate () jsou funkce, které lze volat v experimentu pro skrytí a obnovení experimentu v uživatelském rozhraní nebo ve výchozím nastavení ve volání pro výpis experimentů. Pokud se vytvoří nový experiment se stejným názvem jako archivovaný experiment, můžete při opětovné aktivaci přejmenovat archivovaný experiment tak, že předáte nový název. Může existovat pouze jeden aktivní experiment se zadaným názvem. Příklad: experiment1 = experiment (pracovní prostor, "aktivní Experimenta") experiment1. Archive () # vytvoří nový aktivní experiment se stejným názvem jako Archivovaná. experiment2. = Experiment (pracovní prostor, "aktivní experiment") experiment1. reactivate (new_name = "předchozí aktivní experiment"). seznam statických metod () při experimentování může pobírat filtr názvů a filtr element ViewType. Element ViewType hodnoty jsou "ACTIVE_ONLY", "ARCHIVED_ONLY" a "ALL". Příklad: archived_experiments = experiment. list (pracovní prostor, view_type = "ARCHIVED_ONLY") all_first_experiments = experiment. list (pracovní prostor, název = "první experiment", view_type = "ALL")
    + Podpora použití prostředí pro nasazení modelů a aktualizace služby
  + **azureml-datadrift**
    + Atribut show třídy DataDriftDector už nepodporuje nepovinný argument with_details. Atribut show bude prezentovat pouze koeficient posunu dat a příspěvek na posun dat pro sloupce funkcí.
    + Změny chování atributu DataDriftDetector get_output:
      + Vstupní parametr start_time, end_time jsou volitelné místo povinné;
      + Zadání specifických start_time a/nebo end_time pomocí konkrétního run_id ve stejném vyvolání způsobí výjimku chyby, protože se vzájemně vylučují.
      + Po zadání specifických start_time a/nebo end_time se vrátí jenom výsledky plánovaných spuštění.
      + Parametr ' daily_latest_only ' je zastaralý.
    + Podpora načítání výstupů pro posun dat na základě datové sady
  + **azureml-explain-model**
    + Přejmenuje balíček AzureML-vysvětlit-model na AzureML-interpretace, takže starý balíček zůstane pro zpětnou kompatibilitu nyní.
    + Opravená `automl` Chyba s nezpracovanými vysvětleními nastavenými na úlohu klasifikace místo regrese ve výchozím nastavení při stažení z ExplanationClient
    + Přidat podporu pro `ScoringExplainer` pro vytvoření přímo pomocí `MimicWrapper`
  + **azureml-pipeline-core**
    + Vylepšený výkon pro vytváření velkých kanálů
  + **azureml-train-core**
    + Přidání podpory TensorFlow 2,0 v TensorFlow Estimator
  + **AzureML-vlak – automl**
    + Vytvoření objektu [experimentu](/python/api/azureml-core/azureml.core.experiment.experiment) Získá nebo vytvoří experiment v pracovním prostoru Azure Machine Learning pro sledování historie spuštění. ID experimentu a archivovaný čas se naplní v objektu experiment při vytváření. Příklad:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archiv ()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) a [reactivate ()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) jsou funkce, které lze volat v experimentu pro skrytí a obnovení experimentu v rámci zobrazení v uživatelském rozhraní nebo ve výchozím nastavení ve volání na seznam experimentů. Pokud se vytvoří nový experiment se stejným názvem jako archivovaný experiment, můžete při opětovné aktivaci přejmenovat archivovaný experiment tak, že předáte nový název. Může existovat pouze jeden aktivní experiment se zadaným názvem. Příklad:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Seznam statických metod [()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) při experimentování může pořizovat filtr názvů a filtr element ViewType. Hodnoty element ViewType jsou "ACTIVE_ONLY", "ARCHIVED_ONLY" a "ALL". Příklad:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Podpora použití prostředí pro nasazení modelů a aktualizace služby.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Atribut show třídy [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) už nepodporuje nepovinný argument with_details. Atribut show bude prezentovat pouze koeficient posunu dat a příspěvek na posun dat pro sloupce funkcí.
    + DataDriftDetector funkce [get_output] Python/API/AzureML-datadrift/AzureML.. DataDriftDetector. DataDriftDetector # Get-Output-start-time-None--End-time-None--Run-ID-None-) změny chování:
      + Vstupní parametr start_time, end_time jsou volitelné místo povinné;
      + Zadání specifických start_time a/nebo end_time pomocí konkrétního run_id ve stejném vyvolání způsobí výjimku chyby, protože se vzájemně vylučují.
      + Po zadání specifických start_time a/nebo end_time se vrátí jenom výsledky plánovaných spuštění.
      + Parametr ' daily_latest_only ' je zastaralý.
    + Podpora načítání výstupů pro posun dat na základě datové sady
  + **azureml-explain-model**
    + Přidání podpory pro [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py&preserve-view=true) , která se má vytvořit přímo pomocí MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Vylepšený výkon pro vytváření velkých kanálů.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Přidala se podpora TensorFlow 2,0 v [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
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
    + Přidání prostředí, která jsou s ním uspořádaná Tato prostředí jsou předem nakonfigurovaná s knihovnami pro běžné úkoly strojového učení a byla předem sestavena a ukládána do mezipaměti jako image Docker pro rychlejší provádění. Ve výchozím nastavení se zobrazí v seznamu prostředí v [pracovním prostoru](/python/api/azureml-core/azureml.core.workspace%28class%29)s předponou "AzureML".

  + **AzureML-vlak – automl**
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Přidání podpory převodu ONNX pro ADB a HDI

+ **Funkce ve verzi Preview**
  + **AzureML-vlak – automl**
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Podporované BERT a BiLSTM jako text featurizer (jenom ve verzi Preview)
    + Podporované přizpůsobení featurization pro účely sloupce a transformaci parametrů (jenom ve verzi Preview)
    + Podporovaná neupravená vysvětlení, když uživatel během školení povolí vysvětlení modelu (jenom náhled)
    + Přidání Prophet pro `timeseries` Prognózování jako kanálu pro vlak (jenom pro náhled)

  + **azureml-contrib-datadrift**
    + Balíčky přemístěné z AzureML-contrib-disčárky do AzureML-; `contrib` balíček se odebere v budoucí verzi.

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Zavedení FeaturizationConfig do AutoMLConfig a AutoMLBaseSettings
    + Zavedení FeaturizationConfig do [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) a AutoMLBaseSettings
      + Přepsat účel sloupce pro Featurization s daným typem sloupce a funkce
      + Přepsat parametry transformátoru
    + Přidání zprávy o zastaralosti pro explain_model () a retrieve_model_explanations ()
    + Přidání Prophet jako kanálu pro vlak (jenom pro náhled)
    + Přidání zprávy o zastaralosti pro explain_model () a retrieve_model_explanations ()
    + Přidání Prophet jako kanálu pro vlak (jenom pro náhled)
    + Byla přidána podpora pro automatické zjišťování cílových prodlevy, velikost posuvných oken a maximální horizont. Pokud je jedna z target_lags target_rolling_window_size nebo max_horizon nastavená na auto, použijí se heuristiky k odhadu hodnoty odpovídajícího parametru na základě školicích dat.
    + V případě pevného prognózování v případě, kdy datová sada obsahuje jeden sloupec zrnitosti, je tato zrnitosti číselného typu a mezi vlakem a testovací sadou existuje mezera.
    + Opravili jsme chybovou zprávu o duplicitním indexu ve vzdáleném spuštění při prognózování úloh.
    + Fixní prognóza v případě, kdy datová sada obsahuje jeden sloupec zrnitosti, je tato zrnitosti číselného typu a mezi vlakem a testovací sadou existuje mezera.
    + Opravili jsme chybovou zprávu o duplicitním indexu ve vzdáleném spuštění při prognózování úloh.
    + Přidání guardrail pro kontrolu, zda je datová sada nevyvážená nebo ne. V takovém případě se do konzoly zapíše zpráva guardrail.
  + **azureml-core**
    + Přidání možnosti načíst adresu URL SAS pro model v úložišti prostřednictvím objektu modelu. Např.: model.get_sas_url ()
    + Představení `run.get_details()['datasets']` získání datových sad přidružených k odeslanému běhu
    + Přidejte rozhraní API `Dataset.Tabular.from_json_lines_files` k vytvoření TabularDataset ze souborů řádků JSON. Další informace o těchto tabelárních datech v souborech řádků JSON na TabularDataset najdete v [tomto článku](how-to-create-register-datasets.md) o dokumentaci.
    + Přidání dalších polí velikosti virtuálního počítače (disk s operačním systémem, počet GPU) do funkce supported_vmsizes ()
    + Do funkce list_nodes () byla přidána další pole pro zobrazení spuštění, soukromé a veřejné IP adresy, portu atd.
    + Možnost zadat nové pole během zřizování clusteru – remotelogin_port_public_access, která se dá zapnout nebo vypnout v závislosti na tom, jestli se má port SSH nechat otevřený nebo zavřený v době vytváření clusteru. Pokud ho nezadáte, služba bude port inteligentně otevřít nebo Zavřít v závislosti na tom, jestli nasazujete cluster uvnitř virtuální sítě.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Přidání možnosti načíst adresu URL SAS pro model v úložišti prostřednictvím objektu modelu. Ex: model. [get_sas_url ()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Zaveďte běh. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' DataSets '] pro získání datových sad přidružených k odeslanému běhu
    + Přidat rozhraní API `Dataset.Tabular` .[ from_json_lines_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) pro vytvoření TabularDataset ze souborů JSON řádků. Další informace o těchto tabelárních datech v souborech řádků JSON na TabularDataset najdete v https://aka.ms/azureml-data dokumentaci.
    + Přidání dalších polí velikosti virtuálního počítače (disk s operačním systémem, počet GPU) do funkce [supported_vmsizes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Do funkce [list_nodes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) byla přidána další pole pro zobrazení spuštění, privátního a veřejné IP adresy, portu atd.
    + Možnost zadat nové pole během [zřizování](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  clusteru, která se dá nastavit na povoleno nebo zakázat v závislosti na tom, jestli se má port SSH nechat otevřený nebo zavřený v době vytváření clusteru. Pokud ho nezadáte, služba bude port inteligentně otevřít nebo Zavřít v závislosti na tom, jestli nasazujete cluster uvnitř virtuální sítě.
  + **azureml-explain-model**
    + Vylepšená dokumentace pro vysvětlení výstupů ve scénáři klasifikace.
    + Byla přidána možnost Odeslat předpovězené hodnoty y na vysvětlení pro příklady vyhodnocení. Odemkne užitečnější vizualizace.
    + Přidala se vlastnost vysvětlujícího nástroje do MimicWrapper, která umožňuje získat základní MimicExplainer.
  + **azureml-pipeline-core**
    + Přidání poznámkového bloku pro popis modulu, ModuleVersion a ModuleStep
  + **azureml-pipeline-steps**
    + Přidání RScriptStep pro podporu skriptu jazyka R spouštěného prostřednictvím kanálu AML.
    + Pevné parametry metadat při analýze v AzureBatchStep, která způsobila chybovou zprávu "přiřazení pro parametr SubscriptionId není zadáno."
  + **AzureML-vlak – automl**
    + Podporované training_data, validation_data, label_column_name weight_column_name jako formát vstupu dat
    + Přidání zprávy o zastaralosti pro explain_model () a retrieve_model_explanations ()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Přidal se [Poznámkový blok](https://aka.ms/pl-modulestep) , který popisuje [modul](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion a [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + Přidání [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) pro podporu skriptu jazyka R spouštěného prostřednictvím kanálu AML.
    + Pevné parametry metadat pro analýzu v [AzureBatchStep, které způsobily chybovou zprávu "přiřazení pro parametr SubscriptionId není zadáno".
  + **[AzureML-vlak – automl](/python/api/azureml-train-automl-runtime/)**
    + Podporované training_data, validation_data, label_column_name weight_column_name jako formát vstupu dat.
    + Přidání zprávy o zastaralosti pro [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) a [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Sada SDK Azure Machine Learning pro Python v 1.0.62

+ **Nové funkce**
  + Zavedli jste vlastnost `timeseries`  na TabularDataset. Tento vlastnost umožňuje snadné filtrování časových razítek u dat a TabularDataset, jako je například pořízení všech dat mezi časovým obdobím nebo nejnovějšími daty.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb Příklad poznámkového bloku.
  + Bylo povoleno školení s TabularDataset a datovou sadou. 

  + **azureml-train-core**
      + Přidání `Nccl` a `Gloo` Podpora v PyTorch Estimator

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Nastavení AutoML ' lag_length ' a LaggingTransformer se zastaralo.
    + Oprava správného ověření vstupních dat, jsou-li zadána ve formátu toku dat
    + Upravení fit_pipeline. py pro generování JSON grafu a nahrání do artefaktů.
    + Vykreslení grafu `userrun` pomocí `Cytoscape` .
  + **azureml-core**
    + Přečtěte si přečtěte si zpracování výjimek v kódu ADB a udělejte změny jako při zpracování nových chyb.
    + Bylo přidáno automatické ověřování MSI pro virtuální počítače poznámkových bloků.
    + Opravuje chybu, kde by mohly být nahrány poškozené nebo prázdné modely z důvodu neúspěšných pokusů o selhání.
    + Opravena chyba, kde `DataReference` se název změní `DataReference` , když se změní režim (například při volání `as_upload` , `as_download` nebo `as_mount` ).
    + Nastavit `mount_point` a `target_path` volitelné pro `FileDataset.mount` a `FileDataset.download` .
    + Výjimka, že sloupec časového razítka nejde najít, se zavolá, pokud se volá rozhraní API související s časovými sériovými bez dodaného sloupce časového razítka nebo jsou vyřazeny přiřazené sloupce časového razítka.
    + Sloupce s časovými intervaly by měly být přiřazeny sloupci, jehož typ je datum, jinak se očekává výjimka.
    + Sloupce s časovými intervaly přiřazení rozhraní API with_timestamp_columns můžou mít neomezenou hodnotu None nebo hrubě vyhodnotit název sloupce časového razítka. tím se vymažou dříve přiřazené sloupce časových razítek.
    + Výjimka bude vyvolána, pokud je vyřazena z hrubého zrnitosti sloupec časového razítka s označením pro uživatele, který je možné provést po vyřazení seznamu, nebo volání with_time_stamp s hodnotou None k uvolnění sloupců časových razítek.
    + Výjimka se vygeneruje, pokud se v seznamu ponechat sloupce s označením pro uživatele, který zadáváte jako with_time_stamp sloupec časového razítka, nezahrne do sloupce časového razítka pro vynechání sloupců s časovou známkou, a to v případě, že se nezahrnuje hrubá zrnitost nebo jemné sloupce časového razítka.
    + Bylo přidáno protokolování pro velikost registrovaného modelu.
  + **azureml-explain-model**
    + Pevné upozornění vytištěné na konzolu, když není nainstalované balíček Pythonu pro sbalení: "použití starší verze než podporované verze lightgbm, upgrade na verzi větší než 2.2.1"
    + Základní vysvětlení modelu stažení s horizontálního dělení pro globální vysvětlení s mnoha funkcemi
    + Vysvětlené nevyřešené příklady inicializace pro vysvětlení výstupu
    + Pevná neproměnlivá Chyba při nastavování vlastností při nahrávání s vysvětlením klienta pomocí dvou různých typů modelů
    + Do bodování bodování byl přidán get_raw param. vysvětlit (), takže jeden vysvětlující bodování může vracet jak provedenou, tak nezpracované hodnoty.
  + **AzureML-vlak – automl**
    + Zavedli jsme veřejná rozhraní API ze služby AutoML pro podporu vysvětlení z `automl` vysvětlené sady SDK – novější způsob podpory vysvětlení AutoML pomocí odpojujecích se AutoML featurization a vysvětlená podpora integrovaného vysvětlení sady SDK.
    + Odebírají se výchozí hodnoty ze vzdálených školicích prostředí.
    + Umístění úložiště výchozí mezipaměti bylo změněno z FileCacheStore na jednu až AzureFileCacheStore jedno pro AutoML v Azure Databricks cestě kódu.
    + Oprava správného ověření vstupních dat, jsou-li zadána ve formátu toku dat
  + **azureml-train-core**
    + Vrácení source_directory_data_store se zrušilo.
    + Byla přidána možnost přepsat verze balíčku po instalaci.
    + Přidala se podpora souboru Dockerfile v `environment_definition` parametru v odhady.
    + Zjednodušené parametry distribuovaného školení v odhady.

         ```python
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
+ Prozkoumejte & Příprava datových sad pro vytváření modelů
+ Sledování posunu dat pro vaše modely
+ Zobrazit poslední prostředky z řídicího panelu

V době této verze se podporují následující prohlížeče: Chrome, Firefox, Safari a Microsoft Edge Preview.

**Známé problémy:**

1. Pokud se zobrazí zpráva něco se pokazilo, aktualizujte si prohlížeč. Při načítání souborů bloků dat došlo k chybě, když probíhá nasazení.

1. Nelze odstranit ani přejmenovat soubor v poznámkových blocích a souborech. Během Public Preview můžete k provádění operací s aktualizacemi souboru použít uživatelské rozhraní Jupyter nebo terminál na virtuálním počítači poznámkového bloku. Vzhledem k tomu, že se jedná o všechny změny připojené síťové souborové systému, provedete se v pracovním prostoru poznámkového bloku přímo na virtuálním počítači.

1. Do virtuálního počítače poznámkového bloku přes SSH:
   1. Vyhledejte klíče SSH, které byly vytvořeny při instalaci virtuálního počítače. Nebo Najděte klíče v pracovním prostoru Azure Machine Learning > otevřete kartu COMPUTE > v seznamu vyberte virtuální počítač Poznámkový blok, > otevřete jeho vlastnosti: Zkopírujte klíče z dialogového okna.
   1. Importujte tyto veřejné a soukromé klíče SSH do svého místního počítače.
   1. Použijte je pro SSH do virtuálního počítače poznámkového bloku.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Sada SDK Azure Machine Learning pro Python v 1.0.60

+ **Nové funkce**
  + Byl představen objekt DataSet, který odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Soubory mohou být libovolného formátu. Soubor DataSet poskytuje možnost stahovat soubory nebo je připojit k výpočetnímu prostředí. 
  + Přidání podpory kanálu YAML pro krok PythonScript, krok adla, krok datacihly, DataTransferStep a AzureBatch

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + AutoArima je teď navržený kanál jenom pro náhled.
    + Vylepšené zasílání zpráv o chybách pro prognózování.
    + Vylepšené protokolování pomocí vlastních výjimek namísto obecného v úlohách prognózy.
    + Odebrání kontroly max_concurrent_iterations bylo zrušeno, aby bylo menší než celkový počet iterací.
    + Modely AutoML nyní vracejí AutoMLExceptions
    + Tato verze vylepšuje výkon spuštění automatizovaného místního spuštění machine learningu.
  + **azureml-core**
    + Zaveďte Dataset.get_all (pracovní prostor), která vrátí slovník `TabularDataset` a `FileDataset` objekty s klíčem podle názvu registrace.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Zaveďte `parition_format` jako argument do `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_parquet.files` . Informace o oddílu každé cesty k datům budou extrahovány do sloupců v závislosti na zadaném formátu. {column_name} vytvoří sloupec řetězců a {column_name: RRRR/MM/DD/HH/MM/SS} vytvoří sloupec data a času, kde ' yyyy ', ' MM ', ' DD ', ' HH ', ' mm ' a ' ss ' slouží k extrakci roku, měsíce, dne, hodin, minut a sekund pro typ DateTime. Partition_format by měl začít od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu.. /USA/2019/01/01/data.csv ' kde je oddíl podle země a čas partition_format = '/{Country}/{PartitionDate: RRRR/MM/DD}/data.csv ' vytvoří řetězcový sloupec ' země ' s hodnotou ' USA ' a sloupec DateTime ' PartitionDate ' s hodnotou ' 2019-01-01 '.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Zaveďte `partition_format` jako argument do `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_parquet.files` . Informace o oddílu každé cesty k datům budou extrahovány do sloupců v závislosti na zadaném formátu. {column_name} vytvoří sloupec řetězců a {column_name: RRRR/MM/DD/HH/MM/SS} vytvoří sloupec data a času, kde ' yyyy ', ' MM ', ' DD ', ' HH ', ' mm ' a ' ss ' slouží k extrakci roku, měsíce, dne, hodin, minut a sekund pro typ DateTime. Partition_format by měl začít od pozice prvního klíče oddílu až do konce cesty k souboru. Například s ohledem na cestu.. /USA/2019/01/01/data.csv ' kde je oddíl podle země a čas partition_format = '/{Country}/{PartitionDate: RRRR/MM/DD}/data.csv ' vytvoří řetězcový sloupec ' země ' s hodnotou ' USA ' a sloupec DateTime ' PartitionDate ' s hodnotou ' 2019-01-01 '.
    + `to_csv_files` metody a byly `to_parquet_files` přidány do `TabularDataset` . Tyto metody umožňují převod mezi `TabularDataset` a a `FileDataset` pomocí převodu dat do souborů v zadaném formátu.
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
  + **AzureML-vlak – automl**
    + Vytvořená funkce pro instalaci konkrétních verzí pytorch v 1.1.0, :::no-loc text="cuda"::: toolkit 9,0, pytorch-transformes, která je nutná pro povolení Bert/XLNet ve vzdáleném prostředí Python runtime.
  + **azureml-train-core**
    + Prvotní selhání některých chyb definice prostoru s parametry přímo v sadě SDK namísto na straně serveru.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Sada Azure Machine Learning data PREP SDK v 1.1.14
+ **Opravy chyb a vylepšení**
  + Umožnění psaní do ADLS/ADLSGen2 s použitím nezpracovaných cest a přihlašovacích údajů.
  + Opravili jsme chybu, která způsobila `include_path=True` nefunkčnost `read_parquet` .
  + Opravila se `to_pandas_dataframe()` chyba způsobená výjimkou: Neplatná hodnota vlastnosti: hostSecret.
  + Opravili jsme chybu, kdy se soubory v DBFS v režimu Spark nepodařilo přečíst.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Sada SDK Azure Machine Learning pro Python v 1.0.57
+ **Nové funkce**
  + Povoleno `TabularDataset` , aby je bylo možné spotřebovat pomocí AutomatedML. Další informace o `TabularDataset` najdete na webu https://aka.ms/azureml/howto/createdatasets .

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Certifikát TLS/SSL teď můžete aktualizovat pro vyhodnocování koncového bodu nasazeného v clusteru AKS pro vygenerovaný Microsoft i pro certifikát zákazníka.
  + **azureml-automl-core**
    + Opravili jsme problém v AutoML, řádky s chybějícími popisky se neodebraly správně.
    + Vylepšené protokolování chyb v AutoML; všechny chybové zprávy se teď vždycky zapisují do souboru protokolu.
    + AutoML aktualizovala své připnutí balíčku tak, aby zahrnovala `azureml-defaults` , `azureml-explain-model` a `azureml-dataprep` . AutoML už nebude upozorňovat na neshody balíčků (s výjimkou `azureml-train-automl` balíčku).
    + Opravili jsme problém, ve `timeseries`  kterém jsou rozdělení CV nerovné velikosti způsobující selhání výpočtu bin.
    + Pokud při spuštění iterace kompletu pro školicí typ pro křížové ověření došlo k potížím při stahování modelů vyškolených na celou datovou sadu, máme nekonzistenci mezi závažím modelu a modely, které byly dodány do částice hlasování.
    + Opravili jsme chybu, která se vyvolala při výuce a/nebo ověřovací popisky (y a y_valid), a to ve formě PANDAS dataframe, ale ne jako numpy Array.
    + Opravili jsme problém s prognózou úkolů v případě, že v logických sloupcích vstupních tabulek nebyl žádný nalezen.
    + Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování. – Povolí uživatelům AutoML odstranit zrna ze sady testů, která neexistuje v školicí sadě při prognózování.
  + **azureml-core**
    + Opravili jsme problém s řazením parametrů blob_cache_timeout.
    + Do systémových chyb byly přidány typy výjimek, které se vejdou a transformují.
    + Přidání podpory pro Key Vault tajných kódů pro vzdálené běhy Přidejte do trezoru klíčů přidruženého k vašemu pracovnímu prostoru třídu AzureML. Core. trezor. Podporované operace:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.keyvault.Keyvault.set_secret (název, hodnota)
      + azureml.core.keyvault.Keyvault.set_secrets (secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret (název)
      + azureml.core.keyvault.Keyvault.get_secrets (secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets ()
    + Další metody získání výchozího trezoru klíčů a získání tajných kódů při vzdáleném spuštění:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.run.Run.get_secret (název)
      + azureml.core.run.Run.get_secrets (secrets_list)
    + Přidání dalších parametrů přepsání do příkazu příkazového řádku pro odeslání – HyperDrive
    + Zlepšení spolehlivosti volání rozhraní API při opakovaných pokusech o výjimky knihovny pro běžné požadavky.
    + Přidání podpory pro odeslání spuštění z odeslaného běhu.
    + Opravili jsme problém s tokenem SAS s vypršenou platností ve sledovacím procesu, což způsobilo, že se soubory zastavily po vypršení platnosti počátečního tokenu
    + Podporuje se import souborů HTTP CSV/TSV v sadě DataSet Python SDK.
    + Zastaralá metoda pracovního prostoru. Setup (). Zpráva s upozorněním, která se zobrazuje uživatelům, navrhuje místo toho použití Create () nebo Get ()/from_config ().
    + Přidání Environment.add_private_pip_wheel (), která umožňuje nahrávání privátních vlastních balíčků Pythonu `whl` do pracovního prostoru a jejich bezpečné použití pro sestavení nebo vyhodnotit prostředí.
    + Certifikát TLS/SSL teď můžete aktualizovat pro vyhodnocování koncového bodu nasazeného v clusteru AKS pro vygenerovaný Microsoft i pro certifikát zákazníka.
  + **azureml-explain-model**
    + Přidání parametru pro přidání ID modelu do vysvětlení při nahrávání.
    + Přidání `is_raw` značek k vysvětlení v paměti a nahrání.
    + Přidala se podpora a testy pytorch pro balíček pro AzureML-vysvětlující-model.
  + **azureml-opendatasets**
    + Podporuje detekci a protokolování automatického testovacího prostředí.
    + Přidali jsme třídy, které nám pomohou načíst populace podle země a PSČ.
  + **azureml-pipeline-core**
    + Přidala se vlastnost Label na vstupní a výstupní definice portů.
  + **azureml-telemetry**
    + Opravila se nesprávná konfigurace telemetrie.
  + **AzureML-vlak – automl**
    + Opravili jsme chybu, když při instalaci došlo k chybě, při chybě se nepovedlo přihlásit pole chyby pro spuštění instalačního programu, takže se neuložilo v nadřazeném spuštění "chyby".
    + Opravili jsme problém v AutoML, řádky s chybějícími popisky se neodebraly správně.
    + Umožní uživatelům AutoML vyřadit řadu školení, která nejsou dostatečně dlouhá při prognózování.
    + Povolí uživatelům AutoML odstranit zrna ze sady testů, která neexistuje v školicí sadě při prognózování.
    + Nyní AutoMLStep projde `automl` konfigurací do back-endu, aby se předešlo jakýmkoli problémům při změnách nebo přidávání nových parametrů konfigurace.
    + AutoML data Guardrail je teď ve verzi Public Preview. Po školení se uživateli zobrazí sestava Guardrail dat (pro úlohy klasifikace/regrese) a také k ní bude mít přístup prostřednictvím rozhraní SDK API.
  + **azureml-train-core**
    + Přidala se podpora Torch 1,2 v PyTorch Estimator.
  + **azureml-widgets**
    + Vylepšené maticové grafy pro školení klasifikace.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Sada Azure Machine Learning data PREP SDK v 1.1.12
+ **Nové funkce**
  + Seznamy řetězců lze nyní předat jako vstup do `read_*` metod.

+ **Opravy chyb a vylepšení**
  + Výkon `read_parquet` se vylepšil při spuštění ve Sparku.
  + Opravili jsme problém, kdy `column_type_builder` se nezdařil v případě jednoho sloupce s nejednoznačnými formáty data.

### <a name="azure-portal"></a>Portál Azure Portal
+ **Funkce Preview**
  + Streamování protokolů a výstupních souborů je teď k dispozici na stránkách s podrobnostmi o spuštění. Když je zapnutý přepínač Preview, soubory se budou v reálném čase aktualizovat.
  + Možnost nastavit kvótu na úrovni pracovního prostoru je vydaná ve verzi Preview. Kvóty AmlCompute se přidělují na úrovni předplatného, ale teď vám umožňuje distribuovat tuto kvótu mezi pracovními prostory a přidělit je pro spravedlivé sdílení a zásady správného řízení. Stačí kliknout na okno **používání a kvóty** v levém navigačním panelu pracovního prostoru a vybrat kartu **Konfigurace kvót** . Musíte být správcem předplatného, aby bylo možné nastavit kvóty na úrovni pracovního prostoru, protože se jedná o operaci mezi pracovními prostory.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Sada SDK Azure Machine Learning pro Python v 1.0.55

+ **Nové funkce**
  + Ověřování založené na tokenech se teď podporuje pro volání do koncového bodu nasazeného v AKS. Budeme dál podporovat aktuální ověřování založené na klíčích a uživatelé můžou používat jeden z těchto ověřovacích mechanismů současně.
  + Možnost Registrovat úložiště objektů blob, které je za virtuální sítí (VNet) jako úložiště dat.

+ **Opravy chyb a vylepšení**
  + **azureml-automl-core**
    + Opravuje chybu, při které je velikost ověřování pro rozdělení CV malá a výsledkem je špatný odhad vs. opravdové grafy pro regresi a předpovědi.
    + V případě, že došlo k chybě při spuštění, je k dispozici protokolování úloh prognózování na vzdálené běhy. nyní se uživateli zobrazí komplexní chybová zpráva.
    + Opravené chyby, `Timeseries` Pokud příznak předběžného zpracování má hodnotu true.
    + Provedli jsme některé chybové zprávy při ověřování dat předpovědi, které jsou nenapadnutelné.
    + Snížená spotřeba paměti AutoML se spouští vyřazením a/nebo opožděným načítáním datových sad, zejména mezi vytvářením procesů.
  + **AzureML-contrib-vysvětlený model**
    + Přidání příznaku model_task pro vysvětlení, aby uživatel mohl přepsat výchozí automatickou logiku odvození pro typ modelu
    + Změny widgetů: automaticky se nainstalují pomocí nástroje `contrib` , žádné další `nbextension` vysvětlení instalace/povolení-podpora s důležitostí globálních funkcí (například Permutative).
    + Změny řídicího panelu: – pole vykresluje a Violin se kromě `beeswarm` vykreslení na stránce souhrnu zobrazuje rychleji, což vysvětluje, jak se v horním horním rohu `beeswarm` vypočítala změna horních a užitečných zpráv, které vysvětlují, jak jsou vypočítány shora, užitečné přizpůsobitelné zprávy místo grafů
  + **azureml-core**
    + Přidání metody model. Package () pro vytváření imagí Docker a fázemi, které zapouzdřují modely a jejich závislosti.
    + Místní služby WebService se aktualizovaly, aby přijímaly InferenceConfigs obsahující objekty prostředí.
    + Pevný model. Register () vytvářející neplatné modely, pokud '. ' (pro aktuální adresář) se předává jako parametr model_path.
    + Přidejte Run.submit_child, funkce experimenty experimentují. odeslat při určení spuštění jako nadřazeného objektu odeslaného podřízeného spuštění.
    + Podporují možnosti konfigurace z modelu. Zaregistrujte se v Run.register_model.
    + Možnost spouštět v existujícím clusteru úlohy JAR.
    + Nyní podporuje parametry instance_pool_id a cluster_log_dbfs_path.
    + Přidání podpory pro použití objektu prostředí při nasazování modelu na webovou službu. Objekt prostředí se teď dá zadat jako součást objektu InferenceConfig.
    + Přidání mapování appinsifht pro nové oblasti – centralus-westus-northcentralus
    + Přidali jsme dokumentaci pro všechny atributy ve všech třídách úložiště dat.
    + Přidání parametru blob_cache_timeout do `Datastore.register_azure_blob_container` .
    + Do AzureML. Core. Environment. Environment se přidaly metody save_to_directory a load_from_directory.
    + Do CLI se přidaly příkazy AZ ml Environment download a AZ ml Environment Register.
    + Přidala se Environment.add_private_pip_wheel metoda.
  + **azureml-explain-model**
    + Přidání sledování datové sady k vysvětlení pomocí služby DataSet (Preview).
    + Při streamování globálních vysvětlení z 10 000 na 100 se snížila výchozí velikost dávky.
    + Přidání příznaku model_task pro vysvětlení, aby uživatel mohl přepsat výchozí automatickou logiku odvození pro typ modelu.
  + **azureml-mlflow**
    + Opravená chyba v mlflow.azureml.build_image, kde jsou vnořené adresáře ignorovány.
  + **azureml-pipeline-steps**
    + Přidání možnosti spouštět úlohy JAR v existujícím clusteru Azure Databricks.
    + Přidání podpory instance_pool_id a parametrů cluster_log_dbfs_path pro krok DatabricksStep
    + Přidání podpory pro parametry kanálu v kroku DatabricksStep
  + **AzureML-vlak – automl**
    + Přidáno `docstrings` pro soubory související se kompletem.
    + Aktualizované dokumenty do vhodnějšího jazyka pro `max_cores_per_iteration` a `max_concurrent_iterations`
    + V případě, že došlo k chybě při spuštění, je k dispozici protokolování úloh prognózování na vzdálené běhy. nyní se uživateli zobrazí komplexní chybová zpráva.
    + Z poznámkového bloku kanálu se odebrala get_data `automlstep` .
    + Začala podpora `dataprep` v `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Sada Azure Machine Learning data PREP SDK v 1.1.10

+ **Nové funkce**
  + Nyní můžete požádat o spuštění konkrétních kontrolorů (například histogram, bodový graf atd.) pro konkrétní sloupce.
  + Do se přidal argument paralelizovat `append_columns` . Pokud má hodnotu true, data se načtou do paměti, ale spuštění se spustí paralelně. Pokud je hodnota false, bude spuštění streamování, ale s jedním vláknem.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Sada SDK Azure Machine Learning pro Python v 1.0.53

+ **Nové funkce**
  + Automatizované Machine Learning nyní podporuje školicí modely ONNX na vzdáleném cíli výpočtů.
  + Azure Machine Learning teď nabízí možnost pokračovat v školení z předchozích souborů běhu, kontrolního bodu nebo souborů modelu.
    + Naučte se [používat odhady k obnovení školení z předchozího běhu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) .

+ **Opravy chyb a vylepšení**
  + **Azure-CLI-ml**
    + Příkazy rozhraní příkazového řádku "nasadit model" a "aktualizace služby" nyní přijímají parametry, konfigurační soubory nebo kombinaci těchto dvou. Parametry mají přednost před atributy v souborech.
    + Popis modelu se teď dá aktualizovat po registraci.
  + **azureml-automl-core**
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + Přidání podpory pro kanály NimbusML odhady & pro použití v rámci AutoML odhady.
    + Oprava chyby v postupu výběru kompletu, který nemusel zbytečně rozšiřovat výsledný komplet, i když skóre zůstávala konstantní.
    + Povolí opakované použití některých featurizations u rozdělení CV pro úlohy předpovědi. Tím se zrychlí spuštění instalačního programu zhruba n_cross_validations pro nákladné featurizations, jako je prodlevy a valení okna.
    + Řešení problému, pokud je čas mimo PANDAS podporovaný časový rozsah. V případě, že je čas menší než PD, teď vyvoláme výjimku. Timestamp. min nebo větší než PD Timestamp. max
    + Prognózování teď umožňuje různé frekvence v vlakových a testovacích sadách, pokud je jde zarovnávat. Například "čtvrtletní zahájení v lednu" a "čtvrtletní začátek v říjnu" lze zarovnávat.
    + Do TimeSeriesTransformer se přidala vlastnost Parameters.
    + Odeberte staré třídy výjimek.
    + V úkolech prognózy `target_lags` teď parametr přijímá jednu celočíselnou hodnotu nebo seznam celých čísel. Pokud bylo zadáno celé číslo, bude vytvořena pouze jedna prodleva. Pokud je k dispozici seznam, budou provedeny jedinečné hodnoty prodlevy. target_lags = [1, 2, 2, 4] vytvoří prodlevy jednu, dvě a čtyři tečky.
    + Opravte chybu týkající se ztráty typů sloupců po transformaci (propojená chyba);
    + V nástroji `model.forecast(X, y_query)` povolte y_query jako typ objektu, který neobsahuje žádné (s) na začátku (#459519).
    + Přidat do výstupu očekávané hodnoty `automl`
  + **azureml-contrib-datadrift**
    +  Vylepšení ukázkového poznámkového bloku, včetně přepnutí do programu AzureML-opendatasets namísto funkcí AzureML-contrib-opendatasets a zlepšení výkonu při rozšiřování dat
  + **AzureML-contrib-vysvětlený model**
    + Pevné transformace argumentu pro nástroj pro vysvětlení VÁPNa pro nezpracovaný význam funkcí v balíčku AzureML-contrib-vysvětlit-model
    + Přidání segmentů k vysvětlení obrázků v popisu obrázku pro balíček AzureML-contrib-vysvětlit-model
    + Přidání podpory zhuštění scipy pro LimeExplainer
    + Přidáno `batch_size` pro napodobení `include_local=False` , když, pro streamování globálních vysvětlení v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel
  + **AzureML-contrib-featureengineering**
    + Oprava pro volání set_featurizer_timeseries_params (): Změna typu hodnoty dict – a prázdná check – přidat Poznámkový blok pro `timeseries`  featurizer
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
  + **azureml-core**
    + Přidání možnosti připojit úložiště DBFS v rozhraní příkazového řádku AzureML
    + Opravili jsme chybu pomocí nahrávání úložiště dat, kde se vytvoří prázdná složka, pokud je `target_path` spuštěná s `/`
    + Opravil `deepcopy` se problém v ServicePrincipalAuthentication.
    + Do CLI se přidaly příkazy AZ ml Environment show a AZ ml Environment list.
    + Prostředí teď podporují určení base_dockerfile jako alternativu již sestaveného base_image.
    + Nepoužívané auto_prepare_environment nastavení RunConfiguration bylo označeno jako zastaralé.
    + Popis modelu se teď dá aktualizovat po registraci.
    + Opravu chyb: model a image Delete teď poskytují další informace o načítání nadřazených objektů, které jsou na nich závislé, pokud odstranění selhalo z důvodu nadřazené závislosti.
    + Opravili jsme chybu, která vytiskla prázdné trvání pro nasazení, ke kterým dochází při vytváření pracovního prostoru pro některá prostředí.
    + Vylepšené výjimky při vytváření pracovního prostoru Tato možnost se uživatelům nezobrazí "nelze vytvořit pracovní prostor". Nejde najít... jako zpráva a místo toho se zobrazí skutečná Chyba při vytváření.
    + Přidání podpory pro ověřování tokenu v AKS WebServices.
    + Přidejte `get_token()` metodu do `Webservice` objektů.
    + Přidala se podpora rozhraní příkazového řádku pro správu datových sad Machine Learning.
    + `Datastore.register_azure_blob_container` teď může volitelně přetrvat `blob_cache_timeout` hodnotu (v sekundách), která nakonfiguruje parametry připojení blobfuse, aby se povolilo vypršení platnosti mezipaměti pro toto úložiště dat. Výchozí hodnota není časový limit, například při čtení objektu blob, zůstane v místní mezipaměti až do dokončení úlohy. Většina úloh bude preferovat toto nastavení, ale některé úlohy potřebují číst více dat z velké datové sady, než se vejde do jejich uzlů. Pro tyto úlohy ladění tohoto parametru pomůže úspěšně. Při vyladění tohoto parametru se ujistěte, že nastavení příliš nízké hodnoty může mít za následek špatný výkon, protože data používaná v epocha můžou vypršet, než se znovu použijí. Všechny čtení se budou provádět ze služby Blob Storage/síť a nikoli z místní mezipaměti, což negativně ovlivní dobu školení.
    + Popis modelu se teď dá po registraci správně aktualizovat.
    + Odstranění modelu a obrázku nyní poskytuje další informace o nadřazených objektech, které jsou na nich závislé, což způsobí selhání odstranění.
    + Vylepšete využití prostředků ve vzdálených spuštěních pomocí AzureML. mlflow.
  + **azureml-explain-model**
    + Pevné transformace argumentu pro nástroj pro vysvětlení VÁPNa pro nezpracovaný význam funkcí v balíčku AzureML-contrib-vysvětlit-model
    + Přidání podpory zhuštění scipy pro LimeExplainer
    + přidaný tvar lineárního vysvětlujícího pobalení a také další úroveň pro tabulkový popis vysvětlující lineární modely
    + pro napodobení napodobení v knihovně modelů Vysvětlete chybu, když include_local = false pro zhuštěné datové vstupy
    + Přidat do výstupu očekávané hodnoty `automl`
    + pevná funkce permutace důležitost při zadání argumentu Transformers pro získání důležité funkce
    + Přidáno `batch_size` pro napodobení `include_local=False` , když, pro streamování globálních vysvětlení v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel
    + v případě knihovny pro vysvětlení modelu jsou k disblackbox vysvětlující, kde je pro předpověď vyžadován vstup PANDAS dataframe.
    + Opravili jsme chybu, kde `explanation.expected_values` by někdy vracela float místo seznamu s plovoucí desetinnou čárkou.
  + **azureml-mlflow**
    + Zvýšení výkonu mlflow.set_experiment (experiment_name)
    + Oprava chyby při použití InteractiveLoginAuthentication pro mlflow tracking_uri
    + Vylepšete využití prostředků ve vzdálených spuštěních pomocí AzureML. mlflow.
    + Zlepšení dokumentace k balíčku AzureML-mlflow
    + Oprava chyby, kde mlflow.log_artifacts ("my_dir") by ukládala artefakty v rámci "my_dir/<artefakt-Paths>" místo "<artefakt-Paths>"
  + **azureml-opendatasets**
    + Připnout `pyarrow` `opendatasets` na staré verze (<0.14.0) z důvodu chyby nově zavedené paměti
    + Přesuňte AzureML-contrib-opendatasets do AzureML-opendatasets.
    + Povolí registraci tříd otevřených datových sad do pracovního prostoru Azure Machine Learning a bezproblémové využití AML datových sad funkcí.
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.
  + **azureml-pipeline-steps**
    + Úložiště dat DBFS se teď podporuje pro vstupy a výstupy v DatabricksStep.
    + Aktualizovaná dokumentace pro Azure Batch krok s ohledem na vstupy a výstupy.
    + V AzureBatchStep se změnila *delete_batch_job_after_finish* výchozí hodnota na *true (pravda*).
  + **azureml-telemetry**
    +  Přesuňte AzureML-contrib-opendatasets do AzureML-opendatasets.
    + Povolí registraci tříd otevřených datových sad do pracovního prostoru Azure Machine Learning a bezproblémové využití AML datových sad funkcí.
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.
  + **AzureML-vlak – automl**
    + Aktualizovala se dokumentace na get_output, aby odrážela skutečný návratový typ a poskytovala další poznámky k načítání vlastností klíče.
    + Aktualizuje závislost NimbusML na verzi 1.2.0 (aktuální nejnovější).
    + Přidat do výstupu očekávané hodnoty `automl`
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
  + **AzureML-vlak – automl**
    + Opravili jsme chybu týkající se ztráty typů sloupců po transformaci.
    + Opravili jsme chybu, aby bylo možné y_query typ objektu, který neobsahuje žádné (y) na začátku.
    + Opravili jsme problém v postupu výběru kompletu, který nemusel zbytečně rozšiřovat výsledný komplet, i když skóre zůstávala konstantní.
    + Opravili jsme problém s nastavením povolení list_models a blokování list_models v AutoMLStep.
    + Opravili jsme problém, který zabránil použití předběžného zpracování, pokud by se AutoML použil v kontextu kanálů Azure ML.
  + **azureml-opendatasets**
    + Byl přesunut příkaz AzureML-contrib-opendatasets do procesu AzureML-opendatasets.
    + Povolené otevřené třídy datové sady, které se mají registrovat do Azure Machine Learningho pracovního prostoru a využívat funkce AML DataSet bez problémů.
    + Vylepšený výkon NoaaIsdWeather obohacen v jiné verzi než SPARK.
  + **azureml-explain-model**
    + Aktualizovaná online dokumentace pro objekty interpretace.
    + Přidané `batch_size` pro napodobení `include_local=False` , když, pro streamování globálních vysvětlení v dávkách za účelem zlepšení doby provádění DecisionTreeExplainableModel pro knihovnu pro vysvětlení modelu.
    + Opravili jsme problém, kdy `explanation.expected_values` by někdy vracel místo seznamu s plovoucí desetinnou čárkou.
    + Přidání očekávaných hodnot do `automl` výstupu pro vysvětlení vysvětlujícího vysvětlení v knihovně modelů vysvětlit.
    + Pevná funkce permutace důležitost při zadání argumentu Transformers, který má za problém získat důležitost nezpracovaných funkcí
  + **azureml-core**
    + Do příkazového řádku AzureML se přidala možnost připojit úložiště DBFS.
    + Opravili jsme problém s nahráváním úložiště dat, kde je vytvořená prázdná složka, pokud je `target_path` spuštěná s `/` .
    + Je povolené porovnání dvou datových sad.
    + Odstranění modelu a obrázku nyní poskytuje další informace o načítání nadřazených objektů, které jsou na nich závislé, pokud odstranění selhalo z důvodu nadřazené závislosti.
    + Nepoužívané nastavení RunConfiguration se v auto_prepare_environment zastaralo.
  + **azureml-mlflow**
    + Vylepšené využití prostředků ve vzdálených spuštěních, které používají AzureML. mlflow.
    + Vylepšili dokumentaci k balíčku AzureML-mlflow.
    + Opravili jsme problém, kdy mlflow.log_artifacts ("my_dir") uloží artefakty v rámci "my_dir/Artifact-Paths" místo "artefakt-Paths".
  + **azureml-pipeline-core**
    + Parametr hash_paths pro všechny kroky kanálu je zastaralý a v budoucnu se odebere. Ve výchozím nastavení je obsah source_directory hash (s výjimkou souborů uvedených v `.amlignore` nebo `.gitignore` ).
    + Pokračující zlepšování modulu a ModuleStep pro podporu modulů výpočetních typů, přípravu na RunConfiguration integraci a další změny pro odemčení využití modulu specifického pro výpočet typu v kanálech.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Vylepšená dokumentace s ohledem na vstupy a výstupy.
    + AzureBatchStep: výchozí hodnota delete_batch_job_after_finish hodnotu true.
  + **azureml-train-core**
    + Řetězce jsou nyní přijímány jako cíl výpočtů pro automatizované ladění parametrů.
    + Nepoužívané nastavení RunConfiguration se v auto_prepare_environment zastaralo.
    + Zastaralé parametry `conda_dependencies_file_path` a mají `pip_requirements_file_path` přednost před `conda_dependencies_file` a v `pip_requirements_file` uvedeném pořadí.
  + **azureml-opendatasets**
    + Výrazně Zvyšte výkon NoaaIsdWeather obohacení ve verzi mimo SPARK.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Sada Azure Machine Learning SDK pro Python v 1.0.33 vydaná.

+ Azure ML Modely s hardwarovou akcelerací v [FPGA](how-to-deploy-fpga-web-service.md) je všeobecně dostupná.
  + Nyní můžete [použít balíček AzureML-akcelerace-Models](how-to-deploy-fpga-web-service.md) k těmto akcím:
    + Výuka vah podporované neuronové sítě (ResNet 50, ResNet 152, DenseNet-121, VGG-16 a SSD-VGG)
    + Využijte přenosové učení s podporovaným DNN
    + Zaregistrujte model pomocí služby Správa modelů Service a kontejnerizace model
    + Nasazení modelu do virtuálního počítače Azure s FPGA v clusteru Azure Kubernetes Service (AKS)
  + Nasazení kontejneru na zařízení [Azure Data box Edge](../databox-online/azure-stack-edge-overview.md) serveru
  + Určení skóre dat pomocí koncového bodu gRPC pomocí této [ukázky](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizované strojové učení

+ Funkce, která umožňuje dynamické přidávání :::no-loc text="featurizers"::: pro optimalizaci výkonu. Novinka :::no-loc text="featurizers"::: : vkládání pracovních verzí, váha legitimace, cílové kódování, kódování textového cíle, vzdálenost clusteru
+ Inteligentní CV pro zpracování rozdělených a platných rozdělení v rámci automatizovaného ML
+ Několik změn optimalizace paměti a vylepšení výkonu za běhu
+ Zvýšení výkonu v vysvětlení modelu
+ Převod modelu ONNX pro místní běh
+ Přidání podpory subvzorkování
+ Inteligentní zastavování, když nejsou definovaná žádná kritéria ukončení
+ Skládané komplety

+ Prognózování časové řady
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

+ **Sledování odkazů Gitu**<br/> Zákazníci si na určitou dobu vyžádali základní možnosti integrace Gitu, protože pomáhají udržovat kompletní záznam pro audit. Implementovali jsme sledování mezi hlavními entitami v Azure ML pro metadata týkající se Gitu (úložiště, potvrzení, vyčištění stavu). Tyto informace budou automaticky shromažďovány sadou SDK a rozhraní příkazového řádku.

+ **Profilace modelu & ověřovací služba**<br/> Zákazníci se často setkávají s obtížností pro správnou velikost výpočtů přidružených ke své odvozené službě. Díky naší službě profilace modelu může zákazník poskytovat ukázkové vstupy a pro určení optimální velikosti pro nasazení bude profilovat napříč 16 různými konfiguracemi CPU a paměti.

+ **Přineste si vlastní základní image pro odvození**<br/> Další běžnou stížností bylo obtížnost při přesunu z experimentu na odvozené závislosti při OPAKOVANÉm sdílení. Díky naší nové funkci pro sdílení imagí teď můžete pro odvození znovu použít základní image, závislosti a vše experimentování. To by mělo zrychlit nasazení a snížit mezeru od vnitřní k vnější smyčce.

+ **Vylepšené prostředí pro generování schématu Swagger**<br/> Naše předchozí metoda generování Swagger byla náchylná k chybám a neznemožňuje automatizaci. Máme nový online způsob generování schémat Swagger z jakékoli funkce Pythonu prostřednictvím dekoratéry. Otevřeli jsme tento kód a náš protokol pro generování schématu není spojený s platformou Azure ML.

+ **Azure ML CLI je všeobecně dostupná (GA).**<br/> Modely se teď dají nasadit pomocí jednoho příkazu CLI. Máme běžné názory zákazníků, které neobsahují žádný model ML z Jupyter poznámkového bloku. [**Referenční dokumentace k**](./reference-azure-machine-learning-cli.md) rozhraní příkazového řádku byla aktualizována.


## <a name="2019-04-22"></a>2019-04-22

Sada Azure Machine Learning SDK pro Python v 1.0.30 vydaná.

[`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?preserve-view=true&view=azure-ml-py)Byla představena přidání nové verze publikovaného kanálu při zachování stejného koncového bodu.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portál Azure Portal
  + Existující skript teď můžete znovu odeslat v existujícím vzdáleném výpočetním clusteru.
  + Nyní můžete spustit publikovaný kanál s novými parametry na kartě kanály.
  + Podrobnosti o spuštění teď podporují nový prohlížeč souborů snímků. Po odeslání konkrétního běhu můžete zobrazit snímek adresáře. Můžete si také stáhnout Poznámkový blok, který byl odeslán za účelem spuštění spuštění.
  + Nyní můžete zrušit nadřazená spuštění z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Sada SDK Azure Machine Learning pro Python v 1.0.23

+ **Nové funkce**
  + Sada Azure Machine Learning SDK teď podporuje Python 3,7.
  + Azure Machine Learning DNN odhady teď nabízí integrovanou podporu více verzí. Například `TensorFlow` Estimator nyní přijímá `framework_version` parametr a uživatelé mohou zadat verzi "1,10" nebo "1,12". Seznam verzí podporovaných vaší aktuální verzí sady SDK je vyvolán `get_supported_versions()` na požadovanou třídu rozhraní (například `TensorFlow.get_supported_versions()` ).
  Seznam verzí podporovaných nejnovější verzí sady SDK najdete v [dokumentaci k DNN Estimator](/python/api/azureml-train-core/azureml.train.dnn?preserve-view=true&view=azure-ml-py).

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Sada SDK Azure Machine Learning pro Python v 1.0.21

+ **Nové funkce**
  + Metoda *AzureML.Core.Run.create_children* umožňuje vytváření více podřízených-běhů s jedním voláním s nízkou latencí.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Sada SDK Azure Machine Learning pro Python v 1.0.18

 + **Změny**
   + Balíček AzureML-tensorboard nahrazuje. AzureML-contrib-tensorboard.
   + V této verzi můžete při vytváření amlcompute (Managed Compute Cluster) nastavit uživatelský účet. To lze provést předáním těchto vlastností v konfiguraci zřizování. Další podrobnosti najdete v [referenční dokumentaci k sadě SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

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
  + Azure Machine Learning nyní poskytuje prvotřídní podporu pro oblíbený řetěz DNN Framework. Použití [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py) třídy uživatelé můžou snadno naučit a nasazovat modely řetězení.
    + Naučte se [spouštět distribuované školení pomocí ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb) .
    + Naučte se [spouštět ladění parametrů pomocí chainer pomocí Hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) .
  + Přidání kanálů Azure Machine Learning možnost aktivovat spuštění kanálu na základě úprav úložiště dat. [Poznámkový blok plánu](https://aka.ms/pl-schedule) kanálu se aktualizuje a prezentuje tuto funkci.

+ **Opravy chyb a vylepšení**
  + Přidali jsme podporu Azure Machine Learningch kanálů pro nastavení vlastnosti source_directory_data_store na požadované úložiště dat (například úložiště objektů BLOB) na [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration?preserve-view=true&view=azure-ml-py) , které jsou k [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py)dodány. Ve výchozím nastavení se jako záložní úložiště dat používají úložiště souborů Azure, které může narazit na problémy s omezením při současném spuštění velkého počtu kroků.

### <a name="azure-portal"></a>Portál Azure Portal

+ **Nové funkce**
  + Nové možnosti editoru tabulek přetahování pro sestavy. Uživatelé mohou sloupec přetáhnout ze seznamu i do oblasti tabulky, kde se zobrazí náhled tabulky. Sloupce lze změnit jejich uspořádání.
  + Nový prohlížeč souborů protokolů
  + Odkazy na spuštění, výpočty, modely, image a nasazení z karty aktivity

## <a name="next-steps"></a>Další kroky

Přečtěte si přehled služby [Azure Machine Learning](overview-what-is-azure-ml.md).