---
title: Pomocí Azure Portal nakonfigurujte nahrávání souborů | Microsoft Docs
description: Jak používat Azure Portal ke konfiguraci služby IoT Hub, která umožňuje nahrávání souborů z připojených zařízení. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535871"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurace nahrávání souborů s využitím služby IoT Hub pomocí webu Azure Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Nahrání souboru

Pokud chcete používat [funkci nahrávání souborů v IoT Hub](iot-hub-devguide-file-upload.md), musíte nejdřív přidružit účet Azure Storage k vašemu rozbočovači. Výběrem možnosti **nahrát soubor** zobrazíte seznam vlastností odeslání souborů pro Centrum IoT, které se právě mění.

![Zobrazit IoT Hub nastavení nahrávání souborů na portálu](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Kontejner úložiště** : pomocí Azure Portal vyberte kontejner objektů BLOB v účtu Azure Storage v aktuálním předplatném Azure, který chcete přidružit k vašemu IoT Hub. V případě potřeby můžete vytvořit účet Azure Storage v okně s **účty úložiště** a kontejnerem objektů BLOB v okně **kontejnery** . IoT Hub automaticky generuje identifikátory URI SAS s oprávněním k zápisu do tohoto kontejneru objektů blob, aby bylo možné zařízení použít při nahrávání souborů.

   ![Zobrazit kontejnery úložiště pro nahrání souboru na portálu](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Dostávat oznámení pro nahrané soubory** : povolení nebo zakázání oznámení o nahrávání souborů prostřednictvím přepínače.

* **SAS TTL** : Toto nastavení představuje čas do živého vysílání identifikátorů URI SAS vrácených do zařízení IoT Hub. Nastavte na jednu hodinu ve výchozím nastavení, ale můžete ji přizpůsobit na jiné hodnoty pomocí posuvníku.

* **Nastavení oznámení o souboru výchozí hodnota TTL** : čas do živého oznámení o nahrání souboru před vypršením jeho platnosti. Nastaví se ve výchozím nastavení na jeden den, ale můžete je přizpůsobit na jiné hodnoty pomocí posuvníku.

* **Maximální počet doručení oznámení souborů** : počet, kolikrát se IoT Hub pokusí doručovat oznámení o nahrání souboru. Ve výchozím nastavení nastavte na hodnotu 10, ale můžete je přizpůsobit na jiné hodnoty pomocí posuvníku.

   ![Konfigurace IoT Hubho nahrávání souborů na portálu](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Další kroky

Další informace o možnostech nahrávání souborů IoT Hub najdete v tématu [nahrání souborů ze zařízení](iot-hub-devguide-file-upload.md) v příručce pro vývojáře IoT Hub.

Pokud chcete získat další informace o správě IoT Hub Azure, postupujte podle těchto odkazů:

* [Hromadná správa zařízení IoT](iot-hub-bulk-identity-mgmt.md)
* [Monitorování služby IoT Hub](monitor-iot-hub.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Zabezpečení řešení IoT od základů](../iot-fundamentals/iot-security-ground-up.md)