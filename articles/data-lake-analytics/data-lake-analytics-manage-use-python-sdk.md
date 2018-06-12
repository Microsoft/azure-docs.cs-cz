---
title: Správa Azure Data Lake Analytics používá Python
description: Tento článek popisuje, jak používat Python ke správě účtů Data Lake Analytics, zdroje dat, uživatele a úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 2f3912069b54b3ad761493f02336d8d58866f53a
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261456"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Správa Azure Data Lake Analytics používá Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak pro správu účtů Azure Data Lake Analytics, zdroje dat, uživatelů a úloh s použitím jazyka Python.

## <a name="supported-python-versions"></a>Podporované verze Python

* Použijte 64bitovou verzi jazyka Python.
* Můžete použít standardní distribuci jazyka Python nalezený na  **[Python.org stáhne](https://www.python.org/downloads/)**. 
* Celá řada vývojářů být vhodné použít  **[distribuci jazyka Python Anaconda](https://www.continuum.io/downloads)**.  
* Tento článek byl napsané v Pythonu verze 3.6 ze standardního distribučního Python

## <a name="install-azure-python-sdk"></a>Instalace sady Azure Python SDK

Nainstalujte následující moduly:

* **Prostředků azure mgmt** modul obsahuje další moduly Azure Active Directory, atd.
* **Úložiště azure datalake** modul obsahuje operace systému souborů Azure Data Lake Store. 
* **Azure mgmt datalake úložiště** modul obsahuje operace správy účtů Azure Data Lake Store.
* **Azure-mgmt-datalake-analytics** modulu zahrnuje operace Azure Data Lake Analytics. 

První, zajistěte, abyste měli nejnovější `pip` spuštěním následujícího příkazu:

```
python -m pip install --upgrade pip
```

Tento dokument byla zapsána pomocí `pip version 9.0.1`.

Použijte následující `pip` příkazy pro instalaci modulů z příkazovému řádku:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Vytvořit nový skript v jazyce Python

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

Spusťte tento skript k ověření, že můžete naimportovat moduly.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktivním ověřování uživatelů s automaticky otevíraného okna

Tato metoda není podporována.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktivním ověřování uživatelů s kódem zařízení

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Neinteraktivní ověřování s SPI a tajný klíč

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Neinteraktivní ověřování pomocí rozhraní API a certifikátu

Tato metoda není podporována.

## <a name="common-script-variables"></a>Běžné proměnné skriptu

Tyto proměnné se používají v ukázky.

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
Pak vytvořte ADLA účtu, který použije toto úložiště.

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

## <a name="wait-for-a-job-to-end"></a>Počkejte na ukončení úlohy

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Seznam kanálů a opakování
Podle toho, jestli vaše úlohy mají kanálu nebo opakování metadata připojen, můžete seznam kanálů a opakování.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Správa zásad výpočetní

Objekt DataLakeAnalyticsAccountManagementClient poskytuje metody pro správu výpočetních zásady pro účet Data Lake Analytics.

### <a name="list-compute-policies"></a>Seznam výpočetní zásad

Následující kód načte seznam výpočetní zásad pro účet Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Vytvořit novou zásadu výpočetní

Následující kód vytvoří novou zásadu výpočetní pro účet Data Lake Analytics, nastavení maximální Austrálie dostupná pro zadaného uživatele na 50 a priority minimální úloh na 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Další postup

- Pokud chcete použít jiné podporované nástroje a zobrazit stejný kurz, klikněte na selektory karet v horní části stránky.
- Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informace týkající se úloh správy najdete v tématu [Správa služby Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md).

