---
title: 'Úvodní příručka: Generování miniatury – REST, cURL'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6cb9dadc107c6907f1ccb28a876270e577f10395
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977297"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Úvodní příručka: Generování miniatury pomocí rozhraní API RENA počítačového vidění a cURL

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API PRO ODPOČINEK v počítači. Určíte požadovanou výšku a šířku, která se může lišit v poměru stran od vstupního obrazu. Počítačové vidění využívá inteligentní oříznutí k inteligentní identifikaci oblasti zájmu a generování souřadnic oříznutí kolem této oblasti.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít [cURL](https://curl.haxx.se/windows).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Můžete získat bezplatný zkušební klíč od [try cognitive services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili k odběru počítačového vidění a získali klíč.

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Pomocí [metody Získat miniaturu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)můžete vygenerovat miniaturu obrázku.

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. `<File>` nahraďte cestou a názvem souboru k uložení miniatury.
1. V případě potřeby změňte adresu URL žádosti (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`), aby se použilo umístění, ze kterého jste získali klíče předplatného.
1. Volitelně můžete změnit obrázek (`{\"url\":\"...`) pro analýzu.
1. Na počítači s nainstalovaným cURL otevřete příkazové okno.
1. Kód vložte do tohoto okna a spusťte příkaz.

>[!NOTE]
>Ve volání REST musíte použít stejné umístění, které jste použili k získání klíčů předplatného. Pokud jste například získali klíče předplatného z „westus“, nahraďte westcentralus v adrese URL uvedené níže umístěním westus.

## <a name="create-and-run-the-sample-command"></a>Vytvoření a spuštění ukázkového příkazu

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Hodnotu `<thumbnailFile>` nahraďte cestou a názvem souboru, do kterého chcete miniaturu uložit.
    1. Nahraďte první část adresy`westcentralus`URL požadavku ( ) textem ve vlastní adrese URL koncového bodu.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Volitelně můžete změnit adresu URL obrázku v textu požadavku (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adresu URL jiného obrázku, ze kterého se má generovat miniatura.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a pak příkaz spusťte.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď zapíše obrázek miniatury do souboru určeného v `<thumbnailFile>`. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo. Pokud se zdá, že požadavek je úspěšný, ale vytvořená miniatura není platný soubor obrázku, může se stát, že klíč předplatného není platný.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu, jak analyzovat obrázek, detekovat celebrity a orientační body, vytvořit miniaturu a extrahovat tištěný a ručně psaný text. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
