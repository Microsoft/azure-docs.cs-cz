---
title: Principy digitální dvojče objektové modely a Prostorové řady grafu | Dokumentace Microsoftu
description: Využijte novou službu Azure Digital Twins k modelování vztahů mezi lidmi, místy a zařízeními
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 912a3ed558f8fabfcad517aeb7b7e864cf8f359e
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436921"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Principy digitální dvojče objektové modely a Prostorové řady grafu

Azure digitální dvojče je služba Azure IoT, která je základem komplexní virtuální reprezentace fyzického prostředí a přiřazená zařízení, senzory a lidí. Zdokonaluje vývoj uspořádáním koncepty specifického pro doménu do užitečné modelů. Tyto modely pak nacházejí Prostorové řady grafu. Tyto koncepty věrně modelovat vztahy a interakce mezi lidmi, mezery a zařízení.

Digitální dvojče objektové modely popisují koncepty specifického pro doménu, kategorie a vlastnosti. Uživatelé, kteří chtějí přizpůsobit řešení tak, aby jejich konkrétní potřeby jsou předdefinované modely. Společně tyto předdefinované digitální dvojče tvoří objektových modelů _ontology_. Chytré budovy ontology popisuje oblasti, místa, podlahy, pobočky, zóny, konferenčních místnostech dojde a místnosti fokus. Mřížce ontology energie popisuje různé elektrárny Rozvodny, zdrojů energie a zákazníky. Díky digitální dvojče objektové modely a ontologie je možné přizpůsobit nejrůznější scénáře a požadavky.

Díky digitální dvojče objektové modely a ontology na místě, která můžete naplnit _prostorový graf_. Prostorové grafy jsou virtuální reprezentace mnoho vztahů mezi mezerami, zařízení a osoby, které jsou relevantní pro řešení IoT. Tento diagram znázorňuje příklad prostorový graf, který používá ontology Chytré budovy.

![Digitální vytváření Dvojčat prostorový graf][1]

<a id="model"></a>

Prostorový graf spojuje mezery, zařízení, senzorů a uživatelů. Každá je způsobem, který modeluje reálného světa vzájemně propojený. V této ukázce příslušností 43 má čtyři podlažích, každý s mnoha různých oblastech. Uživatelé jsou spojené s jejich pracovní stanice a poskytnut přístup k části grafu. Správce má oprávnění ke změnám prostorový graf, zatímco návštěvník má práva k zobrazení pouze určitá data sestavení.

## <a name="digital-twins-object-models"></a>Digitální dvojče objektové modely

Digitální dvojče objektové modely podporují tyto hlavní kategorie objektů:

- **Mezery** jsou virtuální nebo fyzické umístění, například `Tenant`, `Customer`, `Region`, a `Venue`.
- **Zařízení** jsou části virtuálních nebo fyzických zařízení, například `AwesomeCompany Device` a `Raspberry Pi 3`.
- **Senzorů** jsou objekty, které zjišťování událostí, například `AwesomeCompany Temperature Sensor` a `AwesomeCompany Presence Sensor`.
- **Uživatelé** identifikaci osob a jejich vlastností.

Další kategorie objekty jsou:

- **Prostředky** jsou připojeny k mezerou a obvykle představují prostředky Azure používané objekty v prostorový graf, například `IoTHub`.
- **Objekty BLOB** jsou připojeny k objektům (např. mezery, zařízení, senzorů a uživatele). Slouží jako soubory s typ mime a metadata, například `maps`, `pictures`, a `manuals`.
- **Rozšířené typy** jsou extensible výčty, které rozšiřují entity s konkrétními vlastnostmi, třeba `SpaceType` a `SpaceSubtype`.
- **Ontologie** představují sadu typů rozšířené, například `Default`, `Building`, `BACnet`, a `EnergyGrid`.
- **Vlastnost klíče a hodnoty** jsou vlastní vlastnosti mezery, zařízení, senzorů a uživatelů. Jejich lze použít společně s integrované vlastnosti, například `DeltaProcessingRefreshTime` jako klíč a `10` jako hodnotu.
- **Role** jsou sady oprávnění přiřazená k uživatelům a zařízením v prostorový graf, například `Space Administrator`, `User Administrator`, a `Device Administrator`.
- **Přiřazení rolí** se přidružení mezi role a objekt v prostorových grafu. Například uživatel nebo instanční objekt služby můžete udělit oprávnění ke správě mezeru prostorových grafu.
- **Úložiště klíčů zabezpečení** poskytnutí klíčů zabezpečení pro všechna zařízení v hierarchii v rámci dané místo objektu a umožňuje zabezpečeně komunikovat se službou digitální dvojče zařízení.
- **Uživatelem definované funkce** (UDF) povolit telemetrii přizpůsobitelné senzor zpracování v rámci prostorový graf. Například můžete uživatelem definovanou FUNKCI:
  - Nastavte hodnotu senzoru.
  - Provést vlastní logiku podle údajů snímačů přes a nastavte výstup na znak.
  - Metadata se připojte k mezerou.
  - Odesílání oznámení, pokud předem definované podmínky jsou splněny. Uživatelem definovanými funkcemi v současné době je možné psát v jazyce JavaScript.
