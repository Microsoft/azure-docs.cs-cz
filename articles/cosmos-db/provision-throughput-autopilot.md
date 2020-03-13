---
title: V režimu autopilotu Vytvářejte kontejnery a databáze Azure Cosmos.
description: Přečtěte si o výhodách, případech použití a o tom, jak zřídit databáze a kontejnery Azure Cosmos v režimu autopilotu.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246653"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Vytvoření kontejnerů a databází Azure Cosmos v režimu autopilotu (Preview)

Azure Cosmos DB umožňuje zřizovat propustnost pro kontejnery v ručním režimu nebo v režimu Autopilot. Tento článek popisuje výhody a případy použití režimu Autopilot.

> [!NOTE]
> Režim autopilotu je aktuálně k dispozici ve verzi Public Preview. Můžete [Povolit autopilotování jenom pro nové databáze a kontejnery](#create-a-database-or-a-container-with-autopilot-mode) . Pro existující kontejnery a databáze není k dispozici.

Kromě ručního zřizování propustnosti teď můžete nakonfigurovat kontejnery Azure Cosmos v režimu autopilotu. Kontejnery a databáze nakonfigurované v režimu autopilotu budou **automaticky a okamžitě škálovat zajištěné propustnosti podle potřeb vašich aplikací, aniž by to mělo vliv na dostupnost, latenci, propustnost nebo výkon pro globální úlohy.**

Při konfiguraci kontejnerů a databází v režimu autopilotu je třeba zadat maximální propustnost `Tmax` nemusíte překročit. Kontejnery pak můžou škálovat svou propustnost, takže `0.1*Tmax < T < Tmax`. Jinými slovy, kontejnery a databáze se okamžitě škálují podle potřeb úloh, od až do 10% maximální hodnoty propustnosti, kterou jste nakonfigurovali na konfigurovanou maximální hodnotu propustnosti. Můžete změnit nastavení maximální propustnosti (`Tmax`) v rámci databáze nebo kontejneru autopilotu v jakémkoli okamžiku. Při volbě autopilotu se už neplatí minimální propustnost 400 RU/s na jeden kontejner nebo databáze.

V rámci verze Preview nástroje autopilot pro zadanou maximální propustnost na kontejneru nebo databázi umožňuje systém pracovat v rámci počítaného limitu úložiště. Pokud dojde k překročení limitu úložiště, pak se maximální propustnost automaticky upraví na vyšší hodnotu. Při použití propustnosti na úrovni databáze v režimu autopilotu se počet kontejnerů povolených v rámci databáze vypočte takto: `0.001*TMax`. Pokud například zřídíte 20 000 autopilot RU/s, databáze může mít 20 kontejnerů.

## <a name="benefits-of-autopilot-mode"></a>Výhody režimu autopilotu

Kontejnery Azure Cosmos, které jsou nakonfigurované v režimu autopilotu, mají následující výhody:

* **Jednoduché:** Kontejnery v režimu autopilotu odstraňují složitost pro správu zřízené propustnosti (ru) a kapacitu ručně pro různé kontejnery.

* **Škálovatelné:** Kontejnery v režimu autopilotu bez problémů škálují kapacitu zřízené propustnosti podle potřeby. Neexistují žádné přerušení připojení klientů, aplikace a neovlivňují žádné existující SLA.

* **Nákladově efektivní:** Pokud používáte kontejnery nakonfigurované v režimu autopilot, platíte jenom za prostředky, které vaše úlohy potřebují po hodinách.

* **Vysoce dostupné:** Kontejnery v režimu autopilotu používají stejný globálně distribuovaný back-end odolný a vysoce dostupný back-end pro zajištění odolnosti dat a vysoké dostupnosti.

## <a name="use-cases-of-autopilot-mode"></a>Případy použití režimu autopilotu

Případy použití pro kontejnery Azure Cosmos nakonfigurované v režimu autopilotu zahrnují:

* **Proměnlivé úlohy:** Pokud používáte lehce používanou aplikaci s špičkou využití 1 hodiny do několika hodin několikrát denně nebo několikrát za rok. Mezi příklady patří aplikace pro lidské zdroje, rozpočtování a generování sestav provozu. V takových scénářích můžete použít kontejnery nakonfigurované v režimu autopilotu a už nebudete muset ručně zřizovat buď pro nejvyšší nebo průměrné kapacity.

* **Nepředvídatelné úlohy:** Když spouštíte úlohy, u kterých je využití databáze v průběhu dne, ale také špička aktivity, která je obtížné odhadnout. Příklad obsahuje lokalitu provozu, která se dohlíží na nárůst aktivity při změně předpovědi počasí. Kontejnery nakonfigurované v režimu autopilotu upravují kapacitu tak, aby splňovala požadavky na zatížení ve špičce aplikace, a při překročení nárůstu aktivity se znovu zvětší.

* **Nové aplikace:** Pokud nasazujete novou aplikaci a nejste si jistí, jak velká část zřízené propustnosti (tj. kolik ru) potřebujete. Pomocí kontejnerů nakonfigurovaných v režimu autopilotu můžete automaticky škálovat na požadavky na kapacitu a požadavky vaší aplikace.

* **Zřídka používané aplikace:** Máte-li aplikaci, která se používá pouze několik hodin několikrát za den nebo týden nebo měsíc, jako je například aplikace s nízkým objemem nebo web nebo blogový Web.

* **Databáze pro vývoj a testování:** Pokud máte vývojáři, kteří používají kontejnery během pracovní doby, ale nepotřebují je po nocích nebo víkendech. U kontejnerů nakonfigurovaných v režimu autopilotu se škálují na minimum, pokud se nepoužívají.

* **Plánované provozní úlohy a dotazy:** Pokud máte sérii plánovaných požadavků/operací/dotazů na jednom kontejneru a v případě nečinných dob, kdy chcete spustit při absolutní nízké propustnosti, můžete to provést snadno. Při odeslání plánovaného dotazu nebo žádosti do kontejneru nakonfigurovaného v režimu autopilotu se automaticky navýší velikost podle potřeby a spustí se operace.

Řešení pro předchozí problémy vyžadují nejen obrovský čas implementace, ale také přináší složitost konfigurace nebo kódu a často vyžadují ruční zásah, aby je bylo možné řešit. Režim autopilotu umožňuje výše uvedeným scénářům docházet z uvedeného seznamu, takže už se o těchto problémech nemusíte starat.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Porovnání – kontejnery nakonfigurované v ručním režimu vs. režim autopilotu

|  | Kontejnery nakonfigurované v ručním režimu  | Kontejnery nakonfigurované v režimu autopilotu |
|---------|---------|---------|
| **Zřízená propustnost** | Ručně zřízené. | Automatické a okamžité škálování na základě vzorců využití úloh. |
| **Frekvence – omezení požadavků/operací (429)**  | Může nastat, pokud spotřeba překročí zřízenou kapacitu. | Nedojde k tomu, pokud je využitá propustnost v rámci maximální propustnosti, kterou zvolíte v režimu autopilotu.   |
| **Plánování kapacity** |  Musíte provést počáteční plánování kapacity a zřídit propustnost, kterou potřebujete. |    Nemusíte si dělat starosti s plánováním kapacity. Systém se automaticky postará o plánování kapacity a správu kapacity. |
| **Ceny** | Ručně zřízené RU/s za hodinu | V případě účtů oblastí s jedním zápisem platíte za propustnost využité každou hodinu, a to pomocí programu autopilot RU/s za hodinovou sazbu. <br/><br/>U účtů s více oblastmi zápisu se neúčtují žádné další poplatky za autopilot. Za tuto propustnost platíte za každou hodinu, a to pomocí stejné sady multi-Master RU/s za hodinovou sazbu. |
| **Nejvhodnější pro typy úloh** |  Předvídatelné a stabilní úlohy|   Nepředvídatelné a proměnlivé úlohy  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Vytvoření databáze nebo kontejneru pomocí režimu autopilotu

Můžete nakonfigurovat autopilot pro nové databáze nebo kontejnery při jejich vytváření prostřednictvím Azure Portal. Pomocí následujících kroků vytvořte novou databázi nebo kontejner, povolte možnost autopilot a zadejte maximální propustnost (RU/s).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo [Azure Cosmos DB Exploreru.](https://cosmos.azure.com/)

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner.** Zadejte název databáze, kontejneru a klíč oddílu. V části **propustnost**vyberte možnost **autopilot** a zvolte maximální propustnost (ru/s), kterou databáze nebo kontejner při použití možnosti autopilotu nepřekračuje.

   ![Vytvoření kontejneru a konfigurace propustnosti autopilotního nasazení](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Vyberte **OK**.

Sdílenou databázi propustnosti můžete vytvořit pomocí režimu autopilotu tak, že vyberete možnost **zřídit propustnost databáze** .

## <a id="autopilot-limits"></a>Omezení propustnosti a úložiště pro autopilot

V následující tabulce je uvedeno maximum v celém rozsahu a omezení úložiště pro různé možnosti v režimu autopilot:

|Maximální počet propustností  |Maximální limit úložiště  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Další kroky

* Přečtěte si [Nejčastější dotazy k autopilotu](autopilot-faq.md).
* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
