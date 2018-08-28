---
title: Správa Azure Data Lake Analytics pomocí Pythonu
description: Tento článek popisuje použití Pythonu ke správě účtů Data Lake Analytics, zdroje dat, uživatele a úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: f73ef118efbdfc94d8cb9b7d81717bd13511c785
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048278"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Správa Azure Data Lake Analytics pomocí Pythonu
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účtů Azure Data Lake Analytics, zdroje dat, uživatele a úlohy s použitím jazyka Python.

## <a name="supported-python-versions"></a>Podporované verze Pythonu

* Použijte 64bitovou verzi jazyka Python.
* Můžete použít standardní, distribuci jazyka Python nalezený na  **[Python.org stáhne](https://www.python.org/downloads/)**. 
* Celá řada vývojářů považuje pohodlné používat ho  **[distribuci jazyka Python Anaconda](https://www.anaconda.com/download/)**.  
* Tento článek byl napsané pomocí Pythonu 3.6 verze ze standardní distribuci jazyka Python

## <a name="install-azure-python-sdk"></a>Instalace sady Azure Python SDK

Nainstalujte následující moduly:

* **Azure-mgmt-resource** modulu zahrnuje další moduly Azure pro Active Directory atd.
* **Azure-datalake-store** modul zahrnuje operace systému souborů Azure Data Lake Store. 
* **Azure-mgmt-datalake-store** modulu zahrnuje operace správy účtů Azure Data Lake Store.
* **Azure-mgmt-datalake-analytics** modulu zahrnuje operace Azure Data Lake Analytics. 

Nejprve, zkontrolujte, že máte nejnovější `pip` spuštěním následujícího příkazu:

```
python -m pip install --upgrade pip
```

Tento dokument byl zapsán pomocí `pip version 9.0.1`.

Pomocí následujících `pip` příkazy, abyste nainstalovali moduly řádku:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Vytvořit nový skript Pythonu

Vložte následující kód do skriptu:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Spusťte tento skript k ověření, že lze importovat moduly.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktivním ověřování uživatelů pomocí automaticky otevírané okno

Tato metoda není podporována.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktivním ověřování uživatelů pomocí kódu zařízení

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Neinteraktivní ověřování SPI a tajného kódu

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Neinteraktivní ověřování s rozhraním API a certifikátu

Tato metoda není podporována.

## <a name="common-script-variables"></a>Společné proměnné skriptu

Tyto proměnné se používají ve vzorcích.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Vytvoření klientů

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
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
Vytvořte účet ADLA, který se použije toto úložiště.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
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

## <a name="wait-for-a-job-to-end"></a>Čekání úlohy na konec

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Seznam kanálech a opakováních
Podle toho, jestli vaše úlohy mají kanálu nebo opakování metadat připojený, můžete vytvořit seznam kanálech a opakováních.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Správa zásad compute

Objekt DataLakeAnalyticsAccountManagementClient poskytuje metody pro správu zásad compute pro účet Data Lake Analytics.

### <a name="list-compute-policies"></a>Seznam zásad compute

Následující kód načte seznam zásad výpočetní prostředky pro účet Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Vytvořit novou zásadu výpočetní prostředky

Následující kód vytvoří novou zásadu výpočetní prostředky pro účet Data Lake Analytics, nastavení maximální počet jednotek au dostupná pro zadaného uživatele na 50 a Priorita minimální úlohy na 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Další postup

- Pokud chcete použít jiné podporované nástroje a zobrazit stejný kurz, klikněte na selektory karet v horní části stránky.
- Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informace týkající se úloh správy najdete v tématu [Správa služby Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md).

