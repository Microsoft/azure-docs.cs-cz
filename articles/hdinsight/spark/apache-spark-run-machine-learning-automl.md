---
title: Spouštění Azure Machine Learning úloh v Apache Spark ve službě HDInsight
description: Naučte se spouštět úlohy Azure Machine Learning pomocí automatizovaného strojového učení (AutoML) v Apache Spark ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 4e6b648ed70f6ff57a2d11cde43b8168b800fcb3
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806911"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Spouštění Azure Machine Learning úloh pomocí automatizovaného strojového učení na Apache Spark ve službě HDInsight

Azure Machine Learning zjednodušuje a zrychluje sestavování, školení a nasazení modelů strojového učení. V rámci automatizovaného strojového učení (AutoML) začnete s školicími daty, která mají definovanou cílovou funkci, a pak iterovat kombinací algoritmů a výběrů funkcí tak, aby automaticky vybrali nejlepší model pro vaše data na základě výsledků školení. HDInsight umožňuje zákazníkům zřídit clustery se stovkami uzlů. AutoML běžící na Sparku v clusteru HDInsight umožňuje uživatelům používat výpočetní kapacitu na těchto uzlech ke spouštění školicích úloh v rámci škálování a spouštění více školicích úloh paralelně. Díky tomu můžou uživatelé spouštět AutoML experimenty při sdílení výpočtů s ostatními úlohami s velkými objemy dat.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalace Azure Machine Learning v clusteru HDInsight

Obecné kurzy k automatizovanému strojovém učení najdete v tématu [kurz: Použití automatizovaného strojového učení k sestavení regresního modelu](../../machine-learning/service/tutorial-auto-train-models.md).
Všechny nové clustery HDInsight-Spark jsou předinstalované se sadou AutoML SDK. Pomocí tohoto [ukázkového poznámkového bloku Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi)můžete začít s AutoML ve službě HDInsight. Tento Jupyter Notebook ukazuje, jak používat automatizované třídění strojového učení pro jednoduchý problém klasifikace.

> [!Note]
> Balíčky Azure Machine Learning se instalují do prostředí conda pro python3. Nainstalovaný Poznámkový blok Jupyter by se měl spustit pomocí jádra PySpark3.

Případně můžete použít také poznámkové bloky Zeppelin k použití AutoML.

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

V [automatizované konfiguraci machine learningu](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)by měla být vlastnost `spark_context` nastavená tak, aby balíček běžel v distribuovaném režimu. Vlastnost `concurrent_iterations`, což je maximální počet souběžně spuštěných iterací, by měl být nastaven na číslo menší než jádro vykonavatele aplikace Spark.

## <a name="next-steps"></a>Další kroky

* Další informace o motivaci na základě automatizovaného strojového učení najdete v tématu věnovaném [vydávání modelů na základě automatizovaného strojového učení od Microsoftu.](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Další informace o používání funkcí automatizovaného ML v Azure ML najdete [v tématu nové možnosti automatizovaného strojového učení v Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/) .
* [Projekt AutoML z Microsoft Research](https://www.microsoft.com/research/project/automl/)
