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
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080261"
---
# <a name="configure-containers"></a>Konfigurace kontejnerů

Běhové prostředí kontejneru Language Understanding (LUIS) je nakonfigurovaný nástrojem `docker run` argumenty příkazu. Služba LUIS má několik požadovaná nastavení, společně s pár volitelná nastavení. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení pro konkrétní kontejner se vstup [nastavení připojování](#mount-settings) a nastavení fakturace. 

Nastavení kontejneru jsou [hierarchické](#hierarchical-settings) a lze ji nastavit s [proměnné prostředí](#environment-variable-settings) nebo docker [argumenty příkazového řádku](#command-line-argument-settings).

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Fakturace](#billing-setting)|Určuje identifikátor URI koncového bodu z _Language Understanding_ prostředků v Azure.|
|Ano|[Smlouva EULA](#eula-setting)| Označuje, že jste přijali licenční pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|[Protokolování](#logging-settings)|Poskytuje podporu pro váš kontejner protokolování ASP.NET Core. |
|Ano|[Připojí](#mount-settings)|Čtení a zápis dat z [hostitelský počítač](luis-container-howto.md#the-host-computer) do kontejneru a z kontejneru zpět na hostitelském počítači.|

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Nastavení ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Musíte zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro _Language Understanding_ prostředek určený pro [ `Billing` ](#billing-setting) nastavení konfigurace.

Toto nastavení můžete najít na dvou místech:

* Azure portal: **Language Understanding** správy prostředků v části **klíče**
* Služba LUIS portál: **klíče a koncových bodů nastavení** stránky. 

Nepoužívejte starter klíč nebo klíč pro vytváření obsahu. 

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

`ApplicationInsights` Nastavení slouží k přidání [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru. Služba Application Insights nabízí podrobné monitorování vašeho kontejneru. Umožňuje snadné monitorování vašeho kontejneru dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vašem kontejneru.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `ApplicationInsights` oddílu.

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ne| `InstrumentationKey` | Řetězec | Instrumentační klíč Application Insights instance, do jaké telemetrická data pro kontejner se odesílají. Další informace najdete v tématu [Application Insights pro ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Příklad:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Nastavení fakturace

`Billing` Nastavení, určuje identifikátor URI koncového bodu z _Language Understanding_ prostředků v Azure umožňuje měřit fakturačních údajů pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být platný identifikátor URI koncového bodu pro _Language Understanding_ prostředků v Azure.

Toto nastavení můžete najít na dvou místech:

* Azure portal: **Language Understanding** přehled s popiskem `Endpoint`
* Služba LUIS portál: **klíče a koncových bodů nastavení** stránce jako součást identifikátor URI koncového bodu.

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Smlouva EULA nastavení

`Eula` Nastavení znamená, že jste přijali licenční pro kontejner. Musíte zadat hodnotu pro toto nastavení konfigurace, a hodnota musí být nastavena na `accept`.

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Eula` | Řetězec | Přijetí licence<br><br>Příklad:<br>`Eula=accept` |

Kontejnery služby cognitive Services se licencují pod [vaší smlouvě](https://go.microsoft.com/fwlink/?linkid=2018657) řídící používání Azure. Pokud nemáte stávající smlouvy řídící používání Azure, souhlasíte s tím, že je vaší smlouvě upravující používání Azure [Microsoft Online Subscription Agreement](https://go.microsoft.com/fwlink/?linkid=2018755), která zahrnuje [podmínky Online služeb ](https://go.microsoft.com/fwlink/?linkid=2018760). Pro verze Preview, souhlasíte také s [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://go.microsoft.com/fwlink/?linkid=2018815). Pomocí kontejneru vyjadřujete souhlas s těmito podmínkami.

## <a name="fluentd-settings"></a>Nastavení Fluentd

Fluentd je open source kolekce pro jednotné přihlašování. `Fluentd` Nastavení spravovat připojení kontejneru [Fluentd](https://www.fluentd.org) serveru. Kontejner LUIS zahrnuje poskytovatele Fluentd protokolování, který umožňuje zapisovat protokoly kontejneru a volitelně data metriky Fluentd server.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `Fluentd` oddílu.

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Host` | Řetězec | IP adresa nebo název hostitele DNS Fluentd serveru. |
| `Port` | Integer | Port serveru Fluentd.<br/> Výchozí hodnota je 24224. |
| `HeartbeatMs` | Integer | Interval prezenčního signálu v milisekundách. Pokud žádný provoz událostí byl odeslán před vypršením platnosti tento interval, na Fluentd server přijde prezenční signál. Výchozí hodnota je 60000 milisekund (1 minuta). |
| `SendBufferSize` | Integer | Vyrovnávací paměť sítě v bajtů přidělených pro operace odeslání. Výchozí hodnota je 32768 bajtů (32 kilobajtů). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Časový limit v milisekundách pro navázání připojení SSL/TLS s Fluentd serveru. Výchozí hodnota je 10000 milisekund (10 sekund).<br/> Pokud `UseTLS` je nastavena na hodnotu false, tato hodnota se ignoruje. |
| `UseTLS` | Logická hodnota | Označuje, zda kontejner musí používat protokol SSL/TLS pro komunikaci se serverem Fluentd. Výchozí hodnota je false. |

## <a name="logging-settings"></a>Nastavení protokolování

`Logging` Nastavení spravovat podporu protokolování ASP.NET Core pro váš kontejner. Pro váš kontejner, který používáte pro aplikace ASP.NET Core můžete použít stejný konfigurační nastavení a hodnoty. 

Podporuje následující zprostředkovatele protokolování LUIS kontejneru:

|Poskytovatel|Účel|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` zprostředkovatele. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Ladění](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` zprostředkovatele. Všechna nastavení konfigurace ASP.NET Core a výchozí hodnoty pro tohoto zprostředkovatele protokolování jsou podporovány.|
|[Disk](#disk-logging)|JSON zprostředkovatele. Tento zprostředkovatel protokolování zapíše data protokolu připojení výstupu.|

### <a name="disk-logging"></a>Disk protokolování
  
`Disk` Protokolování zprostředkovatel podporuje následující nastavení:  

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Format` | Řetězec | Výstupní formát souborů protokolu.<br/> **Poznámka:** tato hodnota musí být nastavená na `json` povolit zprostředkovatele. Pokud tato hodnota je spustit bez úkolu připojení výstupu při vytvoření instance kontejneru, dojde k chybě. |
| `MaxFileSize` | Integer | Maximální velikost v megabajtech (MB), soubor protokolu. Když velikost aktuálního souboru protokolu splňuje nebo překročí tuto hodnotu, nový soubor protokolu je spuštěn poskytovatel protokolování. Pokud není zadána hodnota -1, velikost souboru protokolu je omezen pouze maximální velikost souboru, pokud existuje, pro výstupní připojení. Výchozí hodnota je 1. |

Další informace o konfiguraci protokolování podpora ASP.NET Core najdete v tématu [konfigurační soubor nastavení](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mount-settings"></a>Nastavení připojování

Použití vazby připojí ke čtení a zápisu dat do a z kontejneru. Můžete určit vstupní připojení nebo připojení výstup tak, že zadáte `--mount` možnost [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu. 

Kontejner LUIS nepoužívá vstup nebo výstup připojí k uložení školení nebo dat služby. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](luis-container-howto.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby docker a hostiteli připojit umístění oprávnění. 

Následující tabulka popisuje nastavení podporováno.

|Požaduje se| Název | Typ dat | Popis |
|-------|------|-----------|-------------|
|Ano| `Input` | Řetězec | Cíl vstupní připojení. Výchozí hodnota je `/input`. Toto je umístění souborů balíčku LUIS. <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Ne| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje LUIS dotazu protokoly a protokoly kontejneru. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Hierarchické nastavení

Nastavení pro kontejner LUIS jsou hierarchické a všechny kontejnery na [hostitelský počítač](luis-container-howto.md#the-host-computer) pomocí sdílených hierarchie.

Můžete použít jednu z následujících nastavení:

* [Proměnné prostředí](#environment-variable-settings)
* [Argumenty příkazového řádku](#command-line-argument-settings)

Hodnoty proměnných prostředí přepsat hodnoty argumentů příkazového řádku, které pak přepsat výchozí hodnoty pro image kontejneru. Pokud chcete zadat jiné hodnoty v proměnné prostředí a argument příkazového řádku pro stejné nastavení konfigurace, hodnota v proměnné prostředí používá vytvořenou instanci kontejneru.

|Priorita|Nastavení umístění|
|--|--|
|1|Proměnná prostředí| 
|2|Příkazový řádek|
|3|Výchozí hodnota image kontejneru|

### <a name="environment-variable-settings"></a>Nastavení proměnné prostředí

Výhody použití proměnné prostředí jsou:

* Je možné nakonfigurovat několik nastavení.
* Více kontejnerů můžete použít stejné nastavení.

### <a name="command-line-argument-settings"></a>Nastavení argument příkazového řádku

Výhodou použití argumentů příkazového řádku je, že každý kontejner může použít jiná nastavení.

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](luis-container-howto.md#stop-the-container) ho.


* **Znak pro pokračování řádku**: příkazy dockeru v následujících částech použijte zpětné lomítko `\`, jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud máte velmi zkušenosti s kontejnery dockeru.

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