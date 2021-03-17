---
title: Konfigurace modulu API proxy-Azure IoT Edge | Microsoft Docs
description: Přečtěte si, jak přizpůsobit modul proxy serveru rozhraní API pro IoT Edge hierarchie brány.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1070a4c8daecfedae513f2fd8738c27abfb33078
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200587"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Konfigurace modulu API proxy pro scénář hierarchie brány (Preview)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Modul proxy serveru API umožňuje IoT Edge zařízením odesílat požadavky HTTP prostřednictvím bran namísto přímého připojení ke cloudovým službám. Tento článek vás provede možnostmi konfigurace, abyste mohli upravit modul tak, aby podporoval požadavky na hierarchii vaší brány.

>[!NOTE]
>Tato funkce vyžaduje IoT Edge verze 1,2, která je ve verzi Public Preview, spouští se kontejnery Linux.

V některých síťových architekturách IoT Edge zařízení za brány nemají přímý přístup ke cloudu. Všechny moduly, které se pokoušejí připojit ke cloudovým službám, se nezdaří. Modul API proxy podporuje v této konfiguraci IoT Edgeá zařízení pomocí opětovného směrování připojení modulů, aby procházel přes vrstvy hierarchie brány místo toho. Poskytuje klientům zabezpečený způsob, jak komunikovat s více službami přes protokol HTTPS bez tunelového propojení, ale ukončením připojení v jednotlivých vrstvách. Modul API proxy funguje jako modul proxy mezi zařízeními IoT Edge v hierarchii brány, dokud nedosáhnou IoT Edge zařízení v horní vrstvě. V tomto okamžiku služby běžící na nejvyšší vrstvě IoT Edge zařízení zpracovává tyto požadavky a proxy modul rozhraní API proxy všechny přenosy protokolu HTTP z místních služeb do cloudu prostřednictvím jediného portu.

Modul proxy serveru rozhraní API může povolit mnoho scénářů pro hierarchie brány, včetně povolení nižších vrstev, aby vyčetly image kontejneru nebo push blobů do úložiště. Konfigurace pravidel proxy definuje způsob, jakým jsou směrována data. Tento článek popisuje několik běžných možností konfigurace.

## <a name="deploy-the-proxy-module"></a>Nasazení modulu proxy

