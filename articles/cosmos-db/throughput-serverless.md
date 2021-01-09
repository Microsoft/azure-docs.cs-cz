---
title: Jak vybrat mezi zřízenou propustností a bez serveru v Azure Cosmos DB
description: Přečtěte si, jak zvolit mezi zřízenou propustností a bez serveru pro vaše úlohy.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3f5c3400f319a3f9d5f1544457b009f90d479634
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049826"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Jak vybrat mezi zřízenou propustností a bez serveru
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB je k dispozici ve dvou různých režimech kapacity: [zřízená propustnost](set-throughput.md) a bez [serveru](serverless.md). V obou režimech můžete provádět přesně stejné databázové operace, ale způsob, jakým se vám tyto operace účtuje, se odliší od sebe. Následující video vysvětluje základní rozdíly mezi těmito režimy a způsob, jakým odpovídají různým typům úloh:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>Podrobné porovnání

| Kritéria | Zřízená propustnost | Bez serveru |
| --- | --- | --- |
| Status | Obecná dostupnost | Ve verzi Preview |
| Nejlépe vhodné pro | Úlohy s trvalým provozem vyžadující předvídatelný výkon | Úlohy s přerušovaným nebo nepředvídatelným provozem a nízkým poměrem provozu na nejvyšší úrovni |
| Jak to funguje | Pro každý z vašich kontejnerů zřizujete určitou míru propustnosti vyjádřenou v [jednotkách žádostí](request-units.md) za sekundu. V každé druhé je tato hodnota jednotek žádostí k dispozici pro vaše databázové operace. Zřízenou propustnost lze aktualizovat ručně nebo automaticky upravit pomocí automatického [škálování](provision-throughput-autoscale.md). | Vaše databázové operace se spouští na vašich kontejnerech bez nutnosti zřídit jakoukoli kapacitu. |
| Geografická distribuce | K dispozici (neomezený počet oblastí Azure) | Nedostupné (účty bez serveru se dají spouštět jenom v 1 oblasti Azure) |
| Maximální velikost úložiště na kontejner | Unlimited | 50 GB |
| Výkon | < 10 MS latence pro čtení a zápisy bodů, na které se vztahuje smlouva SLA | Latence pro čtení z < 10 MS pro body čtení a < 30 ms pro zápisy pokryté objektem SLO |
| Model fakturace | Fakturace se provádí po hodinách pro zřízené RU/s, bez ohledu na to, kolik ru bylo spotřebováno. | Fakturace se provádí po hodinách na základě množství ru spotřebovaného vašimi databázovou operací. |

> [!IMPORTANT]
> Některá omezení bez serveru můžou být nesníží nebo odebraná, když je server bez serveru všeobecně dostupný a **vaše názory** nám pomohou se rozhodnout! Kontaktujte a řekněte nám víc o prostředí bez serveru: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>Odhad očekávané spotřeby

V některých situacích může být nejasné, jestli se pro danou úlohu má vybrat zajištěná propustnost nebo bez serveru. K tomu, abyste mohli s tímto rozhodnutím pomáhat, můžete odhadnout celkovou **očekávanou spotřebu**, což je celkový počet ru, které můžete využívat za měsíc (můžete to odhadnout pomocí [níže](plan-manage-costs.md#estimating-serverless-costs)uvedené tabulky).

**Příklad 1**: u úlohy se očekává nárůst počtu na maximálně 500 ru/s a za měsíc se spotřebuje celkem 20 000 000 ru.

- V režimu zřízené propustnosti byste zřídili kontejner s 500 RU/s za měsíční cenu: $0,008 * 5 * 730 = **$29,20**
- V režimu bez serveru platíte za spotřebované ru: $0,25 * 20 = **$5,00**

**Příklad 2**: u úlohy se očekává nárůst zatížení na maximálně 500 ru/s a za měsíc se spotřebuje celkem 250 000 000 ru.

- V režimu zřízené propustnosti byste zřídili kontejner s 500 RU/s za měsíční cenu: $0,008 * 5 * 730 = **$29,20**
- V režimu bez serveru platíte za spotřebované ru: $0,25 * 250 = **$62,50**

(tyto příklady nejsou účetními náklady na úložiště, které jsou stejné mezi dvěma režimy)

> [!NOTE]
> Náklady zobrazené v předchozím příkladu jsou pouze pro demonstrační účely. Nejnovější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [propustnosti zřizování v Azure Cosmos DB](set-throughput.md) .
- Přečtěte si další informace o [Azure Cosmos DB bez serveru](serverless.md)
- Seznámení s konceptem [jednotek žádostí](request-units.md)
