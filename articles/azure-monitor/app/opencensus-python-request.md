---
title: Sledování příchozích požadavků v Přehledech aplikací Azure s Pythonem OpenCensus | Dokumenty společnosti Microsoft
description: Sledování volání požadavků pro vaše aplikace Pythonu přes OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669943"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Sledování příchozích požadavků pomocí OpenCensus Python

Data příchozích požadavků jsou shromažďována pomocí OpenCensus Python a jeho různých integrací. Sledujte data příchozích požadavků odeslaná do webových aplikací `django` `flask` postavená nad oblíbenými webovými rámci a `pyramid`. Data se pak odešlou do Application `requests` Insights v rámci Azure Monitor u telemetrie.

Nejprve instrumentujte aplikaci Python s [nejnovějšíopencensus pythonovou sadou SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Sledování aplikací Django

1. Stáhněte `opencensus-ext-django` si a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-django/) a instrumentujte svou aplikaci pomocí middlewaru. `django` Příchozí žádosti odeslané `django` do vaší aplikace budou sledovány.

2. Zahrnout `opencensus.ext.django.middleware.OpencensusMiddleware` do `settings.py` souboru pod . `MIDDLEWARE`

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Ujistěte se, že AzureExport je správně nakonfigurovaný ve vašem `settings.py` under `OPENCENSUS`.

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

4. Můžete také přidat adresy `settings.py` `BLACKLIST_PATHS` URL pod pro požadavky, které nechcete sledovat.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Aplikace sledovací chňauchy

1. Stáhněte `opencensus-ext-flask` si a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-flask/) a instrumentujte svou aplikaci pomocí middlewaru. `flask` Příchozí žádosti odeslané `flask` do vaší aplikace budou sledovány.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. `flask` Middleware můžete nakonfigurovat přímo v kódu. Pro požadavky z adres URL, které nechcete sledovat, přidejte je do . `BLACKLIST_PATHS`

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Sledování aplikací jehlař

1. Stáhněte `opencensus-ext-django` a nainstalujte z [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) a instrumentujte svou aplikaci s doplněním. `pyramid` Příchozí žádosti odeslané `pyramid` do vaší aplikace budou sledovány.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. `pyramid` Doplnění můžete nakonfigurovat přímo v kódu. Pro požadavky z adres URL, které nechcete sledovat, přidejte je do . `BLACKLIST_PATHS`

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Další kroky

* [Mapa aplikace](../../azure-monitor/app/app-map.md)
* [Dostupnost](../../azure-monitor/app/monitor-web-app-availability.md)
* [Hledat](../../azure-monitor/app/diagnostic-search.md)
* [Dotaz protokolu (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostika transakcí](../../azure-monitor/app/transaction-diagnostics.md)
