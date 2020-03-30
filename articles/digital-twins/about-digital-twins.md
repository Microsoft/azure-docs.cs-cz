---
title: Přehled – Digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: V tomto článku najdete další informace o službě Azure Digital Twins, řešení Azure IoT pro prostorovou inteligenci.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 12/30/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: 91386eaf7753f2aefada2d1423a11b366c3ee924
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370895"
---
# <a name="overview-of-azure-digital-twins-preview"></a>Přehled azure digitálnídvojčata preview

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Azure Digital Twins Preview je služba Azure IoT, která vytváří komplexní modely fyzického prostředí. Může vytvářet grafy prostorové inteligence pro modelování vztahů a interakcí mezi lidmi, prostory a zařízeními.

S Azure Digital Twins můžete dotazovat data z fyzického prostoru, nikoli z mnoha různorodých senzorů. Tato služba vám pomůže vytvářet opakovaně použitelné, vysoce škálovatelné, prostorově uvědomované prostředí, které propojuje streamovaná data v digitálním a fyzickém světě. Vaše aplikace jsou vylepšeny těmito jedinečně relevantními kontextovými funkcemi. 

Azure Digital Twins platí pro všechny typy prostředí, jako jsou sklady, kanceláře, školy, nemocnice a banky. Může být dokonce použit pro stadiony, továrny, parkoviště, parky, inteligentní sítě a města. Níže jsou uvedeny některé scénáře, kde Azure Digital Twins může být užitečné:

- Předvídejte potřeby údržby továrny.
- Analyzujte energetické požadavky v reálném čase pro elektrickou síť.
- Optimalizujte využití dostupného prostoru pro kancelář.
- Sledujte denní teplotu v několika státech.
- Sledujte zaneprázdněné cesty dronů.
- Identifikujte autonomní vozidla.
- Analyzujte obsazenost budovy.
- Najděte nejrušnější pokladnu ve vašem obchodě.

Ať už je váš reálný obchodní scénář, je pravděpodobné, že odpovídající digitální instance může být zřízena prostřednictvím Azure Digital Twins.

Následující video se blíže podívat na Azure Digital Twins.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Klíčové funkce

Azure Digital Twins má následující klíčové funkce.

### <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

[*Graf prostorové inteligence*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph), neboli *prostorový graf*, je virtuální reprezentace fyzického prostředí. Můžete ji použít k modelování vztahů mezi lidmi, místy a zařízeními.

Zvažte inteligentní nástrojovou aplikaci, která zahrnuje několik měřičů spotřeby elektřiny připojených v okolí. Společnost inteligentních utilit musí přesně sledovat a předpovídat spotřebu a fakturaci elektřiny. Každé zařízení a senzor musí být modelováno podle kontextu o umístění a zákazníkovi, který má být fakturován. Graf prostorové inteligence můžete použít k modelování těchto typů složitých vztahů.

### <a name="digital-twin-object-models"></a>Objektové modely služby Digital Twins

[Digitální modely objektů dvojčete](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models) jsou předdefinované protokoly zařízení a schéma dat. Slaďují potřeby vašeho řešení specifické pro doménu, aby urychlily a zjednodušily vývoj.

Aplikace obsazenosti místnosti může například používat předdefinované typy prostor, jako je kampus, budova, podlaha a místnost.

### <a name="multiple-and-nested-tenants"></a>Více tenantů a vnoření tenanti

Můžete vytvářet řešení, která bezpečně škálují a lze je znovu použít pro více klientů. Můžete také vytvořit více podklientů, které lze přistupovat a používat izolovaným a bezpečným způsobem.

Příkladem je aplikace pro využití prostoru, která je nakonfigurovaná tak, aby izolovala data klienta od dat jiných klientů v rámci jedné budovy. Nebo se aplikace používá ke kombinování dat pro jednoho klienta s mnoha budovami.

### <a name="advanced-compute-capabilities"></a>Pokročilé výpočetní funkce

Pomocí [uživatelem definovaných funkcí](./concepts-user-defined-functions.md)můžete definovat a spouštět vlastní funkce proti příchozím [datům zařízení](./concepts-device-ingress.md) a odesílat signály do předdefinovaných koncových bodů. Tato pokročilá funkce zlepšuje přizpůsobení a automatizaci úloh zařízení.

