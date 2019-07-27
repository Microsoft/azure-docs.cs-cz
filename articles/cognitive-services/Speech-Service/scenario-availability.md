---
title: Dostupnost scénáře – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Referenční informace pro oblasti Speech Service.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 94fd415909e86a43916ee2f510732a6a6d9c5ed3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552958"
---
# <a name="scenario-availability"></a>Dostupnost scénáře

Sada SDK služby pro rozpoznávání řeči nabízí mnoho scénářů napříč širokou škálou programovacích jazyků a prostředí.  Ne všechny scénáře jsou aktuálně k dispozici ve všech programovacích jazycích nebo ve všech prostředích.  Níže je uveden seznam dostupnosti jednotlivých scénářů.

- **Rozpoznávání řeči (SR), seznam frází, záměr, překlad a místní kontejnery**
  - Všechny programovací jazyky/prostředí, ve kterých se nachází odkaz na šipku <img src="media/index/link.jpg" height="15" width="15"></img> v tabulce pro rychlé zprovoznění [tady](https://aka.ms/csspeech).
- **PŘEVOD textu na řeč**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - REST API TTS lze použít v každé jiné situaci.
- **Wake Word (klíčové slovo spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - Funkce Wake Word (klíčové slovo spotter/KWS) můžou fungovat s jakýmkoli typem mikrofonu. oficiální podpora KWS se ale v tuto chvíli omezila na pole mikrofonu, která najdete v hardwaru Azure Kinect DK nebo v sadě Speech SDK pro zařízení.
- **Hlas – první virtuální asistent**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
- **Přepis konverzace**
  - C++/Windows & Linux
  - C#(Rozhraní Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
- **Přepis centra volání**
  - REST API a lze je použít v jakékoli situaci
- **Komprimovaný zvukový vstup kodeku**
  - C++/Linux
  - C#/Linux
  - Java/Linux & Android
