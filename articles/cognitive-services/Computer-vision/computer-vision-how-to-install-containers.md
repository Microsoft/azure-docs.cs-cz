---
title: Instalace a spuštění kontejnerů Docker pro rozhraní Počítačové zpracování obrazu Read API
titleSuffix: Azure Cognitive Services
description: Pomocí kontejneru Docker pro rozhraní Počítačové zpracování obrazu Read API můžete detekovat a extrahovat text z imagí místně.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: místní, Docker, kontejner
ms.openlocfilehash: 17aa0050ed3e2a2f5a2cc83964d032caa4b8c098
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460437"
---
# <a name="install-and-run-docker-containers-for-the-read-api-preview"></a>Instalace a spuštění kontejnerů Docker pro rozhraní API pro čtení (Preview) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Kontejnery umožňují spouštět rozhraní API Počítačového zpracování obrazu ve vlastním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner Počítačového zpracování obrazu.

Kontejner *pro čtení* umožňuje detekovat a extrahovat *vytištěný text* z obrázků různých objektů s různými povrchy a pozadími, jako jsou například příjmy, plakáty a vizitky. Kromě toho kontejner *pro čtení* detekuje *rukou psaný text* v obrázcích a poskytuje podporu PDF, TIFF a vícestránkového souboru. Další informace najdete v [dokumentaci k rozhraní API pro čtení](concept-recognizing-text.md#read-api).

Ve verzi Preview jsou k dispozici dvě verze kontejnerů 3. x. Obě verze poskytují další přesnost a funkce oproti předchozímu kontejneru.

Kontejner Read 3,0-Preview poskytuje:
* Nové modely pro vyšší přesnost.
* Podpora více jazyků v rámci jednoho dokumentu
* Podpora: Holandština, angličtina, francouzština, němčina, italština, portugalština a španělština.
* Jedna operace pro dokumenty a obrázky.
* Podpora větších dokumentů a obrázků.
* Výsledky spolehlivosti od 0 do 1.
* Podpora dokumentů pomocí tiskového i rukopisného textu

Kontejner Read 3,1-Preview nabízí stejné výhody jako v 3.0 – Preview s dalšími funkcemi:

* Podpora pro zjednodušenou čínštinu a japonštinu.
* hodnocení a popisky spolehlivosti pro vytištěné a ručně psaný text. 
* Možnost extrahovat text z vybraných stránek v dokumentu.

Když zvažujete, kterou verzi kontejneru chcete použít, Všimněte si, že verze v 3.1 – Preview je ve starší verzi Preview. Pokud dnes používáte kontejnery Read 2,0, přečtěte si informace o změnách v nových verzích v [Průvodci migrací](read-container-migration-guide.md) .

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů musíte splnit následující předpoklady:

|Vyžadováno|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Prostředek Počítačové zpracování obrazu |Aby bylo možné kontejner používat, musíte mít:<br><br>Prostředek Azure **počítačové zpracování obrazu** a přidružený klíč rozhraní API identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehledu a klíčů pro daný prostředek a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}**: jeden ze dvou dostupných klíčů prostředků na stránce **klíče**<br><br>**{ENDPOINT_URI}**: koncový bod uvedený na stránce **Přehled**|

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="request-approval-to-run-the-container"></a>Požádat o schválení ke spuštění kontejneru

