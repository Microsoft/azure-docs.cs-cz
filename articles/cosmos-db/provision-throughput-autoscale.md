---
title: Vytvoření kontejnerů a databází Azure Cosmos v režimu automatického škálování.
description: Přečtěte si o výhodách, případech použití a o tom, jak zřídit databáze a kontejnery Azure Cosmos v režimu automatického škálování.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 49fea2cc9a48d5afc794d6932208b61252bea424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196488"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autoscale-mode"></a>Vytvoření kontejnerů a databází Azure Cosmos v režimu automatického škálování

Azure Cosmos DB umožňuje zřídit propustnost kontejnerů v režimu ručního nebo automatického škálování. Tento článek popisuje výhody a případy použití režimu automatického škálování.

> [!NOTE]
> [Automatické škálování můžete povolit jenom pro nové databáze a kontejnery](#create-db-container-autoscale) . Pro existující kontejnery a databáze není k dispozici.

Kromě ručního zřizování propustnosti teď můžete nakonfigurovat kontejnery Azure Cosmos v režimu automatického škálování. Kontejnery a databáze nakonfigurované v režimu automatického škálování budou **automaticky a okamžitě škálovat zajištěné propustnosti podle potřeb vaší aplikace, aniž by to mělo vliv na dostupnost, latenci, propustnost nebo výkon pro globální úlohy.**

Při konfiguraci kontejnerů a databází v režimu automatického škálování je třeba zadat maximální propustnost `Tmax` , která se nepřekračuje. Kontejnery pak můžou škálovat svou propustnost, `0.1*Tmax < T < Tmax`takže. Jinými slovy, kontejnery a databáze se okamžitě škálují podle potřeb úloh, od až do 10% maximální hodnoty propustnosti, kterou jste nakonfigurovali na konfigurovanou maximální hodnotu propustnosti. Po nakonfigurování automatického škálování můžete změnit nastavení maximální propustnosti (`Tmax`) u databáze nebo kontejneru v jakémkoli okamžiku v čase. V případě možnosti automatického škálování již není minimální propustnost 400 RU/s na jeden kontejner nebo databázi platná.

V případě zadané maximální propustnosti kontejneru nebo databáze systém umožňuje fungování v rámci počítaného limitu úložiště. Pokud dojde k překročení limitu úložiště, pak se maximální propustnost automaticky upraví na vyšší hodnotu. Při použití propustnosti na úrovni databáze s režimem automatického škálování se počet kontejnerů povolených v rámci databáze vypočte `0.001*TMax`takto:. Pokud například zřídíte 20 000 automatického škálování s, může databáze mít 20 kontejnerů.

## <a name="benefits-of-autoscale-mode"></a><a id="autoscale-benefits"></a>Výhody režimu automatického škálování

Kontejnery Azure Cosmos, které jsou nakonfigurované v režimu automatického škálování, mají následující výhody:

* **Jednoduché:** Kontejnery v režimu automatického škálování odstraňují složitost pro správu zřízené propustnosti (ru) a kapacitu ručně pro různé kontejnery.

* **Škálovatelné:** Kontejnery v režimu automatického škálování plynule škálují kapacitu zřízené propustnosti podle potřeby. Neexistují žádné přerušení připojení klientů, aplikace a neovlivňují žádné existující SLA.

* **Nákladově efektivní:** Pokud používáte kontejnery nakonfigurované v režimu automatického škálování, platíte jenom za prostředky, které vaše úlohy potřebují po hodinách.

* **Vysoce dostupné:** Kontejnery v režimu automatického škálování používají stejný globálně distribuovaný back-end odolný a vysoce dostupný back-end, který zajišťuje odolnost dat a vysokou dostupnost.

## <a name="use-cases-of-autoscale-mode"></a><a id="autoscale-usecases"></a>Případy použití režimu automatického škálování

Případy použití pro kontejnery Azure Cosmos, které jsou nakonfigurované v režimu automatického škálování, zahrnují:

* **Proměnlivé úlohy:** Pokud používáte lehce používanou aplikaci s špičkou využití 1 hodiny do několika hodin několikrát denně nebo několikrát za rok. Mezi příklady patří aplikace pro lidské zdroje, rozpočtování a generování sestav provozu. V takových scénářích je možné použít kontejnery nakonfigurované v režimu automatického škálování a už nebudete muset ručně zřizovat buď pro nejvyšší nebo průměrné kapacity.

* **Nepředvídatelné úlohy:** Když spouštíte úlohy, u kterých je využití databáze v průběhu dne, ale také špička aktivity, která je obtížné odhadnout. Příklad obsahuje lokalitu provozu, která se dohlíží na nárůst aktivity při změně předpovědi počasí. Kontejnery nakonfigurované v režimu automatického škálování upravují kapacitu tak, aby splňovala požadavky na zatížení ve špičce aplikace, a pokud dojde k překročení nárůstu aktivity, můžete se škálovat zpátky.

* **Nové aplikace:** Pokud nasazujete novou aplikaci a nejste si jistí, jak velká část zřízené propustnosti (tj. kolik ru) potřebujete. Pomocí kontejnerů nakonfigurovaných v režimu automatického škálování se můžete automaticky škálovat na požadavky na kapacitu a požadavky vaší aplikace.

* **Zřídka používané aplikace:** Máte-li aplikaci, která se používá pouze několik hodin několikrát za den nebo týden nebo měsíc, jako je například aplikace s nízkým objemem nebo web nebo blogový Web.

* **Databáze pro vývoj a testování:** Pokud máte vývojáři, kteří používají kontejnery během pracovní doby, ale nepotřebují je po nocích nebo víkendech. U kontejnerů nakonfigurovaných v režimu automatického škálování se škálují na minimum, pokud se nepoužívají.

* **Plánované provozní úlohy a dotazy:** Pokud máte sérii plánovaných požadavků/operací/dotazů na jednom kontejneru a v případě nečinných dob, kdy chcete spustit při absolutní nízké propustnosti, můžete to provést snadno. Při odeslání plánovaného dotazu nebo žádosti do kontejneru nakonfigurovaného v režimu automatického škálování se automaticky navýší velikost podle potřeby a spustí se operace.

Řešení pro předchozí problémy vyžadují nejen obrovský čas implementace, ale také přináší složitost konfigurace nebo kódu a často vyžadují ruční zásah, aby je bylo možné řešit. Režim automatického škálování umožňuje, aby výše uvedené scénáře byly vycházet z tohoto pole, takže už se o těchto problémech nemusíte starat.

## <a name="comparison--containers-configured-in-manual-mode-vs-autoscale-mode"></a>Porovnání – kontejnery nakonfigurované v ručním režimu vs. režim automatického škálování

|  | Kontejnery nakonfigurované v ručním režimu  | Kontejnery nakonfigurované v režimu automatického škálování |
|---------|---------|---------|
| **Zřízená propustnost** | Ručně zřízené. | Automatické a okamžité škálování na základě vzorců využití úloh. |
| **Frekvence – omezení požadavků/operací (429)**  | Může nastat, pokud spotřeba překročí zřízenou kapacitu. | Nedojde k tomu, pokud je využitá propustnost v rámci maximální propustnosti, kterou zvolíte v režimu automatického škálování.   |
| **Plánování kapacity** |  Musíte provést počáteční plánování kapacity a zřídit propustnost, kterou potřebujete. |    Nemusíte si dělat starosti s plánováním kapacity. Systém se automaticky postará o plánování kapacity a správu kapacity. |
| **Ceny** | Ručně zřízené RU/s za hodinu | U účtů oblastí s jedním zápisem platíte za propustnost využité každou hodinu, a to pomocí automatického škálování RU/s za hodinovou sazbu. <br/><br/>U účtů s více oblastmi zápisu se neúčtují žádné další poplatky za automatické škálování. Za tuto propustnost platíte za každou hodinu, a to pomocí stejné sady multi-Master RU/s za hodinovou sazbu. |
| **Nejvhodnější pro typy úloh** |  Předvídatelné a stabilní úlohy|   Nepředvídatelné a proměnlivé úlohy  |

## <a name="create-a-database-or-a-container-with-autoscale-mode"></a><a id="create-db-container-autoscale"></a>Vytvoření databáze nebo kontejneru pomocí režimu automatického škálování

Automatické škálování pro nové databáze nebo kontejnery můžete nakonfigurovat při jejich vytváření prostřednictvím Azure Portal. Pomocí následujících kroků můžete vytvořit novou databázi nebo kontejner, povolit automatické škálování a zadat maximální propustnost (RU/s).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [Azure Cosmos DB Exploreru.](https://cosmos.azure.com/)

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner.** Zadejte název databáze, kontejneru a klíč oddílu. V části **propustnost**vyberte možnost **automatického škálování** a zvolte maximální propustnost (ru/s), kterou databáze nebo kontejner při použití možnosti automatického škálování nepřekračuje.

   ![Vytvoření kontejneru a konfigurace propustnosti automatického škálování](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Vyberte **OK**.

Sdílenou databázi propustnosti s režimem automatického škálování můžete vytvořit tak, že vyberete možnost **zřídit propustnost databáze** .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Omezení propustnosti a úložiště pro automatické škálování

V následující tabulce je uvedeno maximum v celém rozsahu a omezení úložiště pro různé možnosti v režimu automatického škálování:

|Maximální počet propustností  |Maximální limit úložiště  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Další kroky

* Přečtěte si [Nejčastější dotazy k automatickému škálování](autoscale-faq.md).
* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
