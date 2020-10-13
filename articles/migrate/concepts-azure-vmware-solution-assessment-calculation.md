---
title: Výpočty pro vyhodnocování pro funkci AVS v Azure Migrate | Microsoft Docs
description: Poskytuje přehled výpočtů služby AVS ve službě Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 400c2d91383b5f21fcd40fdbbe279bd83fcef51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576536"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Přehled posouzení serveru (migrace do řešení VMware Azure)

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh. Také sleduje vaše soukromé a veřejné cloudové instance do Azure. Centrum nabízí Azure Migrate nástroje pro posouzení a migraci a také nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Vyhodnocování serveru je nástroj v Azure Migrate, který vyhodnocuje místní servery pro migraci na virtuální počítače Azure IaaS a řešení Azure VMware (AVS). Tento článek poskytuje informace o tom, jak se počítají vyhodnocení řešení Azure VMware Solution (AVS).

> [!NOTE]
> Posouzení řešení Azure VMware (AVS) je aktuálně ve verzi Preview a je možné ho vytvořit jenom pro virtuální počítače VMware.

## <a name="types-of-assessments"></a>Typy posouzení

Posouzení, která vytvoříte pomocí posouzení serveru, jsou snímkem dat k určitému bodu v čase. Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md), [virtuální počítače Hyper-V](how-to-set-up-appliance-hyper-v.md)a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci do Azure. [Další informace](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pomocí tohoto typu posouzení můžete posoudit vhodnost místních [virtuálních počítačů VMware](how-to-set-up-appliance-vmware.md) k migraci do služby Azure VMware Solution (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

Posouzení řešení Azure VMware (AVS) v posouzení serveru poskytuje dvě možnosti pro kritéria změny velikosti:

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu místních virtuálních počítačů. | **Doporučená velikost uzlu**: na základě dat o využití procesoru a paměti spolu s typem uzlu, typem úložiště a FTT nastavením, které jste vybrali pro posouzení.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost uzlu**: v závislosti na velikosti místního virtuálního počítače spolu s typem uzlu, typem úložiště a nastavením FTT, které jste vybrali pro posouzení.

## <a name="how-do-i-run-an-assessment"></a>Návody spustit posouzení?

Existuje několik způsobů, jak spustit posouzení.

- Posuzuje počítače pomocí metadat serveru shromažďovaných odlehčeným Azure Migrate zařízením. Zařízení zjišťuje místní počítače. Pak odešle metadata a data o výkonu počítače do Azure Migrate.
- Vyhodnotit počítače pomocí metadat serveru, která jsou importovaná ve formátu hodnot oddělených čárkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Návody posoudit pomocí zařízení?

Pokud nasazujete zařízení Azure Migrate pro zjišťování místních serverů, proveďte následující kroky:

1. Nastavte Azure a vaše místní prostředí pro práci se serverem hodnocení.
2. Pro vaše první posouzení vytvořte projekt Azure a přidejte do něj Nástroj pro vyhodnocení serveru.
3. Nasaďte odlehčené zařízení Azure Migrate. Zařízení nepřetržitě zjišťuje místní počítače a odesílá data a údaje o výkonu počítačů do Azure Migrate. Nasaďte zařízení jako virtuální počítač. Nemusíte nic instalovat na počítače, které chcete vyhodnotit.

Jakmile zařízení spustí zjišťování počítačů, můžete shromáždit počítače, které chcete vyhodnotit, do skupiny a spustit posouzení pro skupinu s typem posouzení **Řešení Azure VMware (AVS)**.

Pomocí [následujícího postupu vytvořte](how-to-create-azure-vmware-solution-assessment.md)první posouzení služby Azure VMware Solution (AVS).

## <a name="how-do-i-assess-with-imported-data"></a>Návody vyhodnotit s importovanými daty?

Pokud vydáváte servery pomocí souboru CSV, nepotřebujete zařízení. Místo toho proveďte následující kroky:

1. Nastavte Azure pro práci se serverem hodnocení.
2. Pro vaše první posouzení vytvořte projekt Azure a přidejte do něj Nástroj pro vyhodnocení serveru.
3. Stáhněte si šablonu sdíleného svazku clusteru a přidejte do ní data serveru.
4. Importujte šablonu do posouzení serveru.
5. Zjišťování serverů přidaných pomocí importu, jejich shromáždění do skupiny a spuštění posouzení pro skupinu s typem posouzení **Řešení Azure VMware (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data shromažďuje zařízení?

Pokud k posouzení používáte zařízení Azure Migrate, přečtěte si informace o metadatech a datech výkonu shromažďovaných pro [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak zařízení počítá data o výkonu?

Pokud zařízení používáte ke zjišťování, shromažďuje údaje o výkonu pro nastavení výpočtů pomocí těchto kroků:

1. Zařízení shromažďuje ukázkový bod v reálném čase.

    - **Virtuální počítače VMware**: ukázkový bod se shromáždí každých 20 sekund.

2. Zařízení kombinuje ukázkové body za účelem vytvoření jednoho datového bodu každých 10 minut. Pokud chcete vytvořit datový bod, zařízení vybere vrcholové hodnoty ze všech ukázek. Pak odešle datový bod do Azure.
3. Posouzení serveru ukládá všechny datové body 10 minut za poslední měsíc.
4. Při vytváření posouzení Server Assessment identifikuje vhodný datový bod, který se má použít pro snižování. Identifikace je založena na hodnotách percentilu pro *historii výkonu* a procento *využití*.

    - Například pokud je historie výkonu jeden týden a procento využití je 95. percentil, vyřadí vyhodnocování serveru na poslední týden vzorkovací body 10 minut. Seřadí je ve vzestupném pořadí a vybere hodnotu 95. percentilu pro snižování.
    - Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud jste si vybrali 99 percentil.
    - Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné odlehlé hodnoty, vyberte 99 percentil pro využití percentilu.

5. Tato hodnota se vynásobí faktorem komfortu, aby získala efektivní data o využití výkonu pro tyto metriky, které zařízení shromažďuje:

    - Využití procesoru
    - Využití paměti RAM
    - Disk IOPS (čtení a zápis)
    - Propustnost disku (čtení a zápis)
    - Propustnost sítě (v/v)

Následující údaje o výkonu se shromažďují, ale nepoužívají se při určování velikosti doporučení pro vyhodnocení pro funkci AVS:
  - IOPS disku a data propustnosti pro každý disk připojený k virtuálnímu počítači.
  - I/O sítě pro zpracování velikosti pro každý síťový adaptér připojený k virtuálnímu počítači na základě výkonu.

## <a name="how-are-avs-assessments-calculated"></a>Jak se počítají vyhodnocování funkce AVS?

Posouzení serveru používá metadata a data výkonu místních počítačů k výpočtu posouzení. Pokud nasadíte zařízení Azure Migrate, posouzení použije data, která zařízení shromáždí. Pokud ale spustíte posouzení importované pomocí souboru CSV, poskytnete metadata pro tento výpočet.

K výpočtům dochází v těchto třech fázích:

1. **Výpočet připravenosti řešení Azure VMware (AVS)**: zda jsou místní virtuální počítače vhodné pro migraci do řešení Azure VMware (AVS).
2. **Vypočítat počet uzlů a využití služby AVS v uzlech**: odhadovaný počet uzlů služby AVS, které jsou potřebné ke spuštění virtuálních počítačů a předpokládané využití procesoru, paměti a úložiště napříč všemi uzly.
3. **Odhad měsíčních nákladů**: Odhadované měsíční náklady na všechny uzly řešení Azure VMware (AVS), na kterých běží místní virtuální počítače.

Výpočty jsou v předchozím pořadí. Počítačový Server se přesune do pozdější fáze pouze v případě, že předá předchozí. Pokud třeba server neprojde fází připravenosti na službu AVS, je pro Azure označený jako nevhodný. Pro tento server se neprovádí výpočty velikosti a nákladů.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Co je v posouzení řešení Azure VMware (AVS)?

Co je je součástí posouzení služby AVS při vyhodnocování serveru:


| **Vlastnost** | **Podrobnosti** 
| - | - 
| **Cílové umístění** | Určuje umístění privátního cloudu AVS, do kterého chcete migrovat.<br/><br/> Posouzení pro funkci AVS v nástroji Server Assessment aktuálně podporuje tyto cílové oblasti: Východní USA, Západní Evropa Západní USA. 
| **Typ úložiště** | Určuje modul úložiště, který se má používat v rámci služby AVS.<br/><br/> Posouzení služby AVS podporuje pouze síti vSAN jako výchozí typ úložiště. 
**Rezervované instance (RIs)** | Tato vlastnost vám pomůže určit rezervované instance v funkci AVS. Pro uzly služby AVS se aktuálně nepodporují rezervované instance. 
**Typ uzlu** | Určuje [typ uzlu AVS](../azure-vmware/concepts-private-clouds-clusters.md) , který se používá k mapování místních virtuálních počítačů. Výchozí typ uzlu je AV36. <br/><br/> Azure Migrate doporučí, aby se virtuální počítače přenesly do služby AVS požadovaným počtem uzlů. 
**Nastavení FTT, úroveň RAID** | Určuje použitou chybu při tolerování a kombinacích RAID. Vybraná možnost FTT v kombinaci s požadavkem na místní disk virtuálních počítačů určí celkové úložiště síti vSAN, které se v prostředí AVS vyžaduje. 
**Kritérium určení velikosti** | Nastaví kritéria, která se mají použít ke *správné velikosti* virtuálních počítačů pro funkci AVS. Můžete se rozhodnout pro přizpůsobení velikosti na *základě výkonu* nebo *jako místní,* aniž byste museli zvážit historii výkonu. 
**Historie výkonu** | Nastaví dobu trvání, která se má vzít v úvahu při vyhodnocování dat výkonu počítačů. Tato vlastnost je platná pouze v případě, že kritéria změny velikosti jsou *založená na výkonu*. 
**Percentilové využití** | Určuje hodnotu percentilu sady ukázek výkonu, která má být považována za správné určení velikosti. Tato vlastnost je platná pouze v případě, že je velikost na základě výkonu.
**Faktor komfortu** | Vyhodnocování Azure Migrate serveru během posuzování považuje vyrovnávací paměť (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. 
**Nabídka** | Zobrazí [nabídku Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste si zaregistrovali. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Zobrazuje fakturační měnu vašeho účtu. 
**Sleva (%)** | Obsahuje seznam všech slev specifických pro předplatné, které obdržíte nad nabídkou Azure. Výchozí nastavení je 0 %. 
**Zvýhodněné hybridní využití Azure** | Určuje, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). I když nemá žádný vliv na ceny řešení Azure VMware z důvodu ceny založené na uzlu, zákazníci můžou dál používat místní licence na operační systém (na základě Microsoftu) v prostředí AVS pomocí hybridních výhod Azure. Ostatní dodavatelé softwaru pro operační systémy budou muset zadat vlastní licenční podmínky, například RHEL. 
**vCPU – předplatné** | Určuje poměr počtu virtuálních jader vázaných k jednomu fyzickému jádru v uzlu AVS. Výchozí hodnota ve výpočtech je 4 vCPU: 1 fyzická jádro v funkci AVS. <br/><br/> Uživatelé rozhraní API můžou tuto hodnotu nastavit jako celé číslo. Všimněte si, že vCPU > 4:1 může mít dopad na úlohy v závislosti na využití procesoru. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analýza vhodnosti řešení Azure VMware (AVS)

Posouzení služby AVS v rámci posouzení serveru vyhodnocuje u každého z místních virtuálních počítačů jeho vhodnost pro funkci AVS kontrolou vlastností počítače. Každý vyhodnocený počítač se také přiřadí jedné z následujících kategorií vhodnosti:

- **Připraveno k funkci AVS**: počítač se dá migrovat tak, jak je, do Azure (AVS) bez jakýchkoli změn. Spustí se v programu AVS s plnou podporou AVS.
- **Připraveno s podmínkami**: virtuální počítač může mít problémy s kompatibilitou s aktuální verzí vSphere a vyžadovat nástroje VMware a další nastavení před tím, než bude možné dosáhnout plné funkčnosti z virtuálního počítače v prostředí AVS.
- **Není připravené na službu AVS**: virtuální počítač se nespustí v prostředí AVS. Pokud má například místní virtuální počítač VMware připojené externí zařízení, například disk CD-ROM, operace VMware VMotion selže (Pokud používáte VMware VMotion).
- **Připravenost neznámá**: Azure Migrate nedokázala určit připravenost počítače kvůli nedostatečným metadatům shromážděným z místního prostředí.

Posouzení serveru zkontroluje vlastnosti počítače a určí připravenost Azure na místním počítači.

### <a name="machine-properties"></a>Vlastnosti počítače

Posouzení serveru zkontroluje následující vlastnost místního virtuálního počítače, aby zjistila, jestli se dá spustit na řešení Azure VMware (AVS).


| **Vlastnost** | **Podrobnosti** | **Stav připravenosti na funkci AVS** 
| - | - | - 
| **Internet Protocol** | AVS v současné době nepodporuje používání internetových adres IPv6.<br/><br/> Pokud se na vašem počítači zjistí používání protokolu IPv6 a potřebujete pokyny k nápravě, obraťte se na místní tým MSFT AVS GBB.| Podmíněně připravené Internet Protocol


### <a name="guest-operating-system"></a>Hostovaný operační systém

V současné době nejsou v současnosti posuzování pro funkci AVS v rámci analýzy vhodnosti kontrolovat operační systém. Všechny operační systémy běžící na místních virtuálních počítačích se nejspíš budou spouštět v řešení Azure VMware (AVS).

Posouzení serveru spolu s vlastnostmi virtuálního počítače podívá na hostovaný operační systém počítačů a určí, jestli se dá spustit na Azure.


## <a name="sizing"></a>Velikosti

Když je počítač označený jako připravený pro funkci AVS, hodnocení služby AVS v posouzení serveru umožňuje doporučení pro změnu velikosti uzlů, která zahrnují identifikaci příslušných požadavků na místní virtuální počítač a vyhledání celkového počtu požadovaných uzlů služby AVS. Tato doporučení se liší v závislosti na zadaných vlastnostech posouzení.

- Pokud hodnocení používá určení *velikosti na základě výkonu*, Azure Migrate považuje historii výkonu počítače za vhodné, aby se pro službu AVS zajistila vhodná doporučení pro změnu velikosti. Tato metoda je užitečná hlavně v případě, že jste přestali využívat místní virtuální počítač, ale využití je nízké a vy chcete ušetřit náklady tak, aby se virtuální počítač ve službě AVS nastavil na správné velikosti. Tato metoda vám pomůže optimalizovat velikosti během migrace.
- Pokud nechcete brát v úvahu údaje o výkonu pro změny velikosti virtuálních počítačů a chcete, aby místní počítače byly v rámci služby AVS, můžete nastavit kritéria pro změnu velikosti *jako v*místním prostředí. Vyhodnocování serveru pak provede velikost virtuálních počítačů na základě místní konfigurace bez zvážení dat o využití. 


### <a name="ftt-sizing-parameters"></a>Parametry změny velikosti FTT

Modul úložiště používaný v rozhraní AVS je síti vSAN. zásady úložiště síti vSAN definují požadavky na úložiště pro virtuální počítače. Tyto zásady pro vaše virtuální počítače zaručují požadovanou úroveň služeb, protože určují, jak se virtuálním počítačům přiděluje úložiště. Dostupné kombinace FTT-Raid: 

**Tolerované chyby (FTT)** | **Konfigurace RAID** | **Minimální požadovaný počet hostitelů** | **Důležité informace o nastavení velikosti**
--- | --- | --- | --- 
1 | RAID-1 (zrcadlení) | 3 | Virtuální počítač o velikosti 100 GB spotřebuje 200 GB.
1 | RAID-5 (kódování pro případ vymazaní) | 4 | Virtuální počítač o velikosti 100 GB spotřebuje 133,33 GB.
2 | RAID-1 (zrcadlení) | 5 | Virtuální počítač o velikosti 100 GB spotřebuje 300 GB.
2 | RAID-6 (kódování pro případ vymazaní) | 6 | Virtuální počítač o velikosti 100 GB spotřebuje 150 GB.
3 | RAID-1 (zrcadlení) | 7 | Virtuální počítač o velikosti 100 GB spotřebuje 400 GB.

### <a name="performance-based-sizing"></a>Změny velikosti na základě výkonu

Pro určení velikosti na základě výkonu začíná posouzení serveru disky připojenými k virtuálnímu počítači a potom síťové adaptéry. Pak tyto požadavky na virtuální počítač mapuje na odpovídající počet uzlů pro funkci AVS. Zařízení Azure Migrate profiluje místní prostředí pro shromažďování údajů o výkonu procesoru, paměti, disků a síťového adaptéru.

**Postup shromažďování dat výkonu:**

1. Pro virtuální počítače VMware shromažďuje Azure Migrate zařízení ukázkový bod v reálném čase v intervalu 20 sekund. 
2. Zařízení zahrne vzorové body shromážděné každých 10 minut a pošle maximální hodnotu za posledních 10 minut do posouzení serveru.
3. Vyhodnocování serveru ukládá všechny ukázkové body 10 minut za poslední měsíc. V závislosti na vlastnostech posouzení zadaných pro *historii výkonu* a procento *využití*se pak určí vhodný datový bod, který se má použít pro správnou velikost. Například pokud je historie výkonu nastavena na 1 den a procento využití je 95. percentil, vyhodnocování serveru používá pro poslední den 10 minut, seřadí je ve vzestupném pořadí a vybere hodnotu percentilu 95. pro správnou velikost.
4. Tato hodnota je vynásobena faktorem komfortu, aby získala efektivní data o využití výkonu pro každou metriku (využití procesoru, využití paměti, IOPS disku (čtení a zápis), propustnost disku (čtení a zápis) a propustnost sítě (v/v), kterou zařízení shromažďuje.

Po určení efektivní hodnoty využití se velikost úložiště, sítě a výpočetního prostředí zpracuje následujícím způsobem.

**Velikost úložiště**: Azure Migrate jako parametr výpočtu používá celkové místo na disku virtuálního počítače, aby bylo možné kromě nastavení FTT vybraného pro zákazníka určit taky požadavky na úložiště AVS síti vSAN. FTT – při nedodržení možnosti minimální počet uzlů na FTT se určí celkové úložiště síti vSAN, které se v kombinaci s požadavky na disk virtuálního počítače vyžadují.

**Určování velikosti sítě:** Nástroj Hodnocení serverů v současné době při hodnoceních služby AVS nebere v úvahu žádné nastavení sítě.

**Výpočet velikosti**: po výpočtu požadavků na úložiště posuzuje vyhodnocení serveru požadavky na procesor a paměť a určí počet uzlů vyžadovaných pro funkci AVS na základě typu uzlu.

- Na základě kritérií pro změnu velikosti vyhledá server hodnocení buď data virtuálních počítačů na základě výkonu nebo místní konfiguraci virtuálních počítačů. Nastavení faktoru pohodlí umožňuje určit faktor růstu clusteru. V současné době je ve výchozím nastavení povolená technologie Hyper-Threading, a proto uzly s 36 jádry mají 72 virtuálních jader. K určení prahových hodnot CPU na cluster se používají 4 virtuální jádra na fyzický uzel za použití standardu VMware, který zajišťuje, že nedojde k překročení 80% využití, aby bylo možné provádět údržbu nebo zpracování selhání bez ohrožení dostupnosti clusteru. V tuto chvíli není k dispozici žádné přepsání ke změně hodnot nadplatku a v budoucích verzích ho můžeme použít.

### <a name="as-on-premises-sizing"></a>Podle velikosti v místním prostředí

Pokud používáte *jako místní velikost*, posouzení serveru nebere v úvahu historii výkonu virtuálních počítačů a disků. Místo toho přiděluje uzly AVS v závislosti na velikosti přidělené místně. Výchozí typ úložiště je síti vSAN v AVS.

## <a name="confidence-ratings"></a>Hodnocení spolehlivosti

Každé posouzení na základě výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti, které je v rozsahu od jednoho (nejnižší) po pět hvězdiček (nejvyšší).

- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti není použitelné pro vyhodnocení *místních* hodnot.
- Pro určení velikosti na základě výkonu potřebují posouzení služby AVS v rámci posuzování serveru data o využití procesoru a paměti virtuálního počítače. Následující data se shromažďují, ale nepoužívají se v doporučeních pro změnu velikosti pro funkci AVS:
  - IOPS disku a data propustnosti pro každý disk připojený k virtuálnímu počítači.
  - I/O sítě pro zpracování velikosti pro každý síťový adaptér připojený k virtuálnímu počítači na základě výkonu.

  Pokud některá z těchto čísel využití nejsou v vCenter Server k dispozici, doporučení velikosti nemusí být spolehlivé.

V závislosti na procentu dostupných datových bodů se hodnocení spolehlivosti pro posouzení provede následujícím způsobem.


| **Dostupnost datových bodů** | **Hodnocení spolehlivosti** |
| - | - |
| 0-20% | 1 hvězdička |
| 21-40% | 2 hvězdičky |
| 41-60% | 3 hvězdičky |
| 61-80% | 4 hvězdičky |
| 81-100% | 5 hvězdiček |

### <a name="low-confidence-ratings"></a>Hodnocení nízké důvěry

Tady je několik důvodů, proč hodnocení může získat nízkou spolehlivost:

- Nevytvořili jste profil svého prostředí po dobu, po kterou vytváříte posouzení. Pokud například vytvoříte hodnocení s trváním výkonu nastaveným na jeden den, musíte po spuštění zjišťování pro všechny datové body, které se mají shromáždit, počkat aspoň jeden den.
- Některé virtuální počítače se vypnuly během období, pro které se hodnocení vypočítalo. Pokud jsou některé virtuální počítače po určitou dobu vypnuté, vyhodnocování serveru nemůže shromažďovat data o výkonu pro tuto dobu.
- Některé virtuální počítače byly vytvořeny během období, pro které bylo hodnocení vypočítáno. Pokud jste například vytvořili vyhodnocení pro historii výkonu za poslední měsíc, ale některé virtuální počítače byly vytvořeny pouze před týdnem, historie výkonu nových virtuálních počítačů nebude po celou dobu trvání k dispozici.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení menší než pět hvězdiček, doporučujeme, abyste počkali aspoň jeden den, než zařízení profiluje prostředí, a pak posouzení přepočítá. Pokud to neuděláte, velikost na základě výkonu nemusí být spolehlivá. V takovém případě doporučujeme, abyste přepnuli posouzení na místní nastavení velikosti.

## <a name="monthly-cost-estimation"></a>Odhad měsíčních nákladů

Po dokončení doporučení pro změny velikosti Azure Migrate vypočítá celkové náklady na spuštění místních úloh v prostředí AVS vynásobením počtu uzlů služby AVS, které vyžaduje cena uzlu. Náklady na virtuální počítač se vypočítávají z celkových nákladů podle počtu virtuálních počítačů v posouzení. 
- Výpočet přebírá počet požadovaných uzlů, typ uzlu a umístění v rámci účtu.
- Agreguje náklady napříč všemi uzly a vypočítá celkové měsíční náklady.
- Náklady se zobrazují v měně určené v nastavení hodnocení.

Jelikož jsou ceny za Azure VMware Solution (AVS) na jeden uzel, celkové náklady na výpočetní náklady a distribuci nákladů na úložiště se nepočítají. [Další informace](../azure-vmware/introduction.md)

Všimněte si, že jako Azure VMware Solution (AVS) je ve verzi Preview, ceny za uzel v hodnocení jsou ve verzi Preview. Pokyny najdete v místním týmu MSFT AVS GBB.

## <a name="migration-tool-guidance"></a>Pokyny k nástroji pro migraci

V sestavě připravenosti pro Azure pro hodnocení služby Azure VMware Solution (AVS) uvidíte následující navrhované nástroje: 
- **VMware HCX nebo Enterprise**: pro počítače VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). [Další informace](../azure-vmware/tutorial-deploy-vmware-hcx.md)
- **Neznámý:** V případě počítačů importovaných prostřednictvím souboru CSV je výchozí nástroj pro migraci neznámý. I když používáte počítače VMware, doporučuje se použít řešení VMware Hybrid Cloud Extension (HCX).

## <a name="next-steps"></a>Další kroky

Vytvořte posouzení pro [virtuální počítače služby AVS](how-to-create-azure-vmware-solution-assessment.md)pro prostředí VMware.
