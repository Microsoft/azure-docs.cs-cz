---
title: Poradce při potížích – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Řešení potíží v event gridu na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100162"
---
# <a name="common-issues"></a>Běžné problémy

Pokud máte problémy s používáním Azure Event Grid na IoT Edge ve vašem prostředí, použijte tento článek jako vodítko pro řešení potíží a řešení.

## <a name="view-event-grid-module-logs"></a>Zobrazit protokoly modulu Mřížka událostí

Chcete-li vyřešit potíže, může být nutné získat přístup k protokolům modulu Event Grid. Chcete-li to provést, na virtuálním počítači, kde je nasazen modul spustit následující příkaz:

Ve Windows

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Na Linuxu,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Nelze provádět požadavky HTTPS.

* Nejprve se ujistěte, že modul Event Grid má **příchozí:serverAuth:tlsPolicy** nastavený na **striktní** nebo **povolený**.

* Pokud jeho komunikace modul-modul, ujistěte se, že provádíte volání na portu **4438** a název modulu odpovídá co je nasazen. 

  Pokud byl například modul Event Grid nasazen s názvem **eventgridmodule,** měla by být **https://eventgridmodule:4438**vaše adresa URL . Ujistěte se, že kryt a číslo portu jsou správné.
    
* Pokud je z modulu jiného než IoT, ujistěte se, že port Event Grid je namapován na hostitelský počítač během nasazení,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Nelze provést požadavky HTTP.

* Nejprve zkontrolujte, zda má modul Event Grid **příchozí:serverAuth:tlsPolicy** nastavený na **povolený** nebo **zakázaný**.

* Pokud jeho komunikace modul-modul, ujistěte se, že provádíte volání na portu **5888** a název modulu odpovídá co je nasazen. 

  Pokud byl například modul Event Grid nasazen s názvem **eventgridmodule,** měla by být **http://eventgridmodule:5888**vaše adresa URL . Ujistěte se, že kryt a číslo portu jsou správné.
    
* Pokud je z modulu jiného než IoT, ujistěte se, že port Event Grid je namapován na hostitelský počítač během nasazení,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Řetěz certifikátů byl vydán autoritou, která není důvěryhodná.

Ve výchozím nastavení je modul Event Grid nakonfigurován pro ověřování klientů s certifikátem vydaným daemonem zabezpečení IoT Edge. Ujistěte se, že klient představuje certifikát, který je zakořeněndo tohoto řetězce.

Třída **IoTSecurity** v aplikaci [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) ukazuje, jak načíst certifikáty od demonu IoT Edge Security a použít ji ke konfiguraci odchozích volání.

Pokud se jedná o neprodukční prostředí, máte možnost vypnout ověřování klienta. Podrobnosti o tom, jak to provést, naleznete v části [Zabezpečení a ověřování.](security-authentication.md)

## <a name="debug-events-not-received-by-subscriber"></a>Ladicí události, které odběratel neobdržel

Typickými důvody jsou:

* Událost nebyla nikdy úspěšně zaúčtována. Http StatusCode 200 (OK) by měla být přijata při zaúčtování události do modulu Event Grid.

* Zkontrolujte odběr událostí a ověřte:
    * Adresa URL koncového bodu je platná.
    * Žádné filtry v předplatném nezpůsobují, že událost byla "vynechána".

* Ověřte, zda je spuštěn modul odběratele

* Přihlaste se k virtuálnímu počítači, kde se nasazuje modul Event Grid a zobrazte jeho protokoly.

* Zapněte protokolování doručení nastavením **broker:logDeliverySuccess=true** a opětovným nasazením modulu Event Grid a opakováním požadavku. Zapnutí protokolování na doručení může mít vliv na propustnost a latenci, takže po dokončení ladění je naše doporučení vrátit zpět do **broker:logDeliverySuccess=false** a opětovné nasazení modulu Event Grid.

* Zapněte metriky nastavením **metrik:reportertype=console** a znovu nasadit modul Event Grid. Všechny operace po tom bude mít za následek metriky jsou zaznamenány na konzoli modulu Event Grid, který lze použít k dalšímu ladění. Naše doporučení je zapnout metriky pouze pro ladění a po dokončení vypnout nastavením **metriky:reportertype=none** a opětovné nasazení modulu Event Grid.

## <a name="next-steps"></a>Další kroky

Nahlaste všechny problémy, návrhy pomocí [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)funkce Event Grid na IoT Edge na adrese .