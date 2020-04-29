---
title: Značky imagí kontejnerů Cognitive Services
titleSuffix: Azure Cognitive Services
description: Úplný seznam všech značek imagí kontejneru služby vnímání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 786fde90e1bcf47e08857c26235a27862c5b3d75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878761"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Značky image kontejneru Azure Cognitive Services

Azure Cognitive Services nabízí mnoho imagí kontejneru. Registry kontejnerů a odpovídající úložiště se v různých imagích kontejnerů liší. Každý název Image kontejneru nabízí více značek. Značka image kontejneru je mechanismus správy verzí image kontejneru. Tento článek je určený k použití jako ucelený odkaz pro výpis všech Cognitive Servicesch imagí kontejnerů a jejich dostupných značek.

> [!TIP]
> Při použití [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)aplikace věnujte velkou pozornost pro velká a malá písmena registru kontejnerů, úložiště, názvu image kontejneru a odpovídajících značek – protože rozlišují **velká a malá písmena**.

## <a name="anomaly-detector"></a>Detektor anomálií

Image kontejneru [detektoru anomálií][ad-containers] se dá najít v `containerpreview.azurecr.io` registru kontejneru. Je uložený v `microsoft` úložišti a má název `cognitive-services-anomaly-detector`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Computer Vision

Bitovou kopii kontejneru [počítačové zpracování obrazu][cv-containers] najdete v registru `containerpreview.azurecr.io` kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-read`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Tvář

Image kontejneru [Face][fa-containers] se dá najít v registru `containerpreview.azurecr.io` kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-face`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

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

Image kontejneru [pro rozpoznávání formulářů][fr-containers] se dá najít v registru `containerpreview.azurecr.io` kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-form-recognizer`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

Image kontejneru [Luis][lu-containers] se dá najít v registru `mcr.microsoft.com` kontejnerů. Je uložený v `azure-cognitive-services` úložišti a má název `luis`. Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/luis`.

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

