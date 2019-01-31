---
title: Cenový model služby Azure Cosmos DB
description: Tento článek vysvětluje cenový model Azure Cosmos DB a jak se zjednoduší náklady na správu a plánování nákladů.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 997a80ed1a8089c5255292f23bc5dacf8a6cb0e7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452622"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Cenový model ve službě Azure Cosmos DB 

Cenový model služby Azure Cosmos DB zjednodušuje náklady na správu a plánování. Pomocí služby Azure Cosmos DB platíte za zřízenou propustnost a úložiště, které skutečně využijete.

* **Zřízené propustnosti**: Zřízená propustnost (také nazývané rezervovanou propustnost) zaručuje vysoký výkon v libovolném měřítku. Propustnost (RU/s), budete potřebovat a Azure Cosmos DB vyhrazuje prostředky potřebné k zajištění propustnosti nakonfigurované zadáte. Můžete se účtují po hodinách pro nejvyšší zřízená propustnost pro příslušné hodiny.

   > [!NOTE]
   > Protože model zřízená propustnost vyhrazuje prostředky do kontejneru nebo databáze, vám bude účtovat zřízená propustnost i v případě, že při spuštění žádných úloh.

* **Spotřebované úložiště**: Bude se vám účtovat paušální sazbou za celkovou velikost úložiště (GB) dat a indexy pro příslušné hodiny.

Zřízená propustnost, zadaný jako [jednotek žádostí](request-units.md) za sekundu (RU/s), umožňuje čtení nebo zápis dat do kontejnerů nebo databáze. Je možné [zřídit propustnost v databázi nebo kontejner](set-throughput.md). Podle svých potřeb pracovního vytížení, můžete škálovat propustnost směrem nahoru nebo dolů v každém okamžiku. Ceny za Azure Cosmos DB je flexibilní a je přímo úměrná propustnosti, který konfigurujete v databázi nebo kontejneru. Minimální hodnoty propustnosti a úložiště a zvýší hodnotu škálování poskytují celou řadu ceny oproti elasticitu spektra všech segmentů zákazníků, malé škálované do kontejnerů ve velkém měřítku. Každá databáze nebo kontejneru se účtuje podle počtu hodin za zřízenou propustnost v jednotkách 100 RU/s, přičemž minimum je 400 RU/s a úložiště využité GB. Na rozdíl od zřízené propustnosti a úložiště se účtuje na základě spotřeby. To znamená není nutné rezervovat předem jakékoli úložiště. Účtuje se vám jen za skutečně využité úložiště.

Další informace najdete v tématu [stránce s cenami služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) a [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md).

Cenový model ve službě Azure Cosmos DB je konzistentní napříč všechna rozhraní API. Další informace najdete v tématu [jak služby Azure Cosmos DB cenový model je nákladově efektivní pro zákazníky, kteří](total-cost-ownership.md). Je minimální propustnost na databázi nebo kontejner potřeba k tomu smlouvách SLA a při zvětšování a zmenšování zřízenou propustnost v $6 pro každého 100 RU/s.

