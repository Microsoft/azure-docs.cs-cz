---
title: Postup konfigurace kontejneru pro rozhraní API detektoru anomálií
titleSuffix: Azure Cognitive Services
description: Běhové prostředí kontejneru rozhraní API detektoru anomálií se konfiguruje pomocí `docker run` argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363710"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurace kontejnerů Detektoru anomálií

Běhové prostředí kontejneru **detektoru anomálií** se konfiguruje pomocí `docker run` argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení fakturace jsou specifická pro kontejner. 

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení konfigurace:

|Vyžadováno|Nastavení|Účel|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Používá se ke sledování fakturačních informací.|
|No|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](/azure/application-insights) .|
|Yes|[Fakturace](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Yes|[Konkrétní](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|No|[Fluent](#fluentd-settings)|Protokol zápisu a volitelně data metriky na server se systémem.|
|No|[Proxy server http](#http-proxy-credentials-settings)|Nakonfigurujte proxy server HTTP pro vytváření odchozích požadavků.|
|No|[Protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|No|[Připojí](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpátky do hostitelského počítače.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Nastavení, [`Billing`](#billing-configuration-setting) a [`Eula`](#eula-setting) se používají společně a musíte zadat platné hodnoty pro všechny tři z nich. v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey`Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platným klíčem pro prostředek _detektoru anomálií_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Správa prostředků **detektoru anomálií** , v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

`Billing`Nastavení určuje identifikátor URI koncového bodu prostředku _detektoru anomálií_ v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _detektoru anomálií_ v Azure.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Přehled **detektoru anomálií** , označený `Endpoint`

|Vyžadováno| Name | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](anomaly-detector-container-howto.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním `--mount` Možnosti v příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat vstupní připojení nebo připojení pro výstup.

Kontejnery detektoru anomálií nepoužívají vstupní ani výstupní připojení k ukládání dat o školeních nebo službách. 

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](anomaly-detector-container-howto.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|Volitelné| Name | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery detektoru anomálií toto nepoužívají.|
|Volitelné| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker 

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](anomaly-detector-container-howto.md#stop-the-container) .

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\` jako znak pro pokračování řádku pro prostředí bash shell. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. Například znak pro pokračování řádku pro systém Windows je stříška, `^` . Nahraďte zpětné lomítko znakem stříšky. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Hodnotu v závorkách nahraďte `{}` vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Anomaly Detector` prostředku na `Anomaly Detector` stránce klíčů Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na `Anomaly Detector` stránce Přehled Azure.| Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](anomaly-detector-container-howto.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](anomaly-detector-container-howto.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků služby Azure anomálie detektor. 

## <a name="anomaly-detector-container-docker-examples"></a>Příklady Docker kontejneru detektoru anomálií

Následující příklady Docker jsou pro kontejner detektoru anomálií. 

### <a name="basic-example"></a>Základní příklad 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Příklad protokolování s argumenty příkazového řádku

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Další kroky

* [Nasazení kontejneru detektoru anomálií pro Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Další informace o službě rozhraní API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)