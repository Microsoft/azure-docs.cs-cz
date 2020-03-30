---
title: Výběr správné možnosti nasazení
description: Zjistěte, jak si vybrat mezi možnostmi nasazení v rámci Azure SQL mezi databázemi SQL, instancemi spravovanými SQL a SQL Serverem na virtuálních počítačích Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Cloud SQL Server, SQL Server v cloudu, databáze PaaS, cloudový SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/22/2019
ms.openlocfilehash: e642454807511e8e0bc0b6b6ca7af837e03de2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821351"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Výběr správné možnosti nasazení v Azure SQL

Zjistěte, jak se jednotlivé možnosti nasazení vejdou do datové platformy Azure SQL od Microsoftu, a získejte pomoc, která odpovídá správné možnosti pro vaše obchodní požadavky. Ať už upřednostňujete úspory nákladů nebo minimální správu, tento článek vám pomůže rozhodnout, který přístup přináší proti obchodním požadavkům, na kterých vám nejvíce záleží.

## <a name="microsofts-azure-sql-data-platform"></a>Datová platforma Azure SQL od Microsoftu

Azure SQL je moderní platforma SQL, která nabízí několik možností nasazení, která využívá špičkový modul Microsoft SQL Server, od migrace výtahů a přesunů přes modernizaci stávajících aplikací až po vytváření moderních cloudových služeb. Azure SQL je navržený tak, aby podporoval širokou škálu aplikačních vzorů s různými úrovněmi kontroly nad základní platformou, aby splňoval y nejnáročnější požadavky na migraci a modernizaci. Azure SQL eliminuje složitost správy různých kolekcí aplikací založených na serveru SQL Server ve velkém měřítku tím, že poskytuje jediné, jednotné prostředí pro správu.

