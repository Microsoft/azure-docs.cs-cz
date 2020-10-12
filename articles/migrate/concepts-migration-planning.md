---
title: Sestavit plán migrace s Azure Migrate | Microsoft Docs
description: Poskytuje pokyny k vytvoření plánu migrace pomocí Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: db1de363856fd560fea97f8f9cdf542717c4cca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090089"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Sestavení plánu migrace s využitím Azure Migrate

Tento článek poskytuje stručnou příručku, která vám pomůže vytvořit plán migrace do Azure pomocí [Azure Migrate](migrate-services-overview.md).Pokud máte další otázky, ověřte tyto prostředky:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- Dotazy týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Dotazy týkající se [migrace serveru](common-questions-server-migration.md)
- Získání otázek zodpovězených ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="define-the-goals-of-cloud-migration"></a>Definování cílů migrace do cloudu

Než začnete s plánem migrace, je důležité pochopit a vyhodnotit [motivaci](/azure/cloud-adoption-framework/strategy/motivations) , která vám umožní přejít na Cloud, který vám pomůže dosáhnout více úspěšných obchodních výsledků. V [rámci architektury pro přijetí do cloudu pro Azure](/azure/cloud-adoption-framework) se může jednat o různé triggery a přístupy k migraci, které jsou vhodné pro vaši firmu:  

**Kritické obchodní události** | **Výsledek migrace**
--- | ---
Ukončení Datacenter | Úspora nákladů
Fúze, akvizice nebo divestiture | Snížení pro dodavatele nebo technickou složitost
Snížení nákladů na velká písmena | Optimalizace interních operací
Konec podpory pro klíčové technologie | Zvýšení flexibility firmy
Reakce na změny dodržování předpisů v legislativě | Příprava na nové technické možnosti
Nové požadavky na svrchovanost dat | Škálování na splnění požadavků na trh
Omezení výpadků a zlepšení stability IT | Škálování pro splnění geografických požadavků

Motivace migrace vám také může porážet strategické cíle a výsledky, které byste chtěli dosáhnout migrací do Azure. Dalším krokem je identifikace a naplánování cesty migrace do Azure, která je přizpůsobená pro vaše úlohy. Azure Migrate: Nástroj pro vyhodnocení serveru vám pomůže posoudit místní úlohy a poskytuje pokyny a nástroje, které vám pomůžou s migrací.

## <a name="understand-your-digital-estate"></a>Pochopení vaší digitální nemovitosti

Začněte s porozuměním vaší místní infrastruktuře, aplikacemi a závislostmi, které vám pomůžou identifikovat úlohy, které chcete migrovat do Azure, a získejte optimalizované projekce nákladů. Nástroj Server Assessment Tool vám pomůže odpovědět na následující otázky:

### <a name="what-workloads-are-in-use"></a>Jaké úlohy se používají?

Zařízení s odlehčeným Azure Migrate slouží k provádění zjišťování místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických serverů v agentech. Průběžné zjišťování shromažďuje údaje o konfiguraci počítače a výkonu a dá se použít k získání inventáře nainstalovaných aplikací a rolí a funkcí spuštěných na místních počítačích. Zařízení Azure Migrate shromažďuje:

- Podrobnosti o metadatech počítačů, disků a síťových karet

- Nainstalované aplikace včetně aplikací a rolí/funkcí  

- Údaje o výkonu, včetně využití CPU a paměti, IOPS disku a propustnosti

Potom exportujte seznam inventáře aplikací a zjistěte, jaké instance SQL Server běží na vašich úlohách, a použijte nástroj Azure Migrate: Nástroj pro vyhodnocení databáze pro pochopení jejich připravenosti.

 ![Inventář aplikací na portálu](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export inventáře aplikací](./media/concepts-migration-planning/application-inventory-export.png)

Společně s daty zjišťování z nástroje pro vyhodnocení serveru můžete pomocí svých stávajících dat CMDB sestavovat naše zobrazení vašeho serveru a databáze a porozumět distribuci serveru napříč obchodními jednotkami, vlastníky aplikací, geografickými oblastmi atd., což může pomáhat při určování priorit úloh, které se mají migrovat.

### <a name="what-dependencies-exist-between-workloads"></a>Jaké závislosti mezi úlohami existují?

