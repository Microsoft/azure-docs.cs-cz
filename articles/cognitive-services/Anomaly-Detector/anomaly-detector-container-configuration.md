---
title: Konfigurace kontejneru pro rozhraní API detektoru anomálií
titleSuffix: Azure Cognitive Services
description: Prostředí runtime kontejneru rozhraní API detektoru anomálií je konfigurováno pomocí argumentů příkazu. `docker run` Tento kontejner má několik požadovaných nastavení, spolu s několika volitelnými nastaveními.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f7e04a16fa35d492b8e5e6c53a05220e8b96a38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795854"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurace kontejnerů Detektoru anomálií

Prostředí runtime kontejneru **detektoru anomálií** je konfigurováno pomocí argumentů příkazu. `docker run` Tento kontejner má několik požadovaných nastavení, spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení specifická pro kontejner jsou nastavení fakturace. 

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení konfigurace:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-configuration-setting)|Slouží ke sledování fakturačních údajů.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do kontejneru telemetrická podporu [Azure Application Insights.](https://docs.microsoft.com/azure/application-insights)|
|Ano|[Fakturace](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Plynulý](#fluentd-settings)|Zápis protokolu a volitelně metrických dat na server Fluentd.|
|Ne|[Http Proxy](#http-proxy-credentials-settings)|Nakonfigurujte proxy http pro vytváření odchozích požadavků.|
|Ne|[protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET core pro váš kontejner. |
|Ne|[Koně](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpět do hostitelského počítače.|

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) a jsou používány společně a je nutné zadat platné hodnoty pro všechny tři z nich; jinak se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Toto `ApiKey` nastavení určuje klíč prostředků Azure, který slouží ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu apikey a hodnota musí být platný klíč pro [`Billing`](#billing-configuration-setting) prostředek _detektoru anomálií_ zadaný pro nastavení konfigurace.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Správa prostředků **detektoru anomálií** v části **Klíče**

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

Nastavení `Billing` určuje identifikátor URI koncového bodu prostředku _detektoru anomálií v_ Azure, který slouží k měření fakturačních informací pro kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný identifikátor URI koncového bodu pro prostředek _detektoru anomálií v_ Azure.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Přehled **detektoru anomálií** s označením`Endpoint`

|Požaduje se| Name (Název) | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace. Další informace o získání fakturačního identifikátoru URI naleznete v [tématu shromažďování požadovaných parametrů](anomaly-detector-container-howto.md#gathering-required-parameters). Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Kontejnery detektoranolií nepoužívají vstupní nebo výstupní připojení k ukládání dat školení nebo služby. 

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](anomaly-detector-container-howto.md#the-host-computer)navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněními umístění připojení hostitele. 

|Nepovinné| Name (Název) | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery detektoru anomálií nepoužívají toto.|
|Nepovinné| `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru 

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění kontejneru pokračuje v běhu, dokud jej [nezastavíte.](anomaly-detector-container-howto.md#stop-the-container)

* **Znak pokračování řádku**: Příkazy Dockeru v následujících `\`částech používají zpětné lomítko , jako znak pokračování řádku pro bash shell. Nazákladě požadavků hostitelského operačního systému jej nahraďte nebo odeberte. Například znak pokračování řádku pro okna je `^`stříška, . Nahraďte zadní lomítko stříškou. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste velmi obeznámeni s kontejnery Dockeru.

Nahraďte hodnotu `{}`v závorkách , vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Anomaly Detector` prostředku na `Anomaly Detector` stránce Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota koncového bodu fakturace `Anomaly Detector` je dostupná na stránce Přehled Azure.| Viz [shromažďování požadovaných parametrů](anomaly-detector-container-howto.md#gathering-required-parameters) pro explicitní příklady. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](anomaly-detector-container-howto.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure Anomaly Detector. 

## <a name="anomaly-detector-container-docker-examples"></a>Příklady dockeru kontejneru anomálií

Následující příklady Dockeru jsou pro kontejner detektoru anomálií. 

### <a name="basic-example"></a>Základní příklad 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Příklad protokolování s argumenty příkazového řádku

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Další kroky

* [Nasazení kontejneru detektoru anomálií do instancí kontejnerů Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Další informace o službě API detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
