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
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 81eabadba70a2d5334fab43157f17d24c41d97ec
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103414"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Známé problémy a řešení potíží Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo chyby, ke kterým došlo při použití Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problémy s vizuálním rozhraním

Vizuální rozhraní pro problémy se službou Machine Learning.

### <a name="long-compute-preparation-time"></a>Čas přípravy na dlouhou výpočetní výkon

Vytváření nových výpočetních prostředků nebo EVOKE, které opouští výpočetní výkon, může trvat několik minut nebo i delší dobu. Tým pracuje na optimalizaci.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Nelze spustit experiment obsahující pouze datovou sadu. 

Možná budete chtít spustit experiment pouze s datovou sadou, která bude vizualizovat datovou sadu. Není však povoleno spouštět experiment pouze v dnešní době. Aktivně opravujeme tento problém.
 
Před opravou můžete datovou sadu připojit k jakémukoli modulu transformace dat (výběr sloupců v datové sadě, upravit metadata, rozdělit data atd.) a spustit experiment. Pak můžete vizualizovat datovou sadu. 

Následující obrázek ukazuje, jak ![: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problémy při instalaci sady SDK

**Chybová zpráva: Nelze odinstalovat ' PyYAML '**

Azure Machine Learning SDK pro Python: PyYAML je distutils nainstalovaný projekt. Proto nemůžeme přesně určit, které soubory do ní patří, pokud dojde k částečné odinstalaci. Pokud chcete pokračovat v instalaci sady SDK při tato chyba se ignoruje, použijte:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Chybová zpráva: `ERROR: No matching distribution found for azureml-dataprep-native`**

Distribuce Anaconda Python 3.7.4 obsahuje chybu, která přerušuje instalaci aplikace AzureML-SDK. Tento problém je popsaný v tomto [problému GitHubu](https://github.com/ContinuumIO/anaconda-issues/issues/11195) . to se dá vyřešit vytvořením nového prostředí conda pomocí tohoto příkazu:
```bash
conda create -n <env-name> python=3.7.3
```
Díky tomu vytvoří prostředí conda s využitím Pythonu 3.7.3, ve kterém není problém instalace přítomen v 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problémy s vytvářením, Azure Machine Learning Compute

Je vzácné pravděpodobné, že někteří uživatelé, kteří si vytvořili jejich pracovního prostoru Azure Machine Learning z portálu Azure portal před verze GA nemusí být možné vytvořit Azure Machine Learning Compute v daném pracovním prostoru. Můžete zvýšit žádost o podporu na službu nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo pomocí sady SDK pro odblokování sami okamžitě.

## <a name="image-building-failure"></a>Chyba vytváření bitové kopie

Obrázek po nasazení webové služby vytvářet selhání. Alternativním řešením je přidat "pynacl == 1.2.1" jako pip závislosti systému Conda v souboru konfigurace image.

## <a name="deployment-failure"></a>Selhání nasazení

Pokud si `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`myslíte, změňte SKU pro virtuální počítače používané ve vašem nasazení na jednu, která má více paměti.

## <a name="fpgas"></a>FPGA

Nebude moct nasazovat modely na FPGA, dokud si vyžádáte a byla schválena pro FPGA kvótu. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvóty: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizované strojové učení

Služba tensor Flow automatizovaného strojového učení v současné době nepodporuje tensor Flow verze 1,13. Instalace této verze způsobí, že se závislosti balíčku přestanou pracovat. Pracujeme na řešení tohoto problému v budoucí verzi. 

### <a name="experiment-charts"></a>Grafy experimentů

Binární klasifikační grafy (přesnost-odvolání, ROC, křivka získání atd.) zobrazené v automatizovaných iteracích experimentu se v uživatelském rozhraní nevykreslují správně, od 4/12. V grafu jsou v současné době zobrazeny inverzní výsledky, kde je lepší provádět modely s nižšími výsledky. Řešení je v šetření.

## <a name="databricks"></a>Databricks

Problémy s Databricks a Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Chyba při instalaci balíčků

Instalace sady Azure Machine Learning SDK se v Azure Databricks při instalaci dalších balíčků nezdařila. Některé balíčky, jako například `psutil`, může způsobit konflikty. Aby nedocházelo k chybám při instalaci, nainstalujte balíčky zmrazením verze knihovny. Tento problém se vztahuje k datacihlům a nikoli k sadě Azure Machine Learning SDK. Tento problém se může vyskytnout i u jiných knihoven. Příklad:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Případně můžete použít skripty init, pokud máte potíže s instalací v knihovně Python. Tento přístup není oficiálně podporován. Další informace najdete v tématu [skripty init v oboru clusteru](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Zrušení automatizovaného spuštění strojového učení

Když v Azure Databricks používáte automatizované funkce machine learningu, zrušíte spuštění a spustíte nový experiment, restartujete cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterací pro automatizované strojové učení

V případě automatizovaného nastavení strojového učení, pokud máte více než 10 iterací `show_output` , `False` nastavte na hodnotu při odeslání běhu.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget pro sadu Azure Machine Learning SDK/automatizované Machine Learning

Pomůcka Azure Machine Learning SDK není v poznámkovém bloku datacihly podporovaná, protože poznámkové bloky nemůžou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce s Azure Databricksm poznámkového bloku:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Chyba importu: Žádný modul s názvem PANDAS. Core. indexs

Pokud se tato chyba zobrazí při použití automatizovaného strojového učení:

1. Spuštěním tohoto příkazu nainstalujete do clusteru Azure Databricks dva balíčky: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odpojte a znovu připojte cluster ke svému poznámkovému bloku. 

Pokud tyto kroky problém nevyřeší, zkuste restartovat cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Pokud se při čtení `FailToSendFeather` dat v Azure Databricks clusteru zobrazí chyba, přečtěte si následující řešení:

* Upgradujte `azureml-sdk[automl_databricks]` balíček na nejnovější verzi.
* Přidejte `azure-dataprep` 1.1.8 verze nebo vyšší.
* Přidejte `pyarrow` verzi 0,11 nebo vyšší.

## <a name="azure-portal"></a>portál Azure

Pokud přejdete přímo na váš pracovní prostor z sdílet odkaz ze sady SDK nebo na portálu zobrazit, nebudete moct zobrazit stránka s přehledem normální s informace o předplatném v rozšíření. Nebudete také moci přepnout do jiného pracovního prostoru. Pokud potřebujete zobrazit jiný pracovní prostor, alternativní řešení je přejít přímo na [Azure Portal](https://portal.azure.com) a vyhledat název pracovního prostoru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Pokud chcete zobrazit některé protokoly, navštivte [Azure Portal](https://portal.azure.com) a přejděte do svého pracovního prostoru a vyberte **pracovní prostor > Experiment > Run > log**.  Tyto informace můžete najít také v části **experimenty** na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning v průběhu školení protokolovat informace z nejrůznějších zdrojů, jako je například AutoML nebo kontejner Docker, který spouští školicí úlohu. Mnohé z těchto protokolů nejsou dokumentovány. Pokud narazíte na problémy a kontaktujte podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

## <a name="activity-logs"></a>Protokoly aktivit

Některé akce v pracovním prostoru Azure Machine Learning neprotokolují informace do __protokolu aktivit__. Například spuštění školení nebo registrace modelu.

Některé z těchto akcí se zobrazí v oblasti __aktivity__ pracovního prostoru, ale nenaznačují, kdo aktivitu inicioval.

## <a name="resource-quotas"></a>Kvóty prostředků

Další informace o [kvóty prostředků](how-to-manage-quotas.md) můžete setkat při práci se službou Azure Machine Learning.

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provádíte operaci správy na výpočetním cíli ze vzdálené úlohy, zobrazí se jedna z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například se zobrazí chyba, pokud se pokusíte vytvořit nebo připojit výpočetní cíl z kanálu ML, který je odeslán pro vzdálené spuštění.

## <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí chybová `Unable to upload project files to working directory in AzureFile because the storage is overloaded`zpráva, použijte následující alternativní řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je třeba přenos dat, doporučuje se použít objekty blob, aby bylo možné používat pro odeslání spuštění sdílení souborů. Úlohy můžete rozdělit také mezi dva různé pracovní prostory.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Služby WebServices ve službě Azure Kubernetes – chyby 

Mnoho selhání webové služby ve službě Azure Kubernetes se dá ladit připojením ke clusteru pomocí `kubectl`. Cluster služby Azure Kubernetes `kubeconfig.json` můžete získat spuštěním

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Aktualizace komponent Azure Machine Learning v clusteru AKS

Aktualizace komponent Azure Machine Learning nainstalovaných v clusteru služby Azure Kubernetes se musí použít ručně. Tyto clustery můžete použít tak, že cluster odpojíte z pracovního prostoru Azure Machine Learning a pak cluster znovu připojíte k pracovnímu prostoru. Pokud je v clusteru povolený protokol SSL, budete muset při opětovném připojení clusteru dodat certifikát SSL a privátní klíč. 

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

Pokud už certifikát SSL a soukromý klíč nepoužíváte nebo certifikát generovaný Azure Machine Learning, můžete načíst soubory před odpojením clusteru, a to tak, že se připojíte ke clusteru pomocí `kubectl` a načtete tajný klíč. `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes ukládá tajné klíče ve formátu kódování Base-64. Před poskytnutím těchto tajných kódů bude nutné `cert.pem` základní-64 dekódovat `key.pem`. `attach_config.enable_ssl` 