---
title: Běžná rozhraní – Náhled ioT plug and play | Dokumenty společnosti Microsoft
description: Popis běžných rozhraní pro vývojáře IoT Plug and Play
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770491"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>IoT Plug and Play Preview běžná rozhraní

Očekává se, že všechna zařízení IoT Plug and Play implementují některá běžná rozhraní. Běžná rozhraní mají prospěch z řešení IoT, protože poskytují konzistentní funkce. [Certifikace](tutorial-build-device-certification.md) vyžaduje, aby vaše zařízení implementovalo několik běžných rozhraní. Můžete načíst společné definice rozhraní z úložiště veřejného modelu.

## <a name="summary-of-common-interfaces"></a>Shrnutí společných rozhraní

| Název | ID | Popis | Implementovaná sadou Azure IoT SDK | Musí být deklarována v modelu schopností. |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informace o modelu | urn:azureiot:ModelDiscovery:ModelInformation:1 | Pro zařízení deklarovat ID modelu schopností a rozhraní. Vyžadováno pro všechna zařízení IoT Plug and Play. | Ano | Ne |
| Informace o klientovi Digitální dvojče | urn:azureiot:Klient:SDKInformace:1 | Sada SDK klienta pro připojení zařízení k Azure. Vyžadováno pro [certifikaci](tutorial-build-device-certification.md) | Ano | Ne |
| Informace o zařízení | urn:azureiot:DeviceManagement:DeviceInformation:1 | Informace o hardwaru a operačním systému o zařízení. Vyžadováno pro [certifikaci](tutorial-build-device-certification.md) | Ne | Ano |
| Definice modelu | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Pro zařízení deklarovat úplnou definici pro jeho model schopností a rozhraní. Musí být implementována, když definice modelu nejsou hostovány v úložišti modelu. | Ne | Ano |
| Digital Twins | ur:azureiot:ModelDiscovery:DigitalTwin:1 | Pro vývojáře řešení načíst ID modelu schopností a ID rozhraní pro digitální dvojče. Toto rozhraní není deklarováno ani implementováno zařízením IoT Plug and Play. | Ne | Ne |

- Implementovaný Sadou Azure IoT SDK – ať už Azure IoT SDK implementuje funkce deklarované v rozhraních. Zařízení IoT Plug and Play, která používají sadku Azure IoT SDK, toto rozhraní nepotřebují implementovat.
- Musí být deklarována v modelu schopností - Pokud `"implements":` "ano", toto rozhraní musí být deklarováno v části modelu schopností zařízení pro toto zařízení IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Načtení definic rozhraní z veřejného úložiště

### <a name="cli"></a>CLI

Rozšíření Azure IoT pro rozhraní příkazového řádku Azure můžete použít k načtení společných rozhraní z úložiště veřejného modelu.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. K otevření palety příkazů použijte **kombinaci kláves Ctrl+Shift+P.**

1. Zadejte **Plug and Play** a pak vyberte příkaz **IoT Plug and Play: Open Model Repository** . Zvolte **Veřejné úložiště**. Úložiště veřejného modelu se otevře v kódu VS.

1. Do úložiště veřejného modelu zadejte název rozhraní do vyhledávacího pole.

1. Chcete-li vytvořit místní kopii rozhraní, vyberte ji ve výsledcích hledání a pak vyberte **stáhnout**.

## <a name="next-steps"></a>Další kroky

Nyní, když jste se dozvěděli o běžných rozhraních, zde jsou některé další zdroje:

- [Definiční jazyk digitálního dvojčete (DTDL)](https://aka.ms/DTDL)
- [Sada SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
