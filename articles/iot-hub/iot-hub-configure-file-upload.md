---
title: Konfigurace nahrávání souborů pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Jak pomocí portálu Azure nakonfigurovat centrum IoT pro povolení nahrávání souborů z připojených zařízení. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735008"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurace nahrávání souborů s využitím služby IoT Hub pomocí webu Azure Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Nahrání souboru

Chcete-li použít [funkci nahrávání souborů v centru IoT Hub](iot-hub-devguide-file-upload.md), musíte nejprve přidružit účet Azure Storage k vašemu centru. Vyberte **Nahrání souboru,** chcete-li zobrazit seznam vlastností nahrávání souborů pro centrum IoT, které se právě mění.

![Zobrazení nastavení nahrávání souborů služby IoT Hub na portálu](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Kontejner úložiště**: Pomocí portálu Azure vyberte kontejner objektů blob v účtu Azure Storage v aktuálním předplatném Azure, který chcete přidružit k vašemu centru IoT Hub. V případě potřeby můžete vytvořit účet Úložiště Azure na **okno účty úložiště** a kontejner objektů blob na **kontejnery** okna. IoT Hub automaticky generuje identifikátory URI SAS s oprávněními k zápisu do tohoto kontejneru objektů blob, aby je zařízení používala při nahrávání souborů.

   ![Zobrazit kontejnery úložiště pro nahrání souborů na portálu](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Dostávat oznámení pro nahrané soubory**: Povolit nebo zakázat oznámení o nahrávání souborů pomocí přepínače.

* **TTL SAS**: Toto nastavení je čas emitované identifikátory URI SAS vrácené do zařízení službou IoT Hub. Ve výchozím nastavení nastavte na jednu hodinu, ale lze ji přizpůsobit jiným hodnotám pomocí posuvníku.

* **Nastavení oznámení souboru výchozí TTL**: Doba-k-live oznámení o nahrání souboru před vypršením jeho platnosti. Ve výchozím nastavení nastavte na jeden den, ale lze je přizpůsobit jiným hodnotám pomocí posuvníku.

* **Maximální počet doručení oznámení souboru**: Počet pokusů centra IoT Hub doručit oznámení o nahrání souboru. Ve výchozím nastavení nastavte hodnotu 10, ale lze ji přizpůsobit jiným hodnotám pomocí posuvníku.

   ![Konfigurace nahrávání souborů služby IoT Hub na portálu](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Další kroky

Další informace o možnostech nahrávání souborů služby IoT Hub najdete v [tématu Nahrávání souborů ze zařízení](iot-hub-devguide-file-upload.md) v průvodci vývojáři služby IoT Hub.

Další informace o správě služby Azure IoT Hub najdete na těchto odkazech:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Metriky IoT Hubu](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpečte své řešení IoT od základů](../iot-fundamentals/iot-security-ground-up.md)
