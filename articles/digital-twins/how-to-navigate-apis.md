---
title: Navigace v rozhraní API pro digitální vlákna Azure Microsoft Docs
description: Naučte se, jak běžné vzory dotazování rozhraní API pro správu digitálních vláken Azure.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: v-adgera
ms.openlocfilehash: 8472a86800d13cedd228ca881a7c095ff748350a
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172816"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Jak používat rozhraní API pro správu digitálních vláken Azure

Rozhraní API pro správu digitálních vláken Azure poskytují výkonné funkce pro vaše aplikace IoT. V tomto článku se dozvíte, jak procházet strukturu rozhraní API.  

## <a name="api-summary"></a>Souhrn rozhraní API

Následující seznam obsahuje komponenty rozhraní API digitálních vláken.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Tato rozhraní API komunikují s fyzickými umístěními v instalaci. Tyto informace vám pomůžou vytvořit, odstranit a spravovat digitální mapování vašich fyzických umístění ve formě [prostorového grafu](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Tato rozhraní API komunikují se zařízeními v nastavení. Tato zařízení můžou spravovat jeden nebo víc senzorů. Zařízení může být například telefon nebo senzor malinu PI nebo brána Lora vytvořené, a tak dále.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Tato rozhraní API vám pomůžou komunikovat se senzory přidruženými k vašim zařízením a fyzickými umístěními. Senzory zaznamenávají a odesílají okolní hodnoty, které se pak dají použít k manipulaci s prostorovým prostředím.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Tato rozhraní API vám pomůžou nastavit prostředky, jako je například IoT Hub, pro instanci digitálního vlákna.

* [/Types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Tato rozhraní API umožňují přidružit rozšířené typy k objektům digitálního vlákna a přidat k těmto objektům konkrétní vlastnosti. Tyto typy umožňují snadné filtrování a seskupení objektů v uživatelském rozhraní a vlastní funkce, které zpracovávají data telemetrie. Příklady rozšířených typů jsou *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*a tak dále.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Tato rozhraní API vám pomohou spravovat ontologie, což jsou kolekce rozšířených typů. Ontologie poskytují názvy pro typy objektů podle fyzického místa, které představují. Například *BACnet* Ontology poskytuje konkrétní názvy pro *typy senzorů*, DataTypes, *datasubtypes*a *dataunittypes*. Ontologie jsou spravované a vytvořené službou. Uživatelé můžou ontologie načíst a uvolnit. Když se načte Ontology, všechny jeho přidružené názvy typů jsou povolené a připravené ke zřízení v prostorovém grafu. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Pomocí těchto rozhraní API můžete vytvářet vlastní vlastnosti pro *prostory*, *zařízení*, *uživatele*a senzory. Tyto vlastnosti jsou vytvořeny jako páry klíč/hodnota. Můžete definovat datový typ pro tyto vlastnosti nastavením jejich *PrimitiveDataType*. Můžete například definovat vlastnost s názvem *BasicTemperatureDeltaProcessingRefreshTime* typu *uint* pro vaše senzory a potom přiřadit hodnotu pro tuto vlastnost pro každé ze senzorů. Můžete také přidat omezení pro tyto hodnoty při vytváření vlastnosti, jako jsou *minimální* a *maximální* rozsahy, a také povolené hodnoty jako *ValidationData*.

* [/Matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Tato rozhraní API umožňují zadat podmínky, které chcete vyhodnotit ze svých příchozích dat zařízení. Další informace najdete v [tomto článku](concepts-user-defined-functions.md#matchers) . 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Tato rozhraní API umožňují vytvořit, odstranit nebo aktualizovat vlastní funkci, která se spustí, když dojde k podmínkám definovaným *odpovídajícími* , ke zpracování dat přicházejících z nastavení. Další informace o těchto vlastních funkcích, označovaných také jako *uživatelsky definované funkce*, najdete v [tomto článku](concepts-user-defined-functions.md#user-defined-functions) . 

* [/Endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Tato rozhraní API umožňují vytvářet koncové body, aby vaše digitální vlákna mohla komunikovat s dalšími službami Azure pro ukládání a analýzu dat. Další informace najdete v [tomto článku](concepts-events-routing.md) . 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Tato rozhraní API umožňují spravovat úložiště klíčů zabezpečení pro vaše prostory. Tato úložiště můžou obsahovat kolekci klíčů zabezpečení a umožňují snadno načíst nejnovější platné klíče.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Tato rozhraní API umožňují přidružit uživatele k vašim prostorům a vyhledat tyto jednotlivce v případě potřeby. 

* [/](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Tato rozhraní API umožňují spravovat nastavení v rámci systému, jako jsou například výchozí typy prostorů a senzorů. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Tato rozhraní API umožňují přidružit role k entitám, jako je ID uživatele, ID uživatelsky definované funkce atd. Každé přiřazení role zahrnuje ID entity, která se má přidružit, typ entity, ID role k přidružení, ID tenanta a cestu definující horní limit prostředku, ke kterému může entita přistupovat. Další informace najdete v [tomto článku](security-role-based-access-control.md) .


## <a name="api-navigation"></a>Navigace rozhraní API

Rozhraní API digitálních vláken podporují filtrování a navigaci v celém prostorovém grafu pomocí následujících parametrů:

- **spaceId**: Rozhraní API bude filtrovat výsledky podle zadaného ID prostoru. Kromě toho logický příznak **useParentSpace** se vztahuje na rozhraní API [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) , což značí, že dané ID prostoru odkazuje na nadřazený prostor místo na aktuální místo. 

- **minLevel** a **maxLevel**: Kořenové prostory se považují za úroveň 1. Mezery s nadřazeným prostorem na úrovni *n* jsou na úrovni *n + 1*. Díky nastavením těchto hodnot můžete filtrovat výsledky na určitých úrovních. Jedná se o hodnoty, když se nastaví. Zařízení, senzory a další objekty se považují za stejné úrovně jako jejich nejbližší prostor. Chcete-li získat všechny objekty na dané úrovni, nastavte **minLevel** i **maxLevel** na stejnou hodnotu.

- **minRelative** a **maxRelative**: Když jsou tyto filtry uvedené, odpovídající úroveň je relativní vzhledem k úrovni daného ID prostoru:
   - Relativní úroveň *0* odpovídá stejné úrovni jako dané ID prostoru.
   - Relativní úroveň *1* představuje mezery na stejné úrovni jako podřízené objekty daného ID prostoru. Relativní úroveň *n* představuje mezery menší než zadané místo na úrovni *n* .
   - Relativní úroveň *-1* představuje mezery na stejné úrovni jako nadřazený prostor zadaného místa.

- **procházení**: Umožňuje procházet v obou směrech od daného ID prostoru, jak je uvedeno v následujících hodnotách.
   - **Žádný**: Tato výchozí hodnota se filtruje na dané ID prostoru.
   - **Dolů**: Tyto filtry mají zadané ID prostoru a jeho následníky. 
   - **Nahoru**: Tyto filtry se vyfiltrují podle zadaného ID prostoru a jeho nadřazených prvků. 
   - **Rozsah**: Tím se filtruje horizontální část prostorového grafu na stejné úrovni jako dané ID prostoru. K tomu je potřeba nastavit hodnotu true buď na **minRelative** , nebo na **maxRelative** . 


### <a name="examples"></a>Příklady

V následujícím seznamu jsou uvedeny některé příklady navigace prostřednictvím rozhraní [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API. Všimněte si, že `YOUR_MANAGEMENT_API_URL` zástupný symbol odkazuje na identifikátor URI digitálních vláken rozhraní API ve formátu `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, kde `YOUR_INSTANCE_NAME` je název vaší instance digitálního vlákna Azure a `YOUR_LOCATION` je to oblast, ve které je vaše instance hostovaná.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`vrátí všechna zařízení připojená ke kořenovým prostorům.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`vrátí všechna zařízení připojená k prostorům úrovně 2, 3 nebo 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`vrátí všechna zařízení přímo připojená k mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`vrátí všechna zařízení připojená k mySpaceId nebo k jednomu z jeho potomků.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`vrátí všechna zařízení připojená k následníkům mySpaceId s výjimkou mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`vrátí všechna zařízení připojená k přímým podřízeným položkám mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`vrátí všechna zařízení připojená k jednomu z nadřazených prvků mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`vrátí všechna zařízení připojená k následníkům mySpaceId, která jsou na úrovni menší nebo rovna 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`vrátí všechna zařízení připojená k mezerám, které jsou na stejné úrovni jako mySpaceId.


## <a name="odata-support"></a>Podpora OData

Většina rozhraní API, která vracejí kolekce, jako je například volání GET na/Spaces, podporují následující podmnožinu obecných možností dotazů systému [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) :  

* **$filter**
* **$orderby** 
* **$top**
* **$Skip** – Pokud máte v úmyslu zobrazit celou kolekci, měli byste ji požádat jako celek sadu v jednom volání a potom v aplikaci vytvořit stránkování. 

> [!NOTE]
> Některé možnosti OData (například možnosti dotazu **$Count**, **$expand**a **$Search**) nejsou aktuálně podporovány.

### <a name="examples"></a>Příklady

Následující seznam znázorňuje několik dotazů s platnou syntaxí OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 
## <a name="next-steps"></a>Další postup

Pokud se chcete dozvědět několik běžných vzorů dotazů rozhraní API, přečtěte si téma [Postup dotazování rozhraní API digitálních vláken Azure pro běžné úlohy](./how-to-query-common-apis.md).

Pokud chcete získat další informace o vašich koncových bodech rozhraní API, přečtěte si, [Jak používat digitální vlákna Swagger](./how-to-use-swagger.md).

Ke kontrole syntaxe OData a dostupných relačních operátorů si přečtěte [v Azure Search operátory porovnání OData](../search/search-query-odata-comparison-operators.md).
