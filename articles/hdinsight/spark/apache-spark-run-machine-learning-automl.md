---
title: Spouštění Azure Machine Learning úloh v Apache Spark ve službě HDInsight
description: Naučte se spouštět úlohy Azure Machine Learning pomocí automatizovaného strojového učení (AutoML) v Apache Spark ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929729"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Spouštění Azure Machine Learning úloh pomocí automatizovaného strojového učení na Apache Spark ve službě HDInsight

Azure Machine Learning zjednodušuje a zrychluje sestavování, školení a nasazení modelů strojového učení. V rámci automatizovaného strojového učení (AutoML) začnete s školicími daty, která mají definovanou cílovou funkci, a pak iterovat kombinací algoritmů a výběrů funkcí tak, aby automaticky vybrali nejlepší model pro vaše data na základě výsledků školení. HDInsight umožňuje zákazníkům zřídit clustery se stovkami uzlů. AutoML běžící na Sparku v clusteru HDInsight umožňuje uživatelům používat výpočetní kapacitu na těchto uzlech ke spouštění školicích úloh v rámci škálování a spouštění více školicích úloh paralelně. Díky tomu můžou uživatelé spouštět AutoML experimenty při sdílení výpočtů s ostatními úlohami s velkými objemy dat.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalace Azure Machine Learning v clusteru HDInsight

Obecné kurzy k automatizovanému strojovém učení najdete v tématu [kurz: Použití automatizovaného strojového učení k sestavení regresního modelu](../../machine-learning/tutorial-auto-train-models.md).
Všechny nové clustery HDInsight-Spark jsou v sadě SDK sady AzureML-AutoML nainstalovány předem.

> [!Note]
> Balíčky Azure Machine Learning se instalují do prostředí conda pro python3. Nainstalované Jupyter Notebook by měly být spuštěny pomocí jádra PySpark3.

Pomocí poznámkových bloků Zeppelin můžete také použít AutoML.

> [!Note]
> Zeppelin má [známý problém](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) , kdy PySpark3 nevybere správnou verzi Pythonu. Použijte prosím zdokumentované řešení.

## <a name="authentication-for-workspace"></a>Ověřování pro pracovní prostor

Vytváření pracovních prostorů a odesílání experimentů vyžadují ověřovací token. Tento token se dá vygenerovat pomocí [aplikace Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Pokud služba Multi-Factor Authentication není pro tento účet povolená, můžete k vygenerování požadovaného ověřovacího tokenu použít taky [uživatele Azure AD](/azure/python/python-sdk-azure-authenticate) .  

Následující fragment kódu vytvoří ověřovací token pomocí **aplikace Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Následující fragment kódu vytvoří ověřovací token pomocí **uživatele Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Načítá se datová sada.

Automatizované Machine Learning v Sparku používá datové **toky**, které jsou laxně vytvářená vyhodnocené, neměnné operace s daty.  Tok dat může načíst datovou sadu z objektu BLOB s veřejným přístupem pro čtení nebo z adresy URL objektu BLOB s tokenem SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Úložiště dat můžete také zaregistrovat s pracovním prostorem v jednorázové registraci.

## <a name="experiment-submission"></a>Odeslání experimentu

V případě [automatizované konfigurace strojového učení](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)by vlastnost `spark_context` měla být nastavena pro balíček, který má být spuštěn v distribuovaném režimu. Vlastnost `concurrent_iterations` , která má maximální počet spuštěných iterací paralelně, by měla být nastavena na číslo nižší než jádro vykonavatele aplikace Spark.

## <a name="next-steps"></a>Další kroky

* Další informace o motivaci na základě automatizovaného strojového učení najdete v tématu věnovaném [vydávání modelů na základě automatizovaného strojového učení od Microsoftu.](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Další informace o používání funkcí automatizovaného ML v Azure ML najdete [v tématu nové možnosti automatizovaného strojového učení v Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projekt AutoML z Microsoft Research](https://www.microsoft.com/research/project/automl/)
