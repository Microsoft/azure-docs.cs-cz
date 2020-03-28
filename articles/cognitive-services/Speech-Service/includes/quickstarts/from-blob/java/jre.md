---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 02e0e1494a897b31cb6ef28083677fa48f854c91
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925322"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Otevření projektu v aplikaci Eclipse

Prvním krokem je, aby se ujistil, že máte svůj projekt otevřený v Eclipse.

1. Spuštění Eclipse
2. Načtěte projekt `Main.java`a otevřete .

## <a name="add-a-reference-to-gson"></a>Přidání odkazu na Gson
Budeme používat externí JSON serializátor / deserializer v tomto rychlém startu. Pro Javu jsme si vybrali [Gson](https://github.com/google/gson).

Otevřete soubor pom.xml a přidejte následující odkaz.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Obaly JSON

Jako REST API přijmout požadavky ve formátu JSON a také vrátit výsledky v JSON jsme mohli komunikovat s nimi pouze řetězce, ale to se nedoporučuje.
Aby bylo možné požadavky a odpovědi snadněji spravovat, budeme deklarovat několik tříd použít pro serializaci / deserializaci JSON.

Jděte do toho a `Main`dát své prohlášení před .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta Http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento `Main` kód do[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento `Main` kód do[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat požadavek a zkontrolovat jeho stav
Nyní zaúčtujeme požadavek službě Řeči a zkontrolujeme kód počáteční odpovědi. Tento kód odpovědi bude jednoduše označuje, pokud služba obdržela požadavek. Služba vrátí adresu URL v záhlaví odpovědi, která je umístění, kde bude ukládat stav přepisu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte na dokončení přepisu
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme se tak často dotazovat na jeho stav. Budeme to kontrolovat každých 5 sekund.

Můžete zkontrolovat stav načtením obsahu na url jsme dostali, když posta žádost. Když získáme obsah zpět, dekonstruujeme jej do jedné z našich pomocných tříd, abychom usnadnili interakci.

Zde je volební kód se zobrazením stavu pro všechno kromě úspěšného dokončení, uděláme to dále.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Jakmile služba úspěšně dokončí přepis, výsledky budou uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Stáhneme obsah této adresy URL, zoserializujeme JSON a projdeme výsledky tisku zobrazovaného textu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Kontrola kódu
V tomto okamžiku by měl váš kód vypadat takto: (Přidali jsme nějaké komentáře k této verzi) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Teď jste připraveni vytvořit aplikaci a otestovat naše rozpoznávání řeči pomocí služby Řeč.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
