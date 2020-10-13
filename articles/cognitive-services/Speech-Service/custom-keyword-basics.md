---
title: Vytvoření klíčového slova pro rychlý Start – služba Speech
titleSuffix: Azure Cognitive Services
description: Zařízení vždycky naslouchá klíčovému slovu (nebo frázi). Když uživatel uvede klíčové slovo, zařízení pošle veškeré následné zvukové nahrávky do cloudu, dokud uživatel nepřestane mluvit. Přizpůsobení klíčového slova je efektivní způsob, jak odlišit vaše zařízení a posílit vaše branding.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: f2f333cd01057ff5f6f904924880f88b0685c72f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667689"
---
# <a name="get-started-with-custom-keyword"></a>Začínáme se službou Vlastní klíčové slovo

V tomto rychlém startu se naučíte základy práce s vlastními klíčovými slovy pomocí rozpoznávání řeči Studio a sady Speech SDK. Klíčové slovo je slovo nebo krátká fráze, která umožňuje, aby byl váš produkt hlasově aktivovaný. Modely klíčových slov vytvoříte v nástroji Speech Studio a potom exportujete soubor modelu, který používáte se sadou Speech SDK ve vašich aplikacích.

## <a name="prerequisites"></a>Požadavky

Postup v tomto článku vyžaduje předplatné řeči a sadu Speech SDK. Pokud ještě nemáte předplatné, [Vyzkoušejte službu Speech Service zdarma](overview.md#try-the-speech-service-for-free). Chcete-li získat sadu SDK, přečtěte si [příručku Instalace](quickstarts/setup-platform.md) pro vaši platformu.

## <a name="create-a-keyword-in-speech-studio"></a>Vytvoření klíčového slova v studiu řeči

Než budete moct použít vlastní klíčové slovo, musíte vytvořit klíčové slovo pomocí stránky [vlastní klíčová slova](https://aka.ms/sdsdk-wakewordportal) v [studiu řeči](https://aka.ms/sdsdk-speechportal). Po zadání klíčového slova se vytvoří `.table` soubor, který můžete použít se sadou Speech SDK.

> [!IMPORTANT]
> Vlastní modely klíčových slov a výsledné `.table` soubory lze vytvořit **pouze** v studiu řeči.
> Nemůžete vytvořit vlastní klíčová slova ze sady SDK nebo pomocí volání REST.

1. Pokud ještě nemáte předplatné pro rozpoznávání řeči, vyberte [**Vytvořit předplatné**](https://go.microsoft.com/fwlink/?linkid=2086754)a přihlaste se ke službě [Speech Studio](https://aka.ms/sdsdk-speechportal) a **přihlaste** se.

1. Na stránce [vlastní klíčové slovo](https://aka.ms/sdsdk-wakewordportal) vytvořte **Nový projekt**. 

1. Zadejte **název**, volitelný **Popis**a vyberte jazyk. Potřebujete jeden projekt na jazyk a podpora je aktuálně omezená na `en-US` jazyk.

    ![Popis projektu s klíčovým slovem](media/custom-keyword/custom-kws-portal-new-project.png)

1. Vyberte svůj projekt ze seznamu. 

    ![Vyberte projekt vašeho klíčového slova.](media/custom-keyword/custom-kws-portal-project-list.png)

1. Pokud chcete vytvořit nový model klíčových slov, klikněte na **výuka model**.

1. Zadejte **název** modelu, volitelný **Popis**a **klíčové slovo** podle vlastního výběru a pak klikněte na **Další**. Přečtěte si [pokyny](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) pro výběr efektivního klíčového slova.

    ![Zadejte své klíčové slovo](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portál vytvoří kandidáty na kandidáty pro klíčové slovo. Naslouchat každému kandidátovi kliknutím na tlačítko Přehrát a odebrat kontroly u všech nesprávných výslovností. Až budou kontrolovány jenom dobré výslovnosti, klikněte na **výuka** a začněte vygenerovat model klíčových slov. 

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Generování modelu může trvat až 30 minut. Po dokončení modelu se seznam klíčových slov změní ze **zpracování** na **dokončeno** . Pak můžete soubor stáhnout.

    ![Kontrola klíčového slova](media/custom-keyword/custom-kws-portal-download-model.png)

1. Stažený soubor je `.zip` archiv. Extrahujte archiv a zobrazíte soubor s `.table` příponou. Jedná se o soubor, který v sadě SDK použijete v další části, a nezapomeňte si uvědomit jeho cestu. název souboru odráží název vašeho klíčového slova, například klíčové slovo **aktivovat zařízení** má název souboru `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Použití modelu klíčového slova se sadou SDK

Nejprve načtěte soubor modelu klíčových slov pomocí `FromFile()` statické funkce, která vrátí `KeywordRecognitionModel` . Použijte cestu k `.table` souboru, který jste stáhli ze sady Speech Studio. Kromě toho se vytvoří `AudioConfig` pomocí výchozího mikrofonu a pak se vytvoří instance nového `KeywordRecognizer` pomocí konfigurace zvuku.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

V dalším kroku je spuštění rozpoznávání klíčového slova provedeno s jedním voláním metody `RecognizeOnceAsync()` předáním objektu modelu. Tím spustíte relaci rozpoznávání klíčových slov, která trvá, dokud nebude klíčové slovo rozpoznáno. Tento vzor návrhu se proto obecně používá v aplikacích s více vlákny nebo v případech použití, kde můžete počkat na neomezenou dobu probuzení slova.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Příklad, který je zde znázorněn, používá místní rozpoznávání klíčového slova, protože nevyžaduje `SpeechConfig` objekt pro kontext ověřování a nekontaktuje back-end. Rozpoznávání klíčových slov a ověřování však můžete spustit [pomocí nepřetržitého back-endu připojení](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).

## <a name="next-steps"></a>Další kroky

Otestujte svoje vlastní klíčové slovo pomocí [sady Speech Devices SDK pro rychlý Start](https://aka.ms/sdsdk-quickstart).
