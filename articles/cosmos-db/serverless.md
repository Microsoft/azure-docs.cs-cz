---
title: Nabídka bez serveru založená na spotřebě v Azure Cosmos DB
description: Přečtěte si další informace o tom, Azure Cosmos DB nabídka bez serveru založená na spotřebě.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 75d22a5021c7c8ae3a12f25644f2875e0ccf8cdd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098752"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB bez serveru (Preview)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT]
> Azure Cosmos DB bez serveru je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouva SLA a nedoporučuje se pro produkční úlohy. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Azure Cosmos DB bez serveru vám umožní používat účet Azure Cosmos na základě spotřeby, kde se účtují jenom ty jednotky žádosti spotřebované vaší databázovou operací a úložiště spotřebované Vašimi daty. Při použití Azure Cosmos DB v režimu bez serveru se neúčtují žádné minimální poplatky.

> [!IMPORTANT] 
> Máte jakoukoli zpětnou vazbu týkající se bez serveru? Chceme si vyzkoušet! Nehodí se k vyřazení zprávy na Azure Cosmos DB týmu bez serveru: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

Při použití Azure Cosmos DB má každá databázová operace náklady vyjádřené v [jednotkách žádosti](request-units.md). Poplatky za tyto náklady závisí na typu účtu Azure Cosmos, který používáte:

- V režimu [zřízené propustnosti](set-throughput.md) je nutné provést potvrzení na určitou úroveň propustnosti (vyjádřenou v jednotkách žádosti za sekundu), která je zřízena na vašich databázích a kontejnerech. Náklady na vaše databázové operace se pak odečtou od počtu dostupných jednotek žádostí každou sekundu. Na konci fakturačního období se vám bude účtovat množství propustnosti, kterou jste zřídili.
- V režimu bez serveru nemusíte při vytváření kontejnerů v účtu Azure Cosmos zřizovat žádné propustnosti. Na konci fakturačního období se vám bude účtovat počet jednotek žádostí, které byly vaší databázovou operací spotřebovány.

## <a name="use-cases"></a>Případy použití

Azure Cosmos DB nejlépe vyhovuje scénářům, kde očekáváte:

