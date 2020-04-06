---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: fcf2d3513936d50a0a5be8653e9bee5b30821e7d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671029"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nahrání zdrojového souboru do objektu blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Stažení a instalace klientské knihovny rozhraní API

Chcete-li provést ukázku, musíte vygenerovat knihovnu Pythonu pro REST API, které je generováno prostřednictvím [Swagger](https://swagger.io).

Postupem instalace postupujte takto:

1. Přejděte do části https://editor.swagger.io (Soubor > Nový > Jiné).
1. Klepněte na **položku Soubor**a potom klepněte na **položku Importovat adresu URL**.
1. Zadejte adresu URL Swagger včetně oblasti `https://<your-region>.cris.ai/docs/v2.0/swagger`předplatného služby Speech: .
1. Klepněte na **tlačítko Generovat klienta** a vyberte **python**.
1. Uložte klientskou knihovnu.
1. Extrahujte stažený soubor.zip python-client-generated někde ve vašem systému souborů.
1. Nainstalujte extrahovaný modul python-client ve `pip install path/to/package/python-client`vašem prostředí Pythonu pomocí pip: .
1. Nainstalovaný balíček `swagger_client`má název . Můžete zkontrolovat, zda instalace fungovala pomocí příkazu `python -c "import swagger_client"`.

> [!NOTE]
> Vzhledem ke [známé chybě v automatické generaci Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541), `swagger_client` může dojít k chybám při importu balíčku.
> Ty lze opravit odstraněním řádku s obsahem
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> ze souboru `swagger_client/models/model.py` a řádku s obsahem
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> ze souboru `swagger_client/models/inner_error.py` uvnitř nainstalovaného balíčku. Chybová zpráva vám sdělí, kde jsou tyto soubory umístěny pro vaši instalaci.

## <a name="install-other-dependencies"></a>Instalace dalších závislostí

Ukázka používá `requests` knihovnu. Můžete jej nainstalovat pomocí příkazu

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Vytvoření a konfigurace klienta Http
První věc, kterou budeme potřebovat, je klient http, který má správnou základní adresu URL a sadu ověřování.
Vložit tento `transcribe` kód do[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generovat žádost o přepis
Dále vygenerujeme žádost o přepis. Přidat tento `transcribe` kód do[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Odeslat požadavek a zkontrolovat jeho stav
Nyní zaúčtujeme požadavek službě Řeči a zkontrolujeme kód počáteční odpovědi. Tento kód odpovědi bude jednoduše označuje, pokud služba obdržela požadavek. Služba vrátí adresu URL v záhlaví odpovědi, která je umístění, kde bude ukládat stav přepisu.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Počkejte na dokončení přepisu
Vzhledem k tomu, že služba zpracovává přepis asynchronně, musíme se tak často dotazovat na jeho stav. Budeme to kontrolovat každých 5 sekund.

Vyjmenujeme všechny přepisy, které tento prostředek služby Řeči zpracovává, a vyhledáme ten, který jsme vytvořili.

Zde je volební kód se zobrazením stavu pro všechno kromě úspěšného dokončení, uděláme to dále.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Zobrazení výsledků přepisu
Jakmile služba úspěšně dokončí přepis, výsledky budou uloženy v jiné adrese URL, kterou můžeme získat z odpovědi na stav.

Zde jsme si, že výsledek JSON a zobrazit jej.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Kontrola kódu
V tomto okamžiku by měl váš kód vypadat takto: (Přidali jsme některé komentáře k této verzi)[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Teď jste připraveni vytvořit aplikaci a otestovat naše rozpoznávání řeči pomocí služby Řeč.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
