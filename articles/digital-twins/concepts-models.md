---
title: Vlastní modely
titleSuffix: Azure Digital Twins
description: Pochopte, jak digitální vlákna Azure používá uživatelsky definované modely k popisu entit ve vašem prostředí.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 599bb93e747acf504a4ebf43aaea771ed5064886
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131385"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Principy dvojitých modelů v digitálních prozdvojeních Azure

Klíčovou charakteristikou digitálních vláken Azure je možnost definovat vlastní slovník a vytvořit dvojitou graf v rámci podmínek vaší firmy. Tato funkce je k dispozici prostřednictvím uživatelsky definovaných **modelů**. Modely si můžete představit jako podstatná jména v popisu svého světa. 

Model je podobný **třídě** v objektově orientovaném programovacím jazyce, který definuje datový tvar pro jeden konkrétní koncept v reálném pracovním prostředí. Modely mají názvy (například *místnost* nebo *senzor teploty*) a obsahují prvky, jako jsou vlastnosti, telemetrie/události a příkazy, které popisují, co tento typ entity ve vašem prostředí může dělat. Později tyto modely použijete k vytvoření [**digitálních vláken**](concepts-twins-graph.md) , které reprezentují konkrétní entity, které splňují tento popis typu.

Modely digitálních vláken Azure jsou reprezentovány ve formátu **digitálního vlákna (DTDL)** založeného na JSON – ld.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>DTDL (Digital redefinition Language) pro modely

Modely pro digitální vlákna Azure jsou definovány pomocí jazyka DTDL (Digital nedefinovaný jazyk). DTDL je založen na JSON-LD a je nezávislý na programovacím jazyce. DTDL není výhradně pro digitální vlákna Azure, ale používá se také k reprezentaci dat zařízení v jiných službách IoT, jako je [IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). 

