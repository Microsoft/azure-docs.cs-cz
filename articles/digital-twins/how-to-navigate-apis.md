---
title: Navigace v řešení API – digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak běžné vzory dotazování Azure Digitální dvojče správu API.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265165"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Jak používat azure digitální dvojčata správu API

Řešení API pro správu digitálních dvojčat Azure poskytují výkonné funkce pro vaše aplikace IoT. Tento článek ukazuje, jak procházet strukturu rozhraní API.  

## <a name="api-summary"></a>Souhrn rozhraní API

V následujícím seznamu jsou uvedeny součásti digitálních dvojčat API.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Tato řešení API interagují s fyzickými umístěními v nastavení. Ty vám pomohou vytvořit, odstranit a spravovat digitální mapování fyzických umístění ve formě [prostorového grafu](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Tato řešení API interagují se zařízeními v nastavení. Tato zařízení mohou spravovat jeden nebo více senzorů. Zařízení může být například váš telefon nebo snímač Raspberry Pi nebo brána Lora a tak dále.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Tato api vám pomohou komunikovat se senzory přidruženými k vašim zařízením a fyzickým umístěním. Senzory zaznamenávají a posílají okolní hodnoty, které pak mohou být použity k manipulaci s prostorovým prostředím.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Tato api vám pomohou nastavit prostředky, jako je například centrum IoT hub, pro vaši instanci Digitální dvojčata.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Tato řešení API umožňují přidružit rozšířené typy k objektům Digital Twins, chcete-li k těmto objektům přidat specifické charakteristiky. Tyto typy umožňují snadné filtrování a seskupování objektů v unovém počítači a vlastní funkce, které zpracovávají telemetrická data. Příklady rozšířených typů jsou *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*a tak dále.

* [/ontologie](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Tato api vám pomohou spravovat ontologie, které jsou kolekce rozšířených typů. Ontologie poskytují názvy pro typy objektů podle fyzického prostoru, který představují. Například *ontologie BACnet* poskytuje specifické názvy pro *typy senzorů*, *datové typy*, *podtypy dat*a datové *jednotky*. Ontologie jsou spravovány a vytvářeny službou. Uživatelé mohou načíst a uvolnit ontologie. Při načtení ontologie jsou povoleny všechny přidružené názvy typů a připraveny k zřízení v prostorovém grafu. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Tato rozhraní API můžete použít k vytvoření vlastních vlastností pro *vaše prostory*, *zařízení*, *uživatele*a *senzory*. Tyto vlastnosti jsou vytvořeny jako dvojice klíč/hodnota. Datový typ pro tyto vlastnosti můžete definovat nastavením jejich *typu PrimitiveDataType*. Můžete například definovat vlastnost s názvem *BasicTemperatureDeltaProcessingRefreshTime* typu *uint* pro vaše senzory a pak přiřadit hodnotu pro tuto vlastnost pro každý z vašich senzorů. Můžete také přidat omezení pro tyto hodnoty při vytváření vlastnosti, například *Min* a *Max* rozsahy, stejně jako povolené hodnoty jako *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Tato rozhraní API umožňují určit podmínky, které chcete vyhodnotit z dat příchozího zařízení. Další informace najdete v [tomto článku](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Tato rozhraní API umožňují vytvářet, odstraňovat nebo aktualizovat vlastní funkci, která se spustí, když dojde k podmínkám definovaným *matchery,* ke zpracování dat pocházejících z vašeho nastavení. Další informace o těchto vlastních funkcích, nazývaných také *uživatelem definované funkce*, naleznete v [tomto článku.](concepts-user-defined-functions.md#user-defined-functions) 

* [/koncové body:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints)Tato api umožňují vytvářet koncové body, takže vaše řešení digitálních dvojčat může komunikovat s jinými službami Azure pro ukládání dat a analýzy. Přečtěte si [tento článek](concepts-events-routing.md) pro více informací. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Tato api umožňují spravovat úložiště klíčů zabezpečení pro vaše prostory. Tyto obchody mohou obsahovat kolekci klíčů zabezpečení a umožňují snadno načíst nejnovější platné klíče.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Tato řešení API umožňují přidružit uživatele k vašim prostorům a v případě potřeby tyto osoby vyhledat. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Tato nastavení API umožňují spravovat nastavení celého systému, například výchozí typy prostorů a senzorů. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Tato rozhraní API umožňují přidružit role k entitám, jako je ID uživatele, ID uživatelem definované funkce atd. Každé přiřazení role zahrnuje ID entity, kterou chcete přidružit, typ entity, ID role, kterou chcete přidružit, ID klienta a cestu, která definuje horní limit prostředku, ke kterému může entita přistupovat s tímto přidružením. Přečtěte si [tento článek](security-role-based-access-control.md) pro více informací.


## <a name="api-navigation"></a>Navigace rozhraní API

Rozhraní API digitálních dvojčat podporují filtrování a navigaci v celém prostorovém grafu pomocí následujících parametrů:

- **spaceId**: Rozhraní API bude filtrovat výsledky podle daného ID místa. Kromě toho logický příznak **useParentSpace** je použitelný pro [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API, což znamená, že dané ID místa odkazuje na nadřazené místo aktuálního prostoru. 

- **minLevel** a **maxLevel**: Kořenové prostory jsou považovány za na úrovni 1. Mezery s nadřazeným prostorem na úrovni *n* jsou na úrovni *n+1*. S těmito nastavenými hodnotami můžete filtrovat výsledky na určitých úrovních. Jedná se o včetně hodnoty při nastavení. Zařízení, senzory a další objekty jsou považovány za na stejné úrovni jako jejich nejbližší prostor. Chcete-li získat všechny objekty na dané úrovni, nastavte **minLevel** a **maxLevel** na stejnou hodnotu.

- **minRelative** a **maxRelative**: Pokud jsou tyto filtry uvedeny, odpovídající úroveň je relativní k úrovni dané id prostoru:
   - Relativní úroveň *0* je stejná úroveň jako dané ID prostoru.
   - Relativní úroveň *1* představuje mezery na stejné úrovni jako podřízené id daného prostoru. Relativní úroveň *n* představuje mezery nižší než zadaný prostor *n* úrovní.
   - Relativní úroveň *-1* představuje mezery na stejné úrovni jako nadřazený prostor určeného prostoru.

- **procházet**: Umožňuje procházet v obou směrech z daného ID prostoru, jak je určeno následujícími hodnotami.
   - **Žádné**: Tato výchozí hodnota se filtruje na dané ID místa.
   - **Dolů**: Toto filtruje podle daného ID prostoru a jeho potomků. 
   - **Nahoru**: Toto filtruje podle daného ID prostoru a jeho předchůdců. 
   - **Rozsah**: Vyfiltruje se vodorovná část prostorového grafu na stejné úrovni jako dané ID prostoru. To potřebuje buď **minRelative** nebo **maxRelative,** které mají být nastaveny true. 


### <a name="examples"></a>Příklady

Následující seznam ukazuje některé příklady navigace prostřednictvím [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API. Všimněte si, `YOUR_MANAGEMENT_API_URL` že zástupný symbol odkazuje na identifikátor `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`URI `YOUR_INSTANCE_NAME` virtuálních dvojčat API ve formátu `YOUR_LOCATION` , kde je název instance Azure Digital Twins a je oblast, kde je hostovaná vaše instance.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`vrátí všechna zařízení připojená ke kořenovým prostorům.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`vrátí všechna zařízení připojená k prostorům úrovní 2, 3 nebo 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`vrátí všechna zařízení přímo připojená k mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`vrátí všechna zařízení připojená k mySpaceId nebo jednomu z jeho potomků.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`vrátí všechna zařízení připojená k potomkům mySpaceId, s výjimkou mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`vrátí všechna zařízení připojená k bezprostředním dětem mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`vrátí všechna zařízení připojená k jednomu z předků mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`vrátí všechna zařízení připojená k potomkům mySpaceId, která jsou na úrovni menší nebo rovna 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`vrátí všechna zařízení připojená k mezerám, které jsou na stejné úrovni jako mySpaceId.


## <a name="odata-support"></a>Podpora pro OData

Většina api, která vracejí kolekce, jako je například volání GET na /spaces, podporuje následující podmnožinu obecných možností systémových dotazů [OData:](https://www.odata.org/getting-started/basic-tutorial/#queryData)  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - Pokud máte v úmyslu zobrazit celou kolekci, měli byste požádat jako celou sadu v jednom volání a potom provést stránkování v aplikaci. 

> [!NOTE]
> Některé možnosti OData (například možnosti dotazu **$count**, **$expand**a **$search**) nejsou v současné době podporovány.

### <a name="examples"></a>Příklady

Následující seznam znázorňuje několik dotazů s platnou syntaxí OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět některé běžné vzory dotazů rozhraní API, přečtěte [si článek Jak se dotazovat rozhraní API azure digitálnídvojče pro běžné úkoly](./how-to-query-common-apis.md).

Další informace o koncových bodech rozhraní API najdete v článek [Použití funkce Digitální dvojčata Swagger](./how-to-use-swagger.md).

Chcete-li zkontrolovat syntaxi OData a dostupné operátory porovnání, přečtěte si [operátory porovnání OData v Azure Cognitive Search](../search/search-query-odata-comparison-operators.md).
