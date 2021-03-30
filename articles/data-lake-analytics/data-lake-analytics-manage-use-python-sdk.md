---
title: Správa Azure Data Lake Analytics pomocí Pythonu
description: Tento článek popisuje, jak pomocí Pythonu spravovat účty Data Lake Analytics, zdroje dat, uživatele a úlohy &.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/08/2018
ms.custom: devx-track-python
ms.openlocfilehash: fda09047d138e404eeca87ed3eba9bb72ff62d56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220223"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Správa Azure Data Lake Analytics pomocí Pythonu
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Tento článek popisuje, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Pythonu.

## <a name="supported-python-versions"></a>Podporované verze Pythonu

* Použijte 64 verze Pythonu.
* Můžete použít standardní distribuci Pythonu, která se našla v **[Python.org stažení](https://www.python.org/downloads/)**. 
* Mnohé z vývojářů si můžou snadno používat **[distribuci Pythonu Anaconda](https://www.anaconda.com/download/)**.  
* Tento článek se vytvořil pomocí Pythonu verze 3,6 ze standardní distribuce Pythonu.

## <a name="install-azure-python-sdk"></a>Instalace sady Azure Python SDK

Nainstalujte následující moduly:

* Modul **Azure-pro správu prostředků** zahrnuje další moduly Azure pro Active Directory atd.
* Modul **Azure-datalake-Store** zahrnuje operace Azure Data Lake Store systému souborů. 
* Modul **Azure-Správa-datalake-Store** zahrnuje operace správy účtu Azure Data Lake Store.
* Modul **Azure-Správa – datalake-Analytics** zahrnuje operace Azure Data Lake Analytics. 

Nejdřív zajistěte, abyste měli nejnovější verzi spuštěním tohoto `pip` příkazu:

```console
python -m pip install --upgrade pip
```

Tento dokument byl napsán pomocí `pip version 9.0.1` .

Pomocí následujících `pip` příkazů nainstalujte moduly z příkazového řádku:

```console
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

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktivní ověřování uživatelů pomocí automaticky otevíraného okna

Tato metoda není podporována.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktivní ověřování uživatelů pomocí kódu zařízení

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Neinteraktivní ověřování s SPI a tajným kódem

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Neinteraktivní ověřování pomocí rozhraní API a certifikátu

Tato metoda není podporována.

## <a name="common-script-variables"></a>Běžné proměnné skriptu

Tyto proměnné jsou používány v ukázkách.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Vytvoření klientů

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

Nejdřív vytvořte účet úložiště.

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

## <a name="submit-a-job"></a>Odeslat úlohu

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

## <a name="wait-for-a-job-to-end"></a>Počkat na ukončení úlohy

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
V závislosti na tom, jestli mají vaše úlohy připojená metadata kanálu nebo opakování, můžete zobrazit seznam kanálů a opakování.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Správa výpočetních zásad

Objekt DataLakeAnalyticsAccountManagementClient poskytuje metody pro správu výpočetních zásad pro účet Data Lake Analytics.

### <a name="list-compute-policies"></a>Vypsat výpočetní zásady

Následující kód načte seznam výpočetních zásad pro účet Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Vytvořit nové výpočetní zásady

Následující kód vytvoří novou výpočetní zásadu pro účet Data Lake Analytics, nastaví maximální jednotky Austrálie dostupné pro zadaného uživatele na 50 a minimální prioritu úlohy na 250.

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
- Informace o úlohách správy najdete v tématu [správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md).