Obrázek kontejneru [Custom Speech do textu][sp-cstt] lze nalézt v registru `containerpreview.azurecr.io` kontejneru. Je uložený v `microsoft` úložišti a má název `cognitive-services-custom-speech-to-text`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní image kontejneru [pro převod textu na řeč][sp-ctts] najdete v registru `containerpreview.azurecr.io` kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-custom-text-to-speech`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek s obrázkem [mluveného textu na text][sp-stt] najdete v registru `containerpreview.azurecr.io` kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-speech-to-text`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                  | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Obrázek kontejneru s `en-US` národním prostředím |
| `2.1.1-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.1.1-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.1.1-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.1.1-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.1.1-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.1.1-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.1.1-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.1.1-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.1.1-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.1.1-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.1.1-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.1.1-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.1.1-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.1.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.1.1-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.1.1-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.1.1-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.1.1-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.1.1-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.1.1-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.1.1-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.1.1-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.1.1-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.1.1-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.1.1-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.1.1-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.1.1-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.1.1-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.1.1-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.1.1-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.1.1-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.1.1-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.1.1-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.1.1-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.1.1-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.1.1-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.1.1-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.1.1-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.1.1-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.1.0-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.1.0-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.1.0-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.1.0-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.1.0-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.1.0-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.1.0-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.1.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.1.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.1.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.1.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.1.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.1.0-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.1.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.1.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.1.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.1.0-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.1.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.1.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.1.0-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.1.0-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.1.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.1.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.1.0-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.1.0-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.1.0-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.1.0-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.1.0-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.1.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.1.0-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.1.0-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.1.0-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.1.0-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.1.0-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.1.0-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.1.0-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.1.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.1.0-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.1.0-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.0.3-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.2-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.0.2-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.0.2-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.0.2-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.0.2-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.0.2-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.0.2-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.0.2-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.0.2-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.0.2-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.0.2-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.0.2-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.0.2-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.0.2-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.0.2-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.0.2-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.0.2-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.0.2-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.0.2-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.0.2-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.0.2-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.0.2-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.0.2-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.2-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.0.2-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.0.2-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.0.2-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.0.2-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.0.2-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.0.2-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.0.2-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.0.2-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.0.2-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.0.2-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.0.2-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.0.2-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.0.2-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.0.2-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.0.2-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.0.1-amd64-ar-ae-preview` | Obrázek kontejneru s `ar-AE` národním prostředím |
| `2.0.1-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.0.1-amd64-ar-kw-preview` | Obrázek kontejneru s `ar-KW` národním prostředím |
| `2.0.1-amd64-ar-qa-preview` | Obrázek kontejneru s `ar-QA` národním prostředím |
| `2.0.1-amd64-ar-sa-preview` | Obrázek kontejneru s `ar-SA` národním prostředím |
| `2.0.1-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.0.1-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.0.1-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.0.1-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.0.1-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.0.1-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.0.1-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.0.1-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.0.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.0.1-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.0.1-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.0.1-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.0.1-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.0.1-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.0.1-amd64-gu-in-preview` | Obrázek kontejneru s `gu-IN` národním prostředím |
| `2.0.1-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.0.1-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.0.1-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.1-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.0.1-amd64-mr-in-preview` | Obrázek kontejneru s `mr-IN` národním prostředím |
| `2.0.1-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.0.1-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.0.1-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.0.1-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.0.1-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.0.1-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.0.1-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.0.1-amd64-ta-in-preview` | Obrázek kontejneru s `ta-IN` národním prostředím |
| `2.0.1-amd64-te-in-preview` | Obrázek kontejneru s `te-IN` národním prostředím |
| `2.0.1-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.0.1-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.0.1-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.0.1-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.0.1-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `2.0.0-amd64-ar-eg-preview` | Obrázek kontejneru s `ar-EG` národním prostředím |
| `2.0.0-amd64-ca-es-preview` | Obrázek kontejneru s `ca-ES` národním prostředím |
| `2.0.0-amd64-da-dk-preview` | Obrázek kontejneru s `da-DK` národním prostředím |
| `2.0.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `2.0.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `2.0.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `2.0.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `2.0.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `2.0.0-amd64-en-nz-preview` | Obrázek kontejneru s `en-NZ` národním prostředím |
| `2.0.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `2.0.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `2.0.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `2.0.0-amd64-fi-fi-preview` | Obrázek kontejneru s `fi-FI` národním prostředím |
| `2.0.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `2.0.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `2.0.0-amd64-hi-in-preview` | Obrázek kontejneru s `hi-IN` národním prostředím |
| `2.0.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `2.0.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `2.0.0-amd64-ko-kr-preview` | Obrázek kontejneru s `ko-KR` národním prostředím |
| `2.0.0-amd64-nb-no-preview` | Obrázek kontejneru s `nb-NO` národním prostředím |
| `2.0.0-amd64-nl-nl-preview` | Obrázek kontejneru s `nl-NL` národním prostředím |
| `2.0.0-amd64-pl-pl-preview` | Obrázek kontejneru s `pl-PL` národním prostředím |
| `2.0.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `2.0.0-amd64-pt-pt-preview` | Obrázek kontejneru s `pt-PT` národním prostředím |
| `2.0.0-amd64-ru-ru-preview` | Obrázek kontejneru s `ru-RU` národním prostředím |
| `2.0.0-amd64-sv-se-preview` | Obrázek kontejneru s `sv-SE` národním prostředím |
| `2.0.0-amd64-th-th-preview` | Obrázek kontejneru s `th-TH` národním prostředím |
| `2.0.0-amd64-tr-tr-preview` | Obrázek kontejneru s `tr-TR` národním prostředím |
| `2.0.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `2.0.0-amd64-zh-hk-preview` | Obrázek kontejneru s `zh-HK` národním prostředím |
| `2.0.0-amd64-zh-tw-preview` | Obrázek kontejneru s `zh-TW` národním prostředím |
| `1.2.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.2.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.2.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.2.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.2.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.2.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.2.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.2.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.2.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.2.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.2.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.2.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.2.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.2.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.3-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.3-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.3-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.3-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.3-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.3-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.3-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.3-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.3-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.3-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.3-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.3-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.3-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.3-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.2-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.2-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.2-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.2-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.2-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.2-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.2-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.2-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.2-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.2-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.2-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.2-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.2-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.2-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.1-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.1-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.1-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.1-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.1-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.1-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.1-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.1-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.1-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.1-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.1-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.1-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.1-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.1-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.1.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.1.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.1.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.1.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.1.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.1.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.1.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.1.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.1.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.1.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.1.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.1.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.1.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.1.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |
| `1.0.0-amd64-de-de-preview` | Obrázek kontejneru s `de-DE` národním prostředím |
| `1.0.0-amd64-en-au-preview` | Obrázek kontejneru s `en-AU` národním prostředím |
| `1.0.0-amd64-en-ca-preview` | Obrázek kontejneru s `en-CA` národním prostředím |
| `1.0.0-amd64-en-gb-preview` | Obrázek kontejneru s `en-GB` národním prostředím |
| `1.0.0-amd64-en-in-preview` | Obrázek kontejneru s `en-IN` národním prostředím |
| `1.0.0-amd64-en-us-preview` | Obrázek kontejneru s `en-US` národním prostředím |
| `1.0.0-amd64-es-es-preview` | Obrázek kontejneru s `es-ES` národním prostředím |
| `1.0.0-amd64-es-mx-preview` | Obrázek kontejneru s `es-MX` národním prostředím |
| `1.0.0-amd64-fr-ca-preview` | Obrázek kontejneru s `fr-CA` národním prostředím |
| `1.0.0-amd64-fr-fr-preview` | Obrázek kontejneru s `fr-FR` národním prostředím |
| `1.0.0-amd64-it-it-preview` | Obrázek kontejneru s `it-IT` národním prostředím |
| `1.0.0-amd64-ja-jp-preview` | Obrázek kontejneru s `ja-JP` národním prostředím |
| `1.0.0-amd64-pt-br-preview` | Obrázek kontejneru s `pt-BR` národním prostředím |
| `1.0.0-amd64-zh-cn-preview` | Obrázek kontejneru s `zh-CN` národním prostředím |

