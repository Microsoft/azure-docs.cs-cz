---
title: Rozdíly oproti předchozí verzi
titleSuffix: Azure Digital Twins
description: Informace o tom, co se změnilo v nové verzi digitálních vláken Azure
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6d1786766fc61ed0edfdc53295a50ba212818fc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181421"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Co je nového digitálních vláken Azure? Jak se liší od předchozí verze (2018)?

První veřejná verze Preview digitálních vláken Azure byla vydaná v říjnu 2018. I když se základní koncepty z této předchozí verze přenesly do nové služby ve verzi Public Preview, mnoho z rozhraní a podrobnosti implementace se změnily, aby služba byla pružnější a přístupná. Tyto změny byly motivovány názory zákazníků.

> [!IMPORTANT]
> Na základě rozšířených možností nové služby bude předchozí služba Azure Digital Service vyřazena na konci roku 2020.

Pokud jste během předchozí veřejné verze Preview použili první verzi digitálních vláken Azure, použijte informace a osvědčené postupy v tomto článku, abyste se dozvěděli, jak pracovat s novou službou a využívat její funkce.

## <a name="differences-by-topic"></a>Rozdíly podle tématu

Následující graf obsahuje souběžné zobrazení konceptů, které se změnily mezi předchozí verzí služby a novou (aktuální) službou.

| Téma | V předchozí verzi | V nové verzi |
| --- | --- | --- | --- |
| **Modelování**<br>*Flexibilnější* | Předchozí verze byla navržena kolem inteligentních mezer, takže byla dodávána s vestavěným slovníkem pro budovy. | Nové digitální vlákna Azure je Domain-nezávislá. Můžete definovat vlastní slovník a vlastní modely pro vaše řešení, aby bylo možné lépe znázornit více druhů prostředí, a to flexibilním způsobem.<br><br>Další informace najdete v [*konceptech: vlastní modely*](concepts-models.md). |
| **Topologie**<br>*Flexibilnější*| Předchozí verze podporovala stromovou strukturu dat, která je upravena na inteligentní prostory. Digitální vlákna byly propojeny s hierarchickými vztahy. | V nové verzi můžete digitální vlákna připojit do libovolných topologií grafů, které jsou uspořádány, ale chcete. Díky tomu získáte větší flexibilitu při vyjádření složitých vztahů reálného světa.<br><br>Další informace najdete v části [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md). |
| **Výpočetní služby**<br>*Bohatší, flexibilnější* | V předchozí verzi byla logika pro zpracování událostí a telemetrie definována v uživatelsky definovaných funkcích jazyka JavaScript (UDF). Ladění pomocí UDF bylo omezené. | Nová verze obsahuje otevřený výpočetní model: poskytujete vlastní logiku připojením externích výpočetních prostředků, jako je [Azure Functions](../azure-functions/functions-overview.md). To vám umožňuje používat programovací jazyk dle vašeho výběru, přistupovat k vlastním knihovnám kódu bez omezení a využívat vývoj a ladění prostředků, které může mít externí služba.<br><br>Další informace najdete v tématu [*Postupy: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md). |
| **Správa zařízení pomocí IoT Hub**<br>*Přístupnější* | Předchozí vydaná verze spravovaná zařízení s instancí [IoT Hub](../iot-hub/about-iot-hub.md) , která byla interní pro službu Azure Digital revlákens. Toto integrované centrum nebylo pro vývojáře plně dostupné. | V nové verzi získáte vlastní "IoT Hub" tím, že připojíte nezávisle vytvořenou instanci IoT Hub (spolu se všemi zařízeními, která už spravuje). Díky tomu získáte úplný přístup k možnostem IoT Hub a budete mít kontrolu nad správou zařízení.<br><br>Další informace najdete v tématu Postupy: ingestování [*telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Zabezpečení**<br>*Více standardních* | Předchozí verze obsahovala předdefinované role, které můžete použít ke správě přístupu k vaší instanci. | Nová verze se integruje do stejné back-endové služby [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) , kterou používají jiné služby Azure. To může zjednodušit ověřování mezi ostatními službami Azure ve vašem řešení, jako je IoT Hub, Azure Functions, Event Grid a další.<br>Pomocí RBAC můžete pořád používat předdefinované role, nebo můžete vytvářet a konfigurovat vlastní role.<br><br>Přečtěte si další informace v [*konceptech: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md). |
| **Škálovatelnost**<br>*Zvýšen* | Předchozí verze obsahovala omezení škálování pro zařízení, zprávy, grafy a jednotky škálování. Na jedno předplatné se podporuje jenom jedna instance digitálních vláken Azure.  | Nová vydaná verze spoléhá na novou architekturu s lepší škálovatelností a má vyšší výpočetní výkon. Podporuje také 10 instancí na oblast v rámci jednoho předplatného.<br><br>Podrobnosti o limitech ve verzi Public Preview najdete v tématu [*referenční informace: omezení služby Public Preview*](reference-service-limits.md) . |

## <a name="service-limits-in-public-preview"></a>Omezení služby ve verzi Public Preview

Seznam omezení digitálních vláken Azure v této veřejné verzi Preview najdete v tématu Referenční informace [*: omezení služby Public Preview*](reference-service-limits.md).

## <a name="next-steps"></a>Další kroky

V dalším kroku podrobně práci s digitálními akcemi Azure pomocí prvního kurzu:

[*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)