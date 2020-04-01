---
title: Streamování videosouborů pomocí Mediálních služeb Azure – node.js | Dokumenty společnosti Microsoft
description: Podle pokynů tohoto kurzu vytvořte nový účet Azure Media Services, zakódujte soubor a streamujte ho do Programu Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "69639389"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - Node.js

Tento kurz ukazuje, jak snadné je kódovat a spouštět streamování videí na široké škále prohlížečů a zařízení pomocí Mediálních služeb Azure. Vstupní obsah se dá specifikovat jako HTTPS, URL, SAS nebo cesta k souboru v úložišti objektů Blob Azure.

Ukázka v tomto článku kóduje obsah, který zpřístupníte prostřednictvím adresy URL HTTPS. Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

Na konci tutoriálu budete moci streamovat video.  

![Přehrávání videa](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Instalace [souboru Node.js](https://nodejs.org/en/download/)
- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Mediální služby.
- Postupujte podle kroků v [rozhraní Access Azure Media Services API s rozhraním příkazového příkazového příkazu k Síti Azure](access-api-cli-how-to.md) a uložte přihlašovací údaje. Budete je muset použít pro přístup k rozhraní API.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Klonujte úložiště GitHub, které obsahuje ukázku streamovaného node.js, do počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Ukázka je umístěna ve složce [StreamFilesSample.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Otevřete [soubor index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) ve staženém projektu. Nahraďte `endpoint config` hodnoty pověřeními, která jste získali z [přístupu k api](access-api-cli-how-to.md).

Tato ukázka provede následující akce:

1. Vytvoří **Transformace** (nejprve zkontroluje, zda zadaná transformace existuje). 
2. Vytvoří výstupní **prostředek,** který se používá jako výstup **úlohy**kódování.
3. Vytvoří vstup **úlohy,** který je založen na adrese URL HTTPS.
4. Odešle **úlohu** kódování pomocí vstupního a výstupního, který byl vytvořen dříve.
5. Zkontroluje stav úlohy.
6. Vytvoří **lokátor streamování**.
7. Vytvoří adresy URL pro streamování.

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

1. Aplikace stáhne kódované soubory. Vytvořte složku, do které chcete, aby výstupní soubory přecválay, a aktualizujte hodnotu proměnné **outputFolder** v souboru [index.js.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39)
1. Otevřete **příkazový řádek**, vyhledejte adresář ukázky a spusťte následující příkazy.

    ```
    npm install 
    node index.js
    ```

Po dokončení běhu byste měli vidět podobný výstup:

![Spusťte](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

Tento článek používá k otestování streamu přehrávač Azure Media Player. 

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)a přejděte na .
2. Do pole **URL:** vložte jednu z hodnot adres URL pro streamování, které jste získali při spuštění aplikace. 
 
     Adresu URL můžete vložit ve formátu HLS, Dash nebo Smooth a Azure Media Player se automaticky přepne na příslušný protokol streamování pro automatické přehrávání na vašem zařízení.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete žádné prostředky ve skupině prostředků, včetně mediálních služeb a účtů úložiště, které jste vytvořili pro tento kurz, odstraňte skupinu prostředků.

Proveďte následující příkaz příkazového příkazu příkazu příkazu:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Viz také

[Kódy chyb práce](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Koncepty mediálních služeb](concepts-overview.md)
