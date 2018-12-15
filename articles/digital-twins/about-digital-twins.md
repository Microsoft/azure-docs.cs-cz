---
title: Přehled služby Azure Digital Twins | Microsoft Docs
description: V tomto článku najdete další informace o službě Azure Digital Twins, řešení Azure IoT pro prostorovou inteligenci.
author: julieseto
ms.author: jseto
ms.date: 12/14/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 2848f9ce97c2bdad59d86031c5894219875b6059
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437108"
---
# <a name="overview-of-azure-digital-twins"></a>Přehled služby Azure Digital Twins

Azure Digital Twins je služba Azure IoT, která vytváří komplexní modely fyzického prostředí. Je možné vytvořit prostorové řady grafů pro modelování vztahů a interakce mezi lidmi, mezery a zařízení.

S Azure digitální dvojče se můžete dotazovat data z fyzického místa na disku, nikoli z mnoha různorodých senzorů. Tato služba vám pomůže vytvořit opakovaně použitelný, vysoce škálovatelné a jinými objekty používající prostředí, které jsou propojeny streamovaná data po celém světě digitální i fyzické. Vaše aplikace jsou vylepšené tyto kontextové jednoznačně odpovídající funkce. Azure digitální Dvojčata je možné pro následující příklad úkoly:

- Předvídejte potřebu údržby pro objekt pro vytváření.
- Analyzujte požadavky na energie v reálném čase k elektrické mřížky.
- Optimalizujte využití volného místa pro office.

Azure digitální dvojče platí pro všechny typy prostředí. Jen několik příkladů jsou sklady, pobočky, školách, nemocnice a bank. Může dokonce sloužit pro stadionech, objekty pro vytváření, parkovací velké, parků, inteligentní mřížky a města. Azure digitální Dvojčata je možné v následující příklad scénáře:

- Sledovat denní teploty v rámci více státech.
- Monitorování zaneprázdněn dronu cest.
- Identifikujte autonomní vozidel.
- Analyzujte obsazení úrovně budovy.
- Najdete za nejvytíženější pokladna ve vašem úložišti.

Ať už je váš skutečný obchodní scénář jakýkoli, prostřednictvím služby Azure Digital Twins můžete zřídit odpovídající digitální instanci.

V následujícím videu trvá bližší pohled na digitální dvojče Azure.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Klíčové funkce

Azure digitální dvojče má následující klíčové funkce.

### <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

[ *Prostorové řady grafu*](./concepts-objectmodel-spatialgraph.md), nebo *prostorový graf*, je virtuální reprezentace fyzického prostředí. Můžete ho použít pro modelování vztahů mezi osoby, místa a zařízení.

Vezměte v úvahu aplikace s inteligentní nástroj, který zahrnuje několik měřiče využití elektrické energie napříč okolí. Inteligentní nástroj společnosti musí přesně sledovat a předpovídat využití elektrické energie a účtování. Každé zařízení a senzorů musí modelovat s kontextem o umístění a Zákazník, který se bude účtovat. Prostorové řady grafu můžete použít k modelování tyto druhy složité vztahy.

### <a name="digital-twin-object-models"></a>Objektové modely služby Digital Twins

[Digitální dvojče objektové modely](./concepts-objectmodel-spatialgraph.md) jsou předdefinované zařízení protokoly a data schématu. Zařízení vyhovují potřebám vašeho řešení specifického pro doménu k urychlení a zjednodušení vývoje.

Příkladem je že aplikace obsazení místa použít předdefinované místo typy jako campus, sestavování, floor a místnosti.

### <a name="multiple-and-nested-tenants"></a>Více tenantů a vnoření tenanti

Můžete vytvářet řešení, které bezpečně škálování a lze opětovně použít pro více tenantů. Můžete také vytvořit více subtenants, které jde přistupovat a používat izolované a zabezpečené způsobem.

Příkladem je aplikace využití místa, který je nakonfigurován k izolaci dat klienta od jiných klientů v rámci jednoho sestavení. Nebo aplikace se používá k kombinovat data pro jednoho tenanta s mnoha budovy.

### <a name="advanced-compute-capabilities"></a>Pokročilé výpočetní funkce

