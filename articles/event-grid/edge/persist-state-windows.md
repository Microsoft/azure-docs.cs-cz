---
title: Trvalý stav ve Windows-Azure Event Grid IoT Edge | Microsoft Docs
description: Trvalý stav ve Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992259"
---
# <a name="persist-state-in-windows"></a>Trvalý stav ve Windows

Témata a odběry vytvořené v modulu Event Grid jsou ve výchozím nastavení uloženy v systému souborů kontejnerů. Bez trvalosti se při opětovném nasazení modulu všechna vytvořená metadata ztratí. Chcete-li zachovat data napříč nasazeními, budete muset zachovat data mimo systém souborů kontejnerů. V současné době jsou trvalá jenom metadata. Události jsou uloženy v paměti. Pokud se modul Event Grid znovu nasazuje nebo restartuje, ztratí se všechny nedoručené události.

Tento článek popisuje kroky potřebné k nasazení Event Grid modulu s stálostí v nasazeních systému Windows.

> [!NOTE]
>Modul Event Grid se spouští jako **ContainerUser** uživatele s nízkou úrovní oprávnění ve Windows.

## <a name="persistence-via-volume-mount"></a>Stálost prostřednictvím připojení svazku

K zachování dat napříč nasazeními se používají [dokovací svazky](https://docs.docker.com/storage/volumes/) . Pokud chcete připojit svazek, musíte ho vytvořit pomocí příkazů Docker, udělit oprávnění, aby kontejner mohl číst, zapisovat do něj a pak nasadit modul. Neexistuje žádné zřízení pro automatické vytvoření svazku s potřebnými oprávněními ve Windows. Je nutné vytvořit před nasazením.

1. Vytvořte svazek spuštěním následujícího příkazu:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Například:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Pomocí níže uvedeného příkazu Získejte adresář hostitele, ke kterému se svazek mapuje.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Například:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Vzorový výstup:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Přidejte skupinu **uživatelů** do hodnoty, na kterou odkazuje **přípojný bod** , následovně:
    1. Spusťte Průzkumníka souborů.
    1. Přejděte do složky, na kterou odkazuje **přípojný bod**.
    1. Klikněte pravým tlačítkem a pak vyberte **vlastnosti**.
    1. Vyberte **zabezpečení**.
    1. V části * uživatelské jméno nebo název skupiny vyberte **Upravit**.
    1. Vyberte **Přidat**, zadejte `Users`, vyberte **název kontroly**a pak vyberte **OK**.
    1. V části *oprávnění pro uživatele*vyberte **změnit**a vyberte **OK**.
1. Pomocí **vazeb** připojte tento svazek a znovu nasaďte Event Grid modul z Azure Portal

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v modulu. Pro Event Grid modul ve Windows musí být to **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Stálost prostřednictvím hostitelského adresářového připojení

Alternativně můžete zvolit vytvoření adresáře v hostitelském systému a připojit tento adresář.

1. Spuštěním následujícího příkazu vytvořte adresář v systému souborů hostitele.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Například:

   ```sh
   mkdir C:\myhostdir
   ```
1. Pomocí **vazeb** Připojte svůj adresář a znovu nasaďte modul Event Grid z Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v modulu. Pro modul Event Grid ve Windows musí být to **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
