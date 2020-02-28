---
title: Příchozí sledování žádostí ve službě Azure Application Insights s OpenCensus Pythonem | Microsoft Docs
description: Monitorujte volání žádostí pro aplikace Python prostřednictvím OpenCensus Pythonu.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669943"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Sledování příchozích požadavků pomocí OpenCensus Pythonu

Příchozí data žádosti se shromažďují pomocí OpenCensus Pythonu a jejich různých integrací. Sledovat příchozí data požadavku odesílaná do vašich webových aplikací založených na oblíbených webových rozhraních `django`, `flask` a `pyramid`. Data se pak odešlou do Application Insights v části Azure Monitor jako `requests` telemetrie.

Nejdřív Instrumentujte svou aplikaci v Pythonu pomocí nejnovější [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Sledování aplikací Django

1. Stáhněte si a nainstalujte `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-django/) a Instrumentujte svoji aplikaci pomocí middlewaru `django`. Příchozí požadavky odeslané do vaší aplikace `django` budou sledovány.

2. Do souboru `settings.py` v části `MIDDLEWARE`přidejte `opencensus.ext.django.middleware.OpencensusMiddleware`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Ujistěte se, že je AzureExporter správně nakonfigurovaný v `settings.py` v části `OPENCENSUS`.

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

4. Můžete také přidat adresy URL do `settings.py` v části `BLACKLIST_PATHS` pro požadavky, které nechcete sledovat.

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

## <a name="tracking-flask-applications"></a>Sledování aplikací v baňce

1. Stáhněte si a nainstalujte `opencensus-ext-flask` z [PyPI](https://pypi.org/project/opencensus-ext-flask/) a Instrumentujte svoji aplikaci pomocí middlewaru `flask`. Příchozí požadavky odeslané do vaší aplikace `flask` budou sledovány.

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

2. Middleware `flask` můžete nakonfigurovat přímo v kódu. Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `BLACKLIST_PATHS`.

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

## <a name="tracking-pyramid-applications"></a>Sledování jehlanových aplikací

1. Stáhněte a nainstalujte `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) a Instrumentujte svoji aplikaci pomocí `pyramid` doplnění. Příchozí požadavky odeslané do vaší aplikace `pyramid` budou sledovány.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Doplnění `pyramid` můžete nakonfigurovat přímo v kódu. Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `BLACKLIST_PATHS`.

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
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [Dotaz na protokol (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostika transakcí](../../azure-monitor/app/transaction-diagnostics.md)