Jednou z nejdůležitějších věcí, které je potřeba chápat, pokud se porovnávají výhody databází na Azure oproti využívání místních databází SQL Serveru, je to, že použitelné jsou všechny z nich. Datová platforma Microsoftu využívá technologii SQL Serveru a zpřístupňuje ji napříč fyzickými místními počítači, privátními cloudovými prostředími, hostitelskými cloudovými prostředími třetích stran a veřejným cloudem. SQL Server na virtuálních počítačích Azure (virtuální počítače SQL) vám umožňuje uspokojovat jedinečné a rozmanité obchodní potřeby prostřednictvím kombinace místních a cloudových nasazení a zároveň používat stejnou sadu serverových produktů, vývojových nástrojů a odborných znalostí. v těchto prostředích.

   ![Možnosti SQL Serveru v cloudu: SQL server na IaaS nebo databáze SQL SaaS v cloudu.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak je vidět na obrázku, každá nabídka může být charakterizována úrovní správy, kterou máte nad infrastrukturou, a mírou nákladové efektivity.

V Azure můžete mít vaše úlohy SQL Serveru spuštěny jako hostovaná služba[(PaaS](https://azure.microsoft.com/overview/what-is-paas/)) nebo hostovaná infrastruktura ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). V rámci PaaS máte v rámci každé možnosti nasazení několik možností nasazení a úrovně služeb. Klíčovou otázkou, kterou je třeba položit při rozhodování mezi PaaS nebo IaaS je chcete spravovat databázi, použít opravy a převzít zálohy, nebo chcete delegovat tyto operace do Azure?

V závislosti na odpovědi máte následující možnosti:

- [**SQL databases**](sql-database-technical-overview.md): Nejlepší pro moderní cloudové aplikace, které chtějí používat nejnovější stabilní funkce SQL Serveru a mají časová omezení ve vývoji a marketingu. Plně spravovaný databázový stroj SQL založený na nejnovější stabilní edici Enterprise Edition serveru SQL Server. Jedná se o relační databázi jako službu (DBaaS) hostovanou v cloudu Azure, která spadá do oborové kategorie *platformy jako služby (PaaS).* Databáze SQL obsahuje několik možností nasazení, z nichž každá je postavena na standardizovaném hardwaru a softwaru, který je vlastněn, hostován a spravován společností Microsoft. S SQL Server, můžete použít integrované funkce a funkce, které vyžadují rozsáhlou konfiguraci (buď místní nebo ve virtuálním počítači Azure). Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení. SQL Database obsahuje některé další funkce, které nejsou k dispozici v SQL Server, jako je například vestavěná vysoká dostupnost, inteligence a správa.


  Databáze nabízejí následující možnosti nasazení:
  - Jako [***jediná databáze***](sql-database-single-database.md) s vlastní sadou prostředků spravovaných prostřednictvím databázového serveru. Jedna databáze je podobná [obsažené databáze](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) v SQL Server. Tato možnost je optimalizována pro moderní vývoj aplikací nových aplikací narozených v cloudu. K dispozici jsou možnosti [hyperškálování](sql-database-service-tier-hyperscale.md) a [bez serveru.](sql-database-serverless.md)
  - [***Elastický fond***](sql-database-elastic-pool.md), což je kolekce databází se sdílenou sadou prostředků spravovaných prostřednictvím databázového serveru. Jednotlivé databáze lze přesunout do a z elastického fondu. Tato možnost je optimalizována pro moderní vývoj aplikací nových aplikací narozených v cloudu pomocí vzoru víceklientských aplikací SaaS. Elastické fondy poskytují nákladově efektivní řešení pro správu výkonu více databází, které mají proměnné vzorce využití.
  - [***Databázový server***](sql-database-servers.md), který se používá ke správě skupin jednotlivých databází a elastických fondů. Databázové servery fungují jako centrální administrativní bod pro více databází, [přihlášení](sql-database-manage-logins.md), [pravidel brány firewall](sql-database-firewall-configure.md), pravidel [auditování](sql-database-auditing.md), [zásad detekce hrozeb](sql-database-threat-detection.md)a [skupin s podporou převzetí služeb při selhání](sql-database-auto-failover-group.md).

- [**Instance spravované SQL:**](sql-database-managed-instance.md)Nejlepší pro většinu migrace do cloudu. Spravovaná instance je kolekce systémových a uživatelských databází se sdílenou sadou prostředků, která je připravena pro výtah a posun. Nejvhodnější pro nové aplikace nebo existující místní aplikace, které chtějí používat nejnovější stabilní funkce SQL Serveru a které jsou migrovány do cloudu s minimálními změnami. Spravovaná instance je podobná instanci [databázového stroje Microsoft SQL Server,](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) která nabízí sdílené prostředky pro databáze a další funkce s rozsahem instance. Spravovaná instance podporuje migraci databáze z místního prostředí s minimální až žádnou změnou databáze. Tato možnost poskytuje všechny výhody PaaS Azure SQL Database, ale přidává funkce, které byly dříve k dispozici pouze v sql virtuálních počítačích. To zahrnuje nativní virtuální síť (VNet) a téměř 100% kompatibilitu s místním SQL Serverem. Spravované instance poskytují úplný přístup k serveru SQL Server a kompatibilitu funkcí pro migraci serverů SQL do Azure.


- [**Virtuální počítače SQL:**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)Nejlepší pro migrace a aplikace vyžadující přístup na úrovni operačního systému. Virtuální počítače SQL jsou připravené pro výtah a posun pro stávající aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami nebo bez změn. Virtuální počítače SQL nabízejí úplnou kontrolu správy nad instancí SERVERU SQL Server a základním os pro migraci do Azure. Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. Virtuální počítače SQL spadají do oborové kategorie *Infrastruktura jako služba (IaaS)* a umožňují spouštět SQL Server uvnitř plně spravovaného virtuálního počítače (VM) v cloudu Azure. Virtuální počítače SQL také běží na standardizovaném hardwaru, který je vlastněn, hostován a spravován společností Microsoft. Při použití virtuálních počítačů SQL můžete buď průběžně platit za licenci SQL Serveru, která je již součástí bitové kopie serveru SQL Server, nebo snadno použít existující licenci. Můžete také zastavit nebo obnovit virtuální počítače podle potřeby. SQL Server nainstalovaný a hostovaný v cloudu běží na virtuálních počítačích se systémem Windows Server nebo Linux běžících v Azure, označovaných také jako infrastruktura jako služba (IaaS). Virtuální počítače SQL je dobrou volbou pro migraci místních databází a aplikací SERVERU SQL Server bez jakékoli změny databáze. Všechny nejnovější verze a edice SQL Serveru jsou k dispozici pro instalaci ve virtuálním počítači IaaS. Nejvýznamnější rozdíl od databází SQL a sql spravované instance je, že SQL Server virtuálních můech povolit plnou kontrolu nad databázový stroj. Můžete si vybrat, kdy spustit údržbu nebo opravy, změnit model obnovení na jednoduché nebo zaznamenány hromadné, pozastavit nebo spustit službu v případě potřeby a můžete plně přizpůsobit databázový stroj SQL Server. S tímto dodatečným ovládacím prvkem přichází další odpovědnost za správu virtuálního počítače.

  Optimalizované pro migraci existujících aplikací do Azure nebo rozšíření existujících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. S virtuálními počítači SQL máte úplná práva pro správu nad vyhrazenou instancí SERVERU SQL server a cloudovým virtuálním počítačem. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.


Další rozdíly jsou uvedeny v následující tabulce, ale ***databáze i spravované instance jsou optimalizovány tak, aby snížily celkové náklady na správu na minimum pro zřizování a správu mnoha databází.*** Snižuje náklady na průběžnou správu, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](sql-database-automated-backups.md). Obecně platí, že Azure SQL Database může výrazně zvýšit počet databází, které spravuje jeden IT nebo vývojářský zdroj. [Elastické fondy](sql-database-elastic-pool.md) také podporují architektury víceklientských aplikací SaaS s funkcemi, včetně izolace tenanta a možnosti škálování za účelem snížení nákladů sdílením prostředků napříč databázemi. [Spravovaná instance](sql-database-managed-instance.md) poskytuje podporu pro funkce s rozsahem instance, které umožňují snadnou migraci existujících aplikací a také sdílení prostředků mezi databázemi.

| Databáze SQL | Spravované instance SQL | Virtuální počítače SQL |
| :--- | :--- | :--- |
|Podporuje většinu funkcí na úrovni místní databáze. Nejčastěji používané funkce serveru SQL Server jsou k dispozici.<br/>99.995% dostupnost zaručena.<br/>Vestavěné zálohy, opravy, zotavení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Možnost přiřadit potřebné prostředky (CPU/storage) do jednotlivých databází.<br/>Vestavěná pokročilá inteligence a bezpečnost.<br/>Online změna prostředků (CPU/storage).| Podporuje téměř všechny místní možnosti na úrovni instance a databáze. Vysoká kompatibilita s SQL Server místně.<br/>99.99% dostupnost zaručena.<br/>Vestavěné zálohy, opravy, zotavení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Snadná migrace z SQL Serveru.<br/>Privátní IP adresa v rámci virtuální sítě Azure.<br/>Vestavěná pokročilá inteligence a bezpečnost.<br/>Online změna prostředků (CPU/storage).| Máte plnou kontrolu nad sql server motoru. Podporuje všechny místní funkce.<br/>Až 99.99% dostupnost.<br/>Úplná parita s odpovídající verzí místního SQL Serveru.<br/>Opravena, dobře známá verze databázového stroje.<br/>Snadná migrace z místního serveru SQL Server.<br/>Privátní IP adresa v rámci virtuální sítě Azure.<br/>Máte možnost nasadit aplikace nebo služby na hostiteli, kde je umístěn SQL Server.|
|Migrace ze serveru SQL Server může být obtížné.<br/>Některé funkce serveru SQL Server nejsou k dispozici.<br/>Žádná zaručená přesná doba údržby (ale téměř transparentní).<br/>Kompatibilitu s verzí serveru SQL Server lze dosáhnout pouze pomocí úrovní kompatibility databáze.<br/>Nelze přiřadit privátní IP adresu (přístup můžete omezit pomocí pravidel brány firewall).|Stále existuje minimální počet funkcí serveru SQL Server, které nejsou k dispozici.<br/>Žádná zaručená přesná doba údržby (ale téměř transparentní).<br/>Kompatibilitu s verzí serveru SQL Server lze dosáhnout pouze pomocí úrovní kompatibility databáze.|Musíte spravovat zálohy a opravy.<br>Je třeba implementovat vlastní řešení s vysokou dostupností.<br/>Dochází k prostojům při změně prostředků (CPU/úložiště)|
| Databáze až 100 TB. | Až 8 TB. | Instance SQL Serveru s úložištěm s kapacitou až 256 TB. Instance může podporovat tolik databází, kolik je potřeba. |
| Místní aplikace může přistupovat k datům v Azure SQL Database. | [Nativní implementace virtuální sítě](sql-database-managed-instance-vnet-configuration.md) a připojení k místnímu prostředí pomocí azure express route nebo brány VPN Gateway. | S virtuálními počítači SQL můžete mít aplikace, které běží částečně v cloudu a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Další informace o hybridních cloudových řešeních najdete [v tématu Rozšíření místních datových řešení do cloudu](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>Obchodní motivace pro výběr databází, spravovaných instancí nebo virtuálních počítačů SQL

Vaše rozhodnutí vybrat si mezi různými nabídkami dat může ovlivnit několik faktorů:

- [Náklady](#cost) – možnost PaaS i IaaS zahrnují základní cenu, která pokrývá základní infrastrukturu a licencování. Nicméně, s možností IaaS musíte investovat další čas a zdroje pro správu databáze, zatímco v PaaS získáváte tyto funkce správy zahrnuty v ceně. Možnost IaaS umožňuje vypnout prostředky, zatímco je nepoužíváte ke snížení nákladů, zatímco verze PaaS je vždy spuštěna, pokud nepřepadnete a znovu nevytvoříte prostředky, když jsou potřeba.
- [Správa](#administration) - Možnosti PaaS zkracují dobu, kterou je třeba investovat do správy databáze. Omezuje však také rozsah vlastních úloh správy a skriptů, které lze provádět nebo spouštět. Například CLR není podporována s jednou nebo sdružené databáze, ale je podporována pro spravovanou instanci. Také žádné možnosti nasazení v PaaS podporují použití trasování příznaky.
- [Smlouva o úrovni služeb](#service-level-agreement-sla) - IaaS i PaaS poskytují vysokou oborovou smlouvu SLA. Možnost PaaS zaručuje 99,99% sla, zatímco IaaS zaručuje 99,95% sla pro infrastrukturu, což znamená, že je třeba implementovat další mechanismy k zajištění dostupnosti vašich databází. Můžete implementovat řešení s vysokou dostupností na 99,99 % vytvořením dalšího sql serveru ve virtuálním počítači a konfigurací skupin dostupnosti AlwaysOn.
- [Čas přechodu na Azure](#market) – SQL Server v azure virtuálním počítači je přesná shoda vašeho prostředí, takže migrace z místního na virtuální počítač Azure SQL se neliší od přesunutí databází z jednoho místního serveru na jiný. Spravovaná instance také umožňuje velmi snadnou migraci; však může dojít k některé změny, které je třeba použít před migrací do spravované instance.

Tyto faktory budou podrobněji popsány v následujících částech.

### <a name="cost"></a>Náklady

Ať už jste začínající společnost, která nemá mnoho peněz, nebo tým v zavedené společnosti, který pracuje s limitovaným rozpočtem, je omezená výše prostředků často primárním rozhodujícím faktorem při volbě řešení pro hostování firemních databází. V této části se dozvíte o základech fakturace a licencování v Azure s ohledem na tyto dvě možnosti relační databáze: SQL Database a VIRTUÁLNÍ počítače SQL. Zjistíte také, jak vypočítat celkové náklady na aplikaci.

#### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování

V současné době **sql database** se prodává jako služba a je k dispozici s několika možnostmi nasazení a v několika úrovních služeb s různými cenami pro prostředky, z nichž všechny se účtují každou hodinu s pevnou sazbou na základě úrovně služby a výpočetní velikosti, kterou zvolíte. Nejnovější informace o aktuálních úrovních podporovaných služeb, velikostech výpočetních prostředků a částkách úložiště naleznete v [tématu Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

- S databází SQL si můžete vybrat úroveň služeb, která vyhovuje vašim potřebám z široké škály cen od 5 $/měsíc pro základní úroveň.
- Můžete vytvořit [elastické fondy](sql-database-elastic-pool.md) pro sdílení prostředků mezi instancemi databáze, abyste snížili náklady a přizpůsobili se špičkám využití.
- S implementací spravovanou SQL můžete také přinést vlastní licenci. Další informace o licencování přineste si vlastní, najdete [v tématu Mobilita licencí prostřednictvím programu Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/) nebo pomocí [kalkulačky hybridních výhod Azure,](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) kde **najdete způsob,** jak ušetřit až 40 % .

Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Můžete dynamicky upravit úrovně služeb a výpočetní velikosti tak, aby odpovídaly potřebám vaší aplikace různé propustnost.

U **databází SQL a instancí spravovaných SQL**je databázový software automaticky konfigurován, opravován a upgradován společností Microsoft, což snižuje náklady na správu. Kromě toho vám její [integrované funkce zálohování](sql-database-automated-backups.md) pomohou dosáhnout výrazných úspor nákladů, zejména v případě, že máte velký počet databází.

S **virtuálními počítači SQL**můžete použít některou z bitových kopií SQL Serveru poskytovaných platformou (která zahrnuje licenci) nebo přenést licenci SQL Serveru. K dispozici jsou všechny podporované verze systému SQL Server (2008R2, 2012, 2014, 2016) a jeho edice (Developer, Express, Web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL Serveru platíte za minutu licenční náklady na SQL Server a Windows nebo Linux Server, spolu s náklady na úložiště Azure pro disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud do Azure přinesete vlastní licenci SQL Serveru, budou se vám účtovat jenom náklady na server a úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci

Když začnete používat cloudovou platformu, náklady na spuštění aplikace zahrnují náklady na nový vývoj a průběžné náklady na správu a náklady na služby platformy veřejného cloudu.

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

- [Ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Ceny virtuálních strojů](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. S IaaS a PaaS, Microsoft spravuje základní infrastrukturu a automaticky replikuje všechna data poskytovat zotavení po havárii, konfiguruje a upgraduje databázový software, spravuje vyrovnávání zatížení a provádí transparentní převzetí služeb při selhání, pokud je selhání serveru v datovém centru.

- Pomocí **databází SQL a instancí spravovaných SQL**můžete pokračovat ve správě databáze, ale už nemusíte spravovat databázový stroj, operační systém nebo hardware. Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Konfigurace vysoké dostupnosti do jiného datového centra navíc vyžaduje minimální konfiguraci a správu.
- S **virtuálními počítači SQL**máte plnou kontrolu nad konfigurací operačního systému a instance serveru SQL Server. V případě virtuálních počítačů je na vás, kdy se rozhodnete aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako například antivirový program. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)

Pro řadu IT oddělení je nejvyšší prioritou plnit povinnosti z hlediska garantované doby provozuschopnosti vyplývající ze Smlouvy o úrovni služeb (SLA). V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **sql database**poskytuje společnost Microsoft dostupnost s la 99,99 %. Nejnovější informace najdete v tématu věnovaném [Smlouvám o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/sql-database/).

Pro **virtuální počítače SQL**, Microsoft poskytuje dostupnost SLA 99,95 %, která pokrývá pouze virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro databázi vysokou dostupnost (HA) v rámci virtuálních počítačů, měli byste nakonfigurovat jednu z podporovaných možností vysoké dostupnosti v SQL Server, jako je [například vždy na dostupnost skupiny](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

### <a name="time-to-move-to-azure"></a><a name="market"></a>Čas přejít do Azure

**SQL database** (single databases or elastic pools) jsou tím správným řešením pro cloudové aplikace, když je důležitá produktivita vývojářů a rychlý čas uvedení nových řešení na trh. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze.

**Spravovaná instance SQL** výrazně zjednodušuje migraci existujících aplikací do Azure SQL, což vám umožní rychle uvést migrované databázové aplikace na trh v Azure.

**Virtuální počítače SQL** jsou perfektní, pokud vaše stávající nebo nové aplikace vyžadují rozsáhlé databáze nebo přístup ke všem funkcím v SQL Serveru nebo Windows/Linux u a chcete se vyhnout času a nákladům na získání nového místního hardwaru. Je to také vhodné, když chcete migrovat existující místní aplikace a databáze do Azure tak, jak je – v případech, kdy instance spravované Azure SQL Database není vhodné. Vzhledem k tomu, že není nutné měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet na přeplánování stávajícího řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Další kroky

- Podívejte se na [první databázi Azure SQL,](sql-database-single-database-get-started.md) kde můžete začít s databází SQL.
- Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
- Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
- [Identifikujte správnou databázi SQL nebo skladovou položku spravované sql pro místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
