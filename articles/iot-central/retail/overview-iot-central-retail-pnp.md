---
title: Vytváření maloobchodních řešení s využitím Azure IoT Central | Microsoft Docs
description: Naučte se sestavovat připojenou logistickou distribuci, digitálním centrem distribuce, monitorováním podmínek analýzy v obchodě, rezervacích a správě inteligentních inventářů, maloobchodních řešení s Azure IoT Central pomocí šablon aplikací.
author: KishorIoT
ms.author: nandab
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 7da5d7b80dfd07b742af5ff6225f26207747e58c
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615323"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Sestavování řešení pro maloobchod s využitím služby Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central je aplikační platforma pro Internet věcí (IoT) pro sestavovatele řešení, která zjednodušuje problémy při sestavování & správě škálovatelných aplikací. V tomto článku vyzvýrazníme několik šablon aplikací specifických pro maloobchodní prodej v rámci IoT Central. Tvůrci řešení můžou pomocí publikovaných šablon sestavovat řešení IoT pro optimalizaci dodavatelského řetězce, zlepšit prostředí v obchodě pro zákazníky a efektivněji sledovat inventář.

> [!div class="mx-imgBorder"]
> ![přehled maloobchodního prodeje Azure IoT](./media/overview-iot-central-retail/retail-app-templates.png)


## <a name="what-is-connected-logistics-solution"></a>Co je řešení pro logistiku připojené?
U celkové logistické útraty se očekává přístup k $10.6 trn v 2020, což je největší HDP podle odvětví. Přeprava zboží je většinou (70%) z celkových logistických nákladů. Poskytovatelé expedice mají silný konkurenční tlak a omezení. 3PL poskytovatelé se zabývat stále se zkrácením časových snímků a účtují náklady na kompenzaci. Logistika se dále omezuje riziky, které představují geopolitické, extrémní klimatické události a trestné činy. 

V rámci pomoci se senzory IoT můžeme shromažďovat & monitorovat okolní podmínky, tj. v oblasti teploty, vlhkosti, náklonu, nárazu, světla a umístění expedice, a to prostřednictvím GPS v rámci více modálních transformací, tj. vzduch, voda, země. Data získaná ze senzorů, zařízení, počasí a &ch událostí je možné integrovat s cloudovým systémem Business Intelligence. Výhody spojené logistické řešení jsou,
* Odvození dodávek pomocí sledování & trasování v reálném čase 
* Integrita dodávek pomocí monitorování podmínek okolí v reálném čase & studeným řetězcem
* Zabezpečení před odcizením, ztrátou nebo poškozením dodávky
* Monitorování geografických zón, optimalizace tras a řízení loďstva. Analýzy vozidel
* Prognózování & předvídatelnosti pomocí & pro odchodení 

### <a name="out-of-box-experience"></a>Prostředí mimo box
Partneři můžou využít šablonu k vývoji koncových a koncových řešení pro všechna připojená logistická řešení & popsaných výhodách. Tato publikovaná šablona se zaměřuje na připojení zařízení, konfiguraci & správě zařízení v IoT Central. 

> [!div class="mx-imgBorder"]
> Řídicí panel ![připojená logistika](./media/overview-iot-central-retail/connected-logistics-dashboard1.png)

> [!div class="mx-imgBorder"]
> Řídicí panel ![připojená logistika](./media/overview-iot-central-retail/connected-logistics-dashboard2.png)

Všimněte si, že výše uvedený řídicí panel je ukázka a vy můžete tuto aplikaci zcela přizpůsobit tak, aby odpovídala požadovanému případu použití.

Začínáme s [koncovým kurzem](./tutorial-iot-central-connected-logistics-pnp.md) , který vás provede vytvořením řešení s využitím jedné z šablon pro logistické řešení.



## <a name="what-is-digital-distribution-center-solution"></a>Co je to řešení Digital Distribution Center?
Jak si další výrobci a prodejci naváží celosvětové přítomnosti, jejich dodavatelské řetězce se rozvětvení na více než kdy dřív. Distribučním centrům se stává primární výzvou. Distribuční centrum/sklady si Brunt tlak od elektronického obchodování. Spotřebitelé teď očekávají velké množství výběrů produktů, které mají být dostupné, a za to, že se tyto produkty dojdou do jednoho až dvou dnů od nákupu. Distribuční centra se musí přizpůsobit těmto trendům a přitom přejdou stávající neefektivity. 

V současné době se při ruční práci na ruční práci označuje účty pro vybírání a balení pro 55-65% nákladů služby Distribution Center. I když je to špatné, že ruční práce zpomaluje distribuční centrum, rychle se měnící potřeby personálního oddělení (od zaměstnanců si 10X), zajistěte ještě obtížnější vyhovět expedičním objemům. Tato sezónní kolísání vede k vyššímu obratu a pravděpodobnosti chyb a zvyšuje se i nutnost nákladného přepracování.
Řešení založená na fotoaparátech s podporou IoT můžou poskytovat transformační výhody tím, že umožňují smyčku digitální zpětné vazby. Tento datový tok z celého distribučního centra vede k užitečným přehledům, které zase vede k lepšímu využití dat.

