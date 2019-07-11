---
title: Konfigurace kontejneru pro nástroj pro rozpoznávání formuláře
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak konfigurovat kontejner formuláře Rozlišovač analyzovat data formuláře a tabulek.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7752b09dd1bf20d796b19d03e62426b098486c39
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718448"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurace formuláře Rozlišovač kontejnery

Pomocí kontejnerů Rozlišovač formulář Azure můžete vytvořit aplikaci architekturu, která je optimalizováno pro využití možnosti robustní Cloudová a hraniční umístění.

Můžete nakonfigurovat pomocí běhového prostředí modulu pro rozpoznávání formuláře kontejneru `docker run` argumenty příkazu. Tento kontejner víme o několika požadované nastavení a volitelné několik nastavení. Pár příkladů, najdete v článku ["Příklad docker run příkazy"](#example-docker-run-commands) oddílu. Nastavení kontejneru konkrétní jsou fakturace.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně. Je nutné zadat platné hodnoty pro všechny tři nastavení. v opačném případě se nespustí vašeho kontejneru. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředků Azure, který se používá ke sledování fakturačních údajů pro kontejner. Hodnota ApiKey musí být platný klíč pro _formuláře Rozlišovač_ prostředek, který je zadán pro `Billing` v části "Billing (fakturace nastavení konfigurace).

Můžete najít tato nastavení na webu Azure Portal, v **správy prostředků modulu pro rozpoznávání formuláře**v části **klíče**.

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

`Billing` Nastavení, určuje identifikátor URI koncového bodu z _formuláře Rozlišovač_ prostředků v Azure, který umožňuje měřit fakturačních údajů pro kontejner. Hodnota pro toto nastavení konfigurace musí být platný identifikátor URI koncového bodu pro _formuláře Rozlišovač_ prostředků v Azure. Sestavy využití kontejnerů o každých 10 až 15 minut.

Můžete najít tato nastavení na webu Azure Portal, v **formuláře Rozlišovač přehled**v části **koncový bod**.

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

Použití vazby připojí ke čtení a zápisu dat do a z kontejneru. Můžete určit vstupní připojení nebo připojení výstupu tak, že zadáte `--mount` možnost [ `docker run` příkaz](https://docs.docker.com/engine/reference/commandline/run/).

Kontejner modulu pro rozpoznávání formuláře vyžaduje vstupní připojení a připojení výstupu. Vstupní připojení může být jen pro čtení, a to nutné pro přístup k datům, který se používá pro trénování a vyhodnocování. Výstup připojení musí být zapisovatelný a používat k ukládání dočasných dat a modely.

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho připojení umístění [hostitelský počítač](form-recognizer-container-howto.md#the-host-computer) nemusí být dostupný z důvodu konfliktu mezi oprávnění účtu služby Docker a oprávnění umístění připojení hostitele.

|volitelná,| Name | Typ dat | Popis |
|-------|------|-----------|-------------|
|Požadováno| `Input` | Řetězec | Cíl vstupní připojení. Výchozí hodnota je `/input`.    <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Požadováno| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`.  <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy. Když je spuštěn, kontejner zůstane spuštěný dokud [ji zastavit](form-recognizer-container-howto.md#stop-the-container).

* **Znak pro pokračování řádku**: Příkazy Dockeru v následujících částech použijte zpětné lomítko (\\) jako znak pro pokračování řádku. Nahraďte nebo odstraňte tento znak, v závislosti na požadavcích vaší operačního systému hostitele.
* **Pořadí argumentů**: Neměnit pořadí argumentů, pokud jste obeznámeni s kontejnery Dockeru.

Nahradit {_argument_name_} v tabulce následujících vlastními hodnotami:

| Zástupný symbol | Value |
|-------------|-------|
|{BILLING_KEY} | Klíč, který se používá ke spuštění kontejneru. Je k dispozici na webu Azure portal stránky formuláře rozpoznávání klíče.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na portálu Azure portal, stránka s přehledem Rozlišovač formuláře.|
|{COMPUTER_VISION_API_KEY}| Klíč je k dispozici na webu Azure portal stránku klíče rozhraní API pro zpracování obrazu počítačů.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturační koncový bod. Pokud používáte cloudové prostředky pro počítačové zpracování obrazu, je k dispozici na portálu Azure portal, stránka s přehledem rozhraní API pro zpracování obrazu počítače hodnotu identifikátoru URI. Pokud používáte *cognitive services – rozpoznat text* kontejneru, použijte fakturační adresu URL koncového bodu, který je předán do kontejneru v `docker run` příkazu.|

> [!IMPORTANT]
> Pro spuštění kontejneru, zadejte `Eula`, `Billing`, a `ApiKey` možnosti; v opačném případě nebude spuštění kontejneru. Další informace najdete v tématu [fakturace](#billing-configuration-setting).

> [!NOTE] 
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

* Kontrola [instalace a spuštění kontejnerů](form-recognizer-container-howto.md).
