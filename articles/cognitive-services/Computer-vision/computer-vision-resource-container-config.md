---
title: Konfigurace kontejnerů – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak nakonfigurovat požadovaná i volitelná nastavení pro kontejnery Rozpoznávání textu v Počítačové zpracování obrazu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718987"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurace kontejnerů Docker Počítačové zpracování obrazu

Pomocí argumentů příkazu `docker run` nakonfigurujete běhové prostředí kontejneru Počítačové zpracování obrazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení fakturace jsou specifická pro kontejner. 

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Nastavení `ApiKey` Určuje klíč prostředku Azure `Cognitive Services`, který se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platným klíčem pro prostředek _Cognitive Services_ zadaný pro nastavení [`Billing`](#billing-configuration-setting) konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Správa prostředků **Cognitive Services** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

Nastavení `Billing` Určuje identifikátor URI koncového bodu prostředku _Cognitive Services_ v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _Cognitive Services_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: **Cognitive Services** Overview, označený `Endpoint`

Nezapomeňte přidat směrování `vision/v1.0` k identifikátoru URI koncového bodu, jak je znázorněno v následující tabulce. 

|Požaduje se| Název | Data type | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení Logging
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení Mounts

Použití vazby připojí ke čtení a zápisu dat do a z kontejneru. Můžete určit vstupní připojení nebo připojení výstup tak, že zadáte `--mount` možnost [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu.

Kontejnery Počítačové zpracování obrazu nepoužívají k ukládání dat školení nebo služby vstupní ani výstupní připojení. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](computer-vision-how-to-install-containers.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|Nepovinné| Název | Data type | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery Počítačové zpracování obrazu nepoužívají.|
|Nepovinné| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](computer-vision-how-to-install-containers.md#stop-the-container) ho.

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu prostředku `Computer Vision` na stránce klíčů Azure `Computer Vision`. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na stránce Přehled služby Azure `Computer Vision`.| Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](computer-vision-how-to-install-containers.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](computer-vision-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure `Cognitive Services`.

## <a name="container-docker-examples"></a>Příklady Docker kontejneru

Následující příklady Docker jsou určené pro kontejner pro čtení.

### <a name="basic-example"></a>Základní příklad

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak nainstalovat a spustit kontejnery](computer-vision-how-to-install-containers.md).