Modul proxy serveru rozhraní API je k dispozici na webu Microsoft Container Registry (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:latest` .

Modul API proxy můžete nasadit taky přímo z Azure Marketplace: [IoT Edge API proxy serveru](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Pochopení modulu proxy

Modul proxy rozhraní API využívá reverzní proxy server Nginx ke směrování dat prostřednictvím síťových vrstev. V modulu je vložený proxy server, což znamená, že bitová kopie modulu musí podporovat konfiguraci proxy serveru. Pokud třeba proxy naslouchá na určitém portu, musí tento port být otevřený.

Proxy server se spustí s výchozím konfiguračním souborem vloženým v modulu. Do modulu můžete předat novou konfiguraci z cloudu pomocí jejího [nevlákenního modulu](../iot-hub/iot-hub-devguide-module-twins.md). Kromě toho můžete použít proměnné prostředí k zapnutí nebo vypnutí nastavení konfigurace v době nasazení.

Tento článek se zaměřuje nejprve na výchozí konfigurační soubor a použití proměnných prostředí k povolení jeho nastavení. Pak se podíváme na přizpůsobení konfiguračního souboru na konci.

### <a name="default-configuration"></a>Výchozí konfigurace

Modul proxy serveru rozhraní API se dodává s výchozí konfigurací, která podporuje běžné scénáře a umožňuje přizpůsobení. Můžete ovládat výchozí konfiguraci pomocí proměnných prostředí modulu.

V současné době výchozí proměnné prostředí zahrnují:

| Proměnná prostředí | Description |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Zobrazí seznam všech proměnných, které chcete aktualizovat v seznamu odděleném čárkami. Tento krok zabrání nechtěné změně špatného nastavení konfigurace.
| `NGINX_DEFAULT_PORT` | Změní port, na který Nginx proxy naslouchá. Pokud tuto proměnnou prostředí aktualizujete, ujistěte se, že port, který jste vybrali, je také zveřejněn v modulu souboru Dockerfile a deklarovaný jako vazba portu v manifestu nasazení.<br><br>Výchozí hodnota je 443.<br><br>Při nasazení z Azure Marketplace se výchozí port aktualizuje na 8000, aby nedocházelo ke konfliktům s modulem edgeHub. Další informace najdete v tématu [minimalizace otevřených portů](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Adresa pro směrování požadavků Docker. Upravte tuto proměnnou na zařízení nejvyšší vrstvy tak, aby odkazovala na modul registru.<br><br>Výchozí hodnota je nadřazený název hostitele. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Adresa pro směrování požadavků na registr objektů BLOB. Upravte tuto proměnnou na zařízení nejvyšší vrstvy tak, aby odkazovala na modul BLOB Storage.<br><br>Výchozí hodnota je nadřazený název hostitele. |

## <a name="minimize-open-ports"></a>Minimalizovat otevřené porty

K minimalizaci počtu otevřených portů by měl modul proxy serveru rozhraní API přenášet veškerý provoz HTTPS (port 443), včetně provozu zaměřeného na modul edgeHub. Modul proxy serveru rozhraní API je ve výchozím nastavení nakonfigurovaný tak, aby se všechny přenosy edgeHub na portu 443 znovu směrovaly.

Pomocí následujících kroků můžete nakonfigurovat nasazení, aby se minimalizovaly otevřené porty:

1. Aktualizujte nastavení modulu edgeHub tak, aby nevytvářela vazby na portu 443, jinak budou konflikty vazeb portů. Ve výchozím nastavení se modul edgeHub váže na porty 443, 5671 a 8883. Odstraňte vazbu portu 443 a ponechte ostatní dva na svém místě:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Nakonfigurujte modul API proxy tak, aby se navázal na portu 443.

   1. Nastavte hodnotu proměnné prostředí **NGINX_DEFAULT_PORT** na `443` .
   1. Aktualizujte kontejner vytvořením možností vytvoření vazby na portu 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Pokud nepotřebujete minimalizovat otevřené porty, můžete nechat modul edgeHub použít port 443 a nakonfigurovat modul API proxy tak, aby naslouchal na jiném portu. Modul API proxy může například naslouchat na portu 8000 nastavením hodnoty proměnné prostředí **NGINX_DEFAULT_PORT** na `8000` a vytvořením vazby portu pro port 8000.

## <a name="enable-container-image-download"></a>Povolit stažení Image kontejneru

Běžným případem použití modulu API proxy je povolit IoT Edge zařízení v nižších vrstvách pro vyžádání imagí kontejneru. Tento scénář používá [modul Docker Registry](https://hub.docker.com/_/registry) k načtení imagí kontejneru z cloudu a jejich ukládání do mezipaměti v horní vrstvě. Proxy rozhraní API přenáší všechny požadavky HTTPS na stažení Image kontejneru z dolních vrstev, které se budou obsluhovat modulem registru v horní vrstvě.

Tento scénář vyžaduje, aby se pro všechna ta IoT Edge zařízení odkazovala na název domény `$upstream` následovaný číslem portu proxy modulu rozhraní API namísto registru kontejneru v imagi. Příklad: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Tento případ použití je znázorněn v kurzu [vytvoření hierarchie IoT Edge zařízení pomocí bran](tutorial-nested-iot-edge.md).

V **horní vrstvě** nakonfigurujte následující moduly:

* Modul registru Docker
  * Nakonfigurujte modul se zapamatovatm názvem, jako je *registr* , a vystavte port v modulu pro příjem požadavků.
  * Nakonfigurujte modul tak, aby se namapoval do vašeho registru kontejneru.
* Modul proxy serveru rozhraní API
  * Nakonfigurujte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Název modulu registru a otevřený port. Například, `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Port, na kterém proxy server Nginx naslouchá žádostem ze zařízení pro příjem dat. Například, `8000`. |

  * Nakonfigurujte následující createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Nakonfigurujte následující modul na všech **nižších vrstvách** pro tento scénář:

* Modul proxy serveru rozhraní API
  * Nakonfigurujte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Port, na kterém proxy server Nginx naslouchá žádostem ze zařízení pro příjem dat. Například, `8000`. |

  * Nakonfigurujte následující createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Povolit nahrání objektu BLOB

Dalším případem použití modulu API proxy je povolit IoT Edge zařízením v nižších vrstvách pro nahrávání objektů BLOB. Tento případ použití umožňuje funkci řešení potíží na nižších zařízeních vrstev, jako je nahrávání protokolů modulu nebo nahrávání sady prostředků podpory.

V tomto scénáři se používá [BLOB Storage Azure v modulu IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) na nejvyšší vrstvě ke zpracování vytváření a nahrávání objektů BLOB.

V **horní vrstvě** nakonfigurujte následující moduly:

* Blob Storage Azure v modulu IoT Edge.
* Modul proxy serveru rozhraní API
  * Nakonfigurujte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Název modulu BLOB Storage a otevřený port. Například, `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Port, na kterém proxy server Nginx naslouchá žádostem ze zařízení pro příjem dat. Například, `8000`. |

  * Nakonfigurujte následující createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Nakonfigurujte následující modul na všech **nižších vrstvách** pro tento scénář:

* Modul proxy serveru rozhraní API
  * Nakonfigurujte následující proměnné prostředí:

    | Name | Hodnota |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Port, na kterém proxy server Nginx naslouchá žádostem ze zařízení pro příjem dat. Například, `8000`. |

  * Nakonfigurujte následující createOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Pomocí následujících kroků nahrajte sadu prostředků pro podporu nebo soubor protokolu do modulu BLOB Storage, který se nachází v horní vrstvě:

1. Vytvořte kontejner objektů BLOB pomocí Průzkumník služby Azure Storage nebo rozhraní REST API. Další informace najdete v tématu [uložení dat na hraničních zařízeních pomocí Azure Blob Storage v IoT Edge](how-to-store-data-blob.md).
1. Požádejte o nahrání sady protokolů nebo podpory podle kroků v části [načtení protokolů z IoT Edge nasazení](how-to-retrieve-iot-edge-logs.md), ale `$upstream` místo adresy modulu BLOB Storage použijte název domény a otevřený port proxy serveru. Například:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Úprava konfigurace proxy serveru

Výchozí konfigurační soubor je vložený v modulu API proxy, ale do modulu můžete předat novou konfiguraci přes Cloud pomocí modulu s dvojitou úspěšností.

Při psaní vlastní konfigurace můžete prostředí použít k úpravám nastavení pro jednotlivé nasazení. Použijte následující syntaxi:

* Použijte `${MY_ENVIRONMENT_VARIABLE}` k načtení hodnoty proměnné prostředí.
* Použití podmíněných příkazů k zapnutí nebo vypnutí nastavení na základě hodnoty proměnné prostředí:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Když modul proxy serveru rozhraní API analyzuje konfiguraci proxy serveru, nejprve nahradí všechny proměnné prostředí uvedené v `PROXY_CONFIG_ENV_VAR_LIST` s jejich poskytnutými hodnotami pomocí substituce. Pak se nahradí vše mezi `#if_tag` `#endif_tag` pár a. Modul pak poskytne analyzovanou konfiguraci Nginx reverzní proxy.

Chcete-li aktualizovat konfiguraci proxy dynamicky, použijte následující postup:

1. Napište konfigurační soubor. Tuto výchozí šablonu můžete použít jako referenci: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Zkopírujte text konfiguračního souboru a převeďte ho na base64.
1. Vložte kódovaný konfigurační soubor jako hodnotu `proxy_config` požadované vlastnosti v modulu s nevlákenou.

   ![Vložit kódovaný konfigurační soubor jako hodnotu vlastnosti proxy_config](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Další kroky

Pomocí modulu API proxy můžete [připojit IoT Edge zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md).