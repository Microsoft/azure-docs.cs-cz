---
title: Cognitive Services značky imagí kontejneru
titleSuffix: Azure Cognitive Services
description: Úplný seznam všech značek imagí kontejneru služby vnímání.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 8a8c49089d5c0554c4636e98b5820ef206010207
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134175"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Značky image kontejneru Azure Cognitive Services

Azure Cognitive Services nabízí mnoho imagí kontejneru. Registry kontejnerů a odpovídající úložiště se v různých imagích kontejnerů liší. Každý název Image kontejneru nabízí více značek. Značka image kontejneru je mechanismus správy verzí image kontejneru. Tento článek je určený k použití jako ucelený odkaz pro výpis všech Cognitive Servicesch imagí kontejnerů a jejich dostupných značek.

> [!TIP]
> Při použití [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)věnujte velkou pozornost pro velká a malá písmena registru kontejnerů, úložiště, názvu image kontejneru a odpovídajících značek – protože rozlišují **velká a malá písmena**.

## <a name="anomaly-detector"></a>Detektor anomálií

Image kontejneru [detektoru anomálií][ad-containers] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-anomaly-detector`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>Počítačové zpracování obrazu

Image kontejneru [počítačové zpracování obrazu][cv-containers] můžete najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-read`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>Tvář

Image kontejneru [Face][fa-containers] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-face`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>Rozpoznávání formulářů

Image kontejneru [pro rozpoznávání formulářů][fr-containers] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-form-recognizer`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>Rozpoznávání jazyka (LUIS)

Image kontejneru [Luis][lu-containers] se dá najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `luis`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/luis`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>Custom Speech na text

Obrázek kontejneru [Custom Speech do textu][sp-cstt] lze nalézt v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-custom-speech-to-text`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní image kontejneru [textu na řeč][sp-ctts] se dá najít v registru kontejneru `containerpreview.azurecr.io`. Je umístěn v úložišti `microsoft` a má název `cognitive-services-custom-text-to-speech`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>Převod řeči na text

