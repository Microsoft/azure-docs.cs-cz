---
title: Nastavení kontejneru Dockeru – LUIS
titleSuffix: Azure Cognitive Services
description: Prostředí runtime kontejneru LUIS `docker run` je konfigurováno pomocí argumentů příkazu. Služba LUIS má několik požadovaných nastavení spolu s několika volitelnými nastaveními.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795014"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurace kontejnerů Dockeru pro rozpoznávání jazyků 

Prostředí runtime **kontejneru Language Understanding** (LUIS) je konfigurováno pomocí argumentů příkazu. `docker run` Služba LUIS má několik požadovaných nastavení spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení specifická pro kontejner jsou [vstupní nastavení připojení](#mount-settings) a nastavení fakturace. 

## <a name="configuration-settings"></a>Nastavení konfigurace

Tento kontejner má následující nastavení konfigurace:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-setting)|Slouží ke sledování fakturačních údajů.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do kontejneru telemetrická podporu [Azure Application Insights.](https://docs.microsoft.com/azure/application-insights)|
|Ano|[Fakturace](#billing-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Plynulý](#fluentd-settings)|Zápis protokolu a volitelně metrických dat na server Fluentd.|
|Ne|[Http Proxy](#http-proxy-credentials-settings)|Nakonfigurujte proxy http pro vytváření odchozích požadavků.|
|Ne|[protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET core pro váš kontejner. |
|Ano|[Koně](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpět do hostitelského počítače.|

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting), [`Eula`](#eula-setting) a jsou používány společně a je nutné zadat platné hodnoty pro všechny tři z nich; jinak se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Nastavení ApiKey

Toto `ApiKey` nastavení určuje klíč prostředků Azure, který slouží ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu apikey a hodnota musí být platný klíč pro [`Billing`](#billing-setting) prostředek cognitive _services_ zadaný pro nastavení konfigurace.

Toto nastavení naleznete na následujících místech:

* Portál Azure: Správa prostředků **kognitivních služeb** v části **Klíče**
* Portál LUIS: **Stránka Nastavení klíčů a koncového bodu.** 

Nepoužívejte startovací tlačítko ani provázkovací klávesu. 

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Nastavení fakturace

Toto `Billing` nastavení určuje identifikátor URI koncového bodu prostředku _služeb Cognitive Services_ v Azure, který slouží k měření fakturačních informací pro kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný identifikátor URI koncového bodu pro prostředek _služeb Cognitive Services_ v Azure. Kontejner hlásí využití přibližně každých 10 až 15 minut.

Toto nastavení naleznete na následujících místech:

* Portál Azure: Přehled **kognitivních služeb** s označením`Endpoint`
* Portál LUIS: Klíče a stránka **nastavení koncového bodu** jako součást identifikátoru URI koncového bodu.

| Požaduje se | Name (Název) | Datový typ | Popis |
|----------|------|-----------|-------------|
| Ano      | `Billing` | řetězec | Identifikátor URI koncového bodu fakturace. Další informace o získání fakturačního identifikátoru URI naleznete v [tématu shromažďování požadovaných parametrů](luis-container-howto.md#gathering-required-parameters). Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Kontejner LUIS nepoužívá vstupní nebo výstupní připojení k ukládání dat školení nebo služby. 

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](luis-container-howto.md#the-host-computer)navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby dockeru a oprávněními umístění připojení hostitele. 

Následující tabulka popisuje podporovaná nastavení.

|Požaduje se| Name (Název) | Datový typ | Popis |
|-------|------|-----------|-------------|
|Ano| `Input` | Řetězec | Cíl vstupního připojení. Výchozí hodnota je `/input`. Toto je umístění souborů balíčku LUIS. <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Ne| `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly dotazů LUIS a protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění kontejneru pokračuje v běhu, dokud jej [nezastavíte.](luis-container-howto.md#stop-the-container)

* Tyto příklady používají adresář `C:` mimo jednotku, aby se zabránilo konfliktům oprávnění v systému Windows. Pokud potřebujete použít konkrétní adresář jako vstupní adresář, budete možná muset udělit oprávnění služby dockeru. 
* Neměňte pořadí argumentů, pokud nejste velmi dobře obeznámeni s kontejnery dockeru.
* Pokud používáte jiný operační systém, použijte správnou konzolu/terminál, syntaxi složky pro připojení a znak pokračování řádku pro váš systém. Tyto příklady předpokládají konzolu systému `^`Windows se znakem pokračování řádku . Vzhledem k tomu, že kontejner je operační systém Linux, cílové připojení používá syntaxi složky ve stylu Linuxu.

Nahraďte {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `LUIS` prostředku na `LUIS` stránce Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota koncového bodu fakturace `LUIS` je dostupná na stránce Přehled Azure.| Viz [shromažďování požadovaných parametrů](luis-container-howto.md#gathering-required-parameters) pro explicitní příklady. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí. Další informace naleznete v [tématu Fakturace](luis-container-howto.md#billing).
> Hodnota ApiKey je **klíč** ze stránky Klíče a koncové body na portálu `Cognitive Services` LUIS a je také k dispozici na stránce klíče prostředků Azure. 

### <a name="basic-example"></a>Základní příklad

Následující příklad obsahuje nejméně argumentů možné spustit kontejner:

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

Následující příklad nastaví Argument ApplicationInsights pro odesílání telemetrie do Application Insights, když je spuštěn kontejner:

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

Následující příkaz nastaví úroveň `Logging:Console:LogLevel`protokolování , a [`Information`](https://msdn.microsoft.com)nakonfiguruje úroveň protokolování na . 

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

* Přečtěte [si, jak nainstalovat a spustit kontejnery](luis-container-howto.md)
* Informace o řešení problémů souvisejících s funkcemi služby LUIS naleznete v [tématu Poradce při potížích.](troubleshooting.md)
* Použití více [kontejnerů služeb Cognitive Services](../cognitive-services-container-support.md)
