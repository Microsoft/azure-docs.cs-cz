---
title: Připojení zařízení Azure Sphere v Azure IoT Central | Microsoft Docs
description: Naučte se připojit zařízení Azure Sphere (DevKit) k aplikaci Azure IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92123419"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Připojení zařízení Azure Sphere k aplikaci Azure IoT Central

*Tento článek se týká vývojářů zařízení.*

V tomto článku se dozvíte, jak připojit zařízení Azure Sphere (DevKit) k aplikaci Azure IoT Central.

Azure Sphere je zabezpečená platforma pro aplikace vysoké úrovně, která má integrované komunikační a zabezpečovací funkce pro zařízení připojená k internetu. Zahrnuje zabezpečenou, připojenou a křížený mikrokontrolerů jednotku (MCU), vlastní operační systém Linux na bázi Linux a cloudovou službu zabezpečení, která poskytuje nepřetržité a obnovitelné zabezpečení. Další informace najdete v tématu [co je Azure sphere?](/azure-sphere/product-overview/what-is-azure-sphere).

[Azure sphere Development Kit](https://azure.microsoft.com/services/azure-sphere/get-started/) poskytují vše, co potřebujete ke spuštění vytváření prototypů a vývoji Azure sphere aplikací. Azure IoT Central s Azure Sphere umožňuje kompletní zásobník pro řešení IoT. Azure Sphere poskytuje podporu zařízení a IoT Central jako s nulovým kódem a spravovanou aplikační platformou IoT.

V tomto článku s postupem:

- Vytvořte zařízení Azure Sphere v IoT Central pomocí šablony zařízení Azure Sphere DevKit z knihovny.
- Příprava zařízení Azure Sphere DevKit pro Azure IoT
- Připojte Azure Sphere DevKit k Azure IoT Central.
- Zobrazit telemetrii ze zařízení v IoT Central.

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto článku budete potřebovat následující:

- Aplikace Azure IoT Central.
- Visual Studio 2019, verze 16,4 nebo novější.
- [Sada Azure sphere MT3620 Development Kit z sada Seeed studia](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Pokud nemáte fyzické zařízení, pak po prvním kroku přeskočíte na poslední oddíl a vyzkoušíte simulované zařízení.

## <a name="create-the-device-in-iot-central"></a>Vytvořte zařízení v IoT Central

Vytvoření zařízení Azure Sphere v IoT Central:

1. V aplikaci Azure IoT Central vyberte kartu **šablony zařízení** a vyberte **+ Nová**. V části **použití vybrané šablony zařízení** vyberte **Azure sphere ukázkové zařízení**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Šablona zařízení pro Azure Sphere DevKit":::

1. V šabloně zařízení upravte zobrazení s názvem **Přehled** pro zobrazení **teploty** a **stisknutí tlačítka**.

1. Vyberte typ zobrazení pro **úpravy zařízení a cloudového zobrazení dat** a přidejte další zobrazení, ve kterém se zobrazuje **stav** vlastností čtení/zápisu. Přetáhněte vlastnost **indikátor stavu** na prázdný tečkovaný obdélník na pravé straně formuláře. Vyberte **Uložit**.

## <a name="prepare-the-device"></a>Příprava zařízení

Předtím, než budete moci připojit zařízení Azure Sphere DevKit k IoT Central, je třeba [nastavit zařízení a vývojové prostředí](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Připojit zařízení

Pokud chcete, aby se ukázka připojovala k IoT Central, musíte [nakonfigurovat aplikaci Azure IoT Central a pak upravit manifest aplikace ukázky](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Zobrazit telemetrii ze zařízení

Když je zařízení připojené k IoT Central, můžete zobrazit telemetrii na řídicím panelu.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Řídicí panel pro Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Vytvoření simulovaného zařízení

Pokud nemáte fyzické Azure Sphere zařízení DevKit, můžete vytvořit simulované zařízení a vyzkoušet si aplikaci Azure IoT Central.

Postup vytvoření simulovaného zařízení:

- Výběr **zařízení > Azure IoT sphere**
- Vyberte **+ Nový**.
- Zadejte jedinečné **ID zařízení** a popisný **název zařízení**.
- Povolí **simulované** nastavení.
- Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Přečtěte si o [připojení zařízení v Azure IoT Central](./concepts-get-connected.md)
- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .