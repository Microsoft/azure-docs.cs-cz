---
title: 'Rychlý Start: rozpoznávání řeči uložených v BLOB Storage C# , – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 96062057a139e4ab2c91792b5c451e093f7f4c96
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828773"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v sadě Visual Studio

Prvním krokem je ujistit se, že máte projekt otevřený v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Přidat odkaz na NewtonSoftJSon

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt **HelloWorld** a pak výběrem **možnosti spravovat balíčky NuGet** Zobrazte správce balíčků NuGet.

1. V pravém horním rohu Najděte rozevírací seznam **zdroj balíčku** a ujistěte se, že je vybraná možnost **`nuget.org`** .

1. V levém horním rohu vyberte **Procházet**.

1. Do vyhledávacího pole zadejte *Newtonsoft. JSON* a vyberte **ENTER**.

1. Ve výsledcích hledání vyberte balíček **Newtonsoft. JSON** a pak vyberte **instalovat** a nainstalujte nejnovější stabilní verzi.

1. Přijetím všech smluv a licencí spusťte instalaci.

   Po instalaci balíčku se v okně **konzoly Správce balíčků** zobrazí potvrzení.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]
(Hodnoty `YourSubscriptionKey`, `YourServiceRegion`a `YourFileUrl` budete muset nahradit vlastními hodnotami.)
## <a name="json-wrappers"></a>Obálky JSON

Jak REST API přijímá požadavky ve formátu JSON a také vrátí výsledky ve formátu JSON, můžeme s nimi pracovat jenom s řetězci, ale to se nedoporučuje.
Aby bylo možné žádosti a odpovědi snadněji spravovat, deklarujeme několik tříd, které se použijí pro serializaci nebo deserializaci JSON.

Až `TranscribeAsync`, pokračujte a uveďte jejich deklarace.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento kód do `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento kód do `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat žádost a ověřit její stav
Nyní pošleme požadavek službě rozpoznávání řeči a zkontrolujeme kód prvotní odezvy. Tento kód odpovědi jednoduše indikuje, jestli služba požadavek přijala. Služba vrátí adresu URL v hlavičkách odpovědi, kde je umístění, kde bude uložený stav přepisu.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte, až se přepis dokončí.
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme dotazovat se na jeho stav, a to v každém případě často. Zkontrolujeme každých 5 sekund.

Stav můžeme zjistit načtením obsahu na adrese URL, kterou jsme dostali při odeslání žádosti. Když získáme obsah zpátky, deserializovatme ho do jedné z našich pomocných tříd, aby bylo snazší pracovat s.

Toto je kód pro cyklické dotazování se zobrazením stavu pro vše s výjimkou úspěšného dokončení. provedeme to ještě dál.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Po úspěšném dokončení přepisu budou výsledky uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Tady vytvoříme požadavek na stažení těchto výsledků do dočasného souboru před čtením a deserializací.
Po načtení výsledků je můžete vytisknout do konzoly.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Kontrolovat kód
V tomto okamžiku by váš kód měl vypadat takto: (do této verze jsme přidali nějaké komentáře) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

1. **Zkompilujte kód** -z panelu nabídek v aplikaci Visual Studio, vyberte **sestavení** **řešení**Build > .
2. **Spusťte aplikaci** – z panelu nabídek zvolte možnost **ladění** > **Spustit ladění** nebo stiskněte klávesu **F5**.
3. **Spustit rozpoznávání** – zobrazí výzvu k vymluvenému vynechání fráze v angličtině. Váš hlas se odešle službě Speech, přepisu jako text a vykreslí se v konzole nástroje.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
