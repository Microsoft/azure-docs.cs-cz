---
title: Pomocí portálu Azure ke konfiguraci nahrávání souborů | Microsoft Docs
description: Postup konfigurace služby IoT hub, chcete-li povolit nahrávání souborů z připojených zařízení pomocí portálu Azure. Obsahuje informace o konfiguraci cílového účtu úložiště Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 0100cbe4bbc66d0c4ef940cc40f4fa3441176a1a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633202"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurace centra IoT nahrávání souborů pomocí portálu Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Nahrání souboru

Použít [souboru nahrávání funkce IoT hub][lnk-upload], je třeba nejprve přidružit účet služby Azure Storage pomocí centra. Vyberte **nahrávání souborů** zobrazíte seznam vlastností nahrávání souboru pro službu IoT hub, která je upravována.

![Zobrazení IoT Hub soubor nahrát nastavení na portálu][13]

**Kontejner úložiště**: pomocí portálu Azure vyberte kontejner objektů blob v účtu Azure Storage v aktuálním předplatném Azure přidružit služby IoT Hub. Pokud potřeby můžete vytvořit účet úložiště Azure na **účty úložiště** okno a objektů blob kontejneru na **kontejnery** okno. IoT Hub automaticky vygeneruje identifikátory URI SAS s oprávněními k zápisu do tohoto kontejneru objektů blob pro zařízení se má použít při odesílají soubory.

![Zobrazit kontejnery úložiště pro nahrávání souborů na portálu][14]

**Přijímat oznámení pro nahraném soubory**: povolení nebo zakázání oznámení o odeslání souboru přes přepínač.

**Hodnota TTL SAS**: Toto nastavení je time-to-live identifikátorů URI SAS, vrácený do zařízení IoT Hub. Ve výchozím nastavení má jednu hodinu, ale můžete přizpůsobit tak, aby ostatní hodnoty pomocí posuvníku.

**Upozornění na nastavení výchozí hodnota TTL souboru**: time-to-live souboru odeslat oznámení, než vyprší jejich platnost. Ve výchozím nastavení má jeden den, ale můžete přizpůsobit tak, aby ostatní hodnoty pomocí posuvníku.

**Soubor doručení maximální počet oznámení**: počet, kolikrát se Centrum IoT pokusí doručit soubor odeslat oznámení. Ve výchozím nastavení má 10, ale můžete přizpůsobit tak, aby ostatní hodnoty pomocí posuvníku.

![Konfigurace služby IoT Hub nahrávání souborů na portálu][15]

## <a name="next-steps"></a>Další postup

Další informace o možnostech nahrávání souboru Centrum IoT najdete v tématu [nahrání souborů ze zařízení] [ lnk-upload] v příručce pro vývojáře IoT Hub.

Další informace o správě Azure IoT Hub na následujících odkazech:

* [Hromadné spravovat zařízení IoT][lnk-bulk]
* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]
* [Zabezpečení řešení IoT od základů nahoru][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
