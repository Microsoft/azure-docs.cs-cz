---
title: Přejděte rozhraní API Azure digitální dvojče | Dokumentace Microsoftu
description: Zjistěte, jak běžné vzory dotazů na rozhraní API pro správu Azure digitální dvojče.
author: dsk-2015
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 2221e8b22450a353da42564e5b93342a11e59f71
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108318"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Jak používat rozhraní API pro správu Azure digitální dvojče

Rozhraní API pro správu Azure digitální dvojče poskytuje výkonné funkce pro vaše aplikace IoT. Tento článek popisuje, jak procházet strukturu rozhraní API.  

## <a name="api-summary"></a>Souhrn rozhraní API

Následující seznam obsahuje součásti digitální dvojče rozhraní API.

* [/ prostory](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Tato rozhraní API používat fyzických umístěních v nastavení aplikace. Ty umožňují vytvářet, odstraňovat a Správa digitální mapování fyzické umístění ve formě [prostorový graf](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Tato rozhraní API můžete nastavit prostředky, jako jsou centra IoT pro vaši instanci digitální dvojče.

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Tato rozhraní API komunikovat se zařízeními ve vašem nastavení. Tato zařízení můžou spravovat jeden nebo více senzory. Například zařízení může být váš telefon nebo pod senzor Raspberry Pi nebo brány Lora atd.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Tato rozhraní API umožňují komunikovat s senzorů spojené s vaším zařízením a fyzických umístěních. Snímačům zaznamenávat a odesílat okolí hodnoty, které lze použít k manipulaci s prostorových prostředí.  

* [/ typy](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Tato rozhraní API umožňují přidružit Rozšířené typy digitální dvojče objekty, chcete-li přidat specifické vlastnosti na tyto objekty. Tyto typy umožňují snadné filtrování a seskupování objektů v uživatelském rozhraní a vlastní funkce, které zpracovávají vaše telemetrická data. Příkladem rozšířeného typy jsou *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType* , *SpaceBlobType*, *SpaceResourceType*, a tak dále.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Tato rozhraní API pomáhají spravovat ontologie, což jsou kolekce typů rozšířené. Ontologie zadat názvy pro typy objektů podle fyzického místa, které reprezentují. Například *BACnet* ontology poskytuje konkrétní názvy *senzor typy*, *datové typy*, *datasubtypes*a *dataunittypes*. Ontologie jsou spravovaná a vytvořené službou. Uživatelé můžou načtení a uvolnění ontologie. Při načtení ontology všechny jeho přidruženého typu názvy jsou aktivní a připravena, které se mají zřídit v prostorových grafu. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Tato rozhraní API můžete použít k vytvoření vlastní vlastnosti pro vaše *prostory*, *zařízení*, *uživatelé*, a *senzorů*. Tyto vlastnosti jsou vytvořeny jako páry klíč/hodnota. Datový typ pro tyto vlastnosti můžete definovat tak, že nastavíte jejich *PrimitiveDataType*. Například můžete definovat vlastnost s názvem *BasicTemperatureDeltaProcessingRefreshTime* typu *uint* senzorů, a pak přiřadit hodnotu pro tuto vlastnost pro každý z vašich senzory. Omezení pro tyto hodnoty můžete také přidat při vytváření vlastnost, jako například *Min* a *maximální* rozsahy adres, a také povolených hodnot *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Tato rozhraní API umožňují zadat podmínky, které chcete vyhodnotit z příchozích dat zařízení. Zobrazit [v tomto článku](concepts-user-defined-functions.md#matchers) Další informace. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Tato rozhraní API umožňují vytvářet, delete nebo update vlastní funkci, která se spustí při podmínky definované *procesy pro hledání shody* dojít ke zpracování dat z vašeho nastavení. Naleznete v tématu [v tomto článku](concepts-user-defined-functions.md#user-defined-functions) Další informace o těchto vlastních funkcí, také nazývané *uživatelem definované funkce*. 

* [/Endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Tato rozhraní API umožňují vytváření koncových bodů, takže vaše řešení digitální dvojče může komunikovat s ostatními službami Azure pro ukládání dat a analýzu. Čtení [v tomto článku](concepts-events-routing.md) Další informace. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Tato rozhraní API umožňují spravovat zabezpečení klíčů úložiště pro vaše mezery. Těchto úložišť může obsahovat kolekci klíčů zabezpečení a umožňují snadno k dispozici nejnovější platný klíčů.

* [/ Uživatelé](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Tato rozhraní API umožňuje přidružit uživatele k prostory vaší najít těmto osobám v případě potřeby. 

* [/ System](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Tato rozhraní API umožňují spravovat nastavení pro systém, jako je například výchozí typy z mezer a senzorů. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Tato rozhraní API umožňují přiřadit role k entitám, například ID uživatele, ID uživatelem definované funkce atd. Každé přiřazení role obsahuje ID entity pro přidružení typu entity, ID role pro přidružení, ID tenanta a cestu, která definuje horní limit počtu prostředků, ke kterému se toto přidružení přístup entity. Čtení [v tomto článku](security-role-based-access-control.md) Další informace.


## <a name="api-navigation"></a>Rozhraní API navigace

Digitální dvojče rozhraní API podporují filtrování a navigace v rámci prostorový graf s následujícími parametry:

- **spaceId**: Rozhraní API bude filtrovat výsledky podle ID dané místo. Kromě toho logický příznak **useParentSpace** se vztahuje na [/prostory](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) rozhraní API, která označuje, že daný prostor ID odkazuje na nadřazený místo namísto aktuálního místa. 

- **minLevel** a **maxLevel**: Kořenové mezery jsou považovány za na úrovni 1. Mezery za nadřazené místo na úrovni *n* jsou na úrovni *n + 1*. Se sadou tyto hodnoty můžete filtrovat výsledky na určité úrovni. Toto jsou (včetně) hodnot při nastavení. Zařízení, senzorů a dalších objektů jsou považovány za na stejné úrovni jako jejich nejvíce místa. Pokud chcete získat všechny objekty na dané úrovni, jednak nastaveny **minLevel** a **maxLevel** na stejnou hodnotu.

- **minRelative** a **maxRelative**: Když se tyto filtry, odpovídající úroveň je relativní vzhledem k úrovni ID daného místa:
   - Relativní úroveň *0* je jako stejné úrovni jako ID dané místo.
   - Relativní úroveň *1* představuje mezer na stejné úrovni, jako potomci ID dané místo. Relativní úroveň *n* představuje prostory nižší než zadané místo podle *n* úrovně.
   - Relativní úroveň *-1* představuje mezer na stejné úrovni jako nadřazené místo zadaného místa.

- **procházení**: Umožňuje procházet v obou směrech z daného místa ID, podle následujících hodnot.
   - **Žádný**: Tato výchozí hodnota filtry ID dané místo.
   - **Dolů**: Tím vyfiltrujete podle ID daného místa a jejích potomků. 
   - **Až**: To se filtruje podle ID daného prostoru a jeho nadřazenými prvky. 
   - **Značka span**: Tím se vyfiltrují vodorovnou část prostorový graf na stejné úrovni jako ID dané místo. Tato hodnota musí buď **minRelative** nebo **maxRelative** chcete být nastavené na true. 


### <a name="examples"></a>Příklady

Následující seznam obsahuje několik příkladů navigaci [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) rozhraní API. Všimněte si, že zástupný symbol `YOUR_MANAGEMENT_API_URL` odkazuje na identifikátor URI digitální dvojče API ve formátu `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, kde `YOUR_INSTANCE_NAME` je název vaší instance Azure digitální dvojče a `YOUR_LOCATION` je oblast, kde se hostuje vaše instance.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` Vrátí všechna zařízení připojená k kořenové mezery.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` Vrátí všechna zařízení připojená k prostory úrovně 2, 3 nebo 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` Vrátí všechna zařízení, které jsou přímo připojené k mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` Vrátí všechna zařízení připojená k mySpaceId nebo jeden z jeho potomků.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` Vrátí všechna zařízení připojená k následníky mySpaceId, s výjimkou mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` Vrátí všechna zařízení připojená k bezprostředně podřízené mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` Vrátí všechna zařízení připojená k jednomu z nadřazených mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` Vrátí všechna zařízení připojená k následníky mySpaceId, které jsou na úrovni menší než nebo roven hodnotě 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` Vrátí všechna zařízení připojená k prostory, které jsou na stejné úrovni jako mySpaceId.


## <a name="odata-support"></a>Podporu protokolu OData
Většina rozhraní API, která vrací kolekce, jako je například volání GET /spaces, podporuje následující dílčí Obecné [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) možností dotazu systému:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** – Pokud chcete zobrazit celou kolekci, musí si je vyžádat jako celá sada v jednom volání a proveďte stránkování ve vaší aplikaci. 

Všimněte si, že rozbalíte další možnosti dotazu, jako je například $count, $, $search, nejsou podporovány.

### <a name="examples"></a>Příklady

Následující seznam obsahuje příklady dotazů pomocí možností dotazu systému OData na:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Další postup

Přečtěte si některé běžné vzory dotazů rozhraní API, přečtěte si téma [jak provádět dotazy digitální dvojče API služby Azure pro běžné úlohy](how-to-query-common-apis.md).


