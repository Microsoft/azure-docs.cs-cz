---
title: Výběr správného typu nasazení – Azure Database for MariaDB
description: Tento článek popisuje, co je třeba zvážit před nasazením Azure Database for MariaDB jako IaaS (infrastruktura jako služba) nebo platforma jako služba (PaaS).
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 47aff04dfd44ea7fd892fdee763e93d7fd13a9d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542389"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Volba pravého serveru MariaDB v Azure

S Azure můžou vaše zatížení serveru MariaDB běžet v hostované infrastruktuře virtuálních počítačů jako služba (IaaS) nebo jako hostovaná platforma jako služba (PaaS). PaaS má několik možností nasazení a v rámci každé možnosti nasazení jsou dostupné úrovně služeb. Když si vyberete mezi IaaS a PaaS, musíte se rozhodnout, jestli chcete databázi spravovat, použít opravy a udělat zálohy, nebo jestli chcete tyto operace delegovat na Azure.

Při rozhodování Vezměte v úvahu tyto dvě možnosti:

- **Azure Database for MariaDB:** Tato možnost je plně spravovaný databázový stroj MariaDB založený na stabilní verzi MariaDB Community Edition. Tato relační databáze jako služba (DBaaS), která je hostovaná na cloudové platformě Azure, spadá do kategorie odvětví PaaS.

  Se spravovanou instancí MariaDB v Azure můžete použít integrované funkce, které jinak vyžadují rozsáhlou konfiguraci, když je server MariaDB buď místně, nebo na virtuálním počítači Azure.

  Při použití MariaDB jako služby platíte průběžně s možnostmi horizontálního navýšení nebo navýšení kapacity pro větší kontrolu bez přerušení. A na rozdíl od samostatného serveru MariaDB má Azure Database for MariaDB další funkce, jako je integrovaná vysoká dostupnost, Intelligence a správa.

- **MariaDB na virtuálních počítačích Azure:** Tato možnost spadá do kategorie odvětví IaaS. Pomocí této služby můžete spustit MariaDB Server v rámci plně spravovaného virtuálního počítače na cloudové platformě Azure. Na virtuálním počítači s IaaS můžete nainstalovat všechny poslední verze a edice MariaDB.

  V nejvýznamnějších rozdílech od Azure Database for MariaDB MariaDB na virtuálních počítačích Azure nabízí kontrolu nad databázovým strojem. Tento ovládací prvek ale přichází na zodpovědnost za správu virtuálních počítačů a mnoha úloh správy databází (DBA). Mezi tyto úlohy patří údržba a opravy databázových serverů, obnovení databáze a návrh vysoké dostupnosti.

Hlavní rozdíly mezi těmito možnostmi jsou uvedené v následující tabulce:

