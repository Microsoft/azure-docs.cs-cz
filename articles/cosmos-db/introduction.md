---
title: Úvod do služby Azure Cosmos DB
description: Přečtěte si něco o službě Azure Cosmos DB. Tato globálně distribuovaná databáze s více modely je navržená s ohledem na nízkou latenci, elastickou škálovatelnost a vysokou dostupnost a nabízí nativní podporu pro data NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 012df825f38d292d0b0549701ad90334e3e30bb0
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637009"
---
# <a name="welcome-to-azure-cosmos-db"></a>Vítá vás Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

V současné době se od aplikací žádá, aby byly vysoce responzivní a vždy online. Aby bylo možné zajistit nízkou latenci a vysokou dostupnost, instance aplikací je potřeba nasazovat do datových center, která se nachází v blízkosti uživatelů. Aplikace musí v reálném čase reagovat na velké změny ve využití během špiček, ukládat stále větší a větší objemy dat a umožnit jejich dostupnost uživatelům v řádech milisekund.

Azure Cosmos DB je plně spravovaná databáze NoSQL pro vývoj moderních aplikací. Jednorázová doba odezvy v milisekundách a automatická a okamžitá škálovatelnost, zaručená rychlost v jakémkoli měřítku. Provozní kontinuita je zaručená dostupností, která je zajištěná smlouvou [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) a zabezpečením na podnikové úrovni. Vývoj aplikací je rychlejší a produktivnější díky klíč distribuci dat na celém světě, open source rozhraní API a sadách SDK pro oblíbené jazyky. Jako plně spravovaná služba Azure Cosmos DB přebírá správu databáze z vašich rukou s automatickou správou, aktualizacemi a opravami. Taky zajišťuje správu kapacity s cenově efektivními možnostmi bez serveru a automatickým škálováním, které reagují na aplikaci, musí odpovídat kapacitě na vyžádání.

Můžete [vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, zdarma a závazků nebo využít [Azure Cosmos dB úrovně Free](optimize-dev-test.md#azure-cosmos-db-free-tier) k získání účtu s prvním 400 ru/s a 5 GB volného místa.

> [!div class="nextstepaction"]
> [Vyzkoušet Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB je plně spravovaná databáze NoSQL pro vývoj moderních aplikací." border="false":::

## <a name="key-benefits"></a>Klíčové výhody

### <a name="guaranteed-speed-at-any-scale"></a>Zaručená rychlost v jakémkoli měřítku

Získejte nesrovnatelnou rychlost a propustnost [zálohovanou](https://azure.microsoft.com/support/legal/sla/cosmos-db) smlouvou SLA, rychlý globální přístup a rychlou pružnost.

- Přístup v reálném čase s globálním latencí pro čtení a zápis a propustnost a konzistence [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Zápisy do několika oblastí a distribuce dat do libovolné oblasti Azure pomocí kliknutí na tlačítko.
- Nezávislé a elastické škálování úložiště a propustnosti napříč libovolnou oblastí Azure – dokonce i během nepředvídatelných nárůstů provozu – pro neomezené škálování po celém světě.

### <a name="simplified-application-development"></a>Zjednodušený vývoj aplikací

Vytvářejte rychle sestavení pomocí rozhraní API open source, více sad SDK, dat bez schématu a analýz No-ETL přes provozní data.

- Hluboce integrovaná s klíčovými službami Azure, které se používají v moderních cloudových aplikacích pro vývoj aplikací, včetně Azure Functions, IoT Hub, AKS (Azure Kubernetes Service), App Service a dalších.
- Vyberte si z více databázových rozhraní API, včetně rozhraní API nativního jádra (SQL), rozhraní API pro MongoDB, rozhraní API Cassandra, rozhraní Gremlin API a rozhraní API pro tabulky.
- Vytvářejte aplikace na rozhraní API pro jádro (SQL) pomocí jazyků podle vašeho výběru pomocí sad SDK pro .NET, Java, Node.js a Python. Nebo můžete zvolit ovladače pro jakékoli jiné databázové rozhraní API.
- Pomocí služby Azure synapse Analytics spouštějte v reálném čase provozní data, která jsou uložená v Azure Cosmos DB, analýza bez ETL.
- Kanál změn umožňuje snadno sledovat a spravovat změny v kontejnerech databáze a vytvářet události aktivované pomocí Azure Functions.
- Služba bez schématu Azure Cosmos DB automaticky indexuje všechna vaše data, bez ohledu na datový model, aby bylo možné doručovat rychlé dotazy neuvěřitelně.

### <a name="mission-critical-ready"></a>Připraveno pro klíčové úkoly

Zabezpečte provozní kontinuitu, 99,999% dostupnost a zabezpečení na podnikové úrovni pro každou aplikaci.

- Azure Cosmos DB nabízí komplexní sadu [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) , včetně špičkové dostupnosti v oboru po celém světě.
- Snadná distribuce dat do libovolné oblasti Azure pomocí automatické replikace dat. Při použití silné konzistence užívejte v případě vysoké konzistence nenáročné zápisy na více oblastí nebo RPO 0.
- Využijte místní šifrování na podnikové úrovni s klíčem, který se spravuje sami.
- Řízení přístupu na základě role v Azure udržuje vaše data v bezpečí a nabízí jemně vyladěný ovládací prvek.

### <a name="fully-managed-and-cost-effective"></a>Plně spravovaná a nákladově efektivní

Komplexní Správa databází s využitím bez serveru a automatického škálování, které vyhovuje potřebám vaší aplikace a celkových nákladů na vlastnictví

- Plně spravovaná databázová služba. Automatické, žádné dotykové ovládání, údržba, opravy a aktualizace, které šetří čas a peníze pro vývojáře.
- Nákladově efektivní možnosti pro nepředvídatelné nebo občasné úlohy libovolné velikosti a škálování, které vývojářům umožňují snadno začít, aniž by museli plánovat nebo spravovat kapacitu.
- Model bez serveru nabízí službě nárazové úlohy automatické a reagující na správu nárůstů provozu na vyžádání.
- Automatické škálování zřízené propustnosti automaticky a okamžitě škáluje kapacitu pro nepředvídatelné úlohy a přitom udržuje [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Řešení, kterým služba Azure Cosmos DB přináší výhody

Všechny [aplikace pro web, mobilní zařízení, hry a IoT](use-cases.md) , které potřebují zpracovávat obrovské objemy dat, čtení a zápisy v [globálním měřítku](distribute-data-globally.md) s téměř skutečnými dobami odezvy pro nejrůznější data, budou mít výhodu Cosmos DB [garantované vysoké dostupnosti](https://azure.microsoft.com/support/legal/sla/cosmos-db/), vysoké propustnosti, nízké latence a přizpůsobitelné konzistenci. Přečtěte si, jak můžete Azure Cosmos DB použít k sestavování [IoT a telematických](use-cases.md#iot-and-telematics), [maloobchodních a marketingových](use-cases.md#retail-and-marketing), [herních](use-cases.md#gaming) a [webových a mobilních aplikací](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Další kroky

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

- [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
- [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](create-mongodb-nodejs.md)
- [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
- [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
- [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)