---
title: Prohlédněte si telemetrii k odvození modelu Azure Percept DK
description: Přečtěte si, jak v Azure IoT Exploreru Zobrazit telemetrii odvozeného modelu Vision Percept v Azure.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095319"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Prohlédněte si telemetrii k odvození modelu Azure Percept DK

Podle tohoto průvodce si můžete v [Azure IoT Exploreru](https://github.com/Azure/azure-iot-explorer/releases)zobrazit telemetrii k odvození modelu vize Azure Percept DK.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub
- [Model AI pro Vision byl nasazený do vaší Azure Percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Zobrazení telemetrických dat

1. Zapněte si DevKit.

1. Stáhněte a nainstalujte si [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases). Pokud jste uživatelem systému Windows, vyberte soubor. msi.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Obrazovka pro stažení pro Azure IoT Explorer":::

1. Připojte IoT Hub ke službě Azure IoT Explorer:

    1. Přejděte na [Azure Portal](https://portal.azure.com).

    1. Vyberte **Všechny prostředky**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure Portal domovskou stránku.":::

    1. Vyberte IoT Hub, ke které je připojená vaše Azure Percept DK.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Seznam IoT Hub v Azure Portal.":::

    1. Na levé straně stránky IoT Hub vyberte **zásady sdíleného přístupu**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="Stránka IoT Hub znázorňující zásady sdíleného přístupu.":::

    1. Klikněte na **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Obrazovka zásad sdíleného přístupu se zvýrazněným iothubownerem.":::

    1. Klikněte na ikonu modrého kopírování vedle pole **připojovací řetězec – primární klíč**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="okno iothubowner s vybraným tlačítkem kopírování připojovacího řetězce":::

    1. Otevřete Azure IoT Explorer a klikněte na **+ Přidat připojení**.

    1. Vložte připojovací řetězec do pole **připojovací řetězec** v okně **Přidat připojovací řetězec** a klikněte na **Uložit**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Okno Azure IoT Exploreru s polem pro vložení připojovacího řetězce do.":::

    1. Najeďte Vision SoM na objekt pro model Inferencing.

    1. Vyberte **telemetrii**.

    1. Kliknutím na tlačítko **Start** zobrazíte události telemetrie ze zařízení.

## <a name="next-steps"></a>Další kroky
Naučte se, jak zobrazit [datový proud videa Azure PERCEPT DK](./how-to-view-video-stream.md).