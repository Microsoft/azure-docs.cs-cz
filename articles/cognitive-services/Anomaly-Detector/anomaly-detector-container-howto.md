---
title: Instalace a spouštění kontejnerů – detekce anomálií
titleSuffix: Azure Cognitive Services
description: Pomocí pokročilých algoritmů rozhraní API detekce anomálií můžete identifikovat anomálie ve vašich datech časových řad.
services: cognitive-services
author: IEvangelist
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 704ea3ecbc309c2328e120d5b6776e659944d254
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275027"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Instalace a spouštění kontejnerů detekce anomálií

Detekce anomálií má následující kontejneru: 

|Funkce|Funkce|
|-|-|
|Detektor anomálií| <li> Detekuje anomálie, když k nim dojde v reálném čase. <li> Detekuje anomálie v celé datové sady v dávce. <li> Odvodí z něj normální očekávaný rozsah data. <li> Podporuje anomálií detekce citlivosti úpravy lépe přizpůsobit vaše data. |

Podrobné informace o rozhraních API najdete v tématu:
* [Další informace o službě API detekce anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů detekce anomálií, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Prostředek detekce anomálií |Chcete-li použít tyto kontejnery, musíte mít:<br><br>_Detekce anomálií_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách portálu Azure přehled detekce anomálií a klíče a jsou vyžadovány pro spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Nejprve musíte vyplňte a odešlete [formulář žádosti o kontejneru detekce anomálií](https://aka.ms/adcontainer) chcete požádat o přístup ke kontejneru.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro kontejner detekce anomálií.

| QPS (dotazy za sekundu) | Minimální | Doporučené |
|-----------|---------|-------------|
| 10 QPS | 4 jádra, 1GB paměti | 8 jader 2GB paměti |
| 20 QPS | 8 jader, 2GB paměti | 16 jader 4GB paměti |

Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Použití [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru.

| Kontejner | Úložiště |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Operace docker pull pro kontejner detekce anomálií

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s požadovanými fakturace nastavení. Další [příklady](anomaly-detector-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče detekce anomálií webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem detekce anomálií webu Azure portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spuštění kontejneru detekce anomálií z image kontejneru
* Přidělí jedno Procesorové jádro a 4 gigabajty (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Spouštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit několik kontejnerů s vystavené porty, ujistěte se, že ke spuštění každý kontejner s jiným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Nahradit `<container-registry>` a `<container-name>` hodnotami kontejnerů použijete. Toto nemusí být stejný kontejner. Máte kontejner detekce anomálií a LUIS kontejner společně běží na HOSTITELI nebo může mít více detekce anomálií kontejnerech je spuštěná. 

Spuštění prvního kontejneru na portu 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Spusťte na portu 5001 druhý kontejner.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Každé následné kontejner by měl být na jiném portu. 

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

Kontejner poskytuje koncový bod předpovědi dotazů založených na REST API. 

Použít hostitele, https://localhost:5000, pro kontejner rozhraní API.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](anomaly-detector-container-configuration.md#mount-settings) a povolení protokolování kontejneru vygeneruje soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="billing"></a>Fakturace

Odeslat kontejnery detekce anomálií fakturační údaje do Azure, pomocí _detekce anomálií_ prostředků v účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání detekce anomálií kontejnery. Souhrn:

* Detekce anomálií poskytuje jeden kontejner Linuxu pro Docker, zapouzdření s vs batch streamování odvození očekávaný rozsah a optimalizace citlivost detekce anomálií.
* Image kontejneru se stáhnou z registru kontejneru Azure privátní vyhrazené pro kontejnery ve verzi preview.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech detekce anomálií tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (například čas řady data, která je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](anomaly-detector-container-configuration.md) nastavení konfigurace
* [Další informace o službě API detekce anomálií](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
