---
title: Přehled – digitální vlákna Azure | Microsoft Docs
description: V tomto článku najdete další informace o službě Azure Digital Twins, řešení Azure IoT pro prostorovou inteligenci.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 12/30/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: a3c696a5bfa9286e664f69bd379bac408b7459d6
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863236"
---
# <a name="overview-of-azure-digital-twins"></a>Přehled služby Azure Digital Twins

Azure Digital Preview je služba Azure IoT, která vytváří komplexní modely fyzického prostředí. Může vytvořit grafy prostorových informací pro modelování vztahů a interakcí mezi lidmi, mezerami a zařízeními.

Pomocí digitálních vláken Azure můžete zadávat dotazy na data z fyzického místa a nikoli z mnoha různorodých senzorů. Tato služba vám pomůže vytvořit opakovaně použitelné, vysoce škálovatelné a prostorové prostředí, které umožňuje propojit streamovaná data napříč digitálním a fyzickým světem. Vaše aplikace se zvyšují pomocí těchto jednoznačně relevantních kontextových funkcí. 

Digitální vlákna Azure se vztahují na všechny typy prostředí, jako jsou například sklady, kanceláře, školy, nemocnic a bank. Dá se použít i pro Stadium, továrny, parkovací dávky, parky, inteligentní mřížky a města. Níže jsou uvedeny některé scénáře, ve kterých může být užitečné digitální vlákna Azure:

- Předvídání potřeb údržby pro továrnu.
- Analyzujte požadavky na energii v reálném čase pro elektrickou mřížku.
- Optimalizuje používání dostupného místa pro Office.
- Sledovat každodenní teplotu v několika stavech.
- Monitorujte zaneprázdněné pomocí dronů cesty.
- Identifikujte autonomní vozidla.
- Analyzujte úrovně obsazení pro budova.
- Najděte si pokladnu nejvytíženější v úložišti.

Bez ohledu na to, jaký je skutečný podnikový scénář, je pravděpodobně možné zřídit odpovídající digitální instanci prostřednictvím digitálních vláken Azure.

Následující video se podrobněji podíváte na digitální vlákna Azure.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Klíčové funkce

Digitální vlákna Azure mají následující klíčové funkce.

### <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

[*Graf prostorových informací*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)nebo *prostorový graf*je virtuální reprezentace fyzického prostředí. Můžete ji použít k modelování vztahů mezi lidmi, místy a zařízeními.

Vezměte v úvahu aplikaci inteligentního nástroje, která zahrnuje několik měřičů spotřeby elektřiny připojených přes okolí. Společnost inteligentního nástroje musí přesně monitorovat a předpovědět využití elektřiny a fakturace. Každé zařízení a senzor musí být modelovány s využitím kontextu umístění a zákazníka, kterému se má fakturovat. Graf prostorových informací můžete použít k modelování těchto typů komplexních vztahů.

### <a name="digital-twin-object-models"></a>Objektové modely služby Digital Twins

[Digitální objekty s dvojitým datovým modelem](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models) jsou předdefinované protokoly zařízení a data schématu. Zarovnají a zjednodušují vývoj specifické pro doménu vašeho řešení.

Například aplikace pro obsazení místnosti může používat předdefinované typy prostorů, jako jsou areály, budovy, Podlahová plocha a místnost.

### <a name="multiple-and-nested-tenants"></a>Více tenantů a vnoření tenanti

Můžete vytvářet řešení, která se budou bezpečně škálovat a je možné je znovu použít pro více tenantů. Můžete také vytvořit více tenantů, které jsou k dispozici a používány izolovaně a zabezpečeným způsobem.

Příkladem je aplikace s využitím prostoru, která je nakonfigurovaná tak, aby izoluje data klienta z dat ostatních tenantů v rámci jedné budovy. Nebo se aplikace používá ke kombinování dat pro jednoho tenanta s mnoha budovami.

### <a name="advanced-compute-capabilities"></a>Pokročilé výpočetní funkce

Pomocí [uživatelsky definovaných funkcí](./concepts-user-defined-functions.md)můžete definovat a spouštět vlastní funkce pro příchozí [data zařízení](./concepts-device-ingress.md) a odesílat signály předem definovaným koncovým bodům. Tato pokročilá funkce vylepšuje přizpůsobení a automatizaci úloh zařízení.