Obrázek s obrázkem [mluveného textu na text][sp-stt] najdete na `containerpreview.azurecr.io` registru kontejnerů. Je umístěn v úložišti `microsoft` a má název `cognitive-services-speech-to-text`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | Obrázek kontejneru s národním prostředím `en-US` |
| `2.0.0-amd64-zh-tw-preview` | |
| `2.0.0-amd64-zh-hk-preview` | |
| `2.0.0-amd64-zh-cn-preview` | |
| `2.0.0-amd64-tr-tr-preview` | |
| `2.0.0-amd64-th-th-preview` | |
| `2.0.0-amd64-sv-se-preview` | |
| `2.0.0-amd64-ru-ru-preview` | |
| `2.0.0-amd64-pt-pt-preview` | |
| `2.0.0-amd64-pt-br-preview` | |
| `2.0.0-amd64-pl-pl-preview` | |
| `2.0.0-amd64-nl-nl-preview` | |
| `2.0.0-amd64-nb-no-preview` | |
| `2.0.0-amd64-ko-kr-preview` | |
| `2.0.0-amd64-ja-jp-preview` | |
| `2.0.0-amd64-it-it-preview` | |
| `2.0.0-amd64-hi-in-preview` | |
| `2.0.0-amd64-fr-fr-preview` | |
| `2.0.0-amd64-fr-ca-preview` | |
| `2.0.0-amd64-fi-fi-preview` | |
| `2.0.0-amd64-es-mx-preview` | |
| `2.0.0-amd64-es-es-preview` | |
| `2.0.0-amd64-en-us-preview` | |
| `2.0.0-amd64-en-nz-preview` | |
| `2.0.0-amd64-en-in-preview` | |
| `2.0.0-amd64-en-gb-preview` | |
| `2.0.0-amd64-en-ca-preview` | |
| `2.0.0-amd64-en-au-preview` | |
| `2.0.0-amd64-de-de-preview` | |
| `2.0.0-amd64-da-dk-preview` | |
| `2.0.0-amd64-ca-es-preview` | |
| `2.0.0-amd64-ar-eg-preview` | |
| `1.2.0-amd64-zh-cn-preview` | |
| `1.2.0-amd64-pt-br-preview` | |
| `1.2.0-amd64-preview` | |
| `1.2.0-amd64-ja-jp-preview` | |
| `1.2.0-amd64-it-it-preview` | |
| `1.2.0-amd64-fr-fr-preview` | |
| `1.2.0-amd64-fr-ca-preview` | |
| `1.2.0-amd64-es-mx-preview` | |
| `1.2.0-amd64-es-es-preview` | |
| `1.2.0-amd64-en-us-preview` | |
| `1.2.0-amd64-en-in-preview` | |
| `1.2.0-amd64-en-gb-preview` | |
| `1.2.0-amd64-en-ca-preview` | |
| `1.2.0-amd64-en-au-preview` | |
| `1.2.0-amd64-de-de-preview` | |
| `1.1.3-amd64-zh-cn-preview` | |
| `1.1.3-amd64-pt-br-preview` | |
| `1.1.3-amd64-preview` | |
| `1.1.3-amd64-ja-jp-preview` | |
| `1.1.3-amd64-it-it-preview` | |
| `1.1.3-amd64-fr-fr-preview` | |
| `1.1.3-amd64-fr-ca-preview` | |
| `1.1.3-amd64-es-mx-preview` | |
| `1.1.3-amd64-es-es-preview` | |
| `1.1.3-amd64-en-us-preview` | |
| `1.1.3-amd64-en-in-preview` | |
| `1.1.3-amd64-en-gb-preview` | |
| `1.1.3-amd64-en-ca-preview` | |
| `1.1.3-amd64-en-au-preview` | |
| `1.1.3-amd64-de-de-preview` | |
| `1.1.2-amd64-zh-cn-preview` | |
| `1.1.2-amd64-pt-br-preview` | |
| `1.1.2-amd64-preview` | |
| `1.1.2-amd64-ja-jp-preview` | |
| `1.1.2-amd64-it-it-preview` | |
| `1.1.2-amd64-fr-fr-preview` | |
| `1.1.2-amd64-fr-ca-preview` | |
| `1.1.2-amd64-es-mx-preview` | |
| `1.1.2-amd64-es-es-preview` | |
| `1.1.2-amd64-en-us-preview` | |
| `1.1.2-amd64-en-in-preview` | |
| `1.1.2-amd64-en-gb-preview` | |
| `1.1.2-amd64-en-ca-preview` | |
| `1.1.2-amd64-en-au-preview` | |
| `1.1.2-amd64-de-de-preview` | |
| `1.1.1-amd64-zh-cn-preview` | |
| `1.1.1-amd64-pt-br-preview` | |
| `1.1.1-amd64-ja-jp-preview` | |
| `1.1.1-amd64-it-it-preview` | |
| `1.1.1-amd64-fr-fr-preview` | |
| `1.1.1-amd64-fr-ca-preview` | |
| `1.1.1-amd64-es-mx-preview` | |
| `1.1.1-amd64-es-es-preview` | |
| `1.1.1-amd64-en-us-preview` | |
| `1.1.1-amd64-en-in-preview` | |
| `1.1.1-amd64-en-gb-preview` | |
| `1.1.1-amd64-en-ca-preview` | |
| `1.1.1-amd64-en-au-preview` | |
| `1.1.1-amd64-de-de-preview` | |
| `1.1.0-amd64-zh-cn-preview` | |
| `1.1.0-amd64-pt-br-preview` | |
| `1.1.0-amd64-ja-jp-preview` | |
| `1.1.0-amd64-it-it-preview` | |
| `1.1.0-amd64-fr-fr-preview` | |
| `1.1.0-amd64-fr-ca-preview` | |
| `1.1.0-amd64-es-mx-preview` | |
| `1.1.0-amd64-es-es-preview` | |
| `1.1.0-amd64-en-us-preview` | |
| `1.1.0-amd64-en-in-preview` | |
| `1.1.0-amd64-en-gb-preview` | |
| `1.1.0-amd64-en-ca-preview` | |
| `1.1.0-amd64-en-au-preview` | |
| `1.1.0-amd64-de-de-preview` | |
| `1.0.0-amd64-zh-cn-preview` | |
| `1.0.0-amd64-pt-br-preview` | |
| `1.0.0-amd64-ja-jp-preview` | |
| `1.0.0-amd64-it-it-preview` | |
| `1.0.0-amd64-fr-fr-preview` | |
| `1.0.0-amd64-fr-ca-preview` | |
| `1.0.0-amd64-es-mx-preview` | |
| `1.0.0-amd64-es-es-preview` | |
| `1.0.0-amd64-en-us-preview` | |
| `1.0.0-amd64-en-in-preview` | |
| `1.0.0-amd64-en-gb-preview` | |
| `1.0.0-amd64-en-ca-preview` | |
| `1.0.0-amd64-en-au-preview` | |
| `1.0.0-amd64-de-de-preview` | |

