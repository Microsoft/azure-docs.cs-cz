---
title: Kurz – změna Azure IoT Edgech živých videí pro video Analytics
description: V tomto kurzu se dozvíte, jak upravit a sestavit moduly pro bránu video Analytics, které používá šablona aplikace zjišťování objektů a pohybu pro video Analytics.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: e5236bb7034ae4d5f86ab2f8f965bdbfbc3d6ccd
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832041"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Kurz: úpravy a sestavení modulů pro bránu pro video Analytics v reálném čase

V tomto kurzu se dozvíte, jak upravit kód modulu IoT Edge pro moduly LVA (Live video Analytics).

V předchozích kurzech se používají předem připravené image modulů.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto kurzu budete potřebovat:

* [Node.js](https://nodejs.org/en/download/) v10 za účelem nebo novější
* [Visual Studio Code](https://code.visualstudio.com/Download) s nainstalovaným rozšířením [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)
* Modul [Docker](https://www.docker.com/products/docker-desktop)
* [Azure Container Registry](../../container-registry/index.yml) hostovat vaše verze modulů.
* Účet [Azure Media Services](../../media-services/index.yml) . Pokud jste dokončili předchozí kurzy, můžete znovu použít ten, který jste vytvořili dříve.

## <a name="clone-the-repository"></a>Klonování úložiště

Pokud jste úložiště ještě neklonované, pomocí následujícího příkazu ho naklonujte do vhodného umístění v místním počítači:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Otevřete místní složku úložiště *Live-video-Analytics* s vs Code.

## <a name="edit-the-deploymentamd64json-file"></a>Upravit deployment.amd64.jsv souboru

1. Pokud jste to ještě neudělali, vytvořte složku s názvem *ref-Apps/lva-Edge-IoT-Central-Gateway/Storage* v místní kopii úložiště **lva-Gateway** . Tato složka je v Gitu ignorována, takže by nedocházelo k nechtěné kontrole důvěrných informací.
1. Zkopírujte soubor *deployment.amd64.js* ze složky pro *instalaci* do složky *úložiště* .
1. V VS Code otevřete *úložiště/deployment.amd64.jsv* souboru.
1. Upravte `registryCredentials` část a přidejte své přihlašovací údaje Azure Container Registry.
1. Úpravou `LvaEdgeGatewayModule` části modul přidejte název své image a název účtu AMS do `env:amsAccountName:value` .
1. Upravte `lvaYolov3` část modul a přidejte název své image.
1. Upravte `lvaEdge` část modul a přidejte název své image.
1. Další informace o tom, jak dokončit konfiguraci, najdete v tématu [Vytvoření aplikace video Analytics v Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) .

## <a name="build-the-code"></a>Sestavení kódu

1. Předtím, než se pokusíte kód poprvé sestavit, spusťte příkaz pomocí terminálu VS Code `npm install` . Tento příkaz nainstaluje požadované balíčky a spustí instalační skripty.

    > [!TIP]
    > Pokud si vyžádáte novější verzi úložiště GitHub, odstraňte složku *node_modules* a spusťte ji `npm install` znovu.

1. Úpravou souboru *./setup/imageConfig.jsna* soubor pro aktualizaci image s názvem na základě názvu registru kontejneru:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Pomocí terminálu VS Code spusťte `docker login [your server].azurecr.io` příkaz. Použijte stejné přihlašovací údaje, které jste zadali v manifestu nasazení pro moduly.

1. Pomocí terminálu VS Code spusťte příkaz pro **opravu verze npm** . Tento skript sestavení nasadí image do vašeho registru kontejneru. Výstup v okně VS Code terminálu ukazuje, jestli je sestavení úspěšné.

1. Verze bitové kopie **LvaEdgeGatewayModule** se zvýší při každém dokončení sestavení. Tuto verzi je nutné použít v souboru manifestu nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s aplikací hotoví, můžete odebrat všechny prostředky, které jste vytvořili následujícím způsobem:

1. V aplikaci IoT Central přejděte na stránku **aplikace** v části **Správa** . Vyberte **Odstranit**.
1. V Azure Portal odstraňte skupinu prostředků **lva-RG** .

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s šablonou aplikace pro video Analytics – objekt a pohyb a LVA IoT Edge, je navržený další krok Další informace o:

> [!div class="nextstepaction"]
> [Sestavování řešení pro maloobchod s využitím služby Azure IoT Central](overview-iot-central-retail.md)