Digitální vlákna Azure používá **DTDL _verze 2_**. Další informace o této verzi DTDL najdete v dokumentaci k jejímu specifikaci v GitHubu: [*Digital DTDLing Definition Language () – verze 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Použití DTDL _verze 1_ u digitálních vláken Azure je teď zastaralé.

> [!NOTE] 
> Ne všechny služby, které používají DTDL, implementují přesně stejné funkce DTDL. Například IoT technologie Plug and Play nepoužívá funkce DTDL, které jsou pro grafy, zatímco digitální vlákna Azure v současné době neimplementují příkazy DTDL.
>
> Další informace o funkcích DTDL, které jsou specifické pro funkce digitálních vláken Azure, najdete v části dále v tomto článku týkajícími se [specifických implementací Azure pro digitální vlákna DTDL](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Prvky modelu

V rámci definice modelu je položka kódu nejvyšší úrovně **rozhraní**. Tím se zapouzdřuje celý model a zbytek modelu je definovaný v rámci rozhraní. 

Rozhraní modelu DTDL může obsahovat nula, jednu nebo mnoho z následujících polí:
* **Vlastnost** -Properties jsou datová pole, která představují stav entity (jako jsou vlastnosti v mnoha objektově orientovaných programovacích jazycích). Vlastnosti mají záložní úložiště a dají se číst kdykoli.
* **Telemetrie** – pole telemetrie představují měření nebo události a často se používají k popisu čtení snímačů zařízení. Na rozdíl od vlastností telemetrie není uložená na digitálním vlákna; Jedná se o řadu událostí s datovou vazbou, které je potřeba zpracovat při jejich výskytu. Další informace o rozdílech mezi vlastnostmi a telemetrie najdete níže v části [*vlastnosti vs. telemetrie*](#properties-vs-telemetry) .
* **Komponenta** – komponenty umožňují sestavit rozhraní modelu jako sestavení jiných rozhraní, pokud chcete. Příkladem součásti je rozhraní FrontCamera (a další  *rozhraní komponenty),* které se používají při definování modelu pro *telefon*. Nejdřív musíte definovat rozhraní pro *frontCamera* , jako by šlo o svůj vlastní model, a pak na něj můžete odkazovat při definování *telefonu*.

    Pomocí komponenty popište něco, co je nedílnou součástí vašeho řešení, ale nepotřebujete samostatnou identitu, a nemusíte ho vytvářet, odstraňovat ani uspořádávat v nezávisle grafu. Pokud chcete, aby entity měly nezávislou existenci v dodaném grafu, reprezentujte je jako samostatné digitální vlákna různých modelů propojených *vztahy* (viz další odrážka).
    
    >[!TIP] 
    >Komponenty lze také použít pro organizaci k seskupení sad souvisejících vlastností v rámci rozhraní modelu. V této situaci si můžete každou komponentu představit jako obor názvů nebo "složka" uvnitř rozhraní.
* Relace **Relationship** vám umožní vyjádřit, jak se může digitální vlákna zapojit do jiných digitálních vláken. Relace mohou představovat různé sémantické významy, jako je například *Contains* ("podlaha obsahuje místnost"), *studená* ("TVK studená místnost"), *isBilledTo* ("kompresor se účtuje uživateli") atd. Relace umožňují řešení poskytovat graf vzájemně souvisejících entit.

> [!NOTE]
> [Specifikace DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) také definuje **příkazy**, které jsou metody, které lze provést na digitálního vlákna (například příkaz reset nebo příkaz pro přepnutí nebo vypnutí ventilátoru). V případě *digitálních vláken Azure se ale příkazy aktuálně nepodporují.*

### <a name="properties-vs-telemetry"></a>Vlastnosti vs. telemetrie

Tady jsou některé další doprovodné materiály týkající se rozlišení mezi poli **vlastností** DTDL a **telemetrie** v oblasti digitálních vláken Azure.

Rozdíl mezi vlastnostmi a telemetrie pro modely digitálních vláken Azure je následující:
* Očekává se, že **vlastnosti** mají záložní úložiště. To znamená, že můžete kdykoli číst vlastnost a načíst její hodnotu. Pokud je vlastnost zapisovatelná, můžete také uložit hodnotu do vlastnosti.  
* **Telemetrii** je více podobná jako datový proud událostí. Jedná se o sadu datových zpráv, které mají krátké životnosti. Pokud nenastavíte naslouchání pro událost a akce, které se mají provést, když k ní dojde, neexistuje žádné trasování události v pozdějším čase. Nemůžete se k němu vrátit a později si ho přečíst. 
  - V terminologii jazyka C# je telemetrie jako událost jazyka C#. 
  - Ve výrazech IoT je telemetrie obvykle jediná měření odesílaná zařízením.

**Telemetrie** se často používá se zařízeními IoT, protože spousta zařízení není schopná ani zajímat a ukládá vygenerované hodnoty měření. Pouze je odesílají jako proud událostí telemetrie. V takovém případě se v zařízení nemůžete kdykoli dotazovat na nejnovější hodnotu pole telemetrie. Místo toho budete muset naslouchat zprávy ze zařízení a provádět akce, když přijdete o zprávy. 

Výsledkem je, že při navrhování modelu v digitálních proobjektech Azure budete pravděpodobně ve většině případů používat **vlastnosti** pro modelování vláken. To vám umožní mít k dispozici záložní úložiště a možnost číst datová pole a dotazovat se na ně.

Telemetrii a vlastnosti často spolupracují na zpracování dat ze zařízení. Vzhledem k to, že všechny příchozí přenosy dat do Azure jsou prostřednictvím [rozhraní API](how-to-use-apis-sdks.md), obvykle použijete funkci příchozího přenosu dat ke čtení telemetrie nebo událostí vlastností ze zařízení a nastavíte vlastnost v ADT v reakci. 

Můžete také publikovat událost telemetrie z rozhraní API digitálních vláken Azure. Stejně jako u jiné telemetrie, jedná se o krátkodobou událost, která vyžaduje, aby naslouchací proces zpracoval.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital DTDLs – specifické implementace

Aby byl model DTDL kompatibilní s digitálními ovládacími vlákna Azure, musí tyto požadavky splňovat.

* Všechny elementy DTDL nejvyšší úrovně v modelu musí být typu *Interface*. Důvodem je to, že rozhraní API modelu digitálních vláken Azure může přijímat objekty JSON, které reprezentují rozhraní nebo pole rozhraní. V důsledku toho nejsou na nejvyšší úrovni povoleny žádné další typy elementů DTDL.
* DTDL pro digitální vlákna Azure nesmí definovat žádné *příkazy*.
* Funkce digitálních vláken Azure umožňuje pouze jednu úroveň vnoření komponent. To znamená, že rozhraní, které se používá jako součást, nemůže mít samotné součásti. 
* Rozhraní nelze definovat vloženou v jiných rozhraních DTDL; musí být definované jako samostatné entity nejvyšší úrovně s jejich vlastními identifikátory. Až pak jiné rozhraní chce toto rozhraní zahrnout jako komponentu nebo prostřednictvím dědičnosti, může odkazovat na jeho ID.

Digitální vlákna Azure také nezohledňují `writable` atribut u vlastností nebo relací. I když se to dá nastavit podle specifikací DTDL, hodnota se nepoužívá u digitálních vláken Azure. Místo toho se tyto služby vždycky zpracovávají jako zapisovatelné pro externí klienty, kteří mají obecná oprávnění k zápisu do služby Azure Digital probíhají.

## <a name="example-model-code"></a>Příklad kódu modelu

Modely s dvojitým typem lze zapsat v libovolném textovém editoru. Jazyk DTDL se řídí syntaxí JSON, takže byste měli ukládat modely s příponou *. JSON*. Použití rozšíření JSON umožní mnoha programovým textovým editorům poskytnout základní kontrolu syntaxe a zvýrazňování pro dokumenty DTDL. K dispozici je také [rozšíření DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) pro [Visual Studio Code](https://code.visualstudio.com/).

Tato část obsahuje příklad typického modelu, který je napsán jako rozhraní DTDL. Model popisuje **Planet**, každý s názvem, hmotností a teplotu.
 
Vezměte v úvahu, že Planet může také interagovat s **Moons** , které jsou jejich satelity, a může obsahovat **craters**. V následujícím příkladu `Planet` model vyjadřuje připojení k těmto dalším entitám odkazem na dva externí modely – `Moon` a `Crater` . Tyto modely jsou také definovány v následujícím ukázkovém kódu, ale jsou udržovány velmi jednoduché, takže nemusíte odečítat z primárního `Planet` příkladu.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Pole modelu jsou:

| Pole | Description |
| --- | --- |
| `@id` | Identifikátor modelu. Musí být ve formátu `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Určuje druh informací, které jsou popsány. Pro rozhraní je typ *rozhraní*. |
| `@context` | Nastaví [kontext](https://niem.github.io/json/reference/json-ld/context/) dokumentu JSON. Modely by měly používat `dtmi:dtdl:context;2` . |
| `displayName` | volitelné V případě potřeby vám umožní model zadat popisný název. |
| `contents` | Všechna zbývající data rozhraní jsou zde umístěna jako pole definic atributů. Každý atribut musí poskytnout `@type` (*vlastnost*, *telemetrie*, *příkaz*, *vztah* nebo *komponentu*) k identifikaci řazení informací o rozhraní, které popisuje, a poté sadu vlastností, které definují skutečný atribut (například `name` a `schema` k definování *vlastnosti*). |

> [!NOTE]
> Všimněte si, že rozhraní komponenty (*Crater* v tomto příkladu) je definováno ve stejném poli jako rozhraní, které ho používá (*globálním*). Komponenty musí být tímto způsobem definovány v volání rozhraní API, aby bylo rozhraní nalezeno.

### <a name="possible-schemas"></a>Možná schémata

Jak na DTDL, schéma pro atributy *vlastností* a *telemetrie* může být standardní primitivní typy –,, `integer` `double` a, `string` `Boolean` a další typy, jako jsou `DateTime` a `Duration` . 

Kromě primitivních typů mohou mít pole *vlastností* a *telemetrie* tyto komplexní typy:
* `Object`
* `Map`
* `Enum`

 Podporují také pole telemetrie `Array` .

### <a name="model-inheritance"></a>Dědičnost modelů

V některých případech můžete chtít model specializovat. Může být například užitečné mít obecnou *místnost* modelu a specializované varianty *ConferenceRoom* a *posilovně*. Pro expresní specializaci DTDL podporuje dědičnost: rozhraní můžou dědit z jednoho nebo více jiných rozhraní. 

Následující příklad znovu představí model *globálním* z předchozího příkladu DTDL jako podtyp většího modelu *CelestialBody* . Jako první se definuje "nadřazený" model a pak se na něj vytvoří podřízený model pomocí pole `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

V tomto příkladu *CelestialBody* přispívá název, hmotnost a teplotu do *globálním*. `extends`Oddíl je název rozhraní nebo pole názvů rozhraní (což umožňuje rozšířit rozhraní, pokud je požadováno, zdědit z více nadřazených modelů).

Po použití dědičnosti zpřístupňuje rozhraní rozšíření všechny vlastnosti z celého řetězce dědičnosti.

Rozhraní rozšíření nemůže změnit žádnou z definic nadřazených rozhraní; dá se do nich přidávat jenom. Také nemůže předefinovat schopnost, která je již definována v některém z jeho nadřazených rozhraní (i v případě, že jsou možnosti definovány jako stejné). Například Pokud nadřazené rozhraní definuje `double` *hmotnost* vlastnosti, rozhraní rozšíření nemůže obsahovat deklaraci *hmotnosti*, a to i v případě, že je také `double` .

## <a name="best-practices-for-designing-models"></a>Osvědčené postupy pro navrhování modelů

Při návrhu modelů tak, aby odrážely entity ve vašem prostředí, může být užitečné si prohlédnout a vzít v úvahu dopady na [dotazy](concepts-query-language.md) v návrhu. Můžete chtít navrhnout vlastnosti tak, aby se předešlo velkým sadám výsledků z procházení grafů. Můžete také chtít modelovat vztahy, které budou zodpovězeny v jednom dotazu jako vztahy na jedné úrovni.

### <a name="validating-models"></a>Ověřování modelů

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="integrating-with-industry-standard-models"></a>Integrace s oborově standardními modely

Používání modelů založených na oborových standardech nebo použití standardní reprezentace Ontology, jako je například RDF nebo OWL, poskytuje bohatý výchozí bod při návrhu modelů digitálních vláken Azure. Používání průmyslových modelů pomáhá také při standardizaci a sdílení informací.

Aby bylo možné použít s digitálními podmnožinami Azure, musí být model reprezentovaný v [**jazyce DTDL (Digital vlákna)**](concepts-models.md)založeném na JSON – ld. Proto pokud chcete použít standardní model, musíte ho nejdřív převést na DTDL, aby ho mohly používat digitální vlákna Azure. Model DTDL pak slouží jako zdroj pravdy pro model v rámci digitálních vláken Azure.

Existují dva hlavní cesty k integraci standardních modelů s DTDL, a to v závislosti na vaší situaci:
* Pokud jste ještě vytvořili vaše modely, můžete je navrhnout kolem **stávajícího úvodní DTDL ontologie** , která obsahuje jazyk specifický pro váš obor.
* Pokud už máte existující modely založené na oborovém standardu, budete je muset **převést na DTDL** , aby se daly přenést do digitálních vláken Azure.

Další informace o obou těchto procesech naleznete v tématu [*How to: Integral a Industry Standard Models*](how-to-integrate-models.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si téma Správa modelů pomocí rozhraní DigitalTwinModels API:
* [*Postupy: Správa vlastních modelů*](how-to-manage-model.md)

Nebo se dozvíte, jak se vytváří digitální vlákna na základě modelů:
* [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md)