## <a name="text-to-speech"></a>Převod textu na řeč

Obrázek kontejneru [Převod textu na řeč][sp-tts] najdete v registru `containerpreview.azurecr.io` kontejnerů. Je uložený v `microsoft` úložišti a má název `cognitive-services-text-to-speech`. Plně kvalifikovaný název Image kontejneru je, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s `en-US` národním prostředím `en-US-JessaRUS` a hlasem        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Obrázek kontejneru s `ar-EG` národním prostředím `ar-EG-Hoda` a hlasem            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Obrázek kontejneru s `ar-SA` národním prostředím `ar-SA-Naayf` a hlasem           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Obrázek kontejneru s `bg-BG` národním prostředím `bg-BG-Ivan` a hlasem            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Obrázek kontejneru s `ca-ES` národním prostředím `ca-ES-HerenaRUS` a hlasem       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Obrázek kontejneru s `cs-CZ` národním prostředím `cs-CZ-Jakub` a hlasem           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Obrázek kontejneru s `da-DK` národním prostředím `da-DK-HelleRUS` a hlasem        |
| `1.3.0-amd64-de-at-michael-preview`         | Obrázek kontejneru s `de-AT` národním prostředím `de-AT-Michael` a hlasem         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Obrázek kontejneru s `de-CH` národním prostředím `de-CH-Karsten` a hlasem         |
| `1.3.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Hedda` a hlasem           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Hedda` a hlasem           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-HeddaRUS` a hlasem        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Stefan-Apollo` a hlasem   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Obrázek kontejneru s `el-GR` národním prostředím `el-GR-Stefanos` a hlasem        |
| `1.3.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím `en-AU-Catherine` a hlasem       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím `en-AU-HayleyRUS` a hlasem       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Obrázek kontejneru s `en-CA` národním prostředím `en-CA-HeatherRUS` a hlasem      |
| `1.3.0-amd64-en-ca-linda-preview`           | Obrázek kontejneru s `en-CA` národním prostředím `en-CA-Linda` a hlasem           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-George-Apollo` a hlasem   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-HazelRUS` a hlasem        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-Susan-Apollo` a hlasem    |
| `1.3.0-amd64-en-ie-sean-preview`            | Obrázek kontejneru s `en-IE` národním prostředím `en-IE-Sean` a hlasem            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-Heera-Apollo` a hlasem    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-PriyaRUS` a hlasem        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-Ravi-Apollo` a hlasem     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-BenjaminRUS` a hlasem     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím `en-US-Guy24kRUS` a hlasem       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-Jessa24kRUS` a hlasem     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím `en-US-JessaRUS` a hlasem        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím `en-US-ZiraRUS` a hlasem         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-HelenaRUS` a hlasem       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-Laura-Apollo` a hlasem    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-Pablo-Apollo` a hlasem    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím `es-MX-HildaRUS` a hlasem        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím `es-MX-Raul-Apollo` a hlasem     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Obrázek kontejneru s `fi-FI` národním prostředím `fi-FI-HeidiRUS` a hlasem        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím `fr-CA-Caroline` a hlasem        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím `fr-CA-HarmonieRUS` a hlasem     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Obrázek kontejneru s `fr-CH` národním prostředím `fr-CH-Guillaume` a hlasem       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-HortenseRUS` a hlasem     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-Julie-Apollo` a hlasem    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-Paul-Apollo` a hlasem     |
| `1.3.0-amd64-he-il-asaf-preview`            | Obrázek kontejneru s `he-IL` národním prostředím `he-IL-Asaf` a hlasem            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Obrázek kontejneru s `hi-IN` národním prostředím `hi-IN-Hemant` a hlasem          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s `hi-IN` národním prostředím `hi-IN-Kalpana-Apollo` a hlasem  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s `hi-IN` národním prostředím `hi-IN-Kalpana` a hlasem         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Obrázek kontejneru s `hi-IN` národním prostředím `hi-IN-Kalpana` a hlasem         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Obrázek kontejneru s `hr-HR` národním prostředím `hr-HR-Matej` a hlasem           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Obrázek kontejneru s `hu-HU` národním prostředím `hu-HU-Szabolcs` a hlasem        |
| `1.3.0-amd64-id-id-andika-preview`          | Obrázek kontejneru s `id-ID` národním prostředím `id-ID-Andika` a hlasem          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím `it-IT-Cosimo-Apollo` a hlasem   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím `it-IT-LuciaRUS` a hlasem        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-Ayumi-Apollo` a hlasem    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-HarukaRUS` a hlasem       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-Ichiro-Apollo` a hlasem   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím `ko-KR-HeamiRUS` a hlasem        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Obrázek kontejneru s `ms-MY` národním prostředím `ms-MY-Rizwan` a hlasem          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Obrázek kontejneru s `nb-NO` národním prostředím `nb-NO-HuldaRUS` a hlasem        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Obrázek kontejneru s `nl-NL` národním prostředím `nl-NL-HannaRUS` a hlasem        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Obrázek kontejneru s `pl-PL` národním prostředím `pl-PL-PaulinaRUS` a hlasem      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím `pt-BR-Daniel-Apollo` a hlasem   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím `pt-BR-HeloisaRUS` a hlasem      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Obrázek kontejneru s `pt-PT` národním prostředím `pt-PT-HeliaRUS` a hlasem        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Obrázek kontejneru s `ro-RO` národním prostředím `ro-RO-Andrei` a hlasem          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím `ru-RU-EkaterinaRUS` a hlasem    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím `ru-RU-Irina-Apollo` a hlasem    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Obrázek kontejneru s `ru-RU` národním prostředím `ru-RU-Pavel-Apollo` a hlasem    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Obrázek kontejneru s `sk-SK` národním prostředím `sk-SK-Filip` a hlasem           |
| `1.3.0-amd64-sl-si-lado-preview`            | Obrázek kontejneru s `sl-SI` národním prostředím `sl-SI-Lado` a hlasem            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Obrázek kontejneru s `sv-SE` národním prostředím `sv-SE-HedvigRUS` a hlasem       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Obrázek kontejneru s `ta-IN` národním prostředím `ta-IN-Valluvar` a hlasem        |
| `1.3.0-amd64-te-in-chitra-preview`          | Obrázek kontejneru s `te-IN` národním prostředím `te-IN-Chitra` a hlasem          |
| `1.3.0-amd64-th-th-pattara-preview`         | Obrázek kontejneru s `th-TH` národním prostředím `th-TH-Pattara` a hlasem         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Obrázek kontejneru s `tr-TR` národním prostředím `tr-TR-SedaRUS` a hlasem         |
| `1.3.0-amd64-vi-vn-an-preview`              | Obrázek kontejneru s `vi-VN` národním prostředím `vi-VN-An` a hlasem              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-HuihuiRUS` a hlasem       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Kangkang-Apollo` a hlasem |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Yaoyao-Apollo` a hlasem   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Obrázek kontejneru s `zh-HK` národním prostředím `zh-HK-Danny-Apollo` a hlasem    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Obrázek kontejneru s `zh-HK` národním prostředím `zh-HK-Tracy-Apollo` a hlasem    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Obrázek kontejneru s `zh-HK` národním prostředím `zh-HK-TracyRUS` a hlasem        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Obrázek kontejneru s `zh-TW` národním prostředím `zh-TW-HanHanRUS` a hlasem       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Obrázek kontejneru s `zh-TW` národním prostředím `zh-TW-Yating-Apollo` a hlasem   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Obrázek kontejneru s `zh-TW` národním prostředím `zh-TW-Zhiwei-Apollo` a hlasem   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Hedda` a hlasem           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-HeddaRUS` a hlasem        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Stefan-Apollo` a hlasem   |
| `1.2.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím `en-AU-Catherine` a hlasem       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím `en-AU-HayleyRUS` a hlasem       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-George-Apollo` a hlasem   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-HazelRUS` a hlasem        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-Susan-Apollo` a hlasem    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-Heera-Apollo` a hlasem    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-PriyaRUS` a hlasem        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-Ravi-Apollo` a hlasem     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-BenjaminRUS` a hlasem     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím `en-US-Guy24kRUS` a hlasem       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-Jessa24kRUS` a hlasem     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím `en-US-JessaRUS` a hlasem        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím `en-US-ZiraRUS` a hlasem         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-HelenaRUS` a hlasem       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-Laura-Apollo` a hlasem    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-Pablo-Apollo` a hlasem    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím `es-MX-HildaRUS` a hlasem        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím `es-MX-Raul-Apollo` a hlasem     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím `fr-CA-Caroline` a hlasem        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím `fr-CA-HarmonieRUS` a hlasem     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-HortenseRUS` a hlasem     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-Julie-Apollo` a hlasem    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-Paul-Apollo` a hlasem     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím `it-IT-Cosimo-Apollo` a hlasem   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím `it-IT-LuciaRUS` a hlasem        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-Ayumi-Apollo` a hlasem    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-HarukaRUS` a hlasem       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-Ichiro-Apollo` a hlasem   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím `ko-KR-HeamiRUS` a hlasem        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím `pt-BR-Daniel-Apollo` a hlasem   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím `pt-BR-HeloisaRUS` a hlasem      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-HuihuiRUS` a hlasem       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Kangkang-Apollo` a hlasem |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Yaoyao-Apollo` a hlasem   |
| `1.1.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Hedda` a hlasem           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Hedda` a hlasem           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-HeddaRUS` a hlasem        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s `de-DE` národním prostředím `de-DE-Stefan-Apollo` a hlasem   |
| `1.1.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s `en-AU` národním prostředím `en-AU-Catherine` a hlasem       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s `en-AU` národním prostředím `en-AU-HayleyRUS` a hlasem       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-George-Apollo` a hlasem   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-HazelRUS` a hlasem        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s `en-GB` národním prostředím `en-GB-Susan-Apollo` a hlasem    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-Heera-Apollo` a hlasem    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-PriyaRUS` a hlasem        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s `en-IN` národním prostředím `en-IN-Ravi-Apollo` a hlasem     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-BenjaminRUS` a hlasem     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím `en-US-Guy24kRUS` a hlasem       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-Jessa24kRUS` a hlasem     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím `en-US-JessaRUS` a hlasem        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím `en-US-ZiraRUS` a hlasem         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-HelenaRUS` a hlasem       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-Laura-Apollo` a hlasem    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s `es-ES` národním prostředím `es-ES-Pablo-Apollo` a hlasem    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s `es-MX` národním prostředím `es-MX-HildaRUS` a hlasem        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s `es-MX` národním prostředím `es-MX-Raul-Apollo` a hlasem     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s `fr-CA` národním prostředím `fr-CA-Caroline` a hlasem        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s `fr-CA` národním prostředím `fr-CA-HarmonieRUS` a hlasem     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-HortenseRUS` a hlasem     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-Julie-Apollo` a hlasem    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s `fr-FR` národním prostředím `fr-FR-Paul-Apollo` a hlasem     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s `it-IT` národním prostředím `it-IT-Cosimo-Apollo` a hlasem   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s `it-IT` národním prostředím `it-IT-LuciaRUS` a hlasem        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-Ayumi-Apollo` a hlasem    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-HarukaRUS` a hlasem       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s `ja-JP` národním prostředím `ja-JP-Ichiro-Apollo` a hlasem   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s `ko-KR` národním prostředím `ko-KR-HeamiRUS` a hlasem        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s `pt-BR` národním prostředím `pt-BR-Daniel-Apollo` a hlasem   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s `pt-BR` národním prostředím `pt-BR-HeloisaRUS` a hlasem      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-HuihuiRUS` a hlasem       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Kangkang-Apollo` a hlasem |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Yaoyao-Apollo` a hlasem   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-BenjaminRUS` a hlasem     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s `en-US` národním prostředím `en-US-Guy24kRUS` a hlasem       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s `en-US` národním prostředím `en-US-Jessa24kRUS` a hlasem     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s `en-US` národním prostředím `en-US-JessaRUS` a hlasem        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s `en-US` národním prostředím `en-US-ZiraRUS` a hlasem         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-HuihuiRUS` a hlasem       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Kangkang-Apollo` a hlasem |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s `zh-CN` národním prostředím `zh-CN-Yaoyao-Apollo` a hlasem   |

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Bitovou kopii kontejneru [extrakce klíčových frází][ta-kp] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services` úložišti a má název `keyphrase`. Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

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

Bitovou kopii kontejneru [rozpoznávání jazyka][ta-la] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services` úložišti a má název `language`. Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/language`.

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

Bitovou kopii kontejneru [Analýza mínění][ta-se] můžete najít v části `mcr.microsoft.com` Registry kontejneru syndikát. Je uložený v `azure-cognitive-services` úložišti a má název `sentiment`. Plně kvalifikovaný název Image kontejneru je, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

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