Výhody jsou, 
* Fotoaparáty monitorují zboží po doručení a pohyb přes systém dopravníku.
* Identifikujte chybné zboží a odešlete je k opravě
* Efektivní udržování přehledu o objednávkách
* Snížení nákladů, zvýšení produktivity & maximalizace využití

### <a name="out-of-box-experience"></a>Prostředí mimo box
Partneři mohou tuto šablonu aplikace využít k vytvoření digitálního distribučního centra, které vám umožní získat užitečné poznatky & výše popsané výhody. Publikovaná šablona se zaměřuje na konfiguraci připojení zařízení & správě kamery a hraničních zařízení v IoT Central. 

> [!div class="mx-imgBorder"]
> Řídicí panel ![digital Distribution Center](./media/overview-iot-central-retail/digital-distribution-center-dashboard.png)

Všimněte si, že výše uvedený řídicí panel je ukázka a vy můžete tuto aplikaci zcela přizpůsobit tak, aby odpovídala požadovanému případu použití.

Začínáme s [koncovým kurzem](./tutorial-iot-central-digital-distribution-center-pnp.md) , který vás provede vytvořením řešení s využitím jedné ze šablon digitálních distribučních center.



## <a name="what-is-in-store-analytics-condition-monitoring"></a>Co je monitorování podmínek analýzy in-Store?
V dnešní konkurenční konkurenci vyhledává maloobchodní prodejci nové způsoby, jak zákazníkům nabídnout jedinečné nebo zvláštní možnosti, aby mohli řídit provoz přes jejich fyzické obchody. Řada maloobchodních prodejců uznává důležitost podmínek v oblasti životního prostředí v obchodě jako nástroj, který je rozlišuje s konkurencí. Maloobchodní prodejci chtějí zajistit, aby ve všech obchodech udržovali nepřetržité podmínky, aby zákazníkům poskytovali příjemné zkušenosti.  

Šablona aplikace monitorování podmínky analýzy v rámci úložiště v rámci IoT Central poskytuje tvůrce řešení s plátnem, které se dá využít k sestavení kompletního řešení. Šablona aplikace jim umožní digitálně připojit a monitorovat prostředí pro maloobchodní obchod pomocí různých zařízení snímačů. Tato zařízení snímače zachycují smysluplné signály, které je možné převést na obchodní přehledy, což umožňuje maloobchodníkům snižovat provozní náklady a vytvářet prostředí, která jejich zákazníci chtějí.

Šablona aplikace vám umožní:

*  K instanci IoT Central aplikace můžete hladce připojit nejrůznější senzory IoT.
*  Monitorujte a spravujte stav sítě snímače i zařízení brány v prostředí.
*  Umožňuje vytvořit vlastní pravidla týkající se podmínek prostředí v rámci obchodu, aby se aktivovaly příslušné výstrahy.
*  Transformujte podmínky prostředí v obchodě na přehledy, které může využívat tým maloobchodního obchodu.
* Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací, které umožňují prodejcům v maloobchodních prodejích.

