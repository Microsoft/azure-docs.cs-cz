---
title: Podporované jazyky a národní prostředí ve službě vlastní řeči na Azure | Microsoft Docs
description: Přehled podporovaných jazyků vlastní řeči služby v kognitivní služby.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: c378fd951f9cd04884f44fbec5accb5d9a886bfe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342372"
---
# <a name="supported-locales-in-custom-speech-service"></a>Podporovaných národních prostředí na vlastní řeči služby
Vlastní rozpoznávání řeči služba aktuálně podporuje přizpůsobení modelů v následujících národních prostředí:

| Typ modelu | Jazyková podpora |
|----|-----|
| Akustickými modely | Čeština (cs cz) |
| Jazyk modely | Čeština (cs cz), čínština (zh-CN) |

I když přizpůsobení akustickými modelu je podporována pouze v angličtinu, import čínština akustickými dat je podporována pro účely testování offline přizpůsobené čínština jazyk modelů.

Před provedením jakékoli akce je nutné vybrat odpovídající národní prostředí. Aktuální národní prostředí je uvedené v názvu tabulky na všechna data, model a nasazení stránky. Chcete-li změnit národní prostředí, klikněte na tlačítko "Změnit národního prostředí" přidávaném název tabulky. Tím přejdete na potvrzovací stránku národního prostředí. Klikněte na tlačítko "OK" se vrátíte do tabulky.

Měli následnou akci s další kroky
* Další informace [jak vytvořit vlastní akustickými modelu](cognitive-services-custom-speech-create-acoustic-model.md) a zvyšte tak přesnost rozpoznávání
* Další informace [jak vytvořit vlastní jazyk modelu](cognitive-services-custom-speech-create-language-model.md) ke zlepšení vaší rychlost rozpoznávání
* Postupujte podle [přepis pokyny](cognitive-services-custom-speech-transcription-guidelines.md) pro přípravu dat
