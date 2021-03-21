---
title: Co jsou řešení Azure IoT Central zdravotnictví | Microsoft Docs
description: Naučte se sestavovat řešení zdravotnictví pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: e8a72195f0fcacce2c994e8770157b05b65d70ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833146"
---
# <a name="what-are-the-iot-central-healthcare-solutions"></a>Jaká jsou řešení pro zdravotní péči IoT Central?

Naučte se sestavovat řešení zdravotní péče pomocí šablon aplikací pomocí Azure IoT Central.

## <a name="what-is-continuous-patient-monitoring-template"></a>Co je šablona pro průběžné monitorování pacienta?

V prostoru IoT pro zdravotnictví je sledování nepřetržitého pacienta jedním z klíčových příchodů, které snižuje riziko opětovného přístupu, správu chronické choroby a zlepšení výsledků pacientů. Nepřetržité monitorování pacientů se dá rozdělit do dvou hlavních kategorií:

1. **Monitorování v pacientech**: používání lékařských wearables a dalších zařízení v nemocnicích může monitorovat důležité příznaky a lékařské podmínky pro pacienty, aniž by bylo nutné poslat zdravotnímu lékaři, aby se na pacienta několikrát denně zkontroloval. Týmy pro péči o zdravotní péči můžou pochopit okamžik, kdy pacient potřebuje kritickou pozornost prostřednictvím oznámení a efektivně stanovit prioritu jejich času.
1. **Sledování vzdáleného pacienta**: pomocí lékařských wearables a pacientů hlášených pacienty (specialisty) k monitorování pacientů mimo nemocnice je možné snížit riziko zneužití. Data z útoků chronické choroby pacient a rehabilitace pacienty je možné shromažďovat, aby se zajistilo, že pacientům vyhovují plánům péče a že výstrahy na zhoršení pacienta můžou být v souladu s tím, aby se týmy seznámily, než budou kritické.

Tato šablona aplikace se dá použít k sestavení řešení pro obě kategorie nepřetržitého monitorování pacientů. Nabízí například tyto výhody:

* K bezproblémovému připojení různých druhů lékařských wearables instance IoT Central.
* Monitorujte a spravujte zařízení, abyste zajistili, že zůstanou v pořádku.
* Vytvořte vlastní pravidla týkající se dat zařízení, aby se aktivovaly příslušné výstrahy.
* Exportujte data o stavu pacienta do Azure API pro FHIR, což je kompatibilní úložiště dat.
* Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací.

>[!div class="mx-imgBorder"] 
>![CPM – řídicí panel](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Další kroky

Začínáme s vytvářením řešení pro monitorování nepřetržitého pacienta:

* [Nasazení šablony aplikace](tutorial-continuous-patient-monitoring.md)
* [Podívejte se na příklad architektury.](concept-continuous-patient-monitoring-architecture.md)