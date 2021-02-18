---
title: Příchozí sledování žádostí ve službě Azure Application Insights s OpenCensus Pythonem | Microsoft Docs
description: Monitorujte volání žádostí pro aplikace Python prostřednictvím OpenCensus Pythonu.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585673"
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

3. Ujistěte se, že je AzureExporter správně nakonfigurovaný v `settings.py` rámci `OPENCENSUS` . Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
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

2. Aplikaci můžete také nakonfigurovat `flask` přes `app.config` . Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
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

2. Doplnění můžete nakonfigurovat `pyramid` přímo v kódu. Pro žádosti z adres URL, které nechcete sledovat, je přidejte do `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Sledování aplikací FastAPI

OpenCensus nemá příponu pro FastAPI. Chcete-li napsat vlastní middleware FastAPI, proveďte následující kroky:

1. Jsou vyžadovány následující závislosti: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. Přidejte [middleware FastAPI](https://fastapi.tiangolo.com/tutorial/middleware/). Ujistěte se, že jste nastavili server typu span: `span.span_kind = SpanKind.SERVER` .

3. Spusťte aplikaci. Volání do vaší aplikace FastAPI by měla být automaticky sledována a telemetrii by měla být zaprotokolována přímo Azure Monitor.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Další kroky

* [Mapa aplikace](./app-map.md)
* [Dostupnost](./monitor-web-app-availability.md)
* [Hledání](./diagnostic-search.md)
* [Dotaz na protokol (Analytics)](../logs/log-query-overview.md)
* [Diagnostika transakcí](./transaction-diagnostics.md)

