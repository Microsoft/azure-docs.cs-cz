---
title: Co jsou maloobchodní řešení Azure IoT Central | Microsoft Docs
description: Přečtěte si o používání šablon aplikací pro Azure IoT Central k vytvoření připojené logistiky, digitálního distribučního centra, analýz v obchodě, monitorování podmínek, rezervací, správy inteligentních inventářů a maloobchodních řešení.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 56198cf661001bcfcec45d483d0f691234851894
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832279"
---
# <a name="what-are-the-iot-central-retail-solutions"></a>Co jsou maloobchodní řešení IoT Central?

Azure IoT Central je aplikační platforma IoT, která snižuje zatížení a náklady spojené s vývojem, správou a údržbou řešení IoT na podnikové úrovni. Když zvolíte sestavení pomocí Azure IoT Central, získáte možnost zaměřit se na vaše podnikání, peníze a energii při transformaci vaší firmy na data IoT, a ne jenom zachovat a aktualizovat složitou a nepřetržitou vývojovou infrastrukturu IoT.

Tento článek popisuje několik šablon aplikací IoT Central specifických pro maloobchodní prodej. Jako tvůrce řešení můžete tyto šablony použít k sestavování řešení IoT, která optimalizují dodavatelské řetězce, vylepšit prostředí v obchodě pro zákazníky a efektivněji sledovat inventář.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Maloobchodní přehled Azure IoT":::

Následující části popisují možnosti těchto šablon aplikací:

## <a name="connected-logistics"></a>Propojená logistika

U celkové logistické útraty se očekává dosažení $10 600 000 000 000 v 2020. Přeprava nákladových účtů pro většinu těchto výdajů a poskytovatelů námořní dopravy je v rámci výrazného konkurenčního tlaku a omezení.

Pomocí senzorů IoT můžete shromažďovat a monitorovat okolní podmínky, jako je teplota, vlhkost, náklon, náraz, světlo a umístění dodávky. Telemetrii získaná ze senzorů a zařízení IoT můžete kombinovat s dalšími zdroji dat, jako jsou například informace o počasí a provozu v cloudových business intelligencech systémech.

Mezi výhody připojeného logistického řešení patří:

* Monitorování dodávek se sledováním v reálném čase 
* Zajištění neporušenosti dodávek díky monitorování okolních podmínek v reálném čase
* Zabezpečení proti krádeži, ztrátě nebo poškození dodávek
* Monitorování geografických zón, optimalizace tras, správa flotily a analytické informace o vozidlech
* Prognózování s cílem předvídat odbavení a doručení dodávek

Následující snímky obrazovky zobrazují předem připravený řídicí panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval konkrétní požadavky řešení:

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="Snímek obrazovky, který zobrazuje horní polovinu řídicího panelu připojené logistické operace.":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="Snímek obrazovky zobrazující spodní polovinu řídicího panelu připojené logistické operace.":::

Další informace najdete v kurzu [nasazení a Projděte si článek s připojenou logistickou aplikací](./tutorial-iot-central-connected-logistics.md) .

## <a name="digital-distribution-center"></a>Digitální distribuční centrum

Výrobci a prodejci si naváží celosvětové prostředí a jejich dodavatelské řetězy vystaví a stanou se složitějšími. Příjemci teď očekávají velké výběry produktů, které jsou dostupné, a pro tyto předměty dorazí do jednoho nebo dvou dnů od nákupu. Distribuční centra se musí přizpůsobit těmto trendům a přitom přejdou stávající neefektivity.

V současné době se spoléhá na ruční práci, což znamená, že účty pro výdej a balení 55-65% nákladů služby Distribution Center. Ruční výdej a balení jsou také obvykle pomalejší než automatizované systémy a rychlé kolísání potřeb zaměstnanců je ještě obtížnější, aby se objemy přenosu splňovaly. Tato sezónní kolísání vede k dosažení vysokého počtu zaměstnanců a zvyšuje pravděpodobnost nákladných chyb.

Řešení založená na fotoaparátech s podporou IoT můžou poskytovat transformační výhody tím, že umožňují smyčku digitální zpětné vazby. Data z v rámci distribučního centra vede k užitečným přehledům, které naopak mají za následek lepší data.

K výhodám digitálního distribučního centra patří:

* Kamery monitorují zboží po doručení a pohyb přes systém dopravníku.
* Automatická identifikace vadných zboží
* Efektivní sledování pořadí.
* Snížené náklady, Vylepšená produktivita a optimalizované využití.

Následující snímek obrazovky ukazuje předem připravený řídicí panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval konkrétní požadavky řešení: 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="Řídicí panel Digital Distribution Center":::

Další informace najdete v kurzu [nasazení a procházení prostřednictvím šablony aplikace digitálního distribučního centra](./tutorial-iot-central-digital-distribution-center.md) .

## <a name="in-store-analytics---condition-monitoring"></a>Analýzy v obchodě – monitorování podmínek

