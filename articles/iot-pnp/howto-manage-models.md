---
title: Správa modelů IoT technologie Plug and Play ve verzi Preview v úložišti | Microsoft Docs
description: Jak spravovat modely schopností zařízení v úložišti pomocí portálu Azure Certified for IoT, Azure CLI a Visual Studio Code.
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: ec36bef9c2b65a5c88a52621bbf3a1db708c235c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880498"
---
# <a name="manage-models-in-the-repository"></a>Správa modelů v úložišti

Úložiště modelu IoT technologie Plug and Play Preview ukládá modely a rozhraní funkcí zařízení. Úložiště umožňuje vývojářům řešení najít modely a rozhraní a zjistitelné a spotřební.

Existují tři nástroje, které můžete použít ke správě úložiště:

- Portál Azure Certified for IoT
- Rozhraní příkazového řádku Azure
- Visual Studio Code

## <a name="model-repositories"></a>Úložiště modelů

Existují dva typy úložiště modelu pro ukládání modelů a rozhraní schopností zařízení:

- Existuje jedno _veřejné úložiště_ , ve kterém jsou uložené modely schopností zařízení a rozhraní pro zařízení v [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat). Toto úložiště také ukládá [společná rozhraní](./concepts-common-interfaces.md) a [DCMs a rozhraní publikovaná partnery Microsoftu](./howto-onboard-portal.md). Informace o tom, jak certifikovat zařízení a přidat model schopností zařízení do veřejného úložiště, najdete v kurzu [certifikace zařízení IoT technologie Plug and Play](./tutorial-certification-test.md).
- Existuje více _úložišť společnosti_. Firemní úložiště se automaticky vytvoří pro vaši organizaci při [připojování k portálu Azure Certified for IoT](./howto-onboard-portal.md). Úložiště vaší společnosti můžete použít k ukládání modelů a rozhraní schopností zařízení během vývoje a testování.

## <a name="azure-certified-for-iot-portal"></a>Portál Azure Certified for IoT

Na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com)můžete provádět následující úlohy:

- [Dokončete proces certifikace pro vaše zařízení IoT](./tutorial-certification-test.md).
- Najděte si modely schopností zařízení technologie Plug and Play IoT. Tyto modely můžete použít k [rychlému sestavení zařízení s připraveným pro IoT a jejich integraci s řešeními](./quickstart-connect-pnp-device-solution.md).

## <a name="azure-cli"></a>Azure CLI

Azure CLI nabízí příkazy pro správu modelů a rozhraní funkcí zařízení v úložištích IoT technologie Plug and Play veřejných a podnikových modelů.

## <a name="visual-studio-code"></a>Visual Studio Code

Otevřete zobrazení **úložiště modelu** v Visual Studio Code.

1. Otevřete Visual Studio Code, použijte **CTRL + SHIFT + P**, zadejte a vyberte **IoT technologie Plug and Play: Otevřete úložiště**modelu.

1. Můžete zvolit **otevření úložiště veřejného modelu** nebo **otevření úložiště organizačních modelů**. V případě úložiště modelu společnosti musíte zadat připojovací řetězec úložiště modelu. Tento připojovací řetězec najdete na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) na kartě **připojovací řetězce** pro **úložiště vaší společnosti**.

1. Nová karta otevře zobrazení **úložiště modelu** .

    Toto zobrazení slouží k přidání, stažení a odstranění modelů a rozhraní funkcí zařízení. Pomocí filtru můžete najít konkrétní položky v seznamu.

1. Pokud chcete přepínat mezi úložištěm modelu vaší společnosti a veřejným úložištěm modelu, použijte **kombinaci kláves CTRL + SHIFT + P**, zadejte a vyberte **IoT technologie Plug and Play: Odhlásit úložiště**modelu. Pak použijte **technologie Plug and Play IoT: Znovu spusťte příkaz** úložiště modelu.

> [!NOTE]
> V VS Code úložiště veřejného modelu je jen pro čtení. Partneři Microsoftu můžou aktualizovat veřejné úložiště na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Další postup

Navržený další krok se naučíte, jak [Odeslat zařízení IoT technologie Plug and Play pro certifikaci](tutorial-certification-test.md).
