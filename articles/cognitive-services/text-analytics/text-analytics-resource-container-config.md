---
title: Konfigurace kontejnerů
titlesuffix: Text Analytics - Azure Cognitive Services
description: Rozhraní text Analytics poskytuje každý kontejner se společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: dapine
ms.openlocfilehash: 2a9b9ce5109315d940d6dcadf395489612faddec
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356933"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurace kontejnerů Docker Analýza textu

Rozhraní text Analytics poskytuje každý kontejner se společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Analýza textu_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Správa prostředků **Analýza textu** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

Nastavení určuje identifikátor URI koncového bodu analýza textu prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner.  `Billing` Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro objekt __Analýza textu_ prostředku v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: **Analýza textu** Přehled, označený`Endpoint`

|Požadováno| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Požadovaný identifikátor URI koncového bodu fakturace |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení Logging
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení Mounts

Použití vazby připojí ke čtení a zápisu dat do a z kontejneru. Můžete určit vstupní připojení nebo připojení výstup tak, že zadáte `--mount` možnost [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu.

Kontejnery Analýza textu nepoužívají k ukládání dat školení nebo služby vstupní ani výstupní připojení. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby docker a hostiteli připojit umístění oprávnění. 

|volitelná,| Name | Typ dat | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery Analýza textu nepoužívají.|
|volitelná,| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad 

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) ho.

* **Znak pro pokračování řádku**: Příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
|{API_KEY} | Klíč koncového bodu prostředku `Text Analytics` , který je k dispozici na stránce klíčů Azure. `Text Analytics` |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
|{ENDPOINT_URI} | Hodnota fakturačního koncového bodu je k dispozici na stránce Přehled Azure `Text Analytics` .|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure `Text Analytics` . 

## <a name="key-phrase-extraction-container-docker-examples"></a>Příklady Docker kontejneru pro extrakci klíčových frází

Následující příklady Docker jsou pro kontejner extrakce klíčových frází. 

### <a name="basic-example"></a>Základní příklad 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Příklady pro Docker kontejneru rozpoznávání jazyka

Následující příklady Docker jsou pro kontejner rozpoznávání jazyka. 

### <a name="basic-example"></a>Základní příklad

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Příklady mínění kontejneru analýzy

Následující příklady Docker jsou pro kontejner analýzy mínění. 

### <a name="basic-example"></a>Základní příklad

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](how-tos/text-analytics-how-to-install-containers.md)
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
