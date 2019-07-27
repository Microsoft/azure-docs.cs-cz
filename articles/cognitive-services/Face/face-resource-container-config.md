---
title: Konfigurace kontejnerů – rozhraní API pro rozpoznávání tváře
titleSuffix: Azure Cognitive Services
description: Nastavení konfigurace pro kontejnery.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 6dd047b0ba7f9a123ffcc014cff5604466946d07
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564099"
---
# <a name="configure-face-docker-containers"></a>Konfigurace kontejnerů Docker obličeje

Běhové prostředí kontejneru **Face** se konfiguruje pomocí `docker run` argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení fakturace jsou specifická pro kontejner. 

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platný klíč pro prostředek _Cognitive Services_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

* Azure Portal: **Cognitive Services** Správa prostředků, v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

Nastavení určuje identifikátor URI koncového bodu Cognitive Services prostředku v Azure, který se používá pro informace o fakturaci pro daný kontejner.  `Billing` Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _Cognitive Services_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

* Azure Portal: **Cognitive Services** Přehled, označený`Endpoint`

Nezapomeňte přidat směrování _obličeje_ k identifikátoru URI koncového bodu, jak je znázorněno v příkladu. 

|Požadováno| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace<br><br>Příklad:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Nastavení konfigurace CloudAI

Nastavení konfigurace `CloudAI` části poskytují možnosti specifické pro kontejner jedinečných do kontejneru. Jsou podporovány následující nastavení a objekty pro kontejner pro rozpoznávání tváře ve `CloudAI` oddílu

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `Storage` | Objekt | Scénář úložiště používané pro rozpoznávání tváře kontejneru. Další informace o scénáře využití služby storage a související nastavení `Storage` objektu, najdete v článku [scénář nastavení úložiště](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Scénář nastavení úložiště

Kontejner pro rozpoznávání tváře ukládá objekt blob, mezipaměť, metadata a data fronty, podle toho, co je právě uložili. Například školení indexy a výsledky pro velké osoby skupiny se ukládají jako data objektů blob. Kontejner pro rozpoznávání tváře nabízí dva různé scénáře využití služby storage při práci s a ukládání tyto typy dat:

* Memory (Paměť)  
  Všechny čtyři typy dat jsou uložené v paměti. Nejsou distribuovány ani jsou trvalé. Pokud je zastavena nebo odebrání kontejneru pro rozpoznávání tváře je zničen všechna data v úložišti pro tento kontejner.  
  Toto je výchozí scénář úložiště pro kontejner pro rozpoznávání tváře.
* Azure  
  Kontejner pro rozpoznávání tváře využívá Azure Storage a Azure Cosmos DB k distribuci tyto čtyři typy dat napříč trvalého úložiště. Zpracování dat objektů BLOB a fronty Azure Storage. Metadata a data mezipaměti jsou zpracovávána nástrojem Azure Cosmos DB. Pokud kontejner pro rozpoznávání tváře je zastavena nebo odebrán, všechna data v úložišti pro tento kontejner zůstane uložená v Azure Storage a Azure Cosmos DB.  
  Prostředky využívané třídou scénáře služby Azure storage mají následující další požadavky
  * Prostředek služby Azure Storage, musíte použít StorageV2 druh účtu
  * Prostředek Azure Cosmos DB musí používat rozhraní API Azure Cosmos DB pro MongoDB.

Scénáře využití služby storage a související konfigurační nastavení jsou spravovány `Storage` objektu v oblasti `CloudAI` konfigurační oddíl. Jsou k dispozici v následujícím nastavením konfigurace `Storage` objektu:

| Název | Typ dat | Popis |
|------|-----------|-------------|
| `StorageScenario` | Řetězec | Scénář úložiště podporuje kontejneru. Následující hodnoty jsou k dispozici<br/>`Memory` – Výchozí hodnota. Kontejner používá dočasné nedistribuovaná a v paměti úložiště pro použití s jedním uzlem, dočasné. Pokud je zastavena nebo odebrání kontejneru úložiště pro tento kontejner je zničen.<br/>`Azure` -Kontejner využívá úložiště prostředků Azure. Pokud je zastavena nebo odebrání kontejneru úložiště pro tento kontejner se ukládají.|
| `ConnectionStringOfAzureStorage` | Řetězec | Připojovací řetězec pro prostředek služby Azure Storage používaný kontejneru.<br/>Toto nastavení platí jenom v případě `Azure` je určená pro `StorageScenario` nastavení konfigurace. |
| `ConnectionStringOfCosmosMongo` | Řetězec | Připojovacího řetězce MongoDB pro prostředek služby Azure Cosmos DB používá kontejneru.<br/>Toto nastavení platí jenom v případě `Azure` je určená pro `StorageScenario` nastavení konfigurace. |

Například následující příkaz určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro prostředky Azure Storage a Cosmos DB používá k ukládání dat pro kontejner pro rozpoznávání tváře.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scénář úložiště je zpracovávat odděleně od připojení vstupní a výstupní připojení. Můžete zadat kombinací těchto funkcí pro jeden kontejner. Například následující příkaz definuje přípojný vazby Dockeru k `D:\Output` složky na hostitelském počítači jako výstup připojení, pak vytvoří kontejner z image kontejneru pro rozpoznávání tváře a ukládání souborů protokolu ve formátu JSON do výstupu připojení. Příkaz také určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro prostředky Azure Storage a Cosmos DB používá k ukládání dat pro kontejner pro rozpoznávání tváře.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

Kontejnery obličeje nepoužívají vstupní ani výstupní připojení k ukládání dat o školeních nebo službách. 

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](face-how-to-install-containers.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele. 

|volitelná,| Name | Typ dat | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery obličeje to nepoužívají.|
|volitelná,| `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad 

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy.  Po spuštění kontejneru nadále běžel dokud [Zastavit](face-how-to-install-containers.md#stop-the-container) ho.

* **Znak pro pokračování řádku**: Příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
|{API_KEY} | Klíč koncového bodu prostředku Cognitive Services. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | Hodnota koncového bodu, včetně směrování oblastí a obličeje.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](face-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure `Cognitive Services` . 

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

## <a name="next-steps"></a>Další postup

* Kontrola [instalace a spouštění kontejnerů](face-how-to-install-containers.md)
