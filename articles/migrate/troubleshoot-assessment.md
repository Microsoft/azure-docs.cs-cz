---
title: Řešení potíží s vizualizací a vizualizací závislostí v Azure Migrate
description: Získejte pomoc s vizualizací a vizualizací závislostí v Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863594"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Řešení potíží s vyhodnocením a vizualizací závislostí

Tento článek vám pomůže řešit problémy s vizualizací a vizualizací závislostí s [Azure Migrate: zjišťování a posouzení](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problémy s připraveností na posouzení

Opravte problémy připravenosti vyhodnocování následujícím způsobem:

**Problém** | **Oprava**
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštění EFI. Před spuštěním migrace doporučujeme převést typ spouštění na systém BIOS. <br/><br/>K obsluze migrace takových virtuálních počítačů můžete použít migraci Azure Migrate serveru. Při migraci převede typ spouštění virtuálního počítače na systém BIOS.
Podmíněně podporované operační systémy Windows | Operační systém předal své datum ukončení podpory a pro [podporu v Azure](/troubleshoot/azure/virtual-machines/server-software-support)potřebuje vlastní smlouvu o podpoře (CSA). Před migrací do Azure zvažte možnost upgradovat. Zkontrolujte informace o [přípravě serverů s Windows serverem 2003](prepare-windows-server-2003-migration.md) pro migraci do Azure.
Nepodporovaný operační systém Windows | Azure podporuje jenom [vybrané verze operačního systému Windows](/troubleshoot/azure/virtual-machines/server-software-support). Před migrací na Azure zvažte upgrade serveru.
Podmíněně schválené operační systémy Linux | Azure schválí jenom [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md). Před migrací na Azure zvažte upgrade serveru. Další informace najdete také [tady](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) .
Neschválený operační systém Linux | Server se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. Před migrací do Azure zvažte možnost upgradovat na [schválenou verzi systému Linux](../virtual-machines/linux/endorsed-distros.md) .
Neznámý operační systém | Operační systém virtuálního počítače byl ve vCenter Server zadán jako jiný. Toto chování blokuje Azure Migrate Ověření připravenosti na Azure pro virtuální počítač. Před migrací serveru se ujistěte, že Azure [podporuje](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) operační systém.
Nepodporovaná bitová verze | Virtuální počítače s 32 operačním systémem se můžou spouštět v Azure, ale před migrací do Azure doporučujeme upgradovat na 64.
Vyžaduje předplatné Microsoft Visual Studio. | Na serveru běží klientský operační systém Windows, který je podporovaný jenom prostřednictvím předplatného sady Visual Studio.
Pro požadovaný výkon úložiště se nenašel virtuální počítač. | Výkon úložiště (vstupně-výstupní operace za sekundu [IOPS] a propustnost) požadovaná pro server překračuje podporu virtuálních počítačů Azure. Snižte požadavky na úložiště serveru před migrací.
Pro požadovaný výkon sítě se nenašel virtuální počítač. | Výkon sítě (v/v) vyžadovaný pro server překračuje podporu virtuálních počítačů Azure. Snižte požadavky na síť pro server.
Virtuální počítač se nenašel v zadaném umístění. | Před migrací použijte jiné cílové umístění.
Jeden nebo více nevhodných disků | Jeden nebo více disků připojených k virtuálnímu počítači nesplňuje požadavky Azure. Určitého<br/><br/> Azure Migrate: zjišťování a hodnocení aktuálně nepodporují SSD úrovně Ultra disky a posuzuje disky na základě limitů disku pro spravované disky úrovně Premium (32 TB).<br/><br/> U každého disku připojeného k virtuálnímu počítači se ujistěte, že je velikost disku < 64 TB (podporuje disky SSD úrovně Ultra).<br/><br/> Pokud ne, snižte velikost disku před migrací na Azure nebo použijte více disků v Azure a propojte [je dohromady](../virtual-machines/premium-storage-performance.md#disk-striping) , abyste dosáhli vyšších limitů úložiště. Ujistěte se, že [disky virtuálních počítačů spravované](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)službou Azure podporují výkon (IOPS a propustnost), které potřebuje každý disk.
Jeden nebo více nevhodných síťových adaptérů. | Odeberte nepoužívané síťové adaptéry ze serveru před migrací.
Počet disků překračuje limit. | Odstraňte nepoužívané disky ze serveru před migrací.
Velikost disku překračuje limit. | Azure Migrate: zjišťování a posouzení aktuálně nepodporují SSD úrovně Ultra disky a posuzuje disky na základě limitů disků Premium (32 TB).<br/><br/> Azure ale podporuje disky s velikostí až 64 TB (Podporované disky SSD úrovně Ultra). Zmenšete disky na méně než 64 TB před migrací nebo použijte více disků v Azure a propojte [je dohromady](../virtual-machines/premium-storage-performance.md#disk-striping) , abyste dosáhli vyšších limitů úložiště.
Disk není v zadaném umístění k dispozici. | Před migrací zajistěte, aby byl disk v cílovém umístění.
Disk není pro zadanou redundanci k dispozici. | Disk by měl používat typ úložiště redundance definovaný v nastavení hodnocení (ve výchozím nastavení LRS).
Nebylo možné určit vhodnost disku z důvodu vnitřní chyby. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Virtuální počítač s požadovanými jádry a pamětí se nenašel. | Azure nemohl najít vhodný typ virtuálního počítače. Před migrací Snižte velikost paměti a počtu jader místního serveru.
Kvůli vnitřní chybě se nepovedlo určit vhodnost virtuálního počítače. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc disků. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc síťových adaptérů. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Nenašla se žádná velikost virtuálního počítače pro rezervované instance nabídky Currency. | Server označený jako nevhodný, protože velikost virtuálního počítače se nenašla pro vybranou kombinaci rezervované instance, nabídky a měny. Upravte vlastnosti posouzení pro výběr platných kombinací a přepočítejte hodnocení. 
Podmíněně připravené Internet Protocol | Platí jenom pro posouzení řešení Azure VMware (AVS). Služba AVS nepodporuje faktor internetových adres IPv6. Pokud je server detekován s protokolem IPv6, obraťte se na tým služby AVS, kde najdete pokyny k nápravě.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Navrhovaný Nástroj pro migraci v hodnocení AVS založeném na importu, který je označený jako neznámý

U serverů importovaných prostřednictvím souboru CSV je výchozí nástroj pro migraci v nástroji a hodnocení služby AVS neznámý. U serverů v prostředí VMware se ale doporučuje použít řešení VMware Hybrid Cloud Extension (HCX). [Další informace](../azure-vmware/tutorial-deploy-vmware-hcx.md)

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Virtuální počítače se systémem Linux jsou podmíněně připravené ve službě Azure VM Assessment

V případě virtuálních počítačů VMware a Hyper-V vyhodnocování virtuálních počítačů Azure označí virtuální počítače se systémem Linux jako "podmíněně připravené", a to kvůli známému termínu. 

- Tato mezera brání tomu, aby zjistila podverzi operačního systému Linux nainstalovaného na místních virtuálních počítačích.
- Například pro RHEL 6,10 aktuálně vyhodnocování virtuálních počítačů Azure detekuje jako verzi operačního systému jenom RHEL 6. Důvodem je to, že vCenter Server ar hostitele Hyper-V neposkytuje verzi jádra pro operační systémy Linux virtuálních počítačů.
-  Vzhledem k tomu, že Azure připravuje pouze konkrétní verze systému Linux, virtuální počítače se systémem Linux jsou aktuálně označeny příznakem podmíněně připravené ve službě Azure VM Assessment.
- Můžete zjistit, jestli operační systém Linux běžící na místním virtuálním počítači je v Azure schválený, a to kontrolou [podpory Azure Linux](../virtual-machines/linux/endorsed-distros.md).
-  Po ověření schválené distribuce můžete toto upozornění ignorovat.

Tuto mezeru můžete vyřešit povolením [zjišťování aplikací](./how-to-discover-applications.md) na virtuálních počítačích VMware. Posouzení virtuálního počítače Azure používá k dispozici operační systém zjištěný z virtuálního počítače pomocí zadaných přihlašovacích údajů hosta. Tato data operačního systému identifikují správné informace o operačním systému v případě virtuálních počítačů se systémem Windows i Linux.

## <a name="operating-system-version-not-available"></a>Verze operačního systému není k dispozici.

Pro fyzické servery by měly být k dispozici informace o dílčí verzi operačního systému. Pokud není k dispozici, obraťte se na podpora Microsoftu. Pro servery v prostředí VMware používá Azure Migrate informace o operačním systému zadané pro virtuální počítač v vCenter Server. VCenter Server ale neposkytuje dílčí verzi pro operační systémy. Chcete-li zjistit dílčí verzi, je třeba nastavit funkci [zjišťování aplikací](./how-to-discover-applications.md). Pro virtuální počítače Hyper-V není zjišťování dílčí verze operačního systému podporované. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Skladové jednotky Azure větší než místní ve službě Azure VM Assessment

Posouzení virtuálního počítače Azure může doporučit skladové položky virtuálních počítačů Azure s více jádry a pamětí než aktuální místní přidělení na základě typu posouzení:

- Doporučení SKU virtuálního počítače závisí na vlastnostech posouzení.
- To je ovlivněno typem hodnocení, které provádíte ve službě Azure VM assessment: na *základě výkonu* nebo *jako v* místním prostředí.
- Pro vyhodnocení na základě výkonu považuje Azure VM Assessment data o využití místních virtuálních počítačů (CPU, paměť, disk a síť) k určení správné cílové SKU virtuálního počítače pro vaše místní virtuální počítače. Přináší také faktor komfortu při určování efektivního využití.
- Pro místní velikost se data o výkonu neberou v úvahu a cílová skladová jednotka se doporučuje na základě místního přidělení.

Pokud chcete vidět, jak to může ovlivnit doporučení, Podívejme se na příklad:

Máme místní virtuální počítač se čtyřmi jádry a 8 GB paměti s 50% využitím procesoru a 50% využití paměti a stanovený faktor komfortu 1,3.

-  Pokud je posouzení **místní**, doporučuje se skladová položka virtuálního počítače Azure se čtyřmi jádry a 8 GB paměti.
- Pokud je hodnocení založené na výkonu, na základě efektivního využití procesoru a paměti (50% ze 4 jader × 1,3 = 2,6 jader a 50% 8 GB paměti * 1,3 = 5,3-GB paměti), je doporučena SKU nejlevnější virtuálního počítače se čtyřmi jádry (nejbližší podporovaný počet jader) a 8 GB paměti (nejbližší podporovaná velikost paměti).
- [Přečtěte si další informace](concepts-assessment-calculation.md#types-of-assessments) o velikosti hodnocení.

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Proč se Doporučené skladové jednotky Azure ve vyhodnocování virtuálních počítačů Azure budou větší než v místním prostředí?

Posouzení virtuálního počítače Azure může doporučit větší disk na základě typu posouzení.
- Velikost disku závisí na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště.
- Pokud jsou kritéria změny velikosti **založená na výkonu** a typ úložiště je nastaven na hodnotu **automaticky**, při určení typu cílového disku (HDD úrovně Standard, SSD úrovně Standard nebo Premium) se bere v úvahu hodnota vstupně-výstupních operací a propustnosti disku. Pak se doporučuje disková jednotka disku z typu disku a doporučení posuzuje požadavky na velikost místního disku.
- Pokud jsou kritéria změny velikosti v **závislosti na výkonu** a typ úložiště je **Premium**, doporučuje se SKU na disku Premium v Azure na základě požadavků na vstupně-výstupní operace, propustnost a velikost na místním disku. Stejná logika se používá k provedení velikosti disku, pokud jsou kritéria změny velikosti **místní** a typ úložiště je **HDD úrovně Standard**, **SSD úrovně Standard** nebo **Premium**.

Příklad: Pokud máte místní disk s 32 GB paměti, ale agregované vstupně-výstupní operace čtení a zápisu pro disk je 800 IOPS, vyhodnocování virtuálních počítačů Azure doporučuje prémiový disk (kvůli vyššímu počtu požadavků IOPS) a potom doporučuje SKU disku, která může podporovat požadované vstupně-výstupní operace a velikost. Nejbližší shodou by v tomto případě byla skladová položka P15 (256 GB, 1 100 IOPS). I když velikost požadovaná místním diskem byla 32 GB, vyhodnocování virtuálních počítačů Azure doporučuje větší disk kvůli vysokému počtu požadavků na požadavky na místní disk.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Proč v sestavě posouzení chybí data o výkonu některých nebo všech virtuálních počítačů?

Pokud zařízení Azure Migrate nemůže shromáždit údaje o výkonu místních virtuálních počítačů, v sestavě posouzení na základě výkonu se zobrazí PercentageOfCoresUtilizedMissing nebo PercentageOfMemoryUtilizedMissing. Zkontrolujte následující:

- Byly virtuální počítače po dobu trvání, pro kterou vytváříte posouzení, zapnuté?
- Pokud chybí pouze čítače paměti a pokoušíte se posoudit virtuální počítače Hyper-V, zkontrolujte, jestli je na těchto virtuálních počítačích povolená dynamická paměť. V současné době existuje známý problém, kvůli kterému zařízení Azure Migrate nemůže shromáždit data o využití paměti takových virtuálních počítačů.
- Pokud chybí všechny čítače výkonu, zajistěte, aby byly splněny požadavky na přístup k portu pro posouzení. Přečtěte si další informace o požadavcích na přístup k portu pro [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) a [fyzické](./migrate-support-matrix-physical.md#port-access) posouzení.
Poznámka – Pokud některý z čítačů výkonu chybí, Azure Migrate: zjišťování a posouzení se vrátí do přidělených jader/paměti v místním prostředí a doporučuje odpovídající velikost virtuálního počítače.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Proč chybí data o výkonu pro některé nebo všechny servery v sestavě pro vyhodnocení virtuálních počítačů Azure a/nebo služby AVS?

Pro hodnocení "na základě výkonu" Export zprávy o vyhodnocení uvádí "PercentageOfCoresUtilizedMissing" nebo "PercentageOfMemoryUtilizedMissing", když zařízení Azure Migrate nemůže shromažďovat údaje o výkonu pro místní servery. Zkontrolujte následující:

- Pokud jsou servery zapnuté po dobu, po kterou vytváříte posouzení
- Pokud chybí pouze čítače paměti a pokoušíte se vyhodnocovat servery v prostředí technologie Hyper-V. V tomto scénáři Povolte prosím na serverech dynamickou paměť a přepočítejte hodnocení, aby odráželo nejnovější změny. Zařízení může shromažďovat hodnoty využití paměti pro servery v prostředí Hyper-V pouze v případě, že je na serveru povolená dynamická paměť.

- Pokud chybí všechny čítače výkonu, zajistěte, aby odchozí připojení na portech 443 (HTTPS) byla povolená.

    > [!Note]
    > Pokud některý z čítačů výkonu chybí, Azure Migrate: zjišťování a posouzení se vrátí do přidělených jader/paměti v místním prostředí a doporučuje odpovídající velikost virtuálního počítače.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Proč v Azure SQL Assessment chybí data výkonu pro některé/všechny instance nebo databáze SQL?

Pokud chcete zajistit, aby se shromažďovaly údaje o výkonu, zkontrolujte následující:

- Pokud jsou servery SQL zapnuté po dobu, po kterou vytváříte posouzení
- Pokud je stav připojení agenta SQL v Azure Migrate "připojeno" a zkontroluje poslední prezenční signál 
- Pokud Azure Migrate stav připojení pro všechny instance SQL je připojená v okně zjištěná instance SQL
- Pokud chybí všechny čítače výkonu, ujistěte se, že jsou povolená odchozí připojení na portu 443 (HTTPS).

Pokud některý z čítačů výkonu chybí, doporučí Azure SQL Assessment nejmenší konfiguraci Azure SQL pro tuto instanci nebo databázi.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Proč je míra spolehlivosti mého hodnocení nízká?

Míra spolehlivosti posouzení na základě výkonu se počítá na základě procentuální hodnoty [dostupných datových bodů](./concepts-assessment-calculation.md#ratings) potřebných k výpočtu posouzení. Níže jsou uvedené důvody, proč k hodnocení může být přidělená nízká míra spolehlivosti:

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Například pokud vytváříte posouzení s dobou výkonu nastavenou na jeden týden, budete muset počkat alespoň jeden týden po spuštění zjišťování, aby se shromáždily všechny datové body. Pokud nemůžete počkat na dobu trvání, změňte dobu trvání výkonu na menší období a **přepočítejte** hodnocení.
 
- Hodnocení nemůže shromáždit údaje o výkonu některých nebo všech serverů v daném období hodnocení. Pro hodnocení s vysokou mírou jistoty Prosím zajistěte: 
    - Servery jsou napájené po dobu trvání posouzení.
    - Odchozí připojení na portech 443 jsou povolená.
    - Pro servery Hyper-V je dynamická paměť povolená. 
    - Stav připojení agentů v Azure Migrate jsou "připojené" a zkontrolováno poslední prezenční signál
    - Pro vyhodnocení Azure SQL je stav připojení Azure Migrate pro všechny instance SQL "připojené" v okně zjištěná instance SQL.

    **Přepočítejte** hodnocení, aby se projevily poslední změny míry spolehlivosti.

- Pro vyhodnocení virtuálních počítačů a funkce AVS bylo po spuštění zjišťování vytvořeno několik serverů. Pokud například vytváříte vyhodnocení pro historii výkonu za poslední měsíc, ale několik serverů bylo vytvořeno v prostředí pouze před týdnem. V takovém případě nebudou údaje o výkonu pro nové servery k dispozici po celou dobu trvání a hodnocení spolehlivosti bude nízké. [Další informace](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Pro hodnocení Azure SQL se od spuštění zjišťování vytvořilo málo instancí nebo databází SQL. Pokud například vytváříte posouzení historie výkonu za poslední měsíc, ale v prostředí bylo vytvořeno několik instancí SQL nebo databází pouze před týdnem. V takovém případě nebudou údaje o výkonu pro nové servery k dispozici po celou dobu trvání a hodnocení spolehlivosti bude nízké. [Další informace](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Je licence k operačnímu systému zahrnutá v posouzení virtuálního počítače Azure?

Posouzení virtuálního počítače Azure aktuálně zvažuje náklady na licenci na operační systém jenom pro servery Windows. Náklady na licence pro servery Linux se momentálně nepovažují za.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Jak funguje Změna velikosti na základě výkonu v posouzení virtuálních počítačů Azure?

Azure VM Assessment průběžně shromažďuje data o výkonu místních serverů a používá je k tomu, aby v Azure doporučila SKU virtuálního počítače a SKU disku. [Přečtěte si, jak](concepts-assessment-calculation.md#calculate-sizing-performance-based) se shromažďují data založená na výkonu.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>Proč je moje hodnocení zobrazeno upozorněním, že bylo vytvořeno s neplatnou kombinací rezervovaných instancí, doba provozu a slevy virtuálního počítače (%)?
Když vyberete možnost rezervované instance, sleva (%) a vlastnosti pro dobu provozu virtuálního počítače nelze použít. Při vytváření hodnocení s neplatnou kombinací těchto vlastností jsou tlačítka upravit a přepočítat zakázaná. Vytvořte prosím nové posouzení. [Další informace](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nezobrazují se údaje o výkonu pro některé síťové adaptéry na mých fyzických serverech.

K tomu může dojít, pokud má fyzický server zapnutou virtualizaci Hyper-V. V důsledku Azure Migrate v současné době zjišťují fyzické i virtuální síťové adaptéry na těchto serverech. Propustnost sítě je zachycena pouze na zjištěných virtuálních síťových adaptérech.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>Doporučená skladová položka virtuálního počítače Azure pro můj fyzický server má větší velikost

K tomu může dojít, pokud má fyzický server zapnutou virtualizaci Hyper-V. Na těchto serverech Azure Migrate aktuálně zjišťují fyzické i virtuální síťové adaptéry. Proto ne. zjištěné síťové adaptéry jsou vyšší než skutečné. Když Azure VM Assessment vybere virtuální počítač Azure, který podporuje požadovaný počet síťových adaptérů, může to potenciálně způsobit, že dojde k virtuálnímu počítači se stejnou velikostí. [Přečtěte si další](./concepts-assessment-calculation.md#calculating-sizing) informace o dopadu ne. síťových adaptérů při změně velikosti. Jedná se o produktovou mezeru, která se bude řešit dál.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>Kategorie připravenosti "není připravena" pro fyzický server

Kategorie připravenosti v případě fyzického serveru s povolenou virtualizací technologie Hyper-V může být nesprávně označena jako nepřipravená. Na těchto serverech se kvůli přípravku produktu Azure Migrate v současné době zjišťují fyzické i virtuální adaptéry. Proto ne. zjištěné síťové adaptéry jsou vyšší než skutečné. V místním i vyhodnocování na základě výkonu virtuální počítač Azure vybírá virtuální počítač Azure, který může podporovat požadovaný počet síťových adaptérů. Pokud je zjištěno, že počet síťových adaptérů bude vyšší než 32, maximální počet. u síťových adaptérů, které jsou podporovány na virtuálních počítačích Azure, bude server označen jako "Nepřipraveno".  [Přečtěte si další](./concepts-assessment-calculation.md#calculating-sizing) informace o dopadu ne. Síťových adaptérů pro změnu velikosti.


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>Počet zjištěných síťových adaptérů vyšších než skutečný pro fyzické servery

K tomu může dojít, pokud má fyzický server zapnutou virtualizaci Hyper-V. Na těchto serverech Azure Migrate aktuálně zjišťují fyzické i virtuální adaptéry. Proto ne. zjištěné síťové karty jsou vyšší než skutečné.

## <a name="dependency-visualization-in-azure-government"></a>Vizualizace závislostí v Azure Government

Analýza závislostí založená na agentech není v Azure Government podporovaná. Použijte prosím analýzu závislostí bez agenta.


## <a name="dependencies-dont-show-after-agent-install"></a>Po instalaci agenta se nezobrazují závislosti

Po instalaci agentů Vizualizace závislostí na místní virtuální počítače Azure Migrate k zobrazení závislostí na portálu obvykle trvá 15-30 minut. Pokud jste čekali na více než 30 minut, ujistěte se, že Microsoft Monitoring Agent (MMA) se může připojit k pracovnímu prostoru Log Analytics.

Pro virtuální počítače s Windows:
1. V Ovládacích panelech spusťte MMA.
2. V **Microsoft Monitoring Agent vlastnosti**  >  **Azure Log Analytics (OMS)** zkontrolujte, že je **stav** pracovního prostoru zelený.
3. Pokud stav není zelený, zkuste odebrat pracovní prostor a znovu ho přidat do MMA.

    ![Stav MMA](./media/troubleshoot-assessment/mma-properties.png)

Pro virtuální počítače se systémem Linux se ujistěte, že instalační příkazy pro MMA a agenta závislostí byly úspěšné. Další pokyny k odstraňování potíží najdete [tady](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems"></a>Podporované operační systémy

- **Agent MMS**: Zkontrolujte podporované operační systémy [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)a [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) .
- **Agent závislostí**: podporované operační systémy [Windows a Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) .

## <a name="visualize-dependencies-for--1-hour"></a>Vizualizace závislostí pro > 1 hodinu

S analýzou závislostí bez agentů můžete vizualizovat závislosti nebo je exportovat v mapě po dobu až 30 dnů.

S analýzou závislostí na základě agenta, i když Azure Migrate umožňuje přejít zpět k určitému datu v posledním měsíci, maximální doba, po kterou je možné vizualizovat závislosti, je jedna hodina. Například můžete použít funkci časového trvání na mapě závislostí k zobrazení závislostí včera, ale můžete je zobrazit pouze v jednom hodinovém období. Můžete ale použít protokoly Azure Monitor k [dotazování dat závislosti](./how-to-create-group-machine-dependencies.md) v delší době trvání.

## <a name="visualized-dependencies-for--10-servers"></a>Vizuální závislosti pro servery > 10

V Azure Migrate s analýzou závislostí založenou na agentech můžete [vizualizovat závislosti pro skupiny](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) s až 10 virtuálními počítači. U větších skupin doporučujeme, abyste virtuální počítače rozdělili do menších skupin pro vizualizaci závislostí.


## <a name="servers-show-install-agent"></a>Servery zobrazují "instalovat agenta".

Po migraci serverů se zapnutou vizualizací závislostí na Azure se můžou servery zobrazit místo možnosti zobrazit závislosti v důsledku následujícího chování:

- Po migraci do Azure jsou místní servery vypnuté a odpovídající virtuální počítače se prosazuje v Azure. Tyto servery získají jinou adresu MAC.
- Servery mohou mít také jinou IP adresu na základě toho, zda jste zachovali místní IP adresu nebo ne.
- Pokud se adresy MAC i IP liší od místních, Azure Migrate nepřidruží místní servery k žádným Service Mapm datům závislostí. V takovém případě se zobrazí možnost nainstalovat agenta místo zobrazení závislostí.
- Po otestování migrace do Azure zůstanou místní servery zapnuté podle očekávání. Ekvivalentní servery propracované v Azure získají jinou adresu MAC a můžou získat různé IP adresy. Pokud neblokujete odchozí Azure Monitor přenosů protokolů z těchto serverů, Azure Migrate nebude přidružit místní servery k žádným Service Map datům závislostí, a proto se zobrazí možnost nainstalovat agenty, nikoli zobrazit závislosti.

## <a name="dependencies-export-csv-shows-unknown-process"></a>Při exportu sdílených svazků clusteru se zobrazí zpráva "Neznámý proces".
V rámci analýzy závislostí bez agenta se názvy procesů zachycují na základě nejlepšího úsilí. V některých případech, i když jsou zachyceny názvy zdrojového a cílového serveru a cílový port, není možné určit názvy procesů na obou koncích závislosti. V takových případech je proces označený jako "Neznámý proces".

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>Můj pracovní prostor Log Analytics není uveden při pokusu o konfiguraci pracovního prostoru v Azure Migrate
Azure Migrate v současné době podporuje vytvoření pracovního prostoru OMS v oblastech USA – východ, Jihovýchodní Asie a Evropa – západ. Pokud je pracovní prostor vytvořen mimo Azure Migrate v jakékoli jiné oblasti, nelze jej aktuálně přidružit k projektu.


## <a name="capture-network-traffic"></a>Zaznamenání síťového provozu

Shromážděte protokoly síťového provozu následujícím způsobem:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Stisknutím klávesy F12 spusťte Vývojářské nástroje. V případě potřeby zrušte zaškrtnutí  **políčka vymazat položky u nastavení navigace** .
3. Vyberte kartu **síť** a zahajte zachytávání síťového provozu:
   - V části Chrome vyberte **zachovat protokol**. Záznam by se měl spustit automaticky. Červený kroužek indikuje, že se zaznamenává provoz. Pokud se červené kolečko nezobrazí, vyberte černý kroužek, který chcete spustit.
   - V aplikaci Microsoft Edge a Internet Explorer by se měl záznam spustit automaticky. Pokud tomu tak není, vyberte zelené tlačítko Přehrát.
4. Zkuste reprodukování chyby.
5. Po zjištění chyby při nahrávání, zastavení záznamu a uložení kopie zaznamenané aktivity:
   - V Chrome klikněte pravým tlačítkem a vyberte **Uložit jako Har s obsahem**. Tato akce zkomprimuje a exportuje protokoly jako soubor. har.
   - V Microsoft Edge nebo Internet Exploreru vyberte možnost **exportovat zachycené přenosy** . Tato akce komprimuje a exportuje protokol.
6. Vyberte kartu **Konzola** a vyhledejte všechna upozornění a chyby. Uložení protokolu konzoly:
   - V prohlížeči Chrome klikněte pravým tlačítkem myši kdekoli v protokolu konzoly. Vyberte **Uložit jako**, exportovat a protokol zip.
   - V Microsoft Edge nebo Internet Exploreru klikněte pravým tlačítkem na chyby a vyberte **Kopírovat vše**.
7. Zavřete Vývojářské nástroje.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>Kde jsou v mém hodnocení zjištěna data operačního systému?

- Ve výchozím nastavení jsou pro virtuální počítače VMware data operačního systému poskytovaná vCenter. 
   - V případě virtuálních počítačů VMware Linux, pokud je povolené zjišťování aplikací, se podrobnosti o operačním systému načítají z virtuálního počítače hosta. Chcete-li zjistit, které podrobnosti o operačním systému v posouzení, přejdete na zobrazení zjištěné servery a nakurzorujte ukazatel myši na hodnotu ve sloupci operační systém. V textu, který se zobrazí, byste viděli, jestli se data operačního systému shromažďují z vCenter serveru, nebo z virtuálního počítače hosta pomocí přihlašovacích údajů k virtuálnímu počítači. 
   - V případě virtuálních počítačů s Windows se informace o operačním systému vždy načítají z vCenter Server.
- Pro virtuální počítače Hyper-V se data operačního systému shromažďují z hostitele Hyper-V.
- U fyzických serverů se načte ze serveru.

## <a name="next-steps"></a>Další kroky

[Vytvoření](how-to-create-assessment.md) nebo [přizpůsobení](how-to-modify-assessment.md) posouzení.