Aktuálně je minimální cena za databázi a propustnost založenou na kontejnerech $24/ měsíc (viz [stránce s cenami služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) nejnovější informace. Pokud vaše úloha používá více kontejnerů, to, lze optimalizovat náklady pomocí úrovně propustnosti databáze, protože databáze úrovně propustnosti můžete mít libovolný počet kontejnerů v databázi pro sdílení obsahu propustnosti mezi kontejnery. Následující tabulka shrnuje zřízenou propustnost a náklady na různé entity:

|**Entity**  | **Minimální propustnost a nákladů** |**Škálování zvýší & náklady** |**Zřizování oboru** |
|---------|---------|---------|-------|
|Databáze    | 400 RU/s ($24/ měsíc)    | 100 RU/s (6 USD za měsíc)   |Propustnost je vyhrazená pro databázi a je sdílen kontejnery v rámci databáze |
|Kontejner     | 400 RU/s ($24/ měsíc)    | 100 RU/s (6 USD za měsíc)  |Propustnost je vyhrazená pro konkrétní kontejner |

Jak je znázorněno v předchozí tabulce, minimální propustnost ve službě Azure Cosmos DB se začne za cenu $24/ měsíc. Pokud byste začali s minimální propustnost a škálování v čase pro podporu vaše produkční úlohy, náklady se zvýší plynule, v přírůstcích po 6 USD za měsíc. Cenový model ve službě Azure Cosmos DB je flexibilní a je hladké zvýšení nebo snížení ceny při škálování směrem nahoru nebo dolů.

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma 

Azure Cosmos DB nabízí několik možností pro vývojáře, které je zdarma. K těmto možnostem patří:

* **Bezplatný účet Azure**: Azure nabízí [úroveň free](https://azure.microsoft.com/free/) , získáte 200 USD v kreditech Azure pro prvních 30 dnů a omezené množství bezplatných služeb po dobu 12 měsíců. Další informace najdete na stránce [bezplatného účtu Azure](../billing/billing-avoid-charges-free-account.md). Azure Cosmos DB je součástí bezplatného účtu Azure. Konkrétně pro Azure Cosmos DB nabízí bezplatný účet 5 GB úložiště a zřízenou propustnost 400 ru za celý rok. 

* **Vyzkoušejte si Azure Cosmos DB zdarma**: Azure Cosmos DB nabízí časově omezené prostředí s využitím vyzkoušejte Azure Cosmos DB pro bezplatné účty. Můžete vytvořit účet služby Azure Cosmos DB, vytvoření databáze a kolekce a spustit ukázkovou aplikaci pomocí rychlých startů a kurzů. Spuštění ukázkové aplikace bez přihlášení k účtu Azure nebo pomocí platební karty. [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) nabízí služby Azure Cosmos DB na jeden měsíc s možností obnovení vašeho účtu libovolný počet pokusů.

* **Emulátor služby Azure Cosmos DB**: Emulátor služby Azure Cosmos DB poskytuje místní prostředí, které emuluje služby Azure Cosmos DB pro účely vývoje. Emulátor se nabízí zdarma a s vysokou věrností ke cloudové službě. Pomocí emulátoru služby Azure Cosmos DB, můžete vyvíjet a testovat aplikace místně bez vytváření předplatného Azure a bez jakýchkoli nákladů. Aplikace můžete vyvíjet místně pomocí emulátoru před přechodem do produkčního prostředí. Jakmile budete spokojeni s funkcemi aplikace emulátoru, můžete přejít na účet služby Azure Cosmos DB v cloudu a výrazně úspory nákladů. Další informace o emulátoru najdete v tématu [pomocí služby Azure Cosmos DB pro vývoj a testování](local-emulator.md) , kde najdete další podrobnosti.

## <a name="pricing-with-reserved-capacity"></a>Ceny za vyhrazené kapacitě

Azure Cosmos DB [rezervované kapacity](cosmos-db-reserved-capacity.md) pomáhá Šetřete peníze optimalizací pro jeden rok nebo tři roky předem platíte za prostředky Azure Cosmos DB. Můžete výrazně snížit náklady s využitím jeden rok nebo tři roky závazek využití objednané a uložit mezi 20 – 65 % slevu oproti běžné ceny. Azure Cosmos DB vyhrazené kapacity pomáhá snížit náklady na dobu jeden nebo tři roky předem placení za zřízenou propustnost (RU/s) a získejte slevu na zřízenou propustnost. 

Záložní kapacitu poskytuje fakturace slevy a neovlivní jejich běhový stav vašich prostředků Azure Cosmos DB. Rezervované kapacity je k dispozici konzistentní ke všem rozhraním API, což zahrnuje SQL, MongoDB, Cassandra, Gremlin a tabulek Azure a všech oblastech po celém světě. Další informace o vyhrazené kapacitě v [předplatné pro prostředky Azure Cosmos DB pomocí záložní kapacitu](cosmos-db-reserved-capacity.md) článku a koupit rezervované kapacity z [webu Azure portal](https://portal.azure.com/).

## <a name="next-steps"></a>Další postup

Další informace o optimalizaci nákladů pro vaše prostředky Azure Cosmos DB v následujících článcích:

* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizovat náklady na účty Cosmos ve více oblastech](optimize-cost-regions.md)
* Další informace o [rezervované kapacity služby Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Další informace o [emulátor služby Azure Cosmos DB](local-emulator.md)
