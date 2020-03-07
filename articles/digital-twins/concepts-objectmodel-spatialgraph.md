---
title: Principy modelů objektů a prostorových informací v grafu – digitální vlákna Azure | Microsoft Docs
description: Použití digitálních vláken Azure k modelování vztahů mezi lidmi, místy a zařízeními
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382272"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Informace o objektových modelech a grafu prostorové Intelligence pro digitální vlákna

Digitální vlákna Azure je služba Azure IoT, která umožňuje komplexní virtuální reprezentace fyzických prostředí a přidružených zařízení, senzorů a lidí. Zlepšuje vývoj díky uspořádání konceptů specifických pro doménu do užitečných modelů. Modely se pak nacházejí v grafu prostorové Intelligence. Tyto koncepty věrně modelují vztahy a interakce mezi lidmi, mezerami a zařízeními.

Klíčové modely digitálních vláken popisují koncepty, kategorie a vlastnosti specifické pro doménu. Modely jsou předdefinované uživateli, kteří chtějí řešení přizpůsobit podle svých konkrétních potřeb. Dohromady tyto předdefinované digitální vlákna tvoří objekty _Ontology_. Ontology pro inteligentní budova popisuje oblasti, místa, podlahy, pobočky, zóny, konferenční místnosti a místnosti pro výběr. Energetická mřížka Ontology popisuje různé napájecí stanice, podstanice, zdroje energie a zákazníky. U digitálních vláken objektů a ontologie je možné přizpůsobit různé scénáře a potřeby.

U digitálních vláken objektů a Ontology je možné naplnit _prostorový graf_. Prostorové grafy jsou virtuální reprezentace mnoha vztahů mezi mezerami, zařízeními a lidmi, které jsou relevantní pro řešení IoT. Tento diagram znázorňuje příklad prostorového grafu, který používá Ontology inteligentního sestavování.

