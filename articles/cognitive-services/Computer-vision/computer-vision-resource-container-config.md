---
title: Konfigurace čtecích kontejnerů OCR – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak nakonfigurovat požadovaná i volitelná nastavení pro čtení kontejnerů OCR v Počítačové zpracování obrazu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: ee2e4fca697c086b95e83feb9d40ce8e07dc344c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611891"
---
# <a name="configure-read-ocr-docker-containers"></a>Konfigurace čtení kontejnerů Docker pro rozpoznávání OCR

Pomocí argumentů příkazu můžete nakonfigurovat Počítačové zpracování obrazu běhové prostředí kontejneru pro rozpoznávání OCR `docker run` . Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení fakturace jsou specifická pro kontejner. 

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Nastavení, [`Billing`](#billing-configuration-setting) a [`Eula`](#eula-setting) se používají společně a musíte zadat platné hodnoty pro všechny tři z nich. v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](computer-vision-how-to-install-containers.md).

Kontejner také obsahuje následující nastavení konfigurace specifické pro kontejner:

|Vyžadováno|Nastavení|Účel|
|--|--|--|
|No|ReadEngineConfig:ResultExpirationPeriod| pouze kontejnery v 2.0. Doba vypršení platnosti výsledku v hodinách Výchozí hodnota je 48 hodin. Nastavení určuje, kdy má systém vymazat výsledky rozpoznávání. Například pokud `resultExpirationPeriod=1` systém vymaže výsledek rozpoznávání 1 hodina po procesu. Pokud `resultExpirationPeriod=0` systém vymaže výsledek rozpoznávání po načtení výsledku.|
|No|Mezipaměť: Redis| pouze kontejnery v 2.0. Povoluje úložiště Redis pro ukládání výsledků. Mezipaměť je *vyžadována* , pokud je za nástroj pro vyrovnávání zatížení umístěno více kontejnerů pro čtení.|
|No|Fronta: RabbitMQ|pouze kontejnery v 2.0. Povoluje RabbitMQ pro odesílání úloh. Toto nastavení je užitečné, když je za nástroj pro vyrovnávání zatížení umístěno více kontejnerů pro čtení.|
|No|Fronta: Azure: QueueVisibilityTimeoutInMilliseconds | pouze kontejnery v3. x. Čas, kdy bude zpráva viditelná, když ji jiný pracovník zpracovává. |
|No|Storage::D ocumentStore:: MongoDB|pouze kontejnery v 2.0. Povoluje MongoDB pro trvalé úložiště výsledků. |
|No|Storage: ObjectStore: Azureblobu: ConnectionString| pouze kontejnery v3. x. Připojovací řetězec služby Azure Blob Storage. |
|No|Úložiště: TimeToLiveInDays| pouze kontejnery v3. x. Doba vypršení platnosti výsledku ve dnech Nastavení určuje, kdy má systém vymazat výsledky rozpoznávání. Výchozí hodnota je 2 dny (48 hodin), což znamená, že všechny výsledky v reálném čase po delší dobu nebudou zaručeny k úspěšnému načtení. |
|No|Úkol: MaxRunningTimeSpanInMinutes| pouze kontejnery v3. x. Maximální doba běhu pro jeden požadavek. Výchozí hodnota je 60 minut. |

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey`Nastavení určuje klíč prostředku Azure, který `Cognitive Services` se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Cognitive Services_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Správa prostředků **Cognitive Services** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

`Billing`Nastavení určuje identifikátor URI koncového bodu _Cognitive Services_ prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _Cognitive Services_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Přehled **Cognitive Services** , označený `Endpoint`

Nezapomeňte přidat `vision/v1.0` směrování k identifikátoru URI koncového bodu, jak je znázorněno v následující tabulce. 

|Vyžadováno| Name | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním `--mount` Možnosti v příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat vstupní připojení nebo připojení pro výstup.

Kontejnery Počítačové zpracování obrazu nepoužívají k ukládání dat školení nebo služby vstupní ani výstupní připojení. 

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](computer-vision-how-to-install-containers.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|Volitelné| Name | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery Počítačové zpracování obrazu nepoužívají.|
|Volitelné| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\` jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Nahradit {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Computer Vision` prostředku na `Computer Vision` stránce klíčů Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na `Computer Vision` stránce Přehled Azure.| Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](computer-vision-how-to-install-containers.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](computer-vision-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze `Cognitive Services` stránky klíče prostředků Azure.

## <a name="container-docker-examples"></a>Příklady Docker kontejneru

Následující příklady Docker jsou určené pro kontejner pro čtení.


# <a name="version-32-preview"></a>[Verze 3,2-Preview](#tab/version-3-2)

### <a name="basic-example"></a>Základní příklad

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Příklad protokolování 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

### <a name="basic-example"></a>Základní příklad

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Příklad protokolování 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [Jak nainstalovat a spustit kontejnery](computer-vision-how-to-install-containers.md).
