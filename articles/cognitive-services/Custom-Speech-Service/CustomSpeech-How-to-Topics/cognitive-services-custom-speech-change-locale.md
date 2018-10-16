---
title: Podporované jazyky a národní prostředí ve službě Custom Speech Service v Azure | Dokumentace Microsoftu
description: Přehled podporovaných jazyků Custom Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 8493aeb3c1c2436900ae626ad0f34cbe8b060163
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342164"
---
# <a name="supported-locales-in-custom-speech-service"></a>Podporovaná národní prostředí ve službě Custom Speech Service

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Custom Speech Service aktuálně podporuje úpravy modelů v následujících národních prostředí:

| Typ modelu | Podpora jazyků |
|----|-----|
| Akustických modelů | Jazykovou verzi US English (en US) |
| Jazykových modelů | Jazykovou verzi US English (en US), čínština (zh-CN) |

I když přizpůsobení akustického modelu je podporovaná jenom v Americkou angličtinu, import čínský akustických dat je podporován pro účely testování offline čínský vlastní jazykové modely.

Před provedením jakékoli akce je nutné vybrat odpovídající národní prostředí. Aktuální národní prostředí je uvedené v názvu tabulky na všech stránkách dat, modelu a nasazení. Chcete-li změnit národní prostředí, klikněte na tlačítko "Změnit národní prostředí" umístěné v části název tabulky. Tím se dostanete na stránku potvrzení národní prostředí. Kliknutím na OK se vraťte do tabulky.

By měli provést s dalšími kroky.
* Přečtěte si [vytvoření vlastního akustického modelu](cognitive-services-custom-speech-create-acoustic-model.md) zlepšit přesnost rozpoznávání
* Přečtěte si [vytvoření vlastního jazykového modelu](cognitive-services-custom-speech-create-language-model.md) zlepšit rychlost vašeho rozpoznávání
* Postupujte podle [pokyny pro přepis](cognitive-services-custom-speech-transcription-guidelines.md) pro přípravu dat
