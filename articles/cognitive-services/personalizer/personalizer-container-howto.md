---
title: Instalace a spouštění kontejnerů - Personalizer
titleSuffix: Azure Cognitive Services
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů pro Personalizer.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: a197531a7c78823271c0a5fa5413b76746f63a9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507017"
---
# <a name="install-and-run-personalizer-containers"></a>Nainstalujte a spusťte Personalizer kontejnery

Služba Personalizer má následující kontejnerů: 

|Funkce|Funkce|
|-|-|
|Personalizer|Určete nejlepší akci z aktuálního kontextu obsahu a uživatele.|

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery Personalizer služby, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Prostředek služby personalizer |Chcete-li použít tyto kontejnery, musíte mít:<br><br>A _Personalizer služby_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách portálu Azure přehled Personalizer služby a klíče a jsou vyžadovány pro spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>Hostitelském počítači

**Hostitele** je počítač, který spustí kontejner dockeru. Může být počítač připojený k vlastních prostorách nebo v dockeru, který hostuje službu v Azure včetně:

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) clusteru nasadí do [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Další informace najdete v tématu [nasazení Kubernetes pro Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner Personalizer služby.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|Personalizer | 1 jádro, 4 GB paměti | 2 jádra, 8 GB paměti |

Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.

Kontejner musí být schopný se připojit k Azure EventHub v pořadí k přenosu informací v pořadí a potřebu volání Personalizer server v Azure a musí být schopný se připojit k rozhraní API Personalizer-li načíst požadovanou konfigurací a nejnovější počítače modely učení.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Image kontejneru pro službu Personalizer jsou k dispozici. 

| Kontejner | Úložiště |
|-----------|------------|
| Personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Můžete použít [imagí dockeru](https://docs.docker.com/engine/reference/commandline/images/) příkazu zobrazte výpis imagí kontejnerů stažené. Například následující příkaz zobrazí seznam ID, úložiště a značka image každý stažený kontejneru, formátovaná jako tabulka:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Operace docker pull Personalizer služby kontejneru

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Jak funguje kontejneru

Kontejner Personalizer reprezentuje klientská část Personalizer služby. Když se kontejner spustí, získá modely a nastavení konfigurace ze Personalizer služby v cloudu Azure. Služby container service používá tyto informace k reagovat na žádosti o **pořadí** a **reward**. Kontejner rovněž odesílá tyto požadavky na Personalizer prostředek v cloudu Azure. Tyto informace se použije k natrénování modelu Personalizer smyčky, na základě aktuální nastavení frekvence aktualizace modelu. Aktualizace modelu budou odeslána zpět do kontejneru. 

![Lokálně, místního architektury pro klienta Personalizer kontejneru](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s požadovanými fakturace nastavení. Další [příklady](personalizer-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění tři kontejnery. Příkaz používá následující parametry:

| Zástupný symbol | Value |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče služby Personalizer webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce Přehled služby Personalizer webu Azure portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spouští služby Personalizer kontejner z image kontejneru
* Přidělí jedno Procesorové jádro a 4 gigabajty (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Spouštění více kontejnerů na stejném hostiteli

Pokud máte v úmyslu spustit několik kontejnerů s vystavené porty, ujistěte se, že ke spuštění každý kontejner s jiným portem. Například spusťte první kontejner na portu 5000 a druhý kontejner na portu 5001.

Nahradit `<container-registry>` a `<container-name>` hodnotami kontejnerů použijete. Toto nemusí být stejný kontejner. Můžete mít Personalizer kontejner a kontejner LUIS společně běží na HOSTITELI nebo může mít více Personalizer kontejnerech je spuštěná. 

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

## <a name="stop-the-container"></a>Zastavit kontejner

Chcete-li vypnout kontejneru, v prostředí příkazového řádku, ve kterém je kontejner spuštěný, stiskněte **Ctrl + C**.

## <a name="troubleshoot"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](personalizer-container-configuration.md#mount-settings) a povolení protokolování kontejneru vygeneruje soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="container-api-documentation"></a>Dokumentace ke službě Container rozhraní API

Kontejner, poskytuje kompletní dokumentaci pro koncové body a také `Try it now` funkce. Tato funkce umožňuje zadat nastavení do webového formuláře HTML a proveďte dotaz bez nutnosti psát jakýkoli kód. Jakmile je k dispozici dotaz vrací hodnotu, například příkaz CURL k předvedení hlavičky protokolu HTTP a textu formátu potřebném. 

> [!TIP]
> Čtení [specifikace OpenAPI](https://swagger.io/docs/specification/about/), popisující operace rozhraní API podporuje kontejnerem, od `/swagger` relativní identifikátor URI. Příklad:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Fakturace

Odeslat kontejnery služby Personalizer fakturační údaje do Azure, pomocí _Personalizer služby_ prostředků v účtu Azure. 

Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu.  

`docker run` Příkaz používá následující argumenty pro účely fakturace:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API _Personalizer služby_ prostředek, který používá ke sledování fakturační údaje. |
| `Billing` | Koncový bod _Personalizer služby_ prostředek, který používá ke sledování fakturační údaje.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](personalizer-container-configuration.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání služby Personalizer kontejnery. Souhrn:

* Služba personalizer poskytuje kontejnery Linuxu pro Docker, zapouzdření individuálního nastavení.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech Personalizer služby tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Personalizer kontejnery se také odeslat žádost o data odpovídající služby v Azure pro účely online školení a získá aktualizace modelů pravidelně z Azure.

## <a name="next-steps"></a>Další postup

[Konfigurace kontejneru Dockeru pro `Docker Run` příkaz](personalizer-container-configuration.md)