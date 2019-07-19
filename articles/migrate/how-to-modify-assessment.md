---
title: Přizpůsobení hodnocení pro Azure Migrate Server Assessment | Microsoft Docs
description: Popisuje, jak přizpůsobit hodnocení vytvořená pomocí posouzení serveru Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234274"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

Tento článek popisuje, jak přizpůsobit hodnocení vytvořená Azure Migrate posouzení serveru.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Nástroj pro vyhodnocení Azure Migrate serveru můžete použít k vytvoření posouzení pro místní virtuální počítače VMware a virtuálních počítačů Hyper-V v přípravě na migraci do Azure.

## <a name="about-assessments"></a>O posouzení

Existují dva typy posouzení, které můžete spustit pomocí Azure Migrate posouzení serveru.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Posouzení na základě shromážděných dat o výkonu | **Doporučená velikost virtuálního počítače**: Na základě dat využití procesoru a paměti.<br/><br/> **Doporučený typ disku (spravovaný disk Standard nebo Premium)** : Na základě vstupně-výstupních operací a propustnosti místních disků.
**Jako místní** | Posouzení na základě místních velikostí. | **Doporučená velikost virtuálního počítače**: V závislosti na velikosti místního virtuálního počítače<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které jste vybrali pro posouzení.


## <a name="how-is-an-assessment-done"></a>Jak se provádí posouzení?

Posouzení provedené v Azure Migrate posouzení serveru má tři fáze. Posouzení začíná analýzou vhodnosti, za kterou následuje Změna velikosti a nakonec, Odhad měsíčních nákladů. Počítač se přesune pouze do pozdější fáze, pokud předá předchozí. Pokud například počítač nepovede kontrolu vhodnosti Azure, je označený jako nevhodný pro Azure a změna velikosti a oceňování se neprovede. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/> Posouzení serveru aktuálně podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střed Indie, Střed USA, Čína – východ, Čína – sever, Východní Asie, Východní USA, východní USA 2, Německo – střed, Německo – jihovýchod, Japonsko – východ, Japonsko – západ, Jižní Korea, Korea – jih, Severní Střed USA, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, Středozápadní USA, Západní Evropa, Západní Indie, Západní USA a západní USA 2.
**Typ úložiště** | Tato vlastnost slouží k určení typu disků, na které chcete přejít v Azure.<br/><br/> U místních velikostí můžete určit typ cílového úložiště buď jako disky spravované na úrovni Premium, SSD úrovně Standard disky spravované nebo HDD úrovně Standard spravované disky. Pro určení velikosti na základě výkonu můžete určit typ cílového disku buď jako automatické disky spravované na úrovni Premium, HDD úrovně Standard disky spravované systémem nebo pro SSD úrovně Standard spravované disky.<br/><br/> Když zadáte typ úložiště jako automatický, doporučení na disku se provede na základě údajů o výkonu disků (IOPS a propustnost). Pokud zadáte typ úložiště jako Premium/Standard, vyhodnocování bude doporučit SKU disku v rámci vybraného typu úložiště. Pokud chcete dosáhnout smlouvy SLA pro virtuální počítače s jednou instancí 99,9%, můžete zadat typ úložiště jako disky spravované na úrovni Premium. Tím se zajistí, že se všechny disky v posouzení doporučují jako disky spravované na úrovni Premium. Azure
**Rezervované instance (RI)** | Tato vlastnost vám pomůže určit, jestli máte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure, odhad nákladů v posouzení se pak provede s využitím slev vyhrazené na rezervované instance. Rezervované instance se momentálně podporují jenom pro nabídky s průběžnými platbami v Azure Migrate.
**Kritérium určení velikosti** | Kritérium, které se má použít ke správné velikosti virtuálních počítačů pro Azure. Virtuální počítače můžete buď měnit podle *výkonu* , nebo měnit jejich velikost *jako v*místním prostředí, aniž byste museli zvážit historii výkonu.
**Historie výkonu** | Doba, kterou je třeba zvážit při vyhodnocování dat výkonu počítačů. Tato vlastnost je k dispozici pouze v případě, že kritérium velikosti je *založeno na výkonu*.
**Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost je k dispozici pouze v případě, že je nastavena velikost *na základě výkonu*.
**Řada virtuálních počítačů** |     Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Pokud máte například produkční prostředí, které neplánujete migrovat na virtuální počítače řady A-Series v Azure, můžete vyloučit řady-Series ze seznamu nebo řady a správná velikost se provádí pouze ve vybraných řadách.
**Faktor komfortu** | Vyhodnocování Azure Migrate serveru během posuzování považuje vyrovnávací paměť (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Fakturační měna.
**Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure.<br/> Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud vaše virtuální počítače nefungují nepřetržitě v Azure, můžete zadat dobu trvání (počet dní za měsíc a počet hodin za den), pro které by byly spuštěné, a odhad nákladů se provede odpovídajícím způsobem.<br/> Výchozí hodnota je 31 dní za měsíc a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určete, jestli máte program Software Assurance a máte nárok na [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. Výchozí hodnota je Yes (Ano).


## <a name="edit-assessment-properties"></a>Upravit vlastnosti posouzení

Chcete-li upravit vlastnosti posouzení po vytvoření posouzení, postupujte následovně:

1. V projektu Azure Migrate klikněte na možnost **servery**.
2. V **Azure Migrate: Vyhodnocování**serveru klikněte na čítač hodnocení.
3. V části **posouzení**klikněte na příslušné vyhodnocení > **Upravit vlastnosti**.
5. Upravte vlastnosti posouzení podle výše uvedené tabulky.
6. Kliknutím na **Uložit** aktualizujte posouzení.


Můžete také upravit vlastnosti posouzení při vytváření posouzení.


## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