S [uživatelem definované funkce](./concepts-user-defined-functions.md), můžete definovat a spouštět vlastní funkce příchozí [data zařízení](./concepts-device-ingress.md) můžete posílat signály předdefinovaných koncových bodů. Tato rozšířená funkce zlepšuje přizpůsobení a automatizaci úloh zařízení.

Příkladem je inteligentní zemědělství aplikaci, která obsahuje uživatelem definovanou funkci k vyhodnocení údajů snímačů přes půdě vlhkosti a předpovědí počasí. Aplikace pak odešle signály o zavlažování potřebám.

### <a name="built-in-access-control"></a>Integrované řízení přístupu

Pomocí funkcí pro správu přístupu a identit, jako [řízení přístupu na základě rolí](./security-role-based-access-control.md) a [Azure Active Directory](./security-authenticating-apis.md), můžete bezpečně řídit přístup pro jednotlivce a zařízení.

Příkladem je aplikace pro správu zařízení, která nakonfigurována, aby umožňovala cestujících místnosti nastavit teploty do zadaného rozsahu. Zařízení správci můžou nastavit teplota v žádný prostor na libovolnou hodnotu.

### <a name="ecosystem"></a>Ekosystém

Instance digitální dvojče Azure můžete připojit k řadě služeb Azure výkonné. Mezi tyto služby patří Azure Stream Analytics, Azure AI a Azure Storage. Zahrnují taky, že Azure Maps, smíšené Reality Microsoft, Dynamics 365 nebo Office 365.

Příkladem je inteligentní office vytváření aplikace, která používá Azure digitální dvojče představující týmy a zařízení umístěných na mnoho podlahy. Jako zařízení streamování živých dat do zřízené instance digitální dvojče, Stream Analytics zpracuje tato data k poskytování klíčové přehledy. Data jsou uložená ve službě Azure Storage a převést do formátu ke sdílení souborů. Soubor je distribuován v celé organizaci v Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Řešení, kterým služba Azure Digital Twins přináší výhody

Azure digitální dvojče je užitečné pro představující fyzického světa a jeho mnoho relací. Zjednodušuje modelování, zpracování dat, zpracování událostí a sledování zařízení IoT. Vezměte v úvahu jenom některé z následujících scénářů v několika oborech. Výhody jeho použití pro:

* Zobrazit společnost úrovně obsazení prostoru v průběhu času glean přehledů o doporučené způsoby konfigurace jeho kancelářská budova.
* Aktivační událost pracovní lístky pořadí pro mobilní aplikace. Použijte ho k odeslání chrání zabezpečení a údržba plánu a dalším službám v prodejní místa nebo sportovní míst.
* Zobrazit sestavení cestujícího místnosti, které budou obsazeny v budově v reálném čase. Potom pomoct cestujícího rezervovat pracovní prostory, které podle jejich potřeb.
* Sledujte, kde se nachází v prostoru prostředky.
* Optimalizujte electric vozidla účtování podle uživatelských předvoleb a energetické síti omezení modelování.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Digitální dvojče Azure v rámci dalších služeb IoT

Azure Digital Twins se pomocí služby Azure IoT Hub připojuje k zařízením a senzorům IoT, které zajišťují stálou synchronizaci s fyzickým světem. Následující diagram znázorňuje, jak Azure digitální dvojče má vztah k jiné služby Azure IoT.

![Azure Digital Twins je služba postavená na službě Azure IoT Hub][1]

Další informace o IoT najdete v tématu [technologie Azure IoT a řešení](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Další postup

Přejdete na krátkou ukázku o digitální dvojče Azure:

>[!div class="nextstepaction"]
>[Rychlý start: Najít dostupné místnosti s použitím digitálních Dvojčat Azure](./quickstart-view-occupancy-dotnet.md)

Prohlédněte si blíže aplikace pro správu zařízení pomocí Dvojčat digitální Azure:

>[!div class="nextstepaction"]
>[Kurz: Digitální dvojče Azure nasadit a nakonfigurovat prostorový graf](./tutorial-facilities-setup.md)

Seznamte se se základními koncepty služby Azure Digital Twins:

>[!div class="nextstepaction"]
>[Principy digitální dvojče objektový model a Prostorové řady grafu](./concepts-objectmodel-spatialgraph.md)

<!-- Images -->
[1]: media/overview/azure-digital-twins-in-iot-ecosystem.png