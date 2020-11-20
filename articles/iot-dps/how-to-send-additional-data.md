---
title: Jak přenést datovou část mezi zařízením a službou Azure Device Provisioning
description: Tento dokument popisuje, jak přenést datovou část mezi zařízením a službou Device Provisioning (DPS).
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d94bc2fde63090d66da7e98cc239386d958e6bb2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950960"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Jak přenést datovou část mezi zařízením a DPS
V některých případech potřebuje služba DPS větší data ze zařízení, aby je správně zřídila do správného IoT Hub a aby je mohl zařízení poskytnout. A naopak, DPS může vrátit data do zařízení, aby se usnadnily logiky na straně klienta. 

## <a name="when-to-use-it"></a>Kdy ji použít
Tato funkce se dá použít jako vylepšení pro [vlastní přidělení](./how-to-use-custom-allocation-policies.md). Chcete například přidělit zařízení na základě modelu zařízení bez zásahu člověka. V takovém případě budete používat [vlastní přidělení](./how-to-use-custom-allocation-policies.md). Zařízení můžete nakonfigurovat tak, aby v rámci [volání registrace zařízení](/rest/api/iot-dps/runtimeregistration/registerdevice)nahlásilo informace o modelu. DPS bude datovou část zařízení předat do vlastního Webhooku přidělení. A funkce může rozhodnout, na které IoT Hub bude toto zařízení po přijetí informací o modelu zařízení přejít. Podobně pokud Webhook chce vrátit nějaká data do zařízení, přepošle data zpátky jako řetězec v odpovědi Webhooku.  

## <a name="device-sends-data-payload-to-dps"></a>Zařízení odesílá datovou datovou část do DPS.
Když zařízení posílá do DPS [volání registrace zařízení](/rest/api/iot-dps/runtimeregistration/registerdevice) , může být volání registrace vylepšené, aby se v těle převzaly další pole. Tělo vypadá takto: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS vrátí data na zařízení.
Pokud si Webhook zásady přidělení přeje vrátit některá data do zařízení, předají data zpátky jako řetězec v odpovědi Webhooku. Tato změna je uvedená v části datová část níže. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Podpora SDK
Tato funkce je k dispozici v [klientských sadách SDK](./index.yml)C, C#, JAVA a Node.js.  

## <a name="next-steps"></a>Další kroky
* Vývoj s využitím [sady Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Azure IoT Hub Device Provisioning Service