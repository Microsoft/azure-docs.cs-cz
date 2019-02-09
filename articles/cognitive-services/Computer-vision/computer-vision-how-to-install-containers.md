---
title: Jak nainstalovat a spustit kontejnery – pro počítačové zpracování obrazu
titlesuffix: Azure Cognitive Services
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů v tomto kurzu návod pro počítačové zpracování obrazu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 02/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 58a22253efb6928c87937e4aba852dd93a3e4422
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978540"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalace a spouštění kontejnerů rozpoznat Text

Rozpoznávání textu část pro počítačové zpracování obrazu je také k dispozici jako kontejner Dockeru. Umožňuje detekovat a extrahovat tištěný text z obrázků s různými povrchy a pozadími, jako je potvrzení a plakáty nebo vizitky různé objekty.  
> [!IMPORTANT]
> Kontejner rozpoznat Text v současné době používá pouze angličtinu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím rozpoznat textových polí, musí splňovat následující požadavky:

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Rozpoznávání textu prostředků |Chcete-li použít kontejner, musíte mít:<br><br>A [ _rozpoznat Text_ ](vision-api-how-to-topics/howtosubscribe.md) prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách portálu Azure přehled rozpoznání textu a klíče a jsou vyžadovány pro spuštění kontejneru.<br><br>**{BILLING_KEY}** : klíč prostředku<br><br>**{BILLING_ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Následující tabulka popisuje minimální a doporučené jader procesoru a paměti k přidělení pro každý kontejner rozpoznat Text.

| Kontejner | Minimální | Doporučené |
|-----------|---------|-------------|
|Rozpoznání textu|1 jádro, 8 GB paměti, 0,5 TPS|2 jádra, 8 GB paměti, 1 TPS|

Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.

Jader a paměti odpovídají `--cpus` a `--memory` nastavení, které se používají jako součást `docker run` příkazu.


## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Image kontejneru pro rozpoznání textu jsou k dispozici. 

| Kontejner | Úložiště |
|-----------|------------|
|Rozpoznání textu | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Použití [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru.


### <a name="docker-pull-for-the-recognize-text-container"></a>Operace docker pull pro kontejner rozpoznat Text

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-rocognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spuštění kontejneru](#run-the-container-with-docker-run), s požadovanými fakturace nastavení. Další [příklady](computer-vision-resource-container-config.md) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění kontejneru. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{BILLING_KEY} | Tento klíč se používá ke spuštění kontejneru a je k dispozici na stránce klíče rozpoznat Text webu Azure portal.  |
|{BILLING_ENDPOINT_URI} | Fakturační koncový bod hodnotu identifikátoru URI.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Tento příkaz:

* Spouští rozpoznávat kontejner z image kontejneru
* Přidělí jedno Procesorové jádro a 4 gigabajty (GB) paměti
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

Další [příklady](./computer-vision-resource-container-config.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

Kontejner poskytuje koncový bod předpovědi dotazů založených na REST API. 

Použít hostitele, https://localhost:5000, pro kontejner rozhraní API.

### <a name="asynchronous-text-recognition"></a>Rozpoznávání asynchronní textu

Můžete použít `POST /vision/v2.0/recognizeText` a `GET /vision/v2.0/textOperations/*{id}*` operací ve vzájemné součinnosti asynchronně rozpoznat tištěný text v obrázku, podobně jako na používání těchto odpovídající operace REST služby pro počítačové zpracování obrazu. Zásobník rozpoznání textu rozpozná pouze tištěný text, ne rukou psaný text v současné době proto `mode` parametr obvykle zadaný pro operaci služby pro počítačové zpracování obrazu je ignorován v kontejneru rozpoznat Text.

### <a name="synchronous-text-recognition"></a>Rozpoznávání synchronní textu

Můžete použít `POST /vision/v2.0/recognizeTextDirect` operace synchronně rozpoznat tištěný text v obrázku. Protože tato operace je synchronní, text požadavku pro tuto operaci je stejný jako u `POST /vision/v2.0/recognizeText` operace, ale odpověď body pro tuto operaci je stejný jako vrácené `GET /vision/v2.0/textOperations/*{id}*` operace.

## <a name="stop-the-container"></a>Zastavit kontejner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](./computer-vision-resource-container-config.md#mount-settings) a povolení protokolování kontejneru vygeneruje soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="containers-api-documentation"></a>Dokumentace k rozhraní API kontejneru

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fakturace

Odeslat kontejnery rozpoznat Text fakturační údaje do Azure, pomocí _rozpoznat Text_ prostředků v účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů rozpoznat Text. Souhrn:

* Rozpoznávání textu poskytuje kontejner pro Linux pro Docker, zapouzdření rozpoznání textu.
* Image kontejneru se stáhnou z kontejneru registru Microsoft (MCR) v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API nebo sady SDK můžete použít k volání operací v kontejnerech rozpoznat Text tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](computer-vision-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled pro počítačové zpracování obrazu](Home.md) Další informace o rozpoznávání tištěné a rukou psaný text  
* Odkazovat [rozhraní API pro počítačové zpracování obrazu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](FAQ.md) k vyřešení problémů týkajících se funkce pro počítačové zpracování obrazu.
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)
