---
title: Principy digitální dvojče modely a Prostorové řady grafu objektů | Dokumentace Microsoftu
description: Pomocí Azure digitální dvojče pro modelování vztahů mezi osoby, místa a zařízení
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c1d66e0b58567244f8c1406ee258c9311994ff20
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215102"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Principy digitální dvojče objektové modely a Prostorové řady grafu

Azure digitální dvojče je služba Azure IoT, která je základem komplexní virtuální reprezentace fyzického prostředí a přiřazená zařízení, senzory a lidí. Zdokonaluje vývoj podle organizační koncepty specifického pro doménu do užitečné modely, které představují samy o sobě nacházející se v rámci Prostorové řady grafu. Tyto koncepty věrně modelovat vztahy a interakce mezi lidmi, mezery a zařízení.

_Digitální dvojče objektové modely_ popisují koncepty specifického pro doménu, kategorie a vlastnosti. Uživatelé, kteří chtějí přizpůsobit řešení tak, aby jejich konkrétní potřeby jsou předdefinované modely. Společně tyto předdefinované digitální objektové modely Dvojčat společně tvoří _Ontology_. Chytré budovy ontology by popisují oblasti, místa, podlahy, pobočky, zóny, konferenčních místnostech dojde a místnosti fokus. Mřížce ontology energie by popisují různé elektrárny Rozvodny, zdrojů energie a zákazníky. Tyto digitální dvojče objektové modely a ontologie umožnit přizpůsobení digitální dvojče Azure pro nejrůznější scénáře a požadavky.

S _digitální dvojče objektové modely_ a _Ontology_ na místě, jeden naplnit _prostorový graf_. Prostorové grafy jsou virtuální reprezentace mnoho vztahů mezi mezerami, zařízení a uživatelů, které jsou relevantní pro řešení IoT. Následující diagram ukazuje příklad prostorový graf pomocí ontology Chytré budovy.

![Digitální dvojče prostorových vytváření grafu][1]

<a id="model"></a>

Prostorový graf spojuje mezery, zařízení, senzorů a uživatelů. Každá je propojení tak, aby modely z okolního světa: 43 míst má čtyři podlažích, každý s více různých oblastech. Uživatelé jsou spojeny s jejich pracovní stanice a jsou poskytnut přístup k části grafu.  Správce by třeba práva měnit prostorový graf, zatímco návštěvník může mít jenom práva k zobrazení určitých údajů sestavování.

## <a name="digital-twins-object-models"></a>Digitální dvojče objektové modely

Digitální dvojče objektové modely podporují tyto hlavní kategorie objektů:

- **Mezery** jsou virtuální nebo fyzické umístění, například `Tenant`, `Customer`, `Region`, `Venue`.
- **Zařízení** jsou části virtuálních nebo fyzických zařízení, třeba `AwesomeCompany Device`, `Raspberry Pi 3`.
- **Senzorů** jsou objekty, které zjišťování událostí, například `AwesomeCompany Temperature Sensor`, `AwesomeCompany Presence Sensor`.
- **Uživatelé** identifikaci osob a jejich vlastností.

Další kategorie objekty jsou:

