---
title: Značky imagí kontejnerů Cognitive Services
titleSuffix: Azure Cognitive Services
description: Úplný seznam všech značek imagí kontejneru služby vnímání.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219442"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Značky image kontejneru Azure Cognitive Services

Azure Cognitive Services nabízí mnoho imagí kontejneru. Registry kontejnerů a odpovídající úložiště se v různých imagích kontejnerů liší. Každý název Image kontejneru nabízí více značek. Značka image kontejneru je mechanismus správy verzí image kontejneru. Tento článek je určený k použití jako ucelený odkaz pro výpis všech Cognitive Servicesch imagí kontejnerů a jejich dostupných značek.

> [!TIP]
> Při použití [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)věnujte velkou pozornost pro velká a malá písmena registru kontejnerů, úložiště, názvu image kontejneru a odpovídajících značek – protože rozlišují **velká a malá písmena**.

## <a name="anomaly-detector"></a>Detektor anomálií

Image kontejneru [detektoru anomálií][ad-containers] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-anomaly-detector`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Počítačové zpracování obrazu

Image kontejneru [počítačové zpracování obrazu][cv-containers] můžete najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-read`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Tvář

Image kontejneru [Face][fa-containers] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-face`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Rozpoznávání formulářů

Image kontejneru [pro rozpoznávání formulářů][fr-containers] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-form-recognizer`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Rozpoznávání jazyka (LUIS)

