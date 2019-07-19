---
title: Postup konfigurace kontejneru pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Naučte se konfigurovat kontejner pro rozpoznávání formulářů k analýze dat formuláře a tabulky.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 4a490e8a9f111985df9c9e8c9f73bc36d686cc2a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348703"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurovat kontejnery pro rozpoznávání formulářů

Pomocí kontejnerů pro rozpoznávání formulářů Azure můžete vytvořit architekturu aplikace, která je optimalizovaná tak, aby využila výhod robustních cloudových funkcí i možností Edge.

Pomocí `docker run` argumentů příkazu můžete nakonfigurovat prostředí runtime kontejneru rozpoznávání formuláře. Tento kontejner má několik požadovaných nastavení a několik volitelných nastavení. Několik příkladů najdete v části ["Ukázkové příkazy pro spuštění Docker"](#example-docker-run-commands) . Nastavení fakturace jsou specifická pro kontejner.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) [a`Eula`](#eula-setting) se používají společně. Je nutné zadat platné hodnoty pro všechna tři nastavení; v opačném případě se Váš kontejner nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Hodnota parametru ApiKey musí být platným klíčem pro prostředek pro _rozpoznávání formulářů_ , který je zadaný pro `Billing` v části nastavení konfigurace fakturace.

Toto nastavení můžete najít v Azure Portal v části **Správa prostředků nástroje pro rozpoznávání formulářů**v části **klíče**.

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

Nastavení určuje identifikátor URI koncového bodu prostředku nástroje _pro rozpoznávání formulářů_ v Azure, který se používá k měření informací o fakturaci pro kontejner. `Billing` Hodnota pro toto nastavení konfigurace musí být platný identifikátor URI koncového bodu pro prostředek pro _rozpoznávání formulářů_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete v Azure Portal v části **Přehled rozpoznávání formulářů**v části **koncový bod**.

|Požadováno| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení Logging

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Nastavení Mounts

Použití vazby připojí ke čtení a zápisu dat do a z kontejneru. Zadáním `--mount` možnosti [ `docker run` v příkazu](https://docs.docker.com/engine/reference/commandline/run/)můžete zadat vstupní připojení nebo připojení pro výstup.

Kontejner pro rozpoznávání formulářů vyžaduje vstupní připojení a výstupní připojení. Vstupní připojení může být jen pro čtení a vyžaduje se pro přístup k datům, která se používají pro školení a bodování. Ke výstupnímu připojení je potřeba zapisovat a použít ho k uložení modelů a dočasných dat.

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho je možné, že umístění připojení [hostitelského počítače](form-recognizer-container-howto.md#the-host-computer) není k dispozici z důvodu konfliktu mezi oprávněními účtu Docker Service a oprávněními k umístění hostitele připojení.

|volitelná,| Name | Typ dat | Popis |
|-------|------|-----------|-------------|
|Požadováno| `Input` | Řetězec | Cíl vstupní připojení. Výchozí hodnota je `/input`.    <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Požadováno| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`.  <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy. Když je spuštěný, kontejner zůstane spuštěný, dokud [ho](form-recognizer-container-howto.md#stop-the-container)nezastavíte.

* **Znak pro pokračování řádku**: Příkazy Docker v následujících částech používají zpětné lomítko (\\) jako znak pro pokračování řádku. Nahraďte nebo odeberte tento znak v závislosti na požadavcích vašeho hostitelského operačního systému.
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery Docker.

Nahraďte {_argument_name_} v následující tabulce vlastními hodnotami:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | Klíč, který se používá ke spuštění kontejneru. Je k dispozici na stránce klíče pro rozpoznávání formulářů Azure Portal.  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled nástroje pro rozpoznávání formulářů Azure Portal.|
|{COMPUTER_VISION_API_KEY}| Klíč je k dispozici na stránce Azure Portal rozhraní API pro počítačové zpracování obrazu klíče.|
|{COMPUTER_VISION_ENDPOINT_URI}|Koncový bod fakturace. Pokud používáte cloudový Počítačové zpracování obrazu prostředek, hodnota identifikátoru URI je k dispozici na stránce s přehledem Azure Portal rozhraní API pro počítačové zpracování obrazu. Pokud používáte textový kontejner pro rozpoznávání *služeb* , použijte adresu URL fakturačního koncového bodu, která se předává kontejneru v `docker run` příkazu.|

> [!IMPORTANT]
> Chcete-li spustit kontejner, zadejte `Eula`možnosti `Billing`, a `ApiKey` , jinak se kontejner nespustí. Další informace najdete v tématu [fakturace](#billing-configuration-setting).

> [!NOTE] 
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků služby pro rozpoznávání formulářů Azure.

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

## <a name="next-steps"></a>Další postup

* Přečtěte si téma [instalace a spuštění kontejnerů](form-recognizer-container-howto.md).
