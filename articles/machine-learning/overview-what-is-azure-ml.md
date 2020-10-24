---
title: Co je Azure Machine Learning
description: Přehled Azure Machine Learning – integrované, komplexní řešení datové vědy pro profesionální odborníky na data pro vývoj, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: ad025d2278d6c9ab360ddbdc250e8db4b4eec665
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495754"
---
# <a name="what-is-azure-machine-learning"></a>Co je Azure Machine Learning?

V tomto článku se dozvíte o Azure Machine Learning cloudovém prostředí, které můžete využít ke školení, nasazení, automatizaci, správě a sledování modelů ML. 

Azure Machine Learning můžete použít pro libovolný druh strojového učení, od klasického ml až po hloubkové učení, pod dohledem a bez dohledu učení. Bez ohledu na to, jestli chcete psát kód v Pythonu nebo R pomocí sady SDK nebo pracovat s možnostmi žádného kódu a nízkého kódu v [studiu](#build-ml-models-in-the-studio), můžete v pracovní prostor Azure Machine Learning sestavovat, vyvíjet a sledovat modely strojového učení a hloubkového učení. 

Spusťte školení na místním počítači a pak nahorizontální navýšení kapacity do cloudu. 

Služba také spolupracuje s oblíbenými nástroji pro hloubkové učení a posílením open source nástrojů, jako jsou PyTorch, TensorFlow, scikit-Learning a Ray RLlib. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Bezplatná zkušební verze**  Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes Získáte kredity, které můžete využít na služby Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.


## <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Pomocí strojového učení se počítače učí bez explicitního programování.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Například při nakupování online pomůže strojové učení doporučit další produkty, které byste mohli chtít v závislosti na zakoupení. Nebo při protažení platební karty terminálem strojové učení porovná transakci s databází transakcí a pomáhá detekovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="machine-learning-tools-to-fit-each-task"></a>Nástroje pro strojové učení, které se vejdou na jednotlivé úlohy 

Azure Machine Learning poskytuje vývojářům a odborníkům přes data, kteří potřebují pro své pracovní postupy pro strojové učení, včetně těchto nástrojů:
+ [Návrhář Azure Machine Learning](tutorial-designer-automobile-price-train-score.md): přetažení modulů pro sestavování experimentů a následné nasazení kanálů.

+ Jupyter poznámkové bloky: pomocí našich [ukázkových poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks) nebo vytvořte vlastní poznámkové bloky a využijte naši <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sadu SDK pro ukázky Pythonu</a> pro váš strojové učení. 

+ Skripty nebo notebooky r, ve kterých používáte <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">sadu SDK pro R</a> k psaní vlastního kódu nebo použití modulů R v návrháři.

+ + [Mnohé modely řešení](https://aka.ms/many-models) (Preview) jsou sestavené na Azure Machine Learning a umožňují výuku, provozování a správu stovek nebo dokonce tisíců modelů strojového učení.

+ [Rozšíření editoru Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [Machine Learning CLI](reference-azure-machine-learning-cli.md)

+ Open Source architektury, jako jsou PyTorch, TensorFlow a scikit – Naučte se a mnoho dalšího

+ [Posílení učení](how-to-use-reinforcement-learning.md) s využitím Ray RLlib

Můžete dokonce použít [MLflow ke sledování metrik a nasazení modelů](how-to-use-mlflow.md) nebo Kubeflow k [sestavení koncových kanálů pracovního postupu](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Vytváření modelů ML v Pythonu nebo R

Spusťte školení na místním počítači pomocí Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> nebo <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Pak můžete horizontální navýšení kapacity na Cloud. 

Díky mnoha dostupným [výpočetním cílům](how-to-create-attach-compute-studio.md), jako je Azure Machine Learning compute a [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), a s [pokročilými službami ladění pomocí parametrů](how-to-tune-hyperparameters.md)můžete vytvářet lepší modely rychleji pomocí výkonného cloudu.

Můžete také [automatizovat školicí a optimalizační model](tutorial-auto-train-models.md) pomocí sady SDK.

## <a name="build-ml-models-in-the-studio"></a>Vytváření modelů ML v studiu

[Azure Machine Learning Studio](https://studio.azureml.net) je webový portál v Azure Machine Learning pro možnosti s nízkým kódem a bez kódu pro školení modelů, nasazení a správu prostředků. Sada Studio se integruje se sadou Azure Machine Learning SDK, aby se zajistilo bezproblémové prostředí. Další informace najdete v tématu [co je Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

+ **Návrhář služby Azure Machine Learning**

  Použijte [Návrháře](concept-designer.md) ke školení a nasazení modelů strojového učení bez psaní kódu. Vyzkoušejte si [kurz pro návrháře](tutorial-designer-automobile-price-train-score.md) a začněte. 

  ![Animovaný obrázek GIF rozhraní přetažení návrháře Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Sledovat experimenty**

  Naučte se [sledovat a vizualizovat experimenty v oblasti datových věd](tutorial-first-experiment-automated-ml.md) v studiu. 

    ![Podrobnosti o spuštění v Azure Machine Learning Studiu](media/how-to-track-experiments/experimentation-tab.gif)


+ **A mnohem víc...**

  Navštivte Azure Machine Learning studia na adrese [ml.Azure.com](https://studio.azureml.net). 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: nasazení správy životního cyklu &
Pokud máte správný model, můžete ho snadno použít ve webové službě, v zařízení IoT nebo v Power BI. Další informace najdete v článku věnovaném [nasazení a umístění](how-to-deploy-and-where.md).

Pak můžete nasadit nasazené modely pomocí [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true), [Azure Machine Learning Studio](https://ml.azure.com)nebo rozhraní příkazového [řádku Machine Learning](reference-azure-machine-learning-cli.md).

Tyto modely lze spotřebovat a vracet předpovědi v [reálném čase](how-to-consume-web-service.md) nebo [asynchronně](how-to-use-parallel-run-step.md) u velkých objemů dat.

A díky pokročilým [kanálům strojového učení](concept-ml-pipelines.md)můžete v rámci přípravy dat, školení a vyhodnocení modelu spolupracovat na všech krocích prostřednictvím nasazení. Kanály umožňují:

* Automatizace kompletního procesu strojového učení v cloudu
* Znovu použít komponenty a v případě potřeby znovu spustit kroky
* Použití různých výpočetních prostředků v každém kroku
* Spustit úlohy dávkového vyhodnocování

Pokud chcete používat skripty pro automatizaci pracovního postupu machine learningu, rozhraní příkazového řádku [Machine Learning](reference-azure-machine-learning-cli.md) poskytuje nástroje příkazového řádku, které provádějí běžné úlohy, jako je například odeslání školicího běhu nebo nasazení modelu.

Pokud chcete začít používat Azure Machine Learning, přečtěte si [Další kroky](#next-steps).

## <a name="integration-with-other-services"></a>Integrace s dalšími službami

Azure Machine Learning funguje s ostatními službami na platformě Azure a integruje se s open source nástroji, jako je třeba Git a MLFlow.

+ Výpočetní cíle, jako je __Služba Azure Kubernetes__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__a __Azure HDInsight__. Další informace o výpočetních cílech najdete v tématu [co jsou výpočetní cíle?](concept-compute-target.md).
+ __Azure Event Grid__. Další informace najdete v tématu věnovaném [využívání Azure Machine Learningch událostí](concept-event-grid-integration.md).
+ __Azure monitor__. Další informace najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).
+ Úložiště dat, jako jsou __účty Azure Storage__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__a __Azure Open DataSet__. Další informace najdete v tématech [přístup k datům ve službě Azure Storage Services](how-to-access-data.md) a [vytváření datových sad pomocí Azure Open DataSets](how-to-create-register-datasets.md).
+ __Virtuální sítě Azure__. Další informace najdete v tématu [Přehled izolace a ochrany osobních údajů ve virtuální síti](how-to-network-security-overview.md).
+ __Azure Pipelines__. Další informace najdete v tématu [výuka a nasazení modelů strojového učení](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Protokoly úložiště Git__ Další informace najdete v tématu [integrace Gitu](concept-train-model-git-integration.md).
+ __MLFlow__. Další informace najdete v tématu [MLflow ke sledování metrik a nasazení modelů](how-to-use-mlflow.md) . 
+ __Kubeflow__. Další informace najdete v tématu [vytváření koncových kanálů pracovního postupu](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Zabezpečená komunikace

Váš Azure Storage účet, výpočetní cíle a další prostředky se dají v rámci virtuální sítě bezpečně použít k učení modelů a provádění odvození. Další informace najdete v tématu [Přehled izolace a ochrany osobních údajů ve virtuální síti](how-to-network-security-overview.md).

## <a name="next-steps"></a>Další kroky

- Vytvořte svůj první experiment s upřednostňovanou metodou:
- + [Začněte ve vlastním vývojovém prostředí](tutorial-1st-experiment-sdk-setup-local.md)
  + [Použití poznámkových bloků Jupyter na výpočetní instanci ke školení & nasazení modelů ML](tutorial-1st-experiment-sdk-setup.md)
  + [Použití R Markdown ke školení & nasazení modelů ML](tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení ke studiu & nasazení modelů ML](tutorial-first-experiment-automated-ml.md) 
  + [Použití možností přetažení & designeru pro výuku & nasazení](tutorial-designer-automobile-price-train-score.md) 
  + [Použití rozhraní příkazového učení pro strojové učení ke školení a nasazení modelu](tutorial-train-deploy-model-cli.md)

- Seznamte se s [kanály strojového učení](concept-ml-pipelines.md), které umožňují vytvářet, optimalizovat a spravovat scénáře strojového učení.

- Přečtěte si podrobnou [architekturu Azure Machine Learning a koncepty](concept-azure-machine-learning-architecture.md) .
