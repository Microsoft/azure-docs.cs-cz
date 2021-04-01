---
ms.openlocfilehash: e8d8300d2be7a2a48cb48a51b0f81c96f68c45aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750451"
---
1. Klonovat úložiště z tohoto umístění: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. V Visual Studio Code otevřete složku, do které se úložiště stáhlo.
1. V Visual Studio Code přejít do složky *Src/Cloud-to-Device-Console-App* Tam vytvořte soubor a pojmenujte ho *appsettings.js*. Tento soubor bude obsahovat nastavení potřebná ke spuštění programu.
1. Zkopírujte obsah z souboru *~/clouddrive/lva-sample/appsettings.jspro* soubor, který jste dříve vytvořili v tomto rychlém startu.

    Text by měl vypadat jako následující výstup.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Přejít do složky *Src/Edge* a vytvořit soubor s názvem *. env*.
1. Zkopírujte obsah souboru */clouddrive/lva-Sample/Edge-Deployment/.env* . Text by měl vypadat jako následující kód.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    