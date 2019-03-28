---
title: Stream video soubory pomocí Azure Media Services - Node.js | Dokumentace Microsoftu
description: Pomocí kroků v tomto rychlém startu můžete vytvořit nový účet služby Azure Media Services, zakódovat soubor a streamovat ho do Azure Media Playeru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 22b7f2380b509daa4cb9931d6fc57c1297628e3d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522849"
---
# <a name="quickstart-stream-video-files---nodejs"></a>Rychlý start: Stream video soubory – Node.js

Toto rychlé zprovoznění předvádí, jak je jednoduché kódovat a začít se streamováním videí pomocí služby Azure Media Services v široké škále prohlížečů a zařízení. Vstupní obsah se dá specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure.
Ukázka v tomto tématu kóduje obsah, který zpřístupníte prostřednictvím adresy URL protokolu HTTPS. Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

Na konci rychlého startu budete schopni streamovat video.  

![Přehrávání videa](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Instalovat [Node.js](https://nodejs.org/en/download/)
- [Vytvoření účtu Media Services](create-account-cli-how-to.md).<br/>Ujistěte se, že hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services mějte na paměti.
- Postupujte podle kroků v [rozhraní API k přístupu k Azure Media Services pomocí Azure CLI](access-api-cli-how-to.md) a uložte přihlašovací údaje. Je potřeba použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky aplikace

Naklonujte úložiště GitHub obsahující streamování ukázku Node.js k počítači pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Tato ukázka se nachází v [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) složky.

Otevřít [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) stažený projekt. Nahradit `endpoint config` hodnoty s přihlašovacími údaji, které jste získali z [přístup k rozhraní API](access-api-cli-how-to.md).

Tato ukázka provede následující akce:

1. Vytvoří **transformace** (nejprve, zkontroluje, jestli existuje Zadaná transformace). 
2. Vytvoří výstup **Asset** , který se používá jako kódování **úlohy**výstup.
3. Vytvoří **úlohy**vstup, který je založen na adresu URL HTTPS.
4. Odešle kódování **úlohy** pomocí vstup a výstup, který jste vytvořili dříve.
5. Zkontroluje stav úlohy.
6. Vytvoří **Lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Aplikace stáhne kódované soubory. Vytvořte složku, kam chcete pro výstupní soubory do přejděte a aktualizujte hodnotu **outputFolder** proměnné v [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) souboru.
1. Otevřít **příkazového řádku**, přejděte do adresáře ukázky a spusťte následující příkazy.

    ```
    npm install 
    node index.js
    ```

Po dokončení spuštěná, zobrazí se podobný výstup:

![Spusťte](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na adresu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
 
     Můžete vložit adresu URL do HLS, Dash, nebo technologie Smooth formátu a Azure Media Player se přepne na příslušný protokol streamování pro přehrávání na vašem zařízení automaticky.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud ze skupiny prostředků už žádné prostředky nepotřebujete, včetně účtu služby Media Services a účtů úložiště, které jste vytvořili v rámci tohoto rychlého startu, odstraňte tuto skupinu prostředků.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Další informace najdete v tématech

[Kódy chyb pro úlohy](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Koncepty služby Media Services](concepts-overview.md)
