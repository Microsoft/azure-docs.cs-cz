---
title: Trvalý stav v Linuxu – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Zachovat metadata v Linuxu
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086649"
---
# <a name="persist-state-in-linux"></a>Trvalý stav v Linuxu

Témata a odběry vytvořené v modulu Event Grid jsou ve výchozím nastavení uloženy v systému souborů kontejneru. Bez trvalost, pokud je modul znovu nasazen, všechna vytvořená metadata by byla ztracena. Chcete-li zachovat data v rámci nasazení a restartování, je třeba zachovat data mimo systém souborů kontejneru.

Ve výchozím nastavení jsou zachována pouze metadata a události jsou stále uloženy v paměti pro lepší výkon. Postupujte podle části trvalé události povolit trvalost událostí také.

Tento článek obsahuje postup nasazení modulu Event Grid s trvalostí v nasazení linuxu.

> [!NOTE]
>Modul Event Grid běží jako uživatel s nízkými `2000` oprávněními s UID a názvem `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Trvalost pomocí připojení hlasitosti

 [Svazky Dockeru](https://docs.docker.com/storage/volumes/) se používají k zachování dat napříč nasazeními. Docker můžete automaticky vytvořit pojmenovaný svazek jako součást nasazení modulu Event Grid. Tato možnost je nejjednodušší volbou. Název svazku, který má být vytvořen, můžete zadat v části **Vazby** následujícím způsobem:

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
>Neměňte druhou část hodnoty vazby. Ukazuje na konkrétní umístění v modulu. Pro modul Event Grid na Linuxu musí být **/app/metadataDb**.

Například následující konfigurace bude mít za následek vytvoření svazku **egmetadataDbVol,** kde budou metadata trvalé.

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
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
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

Namísto připojení svazku můžete vytvořit adresář v hostitelském systému a připojit tento adresář.

## <a name="persistence-via-host-directory-mount"></a>Trvalost prostřednictvím připojení adresáře hostitele

Namísto svazku dockeru máte také možnost připojit složky hostitele.

1. Nejprve vytvořte uživatele s názvem **eventgriduser** a ID **2000** na hostitelském počítači spuštěním následujícího příkazu:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Pomocí následujícího příkazu vytvořte v hostitelském systému souborů adresář.

   ```sh
   md <your-directory-name-here>
   ```

    Například spuštěnínásledujícího příkazu vytvoří adresář s názvem **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Dále vytvořte **vlastníka eventgriduser** této složky spuštěním následujícího příkazu.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Například:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Pomocí **vazeb** připojte adresář a znovu nasaďte modul Event Grid z webu Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

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
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
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
    >Neměňte druhou část hodnoty vazby. Ukazuje na konkrétní umístění v modulu. Pro modul Event Grid na Linuxu musí být **/app/metadataDb** a **/app/eventsDb**


## <a name="persist-events"></a>Zachovat události

Chcete-li povolit trvalost událostí, musíte nejprve povolit trvalost metadat buď prostřednictvím připojení svazku nebo připojení adresáře hostitele pomocí výše uvedených oddílů.

Důležité informace o trvalých událostech:

* Trvalé události je povolena na základě předplatného na událost a je opt-in po připojení svazku nebo adresáře.
* Trvalost událostí je nakonfigurována v odběr událostí v době vytvoření a nelze ji změnit po vytvoření odběru událostí. Chcete-li přepnout trvalost událostí, je nutné odstranit a znovu vytvořit odběr událostí.
* Trvalé události je téměř vždy pomalejší než v operacích paměti, ale rozdíl rychlosti je vysoce závislá na vlastnostech jednotky. Kompromis mezi rychlostí a spolehlivostí je vlastní všem systémům zasílání zpráv, ale obecně se stává znatelným pouze ve velkém měřítku.

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
