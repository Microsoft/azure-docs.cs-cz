---
title: Trvalý stav ve Windows-Azure Event Grid IoT Edge | Microsoft Docs
description: Trvalý stav ve Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002813"
---
# <a name="persist-state-in-windows"></a>Trvalý stav ve Windows

Témata a odběry vytvořené v modulu Event Grid jsou ve výchozím nastavení uloženy v systému souborů kontejnerů. Bez trvalosti se při opětovném nasazení modulu všechna vytvořená metadata ztratí. Chcete-li zachovat data napříč nasazeními a restarty, je nutné zachovat data mimo systém souborů kontejnerů. 

Ve výchozím nastavení jsou uložena pouze metadata a události jsou stále uloženy v paměti pro zvýšení výkonu. Dodržujte oddíl trvalé události a povolte taky trvalost událostí.

Tento článek popisuje kroky potřebné k nasazení Event Grid modulu s stálostí v nasazeních systému Windows.

> [!NOTE]
>Modul Event Grid se spouští jako **ContainerUser** uživatele s nízkou úrovní oprávnění ve Windows.

## <a name="persistence-via-volume-mount"></a>Stálost prostřednictvím připojení svazku

K zachování dat napříč nasazeními se používají [dokovací svazky](https://docs.docker.com/storage/volumes/) . Pokud chcete připojit svazek, musíte ho vytvořit pomocí příkazů Docker, udělit oprávnění, aby kontejner mohl číst, zapisovat do něj a pak nasadit modul.

1. Vytvořte svazek spuštěním následujícího příkazu:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Třeba

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Pomocí níže uvedeného příkazu Získejte adresář hostitele, ke kterému se svazek mapuje.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Třeba

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
    1. Vyberte **Zabezpečení**.
    1. V části * uživatelské jméno nebo název skupiny vyberte **Upravit**.
    1. Vyberte **Přidat**, zadejte `Users` , vyberte **název kontroly** a pak vyberte **OK**.
    1. V části *oprávnění pro uživatele* vyberte **změnit** a vyberte **OK**.
1. Pomocí **vazeb** připojte tento svazek a znovu nasaďte Event Grid modul z Azure Portal

   Třeba

    ```json
        {
              "Env": [
                "inbound__serverAuth__tlsPolicy=strict",
                "inbound__serverAuth__serverCert__source=IoTEdge",
                "inbound__clientAuth__sasKeys__enabled=false",
                "inbound__clientAuth__clientCert__enabled=true",
                "inbound__clientAuth__clientCert__source=IoTEdge",
                "inbound__clientAuth__clientCert__allowUnknownCA=true",
                "outbound__clientAuth__clientCert__enabled=true",
                "outbound__clientAuth__clientCert__source=IoTEdge",
                "outbound__webhook__httpsOnly=true",
                "outbound__webhook__skipServerCertValidation=false",
                "outbound__webhook__allowUnknownCA=true"
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
   >Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v modulu. Pro Event Grid modul ve Windows musí být to **C: \\ App \\ metadataDb**.


    Třeba

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

Místo připojení svazku můžete vytvořit adresář v hostitelském systému a připojit tento adresář.

1. Spuštěním následujícího příkazu vytvořte adresář v systému souborů hostitele.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Třeba

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
    >Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v modulu. Pro modul Event Grid ve Windows musí být to **C: \\ App \\ metadataDb**.

    Třeba

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Zachovat události

Chcete-li povolit trvalosti událostí, je nutné nejprve povolit trvalá událost buď prostřednictvím připojení svazku, nebo pomocí připojovacího adresáře hostitele v předchozích částech.

Důležité informace o trvalých událostech:

* Trvalé události jsou povolené pro jednotlivé odběry událostí a po připojení svazku nebo adresáře se odhlásí.
* Trvalá událost je nakonfigurovaná pro odběr události během vytváření a nedá se změnit po vytvoření odběru události. Chcete-li přepnout trvalost událostí, je nutné odstranit a znovu vytvořit odběr události.
* Trvalé události jsou téměř vždy pomalejší než při operacích paměti, ale rozdíl rychlosti je vysoce závislý na charakteristikách jednotky. Kompromisy mezi rychlostí a spolehlivostí jsou podstatné pro všechny systémy zasílání zpráv, ale ve velkém měřítku se jenom nejedná o znatelné.

Pro povolení trvalosti události v odběru události nastavte `persistencePolicy` na `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```