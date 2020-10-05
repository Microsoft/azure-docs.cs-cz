---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 2a8deaa76c02e86c9304c2221c0617041ae2e138
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377037"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Otevření projektu v zatmění

Prvním krokem je ujistit se, že máte projekt otevřený v zatmění.

1. Spuštění Eclipse
2. Načtěte projekt a otevřete `Main.java` .

## <a name="add-a-reference-to-gson"></a>Přidat odkaz na gson
V tomto rychlém startu použijeme externí serializátor/deserializaci JSON. Pro Java jsme zvolili [gson](https://github.com/google/gson).

Otevřete pom.xml a přidejte následující odkaz.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Obálky JSON

Jak REST API přijímá požadavky ve formátu JSON a také vrátí výsledky ve formátu JSON, můžeme s nimi pracovat jenom s řetězci, ale to se nedoporučuje.
Aby bylo možné žádosti a odpovědi snadněji spravovat, deklarujeme několik tříd, které se použijí pro serializaci nebo deserializaci JSON.

Pokračujte a uveďte jejich deklarace dřív `Main` .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento kód do `Main`[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento kód do `Main`[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat žádost a ověřit její stav
Nyní pošleme požadavek službě rozpoznávání řeči a zkontrolujeme kód prvotní odezvy. Tento kód odpovědi jednoduše indikuje, jestli služba požadavek přijala. Služba vrátí adresu URL v hlavičkách odpovědi, kde je umístění, kde bude uložený stav přepisu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte, až se přepis dokončí.
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme dotazovat se na jeho stav, a to v každém případě často. Zkontrolujeme každých 5 sekund.

Stav můžeme zjistit načtením obsahu na adrese URL, kterou jsme dostali při odeslání žádosti. Když získáme obsah zpátky, deserializovatme ho do jedné z našich pomocných tříd, aby bylo snazší pracovat s.

Toto je kód pro cyklické dotazování se zobrazením stavu pro vše s výjimkou úspěšného dokončení. provedeme to ještě dál.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Po úspěšném dokončení přepisu budou výsledky uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Stáhneme obsah této adresy URL, deserializaci JSON a projdete výsledky vytištěním zobrazovaného textu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Kontrolovat kód
V tomto okamžiku by váš kód měl vypadat takto: (do této verze jsme přidali nějaké komentáře.) [! Code-Java [] (~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
