---
title: Konfigurace kontejnerů – Translator Text
titleSuffix: Azure Cognitive Services
description: Translator Text poskytuje kontejnery se společným konfiguračním rozhraním, abyste mohli snadno nakonfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení pro vaše kontejnery.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507668"
---
# <a name="configure-translator-text-docker-containers"></a>Konfigurace kontejnerů Docker Translator Text

Translator Text poskytuje každému kontejneru společné konfigurační rozhraní, abyste mohli snadno nakonfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení kontejnerů.

## <a name="configuration-settings"></a>Nastavení konfigurace

Kontejner má následující nastavení konfigurace:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) .|
|Ano|[Konkrétní](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Fluent](#fluentd-settings)|Zapisuje protokol a volitelně také data metriky na server, který je na něj.|
|Ne|Proxy server HTTP|Konfiguruje proxy server HTTP pro vytváření odchozích požadavků.|
|Ne|[Protokolu](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|Ne|[Připojí](#mount-settings)|Přečte a zapisuje data z hostitelského počítače do kontejneru a z kontejneru zpátky do hostitelského počítače.|

> [!IMPORTANT]
> Nastavení [`Eula`](#eula-setting) musí být zadáno s hodnotou `accept`; v opačném případě se Váš kontejner nespustí.

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním možnosti `--mount` v příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat vstupní připojení nebo připojení výstupu.

Kontejnery Translator Text nepoužívají k ukládání dat školení nebo služby vstupní ani výstupní připojení. 

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](how-to-install-containers.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|Volitelné| Name (Název) | Data type | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery Translator Text nepoužívají.|
|Volitelné| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker 

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](how-to-install-containers.md#stop-the-container) .

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

## <a name="translator-text-container-docker-examples"></a>Příklady Translator Text kontejnerů Docker

Následující příklady Docker jsou pro kontejner Translator Text.

### <a name="basic-example"></a>Základní příklad

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Příklad protokolování

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak nainstalovat a spustit kontejnery](how-to-install-containers.md) .
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
