---
title: Jak nainstalovat a spustit kontejnery – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Jak si stáhnout, nainstalovat a spouštění kontejnerů v tomto kurzu návod pro počítačové zpracování obrazu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 51c60c8cd13c8ad7cef123f2001fcd0ec61f38ba
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770794"
---
# <a name="install-and-run-read-containers-preview"></a>Instalace a spuštění kontejnerů pro čtení (Preview)

Kontejnery umožňují spouštět rozhraní API Počítačové zpracování obrazu ve vlastním prostředí. Kontejnery jsou skvělé pro specifické požadavky zabezpečení a zásad správného řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner Počítačové zpracování obrazu.

Pro Počítačové zpracování obrazu je k dispozici jeden kontejner Docker, který je *určen*. Kontejner *pro čtení* umožňuje detekovat a extrahovat *vytištěný text* z obrázků různých objektů s různými povrchy a pozadími, jako jsou například příjmy, plakáty a vizitky. Kromě toho kontejner *pro čtení* detekuje *rukou psaný text* v obrázcích a poskytuje podporu PDF, TIFF a vícestránkového souboru. Další informace najdete v dokumentaci k rozhraní API [pro čtení](concept-recognizing-text.md#read-api) .

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů musíte splnit následující předpoklady:

|Požaduje se|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek Počítačové zpracování obrazu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek Azure **počítačové zpracování obrazu** a přidružený klíč rozhraní API identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehledu a klíčů pro daný prostředek a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}** : jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}** : koncový bod uvedený na stránce **Přehled**|

## <a name="request-access-to-the-private-container-registry"></a>Požádat o přístup k registru kontejneru soukromého

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

K dispozici jsou image kontejneru pro čtení.

| Kontejner | Název Container Registry/úložiště/image |
|-----------|------------|
| Čtení | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

K stažení Image kontejneru použijte příkaz [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) .

### <a name="docker-pull-for-the-read-container"></a>Pull Docker pro kontejner pro čtení

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. K dispozici jsou `docker run` další [příklady](computer-vision-resource-container-config.md) příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner pomocí `docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat `{ENDPOINT_URI}` a `{API_KEY}` hodnoty, najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) .

K dispozici jsou [příklady](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí načtený kontejner z image kontejneru.
* Přiděluje 8 PROCESORových jader a 16 gigabajtů (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

K dispozici jsou `docker run` další [příklady](./computer-vision-resource-container-config.md#example-docker-run-commands) příkazu. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST. 

Pro rozhraní API kontejneru použijte hostitele, `http://localhost:5000`.

### <a name="asynchronous-read"></a>Asynchronní čtení

Můžete použít operace `POST /vision/v2.0/read/core/asyncBatchAnalyze` a `GET /vision/v2.0/read/operations/{operationId}` společně k asynchronnímu čtení obrázku podobným způsobem, jakým služba Počítačové zpracování obrazu používá tyto odpovídající operace REST. Metoda asynchronního POST vrátí `operationId`, který se používá jako identifikátorem požadavku HTTP GET.

V uživatelském rozhraní Swagger vyberte `asyncBatchAnalyze` a rozbalte ho v prohlížeči. Pak vyberte **vyzkoušet,**  > **Zvolte soubor**. V tomto příkladu použijeme následující obrázek:

![tabulátory vs – mezery](media/tabs-vs-spaces.png)

Po úspěšném spuštění asynchronního příspěvku vrátí stavový kód **HTTP 202** . V rámci odpovědi je k dispozici `operation-location` záhlaví, které obsahuje výsledný koncový bod pro požadavek.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` je plně kvalifikovaná adresa URL, ke které se dostanete prostřednictvím HTTP GET. Tady je odpověď JSON pro spuštění `operation-location` URL z předchozího obrázku:

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
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
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

K synchronnímu čtení obrázku můžete použít operaci `POST /vision/v2.0/read/core/Analyze`. Když se image načte v celém rozsahu, pak rozhraní API pak vrátí odpověď JSON. Jedinou výjimkou je situace, kdy dojde k chybě. Pokud dojde k chybě, vrátí se následující JSON:

```json
{
    status: "Failed"
}
```

Objekt odpovědi JSON má stejný graf objektů jako asynchronní verze. Pokud jste uživatelem JavaScriptu a chcete typ zabezpečení, můžete použít následující typy k přetypování odezvy JSON jako objekt `AnalyzeResult`.

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

Příklad použití naleznete <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">tady <span class="docon docon-navigate-external x-hidden-focus"></span> </a> : v karanténě pro TypeScript a výběrem **Spustit** provizualizujte jeho snadné použití.

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](./computer-vision-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Vyúčtování

Kontejnery Cognitive Services odesílají informace o fakturaci do Azure pomocí odpovídajícího prostředku v účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů pro počítačové zpracování obrazu. Souhrn:

* Počítačové zpracování obrazu poskytuje kontejner pro Linux pro Docker, zapouzdření čtení.
* Image kontejnerů se stáhnou z registru kontejnerů "kontejner ve verzi Preview" v Azure.
* Spuštění imagí kontejnerů v Dockeru.
* Pomocí REST API nebo sady SDK můžete volat operace v kontejnerech pro čtení, a to zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Kontejnery Cognitive Services neodesílají zákaznická data (například obrázek nebo analyzovaný text) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Kontrola [konfigurace kontejnery](computer-vision-resource-container-config.md) nastavení konfigurace
* Kontrola [přehled pro počítačové zpracování obrazu](Home.md) Další informace o rozpoznávání tištěné a rukou psaný text
* Odkazovat [rozhraní API pro počítačové zpracování obrazu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podrobné informace o metodách podporuje kontejneru.
* Odkazovat na [– nejčastější dotazy (FAQ)](FAQ.md) k vyřešení problémů týkajících se funkce pro počítačové zpracování obrazu.
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
