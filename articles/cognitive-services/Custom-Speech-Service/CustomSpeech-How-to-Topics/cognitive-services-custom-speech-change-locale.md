---
title: Podporovaná národní prostředí a jazyků – Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Přehled podporovaných jazyků Custom Speech Service ve službě Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223951"
---
# <a name="supported-locales-in-custom-speech-service"></a>Podporovaná národní prostředí ve službě Custom Speech Service
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
