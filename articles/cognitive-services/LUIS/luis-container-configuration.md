---
title: Nastavení kontejneru dockeru
titleSuffix: Language Understanding - Azure Cognitive Services
description: Běhové prostředí kontejneru LUIS je nakonfigurovaný nástrojem `docker run` argumenty příkazu. Služba LUIS má několik požadovaná nastavení, společně s pár volitelná nastavení.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 7858d94b6e2a9ef07da9121cb84ffaf6adaa24d3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360533"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurace kontejnerů Docker Language Understanding 

Prostředí modulu runtime kontejneru **Language Understanding** (Luis) je nakonfigurováno pomocí `docker run` argumentů příkazu. Služba LUIS má několik požadovaná nastavení, společně s pár volitelná nastavení. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení pro konkrétní kontejner se vstup [nastavení připojování](#mount-settings) a nastavení fakturace. 

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Billing](#billing-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|[Http Proxy](#http-proxy-credentials-settings)|Nakonfigurujte proxy server HTTP pro vytváření odchozích požadavků.|
|Ne|[Logging](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|Ano|[Mounts](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpět do hostitelského počítače.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Nastavení ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Cognitive Services_ zadaný pro [`Billing`](#billing-setting) nastavení konfigurace.

Toto nastavení najdete v následujících umístěních:

* Azure Portal: **Cognitive Services** Správa prostředků, v části **klíče**
* Portál LUIS: Stránky **nastavení klíčů a koncových bodů** . 

Nepoužívejte starter klíč nebo klíč pro vytváření obsahu. 

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Nastavení Billing

Nastavení určuje identifikátor URI koncového bodu Cognitive Services prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner.  `Billing` Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _Cognitive Services_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete v následujících umístěních:

* Azure Portal: **Cognitive Services** Přehled, označený`Endpoint`
* Portál LUIS: Stránky **nastavení klíčů a koncových bodů** jako součást identifikátoru URI koncového bodu.

Nezapomeňte zahrnout `luis/v2.0` směrování do adresy URL, jak je znázorněno v následující tabulce:


|Požadováno| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

Kontejner LUIS nepoužívá vstup nebo výstup připojí k uložení školení nebo dat služby. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](luis-container-howto.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby docker a hostiteli připojit umístění oprávnění. 

Následující tabulka popisuje nastavení podporováno.

|Požaduje se| Název | Typ dat | Popis |
|-------|------|-----------|-------------|
|Ano| `Input` | Řetězec | Cíl vstupní připojení. Výchozí hodnota je `/input`. Toto je umístění souborů balíčku LUIS. <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Ne| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje LUIS dotazu protokoly a protokoly kontejneru. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](luis-container-howto.md#stop-the-container) ho.

* Tyto příklady používají adresář mimo `c:` jednotku, aby nedocházelo ke konfliktům oprávnění ve Windows. Pokud je potřeba použít konkrétní adresář jako vstupní adresář, budete muset udělit dockeru služby oprávnění. 
* Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.
* Pokud používáte jiný operační systém, použijte pro připojení správnou konzolu, terminál, syntaxi složky a znak pro pokračování řádku pro svůj systém. Tyto příklady předpokládají konzolu Windows se znakem `^`pro pokračování řádku. Vzhledem k tomu, že kontejner je operačním systémem Linux, cílový připojení používá syntaxi složky ve stylu systému Linux.

Nezapomeňte zahrnout `luis/v2.0` směrování do adresy URL, jak je znázorněno v následující tabulce.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
|{API_KEY} | Klíč koncového bodu trénovaného aplikace LUIS. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URL} | Hodnota fakturačního koncového bodu je k dispozici na stránce Přehled Azure `Cognitive Services` . |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](luis-container-howto.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče a koncové body na portálu Luis a je k dispozici také na stránce klíče prostředků `Cognitive Services` Azure. 

### <a name="basic-example"></a>Základní příklad

V následujícím příkladu má nejmíň argumenty umožňuje spuštění kontejneru:

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

Následující příklad nastaví ApplicationInsights argument k odesílání telemetrie do Application Insights, když je kontejner spuštěný:

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

Následující příkaz nastavuje úroveň protokolování `Logging:Console:LogLevel`, konfigurace úrovně protokolování [ `Information` ](https://msdn.microsoft.com). 

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

## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](luis-container-howto.md)
* Problémy související s LUIS funkcemi najdete v tématu [řešení potíží](troubleshooting.md) .
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
