---
title: 'Rychlý Start: vygenerování miniatur – REST, kudrlinkou'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 40d91d8e8095dea89354f169d9cf6d2092961400
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681151"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Rychlý Start: vygenerování miniatury pomocí Počítačové zpracování obrazu REST API a kudrlinkou

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí REST API Počítačové zpracování obrazu. Určete požadovanou výšku a šířku, která se může v poměrné dávce od vstupní image lišit. Počítačové zpracování obrazu používá inteligentní ořezávání k inteligentnímu identifikaci oblasti zájmu a k vygenerování souřadnic oříznutí kolem této oblasti.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít [cURL](https://curl.haxx.se/windows).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče.

## <a name="create-and-run-the-sample-command"></a>Vytvoření a spuštění ukázkového příkazu

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Hodnotu `<thumbnailFile>` nahraďte cestou a názvem souboru, do kterého chcete miniaturu uložit.
    1. Nahraďte první část adresy URL požadavku ( `westcentralus` ) textem ve vaší vlastní adrese URL koncového bodu.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Volitelně můžete změnit adresu URL obrázku v textu požadavku (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adresu URL jiného obrázku, ze kterého se má generovat miniatura.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku.
1. Stiskněte klávesu ENTER a program se spustí.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď zapíše obrázek miniatury do souboru určeného v `<thumbnailFile>`. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo. Pokud se žádost zdá být úspěšná, ale vytvořená Miniatura není platným souborem obrázku, může to být tím, že váš klíč předplatného není platný.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu k analýze obrázku, detekci celebrit a orientačních bodů, vytvoření miniatury a extrakci vytištěného a rukopisného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
