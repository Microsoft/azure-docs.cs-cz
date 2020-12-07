---
title: Přizpůsobení hodnocení pro Azure Migrate Server Assessment | Microsoft Docs
description: Popisuje, jak přizpůsobit hodnocení vytvořená pomocí posouzení serveru Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: de90cffe8760a55aafebf079678d139c6f6f99e1
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751100"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

Tento článek popisuje, jak přizpůsobit hodnocení vytvořená Azure Migrate posouzení serveru.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Nástroj pro vyhodnocení Azure Migrate serveru můžete použít k vytvoření posouzení pro místní virtuální počítače VMware a virtuálních počítačů Hyper-V v přípravě na migraci do Azure. Nástroj pro vyhodnocení serveru posuzuje místní servery pro migraci na virtuální počítače Azure IaaS a řešení Azure VMware (AVS). 

## <a name="about-assessments"></a>O posouzení

Posouzení, která vytvoříte pomocí posouzení serveru, jsou snímkem dat k určitému bodu v čase. Existují dva typy hodnocení, které můžete vytvořit pomocí Azure Migrate: posouzení serveru.

**Typ posouzení** | **Podrobnosti**
--- | --- 
**Virtuální počítač Azure** | Posouzení vhodnosti místních serverů k migraci na virtuální počítače Azure. <br/><br/> Pomocí tohoto typu posouzení můžete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md), [virtuální počítače Hyper-V](how-to-set-up-appliance-hyper-v.md)a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci do Azure. (concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Posouzení vhodnosti místních serverů k migraci do služby [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pomocí tohoto typu posouzení můžete posoudit vhodnost místních [virtuálních počítačů VMware](how-to-set-up-appliance-vmware.md) k migraci do služby Azure VMware Solution (AVS). [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

Posouzení virtuálního počítače Azure v posouzení serveru poskytuje dvě možnosti pro kritéria změny velikosti:

**Kritéria stanovení velikosti** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení, která poskytují doporučení na základě shromážděných údajů o výkonu | **Posouzení virtuálních počítačů Azure:** Doporučení velikosti virtuálních počítačů vychází z dat o využití procesoru a paměti.<br/><br/> Doporučení typu disků (disky HDD nebo SSD úrovně Standard nebo spravované disky úrovně Premium) vychází z IOPS a propustnosti místních disků.<br/><br/> **Posouzení služby Azure VMware Solution (AVS):** Doporučení uzlů AVS vychází z dat o využití procesoru a paměti.
**Jako v místním prostředí** | Posouzení, která k poskytování doporučení nepoužívají údaje o výkonu. | **Posouzení virtuálních počítačů Azure:** Doporučení velikosti virtuálních počítačů vychází z velikosti místních virtuálních počítačů.<br/><br> Doporučení typu disků vychází z výběru nastavení typu úložiště pro posouzení.<br/><br/> **Posouzení služby Azure VMware Solution (AVS):** Doporučení uzlů AVS vychází z velikosti místních virtuálních počítačů.


## <a name="how-is-an-assessment-done"></a>Jak se provádí posouzení?

Posouzení provedené v Azure Migrate posouzení serveru má tři fáze. Posouzení začíná analýzou vhodnosti, za kterou následuje Změna velikosti a nakonec, Odhad měsíčních nákladů. Počítač se přesune pouze do pozdější fáze, pokud předá předchozí. Pokud například počítač nepovede kontrolu vhodnosti Azure, je označený jako nevhodný pro Azure a změna velikosti a oceňování se neprovede. [Další informace](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Co je ve službě Azure VM Assessment?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/> Posouzení serveru aktuálně podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed Indie, Střed USA, Čína – východ, Čína – sever, Východní Asie, Východní USA, východní USA 2, Německo – střed, Německo – západ, Japonsko – východ, Japonsko – západ, Jižní Asie, Střed USA – sever, Severní Evropa, Střed USA – jih, Jižní Indie , US Gov – Texas, US Gov – Virginie, Středozápadní USA, Západní Evropa, Západní Indie, Západní USA a západní USA 2.
**Typ úložiště** | Tato vlastnost slouží k určení typu disků, na které chcete přejít v Azure.<br/><br/> Pro určení velikosti v místním prostředí můžete zadat cílový typ úložiště buď jako disky spravované na úrovni Premium, SSD úrovně Standard spravované disky nebo HDD úrovně Standardy spravované disky. Pro určení velikosti na základě výkonu můžete určit typ cílového disku buď jako automatické disky spravované na úrovni Premium, HDD úrovně Standard disky spravované systémem nebo pro SSD úrovně Standard spravované disky.<br/><br/> Když zadáte typ úložiště jako automatický, doporučení na disku se provede na základě údajů o výkonu disků (IOPS a propustnost). Pokud zadáte typ úložiště jako Premium/Standard, vyhodnocování bude doporučit SKU disku v rámci vybraného typu úložiště. Pokud chcete dosáhnout smlouvy SLA pro virtuální počítače s jednou instancí 99,9%, můžete zadat typ úložiště jako disky spravované na úrovni Premium. Tím se zajistí, že se všechny disky v posouzení doporučují jako disky spravované na úrovni Premium. Azure
**Rezervované instance (RI)** | Tato vlastnost vám pomůže určit, jestli máte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure, odhad nákladů v posouzení se pak provede s využitím slev vyhrazené na rezervované instance. Rezervované instance se momentálně podporují jenom pro nabídky s průběžnými platbami v Azure Migrate.
**Kritérium určení velikosti** | Kritérium, které se má použít ke správné velikosti virtuálních počítačů pro Azure. Virtuální počítače můžete buď měnit podle *výkonu* , nebo měnit jejich velikost *jako v* místním prostředí, aniž byste museli zvážit historii výkonu.
**Historie výkonu** | Doba, kterou je třeba zvážit při vyhodnocování dat výkonu počítačů. Tato vlastnost je k dispozici pouze v případě, že kritérium velikosti je *založeno na výkonu*.
**Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost je k dispozici pouze v případě, že je nastavena velikost *na základě výkonu*.
**Řada virtuálních počítačů** |     Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Pokud máte například produkční prostředí, které neplánujete migrovat na virtuální počítače řady A-Series v Azure, můžete vyloučit řady-Series ze seznamu nebo řady a správná velikost se provádí pouze ve vybraných řadách.
**Faktor komfortu** | Vyhodnocování Azure Migrate serveru během posuzování považuje vyrovnávací paměť (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Fakturační měna.
**Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure.<br/> Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud vaše virtuální počítače nefungují nepřetržitě v Azure, můžete zadat dobu trvání (počet dní za měsíc a počet hodin za den), pro které by byly spuštěné, a odhad nákladů se provede odpovídajícím způsobem.<br/> Výchozí hodnota je 31 dní za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určete, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. Výchozí hodnota je Yes (Ano).

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Co je v posouzení řešení Azure VMware (AVS)?

Co je je součástí posouzení služby AVS při vyhodnocování serveru:


| **Vlastnost** | **Podrobnosti** |
| - | - |
| **Cílové umístění** | Určuje umístění privátního cloudu AVS, do kterého chcete migrovat.<br/><br/> Posouzení pro funkci AVS v nástroji Server Assessment aktuálně podporuje tyto cílové oblasti: Východní USA, Západní Evropa Západní USA. |
| **Typ úložiště** | Určuje modul úložiště, který se má používat v rámci služby AVS.<br/><br/> Služba AVS Assessments podporuje jenom síti vSAN jako výchozí typ úložiště. |
**Rezervované instance (RIs)** | Tato vlastnost vám pomůže určit rezervované instance v funkci AVS. Pro uzly služby AVS se aktuálně nepodporují rezervované instance. |
**Typ uzlu** | Určuje [typ uzlu AVS](../azure-vmware/concepts-private-clouds-clusters.md) , který se používá k mapování místních virtuálních počítačů. Všimněte si, že výchozí typ uzlu je AV36. <br/><br/> Azure Migrate doporučí, aby se virtuální počítače přenesly do služby AVS požadovaným počtem uzlů. |
**Nastavení FTT, úroveň RAID** | Určuje použitou chybu při tolerování a kombinacích RAID. Vybraná možnost FTT v kombinaci s požadavkem na místní disk virtuálních počítačů určí celkové úložiště síti vSAN, které se v prostředí AVS vyžaduje. |
**Kritérium určení velikosti** | Nastaví kritéria, která se mají použít ke _správné velikosti_ virtuálních počítačů pro funkci AVS. Můžete se rozhodnout pro přizpůsobení velikosti na _základě výkonu_ nebo _jako místní,_ aniž byste museli zvážit historii výkonu. |
**Historie výkonu** | Nastaví dobu trvání, která se má vzít v úvahu při vyhodnocování dat výkonu počítačů. Tato vlastnost je platná pouze v případě, že kritéria změny velikosti jsou _založená na výkonu_. |
**Percentilové využití** | Určuje hodnotu percentilu sady ukázek výkonu, která má být považována za správné určení velikosti. Tato vlastnost je platná pouze v případě, že je velikost na základě výkonu.|
**Faktor komfortu** | Vyhodnocování Azure Migrate serveru během posuzování považuje vyrovnávací paměť (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. |
**Nabídka** | Zobrazí [nabídku Azure](https://azure.microsoft.com/support/legal/offer-details/) , kterou jste si zaregistrovali. Azure Migrate odhadne náklady odpovídajícím způsobem.|
**Měna** | Zobrazuje fakturační měnu vašeho účtu. |
**Sleva (%)** | Obsahuje seznam všech slev specifických pro předplatné, které obdržíte nad nabídkou Azure. Výchozí nastavení je 0 %. |
**Zvýhodněné hybridní využití Azure** | Určuje, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). I když to nemá žádný vliv na ceny řešení Azure VMware z důvodu ceny založené na uzlu, zákazníci můžou dál používat licence na Prem pro operační systémy (založené na Microsoftu) v prostředí AVS pomocí hybridních výhod Azure. Ostatní dodavatelé softwaru pro operační systémy budou muset zadat vlastní licenční podmínky, například RHEL. |
**vCPU – předplatné** | Určuje poměr počtu virtuálních jader vázaných na 1 fyzickou jader v uzlu AVS. Výchozí hodnota ve výpočtech je 4 vCPU: 1 fyzická jádro v funkci AVS. <br/><br/> Uživatelé rozhraní API můžou tuto hodnotu nastavit jako celé číslo. Všimněte si, že vCPU > 4:1 může začít způsobovat snížení výkonu, ale dá se použít pro úlohy typu webový server. |

## <a name="edit-assessment-properties"></a>Upravit vlastnosti posouzení

Chcete-li upravit vlastnosti posouzení po vytvoření posouzení, postupujte následovně:

1. V projektu Azure Migrate klikněte na možnost **servery**.
2. V **Azure Migrate: vyhodnocování serveru** klikněte na čítač posouzení.
3. V části **posouzení** klikněte na příslušné vyhodnocení > **Upravit vlastnosti**.
5. Upravte vlastnosti posouzení podle výše uvedených tabulek.
6. Kliknutím na **Uložit** aktualizujte posouzení.


Můžete také upravit vlastnosti posouzení při vytváření posouzení.


## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace](concepts-assessment-calculation.md) o výpočtu odhadů virtuálních počítačů Azure.
- [Přečtěte si víc](concepts-azure-vmware-solution-assessment-calculation.md) o tom, jak se vypočítávají vyhodnocení funkce AVS.
