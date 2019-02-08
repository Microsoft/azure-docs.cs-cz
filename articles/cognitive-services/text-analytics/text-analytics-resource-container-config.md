---
title: Konfigurace kontejnerů
titlesuffix: Text Analytics - Azure Cognitive Services
description: Rozhraní text Analytics poskytuje každý kontejner se společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: e390b3fbaca23dd60d1ab7553a2d69cfb9b4897c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878251"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurace kontejnery dockeru pro analýzu textu

Rozhraní text Analytics poskytuje každý kontejner se společnou architekturu konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrická data a nastavení zabezpečení pro vaše kontejnery.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro _rozhraní Text Analytics_ prostředek určený pro [ `Billing` ](#billing-setting) nastavení konfigurace.

Toto nastavení najdete v následujícím místě:

* Azure portal: **Analýza textu** správy prostředků v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

`Billing` Nastavení, určuje identifikátor URI koncového bodu z _rozhraní Text Analytics_ prostředků v Azure umožňuje měřit fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být platný koncový bod identifikátoru URI pro výraz __rozhraní Text Analytics_ prostředků v Azure.

Toto nastavení najdete v následujícím místě:

* Azure portal: **Analýza textu** přehled s popiskem `Endpoint`

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0` |

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

Kontejnery pro analýzu textu nepoužívejte vstup nebo výstup připojí k uložení školení nebo dat služby. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby docker a hostiteli připojit umístění oprávnění. 

|Nepovinné| Název | Typ dat | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | String | Text Analytics kontejnery Nepoužívejte toto.|
|Nepovinné| `Output` | String | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejneru. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Hierarchické nastavení

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad 

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) ho.

* **Znak pro pokračování řádku**: Příkazy dockeru v následujících částech použijte zpětné lomítko `\`, jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
|{BILLING_KEY} | Klíč koncového bodu k dispozici na webu Azure portal Text Analytics klíče stránce prostředku pro analýzu textu. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Fakturační hodnota koncového bodu je k dispozici na stránce s přehledem Text Analytics Azure portal.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](how-tos/text-analytics-how-to-install-containers.md#billing).
> Hodnota ApiKey **klíč** ze stránky klíče na prostředek Azure Text Analytics. 

## <a name="keyphrase-extraction-container-docker-examples"></a>Keyphrase extrakce kontejneru dockeru příklady

Následující příklady docker jsou pro kontejner keyphrase extrakce. 

### <a name="basic-example"></a>Základní příklad 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Příklad protokolování s argumenty příkazového řádku

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Příklad protokolování se proměnná prostředí

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase  Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY}
  ```

## <a name="language-detection-container-docker-examples"></a>Příklady docker kontejneru detekce jazyka

Následující příklady docker jsou pro kontejner detekce jazyka. 

### <a name="basic-example"></a>Základní příklad

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-command-line-arguments"></a>Příklad protokolování s argumenty příkazového řádku

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Příklad protokolování se proměnná prostředí

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language  Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY}
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Příklady docker kontejneru analýzy mínění

Následující příklady docker jsou pro kontejner analýzy mínění. 

### <a name="basic-example"></a>Základní příklad

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-command-line-arguments"></a>Příklad protokolování s argumenty příkazového řádku

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Příklad protokolování se proměnná prostředí

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](how-tos/text-analytics-how-to-install-containers.md)
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)
