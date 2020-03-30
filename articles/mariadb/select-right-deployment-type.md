---
title: Výběr správného typu nasazení – Azure Database for MariaDB
description: Tento článek popisuje, jaké faktory je třeba zvážit před nasazením Azure Database pro MariaDB jako infrastruktury jako služby (IaaS) nebo platformy jako služby (PaaS).
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4c7eb5e4f22cb432a9d17e6eafa653e62e1f9129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529896"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Výběr správné možnosti serveru MariaDB v Azure

S Azure, vaše úlohy serveru MariaDB můžete spustit v hostované infrastruktury virtuálních strojů jako služba (IaaS) nebo jako hostovaná platforma jako služba (PaaS). PaaS má více možností nasazení a v rámci každé možnosti nasazení existují úrovně služeb. Když si vyberete mezi IaaS a PaaS, musíte se rozhodnout, jestli chcete spravovat databázi, použít opravy a vytvořit zálohy, nebo pokud chcete delegovat tyto operace do Azure.

Při rozhodování zvažte následující dvě možnosti:

- **Databáze Azure pro MariaDB:** Tato možnost je plně spravovaný databázový stroj MariaDB založený na stabilní verzi komunitní edice MariaDB. Tato relační databáze jako služba (DBaaS), hostovaná na cloudové platformě Azure, spadá do oborové kategorie PaaS.

  Se spravovanou instancí MariaDB v Azure můžete použít integrované funkce, které jinak vyžadují rozsáhlou konfiguraci, když je MariaDB Server místní nebo v virtuálním počítači Azure.

  Při použití MariaDB jako služby platíte průběžně s možnostmi škálování nebo škálování pro větší kontrolu bez přerušení. A na rozdíl od samostatného serveru MariaDB má Azure Database for MariaDB další funkce, jako je integrovaná vysoká dostupnost, inteligence a správa.

- **MariaDB na virtuálních počítačích Azure:** Tato možnost spadá do průmyslové kategorie IaaS. Pomocí této služby můžete spustit MariaDB Server uvnitř plně spravovaného virtuálního počítače na cloudové platformě Azure. Všechny nejnovější verze a edice MariaDB lze nainstalovat na virtuální stroj IaaS.

  V nejvýznamnější rozdíl od Azure Database pro MariaDB, MariaDB na virtuálních počítačích Azure nabízí kontrolu nad databázový stroj. Tento ovládací prvek však přichází na úkor odpovědnosti za správu virtuálních zařízení a mnoho úloh správy databáze (DBA). Mezi tyto úkoly patří údržba a opravy databázových serverů, obnovení databáze a návrh s vysokou dostupností.

Hlavní rozdíly mezi těmito možnostmi jsou uvedeny v následující tabulce:

|            | Azure Database for MariaDB | MariaDB na virtuálních počítačích Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Smlouva o úrovni služeb (SLA)                | Nabízí SLA 99.99% dostupnost| Až 99,95% dostupnost se dvěma nebo více instancemi ve stejné sadě dostupnosti.<br/><br/>99,9% dostupnost s jedním virtuálním virtuálním montovnam s využitím úložiště premium.<br/><br/>99,99 % používá zóny dostupnosti s více instancemi ve více sadách dostupnosti.<br/><br/>Podívejte se na [sla virtuálních počítačů](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Opravy operačního systému        | Automaticky  | Spravováno zákazníky |
| MariaDB opravy     | Automaticky  | Spravováno zákazníky |
| Vysoká dostupnost | Model vysoké dostupnosti (HA) je založen na integrované mechanismy převzetí služeb při selhání pro při přerušení na úrovni uzlu. V takových případech služba automaticky vytvoří novou instanci a připojí úložiště k této instanci. | Zákazníci architekt, implementovat, testovat a udržovat vysokou dostupnost. Možnosti mohou zahrnovat vždy zapnuté clustering s podporou převzetí služeb při selhání, replikaci skupiny always-on, odesílání protokolu nebo transakční replikaci.|
| Redundance zóny | Momentálně není podporováno. | Virtuální počítače Azure se můžou spouštět v různých zónách dostupnosti. Pro místní řešení musí zákazníci vytvářet, spravovat a udržovat své vlastní sekundární datové centrum.|
| Hybridní scénáře | Pomocí [replikace dat](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)můžete synchronizovat data z externího serveru MariaDB do služby Azure Database for MariaDB. Externí server může být místní, ve virtuálních počítačích nebo databázová služba hostovaná jinými poskytovateli cloudu.<br/><br/> Pomocí funkce [repliky pro čtení](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) můžete replikovat data z databáze Azure pro hlavní server MariaDB až do pěti replik jen pro čtení. Repliky jsou buď ve stejné oblasti Azure nebo napříč oblastmi. Repliky jen pro čtení jsou asynchronně aktualizovány pomocí technologie replikace binlogu.<br/><br/>Replikace čtení mezi oblastmi je aktuálně ve verzi Public Preview.| Spravováno zákazníky
| Zálohování a obnovení | Automaticky vytváří [zálohy serveru](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) a ukládá je do úložiště nakonfigurovaného uživatelem, které je místně redundantní nebo geograficky redundantní. Služba trvá úplné, rozdílové a transakční protokol zálohy | Spravováno zákazníky |
| Sledování databázových operací | Nabízí zákazníkům možnost [nastavit výstrahy](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) na databázi operace a jednat při dosažení prahových hodnot. | Spravováno zákazníky |
| Rozšířená ochrana před internetovými útoky | Poskytuje [rozšířenou ochranu před hrozbami](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal). Tato ochrana detekuje neobvyklé aktivity, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.<br/><br/>Rozšířená ochrana před internetovými hrozbami je momentálně ve verzi Public Preview.| Zákazníci musí tuto ochranu vytvořit pro sebe.
| Zotavení po havárii | Ukládá automatické zálohování v místně redundantním [nebo geograficky redundantním úložišti](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)nakonfigurovaném uživatelem . Zálohy můžete také obnovit server do bodu v čase. Retenční doba je kdekoli od 7 do 35 dnů. Obnovení se provádí pomocí portálu Azure. | Plně řízeno zákazníky. Mezi povinnosti patří mimo jiné plánování, testování, archivace, ukládání a uchovávání. Další možností je použití trezoru služby Azure Recovery Services k zálohování virtuálních počítačů azure a databází na virtuálních počítačích. Tato volba je ve verzi Preview. |
| Doporučení k výkonu | Poskytuje zákazníkům [doporučení pro výkon](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) založená na souborech protokolu využití generovaných systémem. Doporučení pomáhají optimalizovat úlohy.<br/><br/>Doporučení pro výkon jsou aktuálně ve verzi Public Preview. | Spravováno zákazníky |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Obchodní motivace pro výběr PaaS nebo IaaS

Existuje několik faktorů, které mohou ovlivnit vaše rozhodnutí vybrat PaaS nebo IaaS hostit vaše Databáze MariaDB.

### <a name="cost"></a>Náklady

Omezené financování je často primární mzda, která určuje nejlepší řešení pro hostování databází. To platí bez ohledu na to, zda jste startup s malým i mincovním i týmem v zavedené společnosti, která pracuje pod přísnými rozpočtovými omezeními. Tato část popisuje základy fakturace a licencování v Azure, protože se vztahují na Azure Database for MariaDB a MariaDB na virtuálních počítačích Azure.

#### <a name="billing"></a>Fakturace

Azure Database for MariaDB je momentálně dostupná jako služba v několika vrstvách s různými cenami za prostředky. Všechny zdroje se účtují každou hodinu pevnou sazbou. Nejnovější informace o aktuálně podporovaných úrovních služeb, velikostech výpočetních prostředků a částkách úložiště naleznete v [tématu nákupní model založený na virtuálních jádrech](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Můžete dynamicky upravit úrovně služeb a výpočetní velikosti tak, aby odpovídaly potřebám vaší aplikace různé propustnost. Poplatky za odchozí internetový provoz se vám účtují při pravidelných [sazbách za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

S Azure Database pro MariaDB Microsoft automaticky konfiguruje, opravuje a upgraduje databázový software. Tyto automatizované akce snižují náklady na správu. Azure Database for MariaDB má také integrované možnosti [zálohování.](https://docs.microsoft.com/azure/MariaDB/concepts-backup) Tyto funkce vám pomohou dosáhnout významné úspory nákladů, zejména v případě, že máte velký počet databází. Naproti tomu s MariaDB na virtuálních počítačích Azure můžete vybrat a spustit libovolnou verzi MariaDB. Bez ohledu na to, jakou verzi MariaDB používáte, zaplatíte za zřízený virtuální virtuální virtuální m a náklady na konkrétní typ licence MariaDB.

Azure Database for MariaDB poskytuje integrovanou vysokou dostupnost pro jakýkoli druh přerušení na úrovni uzlu při zachování 99,99% záruky sla pro službu. Pro vysokou dostupnost databáze v rámci virtuálních počítačů by však zákazníci měli používat možnosti vysoké dostupnosti, jako je [replikace MariaDB,](https://mariadb.com/kb/en/library/setting-up-replication/) které jsou k dispozici v databázi MariaDB. Použití podporované možnosti vysoké dostupnosti neposkytuje další sla. Ale to vám umožní dosáhnout větší než 99,99 % dostupnost databáze za další náklady a administrativní režie.

Další informace o cenách naleznete v následujících článcích:
* [Azure Database pro ceny MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Ceny virtuálních strojů](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

Pro mnoho firem je rozhodnutí o přechodu na cloudovou službu stejně tak o snižování složitosti správy jako o nákladech. S IaaS a PaaS, Microsoft:

- Spravuje základní infrastrukturu.
- Automaticky replikuje všechna data a poskytuje zotavení po havárii.
- Konfiguruje a inovuje databázový software.
- Spravuje vyrovnávání zatížení.
- Provede transparentní převzetí služeb při selhání, pokud dojde k selhání serveru.

Následující seznam popisuje administrativní aspekty pro každou možnost:

* S Azure Database pro MariaDB můžete pokračovat ve správě databáze. Ale již není nutné spravovat databázový stroj, operační systém nebo hardware. Mezi položky, které můžete nadále spravovat, patří:

  - Databáze
  - Přihlášení
  - Ladění indexu
  - Optimalizace dotazů
  - Auditování
  - Zabezpečení

  Konfigurace vysoké dostupnosti do jiného datového centra navíc vyžaduje minimální až žádnou konfiguraci nebo správu.

* S MariaDB na virtuálních počítačích Azure máte plnou kontrolu nad operačním systémem a konfigurací instance serveru MariaDB. S virtuálním počítačem se rozhodnete, kdy aktualizovat nebo upgradovat operační systém a databázový software. Můžete se také rozhodnout, kdy nainstalovat další software, například antivirovou aplikaci. Některé automatizované funkce jsou k dispozici výrazně zjednodušit opravy, zálohování a vysokou dostupnost. Můžete řídit velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Další informace najdete v tématu [Velikosti virtuálních strojů a cloudových služeb pro Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Čas přejít do Azure

* Azure Database for MariaDB je správné řešení pro cloudové aplikace, když je produktivita vývojářů a rychlý čas uvedení nových řešení na trh kritické. S programovou funkcí, která je jako DBA, služba je vhodná pro cloud architekty a vývojáře, protože snižuje potřebu správy základního operačního systému a databáze.

* Pokud se chcete vyhnout času a nákladům na získání nového místního hardwaru, MariaDB na virtuálních počítačích Azure je to pravé řešení pro aplikace, které vyžadují databázi MariaDB nebo přístup k funkcím MariaDB ve Windows nebo Linuxu. Toto řešení je také vhodné pro migraci existujících místních aplikací a databází do Azure beze změny, pro případy, kdy Azure Database pro MariaDB je nevyhovující.

  Vzhledem k tomu, že není nutné měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet na rearchitecting stávajícího řešení. Místo toho se můžete zaměřit na migraci všech vašich řešení do Azure a adresování některých optimalizací výkonu, které může platforma Azure vyžadovat.

## <a name="next-steps"></a>Další kroky

* Viz [Azure Database for MariaDB pricing](https://azure.microsoft.com/pricing/details/MariaDB/).
* Můžete začít [vytvořením prvního serveru](https://docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).
