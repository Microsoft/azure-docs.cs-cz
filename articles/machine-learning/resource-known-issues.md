---
title: Známé problémy & řešení potíží
titleSuffix: Azure Machine Learning
description: Seznam známých problémů, řešení a řešení potíží pro Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 93015da810f163a48529704e69e1747ac1aec401
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889398"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Známé problémy a řešení potíží Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo chyby, ke kterým může dojít při použití Azure Machine Learning.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

V některých případech může být užitečné, pokud při dotazování na nápovědu poskytnete diagnostické informace. Chcete-li zobrazit některé protokoly: 
1. Navštivte [Azure Machine Learning Studio](https://ml.azure.com). 
1. Na levé straně vyberte **experiment** . 
1. Vyberte experiment.
1. Vyberte běh.
1. V horní části vyberte možnost **výstupy + protokoly**.

> [!NOTE]
> Azure Machine Learning v průběhu školení protokolovat informace z nejrůznějších zdrojů, jako je například AutoML nebo kontejner Docker, který spouští školicí úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.


## <a name="resource-quotas"></a>Kvóty prostředků

Přečtěte si o [kvótách prostředků](how-to-manage-quotas.md) , se kterými se můžete setkat při práci s Azure Machine Learning.

## <a name="installation-and-import"></a>Instalace a import
                           
* **Instalace PIP: u závislostí není zaručeno konzistence při instalaci s jedním řádkem**: 

   Toto je známé omezení PIP, protože při instalaci jako jediného řádku nemá funkční překladač závislostí. První jedinečná závislost je pouze ta, na kterou se odkazuje. 

   V následujícím kódu `azure-ml-datadrift` `azureml-train-automl` se obě nainstalují pomocí jediného příkazu PIP. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   V tomto příkladě řekněme `azure-ml-datadrift` , že vyžaduje verzi > 1,0 a `azureml-train-automl` vyžaduje verzi < 1,2. Pokud `azure-ml-datadrift` je nejnovější verze 1,3, pak se oba balíčky upgradují na 1,3 bez ohledu na `azureml-train-automl` požadavky balíčku na starší verzi. 

   Chcete-li zajistit, aby byly pro balíčky nainstalovány příslušné verze, nainstalujte pomocí více řádků, jako v následujícím kódu. Objednávka tady není problém, protože PIP se v rámci dalšího řádku explicitně downgraduje. A proto se aplikují příslušné závislosti verzí.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Balíček s vysvětlením, který se guarateed, aby se nainstaloval při instalaci nástroje AzureML-vlak-automl-Client:** 
   
   Při spuštění vzdáleného spuštění automl s vysvětlením modelu se zobrazí chybová zpráva s oznámením, že je potřeba nainstalovat balíček AzureML-vysvětlit-model pro vysvětlení modelů. Jedná se o známý problém a jako alternativní řešení proveďte jeden z následujících kroků:
  
  1. Nainstalujte si příkaz AzureML-vysvětlit-model místně.
   ```
      pip install azureml-explain-model
   ```
  2. Zakažte funkci vysvětlení zcela předáním model_explainability = false v konfiguraci automl.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Chyby Panda: obvykle se zobrazují během experimentu AutoML:**
   
   Když ručně nakonfigurujete environmnet pomocí PIP, všimnete si chyb atributů (obzvláště z PANDAS), protože se instalují nepodporované verze balíčku. Abyste předešli takovým chybám, [nainstalujte prosím sadu AutoML SDK pomocí příkazu automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Otevřete příkazový řádek Anaconda a naklonujte úložiště GitHub pro sadu ukázkových poznámkových bloků.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. Vložte disk CD do složky s postupy použití-AzureML/automatizovaného strojového učení, kde byly extrahovány ukázkové poznámkové bloky a poté spusťte příkaz:
    
    ```bash
    automl_setup
    ```
  
* **Chybová zpráva: Nejde odinstalovat ' PyYAML '.**

    Azure Machine Learning SDK pro Python: PyYAML je `distutils` nainstalovaný projekt. Proto nemůžeme přesně určit, které soubory do ní patří, pokud dojde k částečné odinstalaci. Pokud chcete pokračovat v instalaci sady SDK a tuto chybu ignorovat, použijte:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Při instalaci balíčků došlo k chybě datacihly.**

    Instalace sady Azure Machine Learning SDK se v Azure Databricks při instalaci dalších balíčků nezdařila. Některé balíčky, například `psutil`, můžou způsobit konflikty. Aby nedocházelo k chybám při instalaci, nainstalujte balíčky zmrazením verze knihovny. Tento problém se vztahuje k datacihlům a nikoli k sadě Azure Machine Learning SDK. Tento problém se může vyskytnout i u jiných knihoven. Příklad:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Případně můžete použít skripty init, pokud máte potíže s instalací v knihovně Python. Tento přístup není oficiálně podporován. Další informace najdete v tématu [skripty init v oboru clusteru](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Chyba importu datacihly: název ' Timedelta ' nelze importovat z ' PANDAS. _libs. tslibs '**: Pokud se vám tato chyba zobrazí při použití automatizovaného strojového učení, spusťte na poznámkovém bloku tyto dva řádky:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Chyba importu datacihly: žádný modul s názvem PANDAS. Core. Indexes**: Pokud se vám při použití automatizovaného strojového učení zobrazí tato chyba:

    1. Spuštěním tohoto příkazu nainstalujete do clusteru Azure Databricks dva balíčky:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Odpojte a znovu připojte cluster ke svému poznámkovému bloku.
    
    Pokud tyto kroky problém nevyřeší, zkuste restartovat cluster.

* **Datacihly FailToSendFeather**: Pokud se při čtení `FailToSendFeather` dat v Azure Databricks clusteru zobrazí chyba, přečtěte si následující řešení:
    
    * Upgradujte `azureml-sdk[automl]` balíček na nejnovější verzi.
    * Přidejte `azureml-dataprep` 1.1.8 verze nebo vyšší.
    * Přidejte `pyarrow` verzi 0,11 nebo vyšší.
    
## <a name="create-and-manage-workspaces"></a>Vytváření a Správa pracovních prostorů

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

* **Azure Portal**: Pokud přejdete přímo k pracovnímu prostoru z odkazu na sdílení ze sady SDK nebo portálu, nebudete moci zobrazit normální stránku **přehledu** s informacemi o předplatném v rozšíření. Nebudete také moci přepnout do jiného pracovního prostoru. Pokud potřebujete zobrazit jiný pracovní prostor, přejít přímo na [Azure Machine Learning Studio](https://ml.azure.com) a vyhledejte název pracovního prostoru.

## <a name="set-up-your-environment"></a>Nastavení prostředí

* **Problémy při vytváření AmlCompute**: je pravděpodobné, že někteří uživatelé, kteří vytvořili svůj Azure Machine Learning pracovní prostor z Azure Portal před vydáním GA, nemusí být schopni vytvořit AmlCompute v tomto pracovním prostoru. Můžete buď vyvolat žádost o podporu na službu, nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo sadu SDK pro okamžité odblokování.

## <a name="work-with-data"></a>Práce s daty

### <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí chybová `Unable to upload project files to working directory in AzureFile because the storage is overloaded`zpráva, použijte následující alternativní řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je třeba přenos dat, doporučuje se použít objekty blob, aby bylo možné používat pro odeslání spuštění sdílení souborů. Úlohy můžete rozdělit také mezi dva různé pracovní prostory.

### <a name="passing-data-as-input"></a>Předávání dat jako vstupů

*  **TypeError: FileNotFound: žádný takový soubor nebo adresář**: k této chybě dochází, pokud se v souboru nenachází cesta, kterou zadáte. Je nutné zajistit, aby se způsob, jakým na soubor odkazujete, shodoval s tím, kam jste připojili datovou sadu na výpočetním cíli. Pro zajištění deterministického stavu doporučujeme při připojování datové sady k cíli výpočtů použít abstraktní cestu. Například v následujícím kódu připojíme datovou sadu do kořenového adresáře systému souborů cíle služby COMPUTE `/tmp`. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Pokud nezadáte úvodní lomítko, "/", budete muset zadat předponu pracovního adresáře, např. `/mnt/batch/.../tmp/dataset` na výpočetním cíli, abyste označili, kam chcete datovou sadu připojit.

### <a name="data-labeling-projects"></a>Projekty označování dat

|Problém  |Řešení  |
|---------|---------|
|Můžou se použít jenom datové sady vytvořené pro úložiště dat objektu BLOB.     |  Toto je známé omezení aktuální verze.       |
|Po vytvoření se v projektu zobrazí "inicializace" po dlouhou dobu.     | Aktualizujte stránku ručně. Inicializace by měla pokračovat zhruba o 20 databody za sekundu. Nedostatečná možnost AutoRefresh je známý problém.         |
|Při kontrole imagí se nezobrazují nově označené obrázky.     |   Chcete-li načíst všechny označené obrázky, klikněte na tlačítko **první** . **První** tlačítko se vrátí zpět na začátek seznamu, ale načte všechna označená data.      |
|Když stisknete klávesu ESC, zatímco při rozpoznávání objektu se vytvoří popisek s nulovou velikostí v levém horním rohu. Odesílání popisků v tomto stavu se nezdařilo.     |   Odstraňte popisek kliknutím na křížek vedle něj.  |

## <a name="azure-machine-learning-designer"></a>Návrhář Azure Machine Learning

Známé problémy:

* **Dlouhodobá doba přípravy výpočtů**: může to trvat několik minut nebo i déle, když se poprvé připojíte k cíli výpočtů nebo ho vytvoříte. 

## <a name="train-models"></a>Trénování modelů

* **ModuleErrors (žádný modul s názvem)**: Pokud při odesílání experimentů ve službě Azure ml používáte systém ModuleErrors, znamená to, že školicí skript očekává, že je balíček nainstalovaný, ale nepřidá se. Až zadáte název balíčku, Azure ML nainstaluje balíček do prostředí, které se používá pro váš školicí běh. 

    Pokud používáte [odhady](concept-azure-machine-learning-architecture.md#estimators) k odesílání experimentů, můžete zadat název balíčku prostřednictvím `pip_packages` nebo `conda_packages` parametr v Estimator na základě toho, ze kterého zdroje chcete balíček nainstalovat. Můžete také zadat soubor YML se všemi vašimi závislostmi pomocí `conda_dependencies_file`nebo vypsat všechny požadavky PIP v souboru txt pomocí `pip_requirements_file` parametru. Pokud máte vlastní objekt prostředí Azure ML, který chcete přepsat výchozí image, kterou používá Estimator, můžete toto prostředí zadat prostřednictvím `environment` parametru konstruktoru Estimator.

    Azure ML také poskytuje odhady specificky pro rozhraní pro Tensorflow, PyTorch, chainer a skriptu sklearn. Pomocí těchto odhady se zajistí, že se závislosti Core Frameworku nainstalují vaším jménem do prostředí používaného pro školení. Máte možnost zadat další závislosti, jak je popsáno výše. 
 
    Azure ML zachovává image Docker a jejich obsah se může zobrazit v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers).
    Závislosti specifické pro rozhraní jsou uvedeny v dokumentaci k příslušnému rozhraní – [chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [skriptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Pokud si myslíte, že konkrétní balíček je dostatečně společný, aby ho bylo možné přidat do spravovaných imagí a prostředí Azure ML, vyřešte v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers)problém GitHubu. 
 
* **NameError (název není definován), AttributeError (objekt nemá žádný atribut)**: Tato výjimka by měla pocházet z vašich školicích skriptů. Můžete si prohlédnout soubory protokolu z Azure Portal a získat další informace o konkrétním názvu, který není definován nebo chyba atributu. V sadě SDK se můžete `run.get_details()` podívat na chybovou zprávu. Zobrazí se také seznam všech souborů protokolu generovaných pro váš běh. Ujistěte se prosím, že se podíváte na školicí skript a opravte chybu před opětovným odesláním běhu. 

* **Horovod byla vypnuta**: ve většině případů, pokud se zobrazí zpráva "AbortedError: Horovod byla vypnuta" Tato výjimka znamená, že došlo k základní výjimce v jednom z procesů, které způsobily vypnutí Horovod. Každé pořadí v úloze MPI získá vlastní vyhrazený soubor protokolu v Azure ML. Tyto protokoly jsou pojmenovány `70_driver_logs`. V případě distribuovaného školení jsou názvy protokolů s `_rank` příponou a usnadňují odlišení protokolů. Pokud chcete najít přesnou chybu, která způsobila vypnutí Horovod, Projděte všechny soubory protokolů a hledejte `Traceback` na konci driver_log souborů. Jeden z těchto souborů vám poskytne vlastní podkladovou výjimku. 

* **Spuštění nebo experimentování při odstraňování**: experimenty se dají archivovat pomocí metody [experiment. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) nebo na kartě experiment v Azure Machine Learning klientovi studia pomocí tlačítka "archivní experiment". Tato akce skryje experiment ze seznamu dotazy a zobrazení, ale neodstraní ho.

    Trvalé odstranění individuálních experimentů nebo spuštění není aktuálně podporováno. Další informace o odstraňování prostředků pracovního prostoru najdete v tématu [Export nebo odstranění dat pracovního prostoru služby Machine Learning](how-to-export-delete-data.md).

* **Dokument metriky je příliš velký**: Azure Machine Learning má interní omezení velikosti objektů metriky, které je možné v jednom z školicích běhů přihlásit. Pokud narazíte na chybu "dokument metriky je moc velký" při protokolování metriky s hodnotou seznamu, zkuste seznam rozdělit na menší bloky dat, například:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Azure ML interně zřetězí bloky se stejným názvem metriky do souvislého seznamu.

## <a name="automated-machine-learning"></a>Automatizované strojové učení

* **Tok tensor**: automatizované Machine Learning v současné době nepodporuje tensor Flow verze 1,13. Instalace této verze způsobí, že se závislosti balíčku přestanou pracovat. Pracujeme na řešení tohoto problému v budoucí verzi.

* **Grafy experimentů**: binární klasifikační grafy (přesnost-odvolání, ROC, křivka získání atd.) zobrazené v automatizovaných iteracích experimentu se nevykreslují správně v uživatelském rozhraní od 4/12. V grafu jsou v současné době zobrazeny inverzní výsledky, kde je lepší provádět modely s nižšími výsledky. Řešení je v šetření.

* **Datacihly ruší automatizované spuštění strojového učení**: když v Azure Databricks používáte automatizované funkce machine learningu, ke zrušení spuštění a spuštění nového experimentu restartujte cluster Azure Databricks.

* **Datacihly >10 iterací pro automatizované strojové učení**: v případě automatizovaného nastavení strojového učení, pokud máte více než 10 iterací `False` , nastavte na hodnotu `show_output` při odeslání běhu.

* **Widget datacihly pro sadu Azure Machine Learning SDK a automatizované Machine Learning**: pomůcka SDK pro Azure Machine Learning není v poznámkovém bloku datacihly podporovaná, protože poznámkové bloky nemůžou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce s Azure Databricksm poznámkového bloku:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Nasazení a obsluha modelů

Proveďte tyto akce při následujících chybách:

|Chyba  | Řešení  |
|---------|---------|
|Chyba při sestavování obrázku při nasazení webové služby     |  Přidat "pynacl = = 1.2.1" jako závislost PIP k souboru conda pro konfiguraci bitové kopie       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Změňte SKU pro virtuální počítače používané ve vašem nasazení na jednu, která má více paměti. |
|Selhání FPGA     |  Modely v FPGA nebudete moct nasadit, dokud si nebudete požádáni a neschválili kvótu FPGA. Pokud chcete požádat o přístup, vyplňte formulář žádosti o kvótu:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizace komponent Azure Machine Learning v clusteru AKS

Aktualizace komponent Azure Machine Learning nainstalovaných v clusteru služby Azure Kubernetes se musí použít ručně. 

Tyto aktualizace můžete použít tak, že cluster odpojíte z pracovního prostoru Azure Machine Learning a pak cluster znovu připojíte k pracovnímu prostoru. Pokud je v clusteru povolený protokol TLS, budete muset při opětovném připojení clusteru dodat certifikát TLS/SSL a privátní klíč. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Pokud již nemáte certifikát TLS/SSL a privátní klíč nebo používáte certifikát vygenerovaný Azure Machine Learning, můžete načíst soubory před odpojením clusteru, a to tak, že se připojíte ke clusteru pomocí `kubectl` a načtete tajný klíč. `azuremlfessl`

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes ukládá tajné klíče ve formátu kódování Base-64. Před poskytnutím těchto tajných kódů bude nutné `cert.pem` základní `key.pem` -64 dekódovat `attach_config.enable_ssl`. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Služby WebServices ve službě Azure Kubernetes – chyby

Mnoho selhání webové služby ve službě Azure Kubernetes se dá ladit připojením ke clusteru pomocí `kubectl`. Cluster služby Azure Kubernetes `kubeconfig.json` můžete získat spuštěním

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provádíte operaci správy na výpočetním cíli ze vzdálené úlohy, zobrazí se jedna z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například se zobrazí chyba, pokud se pokusíte vytvořit nebo připojit výpočetní cíl z kanálu ML, který je odeslán pro vzdálené spuštění.
