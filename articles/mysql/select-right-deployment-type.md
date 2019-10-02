---
title: Výběr správného typu nasazení pro Azure Database for MySQL
description: Tento článek popisuje, co je potřeba vzít v úvahu před tím, než se předá infrastruktura jako služba (IaaS) nebo platforma jako služba (PaaS) pro vaše Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7bcbf379ea8d046c8c477dc716711a6a6ffa1dc9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813831"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Volba pravého serveru MySQL v Azure

S Azure můžou vaše úlohy serveru MySQL běžet ve virtuálním počítači hostované infrastruktury (IaaS) nebo jako hostovaná služba (PaaS). PaaS má několik možností nasazení a v rámci každé možnosti nasazení jsou dostupné úrovně služeb. Při rozhodování mezi PaaS nebo IaaS je nutné určit, zda chcete databázi spravovat, použít opravy a provést zálohování, nebo chcete tyto operace delegovat na Azure.</br>

Na základě vaší odpovědi na tuto otázku Vezměte v úvahu následující možnosti: <br/>

**Azure Database for MySQL** je plně spravovaný databázový stroj MySQL založený na stabilní verzi edice Community. Tato relační databáze jako služba (DBaaS), která je hostovaná v cloudu Azure, spadá do oborové kategorie typu platforma jako služba (PaaS). Se spravovanou instancí MySQL v Azure můžete používat integrované funkce a funkce, které vyžadují rozsáhlou konfiguraci při použití serveru MySQL (buď místně, nebo na virtuálním počítači Azure). Při použití MySQL jako služby můžete průběžně škálovat možnosti horizontálního navýšení nebo navýšení kapacity bez přerušení. Kromě toho na rozdíl od samostatného serveru MySQL má Azure Database for MySQL k dispozici další funkce, jako je například integrovaná vysoká dostupnost, Intelligence a správa. <br/><br/>
Služba **MySQL na virtuálním počítači Azure** spadá do oborové kategorie infrastruktura jako služba (IaaS) a umožňuje spustit MySQL server v rámci plně spravovaného virtuálního počítače v cloudu Azure. Všechny poslední verze a edice MySQL se můžou nainstalovat na virtuální počítač s IaaS. Nejvýznamnějším rozdílem od Azure Database for MySQL je to, že MySQL na virtuálních počítačích Azure umožňuje kontrolu nad databázovým strojem. Tento ovládací prvek se ale dodává na náklady na další zodpovědnost za správu virtuálních počítačů a spousty úloh DBA, jako je například údržba a opravy databázového serveru, návrh obnovení a vysoká dostupnost atd.

Hlavní rozdíly mezi těmito možnostmi jsou uvedené v následující tabulce:

