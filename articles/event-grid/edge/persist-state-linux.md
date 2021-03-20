---
title: Trvalý stav v systému Linux-Azure Event Grid IoT Edge | Microsoft Docs
description: Zachovat metadata v systému Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171478"
---
# <a name="persist-state-in-linux"></a>Trvalý stav v systému Linux

Témata a odběry vytvořené v modulu Event Grid jsou ve výchozím nastavení uloženy v systému souborů kontejnerů. Bez trvalosti se při opětovném nasazení modulu všechna vytvořená metadata ztratí. Chcete-li zachovat data napříč nasazeními a restarty, je nutné zachovat data mimo systém souborů kontejnerů.

Ve výchozím nastavení jsou uložena pouze metadata a události jsou stále uloženy v paměti pro zvýšení výkonu. Dodržujte oddíl trvalé události a povolte taky trvalost událostí.

Tento článek popisuje postup nasazení modulu Event Grid s trvalým nasazením do systému Linux.

> [!NOTE]
>Modul Event Grid se spouští jako uživatel s nízkými oprávněními s identifikátorem UID `2000` a názvem `eventgriduser` .

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

Místo připojení svazku můžete vytvořit adresář v hostitelském systému a připojit tento adresář.

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

    Třeba

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Pomocí **vazeb** připojte adresář a znovu nasaďte modul Event Grid z Azure Portal.

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
    >Neměňte druhou část hodnoty vazby. Odkazuje na konkrétní umístění v rámci modulu. Pro modul Event Grid v systému Linux musí být **/App/metadataDb** a **/App/eventsDb** .


## <a name="persist-events"></a>Zachovat události

Chcete-li povolit trvalosti událostí, je nutné nejprve povolit trvalá metadata buď prostřednictvím připojení svazku, nebo připojení ke službě Host Directory pomocí výše uvedených částí.

Důležité informace o trvalých událostech:

* Trvalé události jsou povolené pro jednotlivé odběry událostí a po připojení svazku nebo adresáře se odhlásí.
* Trvalá událost je nakonfigurovaná pro odběr události během vytváření a nedá se změnit po vytvoření odběru události. Chcete-li přepnout trvalost událostí, je nutné odstranit a znovu vytvořit odběr události.
* Trvalé události jsou téměř vždy pomalejší než při operacích paměti, ale rozdíl rychlosti je vysoce závislý na charakteristikách jednotky. Kompromisy mezi rychlostí a spolehlivostí jsou podstatou pro všechny systémy zasílání zpráv, ale obecně se ve velkém měřítku stávají pouze znatelné.

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
