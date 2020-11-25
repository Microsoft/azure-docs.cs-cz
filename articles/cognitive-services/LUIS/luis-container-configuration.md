---
title: Nastavení kontejneru Docker – LUIS
titleSuffix: Azure Cognitive Services
description: Běhové prostředí kontejneru LUIS se konfiguruje pomocí `docker run` argumentů příkazu. LUIS má několik požadovaných nastavení spolu s několika volitelnými nastaveními.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001144"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurace kontejnerů Docker Language Understanding 

Prostředí modulu runtime kontejneru **Language Understanding** (Luis) je nakonfigurováno pomocí `docker run` argumentů příkazu. LUIS má několik požadovaných nastavení spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení specifická pro daný kontejner představují vstupní [nastavení připojení](#mount-settings) a nastavení fakturace. 

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení konfigurace:

|Vyžadováno|Nastavení|Účel|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Používá se ke sledování fakturačních informací.|
|No|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](/azure/application-insights) .|
|Yes|[Fakturace](#billing-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Yes|[Konkrétní](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|No|[Fluent](#fluentd-settings)|Protokol zápisu a volitelně data metriky na server se systémem.|
|No|[Proxy server http](#http-proxy-credentials-settings)|Nakonfigurujte proxy server HTTP pro vytváření odchozích požadavků.|
|No|[Protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|Yes|[Připojí](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpátky do hostitelského počítače.|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)Nastavení, [`Billing`](#billing-setting) a [`Eula`](#eula-setting) se používají společně a musíte zadat platné hodnoty pro všechny tři z nich. v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Nastavení ApiKey

`ApiKey`Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Cognitive Services_ zadaný pro [`Billing`](#billing-setting) nastavení konfigurace.

Toto nastavení najdete v následujících umístěních:

* Azure Portal: Správa prostředků **Cognitive Services** v části **klíče**
* Portál LUIS: Stránka **nastavení klíčů a koncového bodu** . 

Nepoužívejte počáteční klíč ani klíč pro vytváření. 

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Nastavení fakturace

`Billing`Nastavení určuje identifikátor URI koncového bodu _Cognitive Services_ prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _Cognitive Services_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete v následujících umístěních:

* Azure Portal: Přehled **Cognitive Services** , označený `Endpoint`
* Portál LUIS: **klíčová stránka a nastavení koncového bodu** jako součást identifikátoru URI koncového bodu.

| Vyžadováno | Name | Datový typ | Popis |
|----------|------|-----------|-------------|
| Ano      | `Billing` | řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](luis-container-howto.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním `--mount` Možnosti v příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat vstupní připojení nebo připojení pro výstup. 

Kontejner LUIS nepoužívá k ukládání dat školení nebo služby vstupní ani výstupní připojení. 

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](luis-container-howto.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

V následující tabulce jsou popsána podporovaná nastavení.

|Vyžadováno| Name | Datový typ | Popis |
|-------|------|-----------|-------------|
|Ano| `Input` | Řetězec | Cíl vstupního připojení Výchozí hodnota je `/input`. Toto je umístění souborů balíčku LUIS. <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|No| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly dotazů LUIS a protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](luis-container-howto.md#stop-the-container) .

* Tyto příklady používají adresář mimo `C:` jednotku, aby nedocházelo ke konfliktům oprávnění ve Windows. Pokud potřebujete použít konkrétní adresář jako vstupní adresář, bude pravděpodobně nutné udělit oprávnění služby Docker. 
* Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.
* Pokud používáte jiný operační systém, použijte pro připojení správnou konzolu, terminál, syntaxi složky a znak pro pokračování řádku pro svůj systém. Tyto příklady předpokládají konzolu Windows se znakem pro pokračování řádku `^` . Vzhledem k tomu, že kontejner je operačním systémem Linux, cílový připojení používá syntaxi složky ve stylu systému Linux.

Nahradit {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `LUIS` prostředku na `LUIS` stránce klíčů Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na `LUIS` stránce Přehled Azure.| Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](luis-container-howto.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí. Další informace najdete v tématu [fakturace](luis-container-howto.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče a koncové body na portálu Luis a je k dispozici také na `Cognitive Services` stránce klíče prostředků Azure. 

### <a name="basic-example"></a>Základní příklad

Následující příklad má nejnižší možné argumenty ke spuštění kontejneru:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Příklad ApplicationInsights

Následující příklad nastaví argument ApplicationInsights k odeslání telemetrie do Application Insights, když je kontejner spuštěný:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Příklad protokolování 

Následující příkaz nastaví úroveň protokolování, aby bylo `Logging:Console:LogLevel` možné konfigurovat úroveň protokolování na [`Information`](https://msdn.microsoft.com) . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak nainstalovat a spustit kontejnery](luis-container-howto.md) .
* Problémy související s LUIS funkcemi najdete v tématu [řešení potíží](troubleshooting.md) .
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)