|            | **Azure Database for MySQL** | **MySQL na virtuálních počítačích Azure**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | Nabízí smlouvu SLA s 99,99% dostupností.| Až 99,95% dostupnost s 2 nebo více instancemi ve stejné skupině dostupnosti. <br/>99,9% virtuální počítač s jednou instancí využívající službu Premium Storage <br/> 99,99% s zónou dostupnosti se 2 nebo více instancemi ve 2 nebo více skupině dostupnosti.<br/> Poznámka: [smlouva SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **Opravy operačního systému**        | Automaticky  | Spravováno zákazníky |
|**Aktualizace MySQL**     | Automaticky  | Spravováno zákazníky |
| **Vysoká dostupnost** | Model vysoké dostupnosti (HA) vychází z vestavěných mechanismů převzetí služeb při selhání, když dojde k přerušení na úrovni uzlu. V takových případech služba automaticky vytvoří instanci a připojí úložiště k této nové instanci. | Vysoká dostupnost je navržena, implementována, testována a udržována zákazníkem. To může zahrnovat vždycky zapnutý (Clustering s podporou převzetí služeb při selhání nebo replikaci skupin), přenos protokolů a transakční replikaci, a to v závislosti na používané verzi modulu MySQL.|
| **Redundance zóny** | Aktuálně se nepodporuje. | Virtuální počítače Azure je možné nastavit tak, aby běžely v různých zónách dostupnosti. Pro místní řešení se očekává, že zákazníci vytvoří, spravují a udržují svoje vlastní sekundární datové centrum.|
| **Hybridní scénáře** | [Replikace vstupních dat](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) umožňuje synchronizovat data z externího serveru MySQL do služby Azure Database for MySQL. Externí server může být místní, virtuální počítače nebo databázová služba, jejímž hostitelem jsou jiní poskytovatelé cloudu.  <br/> <br/> Funkce [replika čtení](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) umožňuje replikovat data z Azure Database for MySQL serveru (hlavní) do až pěti serverů jen pro čtení (replik) ve stejné oblasti Azure nebo v různých oblastech. Repliky jen pro čtení se asynchronně aktualizují pomocí technologie replikace binlog.   <br/> <br/> Poznámka – replikace čtení mezi oblastmi je momentálně ve verzi Public Preview.| Spravováno zákazníky <br/>
| **Zálohování a obnovení** | Automaticky vytvoří [zálohy serveru](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Služba používá zálohy úplného, rozdílového a transakčního protokolu. | Spravováno zákazníky |
| **Monitorování operací databáze** | Umožňuje zákazníkům [nastavit výstrahy](https://docs.microsoft.com/azure/mysql/concepts-monitoring) pro databázovou operaci a působit při dosažení prahových hodnot. | Spravováno zákazníky |
| **Rozšířená ochrana před vlákny** | Poskytuje [rozšířenou ochranu před hrozbami](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) , která detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. | Zákazníci se musí sami sestavit.
| **Zálohy (zotavení po havárii)** | Ukládá automatizované zálohování v uživatelsky nakonfigurovaném [místně redundantním nebo geograficky redundantním úložišti](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Zálohy lze také použít k obnovení serveru k určitému bodu v čase. Dobu uchování můžete nastavit z 7-35 dnů. Obnovení můžete provést pomocí Azure Portal. <br/> | Plně spravované zákazníkem, včetně, ale ne omezeného plánování, testování, archivace, ukládání a uchovávání. Další možností je použít Azure Recovery Services Trezor k zálohování virtuálních počítačů a databází Azure na virtuálních počítačích (ve verzi Preview). |
| **Doporučení pro výkon** | Poskytuje zákazníkům proaktivní doporučení na [výkon](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) na základě telemetrie využití, která vám pomůžou s optimalizací úloh. | Spravováno zákazníky |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Obchodní motivace pro výběr PaaS nebo IaaS

Existuje několik faktorů, které mohou mít vliv na rozhodnutí zvolit PaaS nebo IaaS k hostování databází MySQL:

### <a name="cost"></a>Náklady

Bez ohledu na to, jestli jste spouštěcí Strapped pro hotovost nebo tým v zřízené společnosti, která pracuje s přísnými rozpočtovými omezeními, je při určování nejlepšího řešení pro hostování databází často zásadní zvážit omezené financování. Tato část popisuje základy fakturace a licencování v Azure s ohledem na Azure Database for MySQL a MySQL na virtuálních počítačích Azure:

#### <a name="billing"></a>Vyúčtování

V současné době je Azure Database for MySQL k dispozici jako služba v několika úrovních s různými cenami za prostředky, přičemž všechny se účtují po hodinách za pevnou sazbu. Nejnovější informace o aktuálních podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete v článku o [nákupu modelu založeném na Vcore](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Úrovně služeb a výpočetní velikosti můžete dynamicky upravovat tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace. Účtuje se vám odchozí přenos přes Internet za běžné [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

Při Azure Database for MySQL je databázový software automaticky nakonfigurovaný, je opravený a upgradovaný Microsoftem, což snižuje náklady na správu. Kromě toho vám [integrované funkce zálohování](https://docs.microsoft.com/azure/mysql/concepts-backup) pomůžou dosáhnout výrazných úspor nákladů, hlavně v případě, že máte velký počet databází. Pomocí MySQL na virtuálních počítačích Azure můžete zvolit a spustit libovolnou verzi MySQL. Bez ohledu na použitou verzi MySQL platíte za zřízené virtuální počítač a náklady na konkrétní typ licence, který se používá pro MySQL.

Azure Database for MySQL poskytuje vestavěnou vysokou dostupnost pro jakýkoliv typ přerušení na úrovni uzlu a udržuje pro službu SLA 99,99%. Pro zajištění vysoké dostupnosti databáze (HA) v rámci virtuálních počítačů by ale zákazník měl projít možnostmi vysoké dostupnosti dostupnými v databázi MySQL, jako je například [replikace MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html). Použití podporované možnosti vysoké dostupnosti neposkytuje další smlouvu SLA, ale umožňuje zajistit > 99,99% dostupnost databáze při dalších nákladech a administrativní režii.

Další informace o cenách najdete v následujících zdrojích informací a materiálech:
* [Ceny Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

V mnoha firmách je rozhodování o přechodu do cloudové služby velice náročné na složitost správy. V IaaS a PaaS společnost Microsoft spravuje základní infrastrukturu a automaticky replikuje všechna data, aby mohl poskytovat zotavení po havárii, nakonfiguruje a upgraduje databázový software, spravuje vyrovnávání zatížení a transparentní převzetí služeb při selhání, pokud existuje selhání serveru.

* **Pomocí Azure Database for MySQL**můžete dál spravovat databázi, ale nebudete už muset spravovat databázový stroj, operační systém ani hardware. Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální nebo žádnou konfiguraci nebo správu.<br/><br/>
* **S MySQL na virtuálních počítačích Azure**máte plnou kontrolu nad konfigurací operačního systému a instance serveru MySQL. S virtuálním počítačem se rozhodnete, kdy aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako je například anti-virus aplikace. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Informace najdete v tématu velikosti virtuálních počítačů a cloudových služeb pro Azure.

### <a name="time-to-move-to-azure-br"></a>Čas přechodu do Azure <br/>
* **Azure Database for MySQL** je správné řešení pro aplikace navržené pro Cloud, když je vývojářská produktivita a rychlé uvedení na trh pro nová řešení důležitá. S programovou funkcí podobnou službě je služba ideální pro cloudové architekty a vývojáře, protože snižuje nutnost správy základního operačního systému a databáze.<br/><br/>
* **MySQL na virtuálních počítačích Azure** je ideální pro existující nebo nové aplikace, které vyžadují databázi MySQL, nebo přístup k funkcím v databázi MySQL v systému Windows/Linux a vy chcete se vyhnout času a nákladům na získání nového místního hardwaru. Tato možnost je taky vhodná pro migraci stávajících místních aplikací a databází do Azure, jak jsou, v případech, kdy Azure Database for MySQL instance není dobrá. Vzhledem k tomu, že není potřeba měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet při opětovném vytvoření architektury stávajícího řešení. Místo toho se můžete soustředit na migraci všech vašich řešení do Azure a při řešení některých optimalizací výkonu, které může platforma Azure vyžadovat.

## <a name="next-steps"></a>Další kroky

* Zobrazit [ceny Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* Začněte [vytvořením prvního serveru](https://review.docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

