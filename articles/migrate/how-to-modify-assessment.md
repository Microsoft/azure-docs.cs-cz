---
title: Přizpůsobit Azure migrovat nastavení assessment | Microsoft Docs
description: Popisuje, jak nastavit a spustit posouzení pro migraci virtuálních počítačů VMware do Azure pomocí Azure Plánovač migrace
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/03/2018
ms.author: raynew
ms.openlocfilehash: 5054da16a6a02dddb8539011d3baa18f2bb9914a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

[Azure migrací](migrate-overview.md) vytvoří posuzování s výchozí vlastnosti. Po vytvoření posouzení, můžete upravit výchozí vlastnosti pomocí pokynů v tomto článku.


## <a name="edit-assessment-properties"></a>Upravit vlastnosti hodnocení

1. Na stránce **Posouzení** projektu migrace vyberte posouzení a klikněte na **Upravit vlastnosti**.
2. Upravte vlastnosti podle následující tabulky:

    **Nastavení** | **Podrobnosti** | **Výchozí**
    --- | --- | ---
    **Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/><br/> Azure migrací aktuálně podporuje 30 oblastí, včetně Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, Střední Kanada, Východní Kanada, střed, střed USA, východní Čína, severní Čína, východní Asie, východní USA, Německo centrální, Německo – severovýchod, východní USA 2, Japonsko – Východ, Japonsko – Západ, Korejská – střed, Korejská – Jih, střed USA – sever, Severní Evropa, střed USA – Jih, jihovýchodní Asie, – Jih, Indie, Spojené království – Jih, Spojené království – Západ, Virginia verze pro státní správu verze pro státní správu Texas, USA verze pro státní správu Arizona, USA USA, střed USA – Západ, západní Evropa, Západní Indie, západní USA a západní US2. |  Západní USA 2 je výchozí umístění.
    **Redundance úložiště** | Typ redundance úložiště, který budou po migraci využívat virtuální počítače Azure. | Výchozí hodnota je [Místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md). Azure podporuje pouze migrací spravovaných disků na základě hodnocení a spravované disky se podporují jenom LRS, proto vlastnost aktuálně má jenom možnost LRS.
    **Kritérium určení velikosti** | Kritérium, podle kterého Azure Migrate určí správnou velikost virtuálních počítačů pro Azure. Můžete provést určení velikosti *na základě výkonu* nebo použít velikost virtuálních počítačů *jako v místním prostředí* bez ohledu na historii výkonu. | Výchozí možnost je určení velikosti na základě výkonu.
    **Historie výkonu** | Doba, která se má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*. | Výchozí hodnota je jeden den.
    **Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*.  | Výchozí hodnota je 95. percentil.
    **Virtuální počítač řady** | Můžete zadat řady virtuálních počítačů, která se má vzít v úvahu pro optimalizaci velikosti. Například pokud máte provozního prostředí, neplánujete migraci virtuálních počítačů A-series v Azure, A-series můžete vyloučit ze seznamu nebo řad a optimalizaci velikosti bude třeba provést pouze na vybrané řady. | Ve výchozím nastavení jsou vybrané všechny řady virtuálních počítačů.
    **Cenová úroveň** | Můžete zadat [cenovou úroveň (Basic nebo Standard)](../virtual-machines/windows/sizes-general.md) cílových virtuálních počítačů Azure. Pokud například plánujete migrovat produkční prostředí, měli byste zvážit úroveň Standard, která poskytuje virtuální počítače s nízkou latencí, ale může být dražší. Na druhou stranu, pokud máte prostředí pro vývoj a testování, měli byste zvážit úroveň Basic s virtuálními počítači s vyšší latencí, která je levnější. | Ve výchozím nastavení se použije úroveň [Standard](../virtual-machines/windows/sizes-general.md).
    **Faktor komfortu** | Azure Migrate při posuzování počítá s rezervou (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. | Výchozí nastavení je 1,3×.
    **Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. | Výchozí hodnota je [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Měna** | Fakturační měna. | Výchozí hodnota je USD.
    **Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure. | Výchozí nastavení je 0 %.
    **Zvýhodněné hybridní využití Azure** | Zadejte, jestli máte Software Assurance a nárok na [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. | Výchozí hodnota je Yes (Ano).

3. Klikněte na tlačítko **Uložit** aktualizovat hodnocení.


## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
