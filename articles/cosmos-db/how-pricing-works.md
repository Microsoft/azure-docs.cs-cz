---
title: Cenový model Azure Cosmos DB
description: Tento článek vysvětluje cenový model Azure Cosmos DB a jak zjednodušuje správu nákladů a plánování nákladů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977540"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Cenový model ve službě Azure Cosmos DB 

Cenový model služby Azure Cosmos DB zjednodušuje správu a plánování nákladů. V případě služby Azure Cosmos DB platíte za zřízenou propustnost a spotřebované úložiště.

* **Zřízená propustnost**: Zřízená propustnost (označovaná také jako vyhrazená propustnost) zaručuje vysoký výkon v libovolném měřítku. Zadáte propustnost (RU/s), které potřebujete, a Azure Cosmos DB věnuje prostředky potřebné k zajištění nakonfigurované propustnost. Účtuje se vám každou hodinu pro maximální zřízenou propustnost za danou hodinu.

   > [!NOTE]
   > Vzhledem k tomu, že zřízený model propustnost i v případě, že nespustíte žádné úlohy, bude vám zzřízená propustnost účtována i v případě, že nespustíte žádné úlohy.

* **Spotřebované úložiště**: Účtuje se vám paušální sazba za celkové množství úložiště (GBs) spotřebované pro data a indexy za danou hodinu.

Zřízená propustnost, určená jako [jednotky požadavků](request-units.md) za sekundu (RU/s), umožňuje číst nebo zapisovat data do kontejnerů nebo databází. Propustnost můžete [zřídit v databázi nebo v kontejneru](set-throughput.md). Na základě vašich potřeb pracovního vytížení můžete kdykoli škálovat propustnost nahoru/dolů. Ceny Azure Cosmos DB jsou elastické a jsou úměrné propustnosti, kterou nakonfigurujete v databázi nebo v kontejneru. Minimální hodnota propustnosti a úložiště a přírůstky škály poskytují celou škálu spektra ceny a pružnosti všem segmentům zákazníků, od malých až po rozsáhlé kontejnery. Každá databáze nebo kontejner se účtuje po hodinách za propustnost zřízenou v jednotkách 100 RU/s, s minimálně 400 RU/s a úložiště spotřebované v CENTRÁLNÍCH POD. Na rozdíl od zřízené propustnosti se úložiště účtuje na základě spotřeby. To znamená, že nemusíte rezervovat žádné úložiště předem. Účtuje se vám pouze úložiště, které spotřebováváte.

Další informace najdete na [stránce s cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) a [principy vaší faktury Azure Cosmos DB](understand-your-bill.md).

Cenový model v Azure Cosmos DB je konzistentní ve všech api. Další informace najdete v [tématu Jak je cenový model Azure Cosmos DB pro zákazníky nákladově efektivní](total-cost-ownership.md). Existuje minimální propustnost požadovaná v databázi nebo kontejneru k zajištění SLA a můžete zvýšit nebo snížit zřízenou propustnost o $6 pro každých 100 RU/s.

