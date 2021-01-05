---
title: Ceny a související náklady
description: Přečtěte si o nákladech spojených s Defenderem pro IoT a o tom, jak je ovládat.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2020
ms.author: shhazam
ms.openlocfilehash: a97bcbf5ba47289a2e68b0eaa587ea39d7fb705a
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832348"
---
# <a name="pricing-and-associated-costs"></a>Ceny a související náklady

V tomto článku najdete popis cenového modelu v programu Defender, shrnuje všechny přidružené náklady a vysvětluje, jak je spravovat.

## <a name="pricing"></a>Ceny

Cenové modely Defender for IoT se skládají ze dvou částí a účtují se po [povolení](quickstart-onboard-iot-hub.md) IoT Hub v programu Defender pro IoT:

- Náklady podle funkcí zabezpečení integrovaných na zařízení na základě analýzy protokolů IoT Hub.

- Náklady podle zpráv – rozšířené možnosti zabezpečení na základě zpráv zabezpečení z IoT Edge nebo na listovém zařízení.

Další informace najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Přidružené náklady

Defender pro IoT má přidružené náklady, které nejsou součástí přímých cen:

- Náklady na úložiště Log Analytics

Můžete snížit související náklady tím, že se odhlásíte od určitých funkcí řešení. Odsouhlasit změnou nastavení.

Postup změny nastavení:

1. Otevřete IoT Hub.

1. V části **zabezpečení** klikněte na **Nastavení**.

1. Klikněte na **shromažďování dat**.

Následující tabulka poskytuje souhrn souvisejících nákladů a dopadů jednotlivých možností.

| Možnost | Využití | Komentář |
| --- | --- | --- |
| **Log Analytics úložiště** |  |
| Doporučení a výstrahy zařízení| Doporučení a výstrahy zabezpečení vygenerované službou | Nenepovinné |
| Nezpracovaná data zabezpečení| Nezpracovaná data zabezpečení ze zařízení IoT shromažďovaných agenty zabezpečení | Zakázat _události zabezpečení úložiště nezpracovaného zařízení_ |
|

>[!Important]
> K dispozici jsou závažné důsledky pro dostupnost funkcí zabezpečení IoT v programu Defender.

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
