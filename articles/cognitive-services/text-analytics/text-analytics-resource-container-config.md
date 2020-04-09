---
title: Konfigurace kontejnerů – analýza textu
titleSuffix: Azure Cognitive Services
description: Služba Text Analytics poskytuje každému kontejneru společnou konfigurační architekturu, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení pro vaše kontejnery.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878654"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurace kontejnerů dockeru analýzy textu

Služba Text Analytics poskytuje každému kontejneru společnou konfigurační architekturu, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení pro vaše kontejnery.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) a jsou používány společně a je nutné zadat platné hodnoty pro všechny tři z nich; jinak se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Toto `ApiKey` nastavení určuje klíč prostředků Azure, který slouží ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro apikey a hodnota musí být platný klíč [`Billing`](#billing-configuration-setting) pro prostředek _analýzy textu_ zadaný pro nastavení konfigurace.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Správa prostředků **Textové analýzy** v části **Klíče**

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

Toto `Billing` nastavení určuje identifikátor URI koncového bodu prostředku _Analýzy textu v_ Azure, který slouží k měření fakturačních informací pro kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný identifikátor URI koncového bodu pro prostředek __Text Analytics_ v Azure. Kontejner hlásí využití přibližně každých 10 až 15 minut.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Přehled **analýzy textu,** označený`Endpoint`

|Požaduje se| Name (Název) | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace. Další informace o získání fakturačního identifikátoru URI naleznete v [tématu shromažďování požadovaných parametrů](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení fluentdu

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení pověření proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Pomocí vazební připojení číst a zapisovat data do a z kontejneru. Vstupní připojení nebo výstupní připojení můžete určit `--mount` zadáním možnosti v příkazu [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/)

Kontejnery analýzy textu nepoužívají vstupní nebo výstupní připojení k ukládání dat školení nebo služby. 

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby dockeru a oprávněními umístění připojení hostitele. 

|Nepovinné| Name (Název) | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery analýzy textu tuto možnost nepoužívají.|
|Nepovinné| `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru 

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění kontejneru pokračuje v běhu, dokud jej [nezastavíte.](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)

* **Znak pokračování řádku**: Příkazy dockeru v následujících `\`částech používají jako znak pokračování řádku znak zpětného lomítka . Nazákladě požadavků hostitelského operačního systému jej nahraďte nebo odeberte. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste velmi dobře obeznámeni s kontejnery dockeru.

Nahraďte {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Text Analytics` prostředku, který `Text Analytics` je k dispozici na stránce Azure Keys. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Hodnota koncového bodu fakturace `Text Analytics` je dostupná na stránce Přehled Azure.| Viz [shromažďování požadovaných parametrů](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) pro explicitní příklady. |

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze `Text Analytics` stránky klíče prostředků Azure. 

#### <a name="key-phrase-extraction"></a>[Extrakce klíčových frází](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Rozpoznávání jazyka](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Analýza mínění](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak nainstalovat a spustit kontejnery](how-tos/text-analytics-how-to-install-containers.md)
* Použití více [kontejnerů služeb Cognitive Services](../cognitive-services-container-support.md)
