---
title: Přizpůsobení hodnocení pro azure migrate server assessment | Dokumenty společnosti Microsoft
description: Popisuje, jak přizpůsobit hodnocení vytvořená pomocí azure migrate server assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234274"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

Tento článek popisuje, jak přizpůsobit hodnocení vytvořená azure migrate server assessment.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, hodnocení a migrace místních aplikací a úloh a virtuálních počítačích privátního/veřejného cloudu do Azure. Centrum poskytuje nástroje pro migraci Azure pro hodnocení a migraci, stejně jako nabídky nezávislého dodavatele softwaru (ISV) třetích stran.

Nástroj Azure Migrate Server Assessment můžete použít k vytvoření hodnocení pro místní virtuální počítače VMware a virtuální počítače Hyper-V v rámci přípravy na migraci do Azure.

## <a name="about-assessments"></a>O hodnoceních

Existují dva typy hodnocení, které můžete spustit pomocí Azure Migrate Server Assessment.

**Posouzení** | **Podrobnosti** | **Data**
--- | --- | ---
**Na základě výkonu** | Hodnocení založená na shromážděných údajích o výkonnosti | **Doporučená velikost virtuálního počítače:** Na základě dat o využití procesoru a paměti.<br/><br/> **Doporučený typ disku (standardní nebo prémiový spravovaný disk):** Na základě vodítek viopa a propustnost místních disků.
**Jako místní** | Hodnocení založená na místním dimenzování. | **Doporučená velikost virtuálního počítače:** Na základě místní velikosti virtuálního počítače<br/><br> **Doporučený typ disku**: Na základě nastavení typu úložiště, které vyberete pro posouzení.


## <a name="how-is-an-assessment-done"></a>Jak se provádí hodnocení?

Hodnocení provedené v Azure Migrate Server Assessment má tři fáze. Posouzení začíná analýzou vhodnosti, po níž následuje odhad velikosti a nakonec i měsíčního odhadu nákladů. Stroj se přesune do pozdější fáze pouze v případě, že projde předchozí fází. Například pokud počítač selže kontrolu vhodnosti Azure, je označen jako nevhodné pro Azure a velikosti a kalkuje nebude provedeno. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Co je součástí posouzení?

**Vlastnost** | **Podrobnosti**
--- | ---
**Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/> Hodnocení serveru v současné době podporuje tyto cílové oblasti: Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Střední Indie, Střední USA, Čína – východ, Čína – sever, Východní Asie, Východní USA, Východní USA2, Německo – střed, Německo – severovýchod, Japonsko východ, Japonsko Západ, Korea – střed, Korea Jih, Severní Střed USA, Severní Evropa, Jižní Střed USA, Jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Velká Británie – západ, US Gov Arizona, US Gov Texas, US Gov Virginia, West Central US, West Europe, West India, West US a West US2.
**Typ úložiště** | Tuto vlastnost můžete použít k určení typu disků, na které chcete přesunout, v Azure.<br/><br/> Pro místní velikosti můžete určit typ cílového úložiště buď jako disky spravované službou Premium, disky spravované standardem SSD nebo disky se spravovanou disky s pevným diskem Standard HDD. Pro velikost na základě výkonu můžete zadat typ cílového disku buď jako automatické disky spravované službou Premium, disky spravované disky s pevným diskem standardu HDD nebo disky se spravovanou standardním diskem SSD.<br/><br/> Pokud zadáte typ úložiště jako automatický, doporučení disku se provádí na základě dat o výkonu disků (IOPS a propustnost). Pokud zadáte typ úložiště jako premium/standard, posouzení doporučí skladovou položku disku v rámci vybraného typu úložiště. Pokud chcete dosáhnout jedné instance SLA virtuálního počítače 99,9 %, můžete zadat typ úložiště jako disky spravované službou Premium. Tím je zajištěno, že všechny disky v hodnocení jsou doporučeny jako disky spravované službou Premium. Azure
**Rezervované instance (RI)** | Tato vlastnost vám pomůže určit, pokud máte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/) v Azure, odhady nákladů v posouzení se pak provádí s ohledem na slevy RI. Rezervované instance jsou aktuálně podporované jenom pro nabídku průběžných plateb v Azure Migrate.
**Kritérium určení velikosti** | Kritérium, které se má použít pro virtuální počítače správné velikosti pro Azure. Můžete buď provést velikost *na základě výkonu* nebo velikost virtuálních počítače jako *místní*, bez ohledu na historii výkonu.
**Historie výkonu** | Doba, kterou je třeba zvážit pro vyhodnocení údajů o výkonu počítačů. Tato vlastnost je použitelná pouze v případě, že kritérium velikosti je *založeno na výkonu*.
**Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost je použitelná pouze v případě, že je změna velikosti *založena na výkonu*.
**Řada virtuálních počítačů** |     Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Například pokud máte produkční prostředí, které nemáte v plánu migrovat na virtuální počítače řady A v Azure, můžete vyloučit řady A ze seznamu nebo řady a správné velikosti se provádí pouze ve vybrané řadě.
**Faktor komfortu** | Azure Migrate Server Assessment bere v úvahu vyrovnávací paměť (faktor pohodlí) během hodnocení. Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry.
**Nabízejí** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. Azure Migrate odhadne náklady odpovídajícím způsobem.
**Měna** | Fakturační měna.
**Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure.<br/> Výchozí nastavení je 0 %.
**Doba provozu virtuálního počítače** | Pokud vaše virtuální počítače nebudou spuštěny 24 x 7 v Azure, můžete zadat dobu trvání (počet dní za měsíc a počet hodin za den), pro které by se spouštěly a odhady nákladů by se provedly odpovídajícím způsobem.<br/> Výchozí hodnota je 31 dní v měsíci a 24 hodin denně.
**Zvýhodněné hybridní využití Azure** | Určete, zda máte jistotu softwaru a máte nárok na [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. Výchozí hodnota je Yes (Ano).


## <a name="edit-assessment-properties"></a>Upravit vlastnosti hodnocení

Chcete-li upravit vlastnosti hodnocení po vytvoření hodnocení, postupujte takto:

1. V projektu Migrace Azure klikněte na **Servery**.
2. V **Azure Migrate: Vyhodnocení serveru**klikněte na počet hodnocení.
3. V **části Hodnocení**klepněte na příslušné vlastnosti > **upravit**.
5. Přizpůsobte vlastnosti hodnocení v souladu s výše uvedenou tabulkou.
6. Chcete-li aktualizovat hodnocení, klepněte na tlačítko **Uložit.**


Vlastnosti hodnocení můžete také upravit při vytváření hodnocení.


## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
