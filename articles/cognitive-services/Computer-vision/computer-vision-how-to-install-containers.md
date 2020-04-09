---
title: Jak nainstalovat a spustit kontejnery - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Jak stáhnout, nainstalovat a spustit kontejnery pro počítačové zpracování počítače v tomto kurzu návodu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879339"
---
# <a name="install-and-run-read-containers-preview"></a>Instalace a spuštění kontejnerů pro čtení (náhled)

Kontejnery umožňují spustit api počítačového zpracování ve vlastním prostředí. Kontejnery jsou skvělé pro specifické požadavky na zabezpečení a zásadsprávné řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner počítačového vidění.

Jeden kontejner Dockeru *Read*je k dispozici pro počítačové zpracování obrazu. Kontejner *Pro čtení* umožňuje detekovat a extrahovat tištěný *text* z obrázků různých objektů s různými povrchy a pozadími, jako jsou účtenky, plakáty a vizitky. Kontejner pro *čtení* navíc detekuje *ručně psaný text* v obrazech a poskytuje podporu souborů PDF, TIFF a vícestránkových souborů. Další informace naleznete v dokumentaci [k rozhraní READ](concept-recognizing-text.md#read-api) API.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů musíte splnit následující požadavky:

|Požaduje se|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker Engine nainstalovaný v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker také nakonfigurován pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Měli byste mít základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery `docker` a image kontejnerů, stejně jako znalost základních příkazů.| 
|Zdroj počítačového vidění |Chcete-li kontejner používat, musíte mít:<br><br>Prostředek Azure **Computer Vision** a přidružený klíč rozhraní API identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránce Přehled a Klíče pro prostředek a jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}:** Jeden ze dvou dostupných klíčů prostředků na stránce **Klíče**<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce **Přehled**|

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru privátního kontejneru

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Podpora pro Advanced Vector Extension

**Hostitelský** počítač je počítač, který spouští kontejner dockeru. Hostitel *musí podporovat* [rozšíření Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Podporu AVX2 můžete zkontrolovat na hostitelích Linuxu pomocí následujícího příkazu:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Pro podporu AVX2 je *vyžadován* hostitelský počítač. Kontejner *nebude* fungovat správně bez podpory AVX2.

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Získejte obrázek kontejneru pomocí`docker pull`

K dispozici jsou ifotky kontejnerů pro čtení.

| Kontejner | Registr kontejnerů / úložiště / název obrázku |
|-----------|------------|
| Čtení | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru.

### <a name="docker-pull-for-the-read-container"></a>Docker vytáhnout pro kontejner čtení

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak kontejner používat

Jakmile je kontejner v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. Další [příklady](computer-vision-resource-container-config.md) `docker run` příkazu jsou k dispozici. 
1. [Dotaz na koncový bod předpověď kontejneru](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Spusťte nádobu s`docker run`

Ke spuštění kontejneru použijte příkaz [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/) Podrobnosti o tom, jak získat hodnoty `{ENDPOINT_URI}` `{API_KEY}` a, naleznete v [části shromažďování požadovaných parametrů.](#gathering-required-parameters)

[Příklady](computer-vision-resource-container-config.md#example-docker-run-commands) příkazu `docker run` jsou k dispozici.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner pro čtení z image kontejneru.
* Přiděluje 8 procesoru jádro a 16 gigabajtů (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner.
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.

Další [příklady](./computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

Kontejner poskytuje úložiště dat na základě dotazu předpověď koncový bod API. 

Použijte hostitele `http://localhost:5000`, pro kontejnerová řešení API.

### <a name="asynchronous-read"></a>Asynchronní čtení

Můžete použít `POST /vision/v2.0/read/core/asyncBatchAnalyze` a `GET /vision/v2.0/read/operations/{operationId}` operace ve shodě asynchronně číst obrázek, podobně jako služba počítačového zpracování používá tyto odpovídající operace REST. Asynchronní METODA POST vrátí, `operationId` který se používá jako identifer na požadavek HTTP GET.

V unovém ovládacím `asyncBatchAnalyze` místě swagger vyberte, chcete-li jej rozbalit v prohlížeči. Pak vyberte **Vyzkoušet** > **zvolte soubor**. V tomto příkladu použijeme následující obrázek:

![karty vs mezery](media/tabs-vs-spaces.png)

Pokud je asynchronní POST úspěšně spuštěn, vrátí stavový kód **HTTP 202.** Jako součást odpovědi je `operation-location` záhlaví, které obsahuje koncový bod výsledku pro požadavek.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Je `operation-location` plně kvalifikovaná adresa URL a je přístupná přes HTTP GET. Zde je odpověď JSON z `operation-location` provádění URL z předchozího obrázku:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Synchronní čtení

Pomocí `POST /vision/v2.0/read/core/Analyze` této operace můžete synchronně číst obrázek. Při čtení obrázku v plném rozsahu, potom a teprve potom rozhraní API vrátí odpověď JSON. Jedinou výjimkou je, pokud dojde k chybě. Pokud dojde k chybě, je vrácena následující JSON:

```json
{
    status: "Failed"
}
```

Objekt odezvy JSON má stejný objektový graf jako asynchronní verze. Pokud jste uživatel JavaScriptu a chcete bezpečnost typů, následující typy mohou být použity k obsazení odpovědi JSON jako `AnalyzeResult` objekt.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Příklad případu použití naleznete <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">v izolovaném prostoru <span class="docon docon-navigate-external x-hidden-focus"></span> TypeScript zde</a> a vyberte **Spustit,** chcete-li vizualizovat jeho snadnost použití.

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spustíte kontejner s povoleným výstupním [připojením](./computer-vision-resource-container-config.md#mount-settings) a protokolováním, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery služeb Cognitive Services odesílají fakturační údaje do Azure pomocí odpovídajícího prostředku na vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro stahování, instalaci a spouštění kontejnerů počítačového vidění. Souhrn:

* Computer Vision poskytuje linuxový kontejner pro Docker, zapouzdření Read.
* Image kontejnerů se stahují z registru kontejnerů "Container Preview" v Azure.
* Image kontejnerů běží v Dockeru.
* Pomocí rozhraní REST API nebo sady SDK můžete volat operace v kontejnerech read zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instancí kontejneru je nutné zadat fakturační údaje.

> [!IMPORTANT]
> Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají data zákazníků (například obrázek nebo text, který se analyzuje) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Kontrola [Konfigurace kontejnerů](computer-vision-resource-container-config.md) pro nastavení konfigurace
* Další [informace](Home.md) o rozpoznávání tištěného a ručně psaného textu najdete v přehledu informací o rozpoznávání tištěného a ručně psaného textu
* Podrobnosti o metodách podporovaných kontejnerem naleznete v [rozhraní API pro počítačové zpracování obrazu.](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
* Problémy související s funkcí počítačového vidění naleznete v [nejčastějších dotazech.](FAQ.md)
* Použití více [kontejnerů služeb Cognitive Services](../cognitive-services-container-support.md)
