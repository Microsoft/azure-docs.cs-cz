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
ms.openlocfilehash: 90ac4891dbca34ff2d9bdb9653e27ad2a495b317
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021248"
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

Poznámka k verzi pro `2.7.0-amd64` :

**Funkce**
* Ve výchozím nastavení je nastavení interpunkce povoleno.

Všimněte si, že vzhledem k uvedeným seznamům frází se velikost této image kontejneru zvýšila.

| Značky obrázku                    | Poznámky | Otisk                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)
Poznámka k verzi pro `2.6.0-amd64` :

**Funkce**
* Podpora pro PhraseList v2 
* Seznamy frází jsou podporovány v následujících národních prostředích:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * cs-cz
    * zh-cn
* Podpora pro stažení vlastního základního modelu. 
    * Použít `BaseModelLocale=<locale>` s `docker run` příkazem
* Úplná migrace do .NET 3,1

**Řeší**
* Opravili jsme problém, kdy bylo skóre spolehlivosti vždy 1 v režimu Diarization
* Migrováno do rozhraní TextAnalytics 3,0 API

Všimněte si, že vzhledem k uvedeným seznamům frází se velikost této image kontejneru zvýšila.

Poznámka k verzi pro `2.5.0-amd64` :

**Funkce**
* Podpora vlastní výslovnosti pro vlastní modely
* Podpora cloudu Azure a Azure pro státní správu USA

**Řeší**
* Oprava potíží spustit jako nerootového uživatele v režimu Diarization

| Značky obrázku                    | Poznámky               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   první verze GA    |

---

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní image kontejneru [pro převod textu na mluvené slovo][sp-ctts] se dá najít v `mcr.microsoft.com` registru kontejnerů. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `custom-text-to-speech` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Můžete také najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámka k verzi pro `1.9.0-amd64` :

Pravidelná měsíční verze

| Značky obrázku                    | Poznámky | Otisk                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)
Poznámka k verzi pro `1.8.0-amd64` :

**Funkce**
* Úplná migrace do .NET 3,1

Poznámka k verzi pro `1.7.0-amd64` :

**Funkce**
* Částečně migrováno na rozhraní .NET 3,1

| Značky obrázku                    | Poznámky               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   první verze GA    |

---

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek s obrázkem [mluveného řeči na text][sp-stt] najdete v části `mcr.microsoft.com` kontejner kontejneru kontejnerů. Je uložený v `azure-cognitive-services/speechservices/` úložišti a má název `speech-to-text` . Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Můžete najít úplný seznam [značek na MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Vzhledem k tomu, že je převod řeči na text v 2.5.0, jsou obrázky podporované v oblasti *) – Virginia státní správy USA* . Při použití této oblasti prosím použijte koncový bod fakturace *) – Virginia státní správy USA* a klíče rozhraní API.

# <a name="latest-version"></a>[Nejnovější verze](#tab/current)

Poznámka k verzi pro `2.7.0-amd64-<locale>` :

**Funkce**
* Podpora následujících nových národních prostředí:
    * ar-BH, ar-sweetiq, ar-Jo, ar-9,1, ar-OM, ar-sy
    * bg-bg
    * el-gr
    * EN-HK, EN-IE, EN-pH, EN-SG, EN-za
    * ES-ar, ES-Bo, ES-CL, ES-co, ES-CR, ES-CU, ES-do, ES, ES, ES-, ES, gt, ES-PA, ES-PE, ES-PR, ES-py, ES-sv, ES-US, ES-uy, ES-ve
    * et-ee
    * GA – IE
    * hr-hr
    * hu-hu
    * lt – lt
    * lv-lv
    * MT-MT
    * ro-ro
    * sk-sk
    * SL-SL
* Ve výchozím nastavení je interpunkce povolena.

Všimněte si, že vzhledem k uvedeným seznamům frází se velikost této image kontejneru zvýšila. 

| Značky obrázku                    | Poznámky                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Obrázek kontejneru s `en-US` národním prostředím                                                             |
| `2.7.0-amd64-<locale>`        | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `2.7.0-amd64-en-us`. |