Image kontejneru [Luis][lu-containers] se dá najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `luis`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/luis`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Custom Speech na text

Obrázek kontejneru [Custom Speech do textu][sp-cstt] lze nalézt v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-custom-speech-to-text`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní image kontejneru [textu na řeč][sp-ctts] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-custom-text-to-speech`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek s obrázkem [mluveného textu na text][sp-stt] najdete na `containerpreview.azurecr.io` registru kontejnerů. Je umístěn v úložišti `microsoft` a má název `cognitive-services-speech-to-text`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                  | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Obrázek kontejneru s národním prostředím `en-US` |
| `2.1.1-amd64-ar-ae-preview` | Obrázek kontejneru s národním prostředím `ar-AE` |
| `2.1.1-amd64-ar-eg-preview` | Obrázek kontejneru s národním prostředím `ar-EG` |
| `2.1.1-amd64-ar-kw-preview` | Obrázek kontejneru s národním prostředím `ar-KW` |
| `2.1.1-amd64-ar-qa-preview` | Obrázek kontejneru s národním prostředím `ar-QA` |
| `2.1.1-amd64-ar-sa-preview` | Obrázek kontejneru s národním prostředím `ar-SA` |
| `2.1.1-amd64-ca-es-preview` | Obrázek kontejneru s národním prostředím `ca-ES` |
| `2.1.1-amd64-da-dk-preview` | Obrázek kontejneru s národním prostředím `da-DK` |
| `2.1.1-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `2.1.1-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `2.1.1-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `2.1.1-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `2.1.1-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `2.1.1-amd64-en-nz-preview` | Obrázek kontejneru s národním prostředím `en-NZ` |
| `2.1.1-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `2.1.1-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `2.1.1-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `2.1.1-amd64-fi-fi-preview` | Obrázek kontejneru s národním prostředím `fi-FI` |
| `2.1.1-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `2.1.1-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `2.1.1-amd64-gu-in-preview` | Obrázek kontejneru s národním prostředím `gu-IN` |
| `2.1.1-amd64-hi-in-preview` | Obrázek kontejneru s národním prostředím `hi-IN` |
| `2.1.1-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `2.1.1-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `2.1.1-amd64-ko-kr-preview` | Obrázek kontejneru s národním prostředím `ko-KR` |
| `2.1.1-amd64-mr-in-preview` | Obrázek kontejneru s národním prostředím `mr-IN` |
| `2.1.1-amd64-nb-no-preview` | Obrázek kontejneru s národním prostředím `nb-NO` |
| `2.1.1-amd64-nl-nl-preview` | Obrázek kontejneru s národním prostředím `nl-NL` |
| `2.1.1-amd64-pl-pl-preview` | Obrázek kontejneru s národním prostředím `pl-PL` |
| `2.1.1-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `2.1.1-amd64-pt-pt-preview` | Obrázek kontejneru s národním prostředím `pt-PT` |
| `2.1.1-amd64-ru-ru-preview` | Obrázek kontejneru s národním prostředím `ru-RU` |
| `2.1.1-amd64-sv-se-preview` | Obrázek kontejneru s národním prostředím `sv-SE` |
| `2.1.1-amd64-ta-in-preview` | Obrázek kontejneru s národním prostředím `ta-IN` |
| `2.1.1-amd64-te-in-preview` | Obrázek kontejneru s národním prostředím `te-IN` |
| `2.1.1-amd64-th-th-preview` | Obrázek kontejneru s národním prostředím `th-TH` |
| `2.1.1-amd64-tr-tr-preview` | Obrázek kontejneru s národním prostředím `tr-TR` |
| `2.1.1-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `2.1.1-amd64-zh-hk-preview` | Obrázek kontejneru s národním prostředím `zh-HK` |
| `2.1.1-amd64-zh-tw-preview` | Obrázek kontejneru s národním prostředím `zh-TW` |
| `2.1.0-amd64-ar-ae-preview` | Obrázek kontejneru s národním prostředím `ar-AE` |
| `2.1.0-amd64-ar-eg-preview` | Obrázek kontejneru s národním prostředím `ar-EG` |
| `2.1.0-amd64-ar-kw-preview` | Obrázek kontejneru s národním prostředím `ar-KW` |
| `2.1.0-amd64-ar-qa-preview` | Obrázek kontejneru s národním prostředím `ar-QA` |
| `2.1.0-amd64-ar-sa-preview` | Obrázek kontejneru s národním prostředím `ar-SA` |
| `2.1.0-amd64-ca-es-preview` | Obrázek kontejneru s národním prostředím `ca-ES` |
| `2.1.0-amd64-da-dk-preview` | Obrázek kontejneru s národním prostředím `da-DK` |
| `2.1.0-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `2.1.0-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `2.1.0-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `2.1.0-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `2.1.0-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `2.1.0-amd64-en-nz-preview` | Obrázek kontejneru s národním prostředím `en-NZ` |
| `2.1.0-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `2.1.0-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `2.1.0-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `2.1.0-amd64-fi-fi-preview` | Obrázek kontejneru s národním prostředím `fi-FI` |
| `2.1.0-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `2.1.0-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `2.1.0-amd64-gu-in-preview` | Obrázek kontejneru s národním prostředím `gu-IN` |
| `2.1.0-amd64-hi-in-preview` | Obrázek kontejneru s národním prostředím `hi-IN` |
| `2.1.0-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `2.1.0-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `2.1.0-amd64-ko-kr-preview` | Obrázek kontejneru s národním prostředím `ko-KR` |
| `2.1.0-amd64-mr-in-preview` | Obrázek kontejneru s národním prostředím `mr-IN` |
| `2.1.0-amd64-nb-no-preview` | Obrázek kontejneru s národním prostředím `nb-NO` |
| `2.1.0-amd64-nl-nl-preview` | Obrázek kontejneru s národním prostředím `nl-NL` |
| `2.1.0-amd64-pl-pl-preview` | Obrázek kontejneru s národním prostředím `pl-PL` |
| `2.1.0-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `2.1.0-amd64-pt-pt-preview` | Obrázek kontejneru s národním prostředím `pt-PT` |
| `2.1.0-amd64-ru-ru-preview` | Obrázek kontejneru s národním prostředím `ru-RU` |
| `2.1.0-amd64-sv-se-preview` | Obrázek kontejneru s národním prostředím `sv-SE` |
| `2.1.0-amd64-ta-in-preview` | Obrázek kontejneru s národním prostředím `ta-IN` |
| `2.1.0-amd64-te-in-preview` | Obrázek kontejneru s národním prostředím `te-IN` |
| `2.1.0-amd64-th-th-preview` | Obrázek kontejneru s národním prostředím `th-TH` |
| `2.1.0-amd64-tr-tr-preview` | Obrázek kontejneru s národním prostředím `tr-TR` |
| `2.1.0-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `2.1.0-amd64-zh-hk-preview` | Obrázek kontejneru s národním prostředím `zh-HK` |
| `2.1.0-amd64-zh-tw-preview` | Obrázek kontejneru s národním prostředím `zh-TW` |
| `2.0.3-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `2.0.2-amd64-ar-ae-preview` | Obrázek kontejneru s národním prostředím `ar-AE` |
| `2.0.2-amd64-ar-eg-preview` | Obrázek kontejneru s národním prostředím `ar-EG` |
| `2.0.2-amd64-ar-kw-preview` | Obrázek kontejneru s národním prostředím `ar-KW` |
| `2.0.2-amd64-ar-qa-preview` | Obrázek kontejneru s národním prostředím `ar-QA` |
| `2.0.2-amd64-ar-sa-preview` | Obrázek kontejneru s národním prostředím `ar-SA` |
| `2.0.2-amd64-ca-es-preview` | Obrázek kontejneru s národním prostředím `ca-ES` |
| `2.0.2-amd64-da-dk-preview` | Obrázek kontejneru s národním prostředím `da-DK` |
| `2.0.2-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `2.0.2-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `2.0.2-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `2.0.2-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `2.0.2-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `2.0.2-amd64-en-nz-preview` | Obrázek kontejneru s národním prostředím `en-NZ` |
| `2.0.2-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `2.0.2-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `2.0.2-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `2.0.2-amd64-fi-fi-preview` | Obrázek kontejneru s národním prostředím `fi-FI` |
| `2.0.2-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `2.0.2-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `2.0.2-amd64-gu-in-preview` | Obrázek kontejneru s národním prostředím `gu-IN` |
| `2.0.2-amd64-hi-in-preview` | Obrázek kontejneru s národním prostředím `hi-IN` |
| `2.0.2-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `2.0.2-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `2.0.2-amd64-ko-kr-preview` | Obrázek kontejneru s národním prostředím `ko-KR` |
| `2.0.2-amd64-mr-in-preview` | Obrázek kontejneru s národním prostředím `mr-IN` |
| `2.0.2-amd64-nb-no-preview` | Obrázek kontejneru s národním prostředím `nb-NO` |
| `2.0.2-amd64-nl-nl-preview` | Obrázek kontejneru s národním prostředím `nl-NL` |
| `2.0.2-amd64-pl-pl-preview` | Obrázek kontejneru s národním prostředím `pl-PL` |
| `2.0.2-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `2.0.2-amd64-pt-pt-preview` | Obrázek kontejneru s národním prostředím `pt-PT` |
| `2.0.2-amd64-ru-ru-preview` | Obrázek kontejneru s národním prostředím `ru-RU` |
| `2.0.2-amd64-sv-se-preview` | Obrázek kontejneru s národním prostředím `sv-SE` |
| `2.0.2-amd64-ta-in-preview` | Obrázek kontejneru s národním prostředím `ta-IN` |
| `2.0.2-amd64-te-in-preview` | Obrázek kontejneru s národním prostředím `te-IN` |
| `2.0.2-amd64-th-th-preview` | Obrázek kontejneru s národním prostředím `th-TH` |
| `2.0.2-amd64-tr-tr-preview` | Obrázek kontejneru s národním prostředím `tr-TR` |
| `2.0.2-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `2.0.2-amd64-zh-hk-preview` | Obrázek kontejneru s národním prostředím `zh-HK` |
| `2.0.2-amd64-zh-tw-preview` | Obrázek kontejneru s národním prostředím `zh-TW` |
| `2.0.1-amd64-ar-ae-preview` | Obrázek kontejneru s národním prostředím `ar-AE` |
| `2.0.1-amd64-ar-eg-preview` | Obrázek kontejneru s národním prostředím `ar-EG` |
| `2.0.1-amd64-ar-kw-preview` | Obrázek kontejneru s národním prostředím `ar-KW` |
| `2.0.1-amd64-ar-qa-preview` | Obrázek kontejneru s národním prostředím `ar-QA` |
| `2.0.1-amd64-ar-sa-preview` | Obrázek kontejneru s národním prostředím `ar-SA` |
| `2.0.1-amd64-ca-es-preview` | Obrázek kontejneru s národním prostředím `ca-ES` |
| `2.0.1-amd64-da-dk-preview` | Obrázek kontejneru s národním prostředím `da-DK` |
| `2.0.1-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `2.0.1-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `2.0.1-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `2.0.1-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `2.0.1-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `2.0.1-amd64-en-nz-preview` | Obrázek kontejneru s národním prostředím `en-NZ` |
| `2.0.1-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `2.0.1-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `2.0.1-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `2.0.1-amd64-fi-fi-preview` | Obrázek kontejneru s národním prostředím `fi-FI` |
| `2.0.1-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `2.0.1-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `2.0.1-amd64-gu-in-preview` | Obrázek kontejneru s národním prostředím `gu-IN` |
| `2.0.1-amd64-hi-in-preview` | Obrázek kontejneru s národním prostředím `hi-IN` |
| `2.0.1-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `2.0.1-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `2.0.1-amd64-ko-kr-preview` | Obrázek kontejneru s národním prostředím `ko-KR` |
| `2.0.1-amd64-mr-in-preview` | Obrázek kontejneru s národním prostředím `mr-IN` |
| `2.0.1-amd64-nb-no-preview` | Obrázek kontejneru s národním prostředím `nb-NO` |
| `2.0.1-amd64-nl-nl-preview` | Obrázek kontejneru s národním prostředím `nl-NL` |
| `2.0.1-amd64-pl-pl-preview` | Obrázek kontejneru s národním prostředím `pl-PL` |
| `2.0.1-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `2.0.1-amd64-pt-pt-preview` | Obrázek kontejneru s národním prostředím `pt-PT` |
| `2.0.1-amd64-ru-ru-preview` | Obrázek kontejneru s národním prostředím `ru-RU` |
| `2.0.1-amd64-sv-se-preview` | Obrázek kontejneru s národním prostředím `sv-SE` |
| `2.0.1-amd64-ta-in-preview` | Obrázek kontejneru s národním prostředím `ta-IN` |
| `2.0.1-amd64-te-in-preview` | Obrázek kontejneru s národním prostředím `te-IN` |
| `2.0.1-amd64-th-th-preview` | Obrázek kontejneru s národním prostředím `th-TH` |
| `2.0.1-amd64-tr-tr-preview` | Obrázek kontejneru s národním prostředím `tr-TR` |
| `2.0.1-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `2.0.1-amd64-zh-hk-preview` | Obrázek kontejneru s národním prostředím `zh-HK` |
| `2.0.1-amd64-zh-tw-preview` | Obrázek kontejneru s národním prostředím `zh-TW` |
| `2.0.0-amd64-ar-eg-preview` | Obrázek kontejneru s národním prostředím `ar-EG` |
| `2.0.0-amd64-ca-es-preview` | Obrázek kontejneru s národním prostředím `ca-ES` |
| `2.0.0-amd64-da-dk-preview` | Obrázek kontejneru s národním prostředím `da-DK` |
| `2.0.0-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `2.0.0-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `2.0.0-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `2.0.0-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `2.0.0-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `2.0.0-amd64-en-nz-preview` | Obrázek kontejneru s národním prostředím `en-NZ` |
| `2.0.0-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `2.0.0-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `2.0.0-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `2.0.0-amd64-fi-fi-preview` | Obrázek kontejneru s národním prostředím `fi-FI` |
| `2.0.0-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `2.0.0-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `2.0.0-amd64-hi-in-preview` | Obrázek kontejneru s národním prostředím `hi-IN` |
| `2.0.0-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `2.0.0-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `2.0.0-amd64-ko-kr-preview` | Obrázek kontejneru s národním prostředím `ko-KR` |
| `2.0.0-amd64-nb-no-preview` | Obrázek kontejneru s národním prostředím `nb-NO` |
| `2.0.0-amd64-nl-nl-preview` | Obrázek kontejneru s národním prostředím `nl-NL` |
| `2.0.0-amd64-pl-pl-preview` | Obrázek kontejneru s národním prostředím `pl-PL` |
| `2.0.0-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `2.0.0-amd64-pt-pt-preview` | Obrázek kontejneru s národním prostředím `pt-PT` |
| `2.0.0-amd64-ru-ru-preview` | Obrázek kontejneru s národním prostředím `ru-RU` |
| `2.0.0-amd64-sv-se-preview` | Obrázek kontejneru s národním prostředím `sv-SE` |
| `2.0.0-amd64-th-th-preview` | Obrázek kontejneru s národním prostředím `th-TH` |
| `2.0.0-amd64-tr-tr-preview` | Obrázek kontejneru s národním prostředím `tr-TR` |
| `2.0.0-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `2.0.0-amd64-zh-hk-preview` | Obrázek kontejneru s národním prostředím `zh-HK` |
| `2.0.0-amd64-zh-tw-preview` | Obrázek kontejneru s národním prostředím `zh-TW` |
| `1.2.0-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `1.2.0-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `1.2.0-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `1.2.0-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `1.2.0-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `1.2.0-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `1.2.0-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `1.2.0-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `1.2.0-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `1.2.0-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `1.2.0-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `1.2.0-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `1.2.0-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `1.2.0-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `1.1.3-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `1.1.3-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `1.1.3-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `1.1.3-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `1.1.3-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `1.1.3-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `1.1.3-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `1.1.3-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `1.1.3-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `1.1.3-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `1.1.3-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `1.1.3-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `1.1.3-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `1.1.3-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `1.1.2-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `1.1.2-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `1.1.2-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `1.1.2-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `1.1.2-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `1.1.2-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `1.1.2-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `1.1.2-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `1.1.2-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `1.1.2-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `1.1.2-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `1.1.2-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `1.1.2-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `1.1.2-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `1.1.1-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `1.1.1-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `1.1.1-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `1.1.1-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `1.1.1-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `1.1.1-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `1.1.1-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `1.1.1-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `1.1.1-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `1.1.1-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `1.1.1-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `1.1.1-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `1.1.1-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `1.1.1-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `1.1.0-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `1.1.0-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `1.1.0-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `1.1.0-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `1.1.0-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `1.1.0-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `1.1.0-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `1.1.0-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `1.1.0-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `1.1.0-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `1.1.0-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `1.1.0-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `1.1.0-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `1.1.0-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |
| `1.0.0-amd64-de-de-preview` | Obrázek kontejneru s národním prostředím `de-DE` |
| `1.0.0-amd64-en-au-preview` | Obrázek kontejneru s národním prostředím `en-AU` |
| `1.0.0-amd64-en-ca-preview` | Obrázek kontejneru s národním prostředím `en-CA` |
| `1.0.0-amd64-en-gb-preview` | Obrázek kontejneru s národním prostředím `en-GB` |
| `1.0.0-amd64-en-in-preview` | Obrázek kontejneru s národním prostředím `en-IN` |
| `1.0.0-amd64-en-us-preview` | Obrázek kontejneru s národním prostředím `en-US` |
| `1.0.0-amd64-es-es-preview` | Obrázek kontejneru s národním prostředím `es-ES` |
| `1.0.0-amd64-es-mx-preview` | Obrázek kontejneru s národním prostředím `es-MX` |
| `1.0.0-amd64-fr-ca-preview` | Obrázek kontejneru s národním prostředím `fr-CA` |
| `1.0.0-amd64-fr-fr-preview` | Obrázek kontejneru s národním prostředím `fr-FR` |
| `1.0.0-amd64-it-it-preview` | Obrázek kontejneru s národním prostředím `it-IT` |
| `1.0.0-amd64-ja-jp-preview` | Obrázek kontejneru s národním prostředím `ja-JP` |
| `1.0.0-amd64-pt-br-preview` | Obrázek kontejneru s národním prostředím `pt-BR` |
| `1.0.0-amd64-zh-cn-preview` | Obrázek kontejneru s národním prostředím `zh-CN` |

