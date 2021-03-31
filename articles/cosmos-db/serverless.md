---
title: Nabídka bez serveru založená na spotřebě v Azure Cosmos DB
description: Přečtěte si další informace o tom, Azure Cosmos DB nabídka bez serveru založená na spotřebě.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3ee8d5f36977a5a9f20c7e636118ffa9f6ee0b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570998"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB bez serveru (Preview)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB bez serveru vám umožní používat účet Azure Cosmos na základě spotřeby, kde se účtují jenom ty jednotky žádosti spotřebované vaší databázovou operací a úložiště spotřebované Vašimi daty. Kontejnery bez serveru můžou obsluhovat tisíce požadavků za sekundu bez minimálního nabití a nevyžadují se žádné plánování kapacity.

> [!IMPORTANT] 
> Máte jakoukoli zpětnou vazbu týkající se bez serveru? Chceme si vyzkoušet! Nehodí se k vyřazení zprávy na Azure Cosmos DB týmu bez serveru: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Při použití Azure Cosmos DB má každá databázová operace náklady vyjádřené v [jednotkách žádosti](request-units.md). Poplatky za tyto náklady závisí na typu účtu Azure Cosmos, který používáte:

- V režimu [zřízené propustnosti](set-throughput.md) je nutné provést potvrzení na určitou úroveň propustnosti (vyjádřenou v jednotkách žádosti za sekundu), která je zřízena na vašich databázích a kontejnerech. Náklady na vaše databázové operace se pak odečtou od počtu dostupných jednotek žádostí každou sekundu. Na konci fakturačního období se vám bude účtovat množství propustnosti, kterou jste zřídili.
- V režimu bez serveru nemusíte při vytváření kontejnerů v účtu Azure Cosmos zřizovat žádné propustnosti. Na konci fakturačního období se vám bude účtovat počet jednotek žádostí, které byly vaší databázovou operací spotřebovány.

## <a name="use-cases"></a>Případy použití

Azure Cosmos DB nejlépe vyhovuje vašim scénářům, kdy očekáváte **přerušované a nepředvídatelné přenosy** s dlouhou dobou nečinnosti. Vzhledem k tomu, že zřízení kapacity v takových situacích není nutné a může se jednat o náklady, Azure Cosmos DB serveru by se mělo zvážit v následujících případech použití:

- Začínáme s Azure Cosmos DB
- Běžící aplikace s
    - shluky, přerušované provozy, které jsou obtížné vypovědět, nebo
    - nízká (<10%) poměr přenosů z průměru na špičku
- Vývoj, testování, vytváření prototypů a spouštění v produkčních nových aplikacích, kde je vzorek provozu neznámý
- Integrace s výpočetními službami bez serveru, jako je [Azure Functions](../azure-functions/functions-overview.md)

Další informace o tom, jak vybrat nabídku, která nejlépe odpovídá vašemu použití, najdete v článku [jak si vybrat mezi zřízenou propustností a bez serveru](throughput-serverless.md) .

## <a name="using-serverless-resources"></a>Používání prostředků bez serveru

Bez serveru je nový typ účtu Azure Cosmos, což znamená, že při vytváření nového účtu musíte zvolit mezi **zřízenou propustností** a bez **serveru** . Abyste mohli začít pracovat bez serveru, musíte vytvořit nový účet bez serveru. V rámci verze Preview je jediným podporovaným způsobem, jak vytvořit nový účet bez serveru, [použití Azure Portal](create-cosmosdb-resources-portal.md). Migrace existujících účtů do nebo z režimu bez serveru se v tuto chvíli nepodporuje.

Jakýkoli kontejner, který je vytvořený v účtu bez serveru, je kontejner bez serveru. Kontejnery bez serveru zpřístupňují stejné možnosti jako kontejnery vytvořené v režimu zřízené propustnosti, takže můžete data číst, zapisovat a dotazovat přesně stejným způsobem. Účty a kontejnery bez serveru ale mají také specifické vlastnosti:

> [!IMPORTANT]
> Některá z těchto omezení můžou být nesnížíá nebo odebraná, když je server bez serveru všeobecně dostupný a **Váš názor** vám pomůže rozhodnout! Kontaktujte a řekněte nám víc o prostředí bez serveru: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Účet bez serveru se dá spustit jenom v jedné oblasti Azure. Po vytvoření nemůžete do účtu bez serveru přidat další oblasti Azure.
- Není možné povolit [funkci synapse Link Preview](synapse-link.md) na účet bez serveru.
- Propustnost zřizování se nevyžaduje u kontejnerů bez serveru, takže je možné použít následující příkazy:
    - Při vytváření kontejneru bez serveru nemůžete předat žádnou propustnost a v takovém případě vrátí chybu.
    - Nemůžete číst ani aktualizovat propustnost na kontejneru bez serveru a v takovém případě vrátí chybu.
    - Nelze vytvořit sdílenou databázi propustnosti v účtu bez serveru a v takovém případě vrátí chybu.
- Kontejnery bez serveru můžou ukládat maximálně 50 GB dat a indexů.

## <a name="monitoring-your-consumption"></a>Monitorování vaší spotřeby

Pokud jste v režimu zajištěné propustnosti používali Azure Cosmos DB dříve, zjistíte, že bez serveru nebudete mít k dispozici cenově výhodnou kapacitu, když váš provoz neodůvodňuje zřízenou kapacitu. Je možné, že vaše náklady budou méně předvídatelné, protože se vám bude účtovat na základě počtu požadavků, které vaše databáze zpracovala. Z tohoto důvodu je důležité, abyste měli přehled o své aktuální spotřebě.

Při procházení podokna **metrik** vašeho účtu najdete graf s názvem **jednotky žádosti spotřebované** na kartě **Přehled** . Tento graf znázorňuje počet jednotek požadavků, které váš účet spotřeboval:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Graf znázorňující spotřebované jednotky žádostí" border="false":::

Stejný graf můžete najít při použití Azure Monitor, jak je popsáno [zde](monitor-request-unit-usage.md). Všimněte si, že Azure Monitor vám umožní nastavit [Upozornění](../azure-monitor/alerts/alerts-metric-overview.md), která se můžou použít k upozornění, když spotřeba jednotky žádosti předala určitou prahovou hodnotu.

## <a name="performance"></a><a id="performance"></a>Výkon

Prostředky bez serveru poskytují specifické charakteristiky výkonu, které se liší od poskytování prostředků propustnosti. Až bude nabídka bez serveru všeobecně dostupná, latence u kontejnerů bez serveru se pokryje s cílem na úrovni služby (SLO) a 10 milisekundy pro čtení bodů a 30 milisekundami a méně pro zápisy. Operace čtení z bodu se skládá z načtení jedné položky podle jejího ID a hodnoty klíče oddílu.

## <a name="next-steps"></a>Další kroky

Začněte používat bez serveru s následujícími články:

- [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
- [Volba mezi zřízenou propustností a bezserverovým využitím](throughput-serverless.md)
- [Cenový model ve službě Azure Cosmos DB](how-pricing-works.md)
