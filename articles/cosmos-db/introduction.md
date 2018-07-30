---
title: Úvod do služby Azure Cosmos DB | Microsoft Docs
description: Přečtěte si něco o službě Azure Cosmos DB. Tato globálně distribuovaná databáze s více modely je navržená s ohledem na nízkou latenci, elastickou škálovatelnost a vysokou dostupnost a nabízí nativní podporu pro data NoSQL.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 488ec66eb485761d484e305c298dfb9117745a42
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145277"
---
# <a name="welcome-to-azure-cosmos-db"></a>Vítá vás Azure Cosmos DB

Azure Cosmos DB je globálně distribuovaná databáze Microsoftu pro více modelů. Stačí kliknout na tlačítko a Azure Cosmos DB vám umožní elasticky a nezávisle škálovat propustnost a úložiště v řadě geografických oblastí Azure. Nabízí záruku propustnosti, latence, dostupnosti a konzistence s kompletními [smlouvami o úrovni služeb](https://aka.ms/acdbsla) (SLA), které někdy nenabízejí žádné jiné databázové služby. [Službu Azure Cosmos DB můžete vyzkoušet zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure, poplatků a závazků.

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB je globálně distribuovaná databázová služba od Microsoftu, která nabízí elastické škálování na více instancí, zaručenou nízkou latenci, pět modelů konzistence a komplexní zaručené smlouvy SLA.](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>Klíčové funkce
Azure Cosmos DB jakožto globálně distribuovaná databázová služba s více modely usnadňuje vytváření škálovatelných aplikací s rychlou odezvou v globálním měřítku:

* **Globální distribuce na klíč**
    * Můžete [distribuovat data](distribute-data-globally.md) do libovolného počtu [oblastí Azure](https://azure.microsoft.com/regions/), a to [jediným kliknutím na tlačítko](tutorial-global-distribution-sql-api.md). Díky tomu můžete umístit data tam, kde jsou vaši uživatelé, a zajistit uživatelům nejnižší možnou latenci. 
    * Pomocí rozhraní API pro vícenásobné navádění služby Azure Cosmos DB bude aplikace vždycky vědět, kde je nejbližší oblast, a požadavky bude odesílat do nejbližšího datacentra. Toto vše je možné bez změny konfigurace. Nastavíte oblast zápisu a tolik oblastí čtení, kolik chcete, a o zbytek se postará služba za vás.
    * Při přidávání oblastí k databázi Azure Cosmos DB a jejich odebírání není potřeba vaši aplikaci znovu nasazovat. Díky možnostem rozhraní API pro vícenásobné navádění je stále vysoce dostupná.

* **Více datových modelů a oblíbená rozhraní API pro přístup k datům a dotazování na ně**
    * Datový model založený na sekvenci záznamu atomů (ARS), na kterém je služba Azure Cosmos DB postavena, nativně podporuje více datových modelů, mimo jiné modely dokumentů, grafů, párů klíč-hodnota, tabulek a sloupcových dat.
    * Rozhraní API pro následující datové modely jsou podporována se sadami SDK dostupnými v několika jazycích:
        * [SQL API:](sql-api-introduction.md) Databázový stroj JSON bez schématu s bohatými možnostmi dotazování SQL.
        * [MongoDB API](mongodb-introduction.md): Masivně škálovatelné rozhraní *MongoDB jako služba* s využitím platformy Azure Cosmos DB. Kompatibilní se stávajícími knihovnami, ovladači, nástroji a aplikacemi MongoDB.
        * [Cassandra API](cassandra-introduction.md): Globálně distribuovatelné rozhraní Cassandra jako služba s využitím platformy Azure Cosmos DB. Kompatibilní se stávajícími knihovnami, ovladači, nástroji a aplikacemi [Apache Cassandra](https://cassandra.apache.org/).
        * [Gremlin API](graph-introduction.md): Plně spravovaná, horizontálně škálovatelná služba databáze grafů usnadňující vytváření a spouštění aplikací, které pracují s vysoce propojenými datovými sadami podporujícími rozhraní Open Graph API (na základě [specifikace Apache TinkerPop](http://tinkerpop.apache.org/), Apache Gremlin).
        * [Table API](table-introduction.md): Databázová služba pro páry klíč-hodnota vytvořená pro poskytování prémiových možností (například automatické indexování, garantovaná nízká latence, globální distribuce) existujícím aplikacím Azure Table Storage, a to bez nutnosti jakýchkoli změn těchto aplikací.
        * Další datové modely a rozhraní API se připravují.

* **Elastické a nezávislé škálování propustnosti a úložiště na vyžádání, celosvětově**
    * Snadno škálujte propustnost databáze v přírůstcích po [sekundách](request-units.md), přičemž to můžete kdykoli změnit. 
    * Škálujte velikost úložiště [transparentně a automaticky](partition-data.md) s ohledem na současné a budoucí požadavky na velikost.

* **Vytváření aplikací s rychlou odezvou a vysokou důležitostí pro chod firmy**
    * Azure Cosmos DB zaručuje svým zákazníkům koncovou nízkou latentnost na 99. percentilu. 
    * Pro typickou 1kB položku zaručuje služba Cosmos DB koncovou latenci čtení pod 10 ms a indexovaných zápisů pod 15 ms na 99. percentilu v rámci stejné oblasti Azure. Střední latence jsou výrazně nižší (pod 5 ms).

* **Zajištění neustálé dostupnosti**
    * Smlouva SLA o 99,99% dostupnosti pro všechny účty databáze v jedné oblasti a 99,999% dostupnosti čtení pro všechny účty databáze ve více oblastech.
    * Nasaďte je do libovolného počtu [oblastí Azure](https://azure.microsoft.com/regions) a zajistěte vyšší dostupnost a lepší výkon.
    * Dynamicky nastavte priority jednotlivých oblastí, [simulujte selhání](regional-failover.md) jedné nebo více oblastí se zárukou nulových ztrát dat a otestujte tak kompletní dostupnost celé aplikace (nad rámec databáze). 

* **Psaní globálně distribuovaných aplikací správným způsobem**
    * Pět dobře definovaných, praktických a intuitivních [modelů konzistence](consistency-levels.md) poskytuje celé spektrum od silné konzistence podobné SQL až po rozvolněnou konzistenci typu Případné, která je podobná NoSQL, a vše mezi tím. 
  
* **Záruka vrácení peněz**
    * Špičkové komplexní a finančně zajištěné [smlouvy o úrovni služeb](https://aka.ms/acdbsla) (SLA) pro zajištění dostupnosti, latence, propustnosti a konzistence klíčových dat. 

* **Není potřeba správa schématu/indexu databáze**
    * Schéma vaší aplikace můžete rychle iterovat, aniž byste si dělali starosti o schéma databáze nebo správu indexu.
    * Databázový stroj služby Azure Cosmos DB je zcela nezávislý na schématu – automaticky indexuje všechna data, která ingestuje, bez vyžadování jakéhokoli schématu nebo indexů a zajišťuje bleskově rychlé dotazy. 

* **Nízké náklady na vlastnictví**
    * Toto řešení je pětkrát až desetkrát méně nákladné než nespravované řešení nebo místní řešení NoSQL.
    * Třikrát levnější než AWS DynamoDB nebo Google Spanner.

## <a name="capability-comparison"></a>Porovnání schopností

Azure Cosmos DB nabízí ty nejlepší možnosti tradičních relačních a nerelačních databází.

| Možnosti | Relační databáze   | Nerelační databáze (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Globální distribuce | Ne | Ne | Ano, distribuce na klíč ve více než 30 oblastech s rozhraními API pro vícenásobné navádění|
| Horizontální škálování | Ne | Ano | Ano, můžete nezávisle škálovat úložiště a propustnost | 
| Záruky latence | Ne | Ano | Ano, 99 % čtení do 10 ms a zápisů do 15 ms | 
| Vysoká dostupnost | Ne | Ano | Ano, služba Azure Cosmos DB je vždycky aktivní, má dobře definované kompromisy PACELC a poskytuje možnosti automatického a ručního převzetí služeb při selhání.|
| Datový model + API | Relační + SQL | Více modelů + OSS API | Více modelů + SQL + OSS API (další už brzy) |
| Smlouvy SLA | Ano | Ne | Ano, komplexní smlouvy SLA pro latenci, propustnost, konzistenci a dostupnost |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Řešení, kterým služba Azure Cosmos DB přináší výhody

[Garantovaná](https://azure.microsoft.com/support/legal/sla/cosmos-db/) vysoká dostupnost, vysoká propustnost, nízká latence a přizpůsobitelná konzistence ve službě Azure Cosmos DB bude výhodná pro všechny [webové, mobilní a herní aplikace a aplikace IoT](use-cases.md), které potřebují zpracovávat obrovské množství dat, čtení a zápisů v [globálním](distribute-data-globally.md) měřítku s dobou odezvy téměř v reálném čase. Další informace o možnostech použití služby Azure Cosmos DB pro [IoT a telematiku](use-cases.md#iot-and-telematics), [maloobchod a marketing](use-cases.md#retail-and-marketing), [hry](use-cases.md#gaming) a [webové a mobilní aplikace](use-cases.md#web-and-mobile-applications)

## <a name="next-steps"></a>Další kroky
Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s rozhraním MongoDB API služby Azure Cosmos DB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
