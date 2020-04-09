---
title: Značky imagí kontejnerů Cognitive Services
titleSuffix: Azure Cognitive Services
description: Komplexní seznam všech značek image kontejneru služby Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 786fde90e1bcf47e08857c26235a27862c5b3d75
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878761"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Značky obrázků kontejneru Azure Cognitive Services

Azure Cognitive Services nabízí mnoho iniciace kontejnerů. Registry kontejnerů a odpovídající úložiště se liší mezi image kontejneru. Každý název obrázku kontejneru nabízí více značek. Značka image kontejneru je mechanismus správy verzí image kontejneru. Tento článek je určen pro použití jako komplexní odkaz pro výpis všech image kontejneru cognitive services a jejich dostupné značky.

> [!TIP]
> Při [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)použití věnujte velkou pozornost velikosti registru kontejneru, úložiště, názvu bitové kopie kontejneru a odpovídající značky , protože se **rozlišují malá a velká písmena**.

## <a name="anomaly-detector"></a>Detektor anomálií

Image kontejneru [detektoranolií][ad-containers] lze `containerpreview.azurecr.io` nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-anomaly-detector`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Computer Vision

Image [kontejneru počítačového zpracování][cv-containers] `containerpreview.azurecr.io` lze nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-read`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-read`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Tvář

Image kontejneru [plochy][fa-containers] lze `containerpreview.azurecr.io` nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-face`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-face`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
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

Image [kontejneru nástroje pro rozpoznávání formulářů][fr-containers] naleznete v registru kontejneru. `containerpreview.azurecr.io` Je umístěn v `microsoft` úložišti a `cognitive-services-form-recognizer`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

Image kontejneru [LUIS][lu-containers] lze `mcr.microsoft.com` nalézt v syndikátu registru kontejnerů. Je umístěn v `azure-cognitive-services` úložišti a `luis`je pojmenován . Plně kvalifikovaný název bitové `mcr.microsoft.com/azure-cognitive-services/luis`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
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

## <a name="custom-speech-to-text"></a>Vlastní převod řeči na text

[Vlastní image kontejneru řeči na text][sp-cstt] lze `containerpreview.azurecr.io` nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-custom-speech-to-text`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Obrázek [kontejneru vlastní převod textu na][sp-ctts] `containerpreview.azurecr.io` řeč lze nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-custom-text-to-speech`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků            | Poznámky |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek [kontejneru převodu na text][sp-stt] `containerpreview.azurecr.io` lze nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-speech-to-text`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                  | Poznámky                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Image kontejneru `en-US` s národním prostředím. |
| `2.1.1-amd64-ar-ae-preview` | Image kontejneru `ar-AE` s národním prostředím. |
| `2.1.1-amd64-ar-eg-preview` | Image kontejneru `ar-EG` s národním prostředím. |
| `2.1.1-amd64-ar-kw-preview` | Image kontejneru `ar-KW` s národním prostředím. |
| `2.1.1-amd64-ar-qa-preview` | Image kontejneru `ar-QA` s národním prostředím. |
| `2.1.1-amd64-ar-sa-preview` | Image kontejneru `ar-SA` s národním prostředím. |
| `2.1.1-amd64-ca-es-preview` | Image kontejneru `ca-ES` s národním prostředím. |
| `2.1.1-amd64-da-dk-preview` | Image kontejneru `da-DK` s národním prostředím. |
| `2.1.1-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `2.1.1-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `2.1.1-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `2.1.1-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `2.1.1-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `2.1.1-amd64-en-nz-preview` | Image kontejneru `en-NZ` s národním prostředím. |
| `2.1.1-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `2.1.1-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `2.1.1-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `2.1.1-amd64-fi-fi-preview` | Image kontejneru `fi-FI` s národním prostředím. |
| `2.1.1-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `2.1.1-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `2.1.1-amd64-gu-in-preview` | Image kontejneru `gu-IN` s národním prostředím. |
| `2.1.1-amd64-hi-in-preview` | Image kontejneru `hi-IN` s národním prostředím. |
| `2.1.1-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `2.1.1-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `2.1.1-amd64-ko-kr-preview` | Image kontejneru `ko-KR` s národním prostředím. |
| `2.1.1-amd64-mr-in-preview` | Image kontejneru `mr-IN` s národním prostředím. |
| `2.1.1-amd64-nb-no-preview` | Image kontejneru `nb-NO` s národním prostředím. |
| `2.1.1-amd64-nl-nl-preview` | Image kontejneru `nl-NL` s národním prostředím. |
| `2.1.1-amd64-pl-pl-preview` | Image kontejneru `pl-PL` s národním prostředím. |
| `2.1.1-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `2.1.1-amd64-pt-pt-preview` | Image kontejneru `pt-PT` s národním prostředím. |
| `2.1.1-amd64-ru-ru-preview` | Image kontejneru `ru-RU` s národním prostředím. |
| `2.1.1-amd64-sv-se-preview` | Image kontejneru `sv-SE` s národním prostředím. |
| `2.1.1-amd64-ta-in-preview` | Image kontejneru `ta-IN` s národním prostředím. |
| `2.1.1-amd64-te-in-preview` | Image kontejneru `te-IN` s národním prostředím. |
| `2.1.1-amd64-th-th-preview` | Image kontejneru `th-TH` s národním prostředím. |
| `2.1.1-amd64-tr-tr-preview` | Image kontejneru `tr-TR` s národním prostředím. |
| `2.1.1-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `2.1.1-amd64-zh-hk-preview` | Image kontejneru `zh-HK` s národním prostředím. |
| `2.1.1-amd64-zh-tw-preview` | Image kontejneru `zh-TW` s národním prostředím. |
| `2.1.0-amd64-ar-ae-preview` | Image kontejneru `ar-AE` s národním prostředím. |
| `2.1.0-amd64-ar-eg-preview` | Image kontejneru `ar-EG` s národním prostředím. |
| `2.1.0-amd64-ar-kw-preview` | Image kontejneru `ar-KW` s národním prostředím. |
| `2.1.0-amd64-ar-qa-preview` | Image kontejneru `ar-QA` s národním prostředím. |
| `2.1.0-amd64-ar-sa-preview` | Image kontejneru `ar-SA` s národním prostředím. |
| `2.1.0-amd64-ca-es-preview` | Image kontejneru `ca-ES` s národním prostředím. |
| `2.1.0-amd64-da-dk-preview` | Image kontejneru `da-DK` s národním prostředím. |
| `2.1.0-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `2.1.0-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `2.1.0-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `2.1.0-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `2.1.0-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `2.1.0-amd64-en-nz-preview` | Image kontejneru `en-NZ` s národním prostředím. |
| `2.1.0-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `2.1.0-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `2.1.0-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `2.1.0-amd64-fi-fi-preview` | Image kontejneru `fi-FI` s národním prostředím. |
| `2.1.0-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `2.1.0-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `2.1.0-amd64-gu-in-preview` | Image kontejneru `gu-IN` s národním prostředím. |
| `2.1.0-amd64-hi-in-preview` | Image kontejneru `hi-IN` s národním prostředím. |
| `2.1.0-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `2.1.0-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `2.1.0-amd64-ko-kr-preview` | Image kontejneru `ko-KR` s národním prostředím. |
| `2.1.0-amd64-mr-in-preview` | Image kontejneru `mr-IN` s národním prostředím. |
| `2.1.0-amd64-nb-no-preview` | Image kontejneru `nb-NO` s národním prostředím. |
| `2.1.0-amd64-nl-nl-preview` | Image kontejneru `nl-NL` s národním prostředím. |
| `2.1.0-amd64-pl-pl-preview` | Image kontejneru `pl-PL` s národním prostředím. |
| `2.1.0-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `2.1.0-amd64-pt-pt-preview` | Image kontejneru `pt-PT` s národním prostředím. |
| `2.1.0-amd64-ru-ru-preview` | Image kontejneru `ru-RU` s národním prostředím. |
| `2.1.0-amd64-sv-se-preview` | Image kontejneru `sv-SE` s národním prostředím. |
| `2.1.0-amd64-ta-in-preview` | Image kontejneru `ta-IN` s národním prostředím. |
| `2.1.0-amd64-te-in-preview` | Image kontejneru `te-IN` s národním prostředím. |
| `2.1.0-amd64-th-th-preview` | Image kontejneru `th-TH` s národním prostředím. |
| `2.1.0-amd64-tr-tr-preview` | Image kontejneru `tr-TR` s národním prostředím. |
| `2.1.0-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `2.1.0-amd64-zh-hk-preview` | Image kontejneru `zh-HK` s národním prostředím. |
| `2.1.0-amd64-zh-tw-preview` | Image kontejneru `zh-TW` s národním prostředím. |
| `2.0.3-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `2.0.2-amd64-ar-ae-preview` | Image kontejneru `ar-AE` s národním prostředím. |
| `2.0.2-amd64-ar-eg-preview` | Image kontejneru `ar-EG` s národním prostředím. |
| `2.0.2-amd64-ar-kw-preview` | Image kontejneru `ar-KW` s národním prostředím. |
| `2.0.2-amd64-ar-qa-preview` | Image kontejneru `ar-QA` s národním prostředím. |
| `2.0.2-amd64-ar-sa-preview` | Image kontejneru `ar-SA` s národním prostředím. |
| `2.0.2-amd64-ca-es-preview` | Image kontejneru `ca-ES` s národním prostředím. |
| `2.0.2-amd64-da-dk-preview` | Image kontejneru `da-DK` s národním prostředím. |
| `2.0.2-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `2.0.2-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `2.0.2-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `2.0.2-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `2.0.2-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `2.0.2-amd64-en-nz-preview` | Image kontejneru `en-NZ` s národním prostředím. |
| `2.0.2-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `2.0.2-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `2.0.2-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `2.0.2-amd64-fi-fi-preview` | Image kontejneru `fi-FI` s národním prostředím. |
| `2.0.2-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `2.0.2-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `2.0.2-amd64-gu-in-preview` | Image kontejneru `gu-IN` s národním prostředím. |
| `2.0.2-amd64-hi-in-preview` | Image kontejneru `hi-IN` s národním prostředím. |
| `2.0.2-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `2.0.2-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `2.0.2-amd64-ko-kr-preview` | Image kontejneru `ko-KR` s národním prostředím. |
| `2.0.2-amd64-mr-in-preview` | Image kontejneru `mr-IN` s národním prostředím. |
| `2.0.2-amd64-nb-no-preview` | Image kontejneru `nb-NO` s národním prostředím. |
| `2.0.2-amd64-nl-nl-preview` | Image kontejneru `nl-NL` s národním prostředím. |
| `2.0.2-amd64-pl-pl-preview` | Image kontejneru `pl-PL` s národním prostředím. |
| `2.0.2-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `2.0.2-amd64-pt-pt-preview` | Image kontejneru `pt-PT` s národním prostředím. |
| `2.0.2-amd64-ru-ru-preview` | Image kontejneru `ru-RU` s národním prostředím. |
| `2.0.2-amd64-sv-se-preview` | Image kontejneru `sv-SE` s národním prostředím. |
| `2.0.2-amd64-ta-in-preview` | Image kontejneru `ta-IN` s národním prostředím. |
| `2.0.2-amd64-te-in-preview` | Image kontejneru `te-IN` s národním prostředím. |
| `2.0.2-amd64-th-th-preview` | Image kontejneru `th-TH` s národním prostředím. |
| `2.0.2-amd64-tr-tr-preview` | Image kontejneru `tr-TR` s národním prostředím. |
| `2.0.2-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `2.0.2-amd64-zh-hk-preview` | Image kontejneru `zh-HK` s národním prostředím. |
| `2.0.2-amd64-zh-tw-preview` | Image kontejneru `zh-TW` s národním prostředím. |
| `2.0.1-amd64-ar-ae-preview` | Image kontejneru `ar-AE` s národním prostředím. |
| `2.0.1-amd64-ar-eg-preview` | Image kontejneru `ar-EG` s národním prostředím. |
| `2.0.1-amd64-ar-kw-preview` | Image kontejneru `ar-KW` s národním prostředím. |
| `2.0.1-amd64-ar-qa-preview` | Image kontejneru `ar-QA` s národním prostředím. |
| `2.0.1-amd64-ar-sa-preview` | Image kontejneru `ar-SA` s národním prostředím. |
| `2.0.1-amd64-ca-es-preview` | Image kontejneru `ca-ES` s národním prostředím. |
| `2.0.1-amd64-da-dk-preview` | Image kontejneru `da-DK` s národním prostředím. |
| `2.0.1-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `2.0.1-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `2.0.1-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `2.0.1-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `2.0.1-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `2.0.1-amd64-en-nz-preview` | Image kontejneru `en-NZ` s národním prostředím. |
| `2.0.1-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `2.0.1-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `2.0.1-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `2.0.1-amd64-fi-fi-preview` | Image kontejneru `fi-FI` s národním prostředím. |
| `2.0.1-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `2.0.1-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `2.0.1-amd64-gu-in-preview` | Image kontejneru `gu-IN` s národním prostředím. |
| `2.0.1-amd64-hi-in-preview` | Image kontejneru `hi-IN` s národním prostředím. |
| `2.0.1-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `2.0.1-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `2.0.1-amd64-ko-kr-preview` | Image kontejneru `ko-KR` s národním prostředím. |
| `2.0.1-amd64-mr-in-preview` | Image kontejneru `mr-IN` s národním prostředím. |
| `2.0.1-amd64-nb-no-preview` | Image kontejneru `nb-NO` s národním prostředím. |
| `2.0.1-amd64-nl-nl-preview` | Image kontejneru `nl-NL` s národním prostředím. |
| `2.0.1-amd64-pl-pl-preview` | Image kontejneru `pl-PL` s národním prostředím. |
| `2.0.1-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `2.0.1-amd64-pt-pt-preview` | Image kontejneru `pt-PT` s národním prostředím. |
| `2.0.1-amd64-ru-ru-preview` | Image kontejneru `ru-RU` s národním prostředím. |
| `2.0.1-amd64-sv-se-preview` | Image kontejneru `sv-SE` s národním prostředím. |
| `2.0.1-amd64-ta-in-preview` | Image kontejneru `ta-IN` s národním prostředím. |
| `2.0.1-amd64-te-in-preview` | Image kontejneru `te-IN` s národním prostředím. |
| `2.0.1-amd64-th-th-preview` | Image kontejneru `th-TH` s národním prostředím. |
| `2.0.1-amd64-tr-tr-preview` | Image kontejneru `tr-TR` s národním prostředím. |
| `2.0.1-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `2.0.1-amd64-zh-hk-preview` | Image kontejneru `zh-HK` s národním prostředím. |
| `2.0.1-amd64-zh-tw-preview` | Image kontejneru `zh-TW` s národním prostředím. |
| `2.0.0-amd64-ar-eg-preview` | Image kontejneru `ar-EG` s národním prostředím. |
| `2.0.0-amd64-ca-es-preview` | Image kontejneru `ca-ES` s národním prostředím. |
| `2.0.0-amd64-da-dk-preview` | Image kontejneru `da-DK` s národním prostředím. |
| `2.0.0-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `2.0.0-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `2.0.0-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `2.0.0-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `2.0.0-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `2.0.0-amd64-en-nz-preview` | Image kontejneru `en-NZ` s národním prostředím. |
| `2.0.0-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `2.0.0-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `2.0.0-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `2.0.0-amd64-fi-fi-preview` | Image kontejneru `fi-FI` s národním prostředím. |
| `2.0.0-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `2.0.0-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `2.0.0-amd64-hi-in-preview` | Image kontejneru `hi-IN` s národním prostředím. |
| `2.0.0-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `2.0.0-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `2.0.0-amd64-ko-kr-preview` | Image kontejneru `ko-KR` s národním prostředím. |
| `2.0.0-amd64-nb-no-preview` | Image kontejneru `nb-NO` s národním prostředím. |
| `2.0.0-amd64-nl-nl-preview` | Image kontejneru `nl-NL` s národním prostředím. |
| `2.0.0-amd64-pl-pl-preview` | Image kontejneru `pl-PL` s národním prostředím. |
| `2.0.0-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `2.0.0-amd64-pt-pt-preview` | Image kontejneru `pt-PT` s národním prostředím. |
| `2.0.0-amd64-ru-ru-preview` | Image kontejneru `ru-RU` s národním prostředím. |
| `2.0.0-amd64-sv-se-preview` | Image kontejneru `sv-SE` s národním prostředím. |
| `2.0.0-amd64-th-th-preview` | Image kontejneru `th-TH` s národním prostředím. |
| `2.0.0-amd64-tr-tr-preview` | Image kontejneru `tr-TR` s národním prostředím. |
| `2.0.0-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `2.0.0-amd64-zh-hk-preview` | Image kontejneru `zh-HK` s národním prostředím. |
| `2.0.0-amd64-zh-tw-preview` | Image kontejneru `zh-TW` s národním prostředím. |
| `1.2.0-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `1.2.0-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `1.2.0-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `1.2.0-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `1.2.0-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `1.2.0-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `1.2.0-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `1.2.0-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `1.2.0-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `1.2.0-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `1.2.0-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `1.2.0-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `1.2.0-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `1.2.0-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `1.1.3-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `1.1.3-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `1.1.3-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `1.1.3-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `1.1.3-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `1.1.3-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `1.1.3-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `1.1.3-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `1.1.3-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `1.1.3-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `1.1.3-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `1.1.3-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `1.1.3-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `1.1.3-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `1.1.2-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `1.1.2-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `1.1.2-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `1.1.2-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `1.1.2-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `1.1.2-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `1.1.2-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `1.1.2-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `1.1.2-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `1.1.2-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `1.1.2-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `1.1.2-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `1.1.2-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `1.1.2-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `1.1.1-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `1.1.1-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `1.1.1-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `1.1.1-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `1.1.1-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `1.1.1-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `1.1.1-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `1.1.1-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `1.1.1-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `1.1.1-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `1.1.1-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `1.1.1-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `1.1.1-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `1.1.1-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `1.1.0-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `1.1.0-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `1.1.0-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `1.1.0-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `1.1.0-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `1.1.0-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `1.1.0-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `1.1.0-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `1.1.0-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `1.1.0-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `1.1.0-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `1.1.0-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `1.1.0-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `1.1.0-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |
| `1.0.0-amd64-de-de-preview` | Image kontejneru `de-DE` s národním prostředím. |
| `1.0.0-amd64-en-au-preview` | Image kontejneru `en-AU` s národním prostředím. |
| `1.0.0-amd64-en-ca-preview` | Image kontejneru `en-CA` s národním prostředím. |
| `1.0.0-amd64-en-gb-preview` | Image kontejneru `en-GB` s národním prostředím. |
| `1.0.0-amd64-en-in-preview` | Image kontejneru `en-IN` s národním prostředím. |
| `1.0.0-amd64-en-us-preview` | Image kontejneru `en-US` s národním prostředím. |
| `1.0.0-amd64-es-es-preview` | Image kontejneru `es-ES` s národním prostředím. |
| `1.0.0-amd64-es-mx-preview` | Image kontejneru `es-MX` s národním prostředím. |
| `1.0.0-amd64-fr-ca-preview` | Image kontejneru `fr-CA` s národním prostředím. |
| `1.0.0-amd64-fr-fr-preview` | Image kontejneru `fr-FR` s národním prostředím. |
| `1.0.0-amd64-it-it-preview` | Image kontejneru `it-IT` s národním prostředím. |
| `1.0.0-amd64-ja-jp-preview` | Image kontejneru `ja-JP` s národním prostředím. |
| `1.0.0-amd64-pt-br-preview` | Image kontejneru `pt-BR` s národním prostředím. |
| `1.0.0-amd64-zh-cn-preview` | Image kontejneru `zh-CN` s národním prostředím. |

## <a name="text-to-speech"></a>Převod textu na řeč

Obrázek [kontejneru převodu textu na][sp-tts] `containerpreview.azurecr.io` řeč lze nalézt v registru kontejneru. Je umístěn v `microsoft` úložišti a `cognitive-services-text-to-speech`je pojmenován . Plně kvalifikovaný název bitové `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                                  | Poznámky                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obrázek kontejneru s národním prostředím `en-US` a `en-US-JessaRUS` hlasem.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Obrázek kontejneru s národním prostředím `ar-EG` a `ar-EG-Hoda` hlasem.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Obrázek kontejneru s národním prostředím `ar-SA` a `ar-SA-Naayf` hlasem.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Obrázek kontejneru s národním prostředím `bg-BG` a `bg-BG-Ivan` hlasem.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Obrázek kontejneru s národním prostředím `ca-ES` a `ca-ES-HerenaRUS` hlasem.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Obrázek kontejneru s národním prostředím `cs-CZ` a `cs-CZ-Jakub` hlasem.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Obrázek kontejneru s národním prostředím `da-DK` a `da-DK-HelleRUS` hlasem.        |
| `1.3.0-amd64-de-at-michael-preview`         | Obrázek kontejneru s národním prostředím `de-AT` a `de-AT-Michael` hlasem.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Obrázek kontejneru s národním prostředím `de-CH` a `de-CH-Karsten` hlasem.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Hedda` hlasem.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Hedda` hlasem.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-HeddaRUS` hlasem.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Stefan-Apollo` hlasem.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Obrázek kontejneru s národním prostředím `el-GR` a `el-GR-Stefanos` hlasem.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s národním prostředím `en-AU` a `en-AU-Catherine` hlasem.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s národním prostředím `en-AU` a `en-AU-HayleyRUS` hlasem.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Obrázek kontejneru s národním prostředím `en-CA` a `en-CA-HeatherRUS` hlasem.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Obrázek kontejneru s národním prostředím `en-CA` a `en-CA-Linda` hlasem.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-George-Apollo` hlasem.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-HazelRUS` hlasem.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-Susan-Apollo` hlasem.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Obrázek kontejneru s národním prostředím `en-IE` a `en-IE-Sean` hlasem.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-Heera-Apollo` hlasem.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-PriyaRUS` hlasem.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-Ravi-Apollo` hlasem.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-BenjaminRUS` hlasem.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Guy24kRUS` hlasem.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Jessa24kRUS` hlasem.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s národním prostředím `en-US` a `en-US-JessaRUS` hlasem.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s národním prostředím `en-US` a `en-US-ZiraRUS` hlasem.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-HelenaRUS` hlasem.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-Laura-Apollo` hlasem.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-Pablo-Apollo` hlasem.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s národním prostředím `es-MX` a `es-MX-HildaRUS` hlasem.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s národním prostředím `es-MX` a `es-MX-Raul-Apollo` hlasem.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Obrázek kontejneru s národním prostředím `fi-FI` a `fi-FI-HeidiRUS` hlasem.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s národním prostředím `fr-CA` a `fr-CA-Caroline` hlasem.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s národním prostředím `fr-CA` a `fr-CA-HarmonieRUS` hlasem.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Obrázek kontejneru s národním prostředím `fr-CH` a `fr-CH-Guillaume` hlasem.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-HortenseRUS` hlasem.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-Julie-Apollo` hlasem.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-Paul-Apollo` hlasem.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Obrázek kontejneru s národním prostředím `he-IL` a `he-IL-Asaf` hlasem.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Obrázek kontejneru s národním prostředím `hi-IN` a `hi-IN-Hemant` hlasem.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s národním prostředím `hi-IN` a `hi-IN-Kalpana-Apollo` hlasem.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Obrázek kontejneru s národním prostředím `hi-IN` a `hi-IN-Kalpana` hlasem.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Obrázek kontejneru s národním prostředím `hi-IN` a `hi-IN-Kalpana` hlasem.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Obrázek kontejneru s národním prostředím `hr-HR` a `hr-HR-Matej` hlasem.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Obrázek kontejneru s národním prostředím `hu-HU` a `hu-HU-Szabolcs` hlasem.        |
| `1.3.0-amd64-id-id-andika-preview`          | Obrázek kontejneru s národním prostředím `id-ID` a `id-ID-Andika` hlasem.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s národním prostředím `it-IT` a `it-IT-Cosimo-Apollo` hlasem.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s národním prostředím `it-IT` a `it-IT-LuciaRUS` hlasem.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-Ayumi-Apollo` hlasem.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-HarukaRUS` hlasem.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-Ichiro-Apollo` hlasem.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s národním prostředím `ko-KR` a `ko-KR-HeamiRUS` hlasem.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Obrázek kontejneru s národním prostředím `ms-MY` a `ms-MY-Rizwan` hlasem.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Obrázek kontejneru s národním prostředím `nb-NO` a `nb-NO-HuldaRUS` hlasem.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Obrázek kontejneru s národním prostředím `nl-NL` a `nl-NL-HannaRUS` hlasem.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Obrázek kontejneru s národním prostředím `pl-PL` a `pl-PL-PaulinaRUS` hlasem.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s národním prostředím `pt-BR` a `pt-BR-Daniel-Apollo` hlasem.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s národním prostředím `pt-BR` a `pt-BR-HeloisaRUS` hlasem.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Obrázek kontejneru s národním prostředím `pt-PT` a `pt-PT-HeliaRUS` hlasem.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Obrázek kontejneru s národním prostředím `ro-RO` a `ro-RO-Andrei` hlasem.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Obrázek kontejneru s národním prostředím `ru-RU` a `ru-RU-EkaterinaRUS` hlasem.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Obrázek kontejneru s národním prostředím `ru-RU` a `ru-RU-Irina-Apollo` hlasem.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Obrázek kontejneru s národním prostředím `ru-RU` a `ru-RU-Pavel-Apollo` hlasem.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Obrázek kontejneru s národním prostředím `sk-SK` a `sk-SK-Filip` hlasem.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Obrázek kontejneru s národním prostředím `sl-SI` a `sl-SI-Lado` hlasem.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Obrázek kontejneru s národním prostředím `sv-SE` a `sv-SE-HedvigRUS` hlasem.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Obrázek kontejneru s národním prostředím `ta-IN` a `ta-IN-Valluvar` hlasem.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Obrázek kontejneru s národním prostředím `te-IN` a `te-IN-Chitra` hlasem.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Obrázek kontejneru s národním prostředím `th-TH` a `th-TH-Pattara` hlasem.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Obrázek kontejneru s národním prostředím `tr-TR` a `tr-TR-SedaRUS` hlasem.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Obrázek kontejneru s národním prostředím `vi-VN` a `vi-VN-An` hlasem.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-HuihuiRUS` hlasem.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Kangkang-Apollo` hlasem. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Yaoyao-Apollo` hlasem.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Obrázek kontejneru s národním prostředím `zh-HK` a `zh-HK-Danny-Apollo` hlasem.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Obrázek kontejneru s národním prostředím `zh-HK` a `zh-HK-Tracy-Apollo` hlasem.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Obrázek kontejneru s národním prostředím `zh-HK` a `zh-HK-TracyRUS` hlasem.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Obrázek kontejneru s národním prostředím `zh-TW` a `zh-TW-HanHanRUS` hlasem.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Obrázek kontejneru s národním prostředím `zh-TW` a `zh-TW-Yating-Apollo` hlasem.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Obrázek kontejneru s národním prostředím `zh-TW` a `zh-TW-Zhiwei-Apollo` hlasem.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Hedda` hlasem.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-HeddaRUS` hlasem.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Stefan-Apollo` hlasem.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s národním prostředím `en-AU` a `en-AU-Catherine` hlasem.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s národním prostředím `en-AU` a `en-AU-HayleyRUS` hlasem.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-George-Apollo` hlasem.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-HazelRUS` hlasem.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-Susan-Apollo` hlasem.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-Heera-Apollo` hlasem.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-PriyaRUS` hlasem.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-Ravi-Apollo` hlasem.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-BenjaminRUS` hlasem.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Guy24kRUS` hlasem.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Jessa24kRUS` hlasem.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s národním prostředím `en-US` a `en-US-JessaRUS` hlasem.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s národním prostředím `en-US` a `en-US-ZiraRUS` hlasem.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-HelenaRUS` hlasem.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-Laura-Apollo` hlasem.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-Pablo-Apollo` hlasem.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s národním prostředím `es-MX` a `es-MX-HildaRUS` hlasem.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s národním prostředím `es-MX` a `es-MX-Raul-Apollo` hlasem.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s národním prostředím `fr-CA` a `fr-CA-Caroline` hlasem.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s národním prostředím `fr-CA` a `fr-CA-HarmonieRUS` hlasem.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-HortenseRUS` hlasem.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-Julie-Apollo` hlasem.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-Paul-Apollo` hlasem.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s národním prostředím `it-IT` a `it-IT-Cosimo-Apollo` hlasem.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s národním prostředím `it-IT` a `it-IT-LuciaRUS` hlasem.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-Ayumi-Apollo` hlasem.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-HarukaRUS` hlasem.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-Ichiro-Apollo` hlasem.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s národním prostředím `ko-KR` a `ko-KR-HeamiRUS` hlasem.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s národním prostředím `pt-BR` a `pt-BR-Daniel-Apollo` hlasem.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s národním prostředím `pt-BR` a `pt-BR-HeloisaRUS` hlasem.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-HuihuiRUS` hlasem.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Kangkang-Apollo` hlasem. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Yaoyao-Apollo` hlasem.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Hedda` hlasem.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Hedda` hlasem.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-HeddaRUS` hlasem.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Obrázek kontejneru s národním prostředím `de-DE` a `de-DE-Stefan-Apollo` hlasem.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Obrázek kontejneru s národním prostředím `en-AU` a `en-AU-Catherine` hlasem.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Obrázek kontejneru s národním prostředím `en-AU` a `en-AU-HayleyRUS` hlasem.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-George-Apollo` hlasem.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-HazelRUS` hlasem.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Obrázek kontejneru s národním prostředím `en-GB` a `en-GB-Susan-Apollo` hlasem.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-Heera-Apollo` hlasem.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-PriyaRUS` hlasem.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Obrázek kontejneru s národním prostředím `en-IN` a `en-IN-Ravi-Apollo` hlasem.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-BenjaminRUS` hlasem.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Guy24kRUS` hlasem.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Jessa24kRUS` hlasem.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s národním prostředím `en-US` a `en-US-JessaRUS` hlasem.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s národním prostředím `en-US` a `en-US-ZiraRUS` hlasem.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-HelenaRUS` hlasem.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-Laura-Apollo` hlasem.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Obrázek kontejneru s národním prostředím `es-ES` a `es-ES-Pablo-Apollo` hlasem.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Obrázek kontejneru s národním prostředím `es-MX` a `es-MX-HildaRUS` hlasem.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Obrázek kontejneru s národním prostředím `es-MX` a `es-MX-Raul-Apollo` hlasem.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Obrázek kontejneru s národním prostředím `fr-CA` a `fr-CA-Caroline` hlasem.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Obrázek kontejneru s národním prostředím `fr-CA` a `fr-CA-HarmonieRUS` hlasem.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-HortenseRUS` hlasem.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-Julie-Apollo` hlasem.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Obrázek kontejneru s národním prostředím `fr-FR` a `fr-FR-Paul-Apollo` hlasem.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Obrázek kontejneru s národním prostředím `it-IT` a `it-IT-Cosimo-Apollo` hlasem.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Obrázek kontejneru s národním prostředím `it-IT` a `it-IT-LuciaRUS` hlasem.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-Ayumi-Apollo` hlasem.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-HarukaRUS` hlasem.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Obrázek kontejneru s národním prostředím `ja-JP` a `ja-JP-Ichiro-Apollo` hlasem.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Obrázek kontejneru s národním prostředím `ko-KR` a `ko-KR-HeamiRUS` hlasem.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Obrázek kontejneru s národním prostředím `pt-BR` a `pt-BR-Daniel-Apollo` hlasem.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Obrázek kontejneru s národním prostředím `pt-BR` a `pt-BR-HeloisaRUS` hlasem.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-HuihuiRUS` hlasem.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Kangkang-Apollo` hlasem. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Yaoyao-Apollo` hlasem.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-BenjaminRUS` hlasem.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Guy24kRUS` hlasem.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Obrázek kontejneru s národním prostředím `en-US` a `en-US-Jessa24kRUS` hlasem.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Obrázek kontejneru s národním prostředím `en-US` a `en-US-JessaRUS` hlasem.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Obrázek kontejneru s národním prostředím `en-US` a `en-US-ZiraRUS` hlasem.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-HuihuiRUS` hlasem.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Kangkang-Apollo` hlasem. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Obrázek kontejneru s národním prostředím `zh-CN` a `zh-CN-Yaoyao-Apollo` hlasem.   |

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Image [kontejneru extrakce klíčových frází][ta-kp] lze nalézt v syndikátu registru kontejnerů. `mcr.microsoft.com` Je umístěn v `azure-cognitive-services` úložišti a `keyphrase`je pojmenován . Plně kvalifikovaný název bitové `mcr.microsoft.com/azure-cognitive-services/keyphrase`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Rozpoznávání jazyka

Image kontejneru [detekce jazyka][ta-la] `mcr.microsoft.com` lze nalézt v syndikátu registru kontejnerů. Je umístěn v `azure-cognitive-services` úložišti a `language`je pojmenován . Plně kvalifikovaný název bitové `mcr.microsoft.com/azure-cognitive-services/language`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Analýza mínění

Image [kontejneru analýzy mínění][ta-se] `mcr.microsoft.com` lze nalézt v syndikátu registru kontejnerů. Je umístěn v `azure-cognitive-services` úložišti a `sentiment`je pojmenován . Plně kvalifikovaný název bitové `mcr.microsoft.com/azure-cognitive-services/sentiment`kopie kontejneru je . .

Tato image kontejneru má k dispozici následující značky:

| Značky obrázků                    | Poznámky |
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
