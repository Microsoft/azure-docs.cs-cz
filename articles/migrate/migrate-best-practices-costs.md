---
title: Osvědčené postupy pro výpočet nákladů a velikosti úlohy migrace do Azure | Dokumentace Microsoftu
description: Získejte doporučené postupy pro výpočet nákladů a změny velikosti úlohy migrovat do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 9d1acabd07e7c01445c55a57be9b0c9a36140aa5
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163765"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Osvědčené postupy pro úlohy ocenění a změny velikosti migrovat do Azure

Jako je plánování a návrh pro migraci zajišťuje, zaměřuje se na náklady na dlouhodobý úspěch vaší migrace do Azure. Během migrace projektu, je velmi důležité, že všechny týmy (finance, správa, aplikační týmy atd) porozumět související náklady.
- Před migrací odhadování migrace náklady, Směrný plán pro měsíčně, čtvrtletně, a je zásadní pro úspěch roční cíle rozpočtu.
- Po dokončení migrace by měla optimalizovat náklady, průběžně monitorovat úlohy a plánovat budoucí využití vzorů. Migrované prostředky může začíná jako jeden typ úlohy, ale na jiný typ v průběhu času vyvíjejí, na základě využití, náklady a posunutí obchodní požadavky.

Tento článek popisuje osvědčené postupy pro výpočet nákladů a nastavování velikosti před a po migraci.  

> [!IMPORTANT]
> Osvědčené postupy a názory, které jsou popsané v tomto článku jsou založeny na platformě Azure a služby funkce dostupné v době zápisu. Funkce a možnosti v průběhu času měnit. Ne všechna doporučení může být možné použít pro vaše nasazení, vyberte to, co vám vyhovuje.


## <a name="before-migration"></a>Před migrací 

Předtím, než můžete svoje úlohy přesunout do cloudu, odhadněte svoje měsíční náklady spuštěných v Azure. Aktivně spravovat náklady na cloud pomáhá dodržovat rozpočtu provozní výdaje (provozních nákladů). Jestliže rozpočet je omezené, vzít v úvahu před migrací.  Vezměte v úvahu převod úlohy do Azure technologiích bez serverů, kde je to vhodné, abyste snížili náklady.

Osvědčené postupy v této části umožňují odhadnout náklady, provést určení správné velikosti pro virtuální počítače a úložiště, využívat výhody Azure Hybrid, použití rezervovaných virtuálních počítačů a odhadnout cloudové výdaje napříč předplatnými.



## <a name="best-practice-estimate-monthly-workload-costs"></a>Osvědčený postup: Odhadnout měsíční náklady na úlohy
 
Pro předpověď svoje měsíční náklady pro přenášená zatížení jsou různé nástroje, které můžete použít.

- **Cenovou kalkulačku Azure**: Zvolíte produkty, které chcete odhadnout, například virtuální počítače a úložiště. Vstup náklady na cenové kalkulačky, sestavit odhad.

 ![Cenovou kalkulačku Azure](./media/migrate-best-practices-costs/pricing.png) *cenovou kalkulačku Azure*

- **Azure Migrate**: Pokud chcete odhadnout náklady, budete muset zkontrolovat a účet pro všechny prostředky, které jsou potřeba ke spouštění úloh v Azure. K získání těchto dat, vytváření inventáře majetku, včetně serverů, virtuálních počítačů, databází a úložiště. Azure Migrate můžete použít ke shromažďování těchto informací.

 - Azure Migrate zjistí a vyhodnocuje vaše místní prostředí pro poskytnutí inventář.
 - Azure Migrate můžete namapovat a zobrazit závislosti mezi virtuálními počítači, abyste měli ucelený přehled.
 - Posouzení služby Azure Migrate obsahuje odhadované náklady.
    - Náklady na výpočetní výkon: Pomocí velikost virtuálního počítače Azure, doporučuje se, když vytvoříte posouzení, Azure Migrate používá rozhraní API pro fakturaci pro výpočet odhadované měsíční náklady na virtuální počítač. Odhad bere v úvahu operační systém, program software assurance, rezervované instance, virtuálního počítače doby provozu, umístění a nastavení měny. Agreguje náklady ve všech virtuálních počítačích v posouzení a vypočítá celkové měsíční náklady výpočetní prostředky.
    - Náklady na úložiště: Azure Migrate vypočítá agregováním náklady na úložiště všechny virtuální počítače ve vyhodnocení celkové měsíční náklady na úložiště. Na základě agregace měsíční náklady pro všechny disky připojené k němu můžete vypočítat měsíční náklady na úložiště pro konkrétní počítač. 

    ![Azure Migrate](./media/migrate-best-practices-costs/assess.png) *posouzení služby Azure Migrate*

