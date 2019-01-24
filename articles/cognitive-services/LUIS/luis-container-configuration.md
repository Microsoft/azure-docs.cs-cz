---
title: Nastavení kontejneru dockeru
titleSuffix: Language Understanding - Azure Cognitive Services
description: Běhové prostředí kontejneru LUIS je nakonfigurovaný nástrojem `docker run` argumenty příkazu. Služba LUIS má několik požadovaná nastavení, společně s pár volitelná nastavení.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 31d6725b6e02bbc583ad80f235360574941a97d3
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468331"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurace kontejnery dockeru Language Understanding 

Běhové prostředí kontejneru Language Understanding (LUIS) je nakonfigurovaný nástrojem `docker run` argumenty příkazu. Služba LUIS má několik požadovaná nastavení, společně s pár volitelná nastavení. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení pro konkrétní kontejner se vstup [nastavení připojování](#mount-settings) a nastavení fakturace. 

Nastavení kontejneru jsou [hierarchické](#hierarchical-settings) a lze ji nastavit s [proměnné prostředí](#environment-variable-settings) nebo docker [argumenty příkazového řádku](#command-line-argument-settings).

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Billing](#billing-setting)|Určuje identifikátor URI prostředku služby koncového bodu v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenční pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|[Http Proxy](#http-proxy-credentials-settings)|Konfigurace proxy serveru HTTP pro provedení odchozích požadavků.|
|Ne|[Logging](#logging-settings)|Poskytuje podporu pro váš kontejner protokolování ASP.NET Core. |
|Ano|[Mounts](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru však zpět na hostitelském počítači.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Nastavení ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro _Language Understanding_ prostředek určený pro [ `Billing` ](#billing-setting) nastavení konfigurace.

Toto nastavení najdete v následujících umístěních:

* Azure portal: **Language Understanding** správy prostředků v části **klíče**
* Služba LUIS portálu: **Nastavení koncového bodu a klíče** stránky. 

Nepoužívejte starter klíč nebo klíč pro vytváření obsahu. 

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Nastavení Billing

`Billing` Nastavení, určuje identifikátor URI koncového bodu z _Language Understanding_ prostředků v Azure umožňuje měřit fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být platný identifikátor URI koncového bodu pro _Language Understanding_ prostředků v Azure.

Toto nastavení najdete v následujících umístěních:

* Azure portal: **Language Understanding** přehled s popiskem `Endpoint`
* Služba LUIS portálu: **Nastavení koncového bodu a klíče** stránce jako součást identifikátor URI koncového bodu.

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula nastavení

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

## <a name="hierarchical-settings"></a>Hierarchické nastavení

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](luis-container-howto.md#stop-the-container) ho.


* **Znak pro pokračování řádku**: Příkazy dockeru v následujících částech použijte zpětné lomítko `\`, jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
|{ENDPOINT_KEY} | Klíč koncového bodu trénovaného aplikace LUIS. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Fakturační hodnota koncového bodu je k dispozici na stránce s přehledem Language Understanding webu Azure portal.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](luis-container-howto.md#billing).
> Hodnota ApiKey **klíč** z klíče a koncových bodů stránky na portálu služby LUIS a je také k dispozici na na stránce klíče Azure Language Understanding prostředků. 

### <a name="basic-example"></a>Základní příklad

V následujícím příkladu má nejmíň argumenty umožňuje spuštění kontejneru:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Ve výstupu předchozího příkazu využívá adresáři `c:` disku, aby se zabránilo konfliktům oprávnění na Windows. Pokud je potřeba použít konkrétní adresář jako vstupní adresář, budete muset udělit dockeru služby oprávnění. Ve výstupu předchozího příkazu docker používá zpětné lomítko `\`, jako znak pro pokračování řádku. Nahraďte nebo odstraňte to na základě vašich [hostitelský počítač](luis-container-howto.md#the-host-computer) požadavky na operační systém. Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.


### <a name="applicationinsights-example"></a>Příklad ApplicationInsights

Následující příklad nastaví ApplicationInsights argument k odesílání telemetrie do Application Insights, když je kontejner spuštěný:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Příklad protokolování s argumenty příkazového řádku

Následující příkaz nastavuje úroveň protokolování `Logging:Console:LogLevel`, konfigurace úrovně protokolování [ `Information` ](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Příklad protokolování se proměnná prostředí

Následující příkazy použijte proměnnou prostředí s názvem `Logging:Console:LogLevel` konfigurace úrovně protokolování [ `Information` ](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](luis-container-howto.md)
* Odkazovat na [– nejčastější dotazy (FAQ)](luis-resources-faq.md) k vyřešení problémů týkajících se služby LUIS funkce.
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)
