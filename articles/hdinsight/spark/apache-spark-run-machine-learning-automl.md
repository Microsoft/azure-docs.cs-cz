---
title: Spuštění úlohy Azure Machine Learning pomocí automatizovaných strojového učení (AutoML) na Apache Spark v Azure HDInsight
description: Zjistěte, jak spouštět úlohy Azure Machine Learning s automatizované strojového učení (AutoML) na Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917039"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Spuštění úlohy Azure Machine Learning pomocí automatizovaných strojového učení (AutoML) na Apache Spark v Azure HDInsight

Služba Azure Machine Learning zjednodušuje a zrychluje tvorbu, školení a nasazení modelů strojového učení. V automatizovaných strojového učení (AutoML) začněte s trénovací data, která má funkci definovanému cíli a potom iteraci v rámci kombinaci algoritmů a výběry funkcí automaticky vybrat nejlepší model pro data podle skóre školení. HDInsight umožňuje zákazníkům vytvářet clustery s využitím stovek uzlů. AutoML běžící na Sparku v clusteru služby HDInsight umožňuje uživatelům používat výpočetní kapacitu na těchto uzlech spouštět úlohy trénování způsobem horizontální navýšení kapacity a k paralelnímu spouštění několika úlohy trénování. To umožňuje uživatelům spouštět experimenty AutoML při sdílení s jejich ostatními úlohami velkých objemů dat tak výpočetní prostředky.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Clusteru služby HDInsight nainstalovat Azure Machine Learning

Obecné kurzy automatizované strojového učení najdete na stránce [kurzu: Automatizované machine learningu k vytváření regresní model](../../machine-learning/service/tutorial-auto-train-models.md).
Všechny nové clustery HDInsight Spark jsou předinstalovány se sadou SDK AzureML AutoML. Můžete začít s AutoML na HDInsight s tímto [ukázkové aplikace Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Tento poznámkový blok Jupyter ukazuje, jak používat automatické strojového učení třídění pro jednoduché klasifikace problému.

> [!Note]
> Balíčky Azure Machine Learning jsou nainstalovány do prostředí conda Python3. Nainstalované aplikace Jupyter notebook by měl spustit pomocí PySpark3 jádra.

Můžete také použít poznámkových bloků Zeppelin AutoML také používat.

> [!Note]
> Má Zeppelin [známý problém](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) kde PySpark3 nebude vybrat správnou verzi Pythonu. Použijte prosím dokumentované řešení.

## <a name="authentication-for-workspace"></a>Ověřování pro pracovní prostor

Vytváření pracovního prostoru a odeslání experimentu vyžadují ověřovací token. Tento token může být generována pomocí [aplikaci Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). [Uživatele Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) lze také použít k vygenerování požadovaného ověření tokenu, pokud není u daného účtu povoleno ověřování Multi-Factor Authentication.  

Následující fragment kódu vytvoří token ověřování pomocí **aplikaci Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
Následující fragment kódu vytvoří token ověřování pomocí **uživatele Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Načítají se datové sady

Automatizované strojového učení na Spark využívá **Dataflows**, které jsou laxně Vyhodnocená, neměnné operací s daty.  Toku dat můžete načíst datovou sadu z objektu blob s veřejné oprávnění ke čtení, nebo z adresy URL objektu blob s tokenem SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Úložiště budete taky moct registrovat s pracovním prostorem pomocí registraci.

## <a name="experiment-submission"></a>Odeslání experimentu

V [automatizované machine learning configuration](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), vlastnost `spark_context` stanovit pro spuštění v distribuovaném režimu balíčku. Vlastnost `concurrent_iterations`, což je maximální počet iterací, které spouští paralelně, musí být nastavená na číslem menším než jader prováděcí modul pro aplikace Spark.

## <a name="next-steps"></a>Další postup

* Další informace o motivace za automatizované strojového učení najdete v tématu [strojového učení automatizované verze modely na úrovni tempem pomocí společnosti Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Další podrobnosti o použití funkce Azure ML automatizované ML, naleznete v tématu [nový automatizované technologie strojového učení ve službě Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projekt AutoML od Microsoft Research](https://www.microsoft.com/research/project/automl/)
