---
title: Příchozí sledování žádostí ve službě Azure Application Insights s OpenCensus Pythonem | Microsoft Docs
description: Monitorujte volání žádostí pro aplikace Python prostřednictvím OpenCensus Pythonu.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: 6eae11e2157904b47582f78a601c8d2900e61247
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324586"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Sledování příchozích požadavků pomocí OpenCensus Pythonu

Příchozí data žádosti se shromažďují pomocí OpenCensus Pythonu a jejich různých integrací. Sledovat příchozí data požadavku odesílaná do vašich webových aplikací založených na oblíbených webových rozhraních `django` `flask` a `pyramid` . Data se pak odešlou do Application Insights v části Azure Monitor jako `requests` telemetrie.

Nejdřív Instrumentujte svou aplikaci v Pythonu pomocí nejnovější [OpenCensus Python SDK](./opencensus-python.md).

## <a name="tracking-django-applications"></a>Sledování aplikací Django

1. Stáhněte si a nainstalujte si `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-django/) a Instrumentujte svoji aplikaci pomocí `django` middlewaru. Příchozí požadavky odeslané do vaší `django` aplikace budou sledovány.

2. `opencensus.ext.django.middleware.OpencensusMiddleware`Do souboru zadejte `settings.py` `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Ujistěte se, že je AzureExporter správně nakonfigurovaný v `settings.py` rámci `OPENCENSUS` . Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Sledování aplikací v baňce

1. Stáhněte si a nainstalujte si `opencensus-ext-flask` z [PyPI](https://pypi.org/project/opencensus-ext-flask/) a Instrumentujte svoji aplikaci pomocí `flask` middlewaru. Příchozí požadavky odeslané do vaší `flask` aplikace budou sledovány.

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

2. Aplikaci můžete také nakonfigurovat `flask` přes `app.config` . Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `BLACKLIST_PATHS` .

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

1. Stažení a instalace `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) a instrumentace vaší aplikace s `pyramid` doplňováním Příchozí požadavky odeslané do vaší `pyramid` aplikace budou sledovány.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Doplnění můžete nakonfigurovat `pyramid` přímo v kódu. Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `BLACKLIST_PATHS` .

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

* [Mapa aplikace](./app-map.md)
* [Dostupnost](./monitor-web-app-availability.md)
* [Vyhledávání](./diagnostic-search.md)
* [Dotaz na protokol (Analytics)](../log-query/log-query-overview.md)
* [Diagnostika transakcí](./transaction-diagnostics.md)

