---
title: Vytváření maloobchodních řešení s Azure IoT Central | Dokumenty společnosti Microsoft
description: Přečtěte si o používání šablon aplikací Azure IoT Central k vytváření propojených logistických, digitálních distribučních center, analýz v obchodech, monitorování stavu, pokladny, inteligentnísprávy inventáře a maloobchodních řešení.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 808a17fcf303c6eb72f11f5379fa172302dc782a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77059844"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Sestavování řešení pro maloobchod s využitím služby Azure IoT Central



Azure IoT Central je platforma aplikací IoT, která snižuje zátěž a náklady spojené s vývojem, správou a údržbou řešení IoT na podnikové úrovni. Pokud se rozhodnete vytvářet pomocí Azure IoT Central, můžete se soustředit svůj čas, peníze a energii na transformaci vašeho podnikání pomocí dat IoT, místo toho, abyste jen udržovali a aktualizovali složitou a neustále se vyvíjející infrastrukturu IoT.

Tento článek popisuje několik šablon aplikací IoT Central specifických pro maloobchod. Jako tvůrce řešení můžete tyto šablony použít k vytváření řešení IoT, která optimalizují dodavatelské řetězce, zlepšují prostředí v obchodě pro zákazníky a efektivněji sledují zásoby.

> [!div class="mx-imgBorder"]
> ![Přehled pro maloobchod Azure IoT](./media/overview-iot-central-retail/retail-app-templates.png)

Následující části popisují možnosti těchto šablon aplikací:

## <a name="connected-logistics"></a>Propojená logistika

Očekává se, že globální logistické výdaje dosáhnou v roce 2020 10,6 bilionu dolarů. Přeprava zboží představuje většinu těchto výdajů a poskytovatelé lodní dopravy jsou pod silným konkurenčním tlakem a omezeními.

Pomocí senzorů IoT můžete shromažďovat a monitorovat okolní podmínky, jako je teplota, vlhkost, náklon, šok, světlo a umístění zásilky. Telemetrická data získaná ze senzorů a zařízení IoT můžete kombinovat s dalšími zdroji dat, jako jsou informace o počasí a provozu v cloudových systémech business intelligence.

Mezi výhody propojeného logistického řešení patří:

* Sledování zásilek s trasováním a sledováním zásilek v reálném čase. 
* Integrita zásilky s monitorováním okolního stavu v reálném čase.
* Zabezpečení před krádeží, ztrátou nebo poškozením zásilek.
* Geografické oplocení, optimalizace tras, správa vozového parku a analýza vozidel.
* Prognóza předvídatelného odjezdu a příjezdu zásilek.

