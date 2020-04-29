---
title: Konfigurace kontejnerů – Analýza textu
titleSuffix: Azure Cognitive Services
description: Analýza textu poskytuje každému kontejneru společné konfigurační rozhraní, abyste mohli snadno nakonfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení kontejnerů.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f062fb2f3a653bc1b2845b92e373fdb67ba583d8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878654"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurace kontejnerů Docker Analýza textu

Analýza textu poskytuje každému kontejneru společné konfigurační rozhraní, abyste mohli snadno nakonfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení kontejnerů.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)a [`Eula`](#eula-setting) se používají společně a je nutné zadat platné hodnoty pro všechny tři z nich. v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Analýza textu_ zadaný pro nastavení [`Billing`](#billing-configuration-setting) konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Správa prostředků **Analýza textu** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

`Billing` Nastavení určuje identifikátor URI koncového bodu _Analýza textu_ prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro objekt __Analýza textu_ prostředku v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Přehled **Analýza textu** , označený`Endpoint`

|Požaduje se| Název | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním `--mount` možnosti v příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat vstupní připojení nebo připojení pro výstup.

Kontejnery Analýza textu nepoužívají k ukládání dat školení nebo služby vstupní ani výstupní připojení. 

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|Nepovinné| Název | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery Analýza textu nepoužívají.|
|Nepovinné| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker 

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) .

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Nahradit {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu prostředku `Text Analytics` , který je k dispozici na stránce klíčů Azure `Text Analytics` . |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na stránce Přehled Azure `Text Analytics` .| Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) . |

> [!IMPORTANT]
> Pro `Eula`spuštění `Billing`kontejneru musí `ApiKey` být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure `Text Analytics` . 

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak nainstalovat a spustit kontejnery](how-tos/text-analytics-how-to-install-containers.md) .
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
