---
title: Trvalý stav ve Windows – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Trvalý stav v systému Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086626"
---
# <a name="persist-state-in-windows"></a>Trvalý stav v systému Windows

Témata a odběry vytvořené v modulu Event Grid jsou ve výchozím nastavení uloženy v systému souborů kontejneru. Bez trvalost, pokud je modul znovu nasazen, všechna vytvořená metadata by byla ztracena. Chcete-li zachovat data v rámci nasazení a restartování, je třeba zachovat data mimo systém souborů kontejneru. 

Ve výchozím nastavení jsou zachována pouze metadata a události jsou stále uloženy v paměti pro lepší výkon. Postupujte podle části trvalé události povolit trvalost událostí také.

Tento článek obsahuje kroky potřebné k nasazení modulu Event Grid s trvalostí v nasazení systému Windows.

> [!NOTE]
>Modul Event Grid běží jako uživatel s nízkými oprávněními **ContainerUser** v systému Windows.

## <a name="persistence-via-volume-mount"></a>Trvalost pomocí připojení hlasitosti

[Svazky Dockeru](https://docs.docker.com/storage/volumes/) se používají k zachování dat napříč nasazeními. Chcete-li připojit svazek, musíte jej vytvořit pomocí příkazů dockeru, udělit oprávnění, aby kontejner mohl číst, zapisovat do něj a pak nasadit modul.

1. Vytvořte svazek spuštěním následujícího příkazu:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Například:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Získání adresáře hostitele, do kterého se svazek mapuje, spuštěním příkazu níže

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Například:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Ukázkový výstup:-

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
1. Přidejte skupinu **Users** k hodnotě, na kterou upozornil **Mountpoint** takto:
    1. Spusťte Průzkumníka souborů.
    1. Přejděte do složky nasazené **odkazem Mountpoint**.
    1. Klepněte pravým tlačítkem myši a vyberte příkaz **Vlastnosti**.
    1. Vyberte **Zabezpečení**.
    1. V části *Skupinová nebo uživatelská jména vyberte **Upravit**.
    1. Vyberte **Přidat**, zadejte `Users`, vyberte **Zkontrolovat názvy**a vyberte **Ok**.
    1. V části *Oprávnění pro uživatele*vyberte **Změnit**a vyberte **Ok**.
1. Připojení tohoto **svazku** a opětovné nasazení modulu Event Grid z portálu Azure Portal pomocí vazeb pomocí vazeb

   Například:

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
   >Neměňte druhou část hodnoty vazby. Ukazuje na konkrétní umístění v modulu. Pro modul Event Grid v oknech musí být **C:\\\\metadatadb aplikace**.


    Například:

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

## <a name="persistence-via-host-directory-mount"></a>Trvalost prostřednictvím připojení adresáře hostitele

Namísto připojení svazku můžete vytvořit adresář v hostitelském systému a připojit tento adresář.

1. Vytvořte adresář v hostitelském souborovém systému spuštěním následujícího příkazu.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Například:

   ```sh
   mkdir C:\myhostdir
   ```
1. Pomocí **vazeb** připojte adresář a znovu nasaďte modul Event Grid z webu Azure Portal.

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
    >Neměňte druhou část hodnoty vazby. Ukazuje na konkrétní umístění v modulu. Pro modul Event Grid v oknech musí být **C:\\\\metadataDb aplikace**.

    Například:

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

Chcete-li povolit trvalost událostí, musíte nejprve povolit trvalost událostí pomocí připojení svazku nebo připojení k adresáři hostitele pomocí výše uvedených oddílů.

Důležité informace o trvalých událostech:

* Trvalé události je povolena na základě předplatného na událost a je opt-in po připojení svazku nebo adresáře.
* Trvalost událostí je nakonfigurována v odběr událostí v době vytvoření a nelze ji změnit po vytvoření odběru událostí. Chcete-li přepnout trvalost událostí, je nutné odstranit a znovu vytvořit odběr událostí.
* Trvalé události je téměř vždy pomalejší než v operacích paměti, ale rozdíl rychlosti je vysoce závislá na vlastnostech jednotky. Kompromis mezi rychlostí a spolehlivostí je vlastní všem systémům zasílání zpráv, ale pouze se stává znatelným ve velkém měřítku.

Chcete-li povolit trvalost událostí `persistencePolicy` `true`u odběru událostí, nastavte na :

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