V případě mnoha prodejců jsou podmínky prostředí v rámci svých obchodů klíčovým rozdílem od jejich konkurence. Maloobchodní prodejci chtějí zachovat ve svých obchodech zvýhodněné podmínky pro využívání svých zákazníků.  

Jako tvůrce řešení můžete použít šablonu aplikace monitorování podmínky analýzy IoT Central v rámci úložiště k sestavení uceleného řešení. Šablona aplikace vám umožní digitálně se připojit k prodejnímu prostředí pro prodejny a monitorovat je pomocí různých druhů zařízení snímačů. Tato zařízení snímače generují telemetrii, kterou můžete převést na obchodní přehledy, které pomáhají maloobchodníkovi snižovat provozní náklady a vytvářet skvělé prostředí pro zákazníky.

Použijte šablonu aplikace k těmto akcím:

* Připojte různé druhy senzorů IoT k instanci aplikace IoT Central.
* Monitorujte a spravujte stav sítě snímačů a všech zařízení brány v prostředí.
* Vytvořte si vlastní pravidla týkající se podmínek prostředí v rámci obchodu, abyste mohli aktivovat výstrahy pro správce Storu.
* Transformujte podmínky prostředí v obchodě na přehledy, které může tým maloobchodního obchodu využít ke zlepšení prostředí pro zákazníky.
* Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací a poskytněte jim užitečné informace pro maloobchodní pracovníky.

Šablona aplikace je dodávána se sadou šablon zařízení a používá sadu simulovaných zařízení k naplnění řídicího panelu. 

Následující snímek obrazovky ukazuje předem připravený řídicí panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval konkrétní požadavky řešení: 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="Monitorování podmínek analýzy v úložišti":::

Další informace najdete v kurzu [Vytvoření analytické aplikace v rámci úložiště v Azure IoT Central](./tutorial-in-store-analytics-create-app.md) .

## <a name="in-store-analytics---checkout"></a>Analýza v obchodě – rezervace

U některých prodejců jsou možnosti rezervace v rámci svých obchodů klíčovým rozdílem od jejich konkurence. Maloobchodní prodejci chtějí poskytovat hladké možnosti rezervace v rámci svých obchodů, aby mohli zákazníkům povzbudit návrat.  

Jako tvůrce řešení můžete použít šablonu aplikace pro rezervaci IoT Central in-Store Analytics k sestavení řešení, které poskytuje přehledy z oblasti rezervace obchodu pro maloobchodní pracovníky. Senzory můžou například poskytovat informace o délkách fronty a průměrné době čekání pro každou rezervaci.

Použijte šablonu aplikace k těmto akcím:

* Připojte různé druhy senzorů IoT k instanci aplikace IoT Central.
* Monitorujte a spravujte stav sítě snímačů a všech zařízení brány v prostředí.
* Vytvořte vlastní pravidla týkající se podmínky rezervace v rámci obchodu, která aktivuje výstrahy pro pracovníky v maloobchodě.
* Transformujte podmínky rezervace v rámci Storu na přehledy, které může tým maloobchodního obchodu využít ke zlepšení prostředí pro zákazníky.
* Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací a poskytněte jim užitečné informace pro maloobchodní pracovníky.

Šablona aplikace je dodávána se sadou šablon zařízení a používá sadu simulovaných zařízení k naplnění řídicího panelu daty obsazení dráhy. 

Následující snímek obrazovky ukazuje předem připravený řídicí panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval konkrétní požadavky řešení: 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="Rezervace analýz v úložišti":::

Další informace najdete v kurzu [Vytvoření analytické aplikace v rámci úložiště v Azure IoT Central](./tutorial-in-store-analytics-create-app.md) .

## <a name="smart-inventory-management"></a>Inteligentní řízení zásob

Inventář je akcie zboží, které prodejce uchovává. Správa inventáře je kritická, aby se zajistilo, že je správný produkt na správném místě ve správnou dobu. Maloobchodní prodejce musí vyrovnávat náklady na ukládání příliš velkého množství inventáře, a to za cenu, která nemá dostatek položek na skladě, aby splnila požadavky.

Data IoT vygenerovaná z značek radiofrekvenční identifikace (RFID), signály a fotoaparáty poskytují příležitosti pro zlepšení procesů správy inventáře. Telemetrii získaná ze senzorů a zařízení IoT můžete kombinovat s dalšími zdroji dat, jako jsou například informace o počasí a provozu v cloudových business intelligencech systémech.

Mezi výhody správy inteligentních zásob patří:

* Snížení rizika nedostatku položek na sklad a zajištění požadované úrovně služeb pro zákazníky. 
* Podrobné analýzy a přehledy o přesnosti inventáře téměř v reálném čase.
* Nástroje, které vám pomůžou se rozhodnout o správném množství inventáře, aby odpovídal zákaznickým objednávkám

Tato šablona aplikace se zaměřuje na připojení zařízení a konfiguraci a správu zařízení čtečky s nízkou spotřebou (v/v) zařízení s technologií Bluetooth.

