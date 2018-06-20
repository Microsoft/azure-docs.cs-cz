---
title: Přizpůsobit Azure migrovat nastavení assessment | Microsoft Docs
description: Popisuje, jak nastavit a spustit posouzení pro migraci virtuálních počítačů VMware do Azure pomocí Azure Plánovač migrace
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 4e7decc34105b02be51f002e1951145759a9f46e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231521"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

[Azure migrací](migrate-overview.md) vytvoří posuzování s výchozí vlastnosti. Po vytvoření posouzení, můžete upravit výchozí vlastnosti pomocí pokynů v tomto článku.


## <a name="edit-assessment-properties"></a>Upravit vlastnosti hodnocení

1. Na stránce **Posouzení** projektu migrace vyberte posouzení a klikněte na **Upravit vlastnosti**.
2. Upravte vlastnosti podle následující tabulky:

    **Nastavení** | **Podrobnosti** | **Výchozí**
    --- | --- | ---
    **Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/><br/> Azure Migrate v současné době podporuje 30 oblastí, včetně oblastí Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Indie – střed, USA – střed, Čína – východ, Čína – sever, Východní Asie, USA – východ, Německo – střed, Německo – severovýchod, USA – východ 2, Japonsko – východ, Japonsko – západ, Korea – střed, Korea – jih, Střed USA – sever, Severní Evropa, Střed USA – jih, Jihovýchodní Asie, Indie – jih, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, Střed USA – západ, Západní Evropa, Indie – západ, USA – západ a USA – západ 2. |  Západní USA 2 je výchozí umístění.
    **Typ úložiště** | Zadaný typ disky, které chcete přidělit v Azure. Tuto vlastnost lze použít při nastavení velikosti kritérium je jako nastavení velikosti na místě. Cílový typ disku můžete zadat buď jako Premium spravované discích spravovaných pomocí disků nebo Standard. Pro nastavení velikosti na základě výkonu, se provádí doporučení disku automaticky na základě dat výkonu virtuálních počítačů. Všimněte si, že Azure migrace podporuje pouze spravované disky posouzení migrace. | Výchozí hodnota je spravovaná prémiové disky (s kritériem pro změnu velikosti jako *jako místní nastavení velikosti*).
    **Kritérium určení velikosti** | Kritérium, podle kterého Azure Migrate určí správnou velikost virtuálních počítačů pro Azure. Můžete provést určení velikosti *na základě výkonu* nebo použít velikost virtuálních počítačů *jako v místním prostředí* bez ohledu na historii výkonu. | Výchozí možnost je určení velikosti na základě výkonu.
    **Historie výkonu** | Doba, která se má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*. | Výchozí hodnota je jeden den.
    **Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*.  | Výchozí hodnota je 95. percentil.
    **Řada virtuálních počítačů** | Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Pokud máte například produkční prostředí, které se nechystáte migrovat na virtuální počítače řady A v Azure, můžete vyloučit řadu A ze seznamu nebo řad a nastavení správné velikosti se provede jen pro vybrané řady. | Ve výchozím nastavení jsou vybrané všechny řady virtuálních počítačů.
    **Cenová úroveň** | Můžete zadat [cenovou úroveň (Basic nebo Standard)](../virtual-machines/windows/sizes-general.md) cílových virtuálních počítačů Azure. Pokud například plánujete migrovat produkční prostředí, měli byste zvážit úroveň Standard, která poskytuje virtuální počítače s nízkou latencí, ale může být dražší. Na druhou stranu, pokud máte prostředí pro vývoj a testování, měli byste zvážit úroveň Basic s virtuálními počítači s vyšší latencí, která je levnější. | Ve výchozím nastavení se použije úroveň [Standard](../virtual-machines/windows/sizes-general.md).
    **Faktor komfortu** | Azure Migrate při posuzování počítá s rezervou (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. | Výchozí nastavení je 1,3×.
    **Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. | Výchozí hodnota je [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Měna** | Fakturační měna. | Výchozí hodnota je USD.
    **Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure. | Výchozí nastavení je 0 %.
    **Zvýhodněné hybridní využití Azure** | Zadejte, jestli máte Software Assurance a nárok na [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. | Výchozí hodnota je Yes (Ano).

3. Klikněte na tlačítko **Uložit** aktualizovat hodnocení.


## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