Následující snímky obrazovky ukazují předem poskakovací panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval vaše specifické požadavky na řešení:

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> ![Připojený logistický řídicí panel](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Další informace najdete v tématu Nasazení a projděte si kurz [šablony připojené logistické aplikace.](./tutorial-iot-central-connected-logistics-pnp.md)

## <a name="digital-distribution-center"></a>Digitální distribuční centrum

Jak výrobci a maloobchodníci zřizují celosvětovou přítomnost, jejich dodavatelské řetězce se rozvětvují a stávají se složitějšími. Spotřebitelé nyní očekávají, že bude k dispozici velký výběr výrobků a že toto zboží dorazí do jednoho nebo dvou dnů od nákupu. Distribuční centra se těmto trendům musí přizpůsobit a zároveň překonala stávající neefektivitu. 

Spoléhání se na manuální práci dnes znamená, že vychystávání a balení představuje 55-65% nákladů distribučních center. Ruční vychystávání a balení je také obvykle pomalejší než automatizované systémy a rychle kolísající personální potřeby ještě více ztěžují plnění objemu dopravy. Tato sezónní fluktuace vede k vysoké fluktuaci zaměstnanců a zvyšuje pravděpodobnost nákladných chyb.

Řešení založená na kamerách s podporou IoT mohou přinést transformační výhody povolením smyčky digitální zpětné vazby. Data z celého distribučního centra vedou k užitečným poznatkům, které zase vedou k lepším datům.

Mezi výhody digitálního distribučního centra patří:

* Kamery sledují zboží při příjezdu a pohybují se dopravníkovým systémem.
* Automatická identifikace vadného zboží.
* Efektivní sledování objednávek.
* Snížení nákladů, vyšší produktivita a optimalizované využití.

Následující snímek obrazovky ukazuje předem povislého řídicího panelu v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval vaše specifické požadavky na řešení: 

> [!div class="mx-imgBorder"]
> ![Řídicí panel centra digitální distribuce](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Další informace najdete v tématu Nasazení a projděte si kurz [šablony aplikace digitálního distribučního centra.](./tutorial-iot-central-digital-distribution-center-pnp.md)

## <a name="in-store-analytics---condition-monitoring"></a>Analýza v obchodě – monitorování stavu

Pro mnoho maloobchodníků jsou podmínky prostředí v jejich obchodech klíčovým odlišovačem od konkurence. Maloobchodníci chtějí udržovat příjemné podmínky ve svých obchodech ve prospěch svých zákazníků.  

Jako tvůrce řešení můžete použít šablonu aplikace pro monitorování stavu analýzy v obchodě IoT Central k vytvoření komplexního řešení. Šablona aplikace umožňuje digitální připojení a sledování prostředí maloobchodu pomocí různých druhů senzorových zařízení. Tato senzorová zařízení generují telemetrii, kterou můžete převést na obchodní přehledy, které pomáhají prodejci snižovat provozní náklady a vytvářet skvělé prostředí pro své zákazníky.

Šablonu aplikace použijte k:

* Připojte různé senzory IoT k instanci aplikace IoT Central.
* Monitorujte a spravujte stav senzorové sítě i všech zařízení brány v prostředí.
* Vytvořte vlastní pravidla týkající se podmínek prostředí v obchodě a aktivujte výstrahy pro správce obchodů.
* Transformujte podmínky prostředí ve vašem obchodě na přehledy, které může tým maloobchodu použít ke zlepšení zákaznického prostředí.
* Exportujte agregované přehledy do stávajících nebo nových obchodních aplikací a poskytněte užitečné a včasné informace zaměstnancům maloobchodu.

Šablona aplikace je dodávána se sadou šablon zařízení a k naplnění řídicího panelu používá sadu simulovaných zařízení. 

Následující snímek obrazovky ukazuje předem povislého řídicího panelu v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval vaše specifické požadavky na řešení: 

> [!div class="mx-imgBorder"]
> ![Monitorování stavu analýzy v obchodě](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Další informace najdete v [tématu Vytvoření analytické aplikace v obchodě v kurzu Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="in-store-analytics---checkout"></a>Analýza v obchodě – pokladna

Pro některé maloobchodníky, pokladna zkušenosti v rámci svých obchodů je klíčovým odlišovacím diskorálem od svých konkurentů. Maloobchodníci chtějí ve svých obchodech poskytovat hladký zážitek z pokladny, aby povzbudili zákazníky k návratu.  

Jako tvůrce řešení můžete použít šablonu aplikace pro fakturaci v obchodě IoT Central pro fakturaci v obchodě k vytvoření řešení, které poskytuje přehledy z celé zóny pokladny obchodu maloobchodním zaměstnancům. Senzory mohou například poskytnout informace o délkách fronty a průměrných čekacích dobách pro každý jízdní pruh pro pokladnu.

Šablonu aplikace použijte k:

* Připojte různé senzory IoT k instanci aplikace IoT Central.
* Monitorujte a spravujte stav senzorové sítě i všech zařízení brány v prostředí.
* Vytvořte vlastní pravidla kolem podmínky pokladny v obchodě a aktivujte výstrahy pro zaměstnance maloobchodu.
* Transformujte podmínky pokladny v obchodě na přehledy, které může tým maloobchodu použít ke zlepšení zákaznického prostředí.
* Exportujte agregované přehledy do stávajících nebo nových obchodních aplikací a poskytněte užitečné a včasné informace zaměstnancům maloobchodu.

Šablona aplikace je dodávána se sadou šablon zařízení a používá sadu simulovaných zařízení k naplnění řídicího panelu daty o obsazení jízdního pruhu. 

Následující snímek obrazovky ukazuje předem povislého řídicího panelu v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval vaše specifické požadavky na řešení: 

> [!div class="mx-imgBorder"]
> ![Pokladna analýzy v obchodě](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Další informace najdete v [tématu Vytvoření analytické aplikace v obchodě v kurzu Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

## <a name="smart-inventory-management"></a>Inteligentní řízení zásob

Zásoby jsou zásoby zboží, které má maloobchodník v držení. Řízení zásob je zásadní pro zajištění toho, aby byl správný produkt ve správný čas na správném místě. Maloobchodník musí vyvážit náklady na skladování příliš velkého množství zásob s náklady na to, že nemá na skladě dostatek zboží k uspokojení poptávky.

Data IoT generovaná z radiofrekvenčních identifikačních značek, majáků a kamer poskytují příležitosti ke zlepšení procesů řízení zásob. Telemetrická data získaná ze senzorů a zařízení IoT můžete kombinovat s dalšími zdroji dat, jako jsou informace o počasí a provozu v cloudových systémech business intelligence.

Mezi výhody inteligentního řízení zásob patří:

* Snížení rizika, že položky jsou vyprodány a zajištění požadované úrovně služeb zákazníkům. 
* Hloubková analýza a přehled přesnosti zásob téměř v reálném čase.
* Nástroje, které vám pomohou rozhodnout o správném množství zásob, které je třeba držet, aby vyhověly objednávkám zákazníků.

Tato šablona aplikace se zaměřuje na připojení zařízení a konfiguraci a správu zařízení pro čtení RFID a Bluetooth s nízkou spotřebou energie (BLE).

Následující snímek obrazovky ukazuje předem povislého řídicího panelu v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval vaše specifické požadavky na řešení:

> [!div class="mx-imgBorder"]
> ![Řídicí panel inteligentní správy zásob](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Další informace najdete v tématu Nasazení a procházení kurzu [šablony aplikace pro správu inteligentního inventáře.](./tutorial-iot-central-smart-inventory-management-pnp.md)

## <a name="micro-fulfillment-center"></a>Centrum mikroplnění

Ve stále konkurenčnějším maloobchodním prostředí čelí maloobchodníci neustále tlaku, aby překlenuli propast mezi poptávkou a naplněním. Nový trend, který se objevil k řešení rostoucí poptávky spotřebitelů je dům zásob v blízkosti koncových zákazníků a obchodů, které navštěvují.

Šablona aplikace Centra pro mikroplnění IoT Central umožňuje tvůrcům řešení monitorovat a spravovat všechny aspekty svých plně automatizovaných center plnění. Šablona obsahuje sadu simulovaných senzorů pro sledování stavu a robotických nosičů pro urychlení procesu vývoje řešení. Tato senzorová zařízení zachycují smysluplné signály, které lze převést na obchodní poznatky, což maloobchodníkům umožňuje snížit provozní náklady a vytvářet prostředí pro své zákazníky.

Šablona aplikace umožňuje: 

- Bezproblémově připojte různé druhy ioT senzorů, jako jsou roboty nebo senzory sledování stavu, k instanci aplikace IoT Central.
- Monitorujte a spravujte stav sítě senzorů a všech zařízení brány v prostředí.
- Vytvořte vlastní pravidla týkající se podmínek prostředí v centru plnění a aktivujte příslušné výstrahy.
- Přeměňte podmínky prostředí v rámci centra plnění na přehledy, které může využít tým maloobchodních skladů.
- Exportujte agregované přehledy do stávajících nebo nových obchodních aplikací ve prospěch zaměstnanců maloobchodu.

Následující snímek obrazovky ukazuje předem povislého řídicího panelu v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval vaše specifické požadavky na řešení:

> [!div class="mx-imgBorder"]
> ![Centrum mikroplnění](./media/overview-iot-central-retail/MFC-Dashboard.png)

Další informace najdete v tématu Nasazení a projděte si kurz [šablony aplikace centra pro mikroplnění.](./tutorial-micro-fulfillment-center-pnp.md)

## <a name="next-steps"></a>Další kroky

Jak začít vytvářet maloobchodní řešení:

* Začínáme s kurzem [Vytvoření analýzy v obchodě v Azure IoT Central,](./tutorial-in-store-analytics-create-app-pnp.md) který vás provede vytvořením řešení s jednou ze šablon analytických aplikací v obchodě.
* [Nasazujte a projděte si šablonu připojené logistické aplikace](./tutorial-iot-central-connected-logistics-pnp.md).
* [Nasazení a procházení šablony aplikace digitálního distribučního centra](./tutorial-iot-central-digital-distribution-center-pnp.md).
* [Nasazujte a procházete šablonu aplikace pro inteligentní správu zásob](./tutorial-iot-central-smart-inventory-management-pnp.md).
* [Nasazujte a projděte si šablonu aplikace centra pro mikroplnění](./tutorial-micro-fulfillment-center-pnp.md).
* Další informace o IoT Central najdete v [přehledu IoT Central](../preview/overview-iot-central.md).
