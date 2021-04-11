---
title: Postup při kódování a streamování videosouborů pomocí Node.JS
description: Postup streamování videosouborů pomocí Node.JS Pomocí kroků v tomto kurzu vytvoříte nový účet Azure Media Services, zakódujete soubor a Streamujte ho do Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, Stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 5bb061af37f6f6d7e6e27cf25f0faa63bca7353c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109182"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Postup při kódování a streamování videosouborů pomocí Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Toto rychlé zprovoznění předvádí, jak je jednoduché kódovat a začít se streamováním videí pomocí služby Azure Media Services v široké škále prohlížečů a zařízení. Vstupní videosoubor se dá zadat pomocí adres URL protokolu HTTPS, adres URL SAS nebo cest k souborům umístěným v úložišti objektů BLOB v Azure.

Na konci tohoto rychlého startu budete znát:

- Postup při kódování pomocí Node.JS
- Postup při streamování pomocí Node.JS
- Postup nahrání souboru z adresy URL HTTPS pomocí Node.JS
- Použití HLS nebo PŘERUŠOVANého přehrávače klienta s Node.JS

Ukázka v tomto článku zakóduje obsah, který zpřístupníte přes adresu URL HTTPS. Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS.

![Přehrávání videa](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Nainstalovat [Node.js](https://nodejs.org/en/download/)
- [Vytvořte účet Media Services](./create-account-howto.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít pro přístup k rozhraní API.
- Projděte si článek [Konfigurace a připojení pomocí Node.js](./configure-connect-nodejs-howto.md) , jak nejprve porozumět tomu, jak používat Node.js klientské sady SDK.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Naklonujte úložiště GitHub obsahující Node.js ukázka streamování do počítače pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Ukázka se nachází ve složce [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) .

Ve staženém projektu otevřete [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) . Aktualizujte *ukázkový soubor. env* v kořenové složce s hodnotami a přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](./access-api-howto.md). Přejmenujte *vzorový soubor. env* na *. env* (Ano, pouze rozšíření).

Tato ukázka provede následující akce:

1. Vytvoří **transformaci** s [přednastaveným kódováním s podporou obsahu](./encode-content-aware-concept.md). Nejprve zkontroluje, zda zadaná transformace existuje.
1. Vytvoří výstupní **prostředek** , který je používán **úlohou** kódování, aby obsahoval výstup.
1. Volitelně nahraje místní soubor pomocí sady SDK pro úložiště objektů BLOB.
1. Vytvoří vstup **úlohy** , který je založen na adrese URL HTTPS nebo nahraném souboru.
1. Odešle **úlohu** kódování pomocí vstupu a výstupu, který byl vytvořen dříve.
1. Kontroluje stav úlohy.
1. Stáhne výstup úlohy kódování do místní složky.
1. Vytvoří **Lokátor streamování** , který se použije v přehrávači.
1. Vytvoří adresy URL streamování pro HLS a SPOJOVNÍKy.
1. Přehraje obsah zpátky v aplikaci přehrávače – Azure Media Player

## <a name="run-the-sample"></a>Spuštění ukázky

1. Aplikace stáhne kódované soubory. Vytvořte složku, do které chcete výstupní soubory přejít, a aktualizujte hodnotu proměnné **outputFolder** v souboru [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59) . Ve výchozím nastavení je nastavená na tempo.
1. Otevřete **příkazový řádek** a přejděte do adresáře ukázky.
1. Změňte adresář na složku AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Nainstaluje balíčky používané v *packages.js* souboru.

    ```bash
    npm install 
    ```

1. Změňte adresář na složku *StreamFilesSample* .

    ```bash
    cd StreamFilesSample
    ```

1. Spusťte Visual Studio Code ze složky *AMSv3Samples* . (To se vyžaduje pro spuštění ze složky, kde se nachází složka *. VSCode* a *tsconfig.jsv* souborech)

    ```bash
    cd ..
    code .
    ```

Otevřete složku pro *StreamFilesSample* a otevřete soubor *index. ts* v editoru Visual Studio Code.
V souboru *index. TS* stisknutím klávesy F5 spusťte ladicí program.

## <a name="test-with-azure-media-player"></a>Testování s Azure Media Playerem

K otestování streamu použijte Azure Media Player. Můžete také použít libovolný přehrávač kompatibilní s HLS nebo SPOJOVNÍKem, jako je Shaka Player, HLS.js, Dash.js nebo jiné.

Měli byste být schopni kliknout na odkaz vygenerovaný v ukázce a spustit AMP Player s již načtený manifest s POMLČKou.

> [!NOTE]
> Pokud se přehrávač hostuje na webu HTTPS, nezapomeňte adresu URL aktualizovat tak, aby obsahovala „https“. 

1. Otevřete webový prohlížeč a přejděte na [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Do pole **Adresa URL:** vložte jednu z hodnot adresy URL streamování, které jste dostali při spuštění aplikace. Můžete vložit adresu URL ve formátu HLS, pomlčka nebo vyhlazení a Azure Media Player přepnout na příslušný protokol pro streamování pro přehrávání na zařízení automaticky.
3. Stiskněte **Update Player** (Aktualizovat přehrávač).

Azure Media Player můžete použít pro účely testování, nesmí se ale používat v produkčním prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už v rámci skupiny prostředků nepotřebujete žádné prostředky, včetně Media Services a účtů úložiště, které jste pro tento kurz vytvořili, odstraňte skupinu prostředků.

Spusťte následující příkaz rozhraní příkazového řádku:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Další dokumentaci pro vývojáře k Node.js v Azure

- [Azure pro JavaScript & Node.js vývojáři](/azure/developer/javascript/)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti centra Git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentace k balíčku Azure pro vývojáře Node.js](/javascript/api/overview/azure/)

## <a name="see-also"></a>Viz také

- [Kódy chyb úlohy](/rest/api/media/jobs/get#joberrorcode).
- [instalace npm @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure pro JavaScript & Node.js vývojáři](/azure/developer/javascript/)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Další kroky

> [Media Services koncepty](concepts-overview.md)
