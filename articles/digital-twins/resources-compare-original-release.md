---
title: Rozdíly z původní verze
titleSuffix: Azure Digital Twins
description: Informace o tom, co se změnilo v nové verzi digitálních vláken Azure
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "99072635"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Co je nového digitálních vláken Azure? Jak se liší od původní verze (2018)?

První veřejná verze Preview digitálních vláken Azure byla vydaná v říjnu 2018. I když se základní koncepty z této původní verze přenesly do aktuální služby, mnohé z rozhraní a podrobnosti implementace se změnily, aby služba byla pružnější a přístupná. Tyto změny byly motivovány názory zákazníků.

> [!IMPORTANT]
> Na základě rozšířených možností nové služby je původní služba digitálních vláken Azure vyřazená. Od ledna 2021 nejsou již žádná rozhraní API a přidružená data k dispozici.

Pokud jste v první verzi Public Preview použili první verzi digitálních vláken Azure, použijte informace a osvědčené postupy v tomto článku, abyste se dozvěděli, jak pracovat s aktuální službou a využívat její funkce.

## <a name="differences-by-topic"></a>Rozdíly podle tématu

Následující graf obsahuje souběžné zobrazení konceptů, které se změnily mezi původní verzí služby a aktuální službou.

| Téma | V původní verzi | V aktuální verzi |
| --- | --- | --- | --- |
| **Modelování**<br>*Flexibilnější* | Původní vydaná verze byla navržena kolem inteligentních mezer, takže byla dodávána s vestavěným slovníkem pro budovy. | Aktuální digitální vlákna Azure je Domain-nezávislá. Můžete definovat vlastní slovník a vlastní modely pro vaše řešení, aby bylo možné lépe znázornit více druhů prostředí, a to flexibilním způsobem.<br><br>Další informace najdete v [*konceptech: vlastní modely*](concepts-models.md). |
| **Topologie**<br>*Flexibilnější*| Původní verze podporovala stromovou strukturu dat, která je upravena na inteligentní prostory. Digitální vlákna byly propojeny s hierarchickými vztahy. | S aktuální verzí můžete digitální vlákna připojit do libovolných topologií grafů, které jsou uspořádány, ale chcete. Díky tomu získáte větší flexibilitu při vyjádření složitých vztahů reálného světa.<br><br>Další informace najdete v části [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md). |
| **Výpočetní služby**<br>*Bohatší, flexibilnější* | V původní verzi byla logika pro zpracování událostí a telemetrie definována v uživatelsky definovaných funkcích jazyka JavaScript (UDF). Ladění pomocí UDF bylo omezené. | Aktuální verze obsahuje otevřený výpočetní model: poskytujete vlastní logiku připojením externích výpočetních prostředků, jako je [Azure Functions](../azure-functions/functions-overview.md). To vám umožňuje používat programovací jazyk dle vašeho výběru, přistupovat k vlastním knihovnám kódu bez omezení a využívat vývoj a ladění prostředků, které může mít externí služba.<br><br>Další informace najdete v tématu [*Postupy: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md). |
| **Správa zařízení pomocí IoT Hub**<br>*Přístupnější* | Původní vydaná verze spravovaná na zařízeních s instancí služby [IoT Hub](../iot-hub/about-iot-hub.md) , která byla interní pro službu Azure Digital revlákens. Toto integrované centrum nebylo pro vývojáře plně dostupné. | V aktuální verzi získáte vlastní "Centrum IoT" tím, že připojíte nezávisle vytvořenou instanci IoT Hub (spolu se všemi zařízeními, která už spravuje). Díky tomu získáte úplný přístup k možnostem IoT Hub a budete mít kontrolu nad správou zařízení.<br><br>Další informace najdete v tématu Postupy: ingestování [*telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Zabezpečení**<br>*Více standardních* | Původní verze obsahovala předdefinované role, které můžete použít ke správě přístupu k vaší instanci. | Aktuální verze se integruje do stejné back-endové služby [řízení přístupu založené na rolích Azure (Azure RBAC)](../role-based-access-control/overview.md) , které používají jiné služby Azure. To může zjednodušit ověřování mezi ostatními službami Azure ve vašem řešení, jako je IoT Hub, Azure Functions, Event Grid a další.<br>Pomocí RBAC můžete pořád používat předdefinované role, nebo můžete vytvářet a konfigurovat vlastní role.<br><br>Přečtěte si další informace v [*konceptech: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md). |
| **Škálovatelnost**<br>*Zvýšen* | Původní vydaná verze měla omezení škálování pro zařízení, zprávy, grafy a jednotky škálování. Na jedno předplatné se podporuje jenom jedna instance digitálních vláken Azure.  | Aktuální verze spoléhá na novou architekturu s lepší škálovatelností a má vyšší výpočetní výkon. Podporuje také 10 instancí na oblast v rámci jednoho předplatného.<br><br>Podrobnosti o limitech v aktuální verzi najdete v tématu [*omezení služby digitálních vláken Azure*](reference-service-limits.md) . |

## <a name="service-limits"></a>Omezení služby

Seznam omezení digitálních vláken Azure najdete v tématu [*omezení služby digitálních vláken Azure*](reference-service-limits.md).

## <a name="next-steps"></a>Další kroky

* Podrobně se do práce s aktuálním vydáním v rychlém startu: [*rychlý Start: Prozkoumejte ukázkový scénář*](quickstart-adt-explorer.md).

* Nebo začněte číst o klíčových konceptech s [*Koncepty: vlastní modely*](concepts-models.md).