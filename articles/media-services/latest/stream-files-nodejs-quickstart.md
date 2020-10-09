---
title: Streamování videosouborů pomocí Azure Media Services-Node.js | Microsoft Docs
description: Pomocí kroků v tomto kurzu vytvoříte nový účet Azure Media Services, zakódujete soubor a Streamujte ho do Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 38207ac47d2e58c689f1933a6ade6d5c86cd44ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268668"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Kurz: kódování vzdáleného souboru na základě adresy URL a streamu Node.js videa

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto kurzu se dozvíte, jak snadné je zakódovat a spustit streamování videí na nejrůznějších prohlížečích a zařízeních pomocí Azure Media Services. Vstupní obsah se dá specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure.

Ukázka v tomto článku zakóduje obsah, který zpřístupníte přes adresu URL HTTPS. Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

Na konci kurzu budete moct streamovat video.  

![Přehrávání videa](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalovat [Node.js](https://nodejs.org/en/download/)
- [Vytvořte účet Media Services](./create-account-howto.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Naklonujte úložiště GitHub obsahující Node.js ukázka streamování do počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Ukázka se nachází ve složce [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) .

Otevřete [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) ve staženém projektu. Nahraďte `endpoint config` hodnoty přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](./access-api-howto.md).

Tato ukázka provede následující akce:

1. Vytvoří **transformaci** (nejprve zkontroluje, zda zadaná transformace existuje). 
2. Vytvoří výstupní **Asset** , který se používá jako výstup **úlohy**kódování.
3. Vytvoří vstup **úlohy**, který je založen na adrese URL https.
4. Odešle **úlohu** kódování pomocí vstupu a výstupu, který byl vytvořen dříve.
5. Zkontroluje stav úlohy.
6. Vytvoří **Lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Aplikace stáhne kódované soubory. Vytvořte složku, do které chcete výstupní soubory přejít, a aktualizujte hodnotu proměnné **outputFolder** v souboru [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) .
1. Otevřete **příkazový řádek**, přejděte do adresáře ukázky a spusťte následující příkazy.

    ```
    npm install 
    node index.js
    ```

Po dokončení práce by se měl zobrazit podobný výstup:

![Snímek obrazovky okna s příkazem výstup z ukázkové aplikace StreamFileSample zobrazující adresy URL tří souborů stažených do místního adresáře](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
 
     Můžete vložit adresu URL ve formátu HLS, pomlčka nebo vyhlazení a Azure Media Player přepnout na příslušný protokol pro streamování pro přehrávání na zařízení automaticky.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už v rámci skupiny prostředků nepotřebujete žádné prostředky, včetně Media Services a účtů úložiště, které jste pro tento kurz vytvořili, odstraňte skupinu prostředků.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Viz také

[Kódy chyb úlohy](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Media Services koncepty](concepts-overview.md)
