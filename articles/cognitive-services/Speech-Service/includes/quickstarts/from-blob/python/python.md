---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d69cb782b3be7035b7571653dcb434e44d22da93
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376519"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Stažení a instalace klientské knihovny rozhraní API

Chcete-li spustit ukázku, je třeba vygenerovat knihovnu Python pro REST API, která je generována prostřednictvím [Swagger](https://swagger.io).

Pro instalaci použijte tento postup:

1. Přejděte na https://editor.swagger.io.
1. Klikněte na **soubor**a pak na **importovat adresu URL**.
1. Zadejte adresu URL Swagger, včetně oblasti pro předplatné služby Speech: `https://<your-region>.cris.ai/docs/v2.0/swagger` .
1. Klikněte na **vygenerovat klienta** a vyberte **Python**.
1. Uložte klientskou knihovnu.
1. Extrahujte stažený python-client-generated.zip někam do systému souborů.
1. Do prostředí Pythonu nainstalujte extrahovaný klientský modul Pythonu pomocí PIP: `pip install path/to/package/python-client` .
1. Instalovaný balíček má název `swagger_client` . Pomocí příkazu můžete ověřit, zda byla instalace úspěšná `python -c "import swagger_client"` .

> [!NOTE]
> Kvůli [známé chybě v modulu pro vytváření Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541)může dojít k chybám při importu `swagger_client` balíčku.
> Ty je možné opravit odstraněním řádku s obsahem.
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> ze souboru `swagger_client/models/model.py` a řádku s obsahem
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> ze souboru `swagger_client/models/inner_error.py` uvnitř nainstalovaného balíčku. Chybová zpráva vám sdělí, kde jsou tyto soubory umístěny pro vaši instalaci.

## <a name="install-other-dependencies"></a>Nainstalovat další závislosti

Ukázka používá `requests` knihovnu. Můžete ji nainstalovat pomocí příkazu

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento kód do `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento kód do `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat žádost a ověřit její stav
Nyní pošleme požadavek službě rozpoznávání řeči a zkontrolujeme kód prvotní odezvy. Tento kód odpovědi jednoduše indikuje, jestli služba požadavek přijala. Služba vrátí adresu URL v hlavičkách odpovědi, kde je umístění, kde bude uložený stav přepisu.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte, až se přepis dokončí.
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme dotazovat se na jeho stav, a to v každém případě často. Zkontrolujeme každých 5 sekund.

Vytvoříme výčet všech přepisů, které prostředek služby Speech Service zpracovává, a hledáme ten, který jsme vytvořili.

Toto je kód pro cyklické dotazování se zobrazením stavu pro vše s výjimkou úspěšného dokončení. provedeme to ještě dál.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Po úspěšném dokončení přepisu budou výsledky uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Tady zobrazíme výsledek JSON a zobrazíme ho.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Kontrolovat kód
V tomto okamžiku by váš kód měl vypadat takto: (do této verze jsme přidali nějaké komentáře.) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