Vyplňte a odešlete [formulář žádosti](https://aka.ms/csgate) , který vyžádá schválení pro spuštění kontejneru. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Rozšířená podpora rozšíření Vector

**Hostitelský** počítač je počítač, který spouští kontejner Docker. Hostitel *musí podporovat* [Rozšířená rozšíření Vector](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Podporu AVX2 pro hostitele se systémem Linux můžete vyhledat pomocí následujícího příkazu:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> K podpoře AVX2 se *vyžaduje* hostitelský počítač. *Kontejner nebude* správně fungovat bez podpory AVX2.

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

K dispozici jsou image kontejneru pro čtení.

| Kontejner | Název Container Registry/úložiště/image |
|-----------|------------|
| Přečíst 2,0 – Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Přečíst 3,0 – Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| Přečíst 3,1 – Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu Stáhněte image kontejneru.

### <a name="docker-pull-for-the-read-container"></a>Pull Docker pro kontejner pro čtení

# <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. [examples](computer-vision-resource-container-config.md) `docker run` K dispozici jsou další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat hodnoty a, najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

[Examples](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou příklady příkazů.

# <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí načtený kontejner z image kontejneru.
* Přiděluje 8 PROCESORových jader a 18 gigabajtů (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

Tento příkaz:

* Spustí načtený kontejner z image kontejneru.
* Přiděluje 8 PROCESORových jader a 18 gigabajtů (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí načtený kontejner z image kontejneru.
* Přiděluje 8 PROCESORových jader a 16 gigabajtů (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

---


[examples](./computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).

Pokud potřebujete vyšší propustnost (například při zpracování vícestránkových souborů), zvažte nasazení více kontejnerů v [clusteru Kubernetes](deploy-computer-vision-on-premises.md)v 3.0 nebo v 3.1 pomocí [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create) a [fronty Azure](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction).

Pokud používáte Azure Storage k ukládání imagí ke zpracování, můžete vytvořit [připojovací řetězec](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) , který se použije při volání kontejneru.

Postup vyhledání připojovacího řetězce:

1. V Azure Portal přejděte na **účty úložiště** a Najděte svůj účet.
2. V levém navigačním seznamu klikněte na **přístupové klíče** .
3. Váš připojovací řetězec se bude nacházet pod **připojovacím řetězcem** .

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zadání dotazu do prediktivního koncového bodu kontejneru

Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST. 

# <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-1)

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`. Cestu Swagger můžete zobrazit v umístění: `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`. Cestu Swagger můžete zobrazit v umístění: `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`. Cestu Swagger můžete zobrazit v umístění: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Asynchronní čtení


# <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-1)

Můžete použít `POST /vision/v3.1/read/analyze` `GET /vision/v3.1/read/operations/{operationId}` operace a společně k asynchronnímu čtení obrázku, podobně jako služba počítačové zpracování obrazu používá tyto odpovídající operace REST. Asynchronní metoda POST vrátí `operationId` hodnotu, která se používá jako identifikátorem požadavku HTTP GET.


V uživatelském rozhraní Swagger vyberte `asyncBatchAnalyze` a rozbalte ho v prohlížeči. Pak vyberte **vyzkoušet**pro výběr  >  **souboru**. V tomto příkladu použijeme následující obrázek:

![tabulátory vs – mezery](media/tabs-vs-spaces.png)

Po úspěšném spuštění asynchronního příspěvku vrátí stavový kód **HTTP 202** . V rámci odpovědi je k dispozici `operation-location` záhlaví, které obsahuje výsledný koncový bod pro požadavek.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Je plně kvalifikovaná adresa URL, ke které se dostanete prostřednictvím HTTP GET. Tady je odpověď JSON z vykonání `operation-location` adresy URL z předchozího obrázku:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

Můžete použít `POST /vision/v3.0/read/analyze` `GET /vision/v3.0/read/operations/{operationId}` operace a společně k asynchronnímu čtení obrázku, podobně jako služba počítačové zpracování obrazu používá tyto odpovídající operace REST. Asynchronní metoda POST vrátí `operationId` hodnotu, která se používá jako identifikátorem požadavku HTTP GET.

V uživatelském rozhraní Swagger vyberte `asyncBatchAnalyze` a rozbalte ho v prohlížeči. Pak vyberte **vyzkoušet**pro výběr  >  **souboru**. V tomto příkladu použijeme následující obrázek:

![tabulátory vs – mezery](media/tabs-vs-spaces.png)

Po úspěšném spuštění asynchronního příspěvku vrátí stavový kód **HTTP 202** . V rámci odpovědi je k dispozici `operation-location` záhlaví, které obsahuje výsledný koncový bod pro požadavek.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Je plně kvalifikovaná adresa URL, ke které se dostanete prostřednictvím HTTP GET. Tady je odpověď JSON z vykonání `operation-location` adresy URL z předchozího obrázku:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

Můžete použít `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` operace a společně k asynchronnímu čtení obrázku, podobně jako služba počítačové zpracování obrazu používá tyto odpovídající operace REST. Asynchronní metoda POST vrátí `operationId` hodnotu, která se používá jako identifikátorem požadavku HTTP GET.

V uživatelském rozhraní Swagger vyberte `asyncBatchAnalyze` a rozbalte ho v prohlížeči. Pak vyberte **vyzkoušet**pro výběr  >  **souboru**. V tomto příkladu použijeme následující obrázek:

![tabulátory vs – mezery](media/tabs-vs-spaces.png)

Po úspěšném spuštění asynchronního příspěvku vrátí stavový kód **HTTP 202** . V rámci odpovědi je k dispozici `operation-location` záhlaví, které obsahuje výsledný koncový bod pro požadavek.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Je plně kvalifikovaná adresa URL, ke které se dostanete prostřednictvím HTTP GET. Tady je odpověď JSON z vykonání `operation-location` adresy URL z předchozího obrázku:

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

---

> [!IMPORTANT]
> Pokud nasadíte více kontejnerů pro čtení za nástroj pro vyrovnávání zatížení, například v části Docker Compose nebo Kubernetes, musíte mít externí mezipaměť. Vzhledem k tomu, že kontejner zpracování a kontejner požadavků GET nemusí být stejné, externí mezipaměť ukládá výsledky a sdílí je napříč kontejnery. Podrobnosti o nastavení mezipaměti najdete v tématu [konfigurace kontejnerů docker počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).

### <a name="synchronous-read"></a>Synchronní čtení

K synchronnímu čtení obrázku můžete použít následující operaci. 

# <a name="version-31-preview"></a>[Verze 3,1-Preview](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[Verze 3,0-Preview](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Když se image načte v celém rozsahu, pak rozhraní API pak vrátí odpověď JSON. Jedinou výjimkou je situace, kdy dojde k chybě. Pokud dojde k chybě, vrátí se následující JSON:

```json
{
    "status": "Failed"
}
```

Objekt odpovědi JSON má stejný graf objektů jako asynchronní verze. Pokud jste uživatelem JavaScriptu a chcete mít bezpečnost typů, zvažte použití TypeScriptu k přetypování odpovědi JSON.

Příklad použití naleznete <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">tady <span class="docon docon-navigate-external x-hidden-focus"></span> </a> : v karanténě pro TypeScript a výběrem **Spustit** provizualizujte jeho snadné použití.

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](./computer-vision-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery Cognitive Services odesílají informace o fakturaci do Azure pomocí odpovídajícího prostředku v účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů Počítačové zpracování obrazu. Souhrn:

* Počítačové zpracování obrazu poskytuje kontejner pro Linux pro Docker, zapouzdření čtení.
* Image kontejnerů se stáhnou z registru kontejnerů "kontejner ve verzi Preview" v Azure.
* Image kontejneru se spouštějí v Docker.
* Pomocí REST API nebo sady SDK můžete volat operace v kontejnerech pro čtení, a to zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru je nutné zadat informace o fakturaci.

> [!IMPORTANT]
> Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data (například obrázek nebo analyzovaný text) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](computer-vision-resource-container-config.md) pro nastavení konfigurace
* Přečtěte si [počítačové zpracování obrazu přehled](overview.md) , kde najdete další informace o rozpoznávání vytištěného a rukopisného textu.
* Podrobnosti o metodách podporovaných kontejnerem najdete v [rozhraní API pro počítačové zpracování obrazu](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* Přečtěte si [Nejčastější dotazy](FAQ.md) k řešení problémů souvisejících se počítačové zpracování obrazu funkcemi.
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