| Atribut          | Azure Database for MariaDB | MariaDB na virtuálních počítačích Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Smlouva o úrovni služeb (SLA)                | Nabízí smlouvu SLA s 99,99% dostupností.| Až 99,95% dostupnost se dvěma nebo více instancemi ve stejné skupině dostupnosti.<br/><br/>99,9% dostupnost s jednou instancí virtuálního počítače s využitím služby Premium Storage.<br/><br/>99,99% použití Zóny dostupnosti s více instancemi ve více skupinách dostupnosti.<br/><br/>Podívejte se na [Virtual Machines SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Opravy operačního systému        | Automaticky  | Spravováno zákazníky |
| MariaDB opravy     | Automaticky  | Spravováno zákazníky |
| Vysoká dostupnost | Model vysoké dostupnosti (HA) vychází z vestavěných mechanismů převzetí služeb při selhání pro případ, že dojde k přerušení na úrovni uzlu. V takových případech služba automaticky vytvoří novou instanci a připojí úložiště k této instanci. | Uživatelé architekti, implementujte, otestujete a Udržujte vysokou dostupnost. K funkcím může patřit Clustering s podporou převzetí služeb při selhání, trvalá replikace skupin, přesouvání protokolů nebo transakční replikace.|
| Zónová redundance | Aktuálně není podporováno | Virtuální počítače Azure je možné nastavit tak, aby běžely v různých zónách dostupnosti. Pro místní řešení musí zákazníci vytvořit, spravovat a spravovat svoje vlastní sekundární datové centrum.|
| Hybridní scénáře | Pomocí [replikace vstupních dat](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)můžete synchronizovat data z externího serveru MariaDB do služby Azure Database for MariaDB. Externí server může být místní, virtuální počítače nebo databázová služba, jejímž hostitelem jsou jiní poskytovatelé cloudu.<br/><br/> Pomocí funkce [pro čtení repliky](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) můžete replikovat data ze zdrojového serveru Azure Database for MariaDB do pěti serverů repliky jen pro čtení. Repliky jsou buď ve stejné oblasti Azure, nebo v různých oblastech. Repliky jen pro čtení se asynchronně aktualizují pomocí technologie replikace binlog.<br/><br/>Replikace čtení mezi oblastmi je aktuálně ve verzi Public Preview.| Spravováno zákazníky
| Zálohování a obnovení | Automaticky vytvoří [zálohy serveru](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) a uloží je v uživatelsky nakonfigurovaném úložišti, které je buď místně redundantní, nebo geograficky redundantní. Služba používá zálohy úplného, rozdílového a transakčního protokolu. | Spravováno zákazníky |
| Monitorování operací databáze | Nabízí zákazníkům možnost [nastavit výstrahy](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) pro databázovou operaci a působit při dosažení prahových hodnot. | Spravováno zákazníky |
| Advanced Threat Protection | Poskytuje [rozšířenou ochranu před internetovými útoky](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal). Tato ochrana detekuje aktivity neobvyklé, které naznačují neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.<br/><br/>Rozšířená ochrana před internetovými útoky je aktuálně ve verzi Public Preview.| Zákazníci musí tuto ochranu sami sestavit.
| Zotavení po havárii | Ukládá automatizované zálohy v uživatelsky nakonfigurovaném [místně redundantním nebo geograficky redundantním úložišti](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal). Zálohování může také obnovit server k určitému bodu v čase. Doba uchovávání dat je odkudkoli 7 až 35 dní. Obnovení je provedeno pomocí Azure Portal. | Plně spravovaná zákazníky. Mezi odpovědnosti patří mimo jiné plánování, testování, archivace, ukládání a uchovávání dat. Další možností je použití trezoru služby Azure Recovery Services k zálohování virtuálních počítačů a databází Azure na virtuálních počítačích. Tato možnost je ve verzi Preview. |
| Doporučení k výkonu | Poskytuje zákazníkům doporučení týkající se [výkonu](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) na základě systémem generovaných souborů protokolu využití. Doporučení umožňují optimalizovat úlohy.<br/><br/>Doporučení pro výkon jsou momentálně ve verzi Public Preview. | Spravováno zákazníky |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Obchodní motivace pro výběr PaaS nebo IaaS

Existuje několik faktorů, které mohou mít vliv na rozhodnutí zvolit PaaS nebo IaaS pro hostování databází MariaDB.

### <a name="cost"></a>Náklady

Omezené financování je často primárním aspektem, který určuje nejlepší řešení pro hostování databází. To platí bez ohledu na to, jestli se jedná o spuštění s malým hotovostem nebo týmem v zřízené společnosti, která funguje s přísnými rozpočtovými omezeními. Tato část popisuje základy fakturace a licencování v Azure, které se vztahují na Azure Database for MariaDB a MariaDB na virtuálních počítačích Azure.

#### <a name="billing"></a>Fakturace

Azure Database for MariaDB je v tuto chvíli k dispozici jako služba na několika úrovních s různými cenami za prostředky. Všechny prostředky se účtují po hodinách za pevnou sazbu. Nejnovější informace o aktuálně podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete v článku o [nákupu modelu založeném na Vcore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Úrovně služeb a výpočetní velikosti můžete dynamicky upravovat tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace. Účtuje se vám odchozí přenos přes Internet za běžné [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

Pomocí Azure Database for MariaDB Microsoft automaticky konfiguruje, opraví a upgraduje databázový software. Tyto automatizované akce omezují náklady na správu. Azure Database for MariaDB také integrované možnosti [zálohování](https://docs.microsoft.com/azure/MariaDB/concepts-backup) . Tyto funkce vám pomůžou dosáhnout výrazné úspory nákladů, zejména pokud máte velký počet databází. Na rozdíl od MariaDB na virtuálních počítačích Azure můžete zvolit a spustit libovolnou MariaDB verzi. Bez ohledu na to, jakou verzi MariaDB používáte, platíte za zřízený virtuální počítač a náklady na konkrétní použitý typ licence MariaDB.

Azure Database for MariaDB poskytuje integrovanou vysokou dostupnost pro jakýkoliv typ přerušení na úrovni uzlu a zároveň zachovává záruku smlouvy SLA pro službu 99,99%. Pro vysokou dostupnost databáze ale v rámci virtuálních počítačů by zákazníci měli používat možnosti vysoké dostupnosti, jako je [MariaDB replikace](https://mariadb.com/kb/en/library/setting-up-replication/) , která je k dispozici v databázi MariaDB. Použití podporované možnosti vysoké dostupnosti neposkytuje další smlouvu SLA. Ale umožňuje dosáhnout větší než 99,99% dostupnost databáze při dalších nákladech a administrativní režii.

Další informace o cenách najdete v následujících článcích:
* [Ceny Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

V mnoha firmách je rozhodování o přechodu na cloudovou službu velmi náročné na složitost správy, protože se jedná o náklady. S IaaS a PaaS Microsoft:

- Spravuje základní infrastrukturu.
- Automaticky replikuje všechna data pro zajištění zotavení po havárii.
- Nakonfiguruje a upgraduje databázový software.
- Spravuje vyrovnávání zatížení.
- Převezme transparentní služby při selhání, pokud dojde k selhání serveru.

Následující seznam popisuje požadavky na správu pro jednotlivé možnosti:

* Pomocí Azure Database for MariaDB můžete dál spravovat databázi. Ale už nemusíte spravovat databázový stroj, operační systém ani hardware. Mezi položky, které můžete dál spravovat, patří:

  - Databáze
  - Přihlášení
  - Ladění indexu
  - Ladění dotazů
  - Auditování
  - Zabezpečení

  Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální konfiguraci nebo správu.

* S MariaDB na virtuálních počítačích Azure máte plnou kontrolu nad operačním systémem a konfigurací instance serveru MariaDB. S virtuálním počítačem se rozhodnete, kdy se má aktualizovat nebo upgradovat operační systém a databázový software. Také se rozhodnete, kdy nainstalovat další software, jako je třeba antivirová aplikace. K dispozici jsou některé automatizované funkce, které výrazně zjednodušují opravy, zálohování a vysokou dostupnost. Můžete řídit velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Další informace najdete v tématu [velikosti virtuálních počítačů a cloudových služeb pro Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Čas přechodu do Azure

* Azure Database for MariaDB je to pravé řešení pro cloudové aplikace, když je produktivita vývojářů a rychlá doba uvedení na trh pro nová řešení. Díky programovým funkcím, které jsou jako DBA, je služba vhodná pro cloudové architekty a vývojáře, protože snižuje nutnost správy základního operačního systému a databáze.

* Pokud se chcete vyhnout času a nákladům na získání nového místního hardwaru, MariaDB na virtuálních počítačích Azure je správné řešení pro aplikace, které vyžadují databázi MariaDB, nebo přístup k funkcím MariaDB v systému Windows nebo Linux. Toto řešení je také vhodné k migraci stávajících místních aplikací a databází do Azure beze změny, pokud je Azure Database for MariaDB nekvalitním potřebám.

  Vzhledem k tomu, že není potřeba měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet při reorganizaci stávajícího řešení. Místo toho se můžete soustředit na migraci všech vašich řešení do Azure a řešit některé optimalizace výkonu, které může platforma Azure vyžadovat.

## <a name="next-steps"></a>Další kroky

* Viz [ceny Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/).
* Začněte [vytvořením prvního serveru](https://docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).
