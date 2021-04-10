---
title: Vlastní výstrahy zabezpečení pro IoT Hub
description: Přečtěte si o přizpůsobitelných výstrahách zabezpečení a doporučené nápravě pomocí programu Defender pro funkce a službu IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: d7a58bcdb759c3f31290cc7930eba6ca52fcc17b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784726"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender pro IoT Hub vlastní výstrahy zabezpečení

Defender pro IoT průběžně analyzuje vaše řešení IoT pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.

Doporučujeme, abyste vytvořili vlastní výstrahy na základě vašich znalostí očekávaného chování zařízení a zajistili, že výstrahy budou fungovat jako nejúčinnější indikátory potenciálního ohrožení v rámci jedinečného nasazení organizace a na šířku.

Následující seznamy programu Defender pro výstrahy IoT je možné definovat podle očekávaného chování IoT Hub. Další informace o tom, jak přizpůsobit jednotlivé výstrahy, najdete v tématu [Vytvoření vlastních výstrah](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Integrované vlastní výstrahy v IoT Hub

| Závažnost | Název upozornění | Zdroj dat | Description | Navrhovaná náprava |
|--|--|--|--|--|
| Nízká | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah. | IoT Hub | Počet zpráv z cloudu na zařízení (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet odmítnutých zpráv v cloudu na zařízení v protokolu AMQP je mimo povolený rozsah. | IoT Hub | Počet zpráv z cloudu na zařízení (Protokol AMQP) zamítnutý zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní upozornění – počet zpráv zařízení do cloudu v protokolu AMQP je mimo povolený rozsah. | IoT Hub | Množství zpráv ze zařízení do cloudu (AMQP Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet volání přímé metody je mimo povolený rozsah. | IoT Hub | Množství přímé metody vyvolání v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet nahrávání souborů je mimo povolený rozsah. | IoT Hub | Velikost nahrávání souborů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu HTTP je mimo povolený rozsah. | IoT Hub | Množství zpráv z cloudu na zařízení (protokol HTTP) v časovém okně není v nakonfigurovaném povoleném rozsahu. |
| Nízká | Vlastní výstraha – počet odmítnutých zpráv cloudu na zařízení v protokolu HTTP není v povoleném rozsahu. | IoT Hub | Množství zpráv z cloudu na zařízení (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní výstraha – počet zpráv zařízení do cloudu v protokolu HTTP je mimo povolený rozsah. | IoT Hub | Velikost zařízení pro cloudové zprávy (protokol HTTP) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní upozornění – počet zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | IoT Hub | Množství zpráv z cloudu na zařízení (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet odmítnutých zpráv v cloudu na zařízení v protokolu MQTT je mimo povolený rozsah. | IoT Hub | Množství zpráv z cloudu na zařízení (protokol MQTT) odmítnuté zařízením v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní upozornění – počet zpráv zařízení do cloudu v protokolu MQTT je mimo povolený rozsah. | IoT Hub | Množství zpráv ze zařízení do cloudu (MQTT Protocol) v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní výstraha – počet vyprázdnění fronty příkazů, které jsou mimo povolený rozsah | IoT Hub | Velikost fronty příkazů v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |  |
| Nízká | Vlastní výstraha – počet nepracujících aktualizací modulu je mimo povolený rozsah. | IoT Hub | Množství nespuštěných aktualizací v rámci určitého časového okna je mimo aktuálně nakonfigurovaný a povolený rozsah. |
| Nízká | Vlastní výstraha – počet neautorizovaných operací je mimo povolený rozsah. | IoT Hub | Množství neautorizovaných operací v rámci určitého časového období je mimo aktuálně nakonfigurovaný a povolený rozsah. |

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak [přizpůsobit upozornění](quickstart-create-custom-alerts.md)
- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
