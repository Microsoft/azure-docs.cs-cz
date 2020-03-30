---
title: Principy objektových modelů a grafu prostorové inteligence – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Použití Digitálních dvojčat Azure k modelování vztahů mezi lidmi, místy a zařízeními
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265204"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Principy objektových modelů digitálních dvojčat a grafu prostorové inteligence

Azure Digital Twins je služba Azure IoT, která podporuje komplexní virtuální reprezentace fyzických prostředí a přidružených zařízení, senzorů a lidí. Zlepšuje vývoj uspořádáním konceptů specifických pro doménu do užitečných modelů. Modely jsou pak umístěny v grafu prostorové inteligence. Takové koncepty věrně modelují vztahy a interakce mezi lidmi, prostory a zařízeními.

Digitální twins objektové modely popisují koncepty, kategorie a vlastnosti specifické pro doménu. Modely jsou předdefinovány uživateli, kteří chtějí přizpůsobit řešení svým specifickým potřebám. Společně tyto předdefinované digitální dvojče objektové modely tvoří _ontoologii_. Ontologie inteligentní budovy popisuje regiony, místa, podlahy, kanceláře, zóny, konferenční místnosti a zaostřovací místnosti. Ontologie energetické sítě popisuje různé elektrárny, rozvodny, energetické zdroje a zákazníky. S digitálními modely objektů dvojčat a ontologií lze přizpůsobit různé scénáře a potřeby.

S digitální dvojče objektové modely a ontologie na místě, můžete naplnit _prostorový graf_. Prostorové grafy jsou virtuální reprezentace mnoha vztahů mezi mezerami, zařízeními a lidmi, které jsou relevantní pro řešení IoT. Tento diagram znázorňuje příklad prostorového grafu, který používá ontoologii inteligentní budovy.

