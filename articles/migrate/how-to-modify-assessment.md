---
title: Přizpůsobení nastavení posouzení Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak nastavit a spustit posouzení pro migraci virtuálních počítačů VMware do Azure s využitím plánovače migrace Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129819"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

[Azure Migrate](migrate-overview.md) vytvoří s výchozí vlastnosti posouzení. Po vytvoření posouzení, můžete upravit výchozí vlastnosti, podle pokynů v tomto článku.


## <a name="edit-assessment-properties"></a>Upravit vlastnosti posouzení

1. Na stránce **Posouzení** projektu migrace vyberte posouzení a klikněte na **Upravit vlastnosti**.
2. Přizpůsobení vlastnostech posouzení na základě následujících informací:

    **Nastavení** | **Podrobnosti** | **Výchozí**
    --- | --- | ---
    **Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/><br/> Azure Migrate v současné době podporuje 30 oblastí, včetně oblastí Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Indie – střed, USA – střed, Čína – východ, Čína – sever, Východní Asie, USA – východ, Německo – střed, Německo – severovýchod, USA – východ 2, Japonsko – východ, Japonsko – západ, Jižní Korea – střed, Jižní Korea – jih, Střed USA – sever, Severní Evropa, Střed USA – jih, Jihovýchodní Asie, Indie – jih, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, Střed USA – západ, Západní Evropa, Indie – západ, Západní USA a Západní USA 2. |  USA – západ 2 je výchozí umístění.
    **Typ úložiště** | Tato vlastnost slouží k určení typu disky, které chcete přesunout v Azure. Pro jako – místní změny velikosti můžete zadat cílový typ disku buď jako Premium managed disks a Standard managed disks. Pro určení velikosti na základě výkonu můžete zadat cílový typ disku jako automatická, Premium managed disks nebo Standard managed disks. Při zadání typu úložiště jako automatické doporučení disku se provádí na základě dat výkonu disků (IOPS a propustnost). Například, pokud chcete dosáhnout [jednu instanci virtuálního počítače SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), můžete chtít zadat typ úložiště jako Premium managed disks. Tím se zajistí, že všechny disky v posouzení se doporučují jako Premium managed disks. Poznámka: Azure Migrate podporuje pro posouzení migrace jenom spravované disky. | Výchozí hodnota je Premium managed disks (s kritérium určení velikosti jako *jako v místním nastavení velikosti*).
    **Rezervované instance** |  Můžete také zadat, jestli v Azure máte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/), a Azure Migrate odhadne náklady odpovídajícím způsobem. Rezervované instance jsou momentálně podporuje jenom pro nabídku průběžných plateb ve službě Azure Migrate. | Výchozí hodnota této vlastnosti je 3 roky, rezervované instance.
    **Kritérium určení velikosti** | Kritérium, podle kterého Azure Migrate určí správnou velikost virtuálních počítačů pro Azure. Můžete provést určení velikosti *na základě výkonu* nebo použít velikost virtuálních počítačů *jako v místním prostředí* bez ohledu na historii výkonu. | Výchozí možnost je určení velikosti na základě výkonu.
    **Historie výkonu** | Doba, která se má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*. | Výchozí hodnota je jeden den.
    **Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*.  | Výchozí hodnota je 95. percentil.
    **Řada virtuálních počítačů** | Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Například pokud máte produkční prostředí, které nechcete migrovat na virtuální počítače řady A-series v Azure, můžete ze seznamu vyloučí řady A-series nebo řady a správné velikosti se provádí pouze v vybranou řadu. | Ve výchozím nastavení jsou vybrány všechny řady virtuálních počítačů.
    **Faktor komfortu** | Azure Migrate při posuzování počítá s rezervou (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. | Výchozí nastavení je 1,3×.
    **Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. | Výchozí hodnota je [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Měna** | Fakturační měna. | Výchozí hodnota je USD.
    **Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure. | Výchozí nastavení je 0 %.
    **Doba provozu virtuálního počítače** | Pokud se vaše virtuální počítače běžet 24 x 7 v Azure, můžete určit dobu trvání (počet dnů za měsíc) a počtu hodin za den pro kterou se bude spuštěna a odhady nákladů, které by se dělalo odpovídajícím způsobem. | Výchozí hodnota je 31 dnů za měsíc a 24 hodin denně.
    **Zvýhodněné hybridní využití Azure** | Zadejte, jestli máte Software Assurance a nárok na [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. | Výchozí hodnota je Yes (Ano).

3. Klikněte na tlačítko **Uložit** aktualizovat posouzení.

## <a name="faqs-on-assessment-properties"></a>Nejčastější dotazy na vlastnosti posouzení

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaký je rozdíl mezi jako on-premises velikost a velikost na základě výkonu?

Pokud určíte kritérium určení velikosti bude jako typu místní – místní změny velikosti, Azure Migrate nebere v úvahu data o výkonu virtuálních počítačů a velikosti virtuálních počítačů založených na místní konfiguraci. Pokud kritérium určení velikosti na základě výkonu, velikost se provádí na základě dat využití. Příkladem je místní virtuální počítač se 4 jádry a 8 GB paměti s 50% využitím procesoru a 50% využitím paměti. Pokud je kritériem určení velikosti místní nastavení velikosti, doporučí se skladová položka virtuálního počítače Azure se 4 jádry a 8 GB paměti. Pokud je však kritériem určení velikosti výkon, doporučí se skladová položka virtuálního počítače se 2 jádry a 4 GB paměti, protože se v doporučení velikosti zohledňuje procentní využití.

Podobně disků, velikosti disku závisí na dvě vlastnosti posouzení – Změna velikosti kritéria a úložiště typu. Pokud je kritériem určení velikosti výkon a typ úložiště se určuje automaticky, při určování typu cílového disku (Standard nebo Premium) se zohlední hodnoty IOPS a propustnosti disku. Pokud je kritériem určení velikosti výkon a typ úložiště je Premium, doporučí se disk Premium a v Azure se vybere skladová položka disku Premium na základě velikosti místního disku. Stejná logika se používá při určování velikosti disku, když je kritériem určení velikosti místní nastavení velikosti a typ úložiště je Standard nebo Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Doporučení velikosti jaký vliv má percentilu a historii využití výkonu?

Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu. Azure Migrate shromažďuje historii výkonu místních počítačů a používá ji k doporučení typu disku a velikosti virtuálního počítače v Azure.

- Zařízení kolektoru průběžně profiluje místní prostředí a každých 20 sekund shromažďuje data o využití v reálném čase.
- Zařízení seskupuje 20sekundové vzorky a pro každých 15 minut vytvoří jeden datový bod. Jeden datový bod se vytvoří tak, že zařízení ze všech 20sekundových vzorků vybere maximální hodnotu a odešle ji do Azure.
- Když v Azure vytvoříte posouzení, Azure Migrate na základě doby výkonu a hodnoty percentilu historie výkonu vypočítá hodnotu efektivního využití, kterou použije k určení velikosti.

Pokud jste například nastavili dobu výkonu na 1 den a hodnotu percentilu na 95. percentil, Azure Migrate použije body 15minutových vzorků odeslané kolektorem za poslední den, seřadí je ve vzestupném pořadí a jako efektivní využití vyberte hodnotu 95. percentilu. Hodnota 95. percentilu zajistí, že se budou ignorovat případné odlehlé hodnoty, které by se mohly zohlednit, kdybyste vybrali 99. percentil. Pokud chcete vybrat maximální využití v určitém období a nechcete přijít o žádné odlehlé hodnoty, měli byste vybrat 99. percentil.

## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
