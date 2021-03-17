---
title: Konfigurace kontejnerů – Face
titleSuffix: Azure Cognitive Services
description: Běhové prostředí kontejneru Face se konfiguruje pomocí `docker run` argumentů příkazu. K dispozici jsou požadovaná i volitelná nastavení.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878421"
---
# <a name="configure-face-docker-containers"></a>Konfigurace kontejnerů Docker obličeje

Běhové prostředí kontejneru **Face** se konfiguruje pomocí `docker run` argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení fakturace jsou specifická pro kontejner. 

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Nastavení, [`Billing`](#billing-configuration-setting) a [`Eula`](#eula-setting) se používají společně a musíte zadat platné hodnoty pro všechny tři z nich. v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey`Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Cognitive Services_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Správa prostředků **Cognitive Services** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

`Billing`Nastavení určuje identifikátor URI koncového bodu _Cognitive Services_ prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _Cognitive Services_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: Přehled **Cognitive Services** , označený `Endpoint`

Nezapomeňte přidat směrování _obličeje_ k identifikátoru URI koncového bodu, jak je znázorněno v příkladu. 

|Požaduje se| Name | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](face-how-to-install-containers.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Nastavení konfigurace CloudAI

Konfigurační nastavení v `CloudAI` oddílu poskytují možnosti specifické pro kontejner, které jsou pro váš kontejner jedinečné. Následující nastavení a objekty jsou podporovány pro kontejner obličeje v `CloudAI` oddílu.

| Name | Datový typ | Popis |
|------|-----------|-------------|
| `Storage` | Objekt | Scénář úložiště používaný kontejnerem obličeje. Další informace o scénářích úložiště a souvisejících nastaveních pro `Storage` objekt najdete v tématu [nastavení scénáře úložiště](#storage-scenario-settings) . |

### <a name="storage-scenario-settings"></a>Nastavení scénáře úložiště

Kontejner obličeje ukládá data objektů blob, cache, metadata a Queue v závislosti na tom, co se ukládá. Například školicí indexy a výsledky pro velkou skupinu osob jsou uloženy jako data objektů BLOB. Kontejner obličeje nabízí dva různé scénáře úložiště při interakci s těmito typy dat a jejich ukládání:

* Paměť  
  Všechny čtyři typy dat jsou uloženy v paměti. Nejsou distribuované, ani trvalé. Pokud je kontejner obličeje zastavený nebo odebraný, všechna data v úložišti tohoto kontejneru se zničí.  
  Toto je výchozí scénář úložiště pro kontejner obličeje.
* Azure  
  Kontejner obličeje používá Azure Storage a Azure Cosmos DB k distribuci těchto čtyř typů dat mezi trvalé úložiště. Data objektů BLOB a front zpracovává Azure Storage. Metadata a data mezipaměti jsou zpracovávána nástrojem Azure Cosmos DB. Pokud je kontejner obličeje zastavený nebo odebraný, všechna data v úložišti pro tento kontejner zůstanou uložená v Azure Storage a Azure Cosmos DB.  
  Prostředky používané scénářem úložiště Azure mají tyto další požadavky:
  * Prostředek Azure Storage musí používat druh účtu StorageV2.
  * Prostředek Azure Cosmos DB musí používat rozhraní API Azure Cosmos DB pro MongoDB.

Scénáře úložiště a přidružená nastavení konfigurace jsou spravovány `Storage` objektem v části `CloudAI` konfigurace. V objektu jsou k dispozici následující nastavení konfigurace `Storage` :

| Name | Datový typ | Popis |
|------|-----------|-------------|
| `StorageScenario` | Řetězec | Scénář úložiště podporovaný kontejnerem. K dispozici jsou následující hodnoty:<br/>`Memory` -Výchozí hodnota. Kontejner používá netrvalé, nedistribuované a paměťové úložiště pro použití s jedním uzlem, dočasné použití. Pokud dojde k zastavení nebo odebrání kontejneru, bude úložiště pro tento kontejner zničeno.<br/>`Azure` -Container používá prostředky Azure pro úložiště. Pokud je kontejner zastavený nebo odebraný, úložiště pro tento kontejner je trvalé.|
| `ConnectionStringOfAzureStorage` | Řetězec | Připojovací řetězec pro prostředek Azure Storage používaný kontejnerem.<br/>Toto nastavení platí pouze `Azure` v případě, že je zadáno pro `StorageScenario` nastavení konfigurace. |
| `ConnectionStringOfCosmosMongo` | Řetězec | Připojovací řetězec MongoDB pro prostředek Azure Cosmos DB používaný kontejnerem.<br/>Toto nastavení platí pouze `Azure` v případě, že je zadáno pro `StorageScenario` nastavení konfigurace. |

Například následující příkaz určuje scénář Azure Storage a poskytuje ukázkové připojovací řetězce pro Azure Storage a prostředky Cosmos DB používané k ukládání dat kontejneru obličeje.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scénář úložiště se zpracovává odděleně od vstupních připojení a připojení pro výstup. Můžete určit kombinaci těchto funkcí pro jeden kontejner. Například následující příkaz definuje připojení Docker BIND ke `D:\Output` složce na hostitelském počítači jako připojení pro výstup a pak vytvoří instanci kontejneru z image kontejneru Face a uloží soubory protokolu ve formátu JSON do výstupního připojení. Příkaz také určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro Azure Storage a prostředky Cosmos DB používané k ukládání dat kontejneru obličeje.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

Kontejnery obličeje nepoužívají vstupní ani výstupní připojení k ukládání dat o školeních nebo službách. 

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](face-how-to-install-containers.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|Volitelné| Name | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery obličeje to nepoužívají.|
|Volitelné| `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker 

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](face-how-to-install-containers.md#stop-the-container) .

* **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\` jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Nahradit {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Face` prostředku na `Face` stránce klíčů Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na `Face` stránce Přehled Azure.| Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](face-how-to-install-containers.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](face-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze `Cognitive Services` stránky klíče prostředků Azure. 

## <a name="face-container-docker-examples"></a>Příklady Docker kontejneru Face

Následující příklady Docker jsou pro kontejner obličeje. 

### <a name="basic-example"></a>Základní příklad 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak nainstalovat a spustit kontejnery](face-how-to-install-containers.md) .
