---
title: Cenový model Azure Cosmos DB
description: V tomto článku se dozvíte o cenovém modelu Azure Cosmos DB a o tom, jak zjednodušuje správu nákladů a plánování nákladů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: b7d54dfe2efa82aaa650168c476da03d89fe4527
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490488"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Cenový model ve službě Azure Cosmos DB

Cenový model služby Azure Cosmos DB zjednodušuje správu a plánování nákladů. S Azure Cosmos DB platíte za operace, které v databázi provedete, a pro úložiště spotřebované Vašimi daty.

- **Databázové operace**: způsob, jakým se vám účtují operace s databází, závisí na typu účtu Azure Cosmos, který používáte.

  - **Zajištěná propustnost**: [zajištěná](set-throughput.md) propustnost (označovaná také jako rezervovaná propustnost) poskytuje vysoký výkon v jakémkoli měřítku. Zadáte propustnost, kterou potřebujete v [jednotkách žádosti](request-units.md) za sekundu (ru/s), a Azure Cosmos DB vyhradit prostředky potřebné k zajištění nakonfigurované propustnosti. Propustnost můžete [zřídit buď pro databázi, nebo pro kontejner](set-throughput.md). V závislosti na potřebách úloh můžete kdykoli škálovat propustnost nahoru nebo dolů nebo použít [Automatické škálování](provision-throughput-autoscale.md) (i když je v databázi nebo kontejneru zajištěná minimální propustnost, která zaručuje SLA). Za danou hodinu se účtuje po hodinách maximální zajištěné propustnosti.

   > [!NOTE]
   > Vzhledem k tomu, že model zřízené propustnosti vyhradí prostředky pro váš kontejner nebo databázi, bude se vám účtovat propustnost, kterou jste zřídili, i když nespustíte žádné úlohy.

  - Bez **serveru**: v režimu bez [serveru](serverless.md) nemusíte při vytváření prostředků v účtu Azure Cosmos zřizovat žádné propustnosti. Na konci fakturačního období se vám bude účtovat množství jednotek požadavků, které vaše databázové operace spotřebují.

- **Úložiště**: za určitou hodinu se účtuje paušální sazba za celkovou velikost úložiště (v GB) spotřebované Vašimi daty a indexy. Služba Storage se účtuje na základě spotřeby, takže nemusíte rezervovat žádné úložiště předem. Účtuje se vám jenom úložiště, které spotřebováváte.

Cenový model v Azure Cosmos DB je konzistentní napříč všemi rozhraními API. Další informace najdete na stránce s [cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), [principu Azure Cosmos DB fakturace](understand-your-bill.md) a [způsobu, jakým je cenově Azure Cosmos DB cenový model pro zákazníky](total-cost-ownership.md)cenově výhodnější.

Pokud účet Azure Cosmos DB nasadíte do oblasti mimo státní správu v USA, je minimální cena za propustnost databáze i kontejneru v režimu zřízené propustnosti. V režimu bez serveru se neúčtují žádné minimální ceny. Ceny se liší v závislosti na oblasti, kterou používáte, na nejnovější informace o cenách najdete na [stránce s cenami Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="try-azure-cosmos-db-for-free"></a>Vyzkoušejte si Azure Cosmos DB zdarma

Azure Cosmos DB nabízí zdarma mnoho možností pro vývojáře. K těmto možnostem patří:

* **Azure Cosmos DB úroveň Free**: Azure Cosmos dB úrovně Free usnadňuje začátek, vývoj a testování aplikací nebo dokonce i spouštění malých produkčních úloh zdarma. Pokud je na účtu povolená úroveň Free, získáte po dobu životnosti účtu prvních 400 RU/s a 5 GB úložiště v účtu Free. Můžete mít až jeden účet bezplatné úrovně na jedno předplatné Azure a při vytváření účtu musí být výslovný souhlas. Pokud chcete začít, [vytvořte nový účet v Azure Portal s povolenou úrovní Free](create-cosmosdb-resources-portal.md) nebo použijte [šablonu ARM](./manage-with-templates.md#free-tier).

* **Bezplatný účet Azure**: Azure nabízí [bezplatnou úroveň](https://azure.microsoft.com/free/) , která vám poskytne $200 kredity Azure během prvních 30 dnů a omezené množství bezplatných služeb na 12 měsíců. Další informace najdete na stránce [bezplatného účtu Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB je součástí bezplatného účtu Azure. Tento bezplatný účet Azure Cosmos DB konkrétně nabízí 5 GB úložiště a 400 RU/s zřízené propustnost po celý rok.

* **Vyzkoušejte si Azure Cosmos DB zdarma**: Azure Cosmos DB nabízí časově omezené prostředí pomocí funkce vyzkoušet Azure Cosmos DB pro bezplatné účty. Můžete vytvořit účet Azure Cosmos DB, vytvořit databázi a kolekce a spustit ukázkovou aplikaci pomocí rychlých startů a kurzů. Ukázkovou aplikaci můžete spustit bez přihlášení k odběru účtu Azure nebo pomocí platební karty. [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) nabízených nabídek Azure Cosmos DB po dobu jednoho měsíce, s možností obnovit účet několikrát.

* **Emulátor Azure Cosmos DB**: Azure Cosmos DB emulátor poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Emulátor se nabízí zdarma a vysoká věrnost cloudové služby. Pomocí emulátoru Azure Cosmos DB můžete své aplikace vyvíjet a testovat v místním prostředí, aniž byste museli vytvářet předplatné Azure ani náklady. Své aplikace můžete vyvíjet pomocí místního emulátoru před tím, než budete pokračovat v produkci. Až budete spokojeni s funkcemi aplikace proti emulátoru, můžete přepnout na použití účtu Azure Cosmos DB v cloudu a významně ušetřit náklady. Další informace o emulátoru najdete v článku [použití Azure Cosmos DB pro vývoj a testování](local-emulator.md) .

## <a name="pricing-with-reserved-capacity"></a>Ceny s rezervovanou kapacitou

Azure Cosmos DB [Rezervovaná kapacita](cosmos-db-reserved-capacity.md) vám pomůže ušetřit peníze při použití režimu zajištěné propustnosti Předplacením za Azure Cosmos DB prostředky po dobu jednoho roku nebo tří let. V porovnání s běžnými cenami můžete významně snížit náklady za jednoleté nebo tříleté závazky a ušetřit 20-65% slev. Azure Cosmos DB Rezervovaná kapacita pomáhá snížit náklady tím, že se předem platíte za zřízenou propustnost (RU/s) po dobu jednoho roku nebo tři roky a získáte slevu na zřízenou propustnost. 

Rezervovaná kapacita poskytuje slevu z faktury a neovlivňuje běhový stav prostředků Azure Cosmos DB. Rezervovaná kapacita je jednotně dostupná pro všechna rozhraní API, která zahrnují MongoDB, Cassandra, SQL, Gremlin a Azure a všechny oblasti po celém světě. Další informace o rezervované kapacitě najdete v tématu [platba za Azure Cosmos DB prostředky s využitím rezervované kapacity](cosmos-db-reserved-capacity.md) a zakoupení rezervované kapacity z [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů na prostředky Azure Cosmos DB najdete v následujících článcích:

* Informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Cosmos s více oblastmi](optimize-cost-regions.md)
* Další informace o [Azure Cosmos DB rezervované kapacity](cosmos-db-reserved-capacity.md)
* Další informace o [emulátoru Azure Cosmos DB](local-emulator.md)