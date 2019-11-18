---
title: Jak přenést další data mezi zařízením a službou Azure Device Provisioning
description: Tento dokument popisuje, jak přenést další data mezi zařízením a DPS.
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 90402dd6fbe19811b5bb6d5ac0fbdd984b71fd33
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123184"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Jak přenést další data mezi zařízením a DPS
Někdy DPS potřebuje ze zařízení další data, aby je mohla odpovídajícím způsobem zřídit ve vhodném IoT Hubu, a tato data musí poskytnout příslušné zařízení. A naopak, DPS může vrátit data do zařízení, aby se usnadnily logiky na straně klienta. 

## <a name="when-to-use-it"></a>Kdy ji použít
Tato funkce se dá použít jako vylepšení pro [vlastní přidělení](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Chcete například přidělit zařízení na základě modelu zařízení bez zásahu člověka. V takovém případě budete používat [vlastní přidělení](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Zařízení můžete nakonfigurovat tak, aby v rámci [volání registrace zařízení](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)nahlásilo informace o modelu. DPS předáte informace zařízení do vlastního Webhooku přidělení. A funkce může rozhodnout, na které IoT Hub bude toto zařízení po přijetí informací o modelu zařízení přejít. Podobně platí, že pokud webhook chce vrátit zařízení nějaká data, předá je jako řetězec v rámci své odpovědi.  

## <a name="device-sends-data-to-dps"></a>Zařízení odesílá data do DPS.
Když zařízení posílá do DPS [volání registrace zařízení](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) , může být volání registrace vylepšené, aby se v těle převzaly další pole. Tělo vypadá takto: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS vrátí data na zařízení.
Pokud si Webhook zásady přidělení přeje vrátit některá data do zařízení, předají data zpátky jako řetězec v odpovědi Webhooku. Tato změna je uvedená v části returnData níže. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Podpora sady SDK
Tato funkce je k dispozici v C#sadách [SDK klienta](https://docs.microsoft.com/azure/iot-dps/)C,, Java a Node. js.  

## <a name="next-steps"></a>Další kroky
* Vývoj s využitím [sady Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) pro Azure IoT Hub a Azure IoT Hub Device Provisioning Service
