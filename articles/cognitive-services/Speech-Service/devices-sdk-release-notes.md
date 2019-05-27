---
title: Dokumentace ke zpracování řeči Devices SDK
titleSuffix: Azure Cognitive Services
description: Zpráva k vydání verze – co se změnilo v nejnovější vydané verze
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: fab23c86ee2e8f654ac4a9619c39d05ee4e9e4c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237941"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Poznámky k verzi sady Cognitive Services zařízení sadou SDK pro řeč
Následující změny části seznamu v nejnovější vydané verze.

## <a name="speech-devices-sdk-151"></a>Rozpoznávání řeči zařízení SDK 1.5.1:

*   Zahrnout [konverzace určené k transkripci](conversation-transcription-service.md) v ukázkové aplikaci.
*   Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) součásti na verzi 1.5.1. Další informace najdete v tématu jeho [poznámky k verzi](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Zařízení řeči služeb cognitive Services SDK 1.5.0: Května 2019 vydání

*   Sadou SDK pro řeč zařízení je teď GA a už verzi chráněného preview.
*   Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) součásti na verzi 1.5.0. Další informace najdete v tématu jeho [poznámky k verzi](https://aka.ms/csspeech/whatsnew).
*   Novou technologii wake slovo přináší značné kvality vylepšení naleznete v tématu Rozbíjející změny.
*   Nový kanál zpracování zvuku pro zlepšení rozpoznávání úplně pole.

**Rozbíjející změny v**

*   Z důvodu novou technologii wake word je potřeba znovu vytvořit na náš portál slovo vylepšené probuzení všechna slova probuzení. Úplně odebrat starý klíčová slova ze zařízení odinstalujte staré aplikace.
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Zařízení řeči služeb cognitive Services SDK 1.4.0: 2019 dubna vydání 

* Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponentu verze 1.4.0. Další informace najdete v tématu jeho [poznámky k verzi](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Zařízení řeči služeb cognitive Services SDK 1.3.1: 2019 března vydání 

* Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) součásti na verzi 1.3.1. Další informace najdete v tématu jeho [poznámky k verzi](https://aka.ms/csspeech/whatsnew). 
*   Aktualizované probuzení slovo zpracování, naleznete v tématu Rozbíjející změny.
*   Ukázková aplikace přidá výběru jazyka pro rozpoznávání řeči a překladu.

**Rozbíjející změny v** 

*   [Instalace aplikace word probuzení](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) bylo zjednodušené, je teď součástí aplikace a nepotřebuje samostatnou instalaci na zařízení.
*   Došlo ke změně rozpoznávání probuzení aplikace word a jsou podporovány dvě události.
    - RecognizingKeyword, označuje, že výsledek řeči obsahuje text (neověřené) – klíčové slovo.
    - RecognizedKeyword, označuje, že – klíčové slovo rozpoznávání dokončit uznání daným klíčovým slovem.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Zařízení řeči služeb cognitive Services SDK 1.1.0: Verze v listopadu 2018 

* Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) součásti na verzi 1.1.0. Další informace najdete v tématu jeho [poznámky k verzi](https://aka.ms/csspeech/whatsnew). 
* Vylepšili jsme úplně přesnost rozpoznávání řeči pole s algoritmem naše rozšířené zpracování zvuku.
* Ukázková aplikace přidat podporou čínský řeči.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Zařízení řeči služeb cognitive Services SDK 1.0.1: Verze října 2018 

* Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) součásti na verzi 1.0.1. Další informace najdete v tématu jeho [poznámky k verzi](https://aka.ms/csspeech/whatsnew). 
* Pomocí našeho algoritmu vylepšené zpracování zvuku se vylepší přesnost rozpoznávání řeči  
* Jeden průběžné rozpoznávání zvuku relace opravy chyby.

**Rozbíjející změny v** 

* V této verzi představeny počet nejnovější změny. Zkontrolujte prosím [na této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) podrobnosti týkající se rozhraní API. 
* Soubory modelu KWS nejsou kompatibilní se sadou SDK pro řeč zařízení 1.0.1. Až nové probuzení slovo soubory jsou zapsány do zařízení se odstraní existující soubory probuzení aplikace Word. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Zařízení řeči služeb cognitive Services SDK si verzi 0.5.0: Verze srpna 2018

* Zlepšení přesnosti rozpoznávání řeči opravou chyby v kódu zpracování zvuku.
* Aktualizuje [sadou SDK pro řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) součásti na verzi si verzi 0.5.0. Další informace najdete v tématu jeho [poznámky k verzi](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Zařízení řeči služeb cognitive Services SDK 0.2.12733: Verze v květnu 2018

První verze public preview služby Cognitive Services SDK zařízení řeči.
