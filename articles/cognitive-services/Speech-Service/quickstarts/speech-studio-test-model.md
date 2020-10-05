---
title: Testování modelu pomocí zvukových souborů – Speech Studio
titleSuffix: Azure Cognitive Services
description: V tomto postupu použijete Speech Studio k testování rozpoznávání řeči v zvukovém souboru.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 9101944a567df5000d3584ed48eff24e4c5e0057
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89565799"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Testování modelu pomocí zvukového souboru v studiu řeči

V tomto postupu použijete Speech Studio k převodu řeči ze zvukového souboru na text. Speech Studio umožňuje testovat, porovnávat, zlepšovat a nasazovat modely rozpoznávání řeči pomocí souvisejícího textu, zvukového přepisu a pokynů k výslovnosti, které zadáte.

## <a name="prerequisites"></a>Předpoklady

Než použijete portál Speech, [postupujte podle těchto pokynů a vytvořte účet Azure a přihlaste se k odběru služby Speech](../how-to-custom-speech.md#set-up-your-azure-account). Toto jednotné předplatné vám umožní přístup k převodu řeči na text, převod textu na řeč, rozpoznávání řeči a Custom Speech Portal.

## <a name="download-an-audio-file"></a>Stažení zvukového souboru

Postupujte podle těchto kroků a Stáhněte si zvukový soubor, který obsahuje řeč a zabalit ho do souboru ZIP.

1. Stáhněte si **[ukázkový soubor WAV z tohoto odkazu](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** tak, že kliknete pravým tlačítkem na odkaz a vyberete **Uložit odkaz jako**. Kliknutím na **Uložit** stáhněte `whatstheweatherlike.wav` soubor.
2. Pomocí Průzkumníka souborů nebo okna terminálu s nástrojem zip vytvořte soubor zip s názvem `whatstheweatherlike.zip` , který obsahuje `whatstheweatherlike.wav` stažený soubor. V systému Windows můžete otevřít Průzkumníka Windows, přejít do `Downloads` složky, kliknout pravým tlačítkem myši `whatstheweatherliike.wav` , kliknout na možnost **Odeslat do**, kliknout na položku **Komprimovaná (metoda ZIP)** a stisknutím klávesy Enter přijměte výchozí název souboru.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Vytvoření projektu na portálu Custom Speech

Pomocí těchto kroků můžete vytvořit projekt, který obsahuje váš soubor zip jednoho zvukového souboru.

1. Otevřete aplikaci [Speech Studio](https://speech.microsoft.com/)a klikněte na **Nový projekt**. Zadejte název tohoto projektu a klikněte na **vytvořit**. Váš projekt se zobrazí v seznamu Custom Speech.
2. Klikněte na název projektu. Na kartě data klikněte na **Odeslat data**.
3. Datový typ řeči je ve výchozím nastavení **jenom zvuk**, takže klikněte na **Další**.
4. Pojmenujte novou datovou sadu řeči `MyZipOfAudio` a klikněte na **Další**.
5. Klikněte na **Procházet soubory...**, přejděte k `whatstheweatherlike.zip` souboru a klikněte na **otevřít**.
6. Klikněte na tlačítko **Nahrát**. Prohlížeč nahraje váš soubor zip do sady Speech Studio a aplikace Speech Studio tento obsah zpracuje.

## <a name="test-a-model"></a>Testování modelu

Jakmile aplikace Speech Studio zpracuje obsah souboru zip, můžete přehrát zdrojový zvuk při kontrole přepisu a vyhledat chyby nebo opomenutí. Postupujte podle těchto kroků a Projděte si kvalitu přepisu v prohlížeči.

1. Klikněte na kartu **testování** a pak klikněte na **Přidat test**.
2. V tomto testu kontrolujeme kvalitu pouze zvukových dat, proto kliknutím na tlačítko **Další** přijměte tento typ testu.
3. Pojmenujte tento test `MyModelTest` a klikněte na **Další**.
4. Klikněte na přepínač nalevo od `MyZipOfAudio` a klikněte na tlačítko **Další**.
5. Rozevírací seznam **model 1** se nastaví na nejnovější model rozpoznávání, takže klikněte na **vytvořit**. Po zpracování obsahu datové sady zvuku se stav testu změní na **úspěch**.
6. Klikněte na **MyModelTest**. Zobrazí se výsledky rozpoznávání řeči. Kliknutím na trojúhelník směřující vpravo v kruhu uslyšíte zvuk a porovnáte, co uslyšíte s textem pomocí kružnice.

## <a name="download-detailed-results"></a>Stáhnout podrobné výsledky

Soubory, které popisují přepisy, si můžete stáhnout mnohem podrobněji. Soubory obsahují lexikální formu rozpoznávání řeči ve zvukových souborech a soubory JSON, které obsahují informace o posunu, trvání a přepisu každého slova. Tyto soubory zobrazíte pomocí následujícího postupu.

1. Klikněte na tlačítko **Stáhnout**.
2. V dialogu pro stažení zrušte výběr **zvuku**a klikněte na **Stáhnout**.
3. Extrahujte stažený soubor zip a prověřte extrahované soubory.

## <a name="next-steps"></a>Další kroky

Přečtěte si o vylepšení přesnosti rozpoznávání řeči prostřednictvím [školení vlastního modelu](../how-to-custom-speech-test-and-train.md).