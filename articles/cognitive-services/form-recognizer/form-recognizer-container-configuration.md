---
title: Konfigurace kontejneru – nástroj pro rozpoznávání formuláře
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak konfigurovat kontejner formuláře Rozlišovač analyzovat data formuláře a tabulek.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 28acc2d1eafacb9e53fac3e3cce092738401f838
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475382"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurace formuláře Rozlišovač kontejnery

Formulář pro rozpoznávání kontejnery Umožněte zákazníkům vytvářet aplikace architekturu, která je optimalizováno pro využití možnosti robustní Cloudová a hraniční umístění.

**Formuláře Rozlišovač** kontejneru běhové prostředí je nakonfigurovaný nástrojem `docker run` argumenty příkazového. Tento kontejner má několik požadovaná nastavení, společně s pár volitelná nastavení. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení kontejneru konkrétní jsou fakturace.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro _formuláře Rozlišovač_ prostředek určený pro [ `Billing` ](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete v následujícím místě:

* Azure portal: **Formulář pro rozpoznávání** správy prostředků v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

`Billing` Nastavení, určuje identifikátor URI koncového bodu z _formuláře Rozlišovač_ prostředků v Azure umožňuje měřit fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být platný identifikátor URI koncového bodu pro _formuláře Rozlišovač_ prostředků v Azure. Sestavy využití kontejnerů o každých 10 až 15 minut.

Toto nastavení najdete v následujícím místě:

* Azure portal: **Formulář pro rozpoznávání** přehled s popiskem `Endpoint`

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

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

Kontejner modulu pro rozpoznávání formuláře vyžaduje vstupní a výstupní připojení. Vstupní připojení může být jen pro čtení a je potřeba přístup k datům, který se použije pro trénování a vyhodnocování. Výstup připojení musí být zapisovatelný a se použije k ukládání dočasných dat a modely.

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](form-recognizer-container-howto.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby Docker a hostiteli připojit umístění oprávnění.

|Nepovinné| Name | Typ dat | Popis |
|-------|------|-----------|-------------|
|Požaduje se| `Input` | String | Cíl vstupní připojení. Výchozí hodnota je `/input`.    <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Požaduje se| `Output` | String | Cíl připojení výstupu. Výchozí hodnota je `/output`.  <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](form-recognizer-container-howto.md#stop-the-container) ho.

* **Znak pro pokračování řádku**: Příkazy Dockeru v následujících částech použijte zpětné lomítko `\`, jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému.
* **Pořadí argumentů**: Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery Dockeru.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče modulu pro rozpoznávání formuláře webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem Rozlišovač formuláře webu Azure portal.|
|{COMPUTER_VISION_API_KEY}| Klíč je k dispozici na stránce klíče rozhraní API pro zpracování obrazu počítačů webu Azure portal.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturační koncový bod. Pokud používáte cloudové prostředky pro počítačové zpracování obrazu, hodnotu identifikátoru URI je k dispozici na stránce Přehled rozhraní API pro zpracování obrazu počítače webu Azure portal. Pokud používáte `cognitive-services-recognize-text` kontejner, použijte fakturační adresu URL koncového bodu předaný do kontejneru v `docker run` příkazu.|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing-configuration-setting).
> Hodnota ApiKey **klíč** ze stránky klíče na prostředek Azure formuláře Rozlišovače.

## <a name="form-recognizer-container-docker-examples"></a>Formulář příklady Rozlišovač kontejneru Dockeru

Následující příklady Docker jsou pro kontejner Rozlišovač formuláře.

### <a name="basic-example-for-form-recognizer"></a>Základní příklad pro nástroj pro rozpoznávání formuláře

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Příklad protokolování pro nástroj pro rozpoznávání formuláře

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](form-recognizer-container-howto.md)
