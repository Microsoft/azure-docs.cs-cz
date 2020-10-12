---
title: Sledování závislostí ve službě Azure Application Insights s OpenCensus Pythonem | Microsoft Docs
description: Sledujte volání závislostí pro aplikace Python prostřednictvím OpenCensus Pythonu.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: abfd5e104bd4854781a0d3c9d08544506279518a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850079"
---
# <a name="track-dependencies-with-opencensus-python"></a>Sledování závislostí pomocí OpenCensus Pythonu

Závislost je externí komponenta volaná vaší aplikací. Data závislostí se shromažďují pomocí OpenCensus Pythonu a jejich různých integrací. Data se pak odešlou do Application Insights v části Azure Monitor jako `dependencies` telemetrie.

Nejdřív Instrumentujte svou aplikaci v Pythonu pomocí nejnovější [OpenCensus Python SDK](./opencensus-python.md).

## <a name="in-process-dependencies"></a>Závislosti v procesu

OpenCensus Python SDK pro Azure Monitor umožňuje odeslat "vnitroprocesové" telemetrii závislostí (informace a logika, ke kterým dochází v rámci vaší aplikace). Závislosti v procesu budou mít `type` pole jako v části `INPROC` Analýza.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Závislosti s integrací požadavků

Sledujte své odchozí žádosti s `requests` integrací OpenCensus.

Stáhněte si a nainstalujte `opencensus-ext-requests` z [PyPI](https://pypi.org/project/opencensus-ext-requests/) a přidejte je do integrace trasování. Žádosti odeslané pomocí knihovny [požadavků](https://pypi.org/project/requests/) Python budou sledovány.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Závislosti s integrací "httplib"

Sledujte své odchozí žádosti pomocí `httplib` integrace OpenCensus.

Stáhněte si a nainstalujte `opencensus-ext-httplib` z [PyPI](https://pypi.org/project/opencensus-ext-httplib/) a přidejte je do integrace trasování. Žádosti odeslané pomocí [http. Client](https://docs.python.org/3.7/library/http.client.html) pro python3 nebo [httplib](https://docs.python.org/2/library/httplib.html) pro Python2 se budou sledovat.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Závislosti s integrací "Django"

Sledujte své odchozí požadavky Django s `django` integrací OpenCensus.

> [!NOTE]
> Pouze odchozí požadavky Django, které jsou sledovány, jsou volání do databáze. Požadavky na Django aplikaci najdete v tématu [příchozí požadavky](./opencensus-python-request.md#tracking-django-applications).

Stáhněte si a nainstalujte `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-django/) a přidejte následující řádek do `MIDDLEWARE` oddílu v souboru Django `settings.py` .

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Můžete zadat další konfiguraci, přečíst si [vlastní nastavení](https://github.com/census-instrumentation/opencensus-python#customization) pro úplný odkaz.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Závislosti s integrací MySQL

Sledujte závislosti MYSQL pomocí `mysql` integrace OpenCensus. Tato integrace podporuje knihovnu [MySQL-Connector](https://pypi.org/project/mysql-connector-python/) .

Stáhněte si a nainstalujte si `opencensus-ext-mysql` z [PyPI](https://pypi.org/project/opencensus-ext-mysql/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Závislosti s integrací "pymysql"

Sledujte závislosti PyMySQL s `pymysql` integrací OpenCensus.

Stáhněte si a nainstalujte si `opencensus-ext-pymysql` z [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Závislosti s integrací "PostgreSQL"

Sledujte závislosti PostgreSQL s `postgresql` integrací OpenCensus. Tato integrace podporuje knihovnu [psycopg2](https://pypi.org/project/psycopg2/) .

Stáhněte si a nainstalujte si `opencensus-ext-postgresql` z [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Závislosti s integrací "pymongo"

Sledujte závislosti MongoDB s `pymongo` integrací OpenCensus. Tato integrace podporuje knihovnu [pymongo](https://pypi.org/project/pymongo/) .

Stáhněte si a nainstalujte si `opencensus-ext-pymongo` z [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Závislosti s integrací "sqlalchemy"

Sledujte závislosti pomocí SQLAlchemy s využitím `sqlalchemy` integrace OpenCensus. Tato integrace sleduje použití balíčku [sqlalchemy](https://pypi.org/project/SQLAlchemy/) bez ohledu na podkladovou databázi.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Další kroky

* [Mapa aplikace](./app-map.md)
* [Dostupnost](./monitor-web-app-availability.md)
* [Hledání](./diagnostic-search.md)
* [Dotaz na protokol (Analytics)](../log-query/log-query-overview.md)
* [Diagnostika transakcí](./transaction-diagnostics.md)

