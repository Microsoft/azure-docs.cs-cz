---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838901"
---
V následující tabulce jsou uvedené limity, které se vztahují na prostředky Azure IoT Hub Device Provisioning Service.

| Prostředek | Omezení |
| --- | --- |
| Maximální počet služeb Device Provisioning na předplatné Azure | 10 |
| Maximální počet zápisů | 1 000 000 |
| Maximální počet registrací | 1 000 000 |
| Maximální počet skupin zápisu | 100 |
| Maximální počet certifikačních autorit | 25 |
| Maximální počet propojených Center IoT Hub | 50 |
| Maximální velikost zprávy | 96 KB|

> [!NOTE]
> Pokud chcete zvýšit počet zápisů a registrací ve službě zřizování, kontaktujte [Podpora Microsoftu](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Zvýšení maximálního počtu certifikačních autorit se nepodporuje.

Služba Device Provisioning omezuje požadavky na překročení následujících kvót.

| Omezení | Hodnota za jednotku |
| --- | --- |
| Operace | 200/min/Service |
| Registrace zařízení | 200/min/Service |
| Operace cyklického dotazování zařízení | 5/10 s/zařízení |
