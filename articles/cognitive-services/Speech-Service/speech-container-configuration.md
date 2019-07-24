---
title: Konfigurace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Kontejner řeči
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 8a8b0e18c1db7a2e2fc08819aa2f2d64d650ded6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321358"
---
# <a name="configure-speech-service-containers"></a>Konfigurace kontejnerů služby Speech

Díky kontejnerům pro rozpoznávání řeči můžou zákazníci vytvářet jednu architekturu aplikace pro rozpoznávání řeči, která je optimalizovaná tak, aby využila výhod robustních cloudových funkcí i možností Edge. K dvěma kontejnerům pro rozpoznávání řeči teď podporujeme **Převod řeči na text** a **Převod textu na řeč**. 

Prostředí modulu runtime kontejneru **řeči** se konfiguruje pomocí `docker run` argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení fakturace jsou specifická pro kontejner. 

# <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platným klíčem pro prostředek _řeči_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: **Řeč** Správa prostředků, v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

Nastavení určuje identifikátor URI koncového bodu prostředku řeči v Azure, který se používá pro informace o fakturaci pro daný kontejner.  `Billing` Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _řeči_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: **Řeč** Přehled, označený`Endpoint`

|Požadováno| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

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

Kontejnery řeči nepoužívají vstupní ani výstupní připojení k ukládání dat o školeních nebo službách. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](speech-container-howto.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby docker a hostiteli připojit umístění oprávnění. 

|volitelná,| Name | Typ dat | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery řeči toto nepoužívají.|
|volitelná,| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad 

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](speech-container-howto.md#stop-the-container) ho.

* **Znak pro pokračování řádku**: Příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
|{API_KEY} | Klíč rozhraní API prostředku řeči |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Hodnota koncového bodu, včetně oblasti.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing-configuration-setting).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure Speech. 

## <a name="speech-container-docker-examples"></a>Příklady Docker kontejneru řeči

Následující příklady Docker jsou pro kontejner řeči. 

### <a name="basic-example-for-speech-to-text"></a>Základní příklad pro převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="basic-example-for-text-to-speech"></a>Základní příklad pro převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Příklad protokolování pro převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Příklad protokolování pro převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](speech-container-howto.md)