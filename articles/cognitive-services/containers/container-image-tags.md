---
title: Značky imagí kontejnerů Cognitive Services
titleSuffix: Azure Cognitive Services
description: Úplný seznam všech značek imagí kontejneru služby vnímání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: ba51776942ad28fc8d4b0db7dd2d0e162e5322b5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743333"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Značky imagí a poznámky k verzi služby Azure Cognitive Services Container

Azure Cognitive Services nabízí mnoho imagí kontejneru. Registry kontejnerů a odpovídající úložiště se v různých imagích kontejnerů liší. Každý název Image kontejneru nabízí více značek. Značka image kontejneru je mechanismus správy verzí image kontejneru. Tento článek je určený k použití jako ucelený odkaz pro výpis všech Cognitive Servicesch imagí kontejnerů a jejich dostupných značek.

> [!TIP]
> Při použití [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) aplikace věnujte velkou pozornost pro velká a malá písmena registru kontejnerů, úložiště, názvu image kontejneru a odpovídajících značek – protože rozlišují **velká a malá písmena**.

## <a name="anomaly-detector"></a>Detektor anomálií

Image kontejneru [detektoru anomálií][ad-containers] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services/decision` úložišti a má název `anomaly-detector` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Předchozí verze](#tab/previous)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Čtení OCR (optické rozpoznávání znaků)

[Počítačové zpracování obrazu][cv-containers] čtení kontejneru rozpoznávání OCR se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services` úložišti a má název `read` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámky k verzi pro `v2.0.013250001-amd64-preview` :

