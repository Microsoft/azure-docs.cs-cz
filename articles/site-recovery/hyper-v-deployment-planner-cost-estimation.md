---
title: Projděte si Azure Site Recovery Deployment Planner nákladů sestavy odhadu zotavení po havárii virtuálních počítačů Hyper-V do Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak kontrolovat náklady na odhad sestava vygenerována Azure Site Recovery Deployment Planner pro zotavení po havárii Hyper-V do Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 03f71e4e10b65d9f295e13cdd8bc6881622c523d
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320273"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Sestava odhadu nákladů z Plánovače nasazení služby Azure Site Recovery 

Sestava Plánovače nasazení služby Azure Site Recovery obsahuje souhrn odhadu nákladů na listech [Recommendations](hyper-v-deployment-planner-analyze-report.md#recommendations) (Doporučení) a podrobnou analýzu nákladů na listu Cost Estimation (Odhad nákladů). Obsahuje podrobnou analýzu nákladů na virtuální počítač. 

### <a name="cost-estimation-summary"></a>Souhrn odhadu nákladů 
Graf zobrazuje přehled odhadovaných celkových nákladů na zotavení po havárii do Azure ve zvolené cílové oblasti a měnu, kterou jste zadali pro generování sestav.

![Souhrn odhadu nákladů](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Tento souhrn pomáhá porozumět nákladům, které budete muset platit za úložiště, výpočetní prostředky, síť a licenci při ochraně kompatibilní virtuálních počítačů pomocí Azure Site Recovery. Náklady se počítají pro kompatibilní virtuální počítače, a ne pro všechny profilované virtuální počítače. 
 
Náklady můžete zobrazit po měsících nebo letech. Další informace o [podporovaných cílových oblastech](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) a [podporovaných měnách](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies)

**Náklady podle komponent**: Celkové náklady na zotavení po Havárii se dělí do čtyř komponent: licenční náklady na výpočetní prostředky, úložiště, síť a Site Recovery. Náklady se počítají na základě spotřeby, ke které dojde během replikace a v průběhu postupu zotavení po havárii. K výpočtům se používají výpočetní prostředky, úložiště (Premium a Standard), síť ExpressRoute nebo VPN, která je nakonfigurovaná mezi místní lokalitou a Azure, a licence Site Recovery.

**Náklady podle stavu**: Kategorie celkové náklady na zotavení je založen na dvou různých stavů: replikace a zotavení po Havárii zotavení po havárii. 

**Náklady na replikaci**: Náklady, které vzniknou během replikace. Zahrnují náklady na úložiště, síť a licenci Site Recovery. 

**DR-Drill cost**: Náklady, které vzniknou během testovacího převzetí služeb při selhání. Během testovacího převzetí služeb při selhání Site Recovery rozjede virtuální počítače. Náklady na postup zotavení po havárii zahrnují náklady na výpočetní prostředky a úložiště spuštěných virtuálních počítačů. 

**Náklady na úložiště Azure za měsíc a rok**: Celkové náklady na úložiště, které vzniknou pro úrovně premium a standard úložiště pro replikaci a Nácviku zotavení.

## <a name="detailed-cost-analysis"></a>Podrobná analýza nákladů
Ceny Azure za výpočetní prostředky, úložiště a síť se v jednotlivých oblastech Azure liší. Sestavu odhadu nákladů můžete vytvořit s nejnovějšími cenami Azure na základě vašeho předplatného, nabídky přidružené k vašemu předplatnému a zadané cílové oblasti Azure v zadané měně. Ve výchozím nastavení tento nástroj jako oblast Azure používá Západní USA 2 a jako měnu americký dolar (USD). Pokud použijete jinou oblast a měnu, při příštím generování sestavy bez ID předplatného, ID nabídky, cílové oblasti a měny použije nástroj pro odhad nákladů ceny platné pro poslední použitou cílovou oblast a poslední použitou měnu.

Tato část uvádí ID předplatného a ID nabídky, které jste použili pro generování sestav. Pokud jste je nepoužili, je tato část prázdná.

Buňky označené v této sestavě šedě jsou jen pro čtení. Bílé buňky je možné upravit podle vašich požadavků.

![Podrobný odhad nákladů](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Celkové náklady na zotavení po havárii podle komponent
První část ukazuje celkové náklady na zotavení po havárii podle komponent a náklady na zotavení po havárii podle stavů. 

**COMPUTE**: Náklady na spuštění v Azure pro potřeby zotavení po Havárii virtuálních počítačů IaaS. Zahrnují virtuální počítače, které Site Recovery vytvoří během postupů zotavení po havárii (testovací převzetí služeb při selhání). Zahrnují také virtuální počítače spuštěné v Azure, jako je SQL Server, se skupinami dostupnosti AlwaysOn a řadiče domény nebo servery DNS.

**Úložiště**: Náklady na spotřebu úložiště Azure pro potřeby zotavení po Havárii. Zahrnují spotřebu úložiště pro replikaci a během nácviků zotavení po havárii.

**Síť**: ExpressRoute a site-to-site VPN náklady pro potřeby zotavení po Havárii. 

**Licence Azure Site Recovery**: Licenční náklady na Site Recovery pro všechny kompatibilní virtuální počítače. Pokud jste do tabulky s podrobnou analýzou nákladů ručně zadali virtuální počítač, budou v ní zahrnuté také náklady na licence Site Recovery pro tento virtuální počítač.

### <a name="overall-dr-costs-by-states"></a>Celkové náklady na zotavení po havárii podle stavů
Celkové náklady na zotavení po havárii jsou rozdělené do kategorií na základě dvou různých stavů: replikace a postup zotavení po havárii.

**Replikace**: Náklady, které vzniknou během replikace. Zahrnují náklady na úložiště, síť a licenci Site Recovery. 

**DR-Drill**: Náklady, které vzniknou během postupů zotavení po Havárii. Během postupů zotavení po havárii Site Recovery rozjede virtuální počítače. Náklady na postup zotavení po havárii zahrnují náklady na výpočetní prostředky a úložiště spuštěných virtuálních počítačů.

* Celková doba postupů zotavení po havárii za rok = počet postupů zotavení po havárii × doba trvání jednotlivých postupů (dny)
* Průměrné náklady na postupy zotavení po havárii (za měsíc) = celkové náklady na postupy zotavení po havárii / 12

### <a name="storage-cost-table"></a>Tabulka nákladů na úložiště
Tato tabulka zobrazuje náklady na úložiště úrovně Standard a Premium pro replikaci a postupy zotavení po havárii se slevami a bez nich.

### <a name="site-to-azure-network"></a>Síť z lokality do Azure
Vyberte odpovídající nastavení podle vašich požadavků. 

**ExpressRoute**: Nástroj ve výchozím nastavení, vybere nejbližší plán ExpressRoute, který odpovídá požadované šířce pásma pro rozdílovou replikaci. Tento plán můžete změnit podle vašich požadavků.

**Typ brány VPN Gateway**: Azure VPN Gateway vyberte, pokud máte ve vašem prostředí. Ve výchozím nastavení je NA.

**Cílová oblast**: Zadaná oblast Azure pro zotavení po Havárii. Ceny použité v této sestavě pro výpočetní funkce, úložiště, síť a licence jsou založené na cenách Azure pro příslušnou oblast. 

### <a name="vm-running-on-azure"></a>Virtuální počítač spuštěný v Azure
Možná máte řadič domény nebo virtuální počítač DNS nebo virtuální počítač s SQL Serverem se skupinami dostupnosti Always On spuštěnými v Azure pro zotavení po havárii. Můžete zadat počet virtuálních počítačů a jejich velikost, aby se jejich výpočetní náklady braly v úvahu při výpočtu celkových nákladů na zotavení po havárii. 

### <a name="apply-overall-discount-if-applicable"></a>Apply overall discount if applicable (Použít celkovou slevu, pokud se dá použít)
Toto pole můžete použít, pokud jste zákazník nebo partner Azure a máte nárok na slevu z celkových cen. Nástroj použijte tuto slevu (v %) pro všechny komponenty.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Počet typů virtuálních počítačů a náklady na výpočetní výkon (za rok)
Tato tabulka zobrazuje počet virtuálních počítačů s Windows a virtuálních počítačů s jinou platformou a výpočetní náklady na postup zotavení po havárii pro tyto počítače.

### <a name="settings"></a>Nastavení 
**Použití spravovaného disku**: Toto nastavení určuje, jestli se během postupů zotavení po Havárii používá spravovaný disk. Výchozí hodnota je **Yes** (Ano). Pokud nastavíte **-UseManagedDisks** na **No**, pro výpočet nákladů se použije cena za nespravovaný disk.

**Měna**: Měna, ve kterém se generuje sestava.

**Cost duration**: Můžete zobrazit náklady, měsíc nebo za celý rok. 

## <a name="detailed-cost-analysis-table"></a>Tabulka podrobné analýzy nákladů
![Podrobná analýza nákladů](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

Tato tabulka uvádí rozpis nákladů pro jednotlivé kompatibilní virtuální počítače. Tuto tabulku můžete použít také k získání odhadovaných nákladů na zotavení po havárii Azure pro neprofilované virtuální počítače, a to ručním přidáním virtuálních počítačů. Tyto informace jsou užitečné v případech, kdy potřebujete bez podrobné profilace odhadnout náklady Azure pro nové nasazení zotavení po havárii.

Ruční přidání virtuálních počítačů:

1. Výběrem možnosti **Insert row** (Vložit řádek) vložte nový řádek mezi řádky **Start** a **End**.

1. Vyplňte následující sloupce na základě přibližné velikosti virtuálních počítačů a počtu virtuálních počítačů, které odpovídají této konfiguraci: 

    a. **Number of VMs**

    b. **IaaS size (Your selection)**

    c. **Storage type Standard/Premium**

    d. **VM total storage size (GB)**

    e. **Number of DR-Drills in a year**

    f. **Each DR-Drill duration (Days)**

    g. **OS Type**

    h. **Data redundancy**

    i. **Azure Hybrid Use Benefit**

1. Pokud chcete použít stejnou hodnotu pro všechny virtuální počítače v tabulce, vyberte **Apply to all** (Použít u všech) pro sloupce **Number of DR-Drills in a year**, **Each DR-Drill duration (Days)**, **Data redundancy** a **Azure Hybrid Use Benefit**.

1. Výběrem možnosti **Re-calculate cost** (Přepočítat náklady) aktualizujte náklady.

**Název virtuálního počítače**: Název virtuálního počítače.

**Počet virtuálních počítačů**: Počet virtuálních počítačů, které odpovídají konfiguraci. Počet existujících virtuálních počítačů můžete aktualizovat, pokud podobná konfigurace virtuálních počítačů není profilovaná, ale je chráněná.

**IaaS size (Recommendation)**: Velikost role virtuálního počítače pro kompatibilní virtuální počítač, který nástroj doporučuje. 

**Velikost IaaS (Your selection)**: Výchozí velikost je stejná jako doporučená velikost role virtuálního počítače. Roli můžete na základě vašich požadavků změnit. Náklady na výpočetní výkon jsou založené na vybrané velikosti role virtuálního počítače.

**Typ úložiště**: Typ úložiště, které je virtuální počítač využívá. Je to Storage úrovně Standard nebo Premium.

**Velikost virtuálního počítače celková velikost úložiště (GB)**: Celková velikost úložiště virtuálního počítače.

**Počet postupů zotavení po Havárii za rok**: Počet, kolikrát je provést zotavení po Havárii za rok. Ve výchozím nastavení je to čtyřikrát za rok. Můžete změnit období pro konkrétní virtuální počítače nebo použít novou hodnotu pro všechny virtuální počítače. Zadejte novou hodnotu do horního řádku a vyberte **Apply to all** (Použít u všech). Na základě počtu postupů zotavení po havárii za rok a délky trvání jednotlivých postupů se vypočítají celkové náklady na postupy zotavení po havárii. 

**Každý DR-Drill duration (Days)**: Doba trvání jednotlivých zotavení po Havárii. Ve výchozím nastavení je to 7 dnů vždy po 90 dnech, v souladu s [výhodou Software Assurance pro zotavení po havárii](https://azure.microsoft.com/pricing/details/site-recovery). Můžete změnit období pro konkrétní virtuální počítače nebo použít novou hodnotu pro všechny virtuální počítače. Zadejte novou hodnotu do horního řádku a vyberte **Apply to all** (Použít u všech). Celkové náklady na postupy zotavení po havárii se vypočítají na základě počtu postupů zotavení po havárii za rok a délky trvání jednotlivých postupů.
 
**Typ operačního systému**: Typ operačního systému (OS) virtuálního počítače. Je to Windows nebo Linux. Pokud je jako typ operačního systému nastavený systém Windows, je pro příslušný počítač možné využít Zvýhodněné hybridní využití Azure. 

**Redundanci dat**: Může být místně redundantní úložiště, geograficky redundantní úložiště nebo geograficky redundantní úložiště jen pro čtení. Výchozí je místně redundantní úložiště. V závislosti na vašem účtu úložiště můžete změnit typ pro konkrétní virtuální počítače nebo použít nový typ pro všechny virtuální počítače. Změňte typ v horním řádku a vyberte **Apply to all** (Použít u všech). Náklady na úložiště pro replikaci se počítají na základě ceny pro redundanci dat, kterou jste vybrali. 

**Program Azure Hybrid Use Benefit**: Azure Hybrid Use Benefit můžete použít k virtuálním počítačům s Windows, pokud je k dispozici. Výchozí hodnota je **Yes** (Ano). Můžete změnit nastavení pro konkrétní virtuální počítače nebo aktualizovat všechny virtuální počítače. Vyberte **Apply to all** (Použít u všech).

**Total Azure consumption**: Výpočetní prostředky, úložiště a náklady na licence Site Recovery pro vaše zotavení po Havárii. Na základě vašeho výběru se náklady zobrazují jako měsíční nebo roční.

**Náklady na replikaci stálé**: Náklady na úložiště pro replikaci.

**Total DR-Drill cost (average)**: Náklady výpočetní výkon a úložiště pro nácvik zotavení po Havárii.

**Náklady na licence Azure Site Recovery**: Licence Site Recovery nákladů.

## <a name="supported-target-regions"></a>Podporované cílové oblasti
Plánovač nasazení služby Site Recovery poskytuje odhad nákladů pro následující oblasti Azure. Pokud tady není uvedená vaše oblast, můžete použít některou z následujících oblastí, jejichž ceny jsou nejblíže vaší oblasti:

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Podporované měny
Plánovač nasazení služby Site Recovery může vygenerovat sestavu nákladů s libovolnou z následujících měn.

|Měna|Název||Měna|Název||Měna|Název|
|---|---|---|---|---|---|---|---|
|ARS|Argentinské peso ($)||AUD|Australský dolar ($)||BRL|Brazilský real (R$)|
|CAD|Kanadský dolar ($)||CHF|Švýcarský frank (chf)||DKK|Dánská koruna (kr)|
|EUR|Euro (€)||GBP|Britská libra (l)||HKD|Hongkongský dolar (HK$)|
|IDR|Indonéská rupie (Rp)||INR|Indická rupie (₹)||JPY|Japonský (y)|
|KRW|Korejština won (₩)||MXN|Mexické peso (MX$)||MYR|Malajsijský ringgit (RM$)|
|NOK|Norská koruna (kr)||NZD|Novozélandský dolar ($)||RUB|Ruský rubl (руб)|
|SAR|Saúdský rijál (SR)||SEK|Švédská koruna (kr)||TWD|Tchajwanský dolar (NT$)|
|TRY|Turecká lira (TL)||USD| Americký dolar ($)||ZAR|Jihoafrický rand (R)|

## <a name="next-steps"></a>Další postup
Další informace o ochraně [virtuálních počítačů Hyper-V do Azure s využitím Site Recovery](hyper-v-azure-tutorial.md)
