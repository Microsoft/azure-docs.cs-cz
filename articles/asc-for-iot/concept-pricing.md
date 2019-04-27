---
title: Principy Azure Security Center pro IoT nákladů ve verzi Preview | Dokumentace Microsoftu
description: Další informace o náklady spojené s Azure Security Center pro Internet věcí a jak je můžete řídit.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 41b2d012ef2f6dd1ca5f57e04da43eb1a06dafde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360145"
---
# <a name="pricing-and-associated-costs"></a>Ceny a související náklady

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje Azure Security Center (ASC) pro IoT cenový model, shrnuje všechny související náklady a vysvětluje, jak je spravovat.

## <a name="pricing"></a>Ceny

ASC pro IoT cenový model se skládá ze dvou částí a účtuje se po služby IoT Hub [povolené](quickstart-onboard-iot-hub.md) v ASC pro IoT:

- Náklady podle zařízení – možnosti integrované zabezpečení na základě analýzy protokolů služby IoT Hub.

- Náklady podle zpráv – rozšířené možnosti zabezpečení na základě zabezpečení zpráv ze zařízení IoT Edge nebo listu.

  >[!Note]
  > Zabezpečení zprávy budou účtovat také využití kvóty ve službě IoT Hub.

Další informace najdete v tématu [ceny Security Center](https://azure.microsoft.com/en-us/pricing/details/security-center/).

## <a name="associated-costs"></a>Související náklady

ASC pro IoT má dva druhy související náklady, které nejsou součástí ceny s přímým přístupem:

- IoT Hub quota consumption

- Náklady na úložiště analýzy protokolů

Související náklady můžete snížit změnou nastavení nesouhlasu s generováním určité funkce.

Chcete-li změnit nastavení:

1. Otevřete Centrum IoT.

2. V části **zabezpečení**, klikněte na tlačítko **přehled**.

3. Klikněte na tlačítko **nastavení**.

Následující tabulka obsahuje souhrn související náklady a důsledky jednotlivých možností.

|     | Využití | Poznámka |
| --- | --- | --- |
| **Využití kvóty služby IoT Hub** |  |
| [Export zařízení](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) úlohy (dvojčete export) | Jednou za den | Zakázat _dvojčete kolekci metadat_ |
| **Úložiště analýzy protokolů** |  |
| Zařízení doporučení a výstrahy| Doporučení pro zabezpečení a výstrah generovaných službou | Není nepovinný |
| Nezpracovaná zabezpečení dat| Zabezpečení nezpracovaných dat ze zařízení IoT, shromážděná agenty zabezpečení | Zakázat _ukládat události zabezpečení_ |

>[!Important]
> Vyjádří svůj nesouhlas se závažné důsledky pro dostupných funkcí zabezpečení.
  
| Odhlásit | Důsledky |
| --- | --- |
| _Kolekce metadat dvojčete_ | Zakázat [vlastní upozornění](quickstart-create-custom-alerts.md) |
| | Zakázat manifestu doporučení IoT Edge |
| | Zakázat zařízení založená na identitě doporučení a výstrahy |
| _Události zabezpečení Store_ | Podrobnosti o doporučení standardních hodnot operačního systému zařízení nejsou k dispozici |
| | Podrobnosti o [výstraha](concept-security-alerts.md) a [doporučení](concept-recommendations.md) vyšetřování nejsou k dispozici |


## <a name="see-also"></a>Další informace najdete v tématech

- Přístup k vaší [nezpracovaná zabezpečení dat](how-to-security-data-access.md)
- [Prozkoumat zařízení](how-to-investigate-device.md)
- Porozumění a prozkoumávání [doporučení zabezpečení](concept-recommendations.md)
- Porozumění a prozkoumávání [výstrahy zabezpečení](concept-security-alerts.md)
