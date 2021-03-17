---
title: Nahrání souborů ze zařízení do služby Azure Storage | Microsoft Docs
description: Jak nakonfigurovat nahrávání souborů z vašich zařízení do cloudu Až nakonfigurujete nahrávání souborů, implementujte v zařízeních nahrávání souborů.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796900"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Nahrání souborů ze zařízení do cloudu

*Toto téma se týká správců a vývojářů zařízení.*

IoT Central umožňuje nahrávat média a další soubory z připojených zařízení do cloudového úložiště. V aplikaci IoT Central nakonfigurujete schopnost nahrávání souborů a potom v kódu zařízení implementovat nahrávání souborů.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné konfigurovat nahrávání souborů, musíte být správcem v aplikaci IoT Central.

K ukládání nahraných souborů potřebujete účet úložiště Azure a kontejner. Pokud nemáte existující účet úložiště a kontejner, který se má použít, vytvořte [v Azure Portal nový účet úložiště](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Konfigurovat nahrávání souborů zařízení

Konfigurace nahrávání souborů zařízení:

1. V aplikaci přejděte do části **Správa** .

1. Vyberte **nahrávání souboru zařízení**.

1. Vyberte účet úložiště a kontejner, který chcete použít. Pokud se účet úložiště nachází v jiném předplatném Azure z vaší aplikace, zadejte připojovací řetězec účtu úložiště.

1. V případě potřeby upravte časový limit nahrávání, který nastaví, jak dlouho bude požadavek na nahrávání zůstat platný pro. Platné hodnoty jsou od 1 do 24 hodin.

1. Vyberte **Uložit**. Když se stav zobrazuje jako **nakonfigurovaný**, budete připraveni nahrávat soubory ze zařízení.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Konfigurace nahrávání souborů v aplikaci":::

## <a name="disable-device-file-uploads"></a>Zakázat nahrávání souborů zařízení

Pokud chcete zakázat nahrávání souborů zařízení do aplikace IoT Central:

1. V aplikaci přejděte do části **Správa** .

1. Vyberte **nahrávání souboru zařízení**.

1. Vyberte **Odstranit**.

## <a name="upload-a-file-from-a-device"></a>Nahrání souboru ze zařízení

IoT Central používá možnost nahrávání souborů IoT Hub k tomu, aby zařízení mohla nahrávat soubory. Vzorový kód, který ukazuje, jak nahrát soubory ze zařízení, najdete v [ukázce zařízení IoT Central File upload](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Další kroky

Teď, když víte, jak nakonfigurovat a implementovat nahrávání souborů zařízení v IoT Central, je navržený další krok, kde najdete další informace o nahrávání souborů zařízení:

- [Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Nahrání souborů ze zařízení do cloudu pomocí IoT Hub (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)