**Víc se uč:**
- [Použití](https://azure.microsoft.com/pricing/calculator/) cenovou kalkulačku Azure.
- [Získejte přehled](https://docs.microsoft.com/azure/migrate/migrate-overview) Azure Migrate.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation) vyhodnocení Azure Migrate.
- [Další informace](https://docs.microsoft.com/azure/dms/dms-overview) o Database Migration Service (DMS).

## <a name="best-practice-right-size-vms"></a>Osvědčený postup: Nastavení správné velikosti virtuálních počítačů

Když nasadíte virtuální počítače Azure k podpoře úloh můžete řadu možností. Každý typ virtuálního počítače má konkrétní funkce a různé kombinace procesoru, paměti a disky. Virtuální počítače jsou seskupené následujícím způsobem.

**Typ** | **Podrobnosti** | **Použití**
--- | --- | ---
**Obecné účely** | Vyvážený poměr procesorů k paměti. | Vhodné pro testování a vývoj, malé až střední databáze, s nízkým a středním provozem webových serverů.
**Optimalizované výpočetní prostředky** | Vysoký poměr procesorů k paměti. | Vhodné pro webový server středním provozem, síťová zařízení, dávkové procesy, serverů aplikací.
**Paměťově optimalizované** | Vysoké paměti na-využití procesoru. | Vhodné pro relační databáze, střední a velké mezipaměti a analýzu v paměti.
**Optimalizované z hlediska úložiště** | Vysoká propustnost disku a V/V. | Vhodné pro velké objemy dat, databáze SQL a NoSQL.
**Optimalizované z hlediska GPU** | Specializované virtuální počítače. Jeden nebo více grafickými procesory. | Silná grafiky a úpravy videa.
**Vysoký výkon** | Nejrychlejší a procesorově nejvýkonnější procesoru. Virtuální počítače s volitelné vysokou propustnost síťového rozhraní (RDMA) | Kritické vysoce výkonné aplikace.

- Je důležité pochopit rozdíly v cenách tyto virtuální počítače a dlouhodobé účinky rozpočtu.
- Každý typ má celou řadu virtuálních počítačů v rámci něj.
- Kromě toho při výběru virtuálního počítače v rámci řady, je možné pouze škálovat virtuálního počítače navýšení nebo snížení kapacity v rámci této řady. Například DSv2\_2 můžete vertikálně navýšit kapacitu na DSv2\_4, ale nelze změnit na jinou sérii například Fsv2\_2.

**Víc se uč:**
- [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) o velikostech mapování na typy a velikosti a typy virtuálních počítačů.
- [Plánování](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs) velikosti virtuálního počítače.
- [Kontrola](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment) posouzení ukázka pro fiktivní společnosti Contoso.

## <a name="best-practice-select-the-right-storage"></a>Osvědčený postup: Vyberte správné úložiště

Ladění a udržování místního úložiště (sítě SAN nebo NAS) a sítě pro podporu, může být drahá a časově náročné. Data souborů (úložiště) je obvykle migrovat do cloudu a vyřešit provozní a správu obrovskému. Společnost Microsoft poskytuje několik možností pro přesun dat do Azure a budete muset učinit rozhodnutí o těchto možnostech. Vybrat správný typ úložiště pro data můžete uložit vaše organizace několik tisíc dolarů měsíčně. Několik důležitých informací:

- Data, která nejsou přístupné a není důležité obchodní informace, nemusí být umístěny v úložišti nejdražší.
- Naopak důležité důležitých podnikových dat se musí nacházet na vyšší úroveň možností úložiště.
- Při plánování migrace proveďte inventarizaci dat a klasifikovat podle důležitosti, abyste mohli mapovat na nejvhodnějšího úložiště. Vezměte v úvahu rozpočtu a nákladů, jakož i výkonu. Náklady by neměl být nutně faktor hlavní rozhodování. Výběr možnosti nejlevnější může zpřístupnit úloh na výkon a dostupnost rizika. 

### <a name="storage-data-types"></a>Typy úložiště dat

Azure poskytuje různé typy dat úložiště.

**Datový typ** | **Podrobnosti** | **Použití** 
--- | --- |  ---
**Objekty blob** | Optimalizovaná pro ukládání velkých objemů nestrukturovaných objektů, jako jsou textová nebo binární data<br/><br/> | Přístup k datům odkudkoli přes protokol HTTP/HTTPS. | Používá se pro scénáře datových proudů a náhodný přístup. Například k předávání obrázků a dokumentů přímo do prohlížeče, streamování Vida a zvuku a ukládání dat pro obnovení zálohování a po havárii.
**Soubory** | Spravované sdílené složky přístupné přes protokol SMB 3.0 | Použít při migraci místních sdílených složek a k poskytování více/připojení k datům souborů.
**Disky** | Založené na objekty BLOB stránky.<br/><br/> Typ disku (rychlost): Standardní (pevný disk nebo SSD) nebo Premium (SSD).<br/><br/>Správa disků: Nespravované (můžete spravovat nastavení disku a úložiště) nebo spravované (vyberte typ disku a Azure spravuje za vás disk). | Použijte disky úrovně Premium pro virtuální počítače. Použití spravovaných disků pro jednoduchá správa a škálování.
**fronty** | Store a načítání velkého počtu zpráv, které jsou přístupné prostřednictvím ověřených volání (HTTP nebo HTTPS) | Připojení aplikace součástí pomocí asynchronních zpráv zařazení do fronty.
**Tabulky** | Store tabulky. | Nyní součástí rozhraní API tabulky Azure Cosmos DB.



### <a name="access-tiers"></a>Úrovně přístupu
Azure storage nabízí různé možnosti pro přístup k datům objektu blob bloku. Vyberte úroveň přístupu pomáhá zajistit, ukládání dat objektů blob bloku cenově nejvýhodnější způsobem.

**Typ** | **Podrobnosti** | **Použití**
--- | --- | ---
**za běhu** | Vyšší náklady než studené úložiště. Nižší poplatky za přístup než Cool.<br/><br/>Toto je výchozí úroveň. | Použijte pro data v aktivní použití, které se využívají často.
**Cool** | Nižší náklady na úložiště než horká. Vyšší poplatky za přístup než aktivní.<br/><br/> Store pro minimálně za 30 dní. | Krátkodobé na Store, data jsou k dispozici, ale jenom zřídka.
**Archiv** | Používá se pro objekty BLOB bloku jednotlivé.<br/><br/> Většina cenově výhodnější variantou při úložiště. Přístup k datům je nákladnější než horká a studená. | Používá se pro data, která se toleruje latence načtení server hodin a bude i nadále ve vrstvě nejméně 180 dnů. 

### <a name="storage-account-types"></a>Typy účtů úložiště

Azure poskytuje různé typy účtů úložiště a úrovně výkonu.

**Typ účtu** | **Podrobnosti** | **Použití**
--- | --- | ---
**Obecné účely v2 Standard** | Podporuje objekty BLOB (bloku, stránky, připojení), soubory, disky, fronty a tabulky.<br/><br/> Podporuje vrstvami přístupu Hot, Cool a Archive. ZRS se podporuje. | Používá se pro většinu scénářů a většinu typů data. Účty úložiště úrovně Standard může být HHD nebo založené na jednotkách SSD.
**Obecné účely v2 Premium** | Podporuje data objektů Blob storage (objekty BLOB stránky). Podporuje vrstvami přístupu Hot, Cool a Archive. ZRS se podporuje.<br/><br/> Uložené na SSD. | Microsoft doporučuje používat pro všechny virtuální počítače.
**Účty pro obecné účely v1** | Nepodporuje přístup vrstvení. Nepodporuje ZRS | Použijte, pokud aplikace potřebují modelu nasazení Azure classic.
**Objekt blob** | Specializovaný účet úložiště pro ukládání nestrukturovaných objektů. Poskytuje objekty BLOB bloku a doplňovacích objektů BLOB pouze (žádný soubor, fronty, tabulky nebo Disk úložiště služby). Poskytuje stejnou odolnost, dostupnost, škálovatelnost a výkon jako obecné účely v2. | v těchto účtech nejde ukládat objekty BLOB stránky a proto Nejde uložit soubory virtuálního pevného disku. Můžete nastavit na horkou nebo studenou úroveň přístupu.

### <a name="storage-redundancy-options"></a>Možnosti redundance služby Storage

Účty úložiště můžete použít různé typy redundance pro odolnost a vysoká dostupnost.

**Typ** | **Podrobnosti** | **Použití**
--- | --- | ---
**Místně redundantní úložiště (LRS)** | Chrání proti místní výpadek replikací v rámci jednoho úložiště jednotky doména samostatné selhání a aktualizační doména. Udržuje několik kopií vašich dat v jednom datacentru. Poskytuje alespoň 99,999999999 % (11 9\'s) odolnosti objektů v průběhu daného roku. | Zvažte, pokud aplikace ukládá data, která můžou být snadno znovu vytvořena.
**Zónově redundantní úložiště (ZRS)** | Chrání znovu k výpadku datového centra pomocí replikace mezi clustery tři úložiště v jedné oblasti. Každý cluster úložiště je fyzicky oddělené a je umístěn ve své vlastní zónu dostupnosti. Poskytuje alespoň 99,9999999999 % (12 9\'s) odolnosti objektů v průběhu daného roku díky uchovávání několika kopií dat napříč více datových center nebo oblastech. | Zvažte, pokud potřebujete konzistenci, odolnost a vysokou dostupnost. Nemusí ochranu proti regionálního při více zón se trvale vliv.
**Geograficky redundantní úložiště (GRS)** | Replikuje data do sekundární oblasti vzdálené stovky mil od primární chrání před výpadku celé oblasti. Poskytuje alespoň 99,99999999999999 % (16 9\'s) odolnosti objektů v průběhu daného roku. | Data repliky není k dispozici, pokud Microsoft nezahájí převzetí služeb při selhání do sekundární oblasti. Pokud dojde k převzetí služeb při selhání, je k dispozici oprávnění ke čtení a zápisu.
**Geograficky redundantní úložiště jen pro čtení (RA-GRS)** | Podobně jako u GRS. Poskytuje alespoň 99,99999999999999 % (16 9\'s) odolnosti objektů v průběhu daného roku | Poskytuje a 99,99 % dostupnosti čtení tím, že povolíte přístup pro čtení z druhé oblasti používané pro geograficky redundantní úložiště.

**Víc se uč:**
- [Kontrola](https://azure.microsoft.com/pricing/details/storage/) ceny za Azure Storage.
- [Další informace o](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Azure Import/Export pro migraci velkého objemu dat v objektech BLOB Azure a soubory. 
- [Porovnání](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) objekty BLOB, soubory a disku úložiště datové typy.
- [Další informace](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) o úrovně přístupu.
- [Kontrola](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) různé typy účtů úložiště.
- Další informace o [redundance úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy), [LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), [GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), a [Přístupem jen pro čtení](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage).
- [Další informace](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) o službě soubory Azure.



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>Osvědčený postup: Využívat výhody Azure Hybrid

Z důvodu let softwaru investice do systémů, jako jsou Windows Server a SQL Server Microsoft je jedinečné pozici nabízet zákazníkům hodnotu v cloudu pomocí podstatné slevy, které nutně neposkytuje jiných poskytovatelů cloudových služeb. 

Integrované portfolio typu místní nebo Azure produktu Microsoft generuje výhody konkurenceschopnost a nákladů. Pokud máte aktuálně operačního systému nebo jinými nabídkami licencování softwaru prostřednictvím programu software assurance (SA), můžete využít tyto licence s vámi do cloudu s programem Azure Hybrid Benefit.

**Víc se uč:**

- [Podívejte se na](https://azure.microsoft.com/pricing/hybrid-benefit/) Kalkulačka úspor Hybrid Benefit.
- [Další informace](https://azure.microsoft.com/pricing/hybrid-benefit/) o Hybrid Benefit pro Windows Server.
- [Kontrola](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance#bring-your-own-license-byol) ceny pokyny pro virtuální počítače Azure s SQL serverem.


## <a name="best-practice-use-reserved-vm-instances"></a>Osvědčený postup: Použití rezervovaných instancí virtuálních počítačů

Většina cloudových platformách jsou nastaveny jako s průběžnými platbami. Tento model představuje nevýhody, protože zatím nevíte nutně dynamické úlohy budou. Při zadávání vymazat záměry úloh můžete přispět k plánování infrastruktury.

Pomocí služby Azure Reserved VM instances, si Předplatíte jeden nebo tři roky instance virtuálního počítače. 

- Zálohy poskytuje a uplatnit tak slevu na prostředky, které používáte.
- Virtuální počítač, výpočetní databáze SQL, Azure Cosmos DB nebo další náklady na prostředky může výrazně snížit až o 72 % oproti průběžným platbám. 
- Rezervace poskytovat fakturační slevy a neovlivní jejich běhový stav vašich prostředků.
- Rezervované instance můžete zrušit.

![Rezervované instance](./media/migrate-best-practices-costs/reserve.png)
*vyhrazené virtuální počítače Azure*

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) Azure rezervace.
- [Čtení](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq) rezervované instance – nejčastější dotazy.
- [Získejte doprovodné materiály k cenám](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance#bring-your-own-license-byol) pro virtuální počítače Azure s SQL serverem.


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>Osvědčený postup: Útrata v cloudu agregované napříč předplatnými

Je nevyhnutelné, že nakonec budete mít více než jedno předplatné Azure. Například můžete potřebovat další předplatné k oddělení vývoje a provozu hranice, nebo můžete mít platformu, která vyžaduje samostatné předplatné pro každého klienta. Možnost agregovaných dat, vytváření sestav napříč všemi předplatnými do jedné platformy je důležité funkce.

K tomuto účelu můžete použít Azure Cost Management API. Potom po agregace dat do jednoho zdroje jako Azure SQL, můžete použít nástroje, jako je Power BI k poskytování agregovaná data. Můžete vytvořit sestavy agregované předplatného a podrobné sestavy. Například pro uživatele, kteří potřebují proaktivní přehled o náklady na správu, vytvoříte konkrétní zobrazení náklady podle oddělení, skupina prostředků atd. Není nutné jim poskytnout úplný přístup k Azure fakturačních údajů.

**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview) rozhraní Azure Consumption API.
- [Další informace o](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights) připojení k Azure Consumption Insights v Power BI Desktopu.
- [Zjistěte, jak](https://docs.microsoft.com/azure/billing/billing-manage-access) spravovat přístup k fakturačních údajů pro Azure pomocí řízení přístupu na základě role (RBAC).
 
## <a name="after-migration"></a>Po migraci

Po úspěšné migraci úloh a pár týdnů, shromažďovat data o spotřebě budete mít jasnou představu o nákladech na prostředky.
- Jak je můžete analyzovat data, můžete začít generovat rozpočtu směrný plán pro skupiny prostředků Azure a prostředky.
- Poté jak porozumíte, kde je využita rozpočtu cloudu, můžete analyzovat jak snížit náklady a další.

Osvědčené postupy v této části zahrnují náklady na plánování rozpočtu a analýz, monitorování prostředků a provádění rozpočty skupiny prostředků a optimalizaci monitorování úložiště a virtuálních počítačů pomocí Azure Cost Management.

## <a name="best-practice-use-azure-cost-management"></a>Osvědčený postup: Použití Azure Cost Management

Společnost Microsoft poskytuje Azure Cost Management můžete sledovat výdaje, následujícím způsobem:

- Umožňuje sledovat a řídit útraty Azure a optimalizovat využití prostředků.
- Revize, celý předplatného a všechny její prostředky a poskytuje doporučení.
- Poskytuje úplné rozhraní API, integrace externích nástrojů a finančních systémů pro vytváření sestav.
- Sleduje využití prostředků a spravovat náklady na cloud pomocí jednoho unifikovaného zobrazení.
- Obsahuje provozní a finanční informace, které vám pomůže přijímat podložená rozhodnutí.

Ve službě Cost Management můžete:


- **Vytvořit rozpočet**: Vytvoření rozpočet pro finanční zodpovědnost.
    - Můžete účet pro služby využívat nebo přihlášení k odběru pro konkrétní období (měsíčně, čtvrtletně, pořizují na rok) a oboru (předplatných nebo skupinách prostředků). Například můžete vytvořit rozpočtu předplatné Azure pro každý měsíc, čtvrtletí nebo roční období.
    - Po vytvoření rozpočtu, se zobrazí v analýze nákladů. Zobrazení vašemu rozpočtu na aktuální útratu je jedním z prvních kroků při analýze nákladů a výdajů.
    - E-mailová oznámení lze odesílat, když se dosáhne prahové hodnoty.
    - Náklady na správu dat můžete exportovat do úložiště Azure pro účely analýzy.

    ![Cost Management rozpočtu](./media/migrate-best-practices-costs/budget.png) *rozpočet Azure Cost Management*

- **Proveďte analýzu nákladů**: Získat analýzy nákladů můžete zkoumat a analyzovat organizační náklady, které vám pomohou pochopit, jak jsou náklady na operace a odhalovat trendy výdajů.
    - Analýza nákladů je k dispozici uživatelům EA.
    - Můžete zobrazit náklady na analýzu dat pro celou řadou oborů, třeba tak, že oddělení, účet, předplatné nebo skupinu prostředků.
    - Můžete získat analýzy nákladů, který ukazuje celkové náklady pro aktuální měsíc a celkové denní náklady. 

    ![Analýza správy nákladů](./media/migrate-best-practices-costs/analysis.png) *analýzy Azure Cost Management*
- **Získejte doporučení**: Získejte doporučení Advisoru, které ukazují, jak můžete optimalizovat a zvýšení efektivity.


**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/cost-management/overview) služby Azure Cost Management.
- [Zjistěte, jak](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices) optimalizovat vaše cloudové investice ve službě Azure Cost Management.
- [Zjistěte, jak](https://docs.microsoft.com/azure/cost-management/use-reports) pomocí sestav Azure Cost Management.
- [Získejte kurz](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json) na optimalizaci nákladů od doporučení.
- [Kontrola](https://docs.microsoft.com/rest/api/consumption/budgets) konektoru Azure Consumption API.

## <a name="best-practice-monitor-resource-utilization"></a>Osvědčený postup: Monitorování využití prostředků

V Azure platíte za co použijete, když se spotřebovávají prostředky a není platíte, když nejsou. Pro virtuální počítače fakturace dochází, když je přidělen virtuálnímu počítači a se vám neúčtují poplatky po zrušení přidělení virtuálního počítače. S myslete na to by měl monitorovat virtuální počítače v použití a ověřte nastavení velikosti virtuálního počítače.

- Průběžně vyhodnoťte úloh virtuálních počítačů k určení směrné plány.
- Například pokud vaše úloha je použít silně od pondělí do pátku, 8: 00 do 18: 00, ale téměř použít mimo tyto hodiny, může snížit virtuálních počítačů mimo špičky. To může znamenat, změna velikosti virtuálního počítače nebo použijete virtuálního počítače škálovací sady do virtuálních počítačů automatického škálování směrem nahoru nebo dolů.
- Některé společnosti "připomenout znovu", virtuální počítače tak, že je vložíte v kalendáři, který určuje, kdy by měla být k dispozici, a pokud nejsou potřeba. 
- Kromě monitorování virtuálních počítačů, měli byste sledovat další síťové prostředky, jako je například ExpressRoute a brány virtuální sítě pro v a využití.
- Můžete monitorovat využití virtuálních počítačů s počtem nástroje Microsoftu včetně Azure Cost Management, Azure Monitor a Azure Advisoru. Nástroje třetích stran jsou také k dispozici.  

**Víc se uč:**
- Získejte přehled o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) a [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-overview).
- [Získat](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) nákladů doporučení Advisoru.
- [Další informace jak [optimalizovat náklady od doporučení](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json), a [vám nenaúčtovaly neočekávané poplatky](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started).
- [Další informace o](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/) Toolkit (ARO) optimalizace prostředků Azure

## <a name="best-practice-implement-resource-group-budgets"></a>Osvědčený postup: Implementace rozpočty skupiny prostředků

Skupiny prostředků se často používají k reprezentaci hranice náklady. Společně se tento vzor využití pokračuje v týmu Azure pro vývoj nové a vylepšené způsoby, jak sledovat a analyzovat prostředky na různých úrovních, včetně možnosti vytvořit rozpočet na skupinu prostředků a prostředky.  

- Rozpočet skupiny prostředků umožňuje sledovat náklady spojené s skupinu prostředků.
- Můžete aktivovat výstrahy a pracovat širokou škálu playbooky je dosáhli nebo Přesáhli jste rozpočtu. 

**Víc se uč:**

- [Zjistěte, jak](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) Správa nákladů s rozpočty Azure.
- [Postupujte podle kurzu](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json) můžete vytvářet a spravovat Azure rozpočtu.


## <a name="best-practice-optimize-azure-monitor-retention"></a>Osvědčený postup: Optimalizace uchovávání Azure Monitor

Při přesunu prostředků do Azure a povolení protokolování diagnostiky pro ně se vygeneroval velké množství dat protokolu. Tato data protokolu se obvykle odešle na účet úložiště, který je namapovaný na pracovní prostor Log Analytics.

- Čím delší dobu uchování dat protokolů, čím více dat budete mít.
- Ne všechna data protokolu se rovná a několik zdrojů informací, vygeneruje další data protokolu než jiné.
- Z důvodu dodržování předpisů a nařízení je pravděpodobné, že bude nutné zachovat data protokolu pro několik zdrojů informací, který je delší než jiné.
- Pozor, řádek by měl procházet mezi optimalizovat náklady na úložiště vašich protokolů a ponecháním dat protokolu, které potřebujete.
- Doporučujeme, abyste vaše rozhodnutí vyzkoušet a nastavení protokolování okamžitě po dokončení migrace, takže nejsou útraty peníze uchování protokolů žádný význam.

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs) monitorování využití a odhadované náklady.
 
## <a name="best-practice-optimize-storage"></a>Osvědčený postup: Optimalizace úložiště

Pokud jste postupovali podle osvědčené postupy pro výběr úložiště před migrací, jsou pravděpodobně využívat některé výhody. Existují ale náklady pravděpodobně další úložiště, které můžete dál optimalizovat. V čase soubory a objekty BLOB jsou pak zastaralá. Data se možná nepoužívají už ale zákonných požadavků může znamenat, že je potřeba zachovat určitou dobu. V důsledku toho nemusí musíte uložit na vysoce výkonné úložiště, který jste použili pro původní migrace.

Identifikace a přesunutím zastaralá data do oblastí levnější úložiště můžete mít velký dopad na vaše měsíční úložiště rozpočtu a úspory nákladů. Azure poskytuje mnoho způsobů, jak vám pomohou identifikovat a poté uložit tato data zastaralá.

- Využijte výhod úrovně přístupu pro úložiště pro obecné účely v2, přesun méně důležitá data z horké úrovně do úrovně Cool a archivace.
- Pomocí StorSimple přesunout zastaralých dat podle vlastních zásad. 

**Víc se uč:**
- [Další informace](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) o úrovně přístupu.
- [Získejte přehled](https://docs.microsoft.com/azure/azure-monitor/overview) storsimple, a [ceny za StorSimple](https://azure.microsoft.com/pricing/details/storsimple/).

## <a name="best-practice-automate-vm-optimization"></a>Osvědčený postup: Automatizace optimalizace virtuálních počítačů

Konečným cílem spuštění virtuálního počítače v cloudu je pro maximalizaci procesoru, paměti a disku, který ji používá. Pokud zjišťování virtuálních počítačů, které nejsou optimalizovaná nebo máte časté období, kdy nejsou používány virtuálními počítači, je vhodné je vypnout, nebo snížit je pomocí škálovací sady virtuálních počítačů.

Můžete optimalizovat virtuální počítač s Azure Automation, škálovací sady virtuálních počítačů, automatické vypnutí a řešení skriptované nebo 3. stran. 

**Víc se uč:**

- [Zjistěte, jak](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision) použít vertikální automatické škálování.
- [Plán](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/) automatické spuštění virtuálního počítače.
- [Zjistěte, jak](https://docs.microsoft.com/azure/automation/automation-solution-vm-management) spuštěním a zastavením virtuálních počítačů mimo špičku ve službě Azure Automation.
- [Další informace získáte] [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-overview)a [sada nástrojů optimalizace prostředků Azure (ARO)](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/).

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>Osvědčené postupy: Pomocí rozhraní API rozpočty Logic Apps a sady runbook

Azure poskytuje rozhraní REST API, který má přístup k fakturačním údajům vašeho tenanta.

- Integrace externích systémů a pracovní postupy, které jsou aktivovány metriky, které vytvoříte z dat rozhraní API můžete použít rozpočty rozhraní API.
- Do nástroje pro vaše preferované datové analýzy, si můžete vyžádat data o využití a prostředků.
- Rozhraní API využití a ceníku prostředků Azure vám pomohou přesně odhadnout a spravovat vaše náklady.
- Rozhraní API se implementují jako poskytovatele prostředků a jsou zahrnuty v rozhraní API pomocí Azure Resource Manageru.
- Rozhraní API rozpočty je možné integrovat s Azure Logic Apps a sady Runbook.

**Víc se uč:**

- [Další informace](https://docs.microsoft.com/rest/api/consumption/budgets) o rozhraní API rozpočtů.
- [Přehledné znázornění](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) do využití Azure pomocí rozhraní API pro fakturaci.


## <a name="best-practice-implement-serverless-technologies"></a>Osvědčený postup: Implementace technologiích bez serverů

Úlohy virtuálních počítačů se migrují často "tak jak jsou" výpadky. Často virtuálních počítačů může hostovat úlohy, které jsou k nim dochází přerušovaně, s ohledem na krátkou dobu pro spuštění, nebo můžete také mnoho hodin. Například virtuální počítače, na kterých běží naplánované úlohy, jako je například Windows úloh plánovače nebo skripty prostředí PowerShell. Pokud tyto úlohy nejsou spuštěné, jste však zajistit plynulý provoz virtuálního počítače a náklady na úložiště na disku.

Po dokončení migrace po důkladné revizi z těchto typů úloh zvažte migraci na technologiích bez serverů, jako je například úlohy Azure Functions nebo Azure Batch. Pomocí tohoto řešení už nepotřebujete pro správu a údržbu virtuálních počítačů, přináší další náklady na ukládání. 

**Víc se uč:**
- [Další informace o](https://azure.microsoft.com/services/functions/) Azure Functions
- [Další informace o](https://azure.microsoft.com/en-us/services/batch/) Azure Batch
  
## <a name="next-steps"></a>Další postup 

Přečtěte si doporučené postupy:

- [Osvědčené postupy](migrate-best-practices-security-management.md) pro zabezpečení a správu po migraci.
- [Osvědčené postupy](migrate-best-practices-networking.md) sítě po migraci.

