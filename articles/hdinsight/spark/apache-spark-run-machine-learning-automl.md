---
title: Spouštění úloh Azure Machine Learning na Apache Spark v HDInsightu
description: Zjistěte, jak spouštět úlohy Azure Machine Learning s automatickým učením (AutoML) na Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638879"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Spouštění úloh Azure Machine Learning s automatizovaným strojovým učením na Apache Spark v HDInsightu

Azure Machine Learning zjednodušuje a urychluje vytváření, školení a nasazování modelů strojového učení. V automatizovaném strojovém učení (AutoML) začnete s trénovacími daty, která mají definovanou cílovou funkci, a pak iteratem prostřednictvím kombinací algoritmů a výběrů funkcí automaticky vyberete nejlepší model pro vaše data na základě trénovacích skóre. HDInsight umožňuje zákazníkům zřídit clustery se stovkami uzlů. Automatické ml spuštěné na Sparku v clusteru HDInsight umožňuje uživatelům používat výpočetní kapacitu napříč těmito uzly ke spuštění školicích úloh na více stran a ke paralelnímu spouštění více úloh školení. To umožňuje uživatelům spouštět experimenty Automatické ml při sdílení výpočetních prostředků s ostatními úlohami velkých objemů dat.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalace Azure Machine Learning u clusteru HDInsight

Obecné kurzy automatizovaného strojového učení najdete [v tématu Výuka: Pomocí automatizovaného strojového učení k vytvoření regresního modelu](../../machine-learning/tutorial-auto-train-models.md).
Všechny nové clustery HDInsight-Spark jsou předinstalované pomocí sady AzureML-AutoML SDK.

> [!Note]
> Balíčky Azure Machine Learning se instalují do prostředí Conda v Pythonu3. Nainstalovaný notebook Jupyter by měl být spuštěn pomocí jádra PySpark3.

K použití automatické homl můžete použít také poznámkové bloky Zeppelin.

> [!Note]
> Zeppelin má [známý problém,](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) kdy PySpark3 nevybírá správnou verzi Pythonu. Použijte zdokumentované řešení.

## <a name="authentication-for-workspace"></a>Ověřování pro pracovní prostor

Vytvoření pracovního prostoru a odeslání experimentu vyžadují ověřovací token. Tento token lze generovat pomocí [aplikace Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). [Uživatele Azure AD](/azure/python/python-sdk-azure-authenticate) lze také použít ke generování požadovaný ověřovací token, pokud vícefaktorové ověřování není povolena na účtu.  

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

## <a name="loading-dataset"></a>Načítání datové sady

Automatizované strojové učení na Sparku používá **toky dat**, které jsou líně vyhodnocovány, neměnné operace s daty.  Datový tok můžete načíst datovou sadu z objektu blob s veřejným přístupem pro čtení nebo z adresy URL objektu blob s tokenem SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Úložiště dat můžete také zaregistrovat v pracovním prostoru pomocí jednorázové registrace.

## <a name="experiment-submission"></a>Odeslání experimentu

V [konfiguraci automatizovaného strojového učení](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)by měla být vlastnost `spark_context` nastavena tak, aby byl balíček spuštěn v distribuovaném režimu. Vlastnost `concurrent_iterations`, což je maximální počet iterací prováděných paralelně, by měla být nastavena na číslo menší než jádra vykonavatele pro aplikaci Spark.

## <a name="next-steps"></a>Další kroky

* Další informace o motivaci automatizovaného strojového učení najdete [v tématu Vydávání modelů rychlým tempem pomocí automatizovaného strojového učení společnosti Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Další informace o používání funkcí Azure ML Automated ML najdete v tématu [Nové funkce automatizovaného strojového učení v Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projekt AutoML od společnosti Microsoft Research](https://www.microsoft.com/research/project/automl/)
