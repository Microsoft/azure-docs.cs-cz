---
title: Vytvořte kontejnery a databáze Azure Cosmos v režimu autopilota.
description: Seznamte se s výhodami, případy použití a jak zřídit databáze a kontejnery Azure Cosmos v režimu autopilota.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246653"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Vytváření kontejnerů a databází Azure Cosmos DB v režimu Autopilot (Preview)

Azure Cosmos DB umožňuje zřizovat propustnost pro kontejnery v ručním režimu nebo v režimu Autopilot. Tento článek popisuje výhody a případy použití režimu Autopilot.

> [!NOTE]
> Režim autopilota je v současné době k dispozici ve verzi Public Preview. Autopilota můžete povolit pouze [pro nové databáze a kontejnery.](#create-a-database-or-a-container-with-autopilot-mode) Není k dispozici pro existující kontejnery a databáze.

Kromě ručního zřizování propustnosti teď můžete nakonfigurovat kontejnery Azure Cosmos v režimu autopilota. Kontejnery a databáze nakonfigurované v režimu autopilota **automaticky a okamžitě škálují zřízenou propustnost na základě potřeb vaší aplikace, aniž by to mělo vliv na dostupnost, latenci, propustnost nebo výkon úlohy globálně.**

Při konfiguraci kontejnerů a databází v režimu autopilota `Tmax` je třeba zadat maximální propustnost, která nemá být překročena. Kontejnery pak můžete škálovat `0.1*Tmax < T < Tmax`jejich propustnost tak, aby . Jinými slovy kontejnery a databáze škálovat okamžitě na základě potřeb pracovního vytížení, od tak nízké, jak 10 % maximální propustnost hodnotu, kterou jste nakonfigurovali až na nakonfigurovanou hodnotu maximální propustnosti. Nastavení maximální propustnost`Tmax`( ) můžete kdykoli změnit v databázi nebo kontejneru autopilota. S možností autopilota již není použitelná minimální propustnost 400 RU/s na kontejner nebo databázi.

Během náhledu autopilota, pro zadanou maximální propustnost kontejneru nebo databáze, systém umožňuje ovládání v rámci vypočteného limitu úložiště. Pokud je překročen limit úložiště, maximální propustnost se automaticky upraví na vyšší hodnotu. Při použití propustnost na úrovni databáze s režimem autopilota `0.001*TMax`se počet kontejnerů povolených v databázi vypočítá takto: . Například pokud zřídíte 20 000 ru/s autopilota, pak databáze může mít 20 kontejnerů.

## <a name="benefits-of-autopilot-mode"></a>Výhody režimu autopilota

Kontejnery Azure Cosmos, které jsou nakonfigurované v režimu autopilota, mají následující výhody:

* **Jednoduché:** Kontejnery v režimu autopilota odeberou složitost správy zřízené propustnosti (RU) a kapacitu ručně pro různé kontejnery.

* **Škálovatelné:** Kontejnery v režimu autopilota bez problémů škálují kapacitu zřízené propustnosti podle potřeby. Připojení klientů, aplikace nejsou nijak vyrušována a nemají vliv na žádné existující služby SLA.

* **Nákladově efektivní:** Při použití kontejnerů nakonfigurovaných v režimu autopilota platíte jenom za prostředky, které vaše úlohy potřebují za hodinu.

* **Vysoce dostupné:** Kontejnery v režimu autopilota používají stejný globálně distribuovaný, odolný proti chybám a vysoce dostupný back-end, který zajišťuje odolnost dat a vysokou dostupnost.

## <a name="use-cases-of-autopilot-mode"></a>Případy použití režimu autopilota

Případy použití kontejnerů Azure Cosmos nakonfigurovaných v režimu autopilota zahrnují:

* **Proměnné úlohy:** Pokud používáte lehce používanou aplikaci s maximálním využitím 1 hodinu až několik hodin několikrát denně nebo několikrát za rok. Mezi příklady patří aplikace pro lidské zdroje, rozpočtování a provozní vykazování. Pro takové scénáře lze použít kontejnery nakonfigurované v režimu autopilota a již není nutné ručně zřídit maximální nebo průměrnou kapacitu.

* **Nepředvídatelné úlohy:** Pokud spouštěte úlohy, kde je využití databáze po celý den, ale také špičky aktivity, které je obtížné předvídat. Příklad zahrnuje dopravní web, který vidí nárůst aktivity při změně předpovědi počasí. Kontejnery nakonfigurované v režimu autopilota upravují kapacitu tak, aby vyhovovala potřebám špičkového zatížení aplikace, a po dokončení nárůstu aktivity se zmenšit.

* **Nové aplikace:** Pokud nasazujete novou aplikaci a nejste si jisti, kolik zřízená propustnost (tj. kolik ru) potřebujete. S kontejnery nakonfigurovanými v režimu autopilota můžete automaticky škálovat podle požadavků kapacity a požadavků vaší aplikace.

* **Zřídka používané aplikace:** Pokud máte aplikaci, která se používá pouze několik hodin několikrát za den nebo týden nebo měsíc, například nízkoobjemovou aplikaci/ web/blog.

* **Vývojové a testovací databáze:** Pokud máte vývojáře, kteří používají kontejnery během pracovní doby, ale nepotřebují je v noci nebo o víkendech. S kontejnery nakonfigurovanými v režimu autopilota se zmenšují na minimum, když se nepoužívají.

* **Plánované produkční úlohy/dotazy:** Pokud máte řadu naplánovaných požadavků/operací/dotazů na jeden kontejner a pokud existují nečinné období, kde chcete spustit s absolutní nízkou propustností, můžete to nyní provést snadno. Když je naplánovaný dotaz/požadavek odeslán do kontejneru nakonfigurovaného v režimu autopilota, automaticky navádí kapacitu podle potřeby a spustí operaci.

Řešení předchozích problémů vyžadují nejen obrovské množství času při implementaci, ale také zavádějí složitost konfigurace nebo vašeho kódu a často vyžadují ruční zásah k jejich řešení. Režim autopilota umožňuje výše uvedené scénáře po vybalení z krabice, takže se již nemusíte obávat těchto problémů.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Porovnání – kontejnery nakonfigurované v manuálním režimu vrežimu autopilota

|  | Kontejnery nakonfigurované v ručním režimu  | Kontejnery nakonfigurované v režimu autopilota |
|---------|---------|---------|
| **Zřízená propustnost** | Ručně zřízené. | Automaticky a okamžitě škálovat na základě vzorců využití pracovního vytížení. |
| **Omezení rychlosti požadavků/operací (429)**  | Může dojít, pokud spotřeba překročí zřízenou kapacitu. | Nestane, pokud je spotřebovaná propustnost v rámci maximální propustnost, kterou zvolíte v režimu autopilota.   |
| **Plánování kapacity** |  Musíte provést počáteční plánování kapacity a poskytování propustnosti, kterou potřebujete. |    Nemusíte se starat o plánování kapacity. Systém se automaticky stará o plánování kapacity a řízení kapacity. |
| **Ceny** | Ručně zřízené RU/s za hodinu. | U účtů oblasti jednoho zápisu platíte za propustnost používanou každou hodinu pomocí sazby RU/s autopilota za hodinu. <br/><br/>U účtů s více oblastmi zápisu se za autopilota neúčtuje žádný příplatek. Platíte za propustnost používanou každou hodinu pomocí stejné vícehlavní ru/s za hodinovou sazbu. |
| **Nejvhodnější pro typy pracovních vytížení** |  Předvídatelná a stabilní pracovní zátěž|   Nepředvídatelné a variabilní úlohy  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Vytvoření databáze nebo kontejneru v režimu autopilota

Autopilota můžete nakonfigurovat pro nové databáze nebo kontejnery při jejich vytváření prostřednictvím portálu Azure. Pomocí následujících kroků vytvořte novou databázi nebo kontejner, povolte autopilota a určete maximální propustnost (RU/s).

1. Přihlaste se k [portálu Azure nebo](https://portal.azure.com) [průzkumníku Db Azure Cosmos.](https://cosmos.azure.com/)

1. Přejděte na svůj účet Azure Cosmos DB a otevřete kartu **Průzkumník dat.**

1. Vyberte **Nový kontejner.** Zadejte název databáze, kontejneru a klíče oddílu. V části **Propustnost**vyberte možnost **Autopilot** a zvolte maximální propustnost (RU/s), kterou databáze nebo kontejner nesmí překročit při použití možnosti autopilota.

   ![Vytvoření kontejneru a konfigurace propustnost autopilota](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Vyberte **OK**.

Sdílenou databázi propustností v režimu autopilota můžete vytvořit výběrem možnosti **Provision database propustnost.**

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Limity propustnost a úložiště pro autopilota

V následující tabulce jsou uvedeny maximální limity v celém textu a omezení úložiště pro různé možnosti v režimu autopilota:

|Maximální limit propustnosti  |Maximální limit úložiště  |
|---------|---------|
|4000 Ru/s  |   50 GB    |
|20 000 Ru/s  |  200 GB  |
|100 000 Ru/s    |  1 TB   |
|500 000 Ru/s    |  5 TB  |

## <a name="next-steps"></a>Další kroky

* Přečtěte si [nejčastější dotazy k autopilotovi](autopilot-faq.md).
* Další informace o [logických oddílech](partition-data.md).
* Zjistěte, jak [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