Pro tento kontejner jsou k dispozici následující místní prostředí.

| Národní prostředí pro 2.7.0           | Poznámky                                    | Otisk                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | Obrázek kontejneru s `ar-AE` národním prostředím | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | Obrázek kontejneru s `ar-BH` národním prostředím | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | Obrázek kontejneru s `ar-EG` národním prostředím | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | Obrázek kontejneru s `ar-IQ` národním prostředím | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | Obrázek kontejneru s `ar-JO` národním prostředím | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | Obrázek kontejneru s `ar-KW` národním prostředím | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | Obrázek kontejneru s `ar-LB` národním prostředím | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | Obrázek kontejneru s `ar-OM` národním prostředím | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | Obrázek kontejneru s `ar-QA` národním prostředím | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | Obrázek kontejneru s `ar-SA` národním prostředím | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | Obrázek kontejneru s `ar-SY` národním prostředím | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | Obrázek kontejneru s `bg-BG` národním prostředím | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | Obrázek kontejneru s `ca-ES` národním prostředím | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | Obrázek kontejneru s `cs-CZ` národním prostředím | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | Obrázek kontejneru s `da-DK` národním prostředím | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | Obrázek kontejneru s `de-DE` národním prostředím | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | Obrázek kontejneru s `el-GR` národním prostředím | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | Obrázek kontejneru s `en-AU` národním prostředím | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | Obrázek kontejneru s `en-CA` národním prostředím | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | Obrázek kontejneru s `en-GB` národním prostředím | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | Obrázek kontejneru s `en-HK` národním prostředím | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | Obrázek kontejneru s `en-IE` národním prostředím | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | Obrázek kontejneru s `en-IN` národním prostředím | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | Obrázek kontejneru s `en-NZ` národním prostředím | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | Obrázek kontejneru s `en-PH` národním prostředím | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | Obrázek kontejneru s `en-SG` národním prostředím | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | Obrázek kontejneru s `en-US` národním prostředím | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | Obrázek kontejneru s `en-ZA` národním prostředím | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | Obrázek kontejneru s `es-AR` národním prostředím | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | Obrázek kontejneru s `es-BO` národním prostředím | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | Obrázek kontejneru s `es-CL` národním prostředím | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | Obrázek kontejneru s `es-CO` národním prostředím | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | Obrázek kontejneru s `es-CR` národním prostředím | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | Obrázek kontejneru s `es-CU` národním prostředím | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | Obrázek kontejneru s `es-DO` národním prostředím | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | Obrázek kontejneru s `es-EC` národním prostředím | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | Obrázek kontejneru s `es-ES` národním prostředím | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | Obrázek kontejneru s `es-GT` národním prostředím | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | Obrázek kontejneru s `es-HN` národním prostředím | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | Obrázek kontejneru s `es-MX` národním prostředím | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | Obrázek kontejneru s `es-NI` národním prostředím | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | Obrázek kontejneru s `es-PA` národním prostředím | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | Obrázek kontejneru s `es-PE` národním prostředím | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | Obrázek kontejneru s `es-PR` národním prostředím | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | Obrázek kontejneru s `es-PY` národním prostředím | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | Obrázek kontejneru s `es-SV` národním prostředím | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | Obrázek kontejneru s `es-US` národním prostředím | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | Obrázek kontejneru s `es-UY` národním prostředím | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | Obrázek kontejneru s `es-VE` národním prostředím | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | Obrázek kontejneru s `et-EE` národním prostředím | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | Obrázek kontejneru s `fi-FI` národním prostředím | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | Obrázek kontejneru s `fr-CA` národním prostředím | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | Obrázek kontejneru s `fr-FR` národním prostředím | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | Obrázek kontejneru s `ga-IE` národním prostředím | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | Obrázek kontejneru s `gu-IN` národním prostředím | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | Obrázek kontejneru s `hi-IN` národním prostředím | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | Obrázek kontejneru s `hr-HR` národním prostředím | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | Obrázek kontejneru s `hu-HU` národním prostředím | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | Obrázek kontejneru s `it-IT` národním prostředím | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | Obrázek kontejneru s `ja-JP` národním prostředím | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | Obrázek kontejneru s `ko-KR` národním prostředím | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | Obrázek kontejneru s `lt-LT` národním prostředím | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | Obrázek kontejneru s `lv-LV` národním prostředím | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | Obrázek kontejneru s `mr-IN` národním prostředím | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | Obrázek kontejneru s `mt-MT` národním prostředím | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | Obrázek kontejneru s `nb-NO` národním prostředím | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | Obrázek kontejneru s `nl-NL` národním prostředím | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | Obrázek kontejneru s `pl-PL` národním prostředím | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | Obrázek kontejneru s `pt-BR` národním prostředím | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | Obrázek kontejneru s `pt-PT` národním prostředím | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | Obrázek kontejneru s `ro-RO` národním prostředím | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | Obrázek kontejneru s `ru-RU` národním prostředím | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | Obrázek kontejneru s `sk-SK` národním prostředím | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | Obrázek kontejneru s `sl-SI` národním prostředím | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | Obrázek kontejneru s `sv-SE` národním prostředím | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | Obrázek kontejneru s `ta-IN` národním prostředím | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | Obrázek kontejneru s `te-IN` národním prostředím | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | Obrázek kontejneru s `th-TH` národním prostředím | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | Obrázek kontejneru s `tr-TR` národním prostředím | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | Obrázek kontejneru s `zh-CN` národním prostředím | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | Obrázek kontejneru s `zh-HK` národním prostředím | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | Obrázek kontejneru s `zh-TW` národním prostředím | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