- **Prostředky** jsou připojeny k mezerou a obvykle představují prostředky Azure používané objekty v prostorový graf, například `IoTHub`.
- **Objekty BLOB** jsou připojeny k objektů (například mezery, zařízení, senzorů a uživatele) a použít jako soubory s typ mime a metadata, například `maps`, `pictures`, `manuals`.
- **Rozšířené typy** jsou extensible výčty, které rozšiřují entity s konkrétními vlastnostmi, třeba `SpaceType`, `SpaceSubtype`.
- **Ontologie** představují sadu rozšířené typů, třeba `Default`, `Building`, `BACnet`, `EnergyGrid`.
- **Vlastnost klíče a hodnoty** jsou vlastní vlastnosti mezery, zařízení, senzorů a uživatelů. Použitím kromě integrované vlastnosti, například `DeltaProcessingRefreshTime` jako klíč a `10` jako hodnotu.
- **Role** jsou sady oprávnění přiřazená k uživatelům a zařízením v prostorový graf, například `Space Administrator`, `User Administrator`, `Device Administrator`.
- **Přiřazení rolí** se přidružení mezi role a objektu v prostorový graf, například udělování oprávnění instančního objektu služby ke správě mezeru prostorových grafu nebo uživatele.
- **Ukládá klíč zabezpečení** poskytnutí klíčů zabezpečení pro všechna zařízení v hierarchii v rámci dané místo objektu a umožňuje zabezpečeně komunikovat se službou digitální dvojče zařízení.
- **Uživatelem definované funkce** nebo **UDF** povolit telemetrii přizpůsobitelné senzor zpracování v rámci prostorový graf. Například uživatelem definovanou FUNKCI můžete nastavit hodnotu senzor, provádět vlastní logiku podle údajů snímačů přes a nastavte výstup na znak, připojit metadat na znak a odesílání oznámení, pokud předem definované podmínky jsou splněny. Aktuálně uživatelem definovanými funkcemi je možné psát v jazyce JavaScript.
- **Procesy pro hledání shody** jsou objekty, které určují, které UDF se provede pro danou telemetrické zprávy.
- **Koncové body** jsou umístění, ve kterém zprávy telemetrie a událostí digitálním Dvojčata je možné směrovat, například `Event Hub`, `Service Bus`, `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Graf prostorové inteligence

**Prostorový graf** je hierarchický graf mezery, zařízení, a podle lidí **digitální dvojče objektový model**. Prostorový graf podporuje _dědičnosti_, _filtrování_, _procházení_, _škálovatelnost_, a _rozšiřitelnosti_ . Uživatelé mohou spravovat a interakci s jejich prostorový graf s kolekce rozhraní API REST (viz níže).

Uživatel, který nasadí digitální dvojče služby v rámci svého předplatného stane globálním správcem kořenový uzel automaticky uděluje plný přístup k celou jejich strukturu. Tento uživatel může zřídit pak prostory v graphu pomocí rozhraní API místa. Zařízení může být zřízené s využitím rozhraní API zařízení, senzorů může zřídit pomocí rozhraní API snímačů atd. Nabízíme také [open source nástrojů](https://github.com/Azure-Samples/digital-twins-samples-csharp) grafu hromadně zřizovat.

Graf _dědičnosti_ se vztahuje na oprávnění a vlastnosti, které sestup od nadřazeného uzlu na všechny uzly pod ním. Například když role je přiřazená uživateli v daném uzlu, uživatel bude mít tuto roli oprávnění pro daný uzel a každý uzel pod ní. Kromě toho každá klíč vlastnosti a rozšířeného typu definované pro daný uzel zdědí všechny uzly pod tento uzel.

Graf _filtrování_ umožňuje zúžit výsledky požadavku ID, název, typy, podtypy, místem na nadřazený, přidružené mezery, senzor datové typy, vlastnosti klíče a hodnoty, *procházení*,  *minLevel*, *maxLevel*a další parametry filtru OData.

Graf _procházení_ umožňuje uživatelům procházet prostorový graf prostřednictvím své znalosti a možnosti. Pro hloubka, může procházet graf shora dolů nebo pomocí parametrů navigace zdola nahoru *procházení*, *minLevel*, *maxLevel*. Pro kontejnerových nástrojů může získat uzlů na stejné úrovni, které jsou přímo připojené k nadřazené mezeru nebo jeden z jejich potomků pak přejde grafu. Při dotazování na objekt, můžete získat všechny související objekty, které mají relace, že při použití objektu *zahrnuje* parametr rozhraní API pro získání.

Azure digitální dvojče zaručuje grafu _škálovatelnost_, takže mohou zpracovat vaše úlohy v reálném světě. Digitální Dvojčata je možné znázornit rozsáhlá portfolia nemovitosti, infrastruktury, zařízení, senzory, telemetrie a další.

Graf _rozšiřitelnost_ umožňuje uživatelům přizpůsobit základní modely digitální Dvojčat objektu se nové typy a ontologie. Vaše digitální dvojče dat může také být obohacené extensible vlastnosti a hodnoty.

### <a name="spatial-intelligence-graph-management-apis"></a>Prostorové řady grafu rozhraní API pro správu

Jakmile nasadíte Azure digitální dvojče z [webu Azure portal](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) adresu URL rozhraní API pro správu se automaticky vygeneruje a zobrazí se na portálu Azure **přehled** části v následujícím formátu:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| *Název_vaší_instance* | Název instance digitální dvojče Azure |
| *yourLocation* | Jaké oblasti serveru vaší instance je hostován aplikací |

 Můžete zobrazit úplný formát adresy URL používá v následujícím obrázku:

![Rozhraní API pro správu portálu k digitální dvojče][2]

Další podrobnosti o použití prostorových řady grafů najdete na adrese neoficiální Azure digitální dvojče rozhraní API pro správu, ve verzi preview.

> [!TIP]
> Neoficiální Swagger ve verzi preview neposkytujeme k předvedení funkcí rozhraní API nastavit.
> Je hostovaný na [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Další informace o [použití Swagger](how-to-use-swagger.md).

Všechna volání rozhraní API musí ověřovat s použitím [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Postupujte podle rozhraní API [pokynů pro rozhraní API REST systému Microsoft konvence](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Většina rozhraní API, která vrací kolekce podporuje [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) možností dotazu systému.

## <a name="next-steps"></a>Další postup

Další informace o připojení zařízení a jak odesílat telemetrické zprávy do služby Azure digitální dvojče [Azure digitální dvojče zařízení připojení a telemetrie příchozího přenosu dat](concepts-device-ingress.md).

Další informace o omezení rozhraní API pro správu a omezení, přečtěte si [Azure digitální dvojče API management a omezení](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
