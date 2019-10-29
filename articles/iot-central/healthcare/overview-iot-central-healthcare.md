---
title: Sestavování řešení zdravotnictví pomocí Azure IoT Central | Microsoft Docs
description: Naučte se sestavovat řešení zdravotnictví pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: a5a8c8e52c4bebda4e22e592fefa3801449504e3
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027453"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Sestavování řešení zdravotnictví pomocí Azure IoT Central 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Naučte se sestavovat řešení zdravotní péče pomocí šablon aplikací pomocí Azure IoT Central.

## <a name="what-is-continuous-patient-monitoring-template"></a>Co je šablona pro průběžné monitorování pacienta?

V prostoru IoT pro zdravotnictví je sledování nepřetržitého pacienta jedním z klíčových příchodů, které snižuje riziko opětovného přístupu, správu chronické choroby a zlepšení výsledků pacientů. Nepřetržité monitorování pacientů se dá rozdělit do dvou hlavních kategorií:

1. **Monitorování v pacientech**: používání lékařských wearables a dalších zařízení v nemocnicích může monitorovat důležité příznaky a lékařské podmínky pro pacienty, aniž by bylo nutné poslat zdravotnímu lékaři, aby se na pacienta několikrát denně zkontroloval. Týmy pro péči o zdravotní péči můžou pochopit okamžik, kdy pacient potřebuje kritickou pozornost prostřednictvím oznámení a efektivně stanovit prioritu jejich času.
1. **Vzdálené monitorování pacienta**: pomocí lékařských wearables a pacientů hlášených pacienty (specialisty) monitorovat pacienty mimo nemocnice, riziko opakovaného přístupu se dá významně snížit. Data z útoků chronické choroby pacient a rehabilitace pacienty je možné shromažďovat, aby se zajistilo, že pacientům vyhovují plánům péče a že výstrahy na zhoršení pacienta můžou být v souladu s tím, aby se týmy seznámily, než budou kritické.

Tato šablona aplikace se dá použít k sestavení řešení pro obě kategorie nepřetržitého monitorování pacientů. Mezi výhody patří:

* Bezproblémově spojí celou řadu lékařských wearables instance IoT Central.
* Monitorujte a spravujte zařízení, abyste zajistili, že zůstanou v pořádku.
* Vytvořte vlastní pravidla týkající se dat zařízení, aby se aktivovaly příslušné výstrahy.
* Exportujte data o stavu pacienta do Azure API pro FHIR, což je kompatibilní úložiště dat.
* Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací, které umožňují posílit týmy.

>[!div class="mx-imgBorder"] 
>![CPM-](media/in-patient-dashboard.png) řídicího panelu

## <a name="next-steps"></a>Další kroky

Začínáme s vytvářením řešení pro monitorování nepřetržitého pacienta:

* [Nasazení šablony aplikace](tutorial-continuous-patient-monitoring.md)
* [Podívejte se na příklad architektury.](concept-continuous-patient-monitoring-architecture.md)