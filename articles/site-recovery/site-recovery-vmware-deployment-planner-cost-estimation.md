---
title: Zkontrolujte sestavu odhad nákladů v Plánovač nasazení služby Azure Site Recovery | Microsoft Docs
description: V tomto článku se dozvíte, jak zkontrolovat sestavu odhadu nákladů v Plánovač nasazení služby Azure Site Recovery pro zotavení po havárii z VMware do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: mayg
ms.openlocfilehash: 1f825b67baf36c9a1a9187d555522f5a5955d1c7
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620072"
---
# <a name="review-the-cost-estimation-report-in-the-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Přečtěte si sestavu odhad nákladů v Site Recovery Plánovač nasazení pro zotavení po havárii VMware do Azure.

Sestava plánovače nasazení obsahuje souhrn odhadu nákladů na listech [Recommendations](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) (Doporučení) a podrobnou analýzu nákladů na listu Cost Estimation (Odhad nákladů). Obsahuje podrobnou analýzu nákladů na virtuální počítač. 

>[!Note]
>Aktuální verze nástroje pro plánování nasazení v 2.5 poskytuje odhad nákladů na virtuální počítače, které se replikují do Managed Disks.

### <a name="cost-estimation-summary"></a>Souhrn odhadu nákladů 
Graf zobrazuje přehled odhadovaných celkových nákladů na zotavení po havárii do Azure ve zvolené cílové oblasti a měnu, kterou jste zadali pro generování sestav.
Souhrn odhadu nákladů