## <a name="text-to-speech"></a>Převod textu na řeč

Obrázek kontejneru [Převod textu na řeč][sp-tts] najdete na `containerpreview.azurecr.io` registru kontejnerů. Je umístěn v úložišti `microsoft` a má název `cognitive-services-text-to-speech`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS`m hlasem        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Obrázek kontejneru s `ar-EG` národním prostředím a `ar-EG-Hoda`m hlasem            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Obrázek kontejneru s `ar-SA` národním prostředím a `ar-SA-Naayf`m hlasem           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Obrázek kontejneru s `bg-BG` národním prostředím a `bg-BG-Ivan`m hlasem            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Obrázek kontejneru s `ca-ES` národním prostředím a `ca-ES-HerenaRUS`m hlasem       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Obrázek kontejneru s `cs-CZ` národním prostředím a `cs-CZ-Jakub`m hlasem           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Obrázek kontejneru s `da-DK` národním prostředím a `da-DK-HelleRUS`m hlasem        |
| `1.3.0-amd64-de-at-michael-preview`         | Obrázek kontejneru s `de-AT` národním prostředím a `de-AT-Michael`m hlasem         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Obrázek kontejneru s `de-CH` národním prostředím a `de-CH-Karsten`m hlasem         |
| `1.3.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda`m hlasem           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda`m hlasem           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-HeddaRUS`m hlasem        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo`m hlasem   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Obrázek kontejneru s `el-GR` národním prostředím a `el-GR-Stefanos`m hlasem        |
| `1.3.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine`m hlasem       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS`m hlasem       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-HeatherRUS`m hlasem      |
| `1.3.0-amd64-en-ca-linda-preview`           | Obrázek kontejneru s `en-CA` národním prostředím a `en-CA-Linda`m hlasem           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo`m hlasem   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS`m hlasem        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo`m hlasem    |
| `1.3.0-amd64-en-ie-sean-preview`            | Obrázek kontejneru s `en-IE` národním prostředím a `en-IE-Sean`m hlasem            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo`m hlasem    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS`m hlasem        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo`m hlasem     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS`m hlasem     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS`m hlasem       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS`m hlasem     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS`m hlasem        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS`m hlasem         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS`m hlasem       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo`m hlasem    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo`m hlasem    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS`m hlasem        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo`m hlasem     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Obrázek kontejneru s `fi-FI` národním prostředím a `fi-FI-HeidiRUS`m hlasem        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline`m hlasem        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS`m hlasem     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Obrázek kontejneru s `fr-CH` národním prostředím a `fr-CH-Guillaume`m hlasem       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS`m hlasem     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo`m hlasem    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo`m hlasem     |
| `1.3.0-amd64-he-il-asaf-preview`            | Obrázek kontejneru s `he-IL` národním prostředím a `he-IL-Asaf`m hlasem            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Hemant`m hlasem          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana-Apollo`m hlasem  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana`m hlasem         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Obrázek kontejneru s `hi-IN` národním prostředím a `hi-IN-Kalpana`m hlasem         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Obrázek kontejneru s `hr-HR` národním prostředím a `hr-HR-Matej`m hlasem           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Obrázek kontejneru s `hu-HU` národním prostředím a `hu-HU-Szabolcs`m hlasem        |
| `1.3.0-amd64-id-id-andika-preview`          | Obrázek kontejneru s `id-ID` národním prostředím a `id-ID-Andika`m hlasem          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo`m hlasem   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS`m hlasem        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo`m hlasem    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS`m hlasem       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo`m hlasem   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS`m hlasem        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Obrázek kontejneru s `ms-MY` národním prostředím a `ms-MY-Rizwan`m hlasem          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Obrázek kontejneru s `nb-NO` národním prostředím a `nb-NO-HuldaRUS`m hlasem        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Obrázek kontejneru s `nl-NL` národním prostředím a `nl-NL-HannaRUS`m hlasem        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Obrázek kontejneru s `pl-PL` národním prostředím a `pl-PL-PaulinaRUS`m hlasem      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo`m hlasem   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS`m hlasem      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Obrázek kontejneru s `pt-PT` národním prostředím a `pt-PT-HeliaRUS`m hlasem        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Obrázek kontejneru s `ro-RO` národním prostředím a `ro-RO-Andrei`m hlasem          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-EkaterinaRUS`m hlasem    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Irina-Apollo`m hlasem    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím a `ru-RU-Pavel-Apollo`m hlasem    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Obrázek kontejneru s `sk-SK` národním prostředím a `sk-SK-Filip`m hlasem           |
| `1.3.0-amd64-sl-si-lado-preview`            | Obrázek kontejneru s `sl-SI` národním prostředím a `sl-SI-Lado`m hlasem            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Obrázek kontejneru s `sv-SE` národním prostředím a `sv-SE-HedvigRUS`m hlasem       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Obrázek kontejneru s `ta-IN` národním prostředím a `ta-IN-Valluvar`m hlasem        |
| `1.3.0-amd64-te-in-chitra-preview`          | Obrázek kontejneru s `te-IN` národním prostředím a `te-IN-Chitra`m hlasem          |
| `1.3.0-amd64-th-th-pattara-preview`         | Obrázek kontejneru s `th-TH` národním prostředím a `th-TH-Pattara`m hlasem         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Obrázek kontejneru s `tr-TR` národním prostředím a `tr-TR-SedaRUS`m hlasem         |
| `1.3.0-amd64-vi-vn-an-preview`              | Obrázek kontejneru s `vi-VN` národním prostředím a `vi-VN-An`m hlasem              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS`m hlasem       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo`m hlasem |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo`m hlasem   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Danny-Apollo`m hlasem    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-Tracy-Apollo`m hlasem    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Obrázek kontejneru s `zh-HK` národním prostředím a `zh-HK-TracyRUS`m hlasem        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-HanHanRUS`m hlasem       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Yating-Apollo`m hlasem   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Obrázek kontejneru s `zh-TW` národním prostředím a `zh-TW-Zhiwei-Apollo`m hlasem   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda`m hlasem           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-HeddaRUS`m hlasem        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo`m hlasem   |
| `1.2.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine`m hlasem       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS`m hlasem       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo`m hlasem   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS`m hlasem        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo`m hlasem    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo`m hlasem    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS`m hlasem        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo`m hlasem     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS`m hlasem     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS`m hlasem       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS`m hlasem     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS`m hlasem        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS`m hlasem         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS`m hlasem       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo`m hlasem    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo`m hlasem    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS`m hlasem        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo`m hlasem     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline`m hlasem        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS`m hlasem     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS`m hlasem     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo`m hlasem    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo`m hlasem     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo`m hlasem   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS`m hlasem        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo`m hlasem    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS`m hlasem       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo`m hlasem   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS`m hlasem        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo`m hlasem   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS`m hlasem      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS`m hlasem       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo`m hlasem |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo`m hlasem   |
| `1.1.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda`m hlasem           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Hedda`m hlasem           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-HeddaRUS`m hlasem        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím a `de-DE-Stefan-Apollo`m hlasem   |
| `1.1.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-Catherine`m hlasem       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím a `en-AU-HayleyRUS`m hlasem       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-George-Apollo`m hlasem   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-HazelRUS`m hlasem        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím a `en-GB-Susan-Apollo`m hlasem    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Heera-Apollo`m hlasem    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-PriyaRUS`m hlasem        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím a `en-IN-Ravi-Apollo`m hlasem     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS`m hlasem     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS`m hlasem       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS`m hlasem     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS`m hlasem        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS`m hlasem         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-HelenaRUS`m hlasem       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Laura-Apollo`m hlasem    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím a `es-ES-Pablo-Apollo`m hlasem    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-HildaRUS`m hlasem        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím a `es-MX-Raul-Apollo`m hlasem     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-Caroline`m hlasem        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím a `fr-CA-HarmonieRUS`m hlasem     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-HortenseRUS`m hlasem     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Julie-Apollo`m hlasem    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím a `fr-FR-Paul-Apollo`m hlasem     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-Cosimo-Apollo`m hlasem   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím a `it-IT-LuciaRUS`m hlasem        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ayumi-Apollo`m hlasem    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-HarukaRUS`m hlasem       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím a `ja-JP-Ichiro-Apollo`m hlasem   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím a `ko-KR-HeamiRUS`m hlasem        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-Daniel-Apollo`m hlasem   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím a `pt-BR-HeloisaRUS`m hlasem      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS`m hlasem       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo`m hlasem |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo`m hlasem   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-BenjaminRUS`m hlasem     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Guy24kRUS`m hlasem       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím a `en-US-Jessa24kRUS`m hlasem     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím a `en-US-JessaRUS`m hlasem        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím a `en-US-ZiraRUS`m hlasem         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-HuihuiRUS`m hlasem       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Kangkang-Apollo`m hlasem |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím a `zh-CN-Yaoyao-Apollo`m hlasem   |

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Image kontejneru [extrakce klíčových frází][ta-kp] můžete najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `keyphrase`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Rozpoznávání jazyka

Image kontejneru [rozpoznávání jazyka][ta-la] můžete najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `language`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/language`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analýza mínění

Image kontejneru [Analýza mínění][ta-se] můžete najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `sentiment`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
