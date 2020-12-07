---
title: Sestavení plánu migrace pomocí Azure Migrate
description: Poskytuje pokyny k vytvoření plánu migrace pomocí Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ef916e0e8b32c96382a731d4a307e2b2a98ba1ea
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753854"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Sestavení plánu migrace s využitím Azure Migrate

Při sestavování plánu migrace do Azure pomocí [Azure Migrate](migrate-services-overview.md)postupujte podle tohoto článku. 

## <a name="define-cloud-migration-goals"></a>Definování cílů migrace do cloudu

Než začnete, seznámení a vyhodnocení vaší [motivace](/azure/cloud-adoption-framework/strategy/motivations) pro přechod do cloudu může přispět k úspěšnému obchodnímu výsledku. Jak je vysvětleno v tématu [architektura pro přijetí do cloudu](/azure/cloud-adoption-framework), je k dispozici řada triggerů a výsledků.   

**Obchodní událost** | **Výsledek migrace**
--- | ---
Ukončení Datacenter | Náklady 
Fúze, akvizice nebo divestiture | Snížení od dodavatele a technické složitosti
Snížení nákladů na velká písmena | Optimalizace interních operací
Konec podpory pro klíčové technologie | Zvýšení flexibility firmy
Reakce na změny dodržování předpisů v legislativě | Příprava na nové technické možnosti
Nové požadavky na svrchovanost dat | Škálování na splnění požadavků na trh
Snížení výpadků a vylepšení stability IT | Škálování pro splnění geografických požadavků

Identifikace vaší motivace vám pomůže připnout vaše strategické cíle migrace. V dalším kroku se identifikujte a naplánujete cestu migrace, která se přizpůsobí vašim úlohám. [Azure Migrate: Nástroj pro vyhodnocení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool) vám pomůže vyhodnotit místní úlohy a poskytuje pokyny a nástroje, které vám pomůžou s migrací.

## <a name="understand-your-digital-estate"></a>Pochopení vaší digitální nemovitosti

Začněte tím, že identifikujete místní infrastrukturu, aplikace a závislosti. To vám pomůže identifikovat úlohy pro migraci do Azure a shromažďovat optimalizované projekce nákladů. Nástroj pro vyhodnocení serveru vám pomůže identifikovat úlohy, které používáte, závislosti mezi úlohami a optimalizací úloh.

### <a name="workloads-in-use"></a>Používané úlohy

Azure Migrate využívá odlehčené Azure Migrate zařízení k provádění zjišťování místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, dalších virtualizovaných počítačů a fyzických serverů pomocí bez agenta. Průběžné zjišťování shromažďuje informace o konfiguraci počítače a metadata o výkonu a také data aplikací. Toto zařízení shromáždí z místních počítačů: 

- Metadata počítačů, disků a síťových adaptérů.

- Nainstalované aplikace, role a funkce.

- Údaje o výkonu, včetně využití procesoru a paměti, vstupně-výstupních operací disku a propustnosti.

Po shromáždění dat můžete exportovat seznam inventáře aplikací a vyhledat aplikace a SQL Server instancí spuštěných na vašich počítačích. K pochopení SQL Server připravenosti můžete použít nástroj Azure Migrate: vyhodnocení databáze.

 ![Inventář aplikací na portálu](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export inventáře aplikací](./media/concepts-migration-planning/application-inventory-export.png)

Společně s daty zjištěnými pomocí nástroje pro vyhodnocení serveru můžete použít data databáze správy konfigurace (CMDB) k sestavení zobrazení serveru a databáze a porozumět tomu, jak jsou vaše servery distribuované napříč obchodními jednotkami, vlastníky aplikací, geografickými oblastmi atd. To pomáhá určit, které úlohy se mají upřednostnit při migraci. 

### <a name="dependencies-between-workloads"></a>Závislosti mezi úlohami

