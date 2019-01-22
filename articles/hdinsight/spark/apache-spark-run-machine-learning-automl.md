---
title: Spuštění úlohy Azure Machine Learning pomocí automatizovaných strojového učení (AutoML) na Apache Spark v Azure HDInsight
description: Zjistěte, jak spouštět úlohy Azure Machine Learning s automatizované strojového učení (AutoML) na Apache Spark v Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: howto
ms.date: 01/14/2019
ms.openlocfilehash: 58852ee5de97bef043f8c0b67d9e90cdc057912a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440214"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Spuštění úlohy Azure Machine Learning pomocí automatizovaných strojového učení (AutoML) na Apache Spark v Azure HDInsight

Azure Machine Learning je nástroj pro spolupráci, přetáhněte myší, které můžete použít k sestavení, testovat a nasazovat řešení prediktivní analýzy s vašimi daty. Azure Machine Learning publikuje modely jako webové služby, které je možné snadno využívat ve vlastních aplikacích nebo nástrojích BI, jako je například aplikace Excel. Automatizované strojového učení (AutoML) pomáhá vytvářet vysoce kvalitní modelů strojového učení pomocí inteligentní automatizaci a optimalizace. AutoML rozhodne správný algoritmus a hyper parametry se mají použít pro typy konkrétní problém.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Clusteru služby HDInsight nainstalovat Azure Machine Learning

> [!Note]
> Pracovní prostor Azure ML je teď dostupná v těchto oblastech: eastus eastus2 a westcentralus. HDInsight cluster musí také vytvořené v některém z těchto oblastí.

Obecné kurzy Azure Machine Learning a automatizované strojového učení najdete na stránce [kurzu: Vytvoření prvního experimentu datové vědy v nástroji Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) a [kurzu: Automatizované machine learningu k vytváření regresní model](../../machine-learning/service/tutorial-auto-train-models.md).
Chcete-li nainstalovat Azure ml v clusteru Azure HDInsight, spusťte skript akce – [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) – na hlavní uzly a pracovních uzlů v clusteru HDInsight 3.6 Spark 2.3.0 (doporučeno). Tuto akci se skripty můžete spustit buď jako součást procesu vytváření clusteru, nebo v existujícím clusteru na webu Azure portal.

Další informace o akcí skriptů najdete v článku [HDInsight založených na Linuxu přizpůsobit clustery pomocí akcí skriptů](../hdinsight-hadoop-customize-cluster-linux.md). Kromě instalace balíčky Azure Machine Learning a závislosti, skript také soubory ke stažení ukázkové aplikace Jupyter Notebook (do cesty `HdiNotebooks/PySpark` výchozí úložiště). Tento poznámkový blok Jupyter ukazuje, jak používat automatické strojového učení třídění pro jednoduché klasifikace problému.

> [!Note]
> Balíčky Azure Machine Learning jsou nainstalovány do prostředí conda Python3. Nainstalované aplikace Jupyter notebook by měl spustit pomocí PySpark3 jádra.

## <a name="authentication-for-workspace"></a>Ověřování pro pracovní prostor

Vytváření pracovního prostoru a odeslání experimentu vyžadují ověřovací token. Tento token může být generována pomocí [aplikaci Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). [Uživatele Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) lze také použít k vygenerování požadovaného ověření tokenu, pokud není u daného účtu povoleno ověřování Multi-Factor Authentication.  

Následující fragment kódu vytvoří token ověřování pomocí **aplikaci Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
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

V konfiguraci automatizované machine learning, vlastnost `spark_context` stanovit pro spuštění v distribuovaném režimu balíčku. Vlastnost `concurrent_iterations`, což je maximální počet iterací, které spouští paralelně, musí být nastavená na číslem menším než jader prováděcí modul pro aplikace Spark.

## <a name="next-steps"></a>Další postup

* Další informace o motivace za automatizované strojového učení najdete v tématu [strojového učení automatizované verze modely na úrovni tempem pomocí společnosti Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [Projekt AutoML od Microsoft Research](https://www.microsoft.com/research/project/automl/)