- **Lehký provoz** : vzhledem k tomu, že zřízení kapacity v takových situacích není nutné a může být nákladově zakazují
- **Střední úroveň shlukování** : protože kontejnery bez serveru můžou doručovat až 5 000 jednotek žádostí za sekundu.
- **Střední výkon** : vzhledem k tomu, že kontejnery bez serveru mají [specifické charakteristiky výkonu](#performance)

Z těchto důvodů je Azure Cosmos DB bez serveru zvážit pro následující typy úloh:

- Vývoj
- Testování
- Prototyping
- Testování konceptu
- Nekritická aplikace s lehkým provozem

Další informace o tom, jak vybrat nabídku, která nejlépe odpovídá vašemu použití, najdete v článku [jak si vybrat mezi zřízenou propustností a bez serveru](throughput-serverless.md) .

## <a name="using-serverless-resources"></a>Používání prostředků bez serveru

Bez serveru je nový typ účtu Azure Cosmos, což znamená, že při vytváření nového účtu musíte zvolit mezi **zřízenou propustností** a bez **serveru** . Abyste mohli začít pracovat bez serveru, musíte vytvořit nový účet bez serveru. V rámci verze Preview je jediným podporovaným způsobem, jak vytvořit nový účet bez serveru, [použití Azure Portal](create-cosmosdb-resources-portal.md). Migrace existujících účtů do nebo z režimu bez serveru se v tuto chvíli nepodporuje.

> [!NOTE]
> V současné době se podporuje jenom na serveru rozhraní API pro Azure Cosmos DB Core (SQL).

Jakýkoli kontejner, který je vytvořený v účtu bez serveru, je kontejner bez serveru. Kontejnery bez serveru zpřístupňují stejné možnosti jako kontejnery vytvořené v režimu zřízené propustnosti, takže můžete data číst, zapisovat a dotazovat přesně stejným způsobem. Účty a kontejnery bez serveru ale mají také specifické vlastnosti:

> [!IMPORTANT]
> Některá z těchto omezení můžou být nesnížíá nebo odebraná, když je server bez serveru všeobecně dostupný a **Váš názor** vám pomůže rozhodnout! Kontaktujte a řekněte nám víc o prostředí bez serveru: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

- Účet bez serveru se dá spustit jenom v jedné oblasti Azure. Po vytvoření nemůžete do účtu bez serveru přidat další oblasti Azure.
- Není možné povolit [funkci synapse Link Preview](synapse-link.md) na účet bez serveru.
- Propustnost zřizování se nevyžaduje u kontejnerů bez serveru, takže je možné použít následující příkazy:
    - Při vytváření kontejneru bez serveru nemůžete předat žádnou propustnost a v takovém případě vrátí chybu.
    - Nemůžete číst ani aktualizovat propustnost na kontejneru bez serveru a v takovém případě vrátí chybu.
    - Nelze vytvořit sdílenou databázi propustnosti v účtu bez serveru a v takovém případě vrátí chybu.
- Kontejnery bez serveru můžou poskytovat maximální prostupnost propustnosti 5 000 jednotek žádostí za sekundu.
- Kontejnery bez serveru můžou ukládat maximálně 50 GB dat a indexů.

## <a name="monitoring-your-consumption"></a>Monitorování vaší spotřeby

Pokud jste v režimu zajištěné propustnosti používali Azure Cosmos DB dříve, zjistíte, že bez serveru nebudete mít k dispozici cenově výhodnou kapacitu, když váš provoz neodůvodňuje zřízenou kapacitu. Je možné, že vaše náklady budou méně předvídatelné, protože se vám bude účtovat na základě počtu požadavků, které vaše databáze zpracovala. Z tohoto důvodu je důležité, abyste měli přehled o své aktuální spotřebě.

Při procházení podokna **metrik** vašeho účtu najdete graf s názvem **jednotky žádosti spotřebované** na kartě **Přehled** . Tento graf znázorňuje počet jednotek požadavků, které váš účet spotřeboval:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Graf znázorňující spotřebované jednotky žádostí" border="false":::

Stejný graf můžete najít při použití Azure Monitor, jak je popsáno [zde](monitor-request-unit-usage.md). Všimněte si, že Azure Monitor vám umožní nastavit [Upozornění](../azure-monitor/platform/alerts-metric-overview.md), která se můžou použít k upozornění, když spotřeba jednotky žádosti předala určitou prahovou hodnotu.

## <a name="performance"></a><a id="performance"></a>Výkon

Prostředky bez serveru poskytují specifické charakteristiky výkonu, které se liší od poskytovaných prostředků propustnosti:

- **Dostupnost** : až bude nabídka bez serveru všeobecně dostupná, dostupnost kontejnerů bez serveru se pokryje s smlouva SLA (SLA) 99,9%, pokud se nepoužijí zóny dostupnosti (redundance zóny). Smlouva SLA je 99,99% při použití Zóny dostupnosti.
- **Latence** : po zpřístupnění nabídky bez serveru bude obecně dostupná, latence kontejnerů bez serveru bude pokrytá cílovou úrovní služeb (SLO) a 10 milisekundami pro čtení bodů a 30 milisekundami a méně pro zápisy. Operace čtení z bodu se skládá z načtení jedné položky podle jejího ID a hodnoty klíče oddílu.
- Prostupnost **: po** zpřístupnění nabídky bez serveru bude dostupná i v případě, že dojde k obecné dostupnosti nabídek bez serveru, bude se vztahovat na úroveň služeb (SLO) 95%. To znamená, že maximální navýšení zatížení je možné dosáhnout minimálně 95% času.

> [!NOTE]
> Jako jakákoli verze Azure Preview se Azure Cosmos DB bez serveru vylučuje ze smluv o úrovni služeb (SLA). Výše uvedené charakteristiky výkonu se poskytují jako náhled toho, co tato nabídka dodá, když je všeobecně dostupná.

## <a name="next-steps"></a>Další kroky

Začněte používat bez serveru s následujícími články:

- [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)
- [Volba mezi zřízenou propustností a bezserverovým využitím](throughput-serverless.md)
- [Cenový model ve službě Azure Cosmos DB](how-pricing-works.md)