- **Procesy pro hledání shody** jsou objekty, které určují, které UDF jsou spouštěny pro danou telemetrické zprávy.
- **Koncové body** jsou umístění, ve kterém zprávy telemetrie a událostí digitálním Dvojčata je možné směrovat, například `Event Hub`, `Service Bus`, a `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

Prostorový graf je hierarchický graf mezery, zařízení a lidí definovaný v objektovém modelu digitální dvojče. Prostorový graf podporuje dědičnosti, filtrování, procházení, škálovatelnost a rozšiřitelnost. Můžete spravovat a interakci s vaší prostorový graf s sadu rozhraní REST API.

Pokud nasadíte službu, digitální dvojče ve vašem předplatném, stanete globálním správcem kořenového uzlu. Pak máte plný přístup k celé struktury automaticky udělena. Zřízení mezery v graphu pomocí rozhraní API místa. Zřizovat služby pomocí rozhraní API zařízení a senzorů s použitím rozhraní API senzoru. [Open source nástrojů](https://github.com/Azure-Samples/digital-twins-samples-csharp) jsou také dostupné ke zřizování grafu hromadně.

**Graf dědičnosti**. Dědičnost se vztahuje na oprávnění a vlastnosti, které sestup od nadřazeného uzlu na všechny uzly pod ním. Například při role je přiřazená uživateli v daném uzlu, má uživatel danou roli oprávnění pro daný uzel a každý uzel pod ní. Každý klíč vlastnosti a definované pro daný uzel Rozšířený typ zdědí všechny uzly pod tento uzel.

**Filtrování grafu**. Filtrování umožňuje zúžit výsledky požadavku. Můžete filtrovat podle ID, název, typy, podtypy, nadřazené místo a přidružené mezery. Můžete také filtrovat podle senzor datové typy, vlastnosti klíče a hodnoty, *procházení*, *minLevel*, *maxLevel*a další parametry filtru OData.

**Procházení grafu**. Můžete procházet prostorový graf prostřednictvím své znalosti a možnosti. Pro hloubka, procházet graf shora dolů nebo zdola nahoru pomocí parametrů *procházení*, *minLevel*, a *maxLevel*. Procházet graphu a získat přímo připojené k nadřazené mezeru nebo jeden z jejich potomků pak pro šířka uzlů na stejné úrovni. Když odešlete dotaz na objekt, můžete získat všechny související objekty, které mají relace na tento objekt s použitím *zahrnuje* parametr rozhraní API pro získání.

**Graf škálovatelnost**. Digitální dvojče zaručuje grafu škálovatelnost, takže mohou zpracovat vaše úlohy v reálném světě. Digitální Dvojčata je možné znázornit rozsáhlá portfolia nemovitosti, infrastruktury, zařízení, senzory, telemetrie a další.

**Rozšiřitelnost rozhraní Graph**. Pomocí rozšíření můžete přizpůsobit základní modely objektů digitální dvojče se nové typy a ontologie. Vaše digitální dvojče data můžete také rozšiřují o extensible vlastnosti a hodnoty.

### <a name="spatial-intelligence-graph-management-apis"></a>Prostorové řady grafu rozhraní API pro správu

Poté, co nasadíte digitální dvojče z [webu Azure portal](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) adresu URL rozhraní API pro správu není automaticky vygenerován. Zobrazí se na webu Azure Portal v **přehled** části v následujícím formátu.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Název | Nahradit hodnotou |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance digitální dvojče |
| YOUR_LOCATION | Jaké oblasti serveru vaší instance je hostován aplikací |

 Úplný formát adresy URL se zobrazí na tomto obrázku.

![Digitální dvojče portál rozhraní API pro správu][2]

Další podrobnosti o tom, jak používat Prostorové řady grafů najdete na adrese preview neoficiální Azure digitální dvojče rozhraní API pro správu.

> [!TIP]
> Neoficiální Swagger ve verzi preview neposkytujeme k předvedení funkcí rozhraní API nastavit.
> Je hostovaný na [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Další informace o [použití Swagger](how-to-use-swagger.md).

Všechna volání rozhraní API musí být ověřeny pomocí [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Postupujte podle rozhraní API [pokynů pro rozhraní API REST systému Microsoft konvence](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Většina rozhraní API, která vrací kolekce podporuje [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) možností dotazu systému.

## <a name="next-steps"></a>Další postup

- Další informace o připojení zařízení a jak odesílat telemetrické zprávy do digitální dvojče [Azure digitální dvojče zařízení připojení a telemetrie příchozího přenosu dat](concepts-device-ingress.md).

- Další informace o omezení rozhraní API pro správu a omezení, přečtěte si [Azure digitální dvojče API management a omezení](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