![Souhrn odhadu nákladů](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Tento souhrn pomáhá porozumět nákladům, které budete muset platit za úložiště, výpočetní funkce, sítě a licence při ochraně všech kompatibilní virtuálních počítačů v Azure pomocí Azure Site Recovery. Náklady se počítají pro kompatibilní virtuální počítače, a ne pro všechny profilované virtuální počítače.  
 
Náklady můžete zobrazit po měsících nebo letech. Další informace o [podporovaných cílových oblastech](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) a [podporovaných měnách](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)

**Náklady podle komponent** Celkové náklady na zotavení po havárii se dělí do čtyř komponent: Náklady na výpočetní prostředky, úložiště, síť a Azure Site Recovery licence. Náklady se počítají na základě spotřeby, ke které dojde během replikace a v průběhu nácviku zotavení po havárii pro výpočetní funkce, úložiště (Premium a Standard), síť ExpressRoute nebo VPN, která je nakonfigurovaná mezi místní lokalitou a Azure, a licence Azure Site Recovery.

**Náklady podle stavu** Celkové náklady na zotavení po havárii jsou rozdělené do kategorií na základě dvou různých stavů – replikace a nácvik zotavení po havárii. 

**Náklady na replikaci**:  Náklady, které budou účtovány během replikace. Zahrnují náklady na úložiště, síť a licence Azure Site Recovery. 

**Náklady na postup zotavení po havárii**: Náklady, které budou účtovány během testovacího převzetí služeb při selhání. Během testovacího převzetí služeb při selhání Azure Site Recovery rozjede virtuální počítače. Náklady na nácvik zotavení po havárii zahrnují náklady na výpočetní funkce a úložiště spuštěných virtuálních počítačů. 

**Náklady na úložiště Azure za měsíc a rok:** Celkové náklady na úložiště, které vzniknou pro úložiště úrovně Standard a Premium při replikaci a nácviku zotavení po havárii.

## <a name="detailed-cost-analysis"></a>Podrobná analýza nákladů
Ceny Azure pro výpočetní funkce, úložiště, sítě atd. se v jednotlivých oblastech Azure liší. Sestavu odhadu nákladů můžete vytvořit s nejnovějšími cenami Azure na základě vašeho předplatného a nabídky, která je přidružená k vašemu předplatnému a pro zadanou cílovou oblast Azure v zadané měně. Ve výchozím nastavení tento nástroj jako oblast Azure používá Západní USA 2 a jako měnu americký dolar (USD). Pokud jste použili jinou oblast a měny, při příštím generování sestavy bez ID předplatného, ID nabídky, cílové oblasti a měny se pro odhad nákladů použijí ceny poslední použité cílové oblasti a naposledy použitá měna.
Tato část uvádí ID předplatného a ID nabídky, které jste použili pro generování sestav.  Pokud se nepoužily, je tato část prázdná.

Buňky označené v této sestavě šedě jsou jen pro čtení. Bílé buňky je možné na základě vašich požadavků upravit.

![Podrobný odhad nákladů 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Celkové náklady na zotavení po havárii podle komponent
První část ukazuje celkové náklady na zotavení po havárii podle komponent a náklady na zotavení po havárii podle stavů. 

**Výpočetní**prostředí: Náklady na virtuální počítače s IaaS, které běží v Azure pro potřeby zotavení po havárii. Zahrnují virtuální počítače, které Azure Site Recovery vytvoří během nácviků zotavení po havárii (testovací převzetí služeb při selhání), a virtuální počítače spuštěné v Azure jako SQL Server se skupinami dostupnosti AlwaysOn a řadiče domény / servery DNS.

**Úložiště**: Náklady na spotřebu úložiště Azure pro potřeby zotavení po havárii. Zahrnují spotřebu úložiště pro replikaci a během nácviků zotavení po havárii.
Sítě ExpressRoute a cena sítě na síť VPN pro potřeby zotavení po havárii. 

**Licence ASR**: Azure Site Recovery náklady na licenci pro všechny kompatibilní virtuální počítače. Pokud jste do tabulky s podrobnou analýzou nákladů ručně zadali virtuální počítač, budou v ní také zahrnuté náklady na licence Azure Site Recovery pro tento virtuální počítač.

### <a name="overall-dr-cost-by-states"></a>Celkové náklady na zotavení po havárii podle stavů
Celkové náklady na zotavení po havárii jsou rozdělené do kategorií na základě dvou různých stavů – replikace a nácvik zotavení po havárii.

**Náklady na replikaci**: Náklady vznikly v době replikace. Zahrnují náklady na úložiště, síť a licence Azure Site Recovery. 

**Náklady na postup zotavení po havárii**: Náklady vznikly v době přechodu zotavení po havárii. Během nácviků zotavení po havárii Azure Site Recovery rozjede virtuální počítače. Náklady na nácvik zotavení po havárii zahrnují náklady na výpočetní funkce a úložiště spuštěných virtuálních počítačů.
Celková doba nácviků zotavení po havárii za rok = počet nácviků zotavení po havárii x doba trvání jednotlivých nácviků (dny) Průměrná doba nácviků zotavení po havárii (za měsíc) = celková doba nácviků zotavení po havárii / 12

### <a name="storage-cost-table"></a>Tabulka nákladů na úložiště:
Tato tabulka zobrazuje náklady na úložiště úrovně Standard a Premium pro replikaci a nácviky zotavení po havárii se slevami a bez nich.

### <a name="site-to-azure-network"></a>Síť z lokality do Azure
Vyberte odpovídající nastavení podle vašich požadavků. 

**ExpressRoute**: Ve výchozím nastavení nástroj vybere nejbližší plán ExpressRoute, který odpovídá požadované šířce pásma sítě pro rozdílovou replikaci. Tento plán můžete změnit podle vašich požadavků.

**VPN Gateway**: Vyberte VPN Gateway, pokud máte ve svém prostředí. Ve výchozím nastavení je NA.

**Cílová oblast**: Zadaná oblast Azure pro zotavení po havárii Ceny použité v této sestavě pro výpočetní funkce, úložiště, síť a licence jsou založené na cenách Azure pro příslušnou oblast. 

### <a name="vm-running-on-azure"></a>Virtuální počítač spuštěný v Azure
Pokud máte řadič domény nebo virtuální počítač DNS nebo virtuální počítač s SQL Serverem se skupinami dostupnosti Always On spuštěnými v Azure pro zotavení po havárii, můžete zadat počet virtuálních počítačů a jejich velikost, aby se jejich výpočetní náklady braly v úvahu při výpočtu celkových nákladů na zotavení po havárii. 

### <a name="apply-overall-discount-if-applicable"></a>Apply overall discount if applicable (Použít celkovou slevu, pokud se dá použít)
Toto pole můžete použít, pokud jste zákazník nebo partner Azure a máte nárok na slevu z celkových cen. Nástroj použijte tuto slevu (v %) pro všechny komponenty.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Počet typů virtuálních počítačů a náklady na výpočetní výkon (za rok)
Tato tabulka zobrazuje počet virtuálních počítačů s Windows a virtuálních počítačů s jinou platformou a náklady na nácvik zotavení po havárii pro tyto počítače.

### <a name="settings"></a>Nastavení 

**Měna**: Měna, ve které se sestava generuje Doba trvání nákladů:  Můžete si Zobrazit všechny náklady buď za měsíc, nebo za celý rok. 

## <a name="detailed-cost-analysis-table"></a>Tabulka podrobné analýzy nákladů
![Detailed cost analysis](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) Tato tabulka uvádí rozdělení nákladů pro jednotlivé kompatibilní virtuální počítače. Tuto tabulku můžete také použít k získání odhadovaných nákladů na zotavení po havárii Azure pro neprofilované virtuální počítače, a to ručním přidáním virtuálních počítačů. Je užitečná v případech, kdy potřebujete bez podrobné profilace odhadnout náklady Azure pro nové nasazení zotavení po havárii.
Ruční přidání virtuálních počítačů: 
1.  Kliknutím na Insert row (Vložit řádek) vložte nový řádek mezi řádky Start a End.

2.  Vyplňte následující sloupce na základě přibližné velikosti virtuálních počítačů a počtu virtuálních počítačů, které odpovídají této konfiguraci: 

* Number of VMs, IaaS size (Your selection)
* Storage Type (Standard/Premium)
* VIRTUÁLNÍ počítač: Celková velikost úložiště (GB) zdrojového počítače
* Number of DR drills in a year 
* Each DR drill duration (Days) 
* OS Type
* Data redundancy 
* Zvýhodněné hybridní využití Azure

1. Pokud chcete použít stejnou hodnotu pro všechny virtuální počítače v tabulce, klikněte na tlačítko Apply to all (Použít u všech) pro sloupce DR-Drills in a year, Each DR-Drill duration (Days), Data redundancy a Azure Hybrid Use Benefit.

1. Kliknutím na Re-calculate cost (Přepočítat náklady) aktualizujte náklady.

**Název virtuálního počítače**: Název virtuálního počítače

**Počet virtuálních počítačů**: Počet virtuálních počítačů, které odpovídají konfiguraci. Počet existujících virtuálních počítačů můžete aktualizovat, pokud virtuální počítače podobné konfigurace nejsou profilované, ale budou chráněné.

**Velikost IaaS (doporučení)** : Je to velikost role virtuálního počítače pro kompatibilní virtuální počítač, který nástroj doporučuje. 

**Velikost IaaS (váš výběr)** : Ve výchozím nastavení je stejná jako doporučená velikost role virtuálního počítače. Roli můžete na základě vašich požadavků změnit. Náklady na výpočetní výkon jsou založené na vybrané velikosti role virtuálního počítače.

**Typ úložiště**: Typ úložiště, který je používán virtuálním počítačem. Je to Storage úrovně Standard nebo Premium.

**Celková velikost úložiště virtuálního počítače (GB)** : Celková velikost úložiště zdrojového virtuálního počítače.

**Počet podrobná cvičení pro zotavení po havárii za rok**: Počet pokusů o provedení zotavení po havárii za rok. Ve výchozím nastavení je to 4krát do roka. Můžete změnit období pro konkrétní virtuální počítače nebo použít novou hodnotu pro všechny virtuální počítače, a to zadáním požadované hodnoty do horního řádku a kliknutím na tlačítko Apply to all (Použít u všech). Na základě počtu nácviků zotavení po havárii za rok a délce trvání jednotlivých nácviků se vypočtou celkové náklady na nácvik zotavení po havárii.  

**Doba trvání jednotlivých postupů zotavení po havárii (dny)** : Doba trvání jednotlivých postupů zotavení po havárii. Ve výchozím nastavení je to 7 dnů vždy po 90 dnech, v souladu s [výhodou Disaster Recovery programu Software Assurance](https://azure.microsoft.com/pricing/details/site-recovery). Můžete změnit období pro konkrétní virtuální počítače nebo použít novou hodnotu pro všechny virtuální počítače, a to zadáním této hodnoty do horního řádku a kliknutím na tlačítko Apply to all (Použít u všech). Celkové náklady na nácvik zotavení po havárii se vypočtou na základě počtu nácviků zotavení po havárii za rok a délce trvání jednotlivých nácviků.
  
**Typ operačního systému**: Typ operačního systému virtuálního počítače. Je to Windows nebo Linux. Pokud je jako typ operačního systému nastavený systém Windows, je pro příslušný počítač možné využít Zvýhodněné hybridní využití Azure. 

**Redundance dat**: Může to být jedna z následujících místně redundantního úložiště (LRS), geograficky redundantního úložiště (GRS) nebo geograficky redundantního úložiště s přístupem pro čtení (RA-GRS). Výchozí nastavení je LRS. Můžete změnit typ na základě vašeho účtu úložiště nebo použít nový typ pro všechny virtuální počítače, a to zadáním změnou typu v horním řádku a kliknutím na tlačítko Apply to all (Použít u všech).  Náklady na úložiště pro replikaci se počítají na základě ceny pro redundanci dat, kterou jste vybrali. 

**Zvýhodněné hybridní využití Azure**: V případě potřeby můžete Zvýhodněné hybridní využití Azure použít na virtuální počítače s Windows.  Výchozí hodnota je Yes (Ano). Můžete změnit nastavení pro konkrétní virtuální počítače nebo aktualizovat všechny virtuální počítače kliknutím na tlačítko Apply to all (Použít u všech).

**Celková spotřeba Azure**: Zahrnuje náklady na výpočetní prostředky, úložiště a Azure Site Recovery pro vaše zotavení po havárii. Na základě vašeho výběru se náklady zobrazují jako měsíční nebo roční.

**Náklady na replikaci stabilního stavu**: Zahrnuje náklady na úložiště pro replikaci.

**Celkové náklady na postup zotavení po havárii (průměr)** : Zahrnuje náklady na výpočetní prostředky a úložiště pro zotavení po havárii.

**Náklady na licenci ASR**: Azure Site Recovery náklady na licenci.

## <a name="supported-target-regions"></a>Podporované cílové oblasti
Plánovač nasazení Azure Site Recovery poskytuje odhad nákladů pro následující oblasti Azure. Pokud vaše oblast není uvedená níže, můžete použít některou z následujících oblastí, jejichž ceny jsou nejblíže vaší oblasti.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Podporované měny
Plánovač nasazení Azure Site Recovery může vygenerovat sestavu nákladů s libovolnou z následujících měn.

|Currency|Name||Currency|Name||Currency|Name|
|---|---|---|---|---|---|---|---|
|ARS|Argentinské peso ($)||AUD|Australský dolar ($)||BRL|Brazilský real (R$)|
|CAD|Kanadský dolar ($)||CHF|Švýcarský frank (chf)||DKK|Dánská koruna (kr)|
|EUR|Euro (€)||GBP|Britská libra (£)||HKD|Hongkongský dolar (HK$)|
|IDR|Indonéská rupie (Rp)||INR|Indická rupie (₹)||JPY|Japonský jen (¥)|
|KRW|Korejský won (₩)||MXN|Mexické peso (MXN$)||MYR|Malajský ringit ($)|
|NOK|Norská koruna (kr)||NZD|Novozélandský dolar ($)||RUB|Ruský rubl (руб)|
|SAR|Saúdský rijál (SR)||SEK|Švédská koruna (kr)||TWD|Tchajwanský dolar (NT$)|
|TRY|Turecká lira (TL)||USD| Americký dolar ($)||ZAR|Jihoafrický rand (R)|

## <a name="next-steps"></a>Další kroky
Další informace o ochraně [virtuálních počítačů VMware do Azure s využitím Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure)
