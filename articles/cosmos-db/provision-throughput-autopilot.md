---
title: V režimu autopilotu Vytvářejte kontejnery a databáze Azure Cosmos.
description: Přečtěte si o výhodách, případech použití a o tom, jak zřídit databáze a kontejnery Azure Cosmos v režimu autopilotu.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e0c3c88119c3d064326442881854920b411f5ed4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748383"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Vytvoření kontejnerů a databází Azure Cosmos v režimu autopilotu (Preview)

Azure Cosmos DB umožňuje zřídit propustnost pro kontejnery v režimu ručního nebo autopilotního. Tento článek popisuje výhody a případy použití režimu autopilotu.

> [!NOTE]
> Režim autopilotu je aktuálně k dispozici ve verzi Public Preview. Pokud chcete povolit funkci autopilotu pro účet Azure Cosmos, přečtěte si část [Povolení autopilotu](#enable-autopilot) v tomto článku. Můžete povolit autopilotování jenom pro nové databáze a kontejnery, ale pro existující kontejnery a databáze k dispozici.

Kromě ručního zřizování propustnosti teď můžete nakonfigurovat kontejnery Azure Cosmos v režimu autopilotu. Kontejnery a databáze Azure Cosmos nakonfigurované v režimu autopilotu budou **automaticky a okamžitě škálovat zajištěné propustnosti na základě potřeb vaší aplikace, aniž by došlo k narušení SLA.**

Už nebudete muset ručně spravovat zřízenou propustnost nebo zvládnout problémy s omezením četnosti. Kontejnery Azure Cosmos nakonfigurované v režimu autopilotu je možné okamžitě škálovat v reakci na zatížení, aniž by to mělo vliv na dostupnost, latenci, propustnost nebo výkon pro globální úlohy. V případě vysokého využití můžou být kontejnery Azure Cosmos nakonfigurované v režimu autopilotního navýšení nebo snížení kapacity, aniž by to mělo dopad na probíhající operace.

Při konfiguraci kontejnerů a databází v režimu autopilotu je třeba zadat maximální propustnost `Tmax` nemusíte překročit. Kontejnery se pak můžou okamžitě škálovat na základě potřeb úloh v rozsahu `0.1*Tmax < T < Tmax`. Jinými slovy, kontejnery a databáze se okamžitě škálují podle potřeb úloh, od až do 10% maximální hodnoty propustnosti, kterou jste nakonfigurovali, a až po nakonfigurovanou maximální propustnost. V jakémkoli okamžiku v čase můžete změnit nastavení maximální propustnosti (tmax) pro autopilotskou databázi nebo kontejner.

V rámci verze Preview nástroje autopilot pro zadanou maximální propustnost na kontejneru nebo databázi umožňuje systém pracovat v rámci počítaného limitu úložiště. Pokud dojde k překročení limitu úložiště, pak se maximální propustnost automaticky upraví na vyšší hodnotu. Při použití propustnosti na úrovni databáze v režimu autopilotu je počet kontejnerů povolených v rámci databáze vypočítán jako: (0,001 * maximální propustnost). Pokud například zřídíte 20 000 autopilot RU/s, databáze může mít 20 kontejnerů.

## <a name="benefits-of-autopilot-mode"></a>Výhody režimu autopilotu

Kontejnery Azure Cosmos, které jsou nakonfigurované v režimu autopilotu, mají následující výhody:

* **Jednoduché:** Kontejnery v režimu autopilotu odstraňují složitost pro správu zřízené propustnosti (ru) a kapacitu ručně pro různé kontejnery.

* **Škálovatelné:** Kontejnery v režimu autopilotu bez problémů škálují kapacitu zřízené propustnosti podle potřeby. Neexistují žádné přerušení připojení klientů, aplikace a neovlivňují žádné existující SLA.

* **Nákladově efektivní:** Pokud používáte kontejnery Cosmos Azure nakonfigurované v režimu autopilot, platíte jenom za prostředky, které vaše úlohy potřebují po hodinách.

* **Vysoce dostupné:** Kontejnery Azure Cosmos v režimu autopilotu používají stejný globálně distribuovaný back-end, odolný proti chybám, aby se zajistila odolnost dat a vysoká dostupnost.

## <a name="use-cases-of-autopilot-mode"></a>Případy použití režimu autopilotu

Případy použití pro kontejnery Azure Cosmos nakonfigurované v režimu autopilotu zahrnují:

* **Proměnlivé úlohy:** Když spouštíte lehce používanou aplikaci s špičkou využití 1 hodiny do několika hodin několikrát denně nebo několikrát za rok. Mezi příklady patří aplikace pro lidské zdroje, rozpočtování a generování sestav provozu. V takových scénářích můžete použít kontejnery nakonfigurované v režimu autopilotu, takže už nemusíte ručně zřizovat kapacitu pro nejvyšší nebo střední kapacitu.

* **Nepředvídatelné úlohy:** Když spouštíte úlohy, u kterých je využití databáze v průběhu dne, ale také špička aktivity, která je obtížné odhadnout. Příklad obsahuje lokalitu provozu, která se dohlíží na nárůst aktivity při změně předpovědi počasí. Kontejnery nakonfigurované v režimu autopilotu upravují kapacitu tak, aby splňovala požadavky na zatížení ve špičce aplikace, a při překročení nárůstu aktivity se znovu zvětší.

* **Nové aplikace:** Pokud nasazujete novou aplikaci a nejste si jistí, jak velká část zřízené propustnosti (tj. kolik ru) potřebujete. Pomocí kontejnerů nakonfigurovaných v režimu autopilotu můžete automaticky škálovat na požadavky na kapacitu a požadavky vaší aplikace.

* **Zřídka používané aplikace:** Máte-li aplikaci, která se používá pouze několik hodin několikrát za den nebo týden nebo měsíc, jako je například aplikace s nízkým objemem nebo web nebo blogový Web.

* **Databáze pro vývoj a testování:** Vývojáři používají účty Azure Cosmos během pracovní doby, ale nepotřebují je po nocích nebo víkendech. U kontejnerů nakonfigurovaných v režimu autopilotu se škálují na minimum, pokud se nepoužívají.

* **Plánované provozní úlohy a dotazy:** Pokud máte sérii plánovaných požadavků/operací/dotazů na jednom kontejneru a v případě nečinných dob, kdy chcete spustit při absolutní nízké propustnosti, můžete to provést snadno. Při odeslání plánovaného dotazu nebo žádosti do kontejneru nakonfigurovaného v režimu autopilotu se automaticky navýší velikost podle potřeby a spustí se operace.

Řešení pro předchozí problémy vyžadují nejen obrovský čas implementace, ale také přináší složitost konfigurace nebo kódu a často vyžadují ruční zásah, aby je bylo možné řešit. Režim autopilotu umožňuje výše uvedeným scénářům docházet z rámečku, takže už se nemusíte starat o tyto problémy.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Porovnání – kontejnery nakonfigurované v ručním režimu vs. režim autopilotu

|  | Kontejnery nakonfigurované v ručním režimu  | Kontejnery nakonfigurované v režimu autopilotu |
|---------|---------|---------|
| **Zřízená propustnost** | Ručně zřízené | Automatické a okamžité škálování na základě vzorců využití úloh. |
| **Frekvence – omezení požadavků/operací (429)**  | Může nastat, pokud spotřeba překročí zřízenou kapacitu. | Nedojde k tomu, pokud je využitá propustnost v rámci maximální propustnosti, kterou zvolíte v režimu autopilotu.   |
| **Plánování kapacity** |  Musíte provést počáteční plánování kapacity a zřídit propustnost, kterou potřebujete. |    Nemusíte si dělat starosti s plánováním kapacity. Systém se automaticky postará o plánování kapacity a správu kapacity. |
| **Ceny** | Ručně zřízené RU/s za hodinu | V případě účtů oblastí s jedním zápisem platíte za propustnost využité každou hodinu, a to pomocí programu autopilot RU/s za hodinovou sazbu. <br/><br/>U účtů s více oblastmi zápisu se neúčtují žádné další poplatky za autopilot. Za tuto propustnost platíte za každou hodinu, a to pomocí stejné sady multi-Master RU/s za hodinovou sazbu. |
| **Nejvhodnější pro typy úloh** |  Předvídatelné a stabilní úlohy|   Nepředvídatelné a proměnlivé úlohy  |

## <a name="a-idenable-autopilot-enable-autopilot-from-azure-portal"></a><a id="enable-autopilot"> povolení autopilotu z Azure Portal

K vyzkoušení autopilotního projektu můžete v účtech Azure Cosmos povolit v z Azure Portal. Pomocí následujících kroků povolte možnost autopilot:

1. Přihlaste se k [Azure Portal.](https://portal.azure.com)

2. Přejděte k účtu Azure Cosmos a otevřete kartu **nové funkce** . Vyberte **Automatický pilotní** a **Zaregistrujte** se, jak je znázorněno na následujícím snímku obrazovky:

![Vytvoření kontejneru v režimu autopilotu](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Vytvoření databáze nebo kontejneru pomocí režimu autopilotu

Při vytváření můžete nakonfigurovat autopilot pro databáze nebo kontejnery. Následující postup použijte k vytvoření nové databáze nebo kontejneru, povolení modulu autopilot a určení maximální propustnosti.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) nebo ke [službě Azure Cosmos Explorer.](https://cosmos.azure.com/)

1. Přejděte k účtu Azure Cosmos a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner**, zadejte název svého kontejneru, klíč oddílu. Vyberte možnost **autopilot** a zvolte maximální propustnost, kterou kontejner při použití možnosti autopilotu nepřekračuje.

   ![Vytvoření kontejneru v režimu autopilotu](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Vyberte **OK**.

Podobně jako u podobných kroků můžete také vytvořit databázi s zřízenou propustností v režimu autopilotu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
