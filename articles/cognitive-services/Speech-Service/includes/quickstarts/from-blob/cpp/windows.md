---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 20dfc49acdaa30bea6f0652640e007c16f08c572
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925274"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Otevření projektu v sadě Visual Studio

Prvním krokem je ujistěte se, že máte projekt otevřený v sadě Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt `helloworld.cpp`a otevřete .

## <a name="add-a-references"></a>Přidání odkazů

Abychom urychlili vývoj kódu, budeme používat několik externích komponent:
* [CPP Zbytek SDK](https://github.com/microsoft/cpprestsdk) Klientská knihovna pro volání REST do služby REST.
* [nlohmann/json](https://github.com/nlohmann/json) Šikovná knihovna JSON Parsing / Serialization / Deserialization.

Oba lze nainstalovat pomocí [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Obaly JSON

Jako REST API přijmout požadavky ve formátu JSON a také vrátit výsledky v JSON jsme mohli komunikovat s nimi pouze řetězce, ale to se nedoporučuje.
Aby bylo možné požadavky a odpovědi snadněji spravovat, budeme deklarovat několik tříd použít pro serializaci / deserializaci JSON a některé metody na pomoc nlohmann/json.

Jděte do toho a `recognizeSpeech` dát své prohlášení před .
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta Http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento kód do`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento kód do`recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat požadavek a zkontrolovat jeho stav
Nyní zaúčtujeme požadavek službě Řeči a zkontrolujeme kód počáteční odpovědi. Tento kód odpovědi bude jednoduše označuje, pokud služba obdržela požadavek. Služba vrátí adresu URL v záhlaví odpovědi, která je umístění, kde bude ukládat stav přepisu.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte na dokončení přepisu
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme se tak často dotazovat na jeho stav. Budeme to kontrolovat každých 5 sekund.

Můžete zkontrolovat stav načtením obsahu na url jsme dostali, když posta žádost. Když získáme obsah zpět, dekonstruujeme jej do jedné z našich pomocných tříd, abychom usnadnili interakci.

Zde je volební kód se zobrazením stavu pro všechno kromě úspěšného dokončení, uděláme to dále.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Jakmile služba úspěšně dokončí přepis, výsledky budou uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Stáhneme obsah této adresy URL, zoserializujeme JSON a projdeme výsledky tisku zobrazovaného textu.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Kontrola kódu
V tomto okamžiku by měl váš kód vypadat takto: (Přidali jsme některé komentáře k této verzi)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Teď jste připraveni vytvořit aplikaci a otestovat naše rozpoznávání řeči pomocí služby Řeč.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
