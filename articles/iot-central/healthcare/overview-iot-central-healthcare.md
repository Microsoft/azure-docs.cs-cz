---
title: Vytváření řešení zdravotní péče s Azure IoT Central | Dokumenty společnosti Microsoft
description: Naučte se vytvářet řešení zdravotní péče pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021481"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Sestavování řešení pro zdravotnictví s využitím služby Azure IoT Central 



Naučte se vytvářet řešení zdravotní péče pomocí Azure IoT Central pomocí šablon aplikací.

## <a name="what-is-continuous-patient-monitoring-template"></a>Co je průběžná šablona monitorování pacientů?

V oblasti IoT ve zdravotnictví je nepřetržité monitorování pacientů jedním z klíčových faktorů, které umožňují snížit riziko readmise, efektivněji zvládat chronická onemocnění a zlepšovat výsledky pacientů. Průběžné monitorování pacientů lze rozdělit do dvou hlavních kategorií:

1. **Monitorování u pacientů**: Pomocí lékařských nositelných zařízení a dalších zařízení v nemocnici mohou pečovatelské týmy sledovat vitální funkce pacienta a zdravotní stav, aniž by musely posílat zdravotní sestru, aby pacienta několikrát denně zkontrolovala. Pečovatelské týmy mohou pochopit okamžik, kdy pacient potřebuje kritickou pozornost prostřednictvím oznámení a efektivně upřednostňuje svůj čas.
1. **Vzdálené monitorování pacientů**: Pomocí lékařských nositelných zařízení a výsledků hlášených pacienty (PRO) ke sledování pacientů mimo nemocnici může být riziko zpětného přebírání sníženo. Údaje od pacientů s chronickým onemocněním a rehabilitačních pacientů mohou být shromažďovány, aby se zajistilo, že pacienti budou dostavovat plány péče a že záznamy o zhoršení stavu pacientů mohou být objeveny v pečovatelských týmech dříve, než se stanou kritickými.

Tuto šablonu aplikace lze použít k vytvoření řešení pro obě kategorie nepřetržitého monitorování pacientů. Nabízí například tyto výhody:

* Bezproblémově propojte různé druhy lékařských nositelných zařízení s instancí IoT Central.
* Monitorujte a spravujte zařízení, abyste zajistili, že zůstanou v pořádku.
* Vytvořte vlastní pravidla týkající se dat zařízení, která spustí příslušné výstrahy.
* Exportujte data o stavu pacientů do rozhraní Azure API pro FHIR, kompatibilní úložiště dat.
* Exportujte agregované přehledy do stávajících nebo nových obchodních aplikací.

>[!div class="mx-imgBorder"] 
>![Řídicí panel CPM](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Další kroky

Chcete-li začít vytvářet řešení nepřetržitého monitorování pacientů:

* [Nasazení šablony aplikace](tutorial-continuous-patient-monitoring.md)
* [Podívejte se na ukázkovou architekturu](concept-continuous-patient-monitoring-architecture.md)