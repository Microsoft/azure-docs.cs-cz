---
title: Společná rozhraní – IoT technologie Plug and Play Preview | Microsoft Docs
description: Popis společných rozhraní pro vývojáře technologie Plug and Play IoT
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 848210509bf9ab0ffec35004cbb07e39d6de1bc0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879601"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Společná rozhraní IoT technologie Plug and Play ve verzi Preview

U všech zařízení IoT technologie Plug and Play se očekává implementace některých běžných rozhraní. Společná rozhraní využívají řešení IoT, protože poskytují konzistentní funkce. [Certifikace](tutorial-build-device-certification.md) vyžaduje, aby vaše zařízení implementovalo několik společných rozhraní. Můžete načíst společné definice rozhraní z úložiště globálního modelu.

## <a name="summary-of-common-interfaces"></a>Souhrn společných rozhraní

| Name | id | Popis | Implementované sadou Azure IoT SDK | Musí se deklarovat v modelu schopností. |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Informace o modelu | urn: azureiot: ModelDiscovery: ModelInformation: 1 | Aby zařízení deklarovalo ID a rozhraní modelu schopností. Vyžaduje se pro všechna zařízení technologie Plug and Play IoT. | Ano | Ne |
| Informace o digitálních nevlákenných klientských SDK | urn: azureiot: Client: SDKInformation: 1 | Klientská sada SDK pro připojení zařízení k Azure Vyžadováno pro [certifikaci](tutorial-build-device-certification.md) | Ano | Ne |
| Informace o zařízení | urn: azureiot: DeviceManagement: DeviceInformation: 1 | Informace o hardwaru a operačním systému pro zařízení. Vyžadováno pro [certifikaci](tutorial-build-device-certification.md) | Ne | Ano |
| Definice modelu | urn: azureiot: ModelDiscovery: ModelDefinition: 1 | Aby zařízení deklarovalo úplnou definici pro svůj model a rozhraní schopností. Musí být implementováno, pokud definice modelů nejsou hostovány v úložišti modelu. | Ne | Ano |
| Digitální vlákna | urn: azureiot: ModelDiscovery: DigitalTwin: 1 | Pro vývojáře řešení můžete načíst ID modelu schopností a ID rozhraní pro digitální vlákna. Toto rozhraní není deklarované nebo implementované zařízením IoT technologie Plug and Play. | Ne | Ne |

- Je implementováno sadou Azure IoT SDK – zda sada SDK služby Azure IoT implementuje funkce deklarované v rozhraních. Zařízení IoT technologie Plug and Play, která používají sadu Azure IoT SDK, nepotřebují implementovat toto rozhraní.
- Musí být deklarovaný v modelu schopností – Pokud ano, toto rozhraní musí být deklarované v rámci `"implements":` modelu schopností zařízení pro toto zařízení IoT technologie Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Načíst definice rozhraní z veřejného úložiště

### <a name="cli"></a>Rozhraní příkazového řádku

Pomocí rozšíření Azure IoT pro Azure CLI můžete načíst společná rozhraní z globálního úložiště modelu.

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>VS Code

1. Stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů.

1. Zadejte **technologie Plug and Play** a pak vyberte **technologie Plug and Play IoT: Otevřete příkaz úložiště** modelu. Vyberte **úložiště globálních modelů**. Globální úložiště modelu se otevře v VS Code.

1. V úložišti globálních modelů zadejte název rozhraní do vyhledávacího pole.

1. Pokud chcete vytvořit místní kopii rozhraní, vyberte ji ve výsledcích hledání a pak vyberte **Stáhnout**.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s běžnými rozhraními, je zde několik dalších prostředků:

- [DTDL (Digital redefinition Language) – jazyk](https://aka.ms/DTDL)
- [Sady SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
