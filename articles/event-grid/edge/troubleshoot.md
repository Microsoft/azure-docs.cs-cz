---
title: Řešení potíží – Azure Event Grid IoT Edge | Microsoft Docs
description: Řešení potíží v Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0196522618d4b61f615f7cc6faeacbe9a8c7c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171342"
---
# <a name="common-issues"></a>Běžné problémy

Pokud dochází k potížím s používáním Azure Event Grid v IoT Edge ve vašem prostředí, použijte tento článek jako vodítko pro řešení potíží a řešení.

## <a name="view-event-grid-module-logs"></a>Zobrazit protokoly Event Grid modulu

Abyste mohli řešit potíže, možná budete potřebovat přístup k protokolům modulu Event Grid. Pokud to chcete provést, na virtuálním počítači, kde je modul nasazený, spusťte následující příkaz:

V systému Windows,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

V systému Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Nepovedlo se vytvořit požadavky HTTPS.

* Nejdřív se ujistěte, že Event Grid modul má **příchozí: serverAuth: tlsPolicy** je nastavené na **Strict** nebo **Enabled**.

* Pokud se jedná o komunikaci modulu k modulům, ujistěte se, že provádíte volání na portu **4438** a název modulu se shoduje s tím, co je nasazeno. 

  Pokud byl například modul Event Grid nasazen s názvem **eventgridmodule** , měla by být adresa URL **https://eventgridmodule:4438** . Ujistěte se, že velikost písmen a číslo portu jsou správné.
    
* Pokud se jedná o modul mimo IoT, ujistěte se, že Event Grid port je namapovaný na hostitelský počítač během nasazování, například

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

## <a name="unable-to-make-http-requests"></a>Nejde provést požadavky HTTP.

* Nejdřív se ujistěte, že Event Grid modul má **příchozí: serverAuth: tlsPolicy** nastavený na **Enabled** nebo **disabled**.

* Pokud se jedná o komunikaci modulu k modulům, ujistěte se, že provádíte volání na portu **5888** a název modulu se shoduje s tím, co je nasazeno. 

  Pokud byl například modul Event Grid nasazen s názvem **eventgridmodule** , měla by být adresa URL **http://eventgridmodule:5888** . Ujistěte se, že velikost písmen a číslo portu jsou správné.
    
* Pokud se jedná o modul mimo IoT, ujistěte se, že Event Grid port je namapovaný na hostitelský počítač během nasazování, například

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

Ve výchozím nastavení je Event Grid modul nakonfigurován pro ověřování klientů pomocí certifikátu vydaného démonem zabezpečení IoT Edge. Ujistěte se, že klient prezentuje certifikát, který je rootem tohoto řetězce.

Třída **IoTSecurity** v [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) ukazuje, jak načíst certifikáty z démona zabezpečení IoT Edge a použít je ke konfiguraci odchozích volání.

Pokud se jedná o jiné než produkční prostředí, máte možnost vypnout ověřování klientů. Podrobnosti o tom, jak to udělat, najdete v článku [zabezpečení a ověřování](security-authentication.md) .

## <a name="debug-events-not-received-by-subscriber"></a>Odběratel nepřijal události ladění.

Mezi obvyklé příčiny patří:

* Událost nebyla nikdy úspěšně odeslána. Při odesílání události do Event Grid modulu by se měla přijmout zpráva HTTP StatusCode 200 (OK).

* Zkontrolujte odběr události a ověřte:
    * Adresa URL koncového bodu je platná.
    * Jakékoli filtry v předplatném nezpůsobí, že událost bude vyřazena.

* Ověřte, jestli je spuštěný modul předplatitele.

* Přihlaste se k virtuálnímu počítači, kde je nasazený modul Event Grid a zobrazte jeho protokoly.

* Zapněte protokolování doručení podle nastavení **Broker: logDeliverySuccess = true** a znovu nasaďte Event Grid modul a opakujte požadavek. Zapnutí protokolování pro doručování může mít vliv na propustnost a latenci, takže po dokončení ladění se toto doporučení zapíná zpátky na **zprostředkovatele: logDeliverySuccess = false**  a znovu nasazuje Event Grid modul.

* Zapněte metriky nastavením **metrik: reportertype = konzola** a znovu nasaďte Event Grid modul. Všechny operace, které potom budou, budou mít za následek protokolování metrik v konzole modulu Event Grid, které lze použít k ladění. Naším doporučením je zapnout metriky jenom pro ladění a po dokončení jejich vypnutí tím, že nastavíte **metriky: reportertype = None** a znovu nasazujete Event Grid modul.

## <a name="next-steps"></a>Další kroky

Nahlaste všechny problémy, návrhy pomocí Event Grid v IoT Edge na [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .