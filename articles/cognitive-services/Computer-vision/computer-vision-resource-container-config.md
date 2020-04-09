---
title: Konfigurace kontejnerů – počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Tento článek ukazuje, jak nakonfigurovat požadované i volitelné nastavení pro rozpoznat textové kontejnery v počítačovém vidění.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3be302019c712c13bd29d7ed3781151a1648e847
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879305"
---
# <a name="configure-computer-vision-docker-containers"></a>Konfigurace kontejnerů Programu Computer Vision Docker

Runtime prostředí kontejneru počítačového vidění nakonfigurujete pomocí argumentů příkazu. `docker run` Tento kontejner má několik požadovaných nastavení, spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení specifická pro kontejner jsou nastavení fakturace. 

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) a jsou používány společně a je nutné zadat platné hodnoty pro všechny tři z nich; jinak se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Toto `ApiKey` nastavení určuje `Cognitive Services` klíč prostředků Azure, který slouží ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu apikey a hodnota musí být platný klíč pro [`Billing`](#billing-configuration-setting) prostředek cognitive _services_ zadaný pro nastavení konfigurace.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Správa prostředků **kognitivních služeb** v části **Klíče**

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

Toto `Billing` nastavení určuje identifikátor URI koncového bodu prostředku _služeb Cognitive Services_ v Azure, který slouží k měření fakturačních informací pro kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný identifikátor URI koncového bodu pro prostředek _služeb Cognitive Services_ v Azure. Kontejner hlásí využití přibližně každých 10 až 15 minut.

Toto nastavení naleznete na následujícím místě:

* Portál Azure: Přehled **kognitivních služeb** s označením`Endpoint`

Nezapomeňte přidat `vision/v1.0` směrování do identifikátoru URI koncového bodu, jak je znázorněno v následující tabulce. 

|Požaduje se| Name (Název) | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Koncový bod fakturace, identifikátor URI<br><br>Příklad:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení fluentdu

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení pověření proxy http

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Pomocí vazební připojení číst a zapisovat data do a z kontejneru. Vstupní připojení nebo výstupní připojení můžete určit `--mount` zadáním možnosti v příkazu [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/)

Kontejnery počítačového vidění nepoužívají vstupní nebo výstupní připojení k ukládání trénovacích nebo servisních dat. 

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](computer-vision-how-to-install-containers.md#the-host-computer)navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněními umístění připojení hostitele. 

|Nepovinné| Name (Název) | Datový typ | Popis |
|-------|------|-----------|-------------|
|Nepovolené| `Input` | Řetězec | Kontejnery počítačového vidění tonepoužívají.|
|Nepovinné| `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy.  Po spuštění kontejneru pokračuje v běhu, dokud jej [nezastavíte.](computer-vision-how-to-install-containers.md#stop-the-container)

* **Znak pokračování řádku**: Příkazy Dockeru v následujících `\`částech používají jako znak pokračování řádku znak zpětného lomítka . Nazákladě požadavků hostitelského operačního systému jej nahraďte nebo odeberte. 
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste velmi obeznámeni s kontejnery Dockeru.

Nahraďte {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč koncového bodu `Computer Vision` prostředku na `Computer Vision` stránce Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Hodnota koncového bodu fakturace `Computer Vision` je dostupná na stránce Přehled Azure.| Viz [shromažďování požadovaných parametrů](computer-vision-how-to-install-containers.md#gathering-required-parameters) pro explicitní příklady. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](computer-vision-how-to-install-containers.md#billing).
> Hodnota ApiKey je **klíč** ze `Cognitive Services` stránky klíče prostředků Azure.

## <a name="container-docker-examples"></a>Příklady Dockeru kontejnerů

Následující příklady Dockeru jsou pro kontejner read.

### <a name="basic-example"></a>Základní příklad

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Další kroky

* Přečtěte si [jak nainstalovat a spustit kontejnery](computer-vision-how-to-install-containers.md).
