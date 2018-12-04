---
title: Přizpůsobení nastavení posouzení Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak nastavit a spustit posouzení pro migraci virtuálních počítačů VMware do Azure s využitím plánovače migrace Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2018
ms.author: raynew
ms.openlocfilehash: 2423c4fde177ab50552af580a60c7a15550e5586
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840431"
---
# <a name="customize-an-assessment"></a>Přizpůsobení posouzení

[Azure Migrate](migrate-overview.md) vytvoří s výchozí vlastnosti posouzení. Po vytvoření posouzení, můžete upravit výchozí vlastnosti, podle pokynů v tomto článku.


## <a name="edit-assessment-properties"></a>Upravit vlastnosti posouzení

1. Na stránce **Posouzení** projektu migrace vyberte posouzení a klikněte na **Upravit vlastnosti**.
2. Upravit vlastnosti posouzení podle níže požadované údaje:

    **Nastavení** | **Podrobnosti** | **Výchozí**
    --- | --- | ---
    **Cílové umístění** | Umístění Azure, do kterého chcete migrovat.<br/><br/> Azure Migrate v současné době podporuje 30 oblastí, včetně oblastí Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Kanada – střed, Kanada – východ, Indie – střed, USA – střed, Čína – východ, Čína – sever, Východní Asie, USA – východ, Německo – střed, Německo – severovýchod, USA – východ 2, Japonsko – východ, Japonsko – západ, Jižní Korea – střed, Jižní Korea – jih, Střed USA – sever, Severní Evropa, Střed USA – jih, Jihovýchodní Asie, Indie – jih, Velká Británie – jih, Velká Británie – západ, US Gov – Arizona, US Gov – Texas, US Gov – Virginie, Střed USA – západ, Západní Evropa, Indie – západ, USA – západ a USA – západ 2. |  USA – západ 2 je výchozí umístění.
    **Typ úložiště** | Tato vlastnost slouží k určení typu disky, které chcete přidělit v Azure. Pro jako – místní změny velikosti můžete zadat cílový typ disku buď jako Premium managed disks a Standard managed disks. Pro určení velikosti na základě výkonu můžete zadat cílový typ disku jako automatická, Premium managed disks nebo Standard managed disks. Při zadání typu úložiště jako automatické doporučení disku se provádí na základě dat výkonu disků (IOPS a propustnost). Například, pokud chcete dosáhnout [jednu instanci virtuálního počítače SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), můžete chtít zadat typ úložiště jako spravované disky úrovně Premium, které zajistí, že všechny disky v posouzení se doporučují jako spravované disky úrovně Premium. Poznámka: Azure Migrate podporuje pro posouzení migrace jenom spravované disky. | Výchozí hodnota je spravované disky úrovně Premium (s kritérium určení velikosti jako *jako v místním nastavení velikosti*).
    **Rezervované instance** |  Můžete také zadat, jestli v Azure máte [rezervované instance](https://azure.microsoft.com/pricing/reserved-vm-instances/), a Azure Migrate odhadne náklady odpovídajícím způsobem. Rezervované instance se nevztahují na suverénních oblastech (Azure Government, Německo a Čína) a se vztahuje pouze na nabídku průběžných plateb ve službě Azure Migrate. | Výchozí hodnota této vlastnosti je 3 roky, rezervované instance.
    **Kritérium určení velikosti** | Kritérium, podle kterého Azure Migrate určí správnou velikost virtuálních počítačů pro Azure. Můžete provést určení velikosti *na základě výkonu* nebo použít velikost virtuálních počítačů *jako v místním prostředí* bez ohledu na historii výkonu. | Výchozí možnost je určení velikosti na základě výkonu.
    **Historie výkonu** | Doba, která se má zohlednit při vyhodnocování výkonu virtuálních počítačů. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*. | Výchozí hodnota je jeden den.
    **Percentilové využití** | Hodnota percentilu sady vzorků výkonu, která se má zohlednit při určování správné velikosti. Tato vlastnost se dá použít pouze v případě, že kritériem určení velikosti je *určení velikosti na základě výkonu*.  | Výchozí hodnota je 95. percentil.
    **Řada virtuálních počítačů** | Můžete zadat řadu virtuálních počítačů, pro kterou chcete zvážit nastavení správné velikosti. Pokud máte například produkční prostředí, které se nechystáte migrovat na virtuální počítače řady A v Azure, můžete vyloučit řadu A ze seznamu nebo řad a nastavení správné velikosti se provede jen pro vybrané řady. | Ve výchozím nastavení jsou vybrány všechny řady virtuálních počítačů.
    **Faktor komfortu** | Azure Migrate při posuzování počítá s rezervou (faktor komfortu). Tato rezerva se použije nad rámec dat o využití počítače pro virtuální počítače (procesor, paměť, disk a síť). Důvodem použití faktoru komfortu jsou problémy, jako jsou sezónní využití, krátká historie výkonu a pravděpodobný růst budoucího využití.<br/><br/> Například z virtuálního počítače s 10 jádry a 20% využitím je normálně ve výsledku virtuální počítač se 2 jádry. S faktorem komfortu 2,0× je však výsledkem virtuální počítač se 4 jádry. | Výchozí nastavení je 1,3×.
    **Nabídka** | [Nabídka Azure](https://azure.microsoft.com/support/legal/offer-details/), kterou máte zaregistrovanou. | Výchozí hodnota je [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Měna** | Fakturační měna. | Výchozí hodnota je USD.
    **Sleva (%)** | Jakákoli sleva pro konkrétní předplatné, kterou získáte nad rámec nabídky Azure. | Výchozí nastavení je 0 %.
    **Doba provozu virtuálního počítače** | Pokud se vaše virtuální počítače běžet 24 x 7 v Azure, můžete určit dobu trvání (počet dnů za měsíc) a počtu hodin za den pro kterou se bude spuštěna a odhad nákladů se provede odpovídajícím způsobem. | Výchozí hodnota je 31 dnů za měsíc a 24 hodin denně.
    **Zvýhodněné hybridní využití Azure** | Zadejte, jestli máte Software Assurance a nárok na [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Pokud je hodnota nastavená na Yes (Ano), u virtuálních počítačů s Windows se použijí ceny za Azure pro jiný systém než Windows. | Výchozí hodnota je Yes (Ano).

3. Klikněte na tlačítko **Uložit** aktualizovat posouzení.


## <a name="next-steps"></a>Další postup

[Přečtěte si další informace](concepts-assessment-calculation.md) o tom, jak se v rámci posouzení počítají náklady.
