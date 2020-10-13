---
title: Testování aplikace vlastních příkazů
titleSuffix: Azure Cognitive Services
description: V tomto článku se seznámíte s různými přístupy k testování aplikace s vlastními příkazy.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: ec8e40c0908855cd06d647bdd9121106e3553c11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918906"
---
# <a name="test-your-custom-commands-application"></a>Testování aplikace Custom Commands

V tomto článku se seznámíte s různými přístupy k testování aplikace s vlastními příkazy.

## <a name="test-in-the-portal"></a>Test na portálu

Test na portálu je nejjednodušší a nejrychlejší způsob, jak zkontrolovat, jestli vaše aplikace vlastního příkazu pracuje podle očekávání. Po úspěšném školení aplikace klikněte na `Test` tlačítko pro spuštění testování.

> [!div class="mx-imgBorder"]
> ![Test na portálu](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Test pomocí klienta Windows Voice Assistant

Klient hlasového asistenta Windows je aplikace Windows Presentation Foundation (WPF) v jazyce C#, která usnadňuje testování interakcí s robotem před vytvořením vlastní klientské aplikace.

Nástroj může přijmout vlastní ID aplikace příkazu. Umožňuje testovat dokončení úkolu nebo scénář příkazů a řízení hostovaných ve službě Custom Commands.

Chcete-li nastavit klienta, Zarezervujte [klienta programu Windows Voice Assistant](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![WVAC vytvořit profil](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Testování pomocí klientských aplikací s povoleným sadou Speech SDK 
Sada Speech Software Development Kit (SDK) zveřejňuje mnoho funkcí služby pro rozpoznávání řeči, které vám umožní vyvíjet aplikace s podporou řeči. Je také k dispozici v řadě programovacích jazyků a napříč všemi platformami.

Nastavení klientské aplikace Univerzální platforma Windows (UWP) pro sadu Speech SDK a jejich integraci do vlastní aplikace příkazu:  
- [Postupy: integrace s klientskou aplikací pomocí sady Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)
- [Postupy: odeslání aktivity do klientské aplikace](./how-to-custom-commands-send-activity-to-client.md)
- [Postupy: nastavení koncových bodů webu](./how-to-custom-commands-setup-web-endpoints.md)

Pro jiné programovací jazyky a platformy:
- [Programovací jazyky, platformy, kapacity scénářů a sady Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)
- [Ukázkové kódy hlasového asistenta](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit ukázky na GitHubu](https://aka.ms/speech/cc-samples)

