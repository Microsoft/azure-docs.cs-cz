---
title: Výpočty bez serveru databáze – Azure Functions a Azure Cosmos DB
description: Zjistěte, jak Azure Cosmos DB a Azure Functions je možné společně můžete vytvářet řízené událostmi bez serveru výpočetní aplikace.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: ff202c85f20adce173a375987a5f2250fda565b2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041182"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Výpočetní prostředí bez serveru databázi s využitím služby Azure Cosmos DB a Azure Functions

Architektura bez serveru se točí kolem schopnost zaměřit na jednotlivé části logiky, které jsou opakovatelným a bezstavové. Tyto údaje vyžadují žádnou správu infrastruktury a jejich spotřebovávat prostředky jenom pro sekundy a milisekundy, mohou být spuštěny pro. V jádru služby bez serveru výpočetní přesun jsou funkce, které jsou k dispozici v ekosystému Azure podle [Azure Functions](https://azure.microsoft.com/services/functions). Další informace o jiných prostředích provádění bez serveru v Azure najdete v tématu [bez serveru v Azure](https://azure.microsoft.com/solutions/serverless/) stránky. 

Díky nativní integraci mezi službami [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) a Azure Functions můžete vytvořit aktivační procedury databáze, vstupní vazby a výstupní vazby přímo ze svého účtu Azure Cosmos DB. Pomocí Azure Functions a Azure Cosmos DB, můžete vytvořit a nasadit založený na událostech aplikace bez serveru s přístupem s nízkou latencí k velké množství dat pro globální uživatelské základny.

## <a name="overview"></a>Přehled

Azure Cosmos DB a Azure Functions umožňuje integrovat vaše databáze a aplikace bez serveru následujícími způsoby:

* Vytvoření založené na událostech **aktivační událost Azure Cosmos DB** ve funkci Azure functions. Tato aktivační událost spoléhá na [kanálu změn](change-feed.md) streamů. monitorování kontejneru Azure Cosmos DB pro změny. Při jakékoli změně do kontejneru, se pošle kanál stream změn aktivační událost, která volá funkci Azure.
* Můžete také vytvořit vazbu funkce Azure Functions do kontejneru Azure Cosmos DB pomocí **vstupní vazby**. Když funkce provede vstupní vazby číst data z kontejneru.
* Vytvořit vazbu funkce do kontejneru Azure Cosmos DB pomocí **výstupní vazby**. Po dokončení funkce výstupních vazeb zapisovat data do kontejneru.

> [!NOTE]
> V současné době aktivační událost Azure Cosmos DB, vstupní vazby a výstupních vazeb jsou podporovány pro použití s rozhraním SQL API. Pro všechny ostatní Azure Cosmos DB API by měl přístup k databázi z funkce s použitím statické klienta pro vaše rozhraní API.


Následující diagram znázorňuje jednotlivé vlastnosti produktu tyto tři integrace: 

![Jak integrovat službu Azure Cosmos DB a Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Aktivační událost, vstupní vazby a výstupní vazby Azure Cosmos DB je možné tyto kombinace:
* Aktivační událost Azure Cosmos DB je možné s vazbou výstup do jiného kontejneru Azure Cosmos DB. Po funkce provádějí akci, na položku v informačním kanálu změn můžete je zapsat do jiného kontejneru (zápis, že ho do kontejneru, ze které pochází efektivně by vytvořilo smyčku rekurzivní). Nebo můžete použít aktivační událost Azure Cosmos DB efektivně migrovat všechny změněné položky z jednoho kontejneru do jiného kontejneru s využitím výstupní vazbu.
* Vstupní vazby a výstupních vazeb pro služby Azure Cosmos DB je možné ve stejné funkci Azure. Tento postup funguje i v případech, pokud chcete najít určité data s využitím vstupní vazby, upravte ve funkci Azure a uložte ho do kontejneru stejný nebo jiný kontejner po změně.
* Vstupní vazby ke kontejneru služby Azure Cosmos DB je možné ve stejné funkci jako aktivační událost Azure Cosmos DB a je možné s nebo bez vazby i výstup. Můžete použít tuto kombinaci použít informace o systému exchange aktuální měny (získaných pomocí vstupní vazby ke kontejneru exchange) do kanálu změn nové objednávky ve službě nákupního košíku. Aktualizace nákupního košíku celkem s aktuální převodu měny použije, je možné zapisovat na třetí kontejneru prostřednictvím výstupní vazbu.

## <a name="use-cases"></a>Případy použití

Tyto případy použití ukáže několik způsobů, jak můžete využít na maximum z vašich dat Azure Cosmos DB – propojováním vašich dat do služby Azure Functions založený na událostech.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Příklad použití – aktivační událost Azure Cosmos DB IoT a výstupní vazby

V implementace IoT můžete vyvolat funkci při indikátor modulu kontrola se zobrazí v Auto připojené.

**Implementace:** Pomocí aktivační událost Azure Cosmos DB a výstupní vazby

1. **Aktivační událost Azure Cosmos DB** se používá k aktivaci události související s výstrahami auta, jako je například indikátor modulu kontrola přicházejících Auto připojené.
2. Pokud indikátor modulu kontrola pochází, data ze senzorů posílá do služby Azure Cosmos DB.
3. Azure Cosmos DB vytvoří nebo aktualizuje nové dokumenty data ze senzorů a potom tyto změny se streamují do aktivační událost Azure Cosmos DB.
4. Aktivační událost je vyvolána při každé změně dat pro shromažďování dat ze senzorů, jak všechny změny se streamují prostřednictvím kanálu změn.
5. Podmínku prahové hodnoty se používá ve funkci k odesílání dat ze senzorů do záručním oddělením.
6. Teplota je také nad určitou hodnotu, že je také odesláno upozornění na vlastníka.
7. **Výstupní vazby** na funkci aktualizuje záznam car v jiném kontejneru Azure Cosmos DB k ukládání informací o události modulu kontrola.

Následující obrázek ukazuje kód napsaný v na webu Azure portal této aktivační události.

![Vytvoření triggeru služby Azure Cosmos DB na webu Azure Portal](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Příklad použití – trigger časovače finanční a vstupní vazby

Ve finančních implementací můžete vyvolat funkci když zůstatek bankovním účtu klesne pod určitou dobu.

**Implementace:** Trigger časovače s využitím vstupní vazby Azure Cosmos DB

1. Použití [trigger časovače](../azure-functions/functions-bindings-timer.md), můžete načíst informace o bankovním účtu zůstatku uložená v kontejneru Azure Cosmos DB v časových intervalech pomocí **vstupní vazby**.
2. Je-li zůstatek pod prahovou hodnotou nízký zůstatek přidal uživatel, pak proveďte akce z funkce Azure functions.
3. Může být výstupní vazbu [SendGrid integrace](../azure-functions/functions-bindings-sendgrid.md) , která odešle e-mail z účtu služby pro e-mailové adresy pro jednotlivé účty nízký zůstatek.

Následující obrázky znázorňují kódu na webu Azure Portal pro tento scénář.

![Soubor index.js pro trigger časovače pro finanční scénář](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![V souboru Run.csx trigger časovače pro finanční scénář](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Herní příklad použití – aktivační událost Azure Cosmos DB a výstupní vazby

V hry, při vytvoření nového uživatele můžete vyhledat jiných uživatelů, kteří je možné, že pomocí [Gremlin API služby Azure Cosmos DB](graph-introduction.md). Pak můžou zapisovat výsledky [Azure Cosmos DB SQL database] k němu měli snadný přístup.

**Implementace:** Pomocí aktivační událost Azure Cosmos DB a výstupní vazby

1. Pomocí služby Azure Cosmos DB [databáze grafů](graph-introduction.md) k uložení všech uživatelů, můžete vytvořit novou funkci s aktivační událost Azure Cosmos DB. 
2. Pokaždé, když se vloží nového uživatele, je vyvolána funkce a potom výsledek je uložen pomocí **výstupní vazby**.
3. Funkce se dotazuje databáze grafů k vyhledání všech uživatelů, které jsou přímo souvisí s novým uživatelem a vrátí tuto datovou sadu do funkce.
4. Tato data se pak ukládá v Azure Cosmos DB, který lze potom snadno získat front-endu aplikací, která ukazuje přátelům připojených nového uživatele.

### <a name="retail-use-case---multiple-functions"></a>Případ použití maloobchodní - více funkcí

V implementacích maloobchodního prodeje když uživatel přidá položky do nákupního košíku Teď máte možnost k vytvoření a vyvolání funkce pro komponenty kanálu volitelné business.

**Implementace:** Více aktivačních událostí služby Azure Cosmos DB naslouchání na jeden kontejner

1. Víc funkcí Azure můžete vytvořit přidáním aktivace služby Azure Cosmos DB ke každému – které naslouchat na stejný změnit informační kanál data nákupního košíku. Všimněte si, že při naslouchání více funkcí pro stejný kanálu změn novou kolekci zapůjčení se vyžaduje pro každou funkci. Další informace o zapůjčení kolekcí najdete v tématu [Principy knihovnou Change Feed Processor](change-feed-processor.md).
2. Pokaždé, když uživatelé nákupní košík přidá nová položka, každá funkce nezávisle na sobě vyvolá kanál z nákupního košíku kontejneru změn.
    * Jednu funkci použít ke změně zobrazení položek, které uživatel může zajímat obsah aktuální nákupní košík.
    * Jiné funkce může aktualizovat inventáře součty.
    * Jiné funkce může posílat informace o zákaznících pro určité produkty marketingovém oddělení, který jim zašle propagační poštovní modul. 

    Oddělení můžete vytvořit aktivační událost Azure Cosmos DB prostřednictvím naslouchání kanálu změn a ujistěte se, že se nebude zpoždění důležité pořadí zpracování událostí v procesu.

Ve všech těchto případů použití, protože funkce má oddělené vlastní aplikaci, není nutné aktivovat nových instancí aplikace neustále. Místo toho Azure Functions rozjede jednotlivých funkcí k dokončení samostatné procesy podle potřeby.

## <a name="tooling"></a>Nástroje

Nativní integrace mezi službou Azure Cosmos DB a Azure Functions je k dispozici na webu Azure Portal a v sadě Visual Studio 2017.

* Na portálu Azure Functions můžete vytvořit aktivační událost Azure Cosmos DB. Rychlý start pokyny najdete v tématu [vytvořit aktivační událost Azure Cosmos DB na webu Azure Portal](https://aka.ms/cosmosdbtriggerportalfunc).
* Na portálu služby Azure Cosmos DB můžete přidat aktivační událost Azure Cosmos DB do existující aplikace funkce Azure Functions ve stejné skupině prostředků.
* V sadě Visual Studio 2017, můžete vytvořit aktivační událost Azure Cosmos DB pomocí [Azure Functions Tools for Visual Studio 2017](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Proč zvolit službu Azure Functions integraci pro architekturu bez serveru?

Služba Azure Functions umožňuje vytvářet škálovatelné jednotky práce nebo stručné kusy logiku, která je možné spouštět na vyžádání, bez zřizování nebo správy infrastruktury. S využitím Azure Functions, není nutné k vytvoření kompletního aplikace reagovat na změny v databázi Azure Cosmos DB, můžete vytvořit malé opakovaně použitelné funkce pro konkrétní úlohy. Kromě toho můžete také použít data služby Azure Cosmos DB jako vstup nebo výstup do funkce Azure Functions v reakci na události, například HTTP žádosti nebo aktivační procedury vypršel časový limit.

Azure Cosmos DB je doporučené databáze pro výpočetní architektuře bez serveru z následujících důvodů:

* **Okamžitý přístup k vašim datům**: Máte granulární přístup ke každé hodnotě uložit, protože Azure Cosmos DB [automaticky indexuje](index-policy.md) všechna data ve výchozím nastavení a okamžitě zpřístupní tyto indexy. To znamená, že budete moct neustále dotazovat, aktualizovat a přidat nové položky do databáze a mít okamžitý přístup prostřednictvím Azure Functions.

* **Bez schématu**. Azure Cosmos DB je bez schématu - tak, aby byl jednoznačně schopná zpracovat žádný výstup dat z funkce Azure functions. Tento přístup "handle nic" Díky jednoduché vytvořit celou řadu funkcí, které veškerý výstup do služby Azure Cosmos DB.

* **Škálovatelná propustnost**. Propustnost je možné škálovat nahoru a dolů okamžitě ve službě Azure Cosmos DB. Pokud máte stovkami nebo tisícovkami objektů funkce dotazování a zápis do kontejneru, můžete vertikálně navýšit kapacitu vašeho [RU/s](request-units.md) pro zpracování zátěže. Všechny funkce může pracovat souběžně pomocí přidělené RU/s a vašich dat je zaručeně [konzistentní](consistency-levels.md).

* **Globální replikace**. Můžete replikovat data služby Azure Cosmos DB [po celém světě](distribute-data-globally.md) snížit latenci, geografické vyhledávání, vaše data co nejblíž koncovým kde jsou vaši uživatelé. Jako se všechny dotazy služby Azure Cosmos DB, data z triggery řízené událostmi je číst data z Azure Cosmos DB, který je nejblíže uživateli.

Pokud chcete integrovat s Azure Functions k ukládání dat a není nutné hloubkové indexování nebo pokud potřebujete ukládat přílohy a mediální soubory, [aktivační událost Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md) , může být lepší možností.

Výhody služby Azure Functions: 

* **Založený na událostech**. Služba Azure Functions jsou založené na událostech a může naslouchat na změnu informačního kanálu ze služby Azure Cosmos DB. To znamená, že není nutné vytvářet naslouchání logiku, vám stačí se těšit pro změny, které jste poslouchání. 

* **Žádná omezení**. Funkce se mohou spouštět paralelní a služba přede až tolik, je nutné. Můžete nastavit parametry.

* **Pro rychlé úkoly**. Služba spuštění nové instance funkce pokaždé, když se událost aktivuje a uzavře je co nejdřív po dokončení funkce. Platíte jenom za čas, kdy jsou funkce spuštěné.

Pokud si nejste jistí, jestli jsou nejvhodnější pro vaši implementaci Flow, Logic Apps, Azure Functions nebo WebJobs, přečtěte si téma [zvolit mezi službami Flow, Logic Apps, Functions a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Další postup

Teď můžeme připojit služby Azure Cosmos DB a Azure Functions skutečném: 

* [Vytvoření triggeru služby Azure Cosmos DB na webu Azure Portal](https://aka.ms/cosmosdbtriggerportalfunc)
* [Vytvoření triggeru HTTP funkce Azure s využitím vstupní vazby Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Azure Cosmos DB vazbách a aktivačních událostech](../azure-functions/functions-bindings-cosmosdb.md)


 



