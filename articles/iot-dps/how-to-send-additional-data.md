---
title: Přenos datové části mezi zařízením a službou Azure Device Provisioning Service
description: Tento dokument popisuje, jak přenést datovou část mezi zařízením a službou Device Provisioning Service (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246683"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Jak přenést užitečné zatížení mezi zařízením a DPS
Někdy DPS potřebuje více dat ze zařízení, aby je správně zřizovat na pravé IoT Hub a že data musí být poskytnuta zařízením. Naopak DPS můžete vrátit data do zařízení pro usnadnění logiky na straně klienta. 

## <a name="when-to-use-it"></a>Kdy ji použít
Tuto funkci lze použít jako vylepšení pro [vlastní přidělení](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Například chcete přidělit zařízení na základě modelu zařízení bez lidského zásahu. V takovém případě použijete [vlastní přidělení](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Zařízení můžete nakonfigurovat tak, aby hlásilo informace o modelu jako součást [volání registračního zařízení](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS předá datové části zařízení do vlastního přidělení webhooku. A vaše funkce může rozhodnout, které IoT Hub toto zařízení přejde, když obdrží informace o modelu zařízení. Podobně pokud webhook chce vrátit některá data do zařízení, předá data zpět jako řetězec v odpovědi webhooku.  

## <a name="device-sends-data-payload-to-dps"></a>Zařízení odesílá datová část do DPS
Když vaše zařízení odesílá [registrační volání zařízení](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) dps, volání registru lze rozšířit, aby se další pole v těle. Tělo vypadá takto: 
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

## <a name="dps-returns-data-to-the-device"></a>DPS vrací data do zařízení
Pokud vlastní zásady přidělení webhook chce vrátit některá data do zařízení, předá data zpět jako řetězec v webhooku odpověď. Změna je v části datové části níže. 
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
Tato funkce je k dispozici v [sadách SDK klienta](https://docs.microsoft.com/azure/iot-dps/)C, C#, JAVA a Node.js .  

## <a name="next-steps"></a>Další kroky
* Vývoj pomocí [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Službu zřizování zařízení Azure IoT Hub