### <a name="out-of-box-experience"></a>Prostředí mimo box
Šablona aplikace je dodávána se sadou šablon zařízení a s operátorem, který se neobjeví v poli. Využívá sadu simulovaných zařízení k naplnění prvků řídicího panelu. Když nasadíte aplikaci IoT Central pomocí šablony aplikace [monitorování podmínky analýzy v úložišti](https://aka.ms/conditiontemplate) , budete mít na výchozím řídicím panelu aplikace, jak je znázorněno níže. 

> [!div class="mx-imgBorder"]
> Monitorování podmínky analýzy ![v úložišti](./media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png)

Všimněte si, že výše uvedený řídicí panel je ukázka a vy můžete tuto aplikaci zcela přizpůsobit tak, aby odpovídala požadovanému případu použití. 

Začínáme s [koncovým kurzem](./tutorial-in-store-analytics-create-app-pnp.md) , který vás provede vytvořením řešení s využitím jedné z šablon monitorování podmínky analýzy v úložišti.



## <a name="what-is-in-store-analytics-checkout"></a>Co je rezervace analýz v obchodě?
Ve stále rostoucí konkurenci mají moderní maloobchodníky neustále rostoucí tlak na zajištění prostředí v obchodě, které překračuje očekávání zákazníků a ukládá je zpátky. Zatímco několik prodejců začal nasazovat technologii, aby splnila tuto potřebu, je oblast, která je v podstatě neznáma, možností registrace.

Šablona aplikace pro rezervaci v rámci úložiště v rámci IoT Central umožňuje tvůrcům řešení vytvářet prostředí, která zákazníkům umožňují získat smysluplné přehledy o zóně rezervací jejich obchodu. Využívá sadu simulovaných zařízení k určení stavu obsazenosti každé z rezervací v rámci maloobchodního obchodu. Senzory umožňují zachytit počet osob a také průměrnou dobu čekání pro každou z rezervací.

Šablona pomáhá tvůrci řešení urychlit své plány pro uvedení na trh tím, že poskytuje základní řešení IoT, které jim umožní: 

* K instanci IoT Central aplikace můžete hladce připojit nejrůznější senzory IoT.
* Monitorujte a spravujte stav sítě snímače i zařízení brány v prostředí.
* Vytvořte vlastní pravidla týkající se podmínky rezervace v rámci úložiště, aby se aktivovaly příslušné výstrahy.
* Transformujte podmínky rezervace v rámci svého obchodu na přehledy, které může využívat tým maloobchodního obchodu.
* Exportujte agregované poznatky do stávajících nebo nových obchodních aplikací, které umožňují prodejcům v maloobchodních prodejích.

### <a name="out-of-box-experience"></a>Prostředí mimo box
Šablona aplikace je dodávána se sadou šablon zařízení a s operátorem, který se neobjeví v poli. Využívá sadu simulovaných zařízení k naplnění prvků řídicího panelu. Když nasadíte aplikaci IoT Central pomocí šablony aplikace pro [rezervaci v rámci úložiště](https://aka.ms/checkouttemplate) , bude se na výchozím řídicím panelu aplikace nakládat, jak vidíte níže. 

> [!div class="mx-imgBorder"]
> ![rezervace analýz v obchodě](./media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png)

Všimněte si, že výše uvedený řídicí panel je ukázka a vy můžete tuto aplikaci zcela přizpůsobit tak, aby odpovídala požadovanému případu použití. 


Začínáme s koncovým [kurzem](./tutorial-in-store-analytics-create-app-pnp.md) , který vás provede vytvořením řešení s využitím jedné z šablon pro rezervaci v rámci úložiště.


## <a name="what-is-smart-inventory-management-solution"></a>Co je řešení pro správu inteligentních inventářů?
"Inventář" je akcie zboží držené prodejcem. Každý prodejce potřebuje inventář, aby se postaral o poskytnutí a logistiku doby realizace. Inventář je pravděpodobně nejdůležitějším prostředkem, který každý prodejce potřebuje k obchodování. V dnešním omnichannel světě je Správa inventáře zásadním požadavkem, aby se zajistilo, že je správný produkt na správném místě ve správnou dobu. Ukládání velkého množství nebo příliš malého inventáře by mohlo snížit firemního prodejce. Maloobchodní maloobchodní prodejci ztratí 8-10% výnosů z důvodu nedostatku možností správy inventáře.

Data IoT, která jsou povolená identifikací rádiového kmitočtu (RFID), signalizace & kamera je příležitost řešit tuto velkou výzvu ve velkém měřítku. Připojení a analýzy v reálném čase vyplývající z signálů IoT se stávají Game měničem pro inventář prodejce maloobchodního Woes.  Data získaná ze senzorů, zařízení, počasí a &ch událostí je možné integrovat s cloudovým systémem Business Intelligence.  
Výhody správy inteligentního inventáře jsou, 
* Chrání organizaci před zásobami a zajišťuje požadovanou úroveň služeb pro zákazníky. 
* Hloubková analýza & přehledy o přesnosti inventáře téměř v reálném čase
* Rozhodnutí o správném množství inventáře, který postačuje objednávky zákazníka

### <a name="out-of-box-experience"></a>Prostředí mimo box
Partneři můžou využít šablonu k vývoji kompletních řešení pro správu inteligentních inventářů & popsaných výhodách. Tato publikovaná šablona se zaměřuje na připojení zařízení, konfiguraci & správu RFID & čtenáři s nízkými energií (v) v IoT Central. 

> [!div class="mx-imgBorder"]
> Řídicí panel pro správu inteligentního inventáře ![](./media/overview-iot-central-retail/smart-inventory-management-dashboard.png)

Všimněte si, že výše uvedený řídicí panel je ukázka a vy můžete tuto aplikaci zcela přizpůsobit tak, aby odpovídala požadovanému případu použití. 

Začínáme s [koncovým kurzem](./tutorial-iot-central-smart-inventory-management-pnp.md) , který vás provede vytvořením řešení s využitím jedné ze šablon pro správu inteligentního inventáře.


## <a name="next-steps"></a>Další kroky
Začněte vytvářet maloobchodní řešení:
* Začínáme s koncovým [kurzem](./tutorial-in-store-analytics-create-app-pnp.md) , který vás provede vytvořením řešení s využitím jedné z šablon aplikací pro analýzu v úložišti.
* Naučte se nasadit [připojenou šablonu logistického řešení](./tutorial-iot-central-connected-logistics-pnp.md) .
* Postup nasazení šablony nástroje [digital Distribution Center](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Naučte se nasadit [šablonu správy inteligentních inventářů](./tutorial-iot-central-smart-inventory-management-pnp.md) .
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central-pnp.md) .