Poznámka k verzi pro `2.6.0-amd64-<locale>` :

**Funkce**
* Upgrade na nejnovější modely a úplná migrace na .NET 3,1
* Podpora pro PhraseList v2
* Seznamy frází jsou podporovány v následujících národních prostředích:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * cs-cz
    * zh-cn
* Podpora nového národního prostředí `cs-CZ` 
    * Malá a velká písmena nejsou v současné době podporována.

**Řeší**
* Opravuje problém s přesností na 1 v režimu Diarization
* Migrace pomocí rozhraní TextAnalytics 3,0 API

Všimněte si, že vzhledem k uvedeným seznamům frází se velikost této image kontejneru zvýšila. 

Poznámka k verzi pro `2.5.0-amd64-<locale>` :

**Funkce**
* Podpora cloudu Azure USA pro státní správu

**Řeší**
* Opravuje problém, který se spouští jako nerootový uživatel v režimu Diarization.

| Značky obrázku                  | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `2.5.0-amd64-en-us`. |


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

Poznámka k verzi pro `1.9.0-amd64-<locale-and-voice>` :

* Pravidelná měsíční verze

| Značky obrázku                                  | Poznámky                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaRUS` hlasem                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.9.0-amd64-en-us-ariarus`.  |


| Národní prostředí pro v 1.9.0                          | Poznámky                                                                      | Otisk                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | Obrázek kontejneru s `ar-EG` národním prostředím a `ar-EG-Hoda` hlasem            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | Obrázek kontejneru s `ar-SA` národním prostředím a `ar-SA-Naayf` hlasem           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | Obrázek kontejneru s `bg-BG` národním prostředím a `bg-BG-Ivan` hlasem            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | Obrázek kontejneru s `ca-ES` národním prostředím a `ca-ES-HerenaRUS` hlasem       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | Obrázek kontejneru s `cs-CZ` národním prostředím a `cs-CZ-Jakub` hlasem           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | Obrázek kontejneru s `da-DK` národním prostředím a `da-DK-HelleRUS` hlasem        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | Obrázek kontejneru s `de-AT` národním prostředím a `de-AT-Michael` hlasem         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | Obrázek kontejneru s `de-CH` národním prostředím a `de-CH-Karsten` hlasem         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda` hlasem           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo` hlasem   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | Obrázek kontejneru s `el-GR` národním prostředím a `el-GR-Stefanos` hlasem        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine` hlasem       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS` hlasem       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-HeatherRUS` hlasem      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-Linda` hlasem           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo` hlasem   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS` hlasem        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo` hlasem    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | Obrázek kontejneru s `en-IE` národním prostředím a `en-IE-Sean` hlasem            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo` hlasem    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS` hlasem        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo` hlasem     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Aria24kRUS` hlasem      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | Obrázek kontejneru s `en-US` národním prostředím a `en-US-AriaRUS` hlasem         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS` hlasem     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS` hlasem       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS` hlasem         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS` hlasem       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo` hlasem    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo` hlasem    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS` hlasem        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo` hlasem     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | Obrázek kontejneru s `fi-FI` národním prostředím a `fi-FI-HeidiRUS` hlasem        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline` hlasem        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS` hlasem     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | Obrázek kontejneru s `fr-CH` národním prostředím a `fr-CH-Guillaume` hlasem       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS` hlasem     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo` hlasem    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo` hlasem     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | Obrázek kontejneru s `he-IL` národním prostředím a `he-IL-Asaf` hlasem            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Hemant` hlasem          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana-Apollo` hlasem  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana` hlasem         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | Obrázek kontejneru s `hr-HR` národním prostředím a `hr-HR-Matej` hlasem           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | Obrázek kontejneru s `hu-HU` národním prostředím a `hu-HU-Szabolcs` hlasem        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | Obrázek kontejneru s `id-ID` národním prostředím a `id-ID-Andika` hlasem          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo` hlasem   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS` hlasem        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo` hlasem    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS` hlasem       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo` hlasem   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS` hlasem        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | Obrázek kontejneru s `ms-MY` národním prostředím a `ms-MY-Rizwan` hlasem          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | Obrázek kontejneru s `nb-NO` národním prostředím a `nb-NO-HuldaRUS` hlasem        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | Obrázek kontejneru s `nl-NL` národním prostředím a `nl-NL-HannaRUS` hlasem        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | Obrázek kontejneru s `pl-PL` národním prostředím a `pl-PL-PaulinaRUS` hlasem      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo` hlasem   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS` hlasem      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | Obrázek kontejneru s `pt-PT` národním prostředím a `pt-PT-HeliaRUS` hlasem        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | Obrázek kontejneru s `ro-RO` národním prostředím a `ro-RO-Andrei` hlasem          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-EkaterinaRUS` hlasem    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Irina-Apollo` hlasem    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Pavel-Apollo` hlasem    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | Obrázek kontejneru s `sk-SK` národním prostředím a `sk-SK-Filip` hlasem           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | Obrázek kontejneru s `sl-SI` národním prostředím a `sl-SI-Lado` hlasem            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | Obrázek kontejneru s `sv-SE` národním prostředím a `sv-SE-HedvigRUS` hlasem       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | Obrázek kontejneru s `ta-IN` národním prostředím a `ta-IN-Valluvar` hlasem        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | Obrázek kontejneru s `te-IN` národním prostředím a `te-IN-Chitra` hlasem          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | Obrázek kontejneru s `th-TH` národním prostředím a `th-TH-Pattara` hlasem         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | Obrázek kontejneru s `tr-TR` národním prostředím a `tr-TR-SedaRUS` hlasem         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | Obrázek kontejneru s `vi-VN` národním prostředím a `vi-VN-An` hlasem              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS` hlasem       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo` hlasem | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo` hlasem   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Danny-Apollo` hlasem    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Tracy-Apollo` hlasem    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-TracyRUS` hlasem        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-HanHanRUS` hlasem       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Yating-Apollo` hlasem   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Zhiwei-Apollo` hlasem   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

Poznámka k verzi pro `1.8.0-amd64-<locale-and-voice>` :

**Funkce**

* Úplná migrace do .NET 3,1

Poznámka k verzi pro `1.7.0-amd64-<locale-and-voice>` :

**Funkce**

* Upgradované komponenty na .NET 3,1

| Značky obrázku                                  | Poznámky                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | první verze GA Nahraďte `<locale>` některou z dostupných národních prostředí, která jsou uvedená níže. Příklad: `1.7.0-amd64-en-us-ariarus`.  |


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
