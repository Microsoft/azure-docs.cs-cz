---
title: Společná rozhraní – IoT technologie Plug and Play Preview | Microsoft Docs
description: Popis společných rozhraní pro vývojáře technologie Plug and Play IoT
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5773ec2b3ea88fa9a507b7c1b0b84bb7ea305a94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770491"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Společná rozhraní IoT technologie Plug and Play ve verzi Preview

U všech zařízení IoT technologie Plug and Play se očekává implementace některých běžných rozhraní. Společná rozhraní využívají řešení IoT, protože poskytují konzistentní funkce. [Certifikace](tutorial-build-device-certification.md) vyžaduje, aby vaše zařízení implementovalo několik společných rozhraní. Z úložiště veřejného modelu můžete načíst společné definice rozhraní.

## <a name="summary-of-common-interfaces"></a>Souhrn společných rozhraní

| Name | ID | Popis | Implementované sadou Azure IoT SDK | Musí se deklarovat v modelu schopností. |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informace o modelu | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Aby zařízení deklarovalo ID a rozhraní modelu schopností. Vyžaduje se pro všechna zařízení technologie Plug and Play IoT. | Ano | No |
| Informace o digitálních nevlákenných klientských SDK | urn: azureiot: Client: SDKInformation: 1 | Klientská sada SDK pro připojení zařízení k Azure Vyžadováno pro [certifikaci](tutorial-build-device-certification.md) | Ano | No |
| Informace o zařízení | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Informace o hardwaru a operačním systému pro zařízení. Vyžadováno pro [certifikaci](tutorial-build-device-certification.md) | No | Ano |
| Definice modelu | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Aby zařízení deklarovalo úplnou definici pro svůj model a rozhraní schopností. Musí být implementováno, pokud definice modelů nejsou hostovány v úložišti modelu. | No | Ano |
| Digital Twins | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Pro vývojáře řešení můžete načíst ID modelu schopností a ID rozhraní pro digitální vlákna. Toto rozhraní není deklarované nebo implementované zařízením IoT technologie Plug and Play. | Ne | Ne |

- Je implementováno sadou Azure IoT SDK – zda sada SDK služby Azure IoT implementuje funkce deklarované v rozhraních. Zařízení IoT technologie Plug and Play, která používají sadu Azure IoT SDK, nepotřebují implementovat toto rozhraní.
- Musí být deklarovaný v modelu schopností – Pokud ano, toto rozhraní musí být deklarované v rámci `"implements":` modelu schopností zařízení pro toto zařízení IoT technologie Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Načíst definice rozhraní z veřejného úložiště

### <a name="cli"></a>Rozhraní příkazového řádku

Pomocí rozšíření Azure IoT pro Azure CLI můžete načíst společná rozhraní z úložiště veřejného modelu.

```azurecli
az iot pnp interface show --interface {InterfaceID}
```

```azurecli
az iot pnp capability-model show --model {ModelID}
```

### <a name="vs-code"></a>VS Code

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a pak vyberte příkaz **IoT technologie Plug and Play: Open model úložiště** . Vyberte **veřejné úložiště**. Úložiště veřejného modelu se otevře v VS Code.

1. V úložišti veřejného modelu zadejte název rozhraní do vyhledávacího pole.

1. Pokud chcete vytvořit místní kopii rozhraní, vyberte ji ve výsledcích hledání a pak vyberte **Stáhnout**.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s běžnými rozhraními, je zde několik dalších prostředků:

- [DTDL (Digital redefinition Language) – jazyk](https://aka.ms/DTDL)
- [Sada SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
