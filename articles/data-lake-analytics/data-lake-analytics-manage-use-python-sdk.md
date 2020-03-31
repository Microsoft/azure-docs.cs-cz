---
title: Správa Azure Data Lake Analytics pomocí Pythonu
description: Tento článek popisuje, jak používat Python ke správě účtů Data Lake Analytics, zdrojů dat, uživatelů & úloh.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68355976"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Správa Azure Data Lake Analytics pomocí Pythonu
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Pythonu.

## <a name="supported-python-versions"></a>Podporované verze Pythonu

* Použijte 64bitovou verzi Pythonu.
* Můžete použít standardní distribuci Pythonu, která se nachází na **[Python.org ke stažení](https://www.python.org/downloads/)**. 
* Mnoho vývojářů považuje za vhodné používat **[distribuci Anaconda Python](https://www.anaconda.com/download/)**.  
* Tento článek byl napsán pomocí Pythonu verze 3.6 ze standardní distribuce Pythonu

## <a name="install-azure-python-sdk"></a>Instalace sady Azure Python SDK

Nainstalujte následující moduly:

* Modul **azure-mgmt-resource** obsahuje další moduly Azure pro službu Active Directory atd.
* Modul **azure-datalake-store** zahrnuje operace souborového systému Azure Data Lake Store. 
* Modul **azure-mgmt-datalake-store** zahrnuje operace správy účtu Azure Data Lake Store.
* Modul **azure-mgmt-datalake-analytics** zahrnuje operace Azure Data Lake Analytics. 

Nejprve se ujistěte, že máte nejnovější, `pip` spuštěním následujícího příkazu:

```
python -m pip install --upgrade pip
```

Tento dokument byl `pip version 9.0.1`napsán pomocí .

K instalaci `pip` modulů z příkazového řádku použijte následující příkazy:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Vytvoření nového skriptu Pythonu

Do skriptu vložte následující kód:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Spusťte tento skript a ověřte, zda lze moduly importovat.

## <a name="authentication"></a>Ověřování

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktivní ověřování uživatelů s automaticky otevíraným displejem

Tato metoda není podporována.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktivní ověřování uživatelů s kódem zařízení

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Neinteraktivní ověřování pomocí protokolu SPI a tajného klíče

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Neinteraktivní ověřování pomocí rozhraní API a certifikátu

Tato metoda není podporována.

## <a name="common-script-variables"></a>Běžné proměnné skriptu

Tyto proměnné se používají ve vzorcích.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Vytvořit klienty

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics

Nejprve vytvořte účet úložiště.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Pak vytvořte účet ADLA, který používá toto úložiště.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Odeslání úlohy

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Čekání na ukončení úlohy

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Seznam kanálů a opakování
V závislosti na tom, zda jsou připojena metadata kanálu nebo opakování, můžete uvést seznam kanálů a opakování.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Správa výpočetních zásad

Objekt DataLakeAnalyticsAccountManagementClient poskytuje metody pro správu zásad výpočetních prostředků pro účet Data Lake Analytics.

### <a name="list-compute-policies"></a>Seznam zásad výpočtu

Následující kód načte seznam zásad výpočetních prostředků pro účet Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Vytvoření nové výpočetní zásady

Následující kód vytvoří nové výpočetní zásady pro účet Data Lake Analytics, nastavení maximální au k dispozici pro zadaného uživatele 50 a minimální prioritu úlohy 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Další kroky

- Pokud chcete použít jiné podporované nástroje a zobrazit stejný kurz, klikněte na selektory karet v horní části stránky.
- Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Úkoly správy najdete v [tématu Správa Azure Data Lake Analytics pomocí portálu Azure](data-lake-analytics-manage-use-portal.md).