* Další snížení využití paměti pro kontejner.
* Pro nastavení více pisoárů se vyžaduje externí mezipaměť. Například nastavte Redis pro ukládání do mezipaměti.
* Opravené chybějící výsledky při nastavení Redis Cache je nastavené `ResultExpirationPeriod` na 0.
* Omezení velikosti textu požadavku 26MB. Kontejner teď může přijímat >soubory 26MB.
* Přidejte časové razítko a sestavte verzi do protokolování konzoly.

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Předchozí verze](#tab/previous)

Poznámky k verzi pro `1.1.013050001-amd64-preview`

* Přidání `ReadEngineConfig:ResultExpirationPeriod` inicializační konfigurace kontejneru pro určení, kdy má systém vyčistit výsledky rozpoznávání. 
    * Nastavení je v hodinách a výchozí hodnota je 48 hodin.
    * Nastavení může snížit využití paměti pro ukládání výsledků, zejména v případě, že je použito úložiště kontejneru v paměti.
    * Příklad 1 ReadEngineConfig: ResultExpirationPeriod = 1, systém vymaže výsledek rozpoznávání 1hr po dokončení procesu.
    * Pokud je tato konfigurace nastavená na hodnotu 0, systém vymaže výsledek rozpoznávání po načtení výsledku.

* Při předání neplatného formátu obrázku do systému byla opravena interní chyba serveru 500. Nyní se vrátí 400 chyba:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Rozpoznávání formulářů

Image kontejneru [pro rozpoznávání formulářů][fr-containers] se dá najít v `mcr.microsoft.com` kontejneru kontejnerů. Je uložený v `azure-cognitive-services/custom-form` úložišti a má název `labeltool` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Předchozí verze](#tab/previous)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

Image kontejneru [Luis][lu-containers] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services/language` úložišti a má název `luis` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech na text

Obrázek kontejneru [Custom Speech-to-text][sp-cstt] najdete v části `mcr.microsoft.com` kontejner registru kontejneru. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `custom-speech-to-text` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámka k verzi pro `2.6.0-amd64` :

**Funkce**
* Podpora pro PhraseList v2 
* Seznamy frází jsou podporovány v následujících národních prostředích:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * EN-NZ
    * cs-cz
    * zh-cn
* Podpora pro stažení vlastního základního modelu. 
    * Použít `BaseModelLocale=<locale>` s `docker run` příkazem
* Úplná migrace do .NET 3,1

**Řeší**
* Opravili jsme problém, kdy bylo skóre spolehlivosti vždy 1 v režimu Diarization
* Migrováno do rozhraní TextAnalytics 3,0 API

Všimněte si, že vzhledem k uvedeným seznamům frází se velikost této image kontejneru zvýšila.

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

Poznámka k verzi pro `2.5.0-amd64` :

**Funkce**
* Podpora vlastní výslovnosti pro vlastní modely
* Podpora cloudu Azure a Azure pro státní správu USA

**Řeší**
* Oprava potíží spustit jako nerootového uživatele v režimu Diarization

| Značky obrázku                    | Poznámky               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   první verze GA    |

---

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní image kontejneru [pro převod textu na mluvené slovo][sp-ctts] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `custom-text-to-speech` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámka k verzi pro `1.8.0-amd64` :

**Funkce**
* Úplná migrace do .NET 3,1

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

Poznámka k verzi pro `1.7.0-amd64` :

**Funkce**
* Částečně migrováno na rozhraní .NET 3,1

| Značky obrázku                    | Poznámky               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   první verze GA    |

---

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek s obrázkem [mluveného řeči na text][sp-stt] najdete v části `mcr.microsoft.com` kontejner kontejneru kontejnerů. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `speech-to-text` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Můžete najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Vzhledem k tomu, že je převod řeči na text v 2.5.0, jsou obrázky podporované v oblasti *) – Virginia státní správy USA* . Při použití této oblasti prosím použijte koncový bod fakturace *) – Virginia státní správy USA* a klíče rozhraní API.

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámka k verzi pro `2.6.0-amd64-<locale>` :

**Funkce**
* Upgrade na nejnovější modely a úplná migrace na .NET 3,1
* Podpora pro PhraseList v2
* Seznamy frází jsou podporovány v následujících národních prostředích:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * EN-NZ
    * cs-cz
    * zh-cn
* Podpora nového národního prostředí `cs-CZ` 
    * Malá a velká písmena nejsou v současné době podporována.

**Řeší**
* Opravuje problém s přesností na 1 v režimu Diarization
* Migrace pomocí rozhraní TextAnalytics 3,0 API

Všimněte si, že vzhledem k uvedeným seznamům frází se velikost této image kontejneru zvýšila. 

| Značky obrázku                    | Poznámky                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Obrázek kontejneru s `en-US` národním prostředím                                                             |
| `2.6.0-amd64-<locale>`        | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `2.6.0-amd64-en-us`. |

Pro tento kontejner jsou k dispozici následující místní prostředí.

| Národní prostředí pro 2.6.0           | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Obrázek kontejneru s `ar-AE` národním prostředím |
| `ar-eg`                     | Obrázek kontejneru s `ar-EG` národním prostředím |
| `ar-kw`                     | Obrázek kontejneru s `ar-KW` národním prostředím |
| `ar-qa`                     | Obrázek kontejneru s `ar-QA` národním prostředím |
| `ar-sa`                     | Obrázek kontejneru s `ar-SA` národním prostředím |
| `ca-es`                     | Obrázek kontejneru s `ca-ES` národním prostředím |
| `cs-cz`                     | Obrázek kontejneru s `cs-CZ` národním prostředím |
| `da-dk`                     | Obrázek kontejneru s `da-DK` národním prostředím |
| `de-de`                     | Obrázek kontejneru s `de-DE` národním prostředím |
| `en-au`                     | Obrázek kontejneru s `en-AU` národním prostředím |
| `en-ca`                     | Obrázek kontejneru s `en-CA` národním prostředím |
| `en-gb`                     | Obrázek kontejneru s `en-GB` národním prostředím |
| `en-in`                     | Obrázek kontejneru s `en-IN` národním prostředím |
| `en-nz`                     | Obrázek kontejneru s `en-NZ` národním prostředím |
| `en-us`                     | Obrázek kontejneru s `en-US` národním prostředím |
| `es-es`                     | Obrázek kontejneru s `es-ES` národním prostředím |
| `es-mx`                     | Obrázek kontejneru s `es-MX` národním prostředím |
| `fi-fi`                     | Obrázek kontejneru s `fi-FI` národním prostředím |
| `fr-ca`                     | Obrázek kontejneru s `fr-CA` národním prostředím |
| `fr-fr`                     | Obrázek kontejneru s `fr-FR` národním prostředím |
| `gu-in`                     | Obrázek kontejneru s `gu-IN` národním prostředím |
| `hi-in`                     | Obrázek kontejneru s `hi-IN` národním prostředím |
| `it-it`                     | Obrázek kontejneru s `it-IT` národním prostředím |
| `ja-jp`                     | Obrázek kontejneru s `ja-JP` národním prostředím |
| `ko-kr`                     | Obrázek kontejneru s `ko-KR` národním prostředím |
| `mr-in`                     | Obrázek kontejneru s `mr-IN` národním prostředím |
| `nb-no`                     | Obrázek kontejneru s `nb-NO` národním prostředím |
| `nl-nl`                     | Obrázek kontejneru s `nl-NL` národním prostředím |
| `pl-pl`                     | Obrázek kontejneru s `pl-PL` národním prostředím |
| `pt-br`                     | Obrázek kontejneru s `pt-BR` národním prostředím |
| `pt-pt`                     | Obrázek kontejneru s `pt-PT` národním prostředím |
| `ru-ru`                     | Obrázek kontejneru s `ru-RU` národním prostředím |
| `sv-se`                     | Obrázek kontejneru s `sv-SE` národním prostředím |
| `ta-in`                     | Obrázek kontejneru s `ta-IN` národním prostředím |
| `te-in`                     | Obrázek kontejneru s `te-IN` národním prostředím |
| `th-th`                     | Obrázek kontejneru s `th-TH` národním prostředím |
| `tr-tr`                     | Obrázek kontejneru s `tr-TR` národním prostředím |
| `zh-cn`                     | Obrázek kontejneru s `zh-CN` národním prostředím |
| `zh-hk`                     | Obrázek kontejneru s `zh-HK` národním prostředím |
| `zh-tw`                     | Obrázek kontejneru s `zh-TW` národním prostředím |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

Poznámka k verzi pro `2.5.0-amd64-<locale>` :

**Funkce**
* Podpora cloudu Azure USA pro státní správu

**Řeší**
* Opravuje problém, který se spouští jako nerootový uživatel v režimu Diarization.

| Značky obrázku                  | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `2.5.0-amd64-en-us`.  |

Pro tento kontejner jsou k dispozici následující místní prostředí.

| Národní prostředí pro 2.5.0           | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Obrázek kontejneru s `ar-AE` národním prostředím |
| `ar-eg`                     | Obrázek kontejneru s `ar-EG` národním prostředím |
| `ar-kw`                     | Obrázek kontejneru s `ar-KW` národním prostředím |
| `ar-qa`                     | Obrázek kontejneru s `ar-QA` národním prostředím |
| `ar-sa`                     | Obrázek kontejneru s `ar-SA` národním prostředím |
| `ca-es`                     | Obrázek kontejneru s `ca-ES` národním prostředím |
| `da-dk`                     | Obrázek kontejneru s `da-DK` národním prostředím |
| `de-de`                     | Obrázek kontejneru s `de-DE` národním prostředím |
| `en-au`                     | Obrázek kontejneru s `en-AU` národním prostředím |
| `en-ca`                     | Obrázek kontejneru s `en-CA` národním prostředím |
| `en-gb`                     | Obrázek kontejneru s `en-GB` národním prostředím |
| `en-in`                     | Obrázek kontejneru s `en-IN` národním prostředím |
| `en-nz`                     | Obrázek kontejneru s `en-NZ` národním prostředím |
| `en-us`                     | Obrázek kontejneru s `en-US` národním prostředím |
| `es-es`                     | Obrázek kontejneru s `es-ES` národním prostředím |
| `es-mx`                     | Obrázek kontejneru s `es-MX` národním prostředím |
| `fi-fi`                     | Obrázek kontejneru s `fi-FI` národním prostředím |
| `fr-ca`                     | Obrázek kontejneru s `fr-CA` národním prostředím |
| `fr-fr`                     | Obrázek kontejneru s `fr-FR` národním prostředím |
| `gu-in`                     | Obrázek kontejneru s `gu-IN` národním prostředím |
| `hi-in`                     | Obrázek kontejneru s `hi-IN` národním prostředím |
| `it-it`                     | Obrázek kontejneru s `it-IT` národním prostředím |
| `ja-jp`                     | Obrázek kontejneru s `ja-JP` národním prostředím |
| `ko-kr`                     | Obrázek kontejneru s `ko-KR` národním prostředím |
| `mr-in`                     | Obrázek kontejneru s `mr-IN` národním prostředím |
| `nb-no`                     | Obrázek kontejneru s `nb-NO` národním prostředím |
| `nl-nl`                     | Obrázek kontejneru s `nl-NL` národním prostředím |
| `pl-pl`                     | Obrázek kontejneru s `pl-PL` národním prostředím |
| `pt-br`                     | Obrázek kontejneru s `pt-BR` národním prostředím |
| `pt-pt`                     | Obrázek kontejneru s `pt-PT` národním prostředím |
| `ru-ru`                     | Obrázek kontejneru s `ru-RU` národním prostředím |
| `sv-se`                     | Obrázek kontejneru s `sv-SE` národním prostředím |
| `ta-in`                     | Obrázek kontejneru s `ta-IN` národním prostředím |
| `te-in`                     | Obrázek kontejneru s `te-IN` národním prostředím |
| `th-th`                     | Obrázek kontejneru s `th-TH` národním prostředím |
| `tr-tr`                     | Obrázek kontejneru s `tr-TR` národním prostředím |
| `zh-cn`                     | Obrázek kontejneru s `zh-CN` národním prostředím |
| `zh-hk`                     | Obrázek kontejneru s `zh-HK` národním prostředím |
| `zh-tw`                     | Obrázek kontejneru s `zh-TW` národním prostředím |

---

## <a name="text-to-speech"></a>Převod textu na řeč

Obrázek kontejneru [Převod textu na mluvené slovo][sp-tts] najdete v části `mcr.microsoft.com` kontejner kontejneru v registru. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `text-to-speech` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámka k verzi pro `1.8.0-amd64-<locale-and-voice>` :

**Funkce**
* Úplná migrace do .NET 3,1

| Značky obrázku                                  | Poznámky                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaRUS` hlasem                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.8.0-amd64-en-us-ariarus`.  |


| Národní prostředí pro v 1.8.0                          | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Obrázek kontejneru s `ar-EG` národním prostředím a `ar-EG-Hoda` hlasem            |
| `ar-sa-naayf`                               | Obrázek kontejneru s `ar-SA` národním prostředím a `ar-SA-Naayf` hlasem           |
| `bg-bg-ivan`                                | Obrázek kontejneru s `bg-BG` národním prostředím a `bg-BG-Ivan` hlasem            |
| `ca-es-herenarus`                           | Obrázek kontejneru s `ca-ES` národním prostředím a `ca-ES-HerenaRUS` hlasem       |
| `cs-cz-jakub`                               | Obrázek kontejneru s `cs-CZ` národním prostředím a `cs-CZ-Jakub` hlasem           |
| `da-dk-hellerus`                            | Obrázek kontejneru s `da-DK` národním prostředím a `da-DK-HelleRUS` hlasem        |
| `de-at-michael`                             | Obrázek kontejneru s `de-AT` národním prostředím a `de-AT-Michael` hlasem         |
| `de-ch-karsten`                             | Obrázek kontejneru s `de-CH` národním prostředím a `de-CH-Karsten` hlasem         |
| `de-de-hedda`                               | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `de-de-heddarus`                            | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `de-de-stefan-apollo`                       | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo` hlasem   |
| `el-gr-stefanos`                            | Obrázek kontejneru s `el-GR` národním prostředím a `el-GR-Stefanos` hlasem        |
| `en-au-catherine`                           | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine` hlasem       |
| `en-au-hayleyrus`                           | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS` hlasem       |
| `en-ca-heatherrus`                          | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-HeatherRUS` hlasem      |
| `en-ca-linda`                               | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-Linda` hlasem           |
| `en-gb-george-apollo`                       | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo` hlasem   |
| `en-gb-hazelrus`                            | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS` hlasem        |
| `en-gb-susan-apollo`                        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo` hlasem    |
| `en-ie-sean`                                | Obrázek kontejneru s `en-IE` národním prostředím a `en-IE-Sean` hlasem            |
| `en-in-heera-apollo`                        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo` hlasem    |
| `en-in-priyarus`                            | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS` hlasem        |
| `en-in-ravi-apollo`                         | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo` hlasem     |
| `en-us-benjaminrus`                         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     |
| `en-us-guy24krus`                           | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       |
| `en-us-aria24krus`                          | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Aria24kRUS` hlasem      |
| `en-us-ariarus`                             | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaRUS` hlasem         |
| `en-us-zirarus`                             | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         |
| `es-es-helenarus`                           | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS` hlasem       |
| `es-es-laura-apollo`                        | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo` hlasem    |
| `es-es-pablo-apollo`                        | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo` hlasem    |
| `es-mx-hildarus`                            | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS` hlasem        |
| `es-mx-raul-apollo`                         | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo` hlasem     |
| `fi-fi-heidirus`                            | Obrázek kontejneru s `fi-FI` národním prostředím a `fi-FI-HeidiRUS` hlasem        |
| `fr-ca-caroline`                            | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline` hlasem        |
| `fr-ca-harmonierus`                         | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS` hlasem     |
| `fr-ch-guillaume`                           | Obrázek kontejneru s `fr-CH` národním prostředím a `fr-CH-Guillaume` hlasem       |
| `fr-fr-hortenserus`                         | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS` hlasem     |
| `fr-fr-julie-apollo`                        | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo` hlasem    |
| `fr-fr-paul-apollo`                         | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo` hlasem     |
| `he-il-asaf`                                | Obrázek kontejneru s `he-IL` národním prostředím a `he-IL-Asaf` hlasem            |
| `hi-in-hemant`                              | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Hemant` hlasem          |
| `hi-in-kalpana-apollo`                      | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana-Apollo` hlasem  |
| `hi-in-kalpana`                             | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana` hlasem         |
| `hr-hr-matej`                               | Obrázek kontejneru s `hr-HR` národním prostředím a `hr-HR-Matej` hlasem           |
| `hu-hu-szabolcs`                            | Obrázek kontejneru s `hu-HU` národním prostředím a `hu-HU-Szabolcs` hlasem        |
| `id-id-andika`                              | Obrázek kontejneru s `id-ID` národním prostředím a `id-ID-Andika` hlasem          |
| `it-it-cosimo-apollo`                       | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo` hlasem   |
| `it-it-luciarus`                            | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS` hlasem        |
| `ja-jp-ayumi-apollo`                        | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo` hlasem    |
| `ja-jp-harukarus`                           | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS` hlasem       |
| `ja-jp-ichiro-apollo`                       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo` hlasem   |
| `ko-kr-heamirus`                            | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS` hlasem        |
| `ms-my-rizwan`                              | Obrázek kontejneru s `ms-MY` národním prostředím a `ms-MY-Rizwan` hlasem          |
| `nb-no-huldarus`                            | Obrázek kontejneru s `nb-NO` národním prostředím a `nb-NO-HuldaRUS` hlasem        |
| `nl-nl-hannarus`                            | Obrázek kontejneru s `nl-NL` národním prostředím a `nl-NL-HannaRUS` hlasem        |
| `pl-pl-paulinarus`                          | Obrázek kontejneru s `pl-PL` národním prostředím a `pl-PL-PaulinaRUS` hlasem      |
| `pt-br-daniel-apollo`                       | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo` hlasem   |
| `pt-br-heloisarus`                          | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS` hlasem      |
| `pt-pt-heliarus`                            | Obrázek kontejneru s `pt-PT` národním prostředím a `pt-PT-HeliaRUS` hlasem        |
| `ro-ro-andrei`                              | Obrázek kontejneru s `ro-RO` národním prostředím a `ro-RO-Andrei` hlasem          |
| `ru-ru-ekaterinarus`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-EkaterinaRUS` hlasem    |
| `ru-ru-irina-apollo`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Irina-Apollo` hlasem    |
| `ru-ru-pavel-apollo`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Pavel-Apollo` hlasem    |
| `sk-sk-filip`                               | Obrázek kontejneru s `sk-SK` národním prostředím a `sk-SK-Filip` hlasem           |
| `sl-si-lado`                                | Obrázek kontejneru s `sl-SI` národním prostředím a `sl-SI-Lado` hlasem            |
| `sv-se-hedvigrus`                           | Obrázek kontejneru s `sv-SE` národním prostředím a `sv-SE-HedvigRUS` hlasem       |
| `ta-in-valluvar`                            | Obrázek kontejneru s `ta-IN` národním prostředím a `ta-IN-Valluvar` hlasem        |
| `te-in-chitra`                              | Obrázek kontejneru s `te-IN` národním prostředím a `te-IN-Chitra` hlasem          |
| `th-th-pattara`                             | Obrázek kontejneru s `th-TH` národním prostředím a `th-TH-Pattara` hlasem         |
| `tr-tr-sedarus`                             | Obrázek kontejneru s `tr-TR` národním prostředím a `tr-TR-SedaRUS` hlasem         |
| `vi-vn-an`                                  | Obrázek kontejneru s `vi-VN` národním prostředím a `vi-VN-An` hlasem              |
| `zh-cn-huihuirus`                           | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       |
| `zh-cn-kangkang-apollo`                     | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem |
| `zh-cn-yaoyao-apollo`                       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   |
| `zh-hk-danny-apollo`                        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Danny-Apollo` hlasem    |
| `zh-hk-tracy-apollo`                        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Tracy-Apollo` hlasem    |
| `zh-hk-tracyrus`                            | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-TracyRUS` hlasem        |
| `zh-tw-hanhanrus`                           | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-HanHanRUS` hlasem       |
| `zh-tw-yating-apollo`                       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Yating-Apollo` hlasem   |
| `zh-tw-zhiwei-apollo`                       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Zhiwei-Apollo` hlasem   |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

Poznámka k verzi pro `1.7.0-amd64-<locale-and-voice>` :

**Funkce**
* Upgradované komponenty na .NET 3,1

| Značky obrázku                                  | Poznámky                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | první verze GA Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.7.0-amd64-en-us-ariarus`.  |


| Národní prostředí pro v 1.7.0                          | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Obrázek kontejneru s `ar-EG` národním prostředím a `ar-EG-Hoda` hlasem            |
| `ar-sa-naayf`                               | Obrázek kontejneru s `ar-SA` národním prostředím a `ar-SA-Naayf` hlasem           |
| `bg-bg-ivan`                                | Obrázek kontejneru s `bg-BG` národním prostředím a `bg-BG-Ivan` hlasem            |
| `ca-es-herenarus`                           | Obrázek kontejneru s `ca-ES` národním prostředím a `ca-ES-HerenaRUS` hlasem       |
| `cs-cz-jakub`                               | Obrázek kontejneru s `cs-CZ` národním prostředím a `cs-CZ-Jakub` hlasem           |
| `da-dk-hellerus`                            | Obrázek kontejneru s `da-DK` národním prostředím a `da-DK-HelleRUS` hlasem        |
| `de-at-michael`                             | Obrázek kontejneru s `de-AT` národním prostředím a `de-AT-Michael` hlasem         |
| `de-ch-karsten`                             | Obrázek kontejneru s `de-CH` národním prostředím a `de-CH-Karsten` hlasem         |
| `de-de-hedda`                               | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `de-de-heddarus`                            | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           |
| `de-de-stefan-apollo`                       | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo` hlasem   |
| `el-gr-stefanos`                            | Obrázek kontejneru s `el-GR` národním prostředím a `el-GR-Stefanos` hlasem        |
| `en-au-catherine`                           | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine` hlasem       |
| `en-au-hayleyrus`                           | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS` hlasem       |
| `en-ca-heatherrus`                          | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-HeatherRUS` hlasem      |
| `en-ca-linda`                               | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-Linda` hlasem           |
| `en-gb-george-apollo`                       | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo` hlasem   |
| `en-gb-hazelrus`                            | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS` hlasem        |
| `en-gb-susan-apollo`                        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo` hlasem    |
| `en-ie-sean`                                | Obrázek kontejneru s `en-IE` národním prostředím a `en-IE-Sean` hlasem            |
| `en-in-heera-apollo`                        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo` hlasem    |
| `en-in-priyarus`                            | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS` hlasem        |
| `en-in-ravi-apollo`                         | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo` hlasem     |
| `en-us-benjaminrus`                         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     |
| `en-us-guy24krus`                           | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       |
| `en-us-aria24krus`                          | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Aria24kRUS` hlasem      |
| `en-us-ariarus`                             | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaRUS` hlasem         |
| `en-us-zirarus`                             | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         |
| `es-es-helenarus`                           | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS` hlasem       |
| `es-es-laura-apollo`                        | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo` hlasem    |
| `es-es-pablo-apollo`                        | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo` hlasem    |
| `es-mx-hildarus`                            | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS` hlasem        |
| `es-mx-raul-apollo`                         | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo` hlasem     |
| `fi-fi-heidirus`                            | Obrázek kontejneru s `fi-FI` národním prostředím a `fi-FI-HeidiRUS` hlasem        |
| `fr-ca-caroline`                            | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline` hlasem        |
| `fr-ca-harmonierus`                         | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS` hlasem     |
| `fr-ch-guillaume`                           | Obrázek kontejneru s `fr-CH` národním prostředím a `fr-CH-Guillaume` hlasem       |
| `fr-fr-hortenserus`                         | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS` hlasem     |
| `fr-fr-julie-apollo`                        | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo` hlasem    |
| `fr-fr-paul-apollo`                         | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo` hlasem     |
| `he-il-asaf`                                | Obrázek kontejneru s `he-IL` národním prostředím a `he-IL-Asaf` hlasem            |
| `hi-in-hemant`                              | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Hemant` hlasem          |
| `hi-in-kalpana-apollo`                      | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana-Apollo` hlasem  |
| `hi-in-kalpana`                             | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana` hlasem         |
| `hr-hr-matej`                               | Obrázek kontejneru s `hr-HR` národním prostředím a `hr-HR-Matej` hlasem           |
| `hu-hu-szabolcs`                            | Obrázek kontejneru s `hu-HU` národním prostředím a `hu-HU-Szabolcs` hlasem        |
| `id-id-andika`                              | Obrázek kontejneru s `id-ID` národním prostředím a `id-ID-Andika` hlasem          |
| `it-it-cosimo-apollo`                       | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo` hlasem   |
| `it-it-luciarus`                            | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS` hlasem        |
| `ja-jp-ayumi-apollo`                        | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo` hlasem    |
| `ja-jp-harukarus`                           | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS` hlasem       |
| `ja-jp-ichiro-apollo`                       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo` hlasem   |
| `ko-kr-heamirus`                            | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS` hlasem        |
| `ms-my-rizwan`                              | Obrázek kontejneru s `ms-MY` národním prostředím a `ms-MY-Rizwan` hlasem          |
| `nb-no-huldarus`                            | Obrázek kontejneru s `nb-NO` národním prostředím a `nb-NO-HuldaRUS` hlasem        |
| `nl-nl-hannarus`                            | Obrázek kontejneru s `nl-NL` národním prostředím a `nl-NL-HannaRUS` hlasem        |
| `pl-pl-paulinarus`                          | Obrázek kontejneru s `pl-PL` národním prostředím a `pl-PL-PaulinaRUS` hlasem      |
| `pt-br-daniel-apollo`                       | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo` hlasem   |
| `pt-br-heloisarus`                          | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS` hlasem      |
| `pt-pt-heliarus`                            | Obrázek kontejneru s `pt-PT` národním prostředím a `pt-PT-HeliaRUS` hlasem        |
| `ro-ro-andrei`                              | Obrázek kontejneru s `ro-RO` národním prostředím a `ro-RO-Andrei` hlasem          |
| `ru-ru-ekaterinarus`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-EkaterinaRUS` hlasem    |
| `ru-ru-irina-apollo`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Irina-Apollo` hlasem    |
| `ru-ru-pavel-apollo`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Pavel-Apollo` hlasem    |
| `sk-sk-filip`                               | Obrázek kontejneru s `sk-SK` národním prostředím a `sk-SK-Filip` hlasem           |
| `sl-si-lado`                                | Obrázek kontejneru s `sl-SI` národním prostředím a `sl-SI-Lado` hlasem            |
| `sv-se-hedvigrus`                           | Obrázek kontejneru s `sv-SE` národním prostředím a `sv-SE-HedvigRUS` hlasem       |
| `ta-in-valluvar`                            | Obrázek kontejneru s `ta-IN` národním prostředím a `ta-IN-Valluvar` hlasem        |
| `te-in-chitra`                              | Obrázek kontejneru s `te-IN` národním prostředím a `te-IN-Chitra` hlasem          |
| `th-th-pattara`                             | Obrázek kontejneru s `th-TH` národním prostředím a `th-TH-Pattara` hlasem         |
| `tr-tr-sedarus`                             | Obrázek kontejneru s `tr-TR` národním prostředím a `tr-TR-SedaRUS` hlasem         |
| `vi-vn-an`                                  | Obrázek kontejneru s `vi-VN` národním prostředím a `vi-VN-An` hlasem              |
| `zh-cn-huihuirus`                           | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       |
| `zh-cn-kangkang-apollo`                     | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem |
| `zh-cn-yaoyao-apollo`                       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   |
| `zh-hk-danny-apollo`                        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Danny-Apollo` hlasem    |
| `zh-hk-tracy-apollo`                        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Tracy-Apollo` hlasem    |
| `zh-hk-tracyrus`                            | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-TracyRUS` hlasem        |
| `zh-tw-hanhanrus`                           | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-HanHanRUS` hlasem       |
| `zh-tw-yating-apollo`                       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Yating-Apollo` hlasem   |
| `zh-tw-zhiwei-apollo`                       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Zhiwei-Apollo` hlasem   |

---

## <a name="neural-text-to-speech"></a>Neuronové převodu textu na řeč

Image kontejneru [neuronové textu na řeč][sp-ntts] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `neural-text-to-speech` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámky k verzi pro `v1.3.0` :
* Kontejner textu na řeč neuronové je teď všeobecně dostupný. 

| Značky obrázku                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaNeural` hlasem      |
| `1.3.0-amd64-<locale-and-voice>`    | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.3.0-amd64-en-us-arianeural`. |


| Národní prostředí a hlasy v 1.3.0           | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-KatjaNeural` hlasem     |
| `en-au-natashaneural`               | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-NatashaNeural` hlasem   |
| `en-ca-claraneural`                 | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-ClaraNeural` hlasem     |
| `en-gb-libbyneural`                 | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-LibbyNeural` hlasem     |
| `en-gb-mianeural`                   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-MiaNeural` hlasem       |
| `en-us-arianeural`                  | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaNeural` hlasem      |
| `en-us-guyneural`                   | Obrázek kontejneru s `en-US` národním prostředím a `en-US-GuyNeural` hlasem       |
| `es-es-elviraneural`                | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-ElviraNeural` hlasem    |
| `es-mx-dalianeural`                 | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-DaliaNeural` hlasem     |
| `fr-ca-sylvieneural`                | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-SylvieNeural` hlasem    |
| `fr-fr-deniseneural`                | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-DeniseNeural` hlasem    |
| `it-it-elsaneural`                  | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-ElsaNeural` hlasem      |
| `ja-jp-nanamineural`                | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-NanamiNeural` hlasem    |
| `ko-kr-sunhineural`                 | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-SunHiNeural` hlasem     |
| `pt-br-franciscaneural`             | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-FranciscaNeural` hlasem |
| `zh-cn-xiaoxiaoneural`              | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-XiaoxiaoNeural` hlasem  |

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

| Značky obrázku                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaNeural` hlasem      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.2.0-amd64-en-us-arianeural-preview`. |


| Národní prostředí a hlasy v 1.2.0 Preview           | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaNeural` hlasem      |
| `de-de-katjaneural-preview`                 | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-KatjaNeural` hlasem     |
| `en-au-natashaneural-preview`               | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-NatashaNeural` hlasem   |
| `en-ca-claraneural-preview`                 | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-ClaraNeural` hlasem     |
| `en-gb-libbyneural-preview`                 | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-LibbyNeural` hlasem     |
| `en-gb-mianeural-preview`                   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-MiaNeural` hlasem       |
| `en-us-arianeural-preview`                  | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaNeural` hlasem      |
| `en-us-guyneural-preview`                   | Obrázek kontejneru s `en-US` národním prostředím a `en-US-GuyNeural` hlasem       |
| `es-es-elviraneural-preview`                | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-ElviraNeural` hlasem    |
| `es-mx-dalianeural-preview`                 | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-DaliaNeural` hlasem     |
| `fr-ca-sylvieneural-preview`                | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-SylvieNeural` hlasem    |
| `fr-fr-deniseneural-preview`                | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-DeniseNeural` hlasem    |
| `it-it-elsaneural-preview`                  | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-ElsaNeural` hlasem      |
| `ja-jp-nanamineural-preview`                | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-NanamiNeural` hlasem    |
| `ko-kr-sunhineural-preview`                 | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-SunHiNeural` hlasem     |
| `pt-br-franciscaneural-preview`             | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-FranciscaNeural` hlasem |
| `zh-cn-xiaoxiaoneural-preview`              | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-XiaoxiaoNeural` hlasem  |

---

## <a name="speech-language-detection"></a>Rozpoznávání mluveného jazyka

Bitovou kopii kontejneru rozpoznávání [řeči][sp-lid] v `mcr.microsoft.com` registru kontejnerů najdete v části. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `language-detection` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Značky obrázku                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Image kontejneru se dá v `mcr.microsoft.com` registru kontejneru najít. Je uložený v `azure-cognitive-services/textanalytics/` úložišti a má název `keyphrase` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)


| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Předchozí verze](#tab/previous)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Rozpoznávání jazyka textu

Bitovou kopii kontejneru [rozpoznávání jazyka][ta-la] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services/textanalytics/` úložišti a má název `language` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Nejnovější verze](#tab/current)

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Předchozí verze](#tab/previous)


| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Analýza mínění

Bitovou kopii kontejneru [Analýza mínění][ta-se] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services/textanalytics/` úložišti a má název `sentiment` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Tato image kontejneru má k dispozici následující značky. Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Značky obrázku | Poznámky                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Analýza mínění V3 (angličtina)               |
| `3.0-es`   | Analýza mínění V3 (španělština)               |
| `3.0-fr`   | Analýza mínění V3 (francouzština)                |
| `3.0-it`   | Analýza mínění V3 (italština)               |
| `3.0-de`   | Analýza mínění V3 (němčina)                |
| `3.0-zh`   | Analýza mínění V3 (zjednodušená čínština)  |
| `3.0-zht`  | Analýza mínění V3 (tradiční čínština) |
| `3.0-ja`   | Analýza mínění V3 (japonština)              |
| `3.0-pt`   | Analýza mínění V3 (portugalština)            |
| `3.0-nl`   | Analýza mínění V3 (holandština)                 |
| `2.1`    | Analýza mínění v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