[vytváření prostorových grafů ![ch digitálních vláken](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Prostorový graf se skládá z prostorů, zařízení, senzorů a uživatelů. Každá je propojená dohromady způsobem, který modeluje reálný svět. V této ukázce má místo 43 čtyři podlahová místa, z nichž každá má mnoho různých oblastí. Uživatelé jsou přidružení ke svým pracovním stanicím a mají přístup k částem grafu. Správce má oprávnění provádět změny prostorového grafu, zatímco návštěvník má práva pouze k zobrazení určitých dat o sestavách.

## <a name="digital-twins-object-models"></a>Digitální vlákna – objektové modely

Digitální vlákna objektových modelů podporují tyto hlavní kategorie objektů:

- **Mezery** jsou virtuální nebo fyzická umístění, například `Tenant`, `Customer`, `Region`a `Venue`.
- **Zařízení** jsou virtuální nebo fyzická zařízení, například `AwesomeCompany Device` a `Raspberry Pi 3`.
- **Senzory** jsou objekty, které zjišťují události, například `AwesomeCompany Temperature Sensor` a `AwesomeCompany Presence Sensor`.
- **Uživatelé** identifikují osoby a jejich charakteristiky.

Další kategorie objektů jsou:

- **Prostředky** jsou připojené k prostoru a obvykle představují prostředky Azure, které budou používat objekty v prostorovém grafu, například `IoTHub`.
- Objekty **BLOB** jsou připojené k objektům (například k prostorům, zařízením, senzorům a uživatelům). Používají se jako soubory typu MIME a metadata, například `maps`, `pictures`a `manuals`.
- **Rozšířené typy** jsou rozšiřitelné výčty, které rozšiřují entity o konkrétní charakteristiky, například `SpaceType` a `SpaceSubtype`.
- **Ontologie** představuje sadu rozšířených typů, například `Default`, `Building`, `BACnet`a `EnergyGrid`.
- **Klíče a hodnoty vlastností** jsou vlastní charakteristiky mezer, zařízení, senzorů a uživatelů. Dají se použít společně s vestavěnými charakteristikami, například `DeltaProcessingRefreshTime` jako klíč a `10` jako hodnota.
- **Role** jsou sady oprávnění přiřazené uživatelům a zařízením v prostorovém grafu, například `Space Administrator`, `User Administrator`a `Device Administrator`.
- **Přiřazení rolí** jsou přidružení mezi rolí a objektem v prostorovém grafu. Například uživateli nebo instančnímu objektu může být uděleno oprávnění ke správě prostoru v prostorovém grafu.
- **Úložiště klíčů zabezpečení** poskytují klíče zabezpečení pro všechna zařízení v hierarchii v rámci daného objektu prostor, aby bylo možné zařízení bezpečně komunikovat s digitálními podmnožinami.
- **Uživatelsky definované funkce** (UDF) umožňují přizpůsobitelné zpracování telemetrie senzorů v prostorovém grafu. Systém UDF může například:
  - Nastavte hodnotu snímače.
  - Proveďte vlastní logiku na základě čtených senzorů a nastavte výstup na mezeru.
  - Připojte metadata k prostoru.
  - Odeslat oznámení, pokud jsou splněny předdefinované podmínky. V současné době je možné UDF zapisovat do JavaScriptu.
- **Shody** jsou objekty, které určují, které UDF jsou spouštěny pro danou zprávu telemetrie.
- **Koncové body** jsou umístění, kde lze směrovat zprávy telemetrie a události digitálních vláken, například `Event Hub`, `Service Bus`a `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

Prostorový graf je hierarchický graf prostorů, zařízení a osob definovaných v objektovém modelu digitálních vláken. Prostorový graf podporuje dědičnost, filtrování, procházení, škálovatelnost a rozšiřitelnost. Pomocí kolekce rozhraní REST API můžete spravovat prostorový graf a pracovat s nimi.

Pokud ve svém předplatném nasadíte službu digitálního vlákna, stane se globální správce kořenového uzlu. Pak budete automaticky mít plný přístup k celé struktuře. Prostory v grafu zřídíte pomocí rozhraní API pro prostor. Pomocí rozhraní API pro senzory zřiďte služby pomocí rozhraní API a senzorů zařízení. K dispozici jsou také [Open Source nástroje](https://github.com/Azure-Samples/digital-twins-samples-csharp) pro zajištění hromadného zřízení grafu.

**Dědičnost grafu** Dědičnost se vztahuje k oprávněním a vlastnostem, které dovede od nadřazeného uzlu na všechny uzly pod ním. Například když je role přiřazena uživateli na daném uzlu, má uživatel oprávnění této role pro daný uzel a každý uzel pod ním. Každý klíč vlastnosti a rozšířený typ definovaný pro daný uzel je děděn všemi uzly pod tímto uzlem.

**Filtrování grafu**. Filtrování se používá k zúžení výsledků požadavků. Můžete filtrovat podle ID, názvu, typů, podtypů, nadřazeného prostoru a přidružených prostorů. Můžete také filtrovat podle datových typů senzorů, klíčů vlastností a hodnot, *procházením*, *minLevel*, *MaxLevel*a dalších parametrů filtru OData.

**Procházení grafů**. Prostorový graf můžete procházet jeho hloubkou a změnou šířky. Pro hloubku přesměrujte graf nahoru nebo dolů pomocí parametrů *Procházet*, *minLevel*a *maxLevel*. Procházením grafu můžete získat uzly na stejné úrovni přímo připojené k nadřazenému prostoru nebo jednomu z jeho potomků pro účely šířky. Při dotazování objektu můžete získat všechny související objekty, které mají relaci k tomuto objektu, pomocí parametru *include* rozhraní API Get.

**Škálovatelnost grafu**. Digitální vlákna zaručují škálovatelnost grafu, takže dokáže zvládnout reálné úlohy. Digitální vlákna se dají použít k reprezentování rozsáhlých portfolií v oblasti nemovitostí, infrastruktury, zařízení, senzorů, telemetrie a dalších.

**Rozšiřitelnost grafu** Pomocí rozšiřitelnosti můžete přizpůsobit základní modely objektů digitálních vláken novými typy a ontologie. Data digitálního vlákna je také možné rozšířit pomocí rozšiřitelných vlastností a hodnot.

### <a name="spatial-intelligence-graph-management-apis"></a>Rozhraní API pro správu diagramů prostorových informací

Po nasazení digitálních vláken z [Azure Portal](https://portal.azure.com)se automaticky vygeneruje adresa URL [Swagger](https://swagger.io/tools/swagger-ui/) rozhraní API pro správu. Zobrazuje se v Azure Portal v části **Přehled** v následujícím formátu.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Název | Nahradit hodnotou |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance digitální dvojče |
| YOUR_LOCATION | Jaké oblasti serveru vaší instance je hostován aplikací |

 V tomto obrázku se zobrazí úplný formát adresy URL.

[rozhraní API pro správu portálu ![Digital vlákna](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Další informace o tom, jak používat grafy prostorových informací, najdete v tématu rozhraní API pro správu digitálních vláken Azure vás zajímá ve verzi Preview.

> [!TIP]
> K dispozici je vás zajímá Swagger Preview, který předvádí sadu funkcí rozhraní API.
> Hostuje se na [docs.westcentralus.azuresmartspaces.NET/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Přečtěte si další informace o [použití Swagger](how-to-use-swagger.md).

Všechna volání rozhraní API musí být ověřena pomocí protokolu [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Rozhraní API se řídí [konvencemi pro REST API Microsoftu](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Většina rozhraní API, která vracejí kolekce, podporují možnosti dotazů na systém [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) .

## <a name="next-steps"></a>Další kroky

- Pokud se chcete dozvědět o připojení zařízení a o tom, jak odesílat zprávy telemetrie do digitálních vláken, přečtěte si téma [připojení zařízení k digitálním vstupům a telemetrie v Azure](concepts-device-ingress.md).

- Pokud se chcete dozvědět o omezeních a omezeních rozhraní API pro správu, přečtěte si téma [Azure Digital REprops API Management a omezení](concepts-service-limits.md).