Po zjištění vašich serverů použijte mapování závislostí bez agenta k vizualizaci a identifikaci závislostí mezi servery a optimalizačních strategií pro přesun vzájemně závislých serverů do Azure. Tato vizualizace pomáhá pochopit, jestli jsou některé počítače používané, nebo jestli je možné je vyřadit z provozu místo migrace.  Ujistěte se, že analyzujete závislosti a zajistěte, aby nic nezůstalo a nedocházelo k výpadkům během migrace. Jakmile provedete mapování inventáře aplikací a závislostí, můžete vytvořit vysoce spolehlivé skupiny a začít vyhodnotit vaše servery.

 ![Mapování závislostí](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Jsou optimalizované a správně velikosti?

Jelikož Azure poskytuje flexibilitu při změně velikosti cloudové kapacity v průběhu času, migrace je příležitostí k optimalizaci prostředků procesoru a paměti přidělených vašim serverům. Pro skupinu, kterou jste identifikovali, vytvořte posouzení, abyste porozuměli historii výkonu úloh, které se v Azure budou velmi zásadní v doporučeních SKU virtuálních počítačů a disků.

## <a name="assess-your-readiness-for-migration"></a>Posouzení připravenosti na migraci

### <a name="readiness-and-suitability-analysis-for-azure"></a>Připravenost a analýza vhodnosti pro Azure
Vyexportujte sestavu posouzení virtuálních počítačů Azure a vyfiltrujte tyto kategorie připravenosti, abyste pochopili připravenost virtuálních počítačů pro Azure:

- **Připraveno pro Azure**: tyto počítače můžete migrovat tak, jak jsou, do Azure bez jakýchkoli změn.  

- **Podmíněně připravené pro Azure**: tyto počítače můžete migrovat do Azure, ale na těchto serverech budete potřebovat menší změny, a to podle pokynů k nápravě poskytovaných v rámci posouzení.

- **Nepřipraveno pro Azure**: tyto počítače nemůžete migrovat do Azure tak, jak jsou, a je potřeba opravit problémy podle pokynů k nápravě před migrací.

- **Připravenost je neznámá**: Azure Migrate není schopen určit připravenost počítače z důvodu nedostatečných metadat.

Pomocí vyhodnocení databáze můžete vyhodnotit připravenost pro migraci SQL Serverch dat do Azure SQL Database nebo spravovaných instancí Azure SQL. Pro každou instanci SQL serveru vidíte procento stavu připravenosti migrace. U každé z těchto instancí si taky můžete prohlédnout doporučený cíl v Azure, potenciální blokující migrace, počet přerušujících změn, připravenost pro Azure SQL DB/Azure SQL VM a úroveň kompatibility. Můžete Dig hlouběji a pochopit dopad migračních bloků a doporučení, aby je bylo možné opravit.

 ![Vyhodnocení databáze](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Doporučení pro změnu velikosti

Jakmile je počítač označený jako připravený pro Azure, vyhodnocování serveru vytvoří doporučení pro změnu velikosti, která identifikují virtuální počítač Azure a diskovou jednotku pro vaše virtuální počítače. Můžete se rozhodnout pro zobrazení doporučení na změnu velikosti na základě historie výkonu (takže optimalizujete prostředky při migraci) nebo na základě jejich místní konfigurace bez zvážení historie výkonu. V případě databází můžete v vyhodnocení databáze zobrazit doporučení týkající se SKU databáze, cenové úrovně a úrovně výpočtů.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Výpočetní vyhodnocení, které vám umožní získat odhadované náklady na spouštění úloh v Azure

Možnost správného určení velikosti *na základě výkonu* v hodnoceních vám umožní optimalizovat úlohy pro Azure. Kromě snižování existuje několik dalších cesty vedoucí, které vám pomůžou ušetřit náklady:

- **Rezervované instance**: u rezervovaných instancí můžete významně snížit náklady ve srovnání s cenami za průběžné platby, a to s 1 nebo 3 roky ve Windows a Linux (virtuálních počítačích).

- **Výhody hybridního využití Azure**: můžete přenést místní licence Windows serveru se Software Assurance do Azure a zkombinovat je s možnostmi rezervovaných instancí.

- **Smlouva Enterprise nabídka (EA)**: smlouva Enterprise nabízí integrované úspory platné pro vaše předplatné.

- **Nabídky**: existuje několik nabídek Azure, například Průběžné platby dle aktuálního využití pro vývoj/testování a Enterprise pro vývoj/testování, které poskytují nižší sazby pro vývojové nebo testovací virtuální počítače.

- **Doba provozu virtuálního počítače**: můžete uvést dobu ve dnech měsíčně a hodinách za den, kdy se virtuální počítače Azure spustí, aby se snížily náklady (neplatí pro rezervované instance).

- **Cílová oblast**: můžete vytvořit několik posouzení v různých oblastech, abyste mohli porovnat, jestli je možné migrovat do určité oblasti v geograficky úsporném prostředí.

- **Doporučení na základě výkonu**: osvědčeným postupem je zkusit použít práva na virtuální počítače Azure, která vám pomůžou ušetřit náklady na Cloud.

### <a name="visualize-data"></a>Vizualizace dat

Na portálu můžete zobrazit sestavu posouzení serveru s připraveností na Azure a měsíční distribuci nákladů a také vyexportovat hodnocení, které umožňuje použít další vizualizace pro data zjišťování a hodnocení, aby bylo možné plán migrace podrobnější. Můžete vytvořit více posouzení pro různé kombinace vlastností a zvolit sadu vlastností, které jsou pro vaši firmu nejvhodnější.  

 ![Přehled hodnocení](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Vyhodnotit mezery a potenciální blokování

Při určování aplikací a základní infrastruktury pro migraci určete omezení výpadků pro tyto aplikace a vyhledejte jakékoli provozní závislosti mezi vašimi aplikacemi a základní infrastrukturou. Tato analýza vám může přispět k plánování migrace, které splňují váš cíl času obnovení (RTO), a zajišťují minimální ztrátu dat. Před migrací doporučujeme zkontrolovat a zmírnit problémy s kompatibilitou nebo nepodporované funkce, které mohou blokovat migraci serverů a databází SQL pomocí sestavy posouzení serveru a Azure Migrate: doporučení pro vyhodnocení databáze.

### <a name="first-workloads-to-target-and-approach"></a>První úlohy zaměřené na cílení a přístup

Teď, když máte k dispozici všechny zásadní informace o tom, jak provést migraci, byste měli určit, které aplikace a úlohy by se měly migrovat jako první. Vývoj "Apply" (Apply) a Naučte se používat k migraci vašich zamýšlených aplikací systematickým a dobře ovladatelném způsobem, aby bylo možné před započetím migrace na celé škálování vyfiltrovat všechny nedostatky v této strategii. Můžete také použít strategické faktory, jako je například složitost a čas pro migraci, obchodní naléhavost, produkční/neprodukční prostředí, dodržování předpisů a požadavky na zabezpečení, znalosti aplikací atd., a určit tak prioritu, které skupiny aplikací se mají migrovat.

Několik doporučených strategií migrace:

- **Stanovení priorit pro rychlou službu WINS**: sestavy hodnocení můžete použít k identifikaci neúplných plodů, včetně serverů a databází, které jsou plně připravené a vyžadují minimální úsilí při migraci do Azure:
    - Připraveno pro Azure: exportujte sestavu posouzení a vyfiltrujte všechny počítače, které jsou připravené pro Azure. Může to být vaše první skupina počítačů, které můžete nazvednutím a posunutím pomocí nástroje Azure Migrate: Server pro migraci.
    - Konec podpory OS: exportujte sestavu posouzení a filtrujte všechny počítače, na kterých běží operační systémy Windows Server 2008 a Windows Server 2008 R2. Tyto SKU jsou na konci podpory a jenom Azure vám poskytuje zdarma 3 roky aktualizací zabezpečení, když je migrujete do Azure. Při kombinaci Zvýhodněné hybridní využití Azure a používání rezervovaných instancí může být ukládání mnohem vyšší.
    - Migrace SQL Server: pomocí doporučení pro vyhodnocení databáze migrujte databáze připravené pro databáze SQL Azure pomocí Azure Migrate: migrace databáze a databáze připravené pro virtuální počítač Azure SQL pomocí služby Azure Migrate: Migrace serveru.
    - Software na konci podpory: Exportujte inventář aplikací a filtrujte libovolný software nebo rozšíření, která by mohla dosáhnout konce podpory. Tyto aplikace byste měli upřednostňovat.
    - Předem zřízené virtuální počítače: exportujte sestavu posouzení a vyfiltrujte počítače s nízkým využitím procesoru (%). a využití paměti (%).  Tuto příležitost můžete použít k migraci na virtuální počítač s právy v Azure a k uložení toho, co jste se zaplaceni za nevyužité prostředky.
    - Omezení kapacity: exportujte sestavu posouzení a vyfiltrujte počítače s vysokým využitím procesoru (%) a využití paměti (%).  Omezenému počtu virtuálních počítačů můžete zabránit v narušení a zvýšení výkonu jejich migrací do Azure a využití možnosti automatického škálování pro splnění požadavků. Můžete se také podívat na sestavu posouzení, která vám pomůže pochopit omezení úložiště tím, že analyzuje vstupně-výstupní operace disku a propustnost a vyhledá doporučený typ disku, který bude nejlépe vyhovovat vašim potřebám.

- **Začněte s malým objemem a pak se zajděte na velké**: Začněte s přesunutím aplikací a úloh, které jsou minimálním rizikem a méně složité, k sestavování důvěry v strategii migrace. K vyhledání a migraci úloh vývojového a testovacího prostředí ve vašich pilotních migracích můžete také pokládat doporučení pro vyhodnocení Azure Migrate v úložišti CMDB vaší organizace. Výukové materiály z těchto pilotních pilotů se dají použít při migraci produkčních úloh.  

- **Splnění vašich regulativních a průmyslových požadavků**: Azure udržuje největší portfolio dodržování předpisů v celém odvětví, a to z hlediska šířky a hloubky nabídek. Využijte tuto možnost k určení priorit migrace do Azure a dodržování vnitrostátních, regionálních a standardních standardů a zákonů pro konkrétní obory. To platí zejména pro organizace, které se týkají důležitých podnikových nebo blokovaných citlivých informací nebo jsou v silně regulovaných odvětvích, kde se standardy a předpisy Abound a v některých případech často mění, což ztěžuje zachování.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Dokončení plánu migrace a příprava na migraci

Před dokončením plánu migrace zajistěte, aby tyto klíčové předpoklady migrace nehrály překážku pro plánování migrace:

- Vyhodnoťte omezení šířky pásma sítě a latence, což může způsobit nepředvídatelné prodlevy a narušit rychlost replikace migrace.

- Vyrovnávací paměť k provedení testování výkonu a testování přijetí uživateli na migrovaných aplikacích nebo provádění libovolných aplikací po migraci, jako je například aktualizace připojovacích řetězců databáze a konfigurací webového serveru, provádění přímou migraci a čištění atd.

- Zkontrolujte doporučená oprávnění Azure a role serveru/databáze přístupu a model oprávnění potřebné pro migraci.

- Připravte svoji organizaci a zajistěte, aby se zaměstnanci mohli přizpůsobovat digitální transformaci. Základní nadace pro školení je důležitá pro úspěšnou změnu organizace. Podívejte se na bezplatné školení dostupné na [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), včetně kurzů pro základy Azure, architekturu řešení a zabezpečení. Požádejte tým, aby prozkoumal i [certifikaci Azure](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   .  

- V případě potřeby získáte podporu pro vaši implementaci. Mnoho organizací si pro podporu migrace do cloudu vyžádá externí pomoc. Pokud chcete rychle a bez obav přejít na Azure s přizpůsobenou asistencí, vezměte v úvahu [poskytovatele spravované služby Azure expert](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   nebo [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Vytvořte účinný plán migrace do cloudu, který obsahuje podrobné informace o seznamu nebo skupinách aplikací, které chcete migrovat, omezení dostupnosti vaší aplikace a databáze a o požadované milníky migrace. Plán migrace by měl také počítat s tím, jak dlouho bude kopírování dat trvat, a zahrnovat considerate vyrovnávací paměť pro testování po migraci a aktivity přímou migraci. Testování po migraci musí zahrnovat případy použití funkce, integrace, zabezpečení a testování výkonu, aby migrované aplikace fungovaly podle očekávání a všechny databázové objekty a relace dat byly úspěšně přeneseny do cloudu.  

Pomocí této analýzy můžete sestavit plán migrace a deklarovat okno údržby pro migraci vašich aplikací a databází s minimálním počtem nulových výpadků a omezením potenciálního provozního nebo podnikového dopadu při migraci.  

Než začnete s nasazením úplného škálování do Azure, doporučujeme, abyste vždycky otestovali a pokračovali pomocí možnosti *migrace testu* Azure Migrate. Tato skutečná data vám pomůžou odhadnout skutečný čas a udělat si potřebné vylepšení plánu migrace. Testovací migrace také nabízí možnost zjistit případné problémy s plánem migrace a opravit je před tím, než proběhne skutečná migrace.  

Až budete připraveni k migraci, použijte *Nástroj pro migraci serveru* Azure Migrate a *službu migrace dat* Azure Migrate na bezproblémové a integrované prostředí migrace s koncovým sledováním. Nástroj pro migraci serveru podporuje migraci virtuálních počítačů a serverů hostovaných místně v datacentru zákazníků nebo v jakémkoli jiném privátním nebo veřejném cloudu, včetně AWS, GCP atd. s nulovými výpadky. Azure Database Migration Service je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky.  

> [!NOTE]
> U virtuálních počítačů VMware používá posouzení serveru operační systém, který je zadaný pro virtuální počítač v vCenter Server pro zpracování analýzy hostovaného operačního systému. Pro virtuální počítače se systémem Linux běžící na VMware aktuálně neidentifikuje přesnou verzi jádra hostovaného operačního systému.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [cestu k migraci do cloudu](/azure/architecture/cloud-adoption/getting-started/migrate)   v rozhraní Azure cloudu pro přijetí.
- [Začínáme](https://youtu.be/wFfq3YPxYHE) s Azure Migrate.
- Vytvořte posouzení pro [virtuální počítače VMware](tutorial-assess-vmware.md) nebo [virtuální počítače Hyper-V](tutorial-assess-hyper-v.md).
