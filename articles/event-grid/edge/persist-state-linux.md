---
title: Trvalý stav v systému Linux-Azure Event Grid IoT Edge | Microsoft Docs
description: Zachovat metadata v systému Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100338"
---
# <a name="persist-state-in-linux"></a>Trvalý stav v systému Linux

Témata a odběry vytvořené v modulu Event Grid jsou ve výchozím nastavení uloženy v systému souborů kontejnerů. Bez trvalosti se při opětovném nasazení modulu všechna vytvořená metadata ztratí. V současné době jsou uložena pouze metadata. Události jsou uloženy v paměti. Pokud se modul Event Grid znovu nasazuje nebo restartuje, ztratí se všechny nedoručené události.

Tento článek popisuje postup nasazení modulu Event Grid s trvalým nasazením do systému Linux.

> [!NOTE]
>Modul Event Grid se spouští jako uživatel s nízkými oprávněními s `2000` UID a názvem `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Stálost prostřednictvím připojení svazku

 K zachování dat napříč nasazeními se používají [dokovací svazky](https://docs.docker.com/storage/volumes/) . Docker můžete nechat automaticky vytvořit pojmenovaný svazek jako součást nasazení modulu Event Grid. Tato možnost je nejjednodušší. Název svazku, který se má vytvořit, můžete zadat v oddílu **Binds** následujícím způsobem:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v rámci modulu. Pro modul Event Grid v systému Linux musí být **/App/metadataDb**.

Například následující konfigurace bude mít za následek vytvoření svazku **egmetadataDbVol** , ve kterém budou metadata trvalá.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Alternativně můžete vytvořit svazek Docker pomocí příkazů klienta Docker. 

## <a name="persistence-via-host-directory-mount"></a>Stálost prostřednictvím hostitelského adresářového připojení

Místo svazku Docker máte také možnost připojit složku hostitele.

1. Nejdřív na hostitelském počítači vytvořte uživatele s názvem **eventgriduser** a ID **2000** , a to spuštěním následujícího příkazu:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Spuštěním následujícího příkazu vytvořte adresář v systému souborů hostitele.

   ```sh
   md <your-directory-name-here>
   ```

    Například spuštění následujícího příkazu vytvoří adresář s názvem **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Dále vytvořte **eventgriduser** vlastníka této složky spuštěním následujícího příkazu.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Například:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Pomocí **vazeb** připojte adresář a znovu nasaďte modul Event Grid z Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Například:

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v rámci modulu. Pro modul Event Grid v systému Linux musí být **/App/metadata**.
