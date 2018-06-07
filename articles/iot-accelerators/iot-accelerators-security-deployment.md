---
title: Zabezpečit vaše nasazení Internet věcí | Microsoft Docs
description: Tento článek podrobnosti o tom, jak zabezpečit vaše nasazení IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 9422a4ce316053b6560f1e945de7d8018e52c15b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654829"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Sady šifer akcelerátoru řešení IoT

Akcelerátorů řešení IoT podporují následující šifrovací sady, v tomto pořadí.

| Šifrovací sada | Délka |
| --- | --- |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 secp384r1 ECDH (0xc028) (ekvalizéru FS 7680 bits RSA) |256 |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (ekvalizéru FS 3072 bits RSA) |128 |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (ekvalizéru FS 7680 bits RSA) |256 |
| Protokol TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (ekvalizéru FS 3072 bits RSA) |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| Protokol TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Další informace najdete v tématech
Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [– Přehled akcelerátoru řešení prediktivní údržby][lnk-predictive-overview]
* [Nejčastější dotazy k akcelerátorům řešení IoT][lnk-faq]

Další informace o zabezpečení služby IoT Hub v [řízení přístupu ke službě IoT Hub] [ lnk-devguide-security] v příručce pro vývojáře IoT Hub.

[lnk-predictive-overview]:iot-accelerators-predictive-overview.md
[lnk-faq]:iot-accelerators-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
