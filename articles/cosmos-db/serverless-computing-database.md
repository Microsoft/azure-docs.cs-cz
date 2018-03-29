---
title: Bez serveru databáze computing – funkce Azure a Azure Cosmos DB | Microsoft Docs
description: Další informace databáze Cosmos Azure a Azure Functions použití společně vytvářet událostmi řízené bez serveru výpočetní aplikace.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: mimig
ms.openlocfilehash: 39155a2407efa0cc13236a1bbbbcd292daac86b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: Pomocí Azure Functions computing bez serveru databáze

Bez serveru computing se točí kolem umožňuje zaměřit se na jednotlivé logiku, které jsou opakovatelných a bezstavové. Tyto údaje vyžadují žádné správu infrastruktury a jejich spotřebovávat prostředky jenom pro sekundách a milisekundách, mohou být spuštěny pro. Základem bez serveru výpočetní přesun jsou funkce, které jsou k dispozici v ekosystému Azure podle [Azure Functions](https://azure.microsoft.com/services/functions).

Nativní integrace mezi [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) a funkce Azure, můžete vytvořit aktivační události databáze, vstupní vazby a výstup vazby přímo z vašeho účtu Azure Cosmos DB. Pomocí funkce Azure a Azure Cosmos DB, můžete vytvářet a nasazovat událostmi řízené bez serveru aplikace s nízkou latencí přístup k bohaté data pro základní globální uživatele.

## <a name="overview"></a>Přehled

Azure Cosmos DB a Azure Functions umožňují integraci vašich databází a aplikací bez serveru následujícími způsoby:

* Vytvoření událostmi řízené **aktivační událost Azure Cosmos DB** v funkci Azure. Této aktivační události spoléhá na [změnit informačního kanálu](change-feed.md) datových proudů do monitorování vaší Azure Cosmos DB kontejner pro změny. Když do kontejneru se udělají jakékoli změny, změna kanálu datový proud je odeslat aktivační událost, která volá funkci Azure.
* Můžete taky vytvořit vazbu funkce Azure kolekce Azure Cosmos DB používá **vstupní vazby**. Když funkce provede vstupní vazby číst data z kontejneru.
* Vytvořit vazbu funkce kolekce Azure Cosmos DB pomocí **výstup vazby**. Po dokončení funkci vazby výstup zapsat data do kontejneru.

> [!NOTE]
> V současnosti aktivační události Azure Cosmos DB a vstupní a výstupní vazby fungují jenom s účty SQL API a Graph API.

Následující diagram znázorňuje každý z těchto tří integrace: 

![Jak integrovat Azure Cosmos DB a Azure Functions](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Aktivační událost Azure Cosmos DB, vstupní vazby a výstup vazby lze použít v následující kombinace:
* Aktivační událost INSTEAD Azure Cosmos DB lze použít s vazbou výstup do jiného kontejneru Azure Cosmos DB. Poté, co funkce provede akce na položku v informačním kanálu změnu napíšete ho do jiného kontejneru (zápis, že ho do kontejneru, ze které pochází efektivně by vytvořilo smyčku rekurzivní). Nebo můžete použít aktivační událost INSTEAD Azure Cosmos DB efektivně migrovat všechny změněné položky z jednoho kontejneru typu do jiného kontejneru, s použitím vazbu výstup.
* Vazby vstupní a výstupní vazby pro Azure Cosmos DB lze ve stejné funkci Azure. To funguje dobře v případech, pokud chcete vyhledávat určitá data vstupní vazbou, upravte ho ve funkci Azure a uložte je na stejném kontejneru nebo jiný kontejner, po úpravy.
* Vstupní vazbu ke kontejneru Azure Cosmos DB mohou být používány stejnou funkci jako aktivační procedury Azure Cosmos DB a dají se použít s nebo bez vazby také výstup. Můžete použít tuto kombinaci použít informace o aktuální Měna systému exchange (vyžádat pomocí vstupní vazbu ke kontejneru exchange) k informačnímu kanálu změn nových objednávek ve službě nákupní košík. Aktualizované nákupní košík celkem s aktuální převodu měny, který se použije, může být napsán na kontejner třetí pomocí vazbu výstup.

## <a name="use-cases"></a>Případy použití

Následující případy použití ukazují několik způsobů, které můžete provést většinu daty Azure Cosmos DB - připojením vaše data na základě událostí Azure Functions.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT případ - aktivační událost Azure Cosmos DB použití a výstupní vazby

V implementacích IoT můžete vyvolat funkce při světlým modul Kontrola v připojených Auto.

**Implementace:** použít aktivační událost INSTEAD Azure Cosmos DB a vazbu výstup

1. **Aktivační událost Azure Cosmos DB** se používá k aktivaci události související s car výstrahy, jako je například modul světlým kontrola brzo připojené automobilu.
2. Modul světlým kontrola vycházejí, data snímačů posílá Azure Cosmos DB.
3. Azure Cosmos DB vytvoří nebo aktualizuje nové dokumenty data snímačů a potom tyto změny streamovaných k aktivační události Azure Cosmos DB.
4. Aktivační událost je volána při každé změně dat do kolekce dat snímačů, jako jsou všechny změny streamování prostřednictvím kanálu změnu.
5. Podmínku prahová hodnota se používá ve funkci k odesílání dat snímačů oddělení záruky.
6. Pokud teplota je také nad určitou hodnotu, zobrazí se také výstraha vlastníkovi.
7. **Výstup vazby** pro funkci aktualizace car záznamu v jiné kolekci Azure Cosmos DB k uložení informací o událostí modulu.

Následující obrázek znázorňuje kód napsaný v portálu Azure této aktivační události.

![Vytvořit aktivační událost INSTEAD Azure Cosmos DB na portálu Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Finančních případ - aktivaci časovačem použití a vstup vazby

Ve finančních implementace můžete vyvolat funkci když zůstatek účtu bank klesne pod určitou.

**Implementace:** aktivační událost časovače se Azure DB Cosmos vstupní vazby

1. Použití [aktivaci časovačem](../azure-functions/functions-bindings-timer.md), můžete načíst účet bank vyrovnávání informace uložené v kontejner Azure Cosmos DB v určitých intervalech pomocí **vstupní vazby**.
2. Je-li zůstatek pod prahovou hodnotou nízkou vyrovnávání nastavil uživatel, potom podle pokynů s akcí funkce Azure.
3. Může být vazba výstup [sendgrid vám umožňuje integraci](../azure-functions/functions-bindings-sendgrid.md) , odešle e-mailu z účtu služby do e-mailové adresy pro každou nízkou vyrovnání účtů identifikovat.

Následující obrázky znázorňují kód na portálu Azure pro tento scénář.

![Soubor index.js pro aktivační událost časovače pro finanční scénář](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Soubor Run.csx pro aktivační událost časovače pro finanční scénář](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Herní případ - aktivační událost Azure Cosmos DB použití a výstupní vazby

V hry, při vytváření nového uživatele můžete vyhledat jiných uživatelů, kteří mohou vědět pomocí [Azure Cosmos DB Graph API](graph-introduction.md). Poté můžete napsat výsledky do [databáze Azure Cosmos DB SQL] lze snadno načíst.

**Implementace:** použít aktivační událost INSTEAD Azure Cosmos DB a vazbu výstup

1. Pomocí Azure DB Cosmos [grafu databáze](graph-introduction.md) k uložení všech uživatelů, můžete vytvořit novou funkci s aktivační procedury Azure Cosmos DB. 
2. Vždy, když je vložit nového uživatele, je volána funkce a pak výsledek je uložen pomocí **výstup vazby**.
3. Funkce dotazu na graf databázi pro vyhledání všech uživatelů, kteří jsou přímo souvisí s novým uživatelem a vrátí tuto datovou sadu funkce.
4. Tato data se pak uloženy v databázi Cosmos Azure, který může pak snadno načíst všechny front-endu aplikace, která ukazuje svoje přátele připojené nového uživatele.

### <a name="retail-use-case---multiple-functions"></a>Případ použití prodejní - víc funkcí

V implementacích prodejní při přidání položky do nákupního košíku nyní máte možnost vytvořit a vyvolání funkce pro volitelné obchodní kanálu součásti.

**Implementace:** aktivační události více Azure DB Cosmos naslouchání jedna kolekce

1. Víc funkcí Azure můžete vytvořit přidáním Azure Cosmos DB aktivačních událostí ke každému – všechny z nich naslouchat na stejnou změnit informační kanál data nákupního košíku. Všimněte si, že při naslouchání víc funkcí na stejné změnit informačního kanálu novou kolekci zapůjčení je vyžadována pro každou funkci. Další informace o zapůjčení kolekcí najdete v tématu [pochopení knihovně změnu kanálu procesoru](change-feed.md#understand-cf).
2. Vždy, když uživatelům nákupní košík při přidání nové položky, jednotlivé funkce nezávisle vyvolané změnu kanálu z kontejneru nákupní košík.
    * Jednu funkci může obsah aktuální košík použijte ke změně zobrazení položek, které může uživatel zajímá.
    * Celkový počet položek inventáře může aktualizovat jinou funkci.
    * Další funkce může odeslat informace o zákazníkovi pro některé produkty z marketingového oddělení, který je odešle propagační poštovní modul. 

    Všechny oddělení můžete vytvořit aktivační událost INSTEAD Azure Cosmos DB prostřednictvím naslouchání k informačnímu kanálu změn a ujistěte se, že se nebudou zpoždění důležité pořadí zpracování událostí v procesu.

Ve všech těchto případech použití, protože funkce má odpojené aplikace, nemusíte začne pracovat nové instance aplikace vždy. Místo toho Azure Functions otáčí až jednotlivých funkcí na dokončení diskrétní procesů podle potřeby.

## <a name="tooling"></a>Nástrojů

Nativní integrace mezi Azure Cosmos DB a Azure Functions je k dispozici na portálu Azure a Visual Studio 2017.
* Na portálu Azure Functions můžete vytvořit aktivační událost INSTEAD Azure Cosmos DB. Rychlý start pokyny najdete v tématu [na portálu Azure vytvořit aktivační událost INSTEAD Azure Cosmos DB](https://aka.ms/cosmosdbtriggerportalfunc) ![vytvořit aktivační událost INSTEAD Azure Cosmos DB na portálu Azure Functions](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* Na portálu Azure Functions můžete také přidat vazby Azure Cosmos DB vstupní a výstupní vazby na jiné typy služby aktivačních událostí. Rychlý start pokyny najdete v tématu [ukládání nestrukturovaných dat pomocí Azure Functions a Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Vytvořit aktivační událost INSTEAD Azure Cosmos DB na portálu Azure Functions](./media/serverless-computing-database/function-portal-input-binding.png)
*   Na portálu Azure Cosmos DB můžete přidat do existující aplikace funkce Azure ve stejné skupině prostředků aktivační procedury Azure Cosmos DB.
    ![Vytvořit aktivační událost INSTEAD Azure Cosmos DB na portálu Azure Functions](./media/serverless-computing-database/cosmos-db-portal.png)
* V aplikaci Visual Studio 2017 můžete vytvořit aktivační procedury Azure Cosmos DB pomocí integrované šablony:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]


## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Proč zvolit integrace Azure Functions pro výpočty bez serveru?

Azure Functions nabízí možnost vytvářet škálovatelné jednotky práce nebo stručným kusy logiky, která lze spustit na vyžádání, bez zřizování a správa infrastruktury. Pomocí Azure Functions nemáte k vytvoření plnohodnotném aplikace reagovat na změny v databázi Azure Cosmos DB, můžete vytvořit malé opakovaně použitelné funkce pro konkrétní úlohy. Kromě toho můžete taky dat databáze Azure Cosmos jako vstup nebo výstup do funkce Azure v reakci na události, například HTTP požadavků nebo vypršel aktivační události.

Azure Cosmos DB je doporučené databáze v architektuře bez serveru výpočetní z následujících důvodů:

* **Okamžitý přístup ke všem datům**: máte podrobné přístup ke každé hodnotě uložit, protože Azure Cosmos DB [automaticky indexuje](indexing-policies.md) všechna data ve výchozím nastavení a umožňuje okamžitě k dispozici tyto indexy. To znamená, že budete moci neustále dotazování, aktualizovat a přidání nových položek do databáze a mít okamžitý přístup prostřednictvím Azure Functions.

* **Schemaless**. Azure Cosmos DB je schemaless - je jednoznačně schopná zpracovat žádné datovým výstupem z funkce Azure. Tento přístup "zpracování nic" Díky je přehledné na vytvořit celou řadu funkcí, které veškerý výstup do databáze Azure Cosmos.

* **Škálovatelná propustnost**. Propustnost je možné rozšířit nahoru a dolů okamžitě v Azure Cosmos DB. Pokud máte stovkami nebo tisíci funkce dotazování a zápis ke stejné kolekci, můžete postupně škálovat vaše [RU/s](request-units.md) pro zpracování zátěže. Všechny funkce můžete pracovat paralelně pomocí přidělené RU/s a vaše data se musí být [konzistentní](consistency-levels.md).

* **Globální replikace**. Můžete replikovat data Azure Cosmos DB [po celém světě](distribute-data-globally.md) ke snížení latence, vyhledání geograficky nejblíže k kde jsou vaši uživatelé data. Jako s všechny dotazy na databázi Cosmos Azure, data z událostmi řízené aktivační události je přečíst data z Azure DB Cosmos nejblíže uživatele.

Pokud chcete integrovat Azure Functions k ukládání dat a nepotřebujete hloubkové indexování nebo pokud potřebujete k ukládání příloh a mediálních souborů [aktivační událost Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md) může být lepším řešením.

Výhody Azure Functions: 

* **Event-driven**. Azure Functions jsou událostmi řízené a poslouchat na změnu datového kanálu z Azure Cosmos DB. To znamená, nemusíte vytvářet naslouchání logiku, je právě dohlížet změny, které jste naslouchání pro. 

* **Žádná omezení**. Funkce provést v paralelní a službě monitoru je nestabilní až tolik, jako je třeba. Můžete nastavit parametry.

* **Vhodné pro Rychlé úlohy**. Služba otáčí si nové instance třídy funkce vždy, když událost aktivuje a uzavře, je při dokončení funkce. Platíte jenom čas, které jsou spuštěné funkcí.

Pokud si nejste jistí, jestli tok, Logic Apps, Azure Functions nebo webové úlohy jsou nejvhodnější pro implementaci, najdete v části [volba mezi toku, Logic Apps, funkce a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Další postup

Teď umožňuje připojit databázi Cosmos Azure a Azure Functions pro skutečných: 

* [Vytvořit aktivační událost INSTEAD Azure Cosmos DB na portálu Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Vytvořit aktivační událost INSTEAD HTTP funkce Azure s vazbou vstupní Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Ukládání nestrukturovaných dat pomocí Azure Functions a Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Azure Cosmos DB vazby a aktivační události](../azure-functions/functions-bindings-cosmosdb.md)


 