Příkladem je aplikace inteligentního zemědělství, která zahrnuje uživatelsky definovanou funkci pro vyhodnocení čtení snímačů vlhkosti v půdě a předpovědi počasí. Aplikace pak pošle signály týkající se potřeb zavlažování.

### <a name="built-in-access-control"></a>Integrované řízení přístupu

Pomocí funkcí správy přístupu a identit, jako je [řízení přístupu na základě rolí](./security-role-based-access-control.md) a [Azure Active Directory](./security-authenticating-apis.md), můžete bezpečně řídit přístup jednotlivců a zařízení.

Příkladem je aplikace správy zařízení, která je nakonfigurovaná tak, aby umožňovala uživatelům místnosti nastavit teplotu v zadaném rozsahu. Správci zařízení můžou v každé místnosti nastavit teplotu na libovolnou hodnotu.

### <a name="ecosystem"></a>Ekosystém

Instanci digitálních vláken Azure můžete připojit k mnoha výkonným službám Azure. Mezi tyto služby patří Azure Stream Analytics, Azure AI a Azure Storage. Zahrnují taky Azure Maps, Microsoft Mixed reality, Dynamics 365 nebo Office 365.

Příkladem je aplikace pro vytváření inteligentních aplikací pro Office, která používá digitální vlákna Azure k reprezentaci týmů a zařízení umístěných v mnoha podlahách. Zařízení streamují živá data do zřízené digitálního vlákna, Stream Analytics zpracovává Tato data, aby poskytovala užitečné klíčové přehledy. Data jsou uložena v Azure Storage a převedena do formátu sdíleného souboru. Soubor se distribuuje napříč celou organizací pomocí Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Řešení, kterým služba Azure Digital Twins přináší výhody

Digitální vlákna Azure je užitečné pro vyjádření fyzického světa a jeho mnoha vztahů. Zjednodušuje modelování IoT, zpracování dat, zpracování událostí a sledování zařízení. V různých oborech zvažte jenom několik následujících scénářů. Využívají k tomu tyto výhody:

* Zobrazuje, jakou společnost má Správa vlastností na úrovni prostoru v čase až po glean přehledy o nejlepších způsobech konfigurace své kancelářské budovy.
* Aktivovat lístky pracovních objednávek pro mobilní aplikaci. Využijte ji k odesílání ochranných Guard a naplánování janitorial a dalších služeb v maloobchodním prostoru nebo na místě sportovního sportu.
* Ukažte na staveništi, které místnosti se v reálném čase zabírají v budově. Potom pomůžete uživatelům rezervovat pracovní prostory, které vyhovují jejich potřebám.
* Sledovat, kde se nacházejí prostředky v prostoru.
* Optimalizujte zpoplatnění elektrického vozidla modelováním uživatelských preferencí a omezeními pro energetickou mřížku.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Digitální vlákna Azure v kontextu jiných služeb IoT

Azure Digital Twins se pomocí služby Azure IoT Hub připojuje k zařízením a senzorům IoT, které zajišťují stálou synchronizaci s fyzickým světem. Následující diagram znázorňuje způsob, jakým se digitální vlákna Azure týkají jiných služeb Azure IoT.

[![digitálních vláken Azure je služba postavená na Azure IoT Hub](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

Další informace o IoT najdete v článku [technologie a řešení Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).

## <a name="next-steps"></a>Další kroky

Přejít na krátkou ukázku o digitálních Zdvojeních Azure:

>[!div class="nextstepaction"]
>[Rychlý Start: vyhledání dostupných místností pomocí digitálních vláken Azure](./quickstart-view-occupancy-dotnet.md)

Prohlédněte si úzce v aplikaci pro správu zařízení pomocí digitálních vláken Azure:

>[!div class="nextstepaction"]
>[Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu](./tutorial-facilities-setup.md)

Seznamte se se základními koncepty služby Azure Digital Twins:

>[!div class="nextstepaction"]
>[Seznamte se s digitálními objekty a graf prostorové logiky](./concepts-objectmodel-spatialgraph.md)