---
title: Konfigurace kontejnerů – plocha
titleSuffix: Azure Cognitive Services
description: Prostředí runtime kontejneru Plochy `docker run` je konfigurováno pomocí argumentů příkazu. Existují požadovaná i volitelná nastavení.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878421"
---
# <a name="configure-face-docker-containers"></a>Konfigurace kontejnerů Face Dockeru

Prostředí runtime kontejneru **Plochy** `docker run` je konfigurováno pomocí argumentů příkazu. Tento kontejner má několik požadovaných nastavení, spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení specifická pro kontejner jsou nastavení fakturace. 

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) a jsou používány společně a je nutné zadat platné hodnoty pro všechny tři z nich; jinak se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Toto `ApiKey` nastavení určuje klíč prostředků Azure, který slouží ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu apikey a hodnota musí být platný klíč pro [`Billing`](#billing-configuration-setting) prostředek cognitive _services_ zadaný pro nastavení konfigurace.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Správa prostředků **kognitivních služeb** v části **Klíče**

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

Toto `Billing` nastavení určuje identifikátor URI koncového bodu prostředku _služeb Cognitive Services_ v Azure, který slouží k měření fakturačních informací pro kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný identifikátor URI koncového bodu pro prostředek _služeb Cognitive Services_ v Azure. Kontejner hlásí využití přibližně každých 10 až 15 minut.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Přehled **kognitivních služeb** s označením`Endpoint`

Nezapomeňte přidat směrování _plochy_ do identifikátoru URI koncového bodu, jak je znázorněno v příkladu. 

|Požaduje se| Name (Název) | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace. Další informace o získání fakturačního identifikátoru URI naleznete v [tématu shromažďování požadovaných parametrů](face-how-to-install-containers.md#gathering-required-parameters). Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](../cognitive-services-custom-subdomains.md). |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Nastavení konfigurace CloudAI

Nastavení konfigurace v `CloudAI` části poskytují možnosti specifické pro kontejner jedinečné pro váš kontejner. Následující nastavení a objekty jsou podporovány `CloudAI` pro kontejner Plocha v části

| Name (Název) | Datový typ | Popis |
|------|-----------|-------------|
| `Storage` | Objekt | Scénář úložiště používá kontejner face. Další informace o scénářích úložiště `Storage` a přidružených nastaveních objektu najdete v [tématu Nastavení scénáře úložiště.](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Nastavení scénáře úložiště

Kontejner Face ukládá data objektů blob, mezipaměti, metadat a fronty v závislosti na tom, co se ukládá. Například trénovací indexy a výsledky pro skupinu velkých osob jsou uloženy jako data objektu blob. Kontejner Plochy poskytuje dva různé scénáře úložiště při interakci s těmito typy dat a jejich ukládání:

* Memory (Paměť)  
  Všechny čtyři typy dat jsou uloženy v paměti. Nejsou distribuovány, ani nejsou vytrvalé. Pokud je kontejner Face zastaven nebo odebrán, všechna data v úložišti pro tento kontejner je zničena.  
  Toto je výchozí scénář úložiště pro kontejner Plochy.
* Azure  
  Kontejner Face používá Azure Storage a Azure Cosmos DB k distribuci těchto čtyř typů dat napříč trvalým úložištěm. Data objektů blob a fronty jsou zpracovávána službou Azure Storage. Metadata a data mezipaměti zpracovává Azure Cosmos DB. Pokud je kontejner Face zastavené nebo odebrané, všechna data v úložišti pro tento kontejner zůstane uložená v Azure Storage a Azure Cosmos DB.  
  Prostředky používané ve scénáři úložiště Azure mají následující další požadavky
  * Prostředek úložiště Azure musí používat druh účtu StorageV2.
  * Prostředek Azure Cosmos DB musí používat rozhraní API Azure Cosmos DB pro MongoDB

Scénáře úložiště a přidružená nastavení `Storage` konfigurace jsou `CloudAI` spravovány objektem v části konfigurace. V objektu `Storage` jsou k dispozici následující nastavení konfigurace:

| Name (Název) | Datový typ | Popis |
|------|-----------|-------------|
| `StorageScenario` | Řetězec | Scénář úložiště podporované kontejneru. K dispozici jsou následující hodnoty<br/>`Memory`- Výchozí hodnota. Kontejner používá netrvalé, nedistribuované a v paměti úložiště, pro jednouzel, dočasné použití. Pokud je kontejner zastaven nebo odebrán, úložiště pro tento kontejner je zničen.<br/>`Azure`- Container používá prostředky Azure pro úložiště. Pokud je kontejner zastaven nebo odebrán, úložiště pro tento kontejner je trvalé.|
| `ConnectionStringOfAzureStorage` | Řetězec | Připojovací řetězec pro prostředek úložiště Azure používaný kontejnerem.<br/>Toto nastavení platí `Azure` pouze v `StorageScenario` případě, že je určeno pro nastavení konfigurace. |
| `ConnectionStringOfCosmosMongo` | Řetězec | Připojovací řetězec MongoDB pro prostředek Azure Cosmos DB používaný kontejnerem.<br/>Toto nastavení platí `Azure` pouze v `StorageScenario` případě, že je určeno pro nastavení konfigurace. |

Například následující příkaz určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro prostředky Azure Storage a Cosmos DB používané k ukládání dat pro kontejner Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Scénář úložiště je zpracován odděleně od vstupních připojení a výstupních připojení. Můžete zadat kombinaci těchto funkcí pro jeden kontejner. Například následující příkaz definuje připojení vazby `D:\Output` Dockeru ke složce v hostitelském počítači jako výstupní připojení, pak vytvoří instance kontejneru z image kontejneru Face a uloží soubory protokolu ve formátu JSON do výstupního připojení. Příkaz také určuje scénář úložiště Azure a poskytuje ukázkové připojovací řetězce pro prostředky Azure Storage a Cosmos DB používané k ukládání dat pro kontejner Face.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

Kontejnery face nepoužívají vstupní nebo výstupní připojení k ukládání dat školení nebo služby. 

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](face-how-to-install-containers.md#the-host-computer)navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněními umístění připojení hostitele. 

|Nepovinné| Name (Název) | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Čelní nádoby nepoužívají toto.|
|Nepovinné| `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru 

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění kontejneru pokračuje v běhu, dokud jej [nezastavíte.](face-how-to-install-containers.md#stop-the-container)

* **Znak pokračování řádku**: Příkazy Dockeru v následujících `\`částech používají jako znak pokračování řádku znak zpětného lomítka . Nazákladě požadavků hostitelského operačního systému jej nahraďte nebo odeberte. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste velmi obeznámeni s kontejnery Dockeru.

Nahraďte {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Face` prostředku na `Face` stránce Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota koncového bodu fakturace `Face` je dostupná na stránce Přehled Azure.| Viz [shromažďování požadovaných parametrů](face-how-to-install-containers.md#gathering-required-parameters) pro explicitní příklady. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](face-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze `Cognitive Services` stránky klíče prostředků Azure. 

## <a name="face-container-docker-examples"></a>Příklady Dockeru kontejneru tváří

Následující příklady Dockeru jsou pro kontejner plochy. 

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

* Přečtěte [si, jak nainstalovat a spustit kontejnery](face-how-to-install-containers.md)
