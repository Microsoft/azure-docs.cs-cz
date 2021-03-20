---
title: Postup konfigurace kontejneru pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Naučte se konfigurovat kontejner pro rozpoznávání formulářů k analýze dat formuláře a tabulky.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86537999"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurovat kontejnery pro rozpoznávání formulářů

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Pomocí kontejnerů pro rozpoznávání formulářů Azure můžete vytvořit architekturu aplikace, která je optimalizovaná tak, aby využila výhod robustních cloudových funkcí i možností Edge.

Pomocí argumentů příkazu můžete nakonfigurovat prostředí runtime kontejneru rozpoznávání formuláře `docker run` . Tento kontejner má několik požadovaných nastavení a několik volitelných nastavení. Několik příkladů najdete v části ["Ukázkové příkazy pro spuštění Docker"](#example-docker-run-commands) . Nastavení fakturace jsou specifická pro kontejner.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Nastavení, [`Billing`](#billing-configuration-setting) a [`Eula`](#eula-setting) se používají společně. Je nutné zadat platné hodnoty pro všechna tři nastavení; v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey`Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Hodnota parametru ApiKey musí být platným klíčem pro prostředek pro _rozpoznávání formulářů_ , který je zadaný pro `Billing` v části nastavení konfigurace fakturace.

Toto nastavení můžete najít v Azure Portal v části **Správa prostředků nástroje pro rozpoznávání formulářů** v části **klíče**.

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

`Billing`Nastavení určuje identifikátor URI koncového bodu prostředku nástroje _pro rozpoznávání formulářů_ v Azure, který se používá k měření informací o fakturaci pro kontejner. Hodnota pro toto nastavení konfigurace musí být platný identifikátor URI koncového bodu pro prostředek pro _rozpoznávání formulářů_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete v Azure Portal v části **Přehled rozpoznávání formulářů** v části **koncový bod**.

|Vyžadováno| Name | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](form-recognizer-container-howto.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním `--mount` Možnosti v [ `docker run` příkazu](https://docs.docker.com/engine/reference/commandline/run/)můžete zadat vstupní připojení nebo připojení pro výstup.

Kontejner pro rozpoznávání formulářů vyžaduje vstupní připojení a výstupní připojení. Vstupní připojení může být jen pro čtení a vyžaduje se pro přístup k datům, která se používají pro školení a bodování. Ke výstupnímu připojení je potřeba zapisovat a použít ho k uložení modelů a dočasných dat.

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění připojení [hostitelského počítače](form-recognizer-container-howto.md#the-host-computer) není k dispozici z důvodu konfliktu mezi oprávněními účtu Docker Service a oprávněními k umístění hostitele připojení.

|Volitelné| Name | Datový typ | Popis |
|-------|------|-----------|-------------|
|Povinné| `Input` | Řetězec | Cíl vstupního připojení Výchozí hodnota je `/input`.    <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Vyžadováno| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`.  <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy. Když je spuštěný, kontejner zůstane spuštěný, dokud [ho nezastavíte](form-recognizer-container-howto.md#stop-the-container).

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko ( \\ ) jako znak pro pokračování řádku. Nahraďte nebo odeberte tento znak v závislosti na požadavcích vašeho hostitelského operačního systému.
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery Docker.

Nahraďte {_argument_name_} v následující tabulce vlastními hodnotami:

| Zástupný symbol | Hodnota |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Klíč, který se používá ke spuštění kontejneru. Je k dispozici na stránce klíče pro rozpoznávání formulářů Azure Portal. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled nástroje pro rozpoznávání formulářů Azure Portal.|
| **{COMPUTER_VISION_API_KEY}** | Klíč je k dispozici na stránce Azure Portal rozhraní API pro počítačové zpracování obrazu klíče.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Koncový bod fakturace. Pokud používáte cloudový Počítačové zpracování obrazu prostředek, hodnota identifikátoru URI je k dispozici na stránce s přehledem Azure Portal rozhraní API pro počítačové zpracování obrazu. Pokud používáte textový kontejner pro rozpoznávání *služeb* , použijte adresu URL fakturačního koncového bodu, která se předává kontejneru v `docker run` příkazu. |

Podrobnosti o tom, jak tyto hodnoty získat, najdete v článku [shromažďování požadovaných parametrů](form-recognizer-container-howto.md#gathering-required-parameters) .

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Chcete-li spustit kontejner, zadejte `Eula` `Billing` Možnosti, a, `ApiKey` jinak se kontejner nespustí. Další informace najdete v tématu [fakturace](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Příklady Docker kontejneru pro rozpoznávání formulářů

Následující příklady Docker jsou pro kontejner pro rozpoznávání formulářů.

### <a name="basic-example-for-form-recognizer"></a>Základní příklad pro nástroj pro rozpoznávání formulářů

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Příklad protokolování pro rozpoznávání formulářů

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [instalace a spuštění kontejnerů](form-recognizer-container-howto.md).