## <a name="text-to-speech"></a>Převod textu na řeč

Obrázek kontejneru [Převod textu na řeč][sp-tts] najdete na `containerpreview.azurecr.io` registru kontejnerů. Je umístěn v úložišti `microsoft` a má název `cognitive-services-text-to-speech`. Plně kvalifikovaný název Image kontejneru je `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | Obrázek kontejneru s `en-US` národním prostředím a `JessaRUS`m hlasem |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | |
| `1.3.0-amd64-vi-vn-an-preview` | |
| `1.3.0-amd64-tr-tr-sedarus-preview` | |
| `1.3.0-amd64-th-th-pattara-preview` | |
| `1.3.0-amd64-te-in-chitra-preview` | |
| `1.3.0-amd64-ta-in-valluvar-preview` | |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | |
| `1.3.0-amd64-sl-si-lado-preview` | |
| `1.3.0-amd64-sk-sk-filip-preview` | |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | |
| `1.3.0-amd64-ro-ro-andrei-preview` | |
| `1.3.0-amd64-pt-pt-heliarus-preview` | |
| `1.3.0-amd64-pt-br-heloisarus-preview` | |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | |
| `1.3.0-amd64-nl-nl-hannarus-preview` | |
| `1.3.0-amd64-nb-no-huldarus-preview` | |
| `1.3.0-amd64-ms-my-rizwan-preview` | |
| `1.3.0-amd64-ko-kr-heamirus-preview` | |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.3.0-amd64-ja-jp-harukarus-preview` | |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.3.0-amd64-it-it-luciarus-preview` | |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.3.0-amd64-id-id-andika-preview` | |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | |
| `1.3.0-amd64-hr-hr-matej-preview` | |
| `1.3.0-amd64-hi-in-kalpana-preview` | |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | |
| `1.3.0-amd64-hi-in-hemant-preview` | |
| `1.3.0-amd64-he-il-asaf-preview` | |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | |
| `1.3.0-amd64-fr-ch-guillaume-preview` | |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | |
| `1.3.0-amd64-fr-ca-caroline-preview` | |
| `1.3.0-amd64-fi-fi-heidirus-preview` | |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | |
| `1.3.0-amd64-es-mx-hildarus-preview` | |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | |
| `1.3.0-amd64-es-es-laura-apollo-preview` | |
| `1.3.0-amd64-es-es-helenarus-preview` | |
| `1.3.0-amd64-en-us-zirarus-preview` | |
| `1.3.0-amd64-en-us-jessarus-preview` | |
| `1.3.0-amd64-en-us-jessa24krus-preview` | |
| `1.3.0-amd64-en-us-guy24krus-preview` | |
| `1.3.0-amd64-en-us-benjaminrus-preview` | |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | |
| `1.3.0-amd64-en-in-priyarus-preview` | |
| `1.3.0-amd64-en-in-heera-apollo-preview` | |
| `1.3.0-amd64-en-ie-sean-preview` | |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | |
| `1.3.0-amd64-en-gb-hazelrus-preview` | |
| `1.3.0-amd64-en-gb-george-apollo-preview` | |
| `1.3.0-amd64-en-ca-linda-preview` | |
| `1.3.0-amd64-en-ca-heatherrus-preview` | |
| `1.3.0-amd64-en-au-hayleyrus-preview` | |
| `1.3.0-amd64-en-au-catherine-preview` | |
| `1.3.0-amd64-el-gr-stefanos-preview` | |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | |
| `1.3.0-amd64-de-de-heddarus-preview` | |
| `1.3.0-amd64-de-de-hedda-preview` | |
| `1.3.0-amd64-de-ch-karsten-preview` | |
| `1.3.0-amd64-de-at-michael-preview` | |
| `1.3.0-amd64-da-dk-hellerus-preview` | |
| `1.3.0-amd64-cs-cz-jakub-preview` | |
| `1.3.0-amd64-ca-es-herenarus-preview` | |
| `1.3.0-amd64-bg-bg-ivan-preview` | |
| `1.3.0-amd64-ar-sa-naayf-preview` | |
| `1.3.0-amd64-ar-eg-hoda-preview` | |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | |
| `1.2.0-amd64-pt-br-heloisarus-preview` | |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.2.0-amd64-ko-kr-heamirus-preview` | |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.2.0-amd64-ja-jp-harukarus-preview` | |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.2.0-amd64-it-it-luciarus-preview` | |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | |
| `1.2.0-amd64-fr-ca-caroline-preview` | |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | |
| `1.2.0-amd64-es-mx-hildarus-preview` | |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | |
| `1.2.0-amd64-es-es-laura-apollo-preview` | |
| `1.2.0-amd64-es-es-helenarus-preview` | |
| `1.2.0-amd64-en-us-zirarus-preview` | |
| `1.2.0-amd64-en-us-jessarus-preview` | |
| `1.2.0-amd64-en-us-jessa24krus-preview` | |
| `1.2.0-amd64-en-us-guy24krus-preview` | |
| `1.2.0-amd64-en-us-benjaminrus-preview` | |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | |
| `1.2.0-amd64-en-in-priyarus-preview` | |
| `1.2.0-amd64-en-in-heera-apollo-preview` | |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | |
| `1.2.0-amd64-en-gb-hazelrus-preview` | |
| `1.2.0-amd64-en-gb-george-apollo-preview` | |
| `1.2.0-amd64-en-au-hayleyrus-preview` | |
| `1.2.0-amd64-en-au-catherine-preview` | |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | |
| `1.2.0-amd64-de-de-heddarus-preview` | |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | |
| `1.1.0-amd64-pt-br-heloisarus-preview` | |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.1.0-amd64-ko-kr-heamirus-preview` | |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.1.0-amd64-ja-jp-harukarus-preview` | |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.1.0-amd64-it-it-luciarus-preview` | |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | |
| `1.1.0-amd64-fr-ca-caroline-preview` | |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | |
| `1.1.0-amd64-es-mx-hildarus-preview` | |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | |
| `1.1.0-amd64-es-es-laura-apollo-preview` | |
| `1.1.0-amd64-es-es-helenarus-preview` | |
| `1.1.0-amd64-en-us-zirarus-preview` | |
| `1.1.0-amd64-en-us-jessarus-preview` | |
| `1.1.0-amd64-en-us-jessa24krus-preview` | |
| `1.1.0-amd64-en-us-guy24krus-preview` | |
| `1.1.0-amd64-en-us-benjaminrus-preview` | |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | |
| `1.1.0-amd64-en-in-priyarus-preview` | |
| `1.1.0-amd64-en-in-heera-apollo-preview` | |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | |
| `1.1.0-amd64-en-gb-hazelrus-preview` | |
| `1.1.0-amd64-en-gb-george-apollo-preview` | |
| `1.1.0-amd64-en-au-hayleyrus-preview` | |
| `1.1.0-amd64-en-au-catherine-preview` | |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | |
| `1.1.0-amd64-de-de-heddarus-preview` | |
| `1.1.0-amd64-de-de-hedda-preview` | |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | |
| `1.0.0-amd64-en-us-zirarus-preview` | |
| `1.0.0-amd64-en-us-jessarus-preview` | |
| `1.0.0-amd64-en-us-jessa24krus-preview` | |
| `1.0.0-amd64-en-us-guy24krus-preview` | |
| `1.0.0-amd64-en-us-benjaminrus-preview` | |

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Image kontejneru [extrakce klíčových frází][ta-kp] můžete najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `keyphrase`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>Rozpoznávání jazyka

Image kontejneru [rozpoznávání jazyka][ta-la] můžete najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `language`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/language`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>Analýza mínění

Image kontejneru [Analýza mínění][ta-se] můžete najít v části `mcr.microsoft.com` registru kontejnerů. Je umístěn v úložišti `azure-cognitive-services` a má název `sentiment`. Plně kvalifikovaný název Image kontejneru je `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Tato image kontejneru má k dispozici následující značky:

| Značky obrázku | Poznámky: |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

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
