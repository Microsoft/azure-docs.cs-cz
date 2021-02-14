---
title: Nainstalovat čtení kontejnerů Docker pro rozpoznávání OCR z Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: K extrakci textu z obrázků a dokumentů v místním prostředí použijte kontejnery Docker pro rozpoznávání OCR z Počítačové zpracování obrazu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: místní, OCR, Docker, kontejner
ms.openlocfilehash: 843000963bc05cab5415c9bb1db32b3272c8dc9f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391762"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Nainstalovat čtení kontejnerů Docker pro optické rozpoznávání znaků (Preview) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Kontejnery umožňují spouštět rozhraní API Počítačového zpracování obrazu ve vlastním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejnery Počítačové zpracování obrazu.

Kontejner OCR *pro čtení* umožňuje extrahovat vytištěný a rukou psaný text z obrázků a dokumentů s podporou formátů souborů JPEG, PNG, BMP, PDF a TIFF. Další informace najdete v [dokumentaci k rozhraní API pro čtení](concept-recognizing-text.md#read-api).

## <a name="read-32-preview-container"></a>Přečíst kontejner 3,2 – Preview

> [!NOTE]
> Kontejner Read 3,0-Preview se už nepoužívá. 

Kontejner OCR Read 3,2-Preview poskytuje:
* Nové modely pro vyšší přesnost.
* Podpora více jazyků v rámci jednoho dokumentu.
* Podpora celkem 73 jazyků. Podívejte se na úplný seznam [jazyků podporovaných rozpoznáváním OCR](./language-support.md#optical-character-recognition-ocr).
* Jedna operace pro dokumenty a obrázky.
* Podpora větších dokumentů a obrázků.
* Hodnocení spolehlivosti.
* Podpora dokumentů pomocí tiskového i rukopisného textu.
* Možnost extrahovat text z vybraných stránek v dokumentu.
* Vyberte možnost pořadí výstupu z textového řádku z výchozí hodnoty do přirozeného pořadí čtení jenom pro jazyky latinky.
* Klasifikace řádku textu jako ručně psaný styl nebo ne pro jazyky Latin.

Pokud dnes používáte kontejnery Read 2,0, přečtěte si informace o změnách v nových verzích v [Průvodci migrací](read-container-migration-guide.md) .

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů musíte splnit následující předpoklady:

|Vyžadováno|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows** musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
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
| Přečíst 3,2 – Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu Stáhněte image kontejneru.

### <a name="docker-pull-for-the-read-container"></a>Pull Docker pro kontejner pro čtení

# <a name="version-32-preview"></a>[Verze 3,2-Preview](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
```

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným nastavením fakturace. [](computer-vision-resource-container-config.md) `docker run` K dispozici jsou další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat hodnoty a, najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

[](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou příklady příkazů.

# <a name="version-32-preview"></a>[Verze 3,2-Preview](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
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


[](./computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).

Pokud potřebujete vyšší propustnost (například při zpracování vícestránkových souborů), zvažte nasazení více kontejnerů [v clusteru Kubernetes](deploy-computer-vision-on-premises.md)pomocí [Azure Storage](../../storage/common/storage-account-create.md) a [fronty Azure](../../storage/queues/storage-queues-introduction.md).

Pokud používáte Azure Storage k ukládání imagí ke zpracování, můžete vytvořit [připojovací řetězec](../../storage/common/storage-configure-connection-string.md) , který se použije při volání kontejneru.

Postup vyhledání připojovacího řetězce:

1. V Azure Portal přejděte na **účty úložiště** a Najděte svůj účet.
2. V levém navigačním seznamu klikněte na **přístupové klíče** .
3. Váš připojovací řetězec se bude nacházet pod **připojovacím řetězcem** .

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Zadání dotazu do prediktivního koncového bodu kontejneru

Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST. 

# <a name="version-32-preview"></a>[Verze 3,2-Preview](#tab/version-3-2)

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`. Cestu Swagger můžete zobrazit v umístění: `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Verze 2,0-Preview](#tab/version-2)

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`. Cestu Swagger můžete zobrazit v umístění: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Asynchronní čtení


# <a name="version-32-preview"></a>[Verze 3,2-Preview](#tab/version-3-2)

Můžete použít `POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` operace a společně k asynchronnímu čtení obrázku, podobně jako služba počítačové zpracování obrazu používá tyto odpovídající operace REST. Asynchronní metoda POST vrátí `operationId` hodnotu, která se používá jako identifikátorem požadavku HTTP GET.


V uživatelském rozhraní Swagger vyberte `Analyze` a rozbalte ho v prohlížeči. Pak vyberte **vyzkoušet** pro výběr  >  **souboru**. V tomto příkladu použijeme následující obrázek:

![tabulátory vs – mezery](media/tabs-vs-spaces.png)

Po úspěšném spuštění asynchronního příspěvku vrátí stavový kód **HTTP 202** . V rámci odpovědi je k dispozici `operation-location` záhlaví, které obsahuje výsledný koncový bod pro požadavek.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Je plně kvalifikovaná adresa URL, ke které se dostanete prostřednictvím HTTP GET. Tady je odpověď JSON z vykonání `operation-location` adresy URL z předchozího obrázku:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
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

V uživatelském rozhraní Swagger vyberte `asyncBatchAnalyze` a rozbalte ho v prohlížeči. Pak vyberte **vyzkoušet** pro výběr  >  **souboru**. V tomto příkladu použijeme následující obrázek:

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
> Pokud nasadíte více kontejnerů pro čtení za nástroj pro vyrovnávání zatížení, například v části Docker Compose nebo Kubernetes, musíte mít externí mezipaměť. Vzhledem k tomu, že kontejner zpracování a kontejner požadavků GET nemusí být stejné, externí mezipaměť ukládá výsledky a sdílí je napříč kontejnery. Podrobnosti o nastavení mezipaměti najdete v tématu [konfigurace kontejnerů docker počítačové zpracování obrazu](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Synchronní čtení

K synchronnímu čtení obrázku můžete použít následující operaci. 

# <a name="version-32-preview"></a>[Verze 3,2-Preview](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

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

Příklad použití naleznete <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">tady <span class="docon docon-navigate-external x-hidden-focus"></span></a> : v karanténě pro TypeScript a výběrem **Spustit** provizualizujte jeho snadné použití.

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](./computer-vision-resource-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejnery Cognitive Services odesílají informace o fakturaci do Azure pomocí odpovídajícího prostředku v účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](./computer-vision-resource-container-config.md).

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
* Podrobnosti o metodách podporovaných kontejnerem najdete v [rozhraní API pro počítačové zpracování obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) .
* Přečtěte si [Nejčastější dotazy](FAQ.md) k řešení problémů souvisejících se počítačové zpracování obrazu funkcemi.
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)
