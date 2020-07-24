---
title: Ceny a související náklady
description: Přečtěte si informace o nákladech spojených s Azure Security Center pro IoT a o tom, jak je ovládat.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: 98afd1eb1f8a73c2d6dd3f293a07cdb70780d238
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004762"
---
# <a name="pricing-and-associated-costs"></a>Ceny a související náklady

Tento článek vysvětluje Azure Security Center pro cenový model IoT, shrnuje všechny přidružené náklady a vysvětluje, jak je spravovat.

## <a name="pricing"></a>Ceny

Cenové modely Azure Security Center for IoT se skládají ze dvou částí a účtují se po [povolení](quickstart-onboard-iot-hub.md) IoT Hub v Azure Security Center pro IoT:

- Náklady podle funkcí zabezpečení integrovaných na zařízení na základě analýzy protokolů IoT Hub.

- Náklady podle zpráv – rozšířené možnosti zabezpečení na základě zpráv zabezpečení z IoT Edge nebo na listovém zařízení.

Další informace najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Přidružené náklady

Azure Security Center pro IoT má přidružené náklady, které nejsou součástí přímých cen:

- Náklady na úložiště Log Analytics

Můžete snížit související náklady tím, že se odhlásíte od určitých funkcí řešení. Odsouhlasit změnou nastavení.

Postup změny nastavení:

1. Otevřete IoT Hub.

1. V části **zabezpečení**klikněte na **Přehled**.

1. Klikněte na **Nastavení**.

Následující tabulka poskytuje souhrn souvisejících nákladů a dopadů jednotlivých možností.

| Možnost | Využití | Komentář |
| --- | --- | --- |
| **Log Analytics úložiště** |  |
| Doporučení a výstrahy zařízení| Doporučení a výstrahy zabezpečení vygenerované službou | Nenepovinné |
| Nezpracovaná data zabezpečení| Nezpracovaná data zabezpečení ze zařízení IoT shromažďovaných agenty zabezpečení | Zakázat _události zabezpečení úložiště nezpracovaného zařízení_ |
|

>[!Important]
> Možnost vyzkoušení má vážný dopad na Azure Security Center dostupnosti funkcí zabezpečení IoT.

| Odhlásit se | Důsledky |
| --- | --- |
| _Kolekce vlákenných metadat_ | Zakázat [vlastní výstrahy](quickstart-create-custom-alerts.md) |
| | Zakázat doporučení pro IoT Edge manifest |
| | Zakázat doporučení a výstrahy na základě identity zařízení |
| _Ukládat události zabezpečení nezpracovaného zařízení_ | Podrobnosti o doporučeních standardních hodnot operačního systému zařízení nejsou k dispozici. |
| | Podrobnosti o [výstrahách](concept-security-alerts.md) a vyšetřováních [doporučení](concept-recommendations.md) nejsou k dispozici. |
|

## <a name="see-also"></a>Viz také

- Přístup k [nezpracovaným datům zabezpečení](how-to-security-data-access.md)
- [Prozkoumání zařízení](how-to-investigate-device.md)
- Pochopení a zkoumání [doporučení zabezpečení](concept-recommendations.md)
- Pochopení a zkoumání [výstrah zabezpečení](concept-security-alerts.md)