Následující snímek obrazovky ukazuje předem připravený řídicí panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval konkrétní požadavky řešení:

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="Řídicí panel pro správu inteligentního inventáře":::

Další informace najdete v kurzu [nasazení a procházení pomocí šablony aplikace pro správu inteligentního inventáře](./tutorial-iot-central-smart-inventory-management.md) .

## <a name="micro-fulfillment-center"></a>Centrum mikroplnění

Ve stále rostoucí maloobchodní konkurenci mají maloobchodníci nepřetržitý tlak na uzavírání mezer mezi poptávkou a plněním. Nový trend, který se ukázal na rostoucí poptávku spotřebitelů, je vytvoření inventáře poblíž koncových zákazníků a obchodů, které navštíví.

Šablona aplikace IoT Centralého centra vyplňování umožňuje tvůrcům řešení monitorovat a spravovat všechny aspekty jejich plně automatizovaného centra pro plnění. Šablona obsahuje sadu simulovaných senzorů monitorování a automatických dopravců pro zrychlení procesu vývoje řešení. Tato zařízení snímače zachycují smysluplné signály, které je možné převést na obchodní přehledy, což umožňuje maloobchodníkům snižovat provozní náklady a vytvářet prostředí pro své zákazníky.

Šablona aplikace vám umožní: 

- Bez problémů spojíte různé druhy senzorů IoT, jako jsou snímače robotů nebo monitorování podmínek, do instance aplikace IoT Central.
- Monitorujte a spravujte stav sítě snímačů a všech zařízení brány v prostředí.
- Vytvořte si vlastní pravidla týkající se podmínek v oblasti životního prostředí v rámci centra plnění, která aktivuje příslušné výstrahy.
- Transformujte podmínky prostředí v rámci vašeho centra plnění na přehledy, které může využívat tým maloobchodního skladu.
- Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací pro výhody maloobchodních zaměstnanců.

Následující snímek obrazovky ukazuje předem připravený řídicí panel v šabloně aplikace. Řídicí panel je plně přizpůsobitelný tak, aby splňoval konkrétní požadavky řešení:

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="Centrum Micro-vyplňování":::

Další informace najdete v kurzu [nasazení a procházení pomocí šablony aplikace centra vyplňování](./tutorial-micro-fulfillment-center.md) .

## <a name="video-analytics---object-and-motion-detection"></a>Analýza videa – detekce objektů a pohybu

Šablona aplikace *IoT Central video Analytics – objekt a detekce pohybu* vám umožní rychle se naučit, jak nasadit, spravovat a monitorovat řešení, které využívá inteligentní hraniční fotoaparáty ke zjišťování objektů a pohybu.

Aplikace video Analytics používá modul [lva (Live video Analytics)](#live-video-analytics) běžící v IoT Edge. Modul LVA poskytuje platformu pro sestavování inteligentních aplikací pro video, které přesahují hranice a Cloud. Platformu můžete použít ke zlepšení řešení IoT, jako je například aplikace video Analytics, s detekcí objektů a pohybů.

Šablona aplikace zahrnuje čtyři řídicí panely aplikace:

* **Začínáme** poskytuje odkazy na prostředky, které vám pomohou začít používat šablonu aplikace.
* **Ukázka řídicího panelu** poskytuje ilustraci typů informací, které můžete zobrazit z připojených fotoaparátů.
* **(Ukázka) Správa reálné kamery** používá simulované kamery k zobrazení, jak můžete spravovat kamery z aplikace.
* **(Ukázka) monitor reálné kamery** používá simulované kamery k zobrazení, jak můžete monitorovat kamery z aplikace.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="Analýza videa – detekce objektů a pohybu":::

Další informace o architektuře řešení najdete v tématu [Architektura aplikace pro video Analytics](architecture-video-analytics.md).

Informace o tom, jak nasadit řešení, najdete v kurzu [Vytvoření aplikace video Analytics v Azure IoT Central](tutorial-video-analytics-deploy.md) .

### <a name="live-video-analytics"></a>Analýza živých videí

[Live video Analytics](https://github.com/Azure/live-video-analytics) nabízí platformu pro vytváření inteligentních aplikací pro video, které přesahují hranice a Cloud. Platforma nabízí možnost zachytit, nahrávat, analyzovat živé video a publikovat výsledky, což může být video nebo video Analytics, ke službám Azure. Služby Azure by mohly běžet v cloudu nebo na hraničních zařízeních. Platformu můžete použít ke zlepšení řešení IoT pomocí analýzy videí.

## <a name="next-steps"></a>Další kroky

Začněte vytvářet maloobchodní řešení:

* Začněte s [vytvořením analytické aplikace v Azure IoT Central](./tutorial-in-store-analytics-create-app.md) kurzu, která vás provede postupem sestavení řešení pomocí jedné z šablon aplikací pro analýzu v rámci služby Store.
* [Nasaďte a Projděte si šablonu aplikace video Analytics](./tutorial-video-analytics-deploy.md).
* [Nasaďte a Projděte si připojenou šablonu logistické aplikace](./tutorial-iot-central-connected-logistics.md).