[![Digitální Twins prostorový graf budovy](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Prostorový graf spojuje prostory, zařízení, senzory a uživatele. Každý z nich je spojen způsobem, který modeluje skutečný svět. V tomto vzorku má místo 43 čtyři patra, z nichž každé má mnoho různých oblastí. Uživatelé jsou přidruženi ke svým pracovním stanicím a mají přístup k částem grafu. Správce má práva provádět změny prostorového grafu, zatímco návštěvník má práva k zobrazení pouze určitých dat budovy.

## <a name="digital-twins-object-models"></a>Digitální modely objektů Twins

Digitální twins objektové modely podporují tyto hlavní kategorie objektů:

- **Prostory** jsou virtuální nebo fyzická `Customer` `Region`umístění, `Venue` `Tenant`například , , a .
- **Zařízení** jsou například virtuální nebo fyzické `AwesomeCompany Device` `Raspberry Pi 3`vybavení a .
- **Senzory** jsou objekty, které `AwesomeCompany Temperature Sensor` detekují události, například a `AwesomeCompany Presence Sensor`.
- **Uživatelé** identifikují cestující a jejich charakteristiky.

Další kategorie objektů jsou:

- **Prostředky** jsou připojeny k prostoru a obvykle představují prostředky Azure, které mají `IoTHub`být použity objekty v prostorovém grafu, například .
- **Objekty BLOB** jsou připojeny k objektům (například prostorům, zařízením, senzorům a uživatelům). Používají se jako soubory s typem mime a `maps` `pictures`metadaty, například , , a `manuals`.
- **Rozšířené typy** jsou rozšiřitelné výčty, které rozšiřují entity `SpaceType` `SpaceSubtype`o specifické charakteristiky, například a .
- **Ontologie** `Default`představují sadu rozšířených typů, například , `EnergyGrid` `Building`, `BACnet`a .
- **Klíče vlastností a hodnoty** jsou vlastní charakteristiky prostorů, zařízení, senzorů a uživatelů. Lze je použít spolu s vestavěnými `DeltaProcessingRefreshTime` charakteristikami, `10` například jako klíč a jako hodnotu.
- **Role** jsou sady oprávnění přiřazených uživatelům a zařízením v `Space Administrator` `User Administrator`prostorovém grafu, například , a `Device Administrator`.
- **Přiřazení rolí** jsou přidružení mezi rolí a objektem v prostorovém grafu. Uživateli nebo instančnímu objektu služby může být například uděleno oprávnění ke správě prostoru v prostorovém grafu.
- **Úložiště klíčů zabezpečení** poskytují klíče zabezpečení pro všechna zařízení v hierarchii pod daným objektem prostoru, aby zařízení mohlo bezpečně komunikovat s digitálními dvojčaty.
- **Uživatelem definované funkce** (UD) umožňují přizpůsobitelné zpracování telemetrie senzoru v rámci prostorového grafu. Například UDF může:
  - Nastavte hodnotu senzoru.
  - Proveďte vlastní logiku založenou na odečtech senzorů a nastavte výstup na mezeru.
  - Připojte metadata k prostoru.
  - Posílejte oznámení, pokud jsou splněny předdefinované podmínky. V současné době lze UDFs psát v JavaScriptu.
- **Matchers** jsou objekty, které určují, které UDFs jsou spouštěny pro danou zprávu telemetrie.
- **Koncové body** jsou umístění, kde telemetrické zprávy a digitální dvojče `Event Hub` `Service Bus`události `Event Grid`mohou být směrovány, například , , a .

## <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

Prostorový graf je hierarchický graf prostorů, zařízení a osob definovaných v objektovém modelu Digitální dvojčata. Prostorový graf podporuje dědičnost, filtrování, procházení, škálovatelnost a rozšiřitelnost. Můžete spravovat a pracovat s prostorovým grafem s kolekcí rest API.

Pokud v předplatném nasadíte službu Digitální dvojčata, stanete se globálním správcem kořenového uzlu. Pak je automaticky udělen úplný přístup k celé struktuře. Zřizování mezer v grafu pomocí rozhraní Space API. Zřisti služby pomocí rozhraní API zařízení a senzory pomocí rozhraní API senzoru. [Open source nástroje](https://github.com/Azure-Samples/digital-twins-samples-csharp) jsou také k dispozici pro zřizování grafu ve velkém.

**Dědičnost grafu**. Dědičnost se vztahuje na oprávnění a vlastnosti, které sestupují z nadřazeného uzlu do všech uzlů pod ním. Například když je role přiřazena uživateli v daném uzlu, uživatel má oprávnění této role k danému uzlu a každému uzlu pod ním. Každý klíč vlastnosti a rozšířený typ definovaný pro daný uzel je zděděn všemi uzly pod tímto uzlem.

**Filtrování grafů**. Filtrování se používá k zúžení výsledků požadavku. Můžete filtrovat podle ID, názvu, typů, podtypů, nadřazeného prostoru a přidružených prostorů. Můžete také filtrovat podle datových typů senzorů, klíčů vlastností a hodnot, *procházení*, *minLevel*, *maxLevel*a dalších parametrů filtru OData.

**Graf projíždějící**. Prostorový graf můžete procházet jeho hloubkou a šířkou. Hloubka procházet grafem shora dolů nebo zdola nahoru pomocí parametrů *traverse*, *minLevel*a *maxLevel*. Procházenígrafu získat uzly na stejné úrovni přímo připojené k nadřazené prostoru nebo jeden z jeho potomků pro šířku. Při dotazování objektu můžete získat všechny související objekty, které mají vztahy k tomuto objektu pomocí *includes* parametr GET ROZHRANÍ API.

**Škálovatelnost grafu**. Digitální dvojčata garantuje škálovatelnost grafu, takže zvládne vaše skutečné úlohy. Digitální dvojčata lze použít k reprezentaci velkých portfolií nemovitostí, infrastruktury, zařízení, senzorů, telemetrie a dalších.

**Rozšiřitelnost grafu**. Rozšiřitelnost slouží k přizpůsobení podkladových objektůových modelů Digital Twins s novými typy a ontologiemi. Vaše data digitálních dvojčat mohou být také obohacena o rozšiřitelné vlastnosti a hodnoty.

### <a name="spatial-intelligence-graph-management-apis"></a>Graf prostorové inteligence Správu API

Po nasazení digitální dvojče z [portálu Azure](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) URL sanace API pro správu se automaticky vygeneruje. Zobrazuje se na webu Azure Portal v části **Přehled** s následujícím formátem.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name (Název) | Nahradit hodnotou |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance Digitální dvojčata |
| YOUR_LOCATION | Ve které oblasti serveru je instance hostována |

 Na tomto obrázku se zobrazí úplný formát adresy URL.

[![Rozhraní API pro správu portálu Digital Twins](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Další podrobnosti o tom, jak používat grafy prostorové inteligence, najdete na webu Azure Digital Twins Management API sneak preview.

> [!TIP]
> Swagger sneak preview je k dispozici k prokázání sady funkcí rozhraní API.
> Hostuje se v [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Další informace o [používání swaggeru](how-to-use-swagger.md).

Všechna volání rozhraní API musí být ověřena pomocí [oauth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Rozhraní API se řídí [konvencemi pokynů rozhraní API společnosti Microsoft REST](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Většina api, které vracejí kolekce, podporují možnosti systémových dotazů [OData.](https://www.odata.org/getting-started/basic-tutorial/#queryData)

## <a name="next-steps"></a>Další kroky

- Další informace o připojení zařízení a jak odesílat telemetrické zprávy digitálnídvojče, přečtěte si [připojení zařízení Azure Digital Twins a příchozí přenos dat telemetrie](concepts-device-ingress.md).

- Další informace o omezeních a omezeních rozhraní API pro správu rozhraní API pro správu a [omezení rozhraní Api pro](concepts-service-limits.md)správu azure digital twins .
