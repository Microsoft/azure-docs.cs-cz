---
title: Řešení Azure pro Internet věcí (IoT Edge) | Dokumentace Microsoftu
description: Přehled ukázkové architektury řešení IoT a informace o tom, jak souvisí se zařízeními, službou Azure IoT Hub, sadami SDK pro zařízení Azure IoT, sadami SDK pro služby Azure IoT a dalšími službami Azure.
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 56b7bfe02ddb0f2c909b2f363c37308527ec8db1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029083"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Další postup

Azure IoT Edge je služba Azure, která umožňuje provádění analýz a zpracování dat na hraničních zařízeních. Pomocí IoT Edge můžete ve svých zařízeních využívat kód založený na kontejnerech, který zahrnuje logiku převzatou přímo ze služeb Azure, které již používáte, nebo vlastní kód specifický pro konkrétní řešení. Zařízení díky tomu můžou také:

* Fungovat jako zařízení brány a agregovat a zpracovávat data z několika zařízení typu list.
* Provádět detekci anomálií a reagovat na změny prostředí bez nutnosti čekat na pokyny z cloudu.
* Minimalizovat náklady na šířku pásma a úložiště díky předběžnému zpracování dat a odesílání výsledků. 

IoT Edge zahrnuje také cloudové rozhraní, které umožňuje vzdálenou správu zařízení. Do zařízení můžete nasazovat kód, monitorovat jejich stav a aktualizovat je bez nutnosti fyzického přístupu. Můžete vzdáleně spravovat jednotlivá zařízení nebo vytvářet nasazení, která ovlivní velké sady zařízení, které nadefinujete. Další informace najdete v tématu [Vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku][lnk-deployment].

Informace o komponentách umožňujících IoT Edge najdete v tématu [Popis fungování Azure IoT Edge][lnk-overview].

Pokud jste ještě nikdy nepoužili Azure IoT Hub, možná byste měli začít tématem [Přehled služby Azure IoT Hub][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: about-iot-edge.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
