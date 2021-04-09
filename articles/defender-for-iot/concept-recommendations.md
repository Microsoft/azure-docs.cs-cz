---
title: Doporučení zabezpečení pro IoT Hub
description: Přečtěte si o konceptu doporučení zabezpečení a o tom, jak se používají v programu Defender pro IoT Hub.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779337"
---
# <a name="security-recommendations-for-iot-hub"></a>Doporučení zabezpečení pro IoT Hub

Defender pro IoT kontroluje vaše prostředky Azure a zařízení IoT a poskytuje doporučení pro zabezpečení pro omezení prostoru pro útok.
Doporučení zabezpečení jsou vhodná a zaměřují se na pomoc zákazníkům v souladu s osvědčenými postupy zabezpečení.

V tomto článku najdete seznam doporučení, která se můžou aktivovat na svém IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Integrovaná doporučení v IoT Hub

Výstrahy doporučení poskytují přehled a návrhy akcí pro zlepšení stav zabezpečení vašeho prostředí.

| Závažnost | Name | Zdroj dat | Description |
|--|--|--|--|
| Vysoká | Totožná pověření ověřování používaná více zařízeními | IoT Hub | IoT Hub přihlašovací údaje pro ověřování používají více zařízení. Tento proces může znamenat, že zařízení illegitimate zosobňuje legitimní zařízení. Duplicitní použití přihlašovacích údajů zvyšuje riziko zosobnění zařízení škodlivým objektem actor. |
| Střední | Výchozí zásady filtru IP adres by se měly odepřít. | IoT Hub | Konfigurace filtru IP adres by měla mít pravidla definovaná pro povolený provoz a měla by ve výchozím nastavení Odepřít všechny ostatní přenosy. |
| Střední | Pravidlo filtru IP obsahuje velký rozsah IP adres. | IoT Hub | Rozsah IP adres zdroje povoleného pravidla filtru IP je příliš velký. Přesná povolující pravidla můžou vaše centrum IoT vystavovat škodlivým aktérům. |
| Nízká | Povolit diagnostické protokoly v IoT Hub | IoT Hub | Povolte protokoly a zachovejte je po dobu až do roku. Uchovávání protokolů umožňuje znovu vytvořit stopy aktivity pro účely šetření, pokud dojde k bezpečnostnímu incidentu nebo dojde k ohrožení vaší sítě. |

## <a name="next-steps"></a>Další kroky

- Defender pro službu IoT [– Přehled](overview.md)
- Informace o [přístupu k datům zabezpečení](how-to-security-data-access.md)
- Další informace o [prověřování zařízení](how-to-investigate-device.md)
