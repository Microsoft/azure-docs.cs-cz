---
title: Vytváření kontejnerů a databází Azure Cosmos v propustnosti zřízené při automatickém škálování
description: Přečtěte si o výhodách, případech použití a způsobu zřízení databází a kontejnerů Azure Cosmos v rámci zajištění propustnosti pomocí automatického škálování.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791710"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Vytvoření kontejnerů a databází Azure Cosmos pomocí zřízené propustnosti pomocí automatického škálování

Azure Cosmos DB vám umožní nakonfigurovat kontejnery pomocí standardní (ruční) zřízené propustnosti nebo zajištěné propustnosti automatického škálování. Tento článek popisuje výhody a případy použití automatického škálování.

> [!NOTE]
> [Automatické škálování můžete povolit jenom pro nové databáze a kontejnery](#create-db-container-autoscale) . Pro existující kontejnery a databáze není k dispozici.

Kromě standardního zřizování propustnosti teď můžete nakonfigurovat Azure Cosmos Containers pomocí zřízené propustnosti pomocí automatického škálování. Kontejnery a databáze nakonfigurované v rámci automatického škálování zřízené propustnost budou **automaticky a okamžitě škálovat zajištěné propustnosti podle potřeb vaší aplikace, aniž by to mělo vliv na dostupnost, latenci, propustnost nebo výkon pro globální úlohy.**

Při konfiguraci kontejnerů a databází v automatickém škálování je potřeba zadat maximální propustnost `Tmax` , která se nepřekračuje. Kontejnery pak můžou škálovat svou propustnost, `0.1*Tmax < T < Tmax`takže. Jinými slovy, kontejnery a databáze se okamžitě škálují podle potřeb úloh, od až do 10% maximální hodnoty propustnosti, kterou jste nakonfigurovali na konfigurovanou maximální hodnotu propustnosti. Po nakonfigurování automatického škálování můžete změnit nastavení maximální propustnosti (`Tmax`) u databáze nebo kontejneru v jakémkoli okamžiku v čase. V případě možnosti automatického škálování již není minimální propustnost 400 RU/s na jeden kontejner nebo databázi platná.

V případě zadané maximální propustnosti kontejneru nebo databáze systém umožňuje fungování v rámci počítaného limitu úložiště. Pokud dojde k překročení limitu úložiště, pak se maximální propustnost automaticky upraví na vyšší hodnotu. Při použití propustnosti na úrovni databáze s AutoScale se počet kontejnerů povolených v rámci databáze vypočte takto `0.001*TMax`:. Pokud například zřídíte 20 000 automatického škálování s, může databáze mít 20 kontejnerů.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Výhody zřízené propustnosti automatického škálování

Kontejnery Azure Cosmos, které jsou nakonfigurované pomocí automatického škálování, mají následující výhody:

* **Jednoduché:** Kontejnery s možností automatického škálování odstraňují složitost pro správu zřízené propustnosti (ru) a kapacitu ručně pro různé kontejnery.

* **Škálovatelné:** Kontejnery s autoškálou bez problémů škálují kapacitu zřízené propustnosti podle potřeby. Neexistují žádné přerušení připojení klientů, aplikace a neovlivňují žádné existující SLA.

* **Nákladově efektivní:** Pokud používáte kontejnery nakonfigurované s funkcí automatického škálování, platíte jenom za prostředky, které vaše úlohy potřebují po hodinách.

* **Vysoce dostupné:** Kontejnery s AutoScale používají stejný globálně distribuovaný back-end odolný a vysoce dostupný back-end, který zajišťuje odolnost dat a vysokou dostupnost.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Případy použití zřízené propustnosti pomocí automatického škálování

Případy použití pro kontejnery Azure Cosmos nakonfigurované pomocí automatického škálování zahrnují:

* **Proměnlivé úlohy:** Pokud používáte lehce používanou aplikaci s špičkou využití 1 hodiny do několika hodin několikrát denně nebo několikrát za rok. Mezi příklady patří aplikace pro lidské zdroje, rozpočtování a generování sestav provozu. V takových scénářích je možné použít kontejnery nakonfigurované pomocí automatického škálování a už nemusíte ručně zřizovat kapacitu pro nejvyšší nebo střední kapacitu.

* **Nepředvídatelné úlohy:** Když spouštíte úlohy, u kterých je využití databáze v průběhu dne, ale také špička aktivity, která je obtížné odhadnout. Příklad obsahuje lokalitu provozu, která se dohlíží na nárůst aktivity při změně předpovědi počasí. Kontejnery nakonfigurované pomocí automatického škálování upraví kapacitu tak, aby splňovala požadavky na zatížení ve špičce aplikace, a pokud dojde k překročení nárůstu aktivity, můžete se škálovat zpátky.

* **Nové aplikace:** Pokud nasazujete novou aplikaci a nejste si jistí, jak velká část zřízené propustnosti (tj. kolik ru) potřebujete. Díky kontejnerům nakonfigurovaným pomocí automatického škálování se můžete automaticky škálovat na požadavky na kapacitu a požadavky vaší aplikace.

* **Zřídka používané aplikace:** Máte-li aplikaci, která se používá pouze několik hodin několikrát za den nebo týden nebo měsíc, jako je například aplikace s nízkým objemem nebo web nebo blogový Web.

* **Databáze pro vývoj a testování:** Pokud máte vývojáři, kteří používají kontejnery během pracovní doby, ale nepotřebují je po nocích nebo víkendech. S kontejnery nakonfigurovanými pomocí automatického škálování se škálují na minimum, pokud se nepoužívají.

* **Plánované provozní úlohy a dotazy:** Pokud máte sérii plánovaných požadavků/operací/dotazů na jednom kontejneru a v případě nečinných dob, kdy chcete spustit při absolutní nízké propustnosti, můžete to provést snadno. Po odeslání plánovaného dotazu nebo žádosti do kontejneru nakonfigurovaného s automatickým škálováním se automaticky navýší velikost podle potřeby a spustí se operace.

Řešení pro předchozí problémy vyžadují nejen obrovský čas implementace, ale také přináší složitost konfigurace nebo kódu a často vyžadují ruční zásah, aby je bylo možné řešit. Automatické škálování umožňuje vydávat výše uvedené scénáře předem, takže už se o těchto problémech nemusíte starat.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Porovnání – standardní (ruční) oproti zřízené propustnosti automatického škálování

|  | Kontejnery s nakonfigurovanou standardní zřízenou propustností  | Kontejnery nakonfigurované s zajištěnou propustností automatického škálování |
|---------|---------|---------|
| **Zřízená propustnost** | Ručně zřízené. | Automatické a okamžité škálování na základě vzorců využití úloh. |
| **Frekvence – omezení požadavků/operací (429)**  | Může nastat, pokud spotřeba překročí zřízenou kapacitu. | Nedojde k tomu, pokud je využitá propustnost v rámci maximální propustnosti, kterou zvolíte pomocí automatického škálování.   |
| **Plánování kapacity** |  Musíte provést počáteční plánování kapacity a zřídit propustnost, kterou potřebujete. |    Nemusíte si dělat starosti s plánováním kapacity. Systém se automaticky postará o plánování kapacity a správu kapacity. |
| **Ceny** | Ručně zřízené RU/s za hodinu | U účtů oblastí s jedním zápisem platíte za propustnost využité každou hodinu, a to pomocí automatického škálování RU/s za hodinovou sazbu. <br/><br/>U účtů s více oblastmi zápisu se neúčtují žádné další poplatky za automatické škálování. Za tuto propustnost platíte za každou hodinu, a to pomocí stejné sady multi-Master RU/s za hodinovou sazbu. |
| **Nejvhodnější pro typy úloh** |  Předvídatelné a stabilní úlohy|   Nepředvídatelné a proměnlivé úlohy  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Vytvoření databáze nebo kontejneru pomocí automatického škálování

Automatické škálování pro nové databáze nebo kontejnery můžete nakonfigurovat při jejich vytváření prostřednictvím Azure Portal. Pomocí následujících kroků můžete vytvořit novou databázi nebo kontejner, povolit automatické škálování a zadat maximální propustnost (RU/s).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [Azure Cosmos DB Exploreru.](https://cosmos.azure.com/)

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner.** Zadejte název databáze, kontejneru a klíč oddílu. V části **propustnost**vyberte možnost **automatického škálování** a zvolte maximální propustnost (ru/s), kterou databáze nebo kontejner při použití možnosti automatického škálování nepřekračuje.

   ![Vytvoření kontejneru a konfigurace propustnosti automatického škálování](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Vyberte **OK**.

Sdílenou databázi propustnosti pomocí automatického škálování můžete vytvořit tak, že vyberete možnost **zřídit propustnost databáze** .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Omezení propustnosti a úložiště pro automatické škálování

V následující tabulce jsou uvedeny maximální limity v celém rozsahu a úložiště pro různé možnosti automatického škálování:

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
