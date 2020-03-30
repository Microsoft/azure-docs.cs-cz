---
title: Principy nákladů Centra zabezpečení Azure pro IoT| Dokumenty společnosti Microsoft
description: Přečtěte si o nákladech spojených s Azure Security Center pro IoT a o tom, jak je řídit.
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
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348521"
---
# <a name="pricing-and-associated-costs"></a>Ceny a související náklady

Tento článek vysvětluje Azure Security Center pro ioT cenový model, shrnuje všechny související náklady a vysvětluje, jak je spravovat.

## <a name="pricing"></a>Ceny

Azure Security Center for IoT cenový model se skládá ze dvou částí a účtuje se, jakmile je služba IoT Hub [povolená](quickstart-onboard-iot-hub.md) v Centru zabezpečení Azure pro IoT:

- Náklady podle zařízení – integrované možnosti zabezpečení založené na analýze protokolů služby IoT Hub.

- Náklady podle zpráv – rozšířené možnosti zabezpečení založené na zprávách zabezpečení z IoT Edge nebo leaf zařízení.


Další informace naleznete v [tématu Security Center pricing](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Související náklady

Azure Security Center pro IoT má přidružené náklady, které nejsou součástí přímých cen:


- Náklady na úložiště Log Analytics

Související náklady můžete snížit tím, že se odhlásíte z určitých funkcí řešení. Odhlaste se změnou nastavení.

Změna nastavení:

1. Otevřete centrum IoT.

2. V části **Zabezpečení**klepněte na položku **Přehled**.

3. Klikněte na příkaz **Nastavení**.

Následující tabulka obsahuje souhrn souvisejících nákladů a důsledků jednotlivých možností.

|     | Využití | Poznámka |
| --- | --- | --- |
| **Úložiště Log Analytics** |  |
| Doporučení a upozornění zařízení| Doporučení zabezpečení a výstrahy generované službou | Není volitelné |
| Nezpracovaná bezpečnostní data| Nezpracovaná bezpečnostní data ze zařízení IoT shromážděná bezpečnostními agenty | Zakázat _ukládání nezpracovaných událostí zabezpečení zařízení_ |
|

>[!Important]
> Odhlášení má závažné důsledky pro dostupnost funkcí zabezpečení Azure Security Center for IoT. 
  
| Odhlásit | Důsledky |
| --- | --- |
| _Kolekce metadat dvojčat_ | Zakázání [vlastních výstrah](quickstart-create-custom-alerts.md) |
| | Zakázat doporučení manifestu IoT Edge |
| | Zakázání doporučení a výstrah založených na identitách zařízení |
| _Ukládání nezpracovaných událostí zabezpečení zařízení_ | Podrobnosti o základních doporučeních operačního systémem zařízení nejsou k dispozici. |
| | Podrobnosti o [vyšetřování výstrah](concept-security-alerts.md) a [doporučení](concept-recommendations.md) nejsou k dispozici |
|


## <a name="see-also"></a>Viz také

- Přístup k [nezpracovaným bezpečnostním datům](how-to-security-data-access.md)
- [Prozkoumání zařízení](how-to-investigate-device.md)
- Pochopení a zkoumání [bezpečnostních doporučení](concept-recommendations.md)
- Pochopení a prozkoumání [výstrah zabezpečení](concept-security-alerts.md)