Příkladem je inteligentní zemědělské aplikace, která zahrnuje uživatelem definovanou funkci pro posouzení hodnot čidla vlhkosti půdy a předpověď počasí. Aplikace pak vysílá signály o zavlažovacích potřebách.

### <a name="built-in-access-control"></a>Integrované řízení přístupu

Pomocí funkcí pro správu přístupu a identit, jako je [řízení přístupu na základě rolí](./security-role-based-access-control.md) a služba Azure Active [Directory](./security-authenticating-apis.md), můžete bezpečně řídit přístup pro jednotlivce a zařízení.

Příkladem je aplikace pro správu zařízení, která je nakonfigurována tak, aby umožnila cestujícím v místnosti nastavit teplotu v zadaném rozsahu. Správci zařízení mohou nastavit teplotu v každé místnosti na libovolnou hodnotu.

### <a name="ecosystem"></a>Ekosystém

Instanci Azure Digital Twins můžete připojit k mnoha výkonným službám Azure. Mezi tyto služby patří Azure Stream Analytics, Azure AI a Azure Storage. Patří mezi ně také Azure Maps, Microsoft Mixed Reality, Dynamics 365 nebo Office 365.

Příkladem je aplikace inteligentní kancelářské budovy, která používá Azure Digital Twins k reprezentaci týmů a zařízení umístěných na mnoha podlažích. Zatímco zařízení streamují živá data do zřízené instance Digital Twin, Stream Analytics tato data zpracovává, aby poskytla užitečné klíčové poznatky. Data se ukládají ve službě Azure Storage a převedou se na formát souboru, který lze sdílet. Soubor je distribuován v celé organizaci pomocí Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Řešení, kterým služba Azure Digital Twins přináší výhody

Azure Digital Twins je užitečná pro reprezentaci fyzického světa a jeho mnoha vztahů. Zjednodušuje modelování IoT, zpracování dat, zpracování událostí a sledování zařízení. Vezměme si jen několik z následujících scénářů v několika odvětvích. Mají prospěch z jeho použití:

* Ukažte společnosti spravující nemovitosti úroveň obsazenosti prostoru v průběhu času, abyste získali přehled o nejlepších způsobech konfigurace kancelářské budovy.
* Spusťte lístky pracovního příkazu pro mobilní aplikaci. Použijte jej k odeslání bezpečnostních stráží a plánování úklidových a dalších služeb v maloobchodním prostoru nebo sportovním areálu.
* Ukažte obyvateli budovy, které pokoje jsou v budově obsazeny v reálném čase. Poté pomozte cestujícímu rezervovat pracovní prostory, které vyhovují jejich potřebám.
* Sledujte, kde se datové zdroje nacházejí v prostoru.
* Optimalizujte nabíjení elektrických vozidel modelováním uživatelských preferencí a omezení energetické sítě.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Digitální dvojčata Azure v kontextu jiných služeb IoT

Azure Digital Twins se pomocí služby Azure IoT Hub připojuje k zařízením a senzorům IoT, které zajišťují stálou synchronizaci s fyzickým světem. Následující diagram znázorňuje, jak Azure Digital Twins souvisí s jinými službami Azure IoT.

[![Azure Digital Twins je služba postavená na službě Azure IoT Hub](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

Další informace o IoT načtou [technologie a řešení Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).

## <a name="next-steps"></a>Další kroky

Přejděte na krátkou ukázku o Azure Digital Twins:

>[!div class="nextstepaction"]
>[Úvodní příručka: Vyhledání dostupných pokojů pomocí Azure Digital Twins](./quickstart-view-occupancy-dotnet.md)

Podívejte se pozorně na aplikaci pro správu zařízení pomocí Azure Digital Twins:

>[!div class="nextstepaction"]
>[Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu](./tutorial-facilities-setup.md)

Seznamte se se základními koncepty služby Azure Digital Twins:

>[!div class="nextstepaction"]
>[Principy objektového modelu Digitální dvojčata a grafu prostorové inteligence](./concepts-objectmodel-spatialgraph.md)