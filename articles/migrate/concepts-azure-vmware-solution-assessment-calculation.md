---
title: Výpočty pro vyhodnocování pro funkci AVS v Azure Migrate | Microsoft Docs
description: Poskytuje přehled výpočtů služby AVS ve službě Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 1d9918786b22faddaeb07a12f0840b36a11ffe4d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778377"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Přehled posouzení (migrace do řešení VMware Azure)

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh. Také sleduje vaše soukromé a veřejné cloudové instance do Azure. Centrum nabízí Azure Migrate nástroje pro posouzení a migraci a také nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Nástroj pro zjišťování a vyhodnocení v Azure Migrate vyhodnocuje místní servery pro migraci na virtuální počítače Azure a řešení Azure VMware (AVS). Tento článek poskytuje informace o tom, jak se počítají vyhodnocení řešení Azure VMware Solution (AVS).

> [!NOTE]
> Posouzení řešení Azure VMware (AVS) se dá vytvořit jenom pro virtuální počítače VMware.

## <a name="types-of-assessments"></a>Typy posouzení

Hodnocení, která vytvoříte pomocí Azure Migrate, jsou snímkem dat v určitém časovém okamžiku. Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate:

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. Místní servery můžete vyhodnotit v prostředí [VMware](how-to-set-up-appliance-vmware.md) a [Hyper-V](how-to-set-up-appliance-hyper-v.md) a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci na virtuální počítače Azure pomocí tohoto typu posouzení.
**Azure SQL** | Vyhodnotí migraci místních SQL serverů z prostředí VMware do Azure SQL Database nebo spravované instance Azure SQL.
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md) pro migraci do řešení Azure VMware (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

Posouzení řešení Azure VMware (AVS) poskytuje dvě možnosti pro kritéria změny velikosti:

| **Posouzení** | **Podrobnosti** | **Data** |
| - | - | - |
| **Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu místních virtuálních počítačů. | **Doporučená velikost uzlu**: na základě dat o využití procesoru a paměti spolu s typem uzlu, typem úložiště a FTT nastavením, které jste vybrali pro posouzení. |
| **Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost uzlu**: v závislosti na velikosti místního virtuálního počítače spolu s typem uzlu, typem úložiště a nastavením FTT, které jste vybrali pro posouzení. |

## <a name="how-do-i-run-an-assessment"></a>Návody spustit posouzení?

Existuje několik způsobů, jak spustit posouzení.

- Posuzuje servery pomocí metadat serveru shromažďovaných odlehčeným Azure Migrate zařízením. Zařízení zjišťuje místní servery. Pak odešle metadata serveru a data o výkonu do Azure Migrate. To umožňuje přesnější přesnost.
- Vyhodnoťte servery pomocí metadat serveru, která jsou importovaná ve formátu hodnot oddělených čárkami (CSV).

## <a name="how-do-i-assess-with-the-appliance"></a>Návody posoudit pomocí zařízení?

Pokud nasazujete zařízení Azure Migrate pro zjišťování místních serverů, proveďte následující kroky:

1. Nastavte Azure a vaše místní prostředí pro práci s Azure Migrate.
2. Pro vaše první posouzení vytvořte projekt Azure a přidejte do něj Nástroj pro zjišťování a vyhodnocení.
3. Nasaďte odlehčené zařízení Azure Migrate. Zařízení nepřetržitě zjišťuje místní servery a odesílá údaje o metadatech a výkonu serveru do Azure Migrate. Nasaďte zařízení jako virtuální počítač. Nemusíte nic instalovat na servery, které chcete vyhodnotit.

Jakmile zařízení zahájí zjišťování serveru, můžete shromáždit servery, které chcete vyhodnotit do skupiny, a spustit posouzení pro skupinu s typem posouzení **Řešení Azure VMware (AVS)**.

Pomocí [následujícího postupu vytvořte](how-to-create-azure-vmware-solution-assessment.md)první posouzení služby Azure VMware Solution (AVS).

## <a name="how-do-i-assess-with-imported-data"></a>Návody vyhodnotit s importovanými daty?

Pokud vydáváte servery pomocí souboru CSV, nepotřebujete zařízení. Místo toho proveďte následující kroky:

1. Nastavte Azure pro práci s Azure Migrate.
2. Pro vaše první posouzení vytvořte projekt Azure a přidejte do něj Nástroj pro zjišťování a vyhodnocení.
3. Stáhněte si šablonu sdíleného svazku clusteru a přidejte do ní data serveru.
4. Importujte šablonu do Azure Migrate.
5. Zjišťování serverů přidaných pomocí importu, jejich shromáždění do skupiny a spuštění posouzení pro skupinu s typem posouzení **Řešení Azure VMware (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data shromažďuje zařízení?

Pokud k posouzení používáte zařízení Azure Migrate, přečtěte si informace o metadatech a datech výkonu shromažďovaných pro [VMware](migrate-appliance.md#collected-data---vmware).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Jak zařízení počítá data o výkonu?

Pokud zařízení používáte ke zjišťování, shromažďuje údaje o výkonu pro nastavení výpočtů pomocí těchto kroků:

1. Zařízení shromažďuje ukázkový bod v reálném čase.

   - **Virtuální počítače VMware**: ukázkový bod se shromáždí každých 20 sekund.
2. Zařízení kombinuje ukázkové body za účelem vytvoření jednoho datového bodu každých 10 minut. Pokud chcete vytvořit datový bod, zařízení vybere vrcholové hodnoty ze všech ukázek. Pak odešle datový bod do Azure.
3. Azure Migrate ukládá všechny datové body 10 minut za poslední měsíc.
4. Při vytváření posouzení identifikuje posouzení vhodný datový bod, který se má použít pro snižování. Identifikace je založena na hodnotách percentilu pro *historii výkonu* a procento *využití*.

   - Například pokud je historie výkonu jeden týden a procento využití je 95. percentil, vyřadí hodnocení 10 minut vzorků za poslední týden. Seřadí je ve vzestupném pořadí a vybere hodnotu 95. percentilu pro snižování.
   - Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud jste si vybrali 99 percentil.
   - Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné odlehlé hodnoty, vyberte 99 percentil pro využití percentilu.
5. Tato hodnota se vynásobí faktorem komfortu, aby získala efektivní data o využití výkonu pro tyto metriky, které zařízení shromažďuje:

   - Využití procesoru
   - Využití paměti RAM

Následující údaje o výkonu se shromažďují, ale nepoužívají se při určování velikosti doporučení pro vyhodnocení pro funkci AVS:

- IOPS disku a data propustnosti pro každý disk připojený k virtuálnímu počítači.
- I/O sítě pro zpracování velikosti pro každý síťový adaptér připojený k virtuálnímu počítači na základě výkonu.

## <a name="how-are-avs-assessments-calculated"></a>Jak se počítají vyhodnocování funkce AVS?

Při výpočtu posouzení používá nástroj AVS metadata a data o výkonu místních serverů. Pokud nasadíte zařízení Azure Migrate, posouzení použije data, která zařízení shromáždí. Pokud ale spustíte posouzení importované pomocí souboru CSV, poskytnete metadata pro tento výpočet.

K výpočtům dochází v těchto třech fázích:

1. **Výpočet připravenosti řešení Azure VMware (AVS)**: zda jsou místní virtuální počítače vhodné pro migraci do řešení Azure VMware (AVS).
2. **Vypočítat počet uzlů a využití služby AVS v uzlech**: odhadovaný počet uzlů služby AVS, které jsou nutné ke spuštění virtuálních počítačů VMware a předpokládaného využití procesoru, paměti a úložiště napříč všemi uzly.
3. **Odhad měsíčních nákladů**: Odhadované měsíční náklady na všechny uzly řešení Azure VMware (AVS), na kterých běží místní virtuální počítače.

Výpočty jsou v předchozím pořadí. Server se přesune do pozdější fáze pouze v případě, že předá předchozí. Pokud třeba server neprojde fází připravenosti na službu AVS, je pro Azure označený jako nevhodný. Pro tento server se neprovádí výpočty velikosti a nákladů.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Co je v posouzení řešení Azure VMware (AVS)?

Co je je součástí posouzení služby AVS:

| **Vlastnost** | **Podrobnosti** |
| - | - |
| **Cílové umístění** | Určuje umístění privátního cloudu AVS, do kterého chcete migrovat. |
| **Typ úložiště** | Určuje modul úložiště, který se má používat v rámci služby AVS. Služba AVS v současné době podporuje pouze síti vSAN jako výchozí typ úložiště, ale další možnosti úložiště budou přicházet jako na plán. |
| **Rezervované instance (RIs)** | Tato vlastnost vám pomůže zadat rezervované instance v případě nákupu a podmínky rezervované instance. Slouží k výpočtu nákladů. |
| **Typ uzlu** | Určuje [typ uzlu služby AVS](../azure-vmware/concepts-private-clouds-clusters.md) , který se používá pro použití v Azure. Výchozí typ uzlu je AV36. V budoucnu může být k dispozici více typů uzlů.  Azure Migrate doporučí, aby se virtuální počítače přenesly do služby AVS požadovaným počtem uzlů. |
| **Nastavení FTT, úroveň RAID** | Určuje platnou kombinaci chyb pro tolerovat kombinaci a RAID. Vybraná možnost FTT v kombinaci s úrovní RAID a požadavkem na místní disk virtuálních počítačů určí celkové úložiště síti vSAN, které se v prostředí AVS vyžaduje. Celkové dostupné úložiště po výpočtech zahrnuje i místo vyhrazené pro objekty pro správu, jako jsou vCenter a b) 25% časová rezerva úložiště požadovaná pro síti vSAN operace. |
| **Kritérium určení velikosti** | Nastaví kritéria, která se mají použít k určení paměti, požadavků na procesor a úložiště pro uzly služby AVS. Můžete se rozhodnout pro přizpůsobení velikosti na *základě výkonu* nebo *jako místní,* aniž byste museli zvážit historii výkonu. Chcete-li jednoduše vyzvednutí a posunování, vyberte možnost místní. Pokud chcete získat velikost na základě využití, vyberte na základě výkonu. |
| **Historie výkonu** | Nastaví dobu trvání, která se má vzít v úvahu při vyhodnocování dat výkonu serverů. Tato vlastnost je platná pouze v případě, že kritéria změny velikosti jsou *založená na výkonu*. |
| **Percentilové využití** | Určuje hodnotu percentilu sady ukázek výkonu, která má být považována za správné určení velikosti. Tato vlastnost je platná pouze v případě, že je velikost na základě výkonu. |
| **Faktor komfortu** | Azure Migrate při posuzování počítá s rezervou (faktor komfortu). Tato vyrovnávací paměť je aplikována na data o využití serveru pro virtuální počítače (CPU, paměť a disk). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití. Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. |
| **Nabídka** | Zobrazí [nabídku Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste si zaregistrovali. Azure Migrate odhadne náklady odpovídajícím způsobem. |
| **Měna** | Zobrazuje fakturační měnu vašeho účtu. |
| **Sleva (%)** | Obsahuje seznam všech slev specifických pro předplatné, které obdržíte nad nabídkou Azure. Výchozí nastavení je 0 %. |
| **Zvýhodněné hybridní využití Azure** | Určuje, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). I když nemá žádný vliv na ceny řešení Azure VMware z důvodu ceny založené na uzlu, zákazníci můžou dál používat místní licence na operační systém nebo SQL (založené na Microsoftu) v prostředí AVS pomocí hybridních výhod Azure. Ostatní dodavatelé softwaru pro operační systémy budou muset zadat vlastní licenční podmínky, například RHEL. |
| **vCPU – předplatné** | Určuje poměr počtu virtuálních jader vázaných k jednomu fyzickému jádru v uzlu AVS. Výchozí hodnota ve výpočtech je 4 vCPU: 1 fyzická jádro v funkci AVS. Uživatelé rozhraní API můžou tuto hodnotu nastavit jako celé číslo. Všimněte si, že vCPU > 4:1 může mít dopad na úlohy v závislosti na využití procesoru. Při změně velikosti vždy předpokládáme 100% využití vybraných jader. |
| **Faktor přetvrzování paměti** | Určuje poměr paměti nad potvrzením v clusteru. Hodnota 1 představuje 100% využití paměti, 0,5 například 50% a 2 by používalo 200% dostupné paměti. Hodnoty můžete přidat jenom od 0,5 do 10 až na jedno desetinné místo. |
| **Deduplicity a faktor komprese** | Určuje očekávané deduplicity a kompresní faktor pro vaše úlohy. Skutečnou hodnotu lze získat z místního síti vSAN nebo z konfigurace úložiště. Liší se podle úloh. Hodnota 3 by znamenala 3x, takže se použije jenom úložiště 100 GB pro disk 300 GB. Hodnota 1 by znamenala žádné deduplicity ani komprimaci. Můžete přidat jenom hodnoty od 1 do 10 až na jedno desetinné místo. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analýza vhodnosti řešení Azure VMware (AVS)

Posouzení služby AVS vyhodnocuje každý místní virtuální počítač a jeho vhodnost pro funkci AVS kontrolou vlastností serveru. Každý vyhodnocený server přiřadí taky jednu z následujících kategorií vhodnosti:

- **Připraveno k funkci AVS**: Server se dá migrovat tak, jak je, do Azure (AVS) bez jakýchkoli změn. Spustí se v programu AVS s plnou podporou AVS.
- **Připraveno s podmínkami**: virtuální počítač může mít problémy s kompatibilitou s aktuální verzí vSphere a vyžadovat nástroje VMware a další nastavení před tím, než bude možné dosáhnout plné funkčnosti z virtuálního počítače v prostředí AVS.
- **Není připravené na službu AVS**: virtuální počítač se nespustí v prostředí AVS. Pokud má například místní virtuální počítač VMware připojené externí zařízení, například disk CD-ROM, operace VMware VMotion selže (Pokud používáte VMware VMotion).
- **Připravenost neznámá**: Azure Migrate nedokázala určit připravenost serveru kvůli nedostatečným metadatům shromážděným z místního prostředí.

Posouzení zkontroluje vlastnosti serveru a určí připravenost na Azure na místním serveru.

### <a name="server-properties"></a>Vlastnosti serveru

Posouzení zkontroluje následující vlastnost místního virtuálního počítače, aby zjistila, jestli se dá spustit na řešení Azure VMware (AVS).

| **Vlastnost** | **Podrobnosti** | **Stav připravenosti na funkci AVS** |
| - | - | - |
| **Internet Protocol** | Azure v současné době nepodporuje koncové adresování Internetu s koncovým protokolem IPv6. Pokud se server detekuje s protokolem IPv6, kontaktujte svůj místní tým protokolu MSFT AVS GBB, kde najdete pokyny k nápravě. | Podmíněně připravené Internet Protocol |

### <a name="guest-operating-system"></a>Hostovaný operační systém

V současné době nejsou v současnosti posuzování pro funkci AVS v rámci analýzy vhodnosti kontrolovat operační systém. Všechny operační systémy běžící na místních virtuálních počítačích se nejspíš budou spouštět v řešení Azure VMware (AVS).

Spolu s vlastnostmi virtuálního počítače vyhodnocení vyhledá v hostovaném operačním systému serverů, abyste zjistili, jestli ho můžete spustit v Azure.

## <a name="sizing"></a>Velikosti

Když je server označený jako připravený pro funkci AVS, vyhodnocování služby AVS vytvoří doporučení pro změnu velikosti uzlů, která zahrnují identifikaci příslušných požadavků na místní virtuální počítač a vyhledání celkového počtu požadovaných uzlů služby AVS. Tato doporučení se liší v závislosti na zadaných vlastnostech posouzení.

- Pokud hodnocení používá určení *velikosti na základě výkonu*, Azure Migrate považuje historii výkonu serveru za účelem provedení vhodného doporučení pro změnu velikosti pro funkci AVS. Tato metoda je užitečná hlavně v případě, že jste přestali využívat místní virtuální počítač, ale využití je nízké a vy chcete ušetřit náklady tak, aby se virtuální počítač ve službě AVS nastavil na správné velikosti. Tato metoda vám pomůže optimalizovat velikosti během migrace.
- Pokud nechcete brát v úvahu údaje o výkonu pro změny velikosti virtuálních počítačů a chcete místní servery považovat za nefunkční, můžete nastavit kritéria pro změnu velikosti na * místní *. Hodnocení pak bude na základě místních konfigurací měnit velikost virtuálních počítačů bez zvážení dat o využití.

### <a name="ftt-sizing-parameters"></a>Parametry změny velikosti FTT

Modul úložiště používaný v rozhraní AVS je síti vSAN. zásady úložiště síti vSAN definují požadavky na úložiště pro vaše servery. Tyto zásady pro vaše virtuální počítače zaručují požadovanou úroveň služeb, protože určují, jak se virtuálním počítačům přiděluje úložiště. Dostupné kombinace FTT-Raid:

| **Tolerované chyby (FTT)** | **Konfigurace RAID** | **Minimální požadovaný počet hostitelů** | **Důležité informace o nastavení velikosti** |
| - | - | - | - |
| 1 | RAID-1 (zrcadlení) | 3 | Virtuální počítač o velikosti 100 GB spotřebuje 200 GB. |
| 1 | RAID-5 (kódování pro případ vymazaní) | 4 | Virtuální počítač o velikosti 100 GB spotřebuje 133,33 GB. |
| 2 | RAID-1 (zrcadlení) | 5 | Virtuální počítač o velikosti 100 GB spotřebuje 300 GB. |
| 2 | RAID-6 (kódování pro případ vymazaní) | 6 | Virtuální počítač o velikosti 100 GB spotřebuje 150 GB. |
| 3 | RAID-1 (zrcadlení) | 7 | Virtuální počítač o velikosti 100 GB spotřebuje 400 GB. |

### <a name="performance-based-sizing"></a>Změny velikosti na základě výkonu

Pro určení velikosti na základě výkonu Azure Migrate profily zařízení v místním prostředí za účelem shromažďování údajů o výkonu pro procesor, paměť a disk. Proto se velikost na základě výkonu pro funkci AVS vezme v úvahu přiděleného místa na disku a využitím zvolené hodnoty percentilu paměti a procesoru. Například pokud má virtuální počítač přidělené 4vCores, ale jenom pomocí 25%, pak bude velikost služby AVS pro 1vCore pro tento virtuální počítač.

**Postup shromažďování dat výkonu:**

1. Pro virtuální počítače VMware shromažďuje Azure Migrate zařízení ukázkový bod v reálném čase v intervalu 20 sekund.
2. Zařízení zahrne vzorové body shromážděné každých 10 minut a pošle maximální hodnotu za posledních 10 minut, než se Azure Migrate.
3. Azure Migrate ukládá všechny vzorové body 10 minut za poslední měsíc. V závislosti na vlastnostech posouzení zadaných pro *historii výkonu* a procento *využití* se pak určí vhodný datový bod, který se má použít pro správnou velikost. Například pokud je historie výkonu nastavená na 1 den a procento využití je 95. percentil, Azure Migrate použije pro poslední den vzorové body 10 minut, seřadí je vzestupně a vybere hodnotu percentilu 95. pro správnou velikost.
4. Tato hodnota se vynásobí faktorem komfortu, aby získala efektivní data o využití výkonu pro každou metriku (využití procesoru a využití paměti), které zařízení shromažďuje.

Po určení efektivní hodnoty využití se velikost úložiště, sítě a výpočetního prostředí zpracuje následujícím způsobem.

**Velikost úložiště**: Azure Migrate jako parametr výpočtu používá celkové místo na disku virtuálního počítače, aby bylo možné kromě nastavení FTT vybraného pro zákazníka určit taky požadavky na úložiště AVS síti vSAN. FTT – při nedodržení možnosti minimální počet uzlů na FTT se určí celkové úložiště síti vSAN, které se v kombinaci s požadavky na disk virtuálního počítače vyžadují. Využití úložiště se v současnosti nebere v úvahu a logika vyhledává pouze přidělené úložiště na virtuální počítač.

**Velikost sítě**: posouzení pro funkci AVS aktuálně nebere v úvahu žádná nastavení sítě.

**Výpočet velikosti**: po výpočtu požadavků na úložiště vychází služba AVS požadavky na procesor a paměť, aby určila počet uzlů vyžadovaných pro funkci AVS na základě typu uzlu.

- V závislosti na kritériích pro změnu velikosti vyhodnocování službou AVS vyhledá data virtuálních počítačů na základě výkonu nebo místní konfiguraci virtuálních počítačů. Nastavení faktoru pohodlí umožňuje určit faktor růstu clusteru. V současné době je ve výchozím nastavení povolená technologie Hyper-Threading, a proto uzly s 36 jádry mají 72 virtuálních jader. K určení prahových hodnot CPU na cluster se používají 4 virtuální jádra na fyzický uzel za použití standardu VMware, který zajišťuje, že nedojde k překročení 80% využití, aby bylo možné provádět údržbu nebo zpracování selhání bez ohrožení dostupnosti clusteru. V tuto chvíli není k dispozici žádné přepsání ke změně hodnot nadplatku a v budoucích verzích ho můžeme použít.

### <a name="as-on-premises-sizing"></a>Podle velikosti v místním prostředí

Pokud použijete funkci *as-premises* reprodukčního prostředí, hodnocení AVS nebere v úvahu historii výkonu virtuálních počítačů a disků. Místo toho přiděluje uzly AVS v závislosti na velikosti přidělené místně. Výchozí typ úložiště je síti vSAN v AVS.

[Přečtěte si další informace](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) o tom, jak zkontrolovat vyhodnocení řešení Azure VMware.

### <a name="cpu-utilization-on-avs-nodes"></a>Využití procesoru na uzlech AVS

Využití CPU předpokládá 100% využití dostupných jader. Aby se snížil počet uzlů, které by vyžadovaly, mohl by toto procento zvýšit na 6:1 4:1 na základě charakteristik úloh a místního prostředí. Na rozdíl od pro disk služba AVS neomezuje žádné limity využití procesoru, je to pro zákazníky, aby se zajistilo, že jejich cluster bude optimálně fungovat, takže pokud se odpovídajícím způsobem musí upravit "běžící horká". Pokud chcete více místa pro růst, snižte předplatné nebo zvyšte hodnotu faktoru růstu.

Využití procesoru už taky účty pro režijní náklady na správu z vCenter, NSX Manageru a dalších menších prostředků.

### <a name="memory-utilization-on-avs-nodes"></a>Využití paměti na uzlech AVS

Využití paměti zobrazuje celkovou paměť ze všech uzlů vs. požadavky ze serveru nebo úloh. Paměť může být přes odběr a opět služba AVS neomezuje žádné limity a je zákazníkem, aby pro své úlohy spouštěla optimální výkon clusteru.

Využití paměti už taky účty pro režijní náklady na správu z vCenter, NSX Manageru a dalších menších prostředků.

### <a name="storage-utilization-on-avs-nodes"></a>Využití úložiště na uzlech služby AVS

Využití úložiště se počítá na základě následujícího pořadí:

1. Velikost požadovaná pro virtuální počítač (buď přiděleno jako nebo využité místo na základě výkonu)
2. Použít faktor růstu, pokud existuje
3. Přidat režijní náklady na správu a použít poměr FTT
4. Použít deduplicity a kompresní faktor
5. Použít pro síti vSAN požadovanou časovou rezervu 25%
6. Výsledkem je úložiště dostupné pro virtuální počítače z celkového úložiště, včetně režijních nákladů na správu.

Dostupné úložiště v clusteru se 3 uzly bude založené na výchozích zásadách úložiště, které jsou RAID-1 a využívají silné zřizování. Při výpočtu pro mazání kódu nebo RAID-5 například je vyžadováno minimálně 4 uzly. Všimněte si, že v případě služby AVS musí správce změnit zásady úložiště tak, aby umožňovalo více místa.

## <a name="confidence-ratings"></a>Hodnocení spolehlivosti

Každé posouzení na základě výkonu v Azure Migrate je přidruženo k hodnocení spolehlivosti, které je v rozsahu od jednoho (nejnižší) po pět hvězdiček (nejvyšší).

- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Hodnocení spolehlivosti posouzení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti není použitelné pro vyhodnocení *místních* hodnot.
- Pro určení velikosti na základě výkonu potřebují hodnocení služby AVS data o využití procesoru a paměti virtuálního počítače. Následující data se shromažďují, ale nepoužívají se v doporučeních pro změnu velikosti pro funkci AVS:

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

- Po dobu trvání, pro kterou vytváříte hodnocení, jste neprofilovali své prostředí. Pokud například vytvoříte hodnocení s trváním výkonu nastaveným na jeden den, musíte počkat alespoň den po zahájení zjišťování všech datových bodů, které se mají shromáždit.
- Posouzení nemůže shromáždit údaje o výkonu pro některé nebo všechny virtuální počítače v období posouzení. Pro hodnocení s vysokou mírou jistoty Prosím zajistěte:

  - Virtuální počítače jsou napájené po dobu trvání posouzení.
  - Odchozí připojení na portech 443 jsou povolená.
  - Pro virtuální počítače Hyper-V je povolená dynamická paměť.

  Přepočítejte posouzení, aby se projevily poslední změny míry spolehlivosti.
- Některé virtuální počítače byly vytvořeny během doby, kdy bylo hodnocení vypočítáno. Předpokládejme například, že jste vytvořili posouzení historie výkonu za poslední měsíc, ale některé virtuální počítače byly vytvořeny pouze v týdnu. V takovém případě nebudou k dispozici data o výkonu nových virtuálních počítačů za celou dobu trvání a míra spolehlivosti bude nízká.

> [!NOTE]
> Pokud je hodnocení spolehlivosti nějakého posouzení menší než pět hvězdiček, doporučujeme, abyste počkali aspoň jeden den, než zařízení profiluje prostředí, a pak posouzení přepočítá. Pokud to neuděláte, velikost na základě výkonu nemusí být spolehlivá. V takovém případě doporučujeme, abyste přepnuli posouzení na místní nastavení velikosti.

## <a name="monthly-cost-estimation"></a>Odhad měsíčních nákladů

Po dokončení doporučení pro změny velikosti Azure Migrate vypočítá celkové náklady na spuštění místních úloh v prostředí AVS vynásobením počtu uzlů služby AVS, které vyžaduje cena uzlu. Náklady na virtuální počítač se vypočítávají z celkových nákladů podle počtu virtuálních počítačů v posouzení.

- Výpočet přebírá počet požadovaných uzlů, typ uzlu a umístění v rámci účtu.
- Agreguje náklady napříč všemi uzly a vypočítá celkové měsíční náklady.
- Náklady se zobrazují v měně určené v nastavení hodnocení.

Jelikož jsou ceny za Azure VMware Solution (AVS) na jeden uzel, celkové náklady na výpočetní náklady a distribuci nákladů na úložiště se nepočítají. [Další informace](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Pokyny k nástroji pro migraci

V sestavě připravenosti pro Azure pro hodnocení služby Azure VMware Solution (AVS) uvidíte následující navrhované nástroje:

- **VMware HCX nebo Enterprise**: pro servery VMware je řešení Azure Hybrid Cloud Extension (HCX) navrhovaným nástrojem pro migraci, který vaše místní úlohy migruje do privátního cloudu řešení Azure VMware (AVS). [Další informace](../azure-vmware/tutorial-deploy-vmware-hcx.md)
- **Neznámé**: u serverů importovaných prostřednictvím souboru CSV není výchozí nástroj pro migraci známý. I když se pro servery VMware doporučuje používat řešení VMware Hybrid Cloud Extension (HCX).

## <a name="next-steps"></a>Další kroky

Vytvořte posouzení pro [virtuální počítače služby AVS](how-to-create-azure-vmware-solution-assessment.md)pro prostředí VMware.
