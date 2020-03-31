---
title: Sledování závislostí v Přehledech aplikací Azure s Pythonem OpenCensus | Dokumenty společnosti Microsoft
description: Monitorujte volání závislostí pro aplikace Pythonu prostřednictvím OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669926"
---
# <a name="track-dependencies-with-opencensus-python"></a>Sledování závislostí pomocí OpenCensus Python

Závislost je externí součást, která je volána vaší aplikací. Data závislostí jsou shromažďována pomocí OpenCensus Python a jeho různých integrací. Data se pak odešlou do Application `dependencies` Insights v rámci Azure Monitor u telemetrie.

Nejprve instrumentujte aplikaci Python s [nejnovějšíopencensus pythonovou sadou SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Neprocesové závislosti

OpenCensus Python SDK pro Azure Monitor umožňuje odesílat telemetrie závislostí "v procesu" (informace a logika, ke kterým dochází v rámci vaší aplikace). Neprocesové závislosti budou `type` mít `INPROC` pole jako v analýze.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Závislosti s integrací "požadavků"

Sledujte své odchozí požadavky s `requests` integrací OpenCensus.

Stáhněte `opencensus-ext-requests` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-requests/) a přidejte jej do integrace trasování. Požadavky odeslané pomocí [knihovny požadavků](https://pypi.org/project/requests/) Pythonu budou sledovány.

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

## <a name="dependencies-with-httplib-integration"></a>Závislosti s integrací httplib

Sledujte své odchozí požadavky `httplib` s integrací OpenCensus.

Stáhněte `opencensus-ext-httplib` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-httplib/) a přidejte jej do integrace trasování. Požadavky odeslané pomocí [http.client](https://docs.python.org/3.7/library/http.client.html) pro Python3 nebo [httplib](https://docs.python.org/2/library/httplib.html) pro Python2 budou sledovány.

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

## <a name="dependencies-with-django-integration"></a>Závislosti s integrací "django"

Sledujte své odchozí žádosti Django `django` s integrací OpenCensus.

Stáhněte `opencensus-ext-django` si a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-django/) a přidejte následující řádek do `MIDDLEWARE` sekce v souboru Django. `settings.py`

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Další konfigurace může být poskytnuta, přečtěte si [vlastní nastavení](https://github.com/census-instrumentation/opencensus-python#customization) pro úplnou referenci.

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

## <a name="dependencies-with-mysql-integration"></a>Závislosti s integrací "mysql"

Sledujte své závislosti mysql `mysql` s integrací OpenCensus. Tato integrace podporuje knihovnu [mysql-connector.](https://pypi.org/project/mysql-connector-python/)

Stáhněte `opencensus-ext-mysql` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-mysql/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Závislosti s integrací "pymysql"

Sledujte své závislosti PyMySQL `pymysql` s integrací OpenCensus.

Stáhněte `opencensus-ext-pymysql` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Závislosti s integrací "postgresql"

Sledujte své závislosti PostgreSQL `postgresql` s integrací OpenCensus. Tato integrace podporuje knihovnu [psycopg2.](https://pypi.org/project/psycopg2/)

Stáhněte `opencensus-ext-postgresql` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Závislosti s integrací "pymongo"

Sledujte své závislosti MongoDB `pymongo` s integrací OpenCensus. Tato integrace podporuje knihovnu [pymongo.](https://pypi.org/project/pymongo/)

Stáhněte `opencensus-ext-pymongo` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) a přidejte následující řádky do kódu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Závislosti s integrací "sqlalchemy"

Sledujte své závislosti pomocí SQLAlchemy pomocí integrace OpenCensus. `sqlalchemy` Tato integrace sleduje použití balíčku [sqlalchemy,](https://pypi.org/project/SQLAlchemy/) bez ohledu na základní databázi.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Další kroky

* [Mapa aplikace](../../azure-monitor/app/app-map.md)
* [Dostupnost](../../azure-monitor/app/monitor-web-app-availability.md)
* [Hledat](../../azure-monitor/app/diagnostic-search.md)
* [Dotaz protokolu (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostika transakcí](../../azure-monitor/app/transaction-diagnostics.md)
