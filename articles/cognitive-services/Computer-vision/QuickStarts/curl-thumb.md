---
title: 'Rychlý start: Vygenerování miniatury – REST, cURL – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a cURL.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: afe9aad1981c53d2ce0e06d68a4b36401272e333
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634655"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Rychlý start: Vygenerování miniatury pomocí rozhraní REST API a cURL v počítačovém zpracování obrazu

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní REST API počítačového zpracování obrazu. Miniaturu obrázku můžete vygenerovat pomocí metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Miniaturu obrázku můžete vygenerovat pomocí [metody Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. `<Subscription Key>` nahraďte platným klíčem předplatného.
1. `<File>` nahraďte cestou a názvem souboru k uložení miniatury.
1. V případě potřeby změňte adresu URL žádosti (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`), aby se použilo umístění, ze kterého jste získali klíče předplatného.
1. Volitelně můžete změnit obrázek (`{\"url\":\"...`) pro analýzu.
1. Na počítači s nainstalovaným cURL otevřete příkazové okno.
1. Kód vložte do tohoto okna a spusťte příkaz.

>[!NOTE]
>Ve volání REST musíte použít stejné umístění, které jste použili k získání klíčů předplatného. Pokud jste například získali klíče předplatného z „westus“, nahraďte westcentralus v adrese URL uvedené níže umístěním westus.

## <a name="prerequisites"></a>Požadavky

- Musíte mít [cURL](https://curl.haxx.se/windows).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-command"></a>Vytvoření a spuštění ukázkového příkazu

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Hodnotu `<thumbnailFile>` nahraďte cestou a názvem souboru, do kterého chcete miniaturu uložit.
    1. Adresu URL požadavku (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) nahraďte adresou URL koncového bodu metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Volitelně můžete změnit adresu URL obrázku v textu požadavku (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adresu URL jiného obrázku, ze kterého se má generovat miniatura.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a pak příkaz spusťte.

```console
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď zapíše obrázek miniatury do souboru určeného v `<thumbnailFile>`. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tuto ukázku nepotřebujete, zavřete okno příkazového řádku a textový editor.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu, které se používá pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