Po zjištění serveru můžete [analyzovat závislosti, analyzovat](concepts-dependency-visualization.md)a identifikovat závislosti mezi servery a strategie optimalizace pro přesun vzájemně závislých serverů do Azure. Tato vizualizace pomáhá pochopit, jestli se konkrétní počítače používají, nebo jestli je možné je vyřadit z provozu místo migrace.  Analýza závislostí pomáhá zajistit, že nic není v provozu a že během migrace dojde k neočekávanému výpadku. Díky inventarizaci vašich aplikací a analýzám závislostí můžete vytvořit vysoce spolehlivé skupiny serverů a začít je vyhodnotit.

 ![Mapování závislostí](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Optimalizace a změna velikosti

Azure poskytuje flexibilitu při změně velikosti cloudové kapacity v průběhu času a migrace nabízí možnost optimalizace prostředků procesoru a paměti přidělených vašim serverům. Vytvoření posouzení na serverech, které máte v identity, vám pomůže pochopit historii výkonu vašich úloh. To je důležité pro správnou změnu velikosti skladových položek virtuálních počítačů Azure a doporučení pro disky v Azure.

## <a name="assess-migration-readiness"></a>Posouzení připravenosti na migraci


### <a name="readinesssuitability-analysis"></a>Analýza připravenosti/vhodnosti

Sestavu posouzení můžete vyexportovat a vyfiltrovat podle těchto kategorií, abyste pochopili připravenost na Azure:

- **Připraveno pro Azure**: počítače se dají migrovat tak, jak jsou, do Azure bez jakýchkoli změn. 
- **Podmíněně připravené pro Azure**: počítače se dají migrovat do Azure, ale potřebují menší změny v souladu s pokyny k nápravě, které jsou uvedené v posouzení.
- **Nepřipraveno pro Azure**: počítače nejde migrovat do Azure tak, jak jsou. Před migrací musí být problémy vyřešené v souladu s pokyny k nápravě. 
- **Připravenost neznámá**: Azure Migrate nemůže určit připravenost počítače z důvodu nedostatečných metadat.

Pomocí vyhodnocení databáze můžete vyhodnotit připravenost SQL Serverch dat pro migraci na Azure SQL Database nebo na spravované instance Azure SQL. Posouzení znázorňuje procento stavu připravenosti migrace pro každou instanci SQL serveru. U každé instance můžete navíc zobrazit doporučený cíl v Azure, potenciální blokující migrace, počet nezměněných změn, připravenost pro Azure SQL DB nebo Azure SQL VM a úroveň kompatibility. Můžete Dig hlouběji a pochopit dopad migračních bloků a doporučení pro jejich opravu.

 ![Vyhodnocení databáze](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Doporučení pro změnu velikosti

Když je počítač označený jako připravený pro Azure, vyhodnocování serveru vytvoří doporučení pro změnu velikosti, která identifikují SKU virtuálního počítače Azure a typ disku pro vaše počítače. Můžete získat doporučení pro velikost na základě historie výkonu (k optimalizaci prostředků při migraci) nebo na základě místních nastavení počítače bez historie výkonu. V rámci vyhodnocení databáze můžete zobrazit doporučení pro SKU databáze, cenovou úroveň a úroveň výpočtů.  

### <a name="get-compute-costs"></a>Získat výpočetní náklady

Možnost změny velikosti na základě výkonu v Azure Migrate hodnocení vám pomůže s virtuálními počítači správné velikosti a měla by se používat jako osvědčený postup pro optimalizaci úloh v Azure. Kromě správného určení velikosti je k dispozici několik dalších možností, které vám pomůžou ušetřit náklady na Azure: 

- **Rezervované instance**: s [rezervovanými instancemi (ri)](https://azure.microsoft.com/pricing/reserved-vm-instances/)můžete významně snížit náklady v porovnání s [cenami za](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)průběžné platby.
- **Zvýhodněné hybridní využití Azure**: s [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)můžete přenést místní licence k Windows serveru s aktivním softwarem Software Assurance nebo předplatným systému Linux, do Azure a kombinovat s možnostmi rezervovaných instancí.
- **Smlouva Enterprise**: [smlouvy Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) můžou nabízet úspory pro služby a předplatná Azure.
- **Nabídky**: existuje několik [nabídek Azure](https://azure.microsoft.com/support/legal/offer-details/). Například [průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/pricing/dev-test/)nebo [Enterprise pro vývoj/testování nabídku](https://azure.microsoft.com/offers/ms-azr-0148p/)pro zajištění nižších sazeb pro virtuální počítače pro vývoj a testování
- **Doba provozu virtuálního počítače**: můžete zkontrolovat dny za měsíc a hodiny za den, kdy se virtuální počítače Azure spouštějí. Vypnutí počítačů, když se nepoužívají, může snížit vaše náklady (neplatí pro služby RIs).
- **Cílová oblast**: můžete vytvořit posouzení v různých oblastech, abyste zjistili, jestli může být migrace do konkrétní oblasti cenově výhodnější. 

### <a name="visualize-data"></a>Vizualizace dat

Na portálu můžete zobrazit sestavy posouzení serveru (s informacemi o připravenosti na Azure a rozdělení měsíčních nákladů). Hodnocení můžete také exportovat a rozšířit svůj plán migrace pomocí dalších vizualizací. Můžete vytvořit více hodnocení s různými kombinacemi vlastností a zvolit sadu vlastností, které jsou pro vaši firmu nejvhodnější.  

 ![Přehled hodnocení](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Vyhodnotit mezery/blokování

Jak se podíváte na aplikace a úlohy, které chcete migrovat, identifikujte pro ně omezení výpadku a vyhledáte všechny provozní závislosti mezi vašimi aplikacemi a základní infrastrukturou. Tato analýza vám pomůže naplánovat migrace, které vyhovují vašemu cíli doby obnovení (RTO), a zajistěte minimální ztrátu dat. Před migrací doporučujeme zkontrolovat a zmírnit problémy s kompatibilitou nebo nepodporované funkce, které mohou blokovat migraci serveru nebo databáze SQL. K tomu může pomáhat Azure Migrate sestava posouzení serveru a Azure Migrate vyhodnocení databáze. 

### <a name="prioritize-workloads"></a>Stanovení priorit úloh

Po shromáždění informací o inventáři můžete určit, které aplikace a úlohy se mají migrovat jako první. Vytvořte přístup "použít a naučit" a migrujte aplikace systematicky a ovladatelné způsobem, abyste před zahájením migrace v plném rozsahu mohli odpravit všechny chyby.

Pokud chcete určit prioritu pořadí migrace, můžete použít strategické faktory, jako je složitost, doba migrace, obchodní naléhavost, provozní/neprodukční požadavky, dodržování předpisů, požadavky na zabezpečení, znalosti aplikací atd. 

Několik doporučení:

- Určení **priorit rychlé služby WINS**: pomocí sestav hodnocení Identifikujte neúplné ovoce, včetně serverů a databází, které jsou plně připravené a vyžadují minimální úsilí při migraci do Azure. Tato tabulka shrnuje několik způsobů, jak to provést.

    **Státech** | **Akce**
    --- | ---
    **Virtuální počítače připravené pro Azure** | Exportujte sestavu posouzení a vyfiltrujte všechny počítače se stavem *připraveným pro Azure*. Může se jednat o první skupinu počítačů, které získáte a přesunete do Azure, pomocí nástroje [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **Operační systémy ukončení podpory** | Exportujte sestavu posouzení a filtrujte všechny počítače se systémem Windows Server 2008 R2/Windows Server 2008. Tyto operační systémy jsou na konci podpory a jenom Azure poskytuje zdarma tři roky aktualizací zabezpečení, když je migrujete do Azure. Pokud kombinujete Zvýhodněné hybridní využití Azure a použijete služby vzdálené instalace, může být úspory mnohem vyšší.
    **Migrace SQL Server** | Použijte doporučení pro vyhodnocení databáze k migraci databází, které jsou připravené k Azure SQL Database, pomocí nástroje Azure Migrate: Database Migration Tool. Migrujte databáze připravené pro virtuální počítač Azure SQL pomocí nástroje Azure Migrate: Server pro migraci.
    **Software na konci podpory** | Exportujte inventář aplikací a vyfiltrujte veškerý software a rozšíření, která se můžou dostat do konce podpory. Nastavte prioritu těchto aplikací pro migraci.
    **Místně zřízené počítače** | Exportujte sestavu posouzení a vyfiltrujte počítače s nízkým využitím procesoru (%) a využití paměti (%).  Migrujte na virtuální počítač Azure ve správné velikosti a ušetříte náklady na nevyužité prostředky.
    **Předem zřízené počítače** | Exportovat sestavu posouzení a vyfiltrovat pro počítače s vysokým využitím procesoru (%) a využití paměti (%).  Vyřešte omezení kapacity, Zabraňte poškození přetížení počítačů a zvyšte výkon migrací těchto počítačů do Azure. V Azure Využijte možnosti automatického škálování pro splnění požadavků.<br/><br/> Analyzujte sestavy hodnocení a prozkoumejte omezení úložiště. Analyzujte vstupně-výstupní operace disku a propustnost a doporučený typ disku.

- **Začněte malým a pak se zajděte na velké**: Začněte přesunutím aplikací a úloh, které představují minimální riziko a složitost, a Sestavujte důvěru v strategii migrace. Analyzujte doporučení pro vyhodnocení Azure Migrate společně s úložištěm CMDB, abyste našli a migrovali úlohy pro vývoj a testování, které mohou být kandidáty na pilotní migrace. Zpětná vazba a učení z pilotních migrací můžou být užitečné při zahájení migrace produkčních úloh.  
- V **souladu** s: Azure udržuje největší portfolio dodržování předpisů v oboru, a to z hlediska šířky a hloubky nabídek. Použijte požadavky na dodržování předpisů k určení priorit migrace, aby aplikace a úlohy splňovaly vaše národní, regionální a standardní standardy a zákony. To platí hlavně pro organizace, které zabývají podnikovým procesem, uchovávají citlivé informace nebo jsou v silně regulovaných odvětvích. V těchto typech organizací, standardů a předpisů Abound a se mohou často měnit, takže je obtížné je udržet.  

## <a name="finalize-the-migration-plan"></a>Finalizace plánu migrace

Před dokončením plánu migrace se ujistěte, že máte v úvahu a zmírnit další možné blokování, a to takto: 

- **Požadavky na síť**: vyhodnoťte omezení šířky pásma sítě a latence, což může způsobit nepředvídatelné zpoždění a přerušení rychlosti replikace migrace.
- **Vylepšení testování/po migraci**: umožňuje, aby časová vyrovnávací paměť prováděla testování přijetí a schvalování uživatelů pro migrované aplikace, nebo aby se nakonfigurovali/neprováděla následná migrace aplikací, jako je třeba aktualizace připojovacích řetězců databáze, konfigurace webových serverů, provádění vyjmutí nebo vyčištění atd.
- **Oprávnění**: Zkontrolujte doporučená oprávnění Azure a role serveru/databáze přístupu a oprávnění potřebná k migraci.
- **Školení**: Připravte svoji organizaci na digitální transformaci. Základní nadace pro školení je důležitá pro úspěšnou změnu organizace. Projděte si bezplatné školení na [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), včetně kurzů pro základy Azure, architektury řešení a zabezpečení. Doporučte vašemu týmu prozkoumat [certifikace Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF).  
- **Podpora implementace**: Získejte podporu pro vaši implementaci, pokud ji potřebujete. Mnoho organizací si pro podporu migrace do cloudu vyžádá externí pomoc. Pokud chcete rychle a bez obav přejít na Azure s přizpůsobenou asistencí, berte v úvahu [poskytovatele spravované služby Azure expert](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)nebo [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Vytvořte účinný plán migrace do cloudu, který obsahuje podrobné informace o aplikacích, které chcete migrovat, dostupnosti aplikace nebo databáze, omezeních výpadků a milníky migrace. Plán posuzuje, jak dlouho bude trvat kopírování dat, a zahrnuje realistickou vyrovnávací paměť pro testování po migraci a aktivity vyjmuté z provozu. 

Plán testování po migraci by měl zahrnovat funkce pro testování funkčnosti, integrace, zabezpečení a výkonu a k zajištění toho, aby migrované aplikace fungovaly podle očekávání a aby všechny databázové objekty a datové vztahy byly úspěšně přeneseny do cloudu.  

Sestavte plán migrace a deklarujete okno údržby pro migraci vašich aplikací a databází s minimálním počtem nulových výpadků a omezte potenciální provozní a obchodní dopad na migraci.  

## <a name="migrate"></a>Migrace

Před zahájením migrace v plném rozsahu doporučujeme spustit migraci testů v Azure Migrate. Testovací migrace vám pomůže odhadnout čas a upravit plán migrace. Nabízí příležitost zjistit případné problémy a opravit je před úplnou migrací.

Až budete připraveni na migraci, použijte nástroj Azure Migrate: Server pro migraci a službu Azure Data Migration Service (DMS) pro bezproblémové a integrované prostředí migrace s komplexním sledováním.

- Pomocí nástroje pro migraci serveru můžete migrovat místní virtuální počítače a servery nebo virtuální počítače umístěné v jiném privátním nebo veřejném cloudu (včetně AWS, GCP) bez výpadků.
- Azure DMS poskytuje plně spravovanou službu, která je navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky.  

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [cestu k migraci do cloudu](/azure/architecture/cloud-adoption/getting-started/migrate)   v rozhraní Azure cloudu pro přijetí.
- Získejte [rychlý přehled](migrate-services-overview.md) o Azure Migrate a podívejte se na [video Začínáme](https://youtu.be/wFfq3YPxYHE).
- Přečtěte si další informace o vyhodnocování virtuálních počítačů pro migraci na [virtuální počítače Azure](concepts-assessment-calculation.md).