V současné době minimální cena pro databázi a propustnost na základě kontejneru je $24/month (nejnovější informace najdete na [stránce s cenami azure cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/) Pokud vaše úloha používá více kontejnerů, může být optimalizována pro náklady pomocí propustnosti na úrovni databáze, protože propustnost na úrovni databáze umožňuje mít libovolný počet kontejnerů v databázi, které sdílejí propustnost mezi kontejnery. Následující tabulka shrnuje zřízenou propustnost a náklady pro různé entity:

|**Entita**  | **Minimální propustnost & náklady** |**Přírůstky v měřítku & náklady** |**Obor zřizování** |
|---------|---------|---------|-------|
|Databáze    | 400 RU/s ($24/měsíc)    | 100 RU/s ($6/měsíc)   |Propustnost je vyhrazena pro databázi a je sdílena kontejnery v rámci databáze. |
|Kontejner     | 400 RU/s ($24/měsíc)    | 100 RU/s ($6/měsíc)  |Propustnost je vyhrazena pro konkrétní kontejner. |

Jak je znázorněno v předchozí tabulce, minimální propustnost v Azure Cosmos DB začíná za cenu $24/měsíc. Pokud začnete s minimální propustností a v průběhu času navýšit kapacitu, abyste podpořili produkční úlohy, vaše náklady porostou hladce, v přírůstcích $ 6 /měsíc. Cenový model v Azure Cosmos DB je elastický a při škálování na vertekonnebo dolů dochází k hladkému zvýšení nebo snížení ceny.

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma 

Azure Cosmos DB nabízí několik možností pro vývojáře k němu zdarma. K těmto možnostem patří:

* **Bezplatný účet Azure:** Azure nabízí [bezplatnou úroveň,](https://azure.microsoft.com/free/) která vám poskytuje kredity Azure ve výši $200 za prvních 30 dní a omezené množství bezplatných služeb po dobu 12 měsíců. Další informace najdete na stránce [bezplatného účtu Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB je součástí bezplatného účtu Azure. Konkrétně pro Azure Cosmos DB, tento bezplatný účet nabízí úložiště 5 GB a 400 RU zřízené propustnostpro celý rok. 

* **Vyzkoušejte Azure Cosmos DB zdarma**: Azure Cosmos DB nabízí časově omezené prostředí pomocí vyzkoušet Azure Cosmos DB pro bezplatné účty. Můžete vytvořit účet Azure Cosmos DB, vytvořit databázi a kolekce a spustit ukázkovou aplikaci pomocí rychlých startů a kurzů. Ukázkovou aplikaci můžete spustit bez přihlášení k odběru účtu Azure nebo pomocí platební karty. [Vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) nabízí Azure Cosmos DB po dobu jednoho měsíce, s možností obnovení účtu libovolný počet.

* **Emulátor Azure Cosmos DB**: Emulátor Azure Cosmos DB poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Emulátor je nabízen zdarma a s vysokou věrností cloudové služby. Pomocí emulátoru Azure Cosmos DB můžete vyvíjet a testovat aplikace místně, bez vytvoření předplatného Azure nebo vzniklých jakýchkoli nákladů. Můžete rozvíjet aplikace pomocí emulátoru místně před vstupem do produkčního prostředí. Poté, co jste spokojeni s funkcemi aplikace proti emulátoru, můžete přepnout na použití účtu Azure Cosmos DB v cloudu a výrazně ušetřit na nákladech. Další informace o emulátoru najdete [v článku použití Azure Cosmos DB pro vývoj a testování](local-emulator.md) článku pro další podrobnosti.

## <a name="pricing-with-reserved-capacity"></a>Ceny s rezervovanou kapacitou

[Rezervovaná kapacita](cosmos-db-reserved-capacity.md) Azure Cosmos DB vám pomůže ušetřit peníze tak, že předplatíte prostředky Azure Cosmos DB na jeden rok nebo tři roky. Můžete výrazně snížit své náklady s jednoletými nebo tříletými počátečními závazky a ušetřit 20-65% slevy ve srovnání s běžnými cenami. Vyhrazená kapacita Azure Cosmos DB vám pomůže snížit náklady tak, že předplatíte zřízenou propustnost (RU/s) po dobu jednoho roku nebo tří let a získáte slevu na zřízené propustnosti. 

Rezervovaná kapacita poskytuje slevu z faktury a neovlivňuje běhový stav prostředků Azure Cosmos DB. Rezervovaná kapacita je konzistentně k dispozici pro všechna rozhraní API, která zahrnují MongoDB, Cassandra, SQL, Gremlin a Azure Tables a všechny oblasti po celém světě. Další informace o rezervované kapacitě najdete v [části Předplacené prostředky služby Azure Cosmos DB s](cosmos-db-reserved-capacity.md) článkem o rezervované kapacitě a zakoupením rezervované kapacity z [portálu Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů na prostředky Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)
* Další informace o [optimalizaci nákladů na účty Cosmos s více oblastmi](optimize-cost-regions.md)
* Informace o [rezervované kapacitě Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Informace o [emulátoru Azure Cosmos DB](local-emulator.md)
