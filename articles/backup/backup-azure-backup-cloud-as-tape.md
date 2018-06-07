---
title: Použití služby Azure Backup k náhradě páskové infrastruktury
description: Zjistěte, jak Azure Backup poskytuje sémantiku podobné pásku, která umožňuje zálohování a obnovení dat v Azure
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/10/2017
ms.author: saurse
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff30dd0e4c7cadabddbeddc38c28a773db68d8ff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606490"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Přesunout do cloudu Azure a dlouhodobého úložiště z pásky
Může Azure zákazníky zálohování a System Center Data Protection Manager:

* Zálohování dat v plány, které co nejlépe vyhovovaly potřebám organizace.
* Zachovat zálohovaná data delší
* Ujistěte se, Azure, které jsou součástí jejich dlouhodobé uchovávání musí (namísto pásku).

Tento článek vysvětluje, jak zákazníci povolit zásady zálohování a uchovávání. Zákazníci, kteří používají pásky k vyřešení jejich dlouhodobý-dlouhodobé uchovávání musí mít teď výkonný a přijatelná alternativa k dispozici tato funkce. Tato funkce je povolena v nejnovější verzi služby Azure Backup (která je k dispozici [sem](http://aka.ms/azurebackup_agent)). System Center DPM zákazníci musí aktualizovat, minimálně, DPM 2012 R2 UR5 před použitím aplikace DPM pomocí služby Azure Backup.

## <a name="what-is-the-backup-schedule"></a>Co je plán zálohování?
Plán zálohování určuje četnost zálohování. Nastavení na následující obrazovce například znamenat, že jsou provedeny zálohy denně v 18: 00 a půlnoci.

![Denní plán](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Zákazníci můžete také naplánovat týdenní zálohování. Nastavení na následující obrazovce například znamenat, že zálohy se pořídí každých alternativní neděle & středa na 9:30:00 a 1:00:00.

![Týdenní plán](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co je zásady uchovávání informací?
Zásady uchovávání informací Určuje dobu, pro které musí být uložen zálohování. Místo zadání "ploché zásady" pro všechny body zálohy, zákazníky, můžete zadat různé zásady uchovávání informací podle toho, kdy dochází k zálohování. Zálohování bod pořizovat denně, který slouží jako bod provozní obnovení, je třeba zachovaná 90 dní. Bod zálohy prováděné na konci každé čtvrtletí pro účely auditu je zachovaná delší dobu.

![Zásady uchovávání informací](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Celkový počet "body uchovávání informací" uvedenými v této zásadě je 90 (denní body) + 40 (jeden pro každé čtvrtletí 10 let) = 130.

## <a name="example--putting-both-together"></a>Příklad – obě připravuje umístění
![Ukázka obrazovky](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Denní zásady uchovávání informací**: zálohy pořizovat denně jsou uložené sedm dní.
2. **Týdenní zásady uchovávání informací**: zálohy pořízené každý den o půlnoci a 18: 00 Sobota se zachovají pro čtyři týdny
3. **Měsíční zásady uchovávání informací**: zálohy pořízené o půlnoci a 18: 00 na poslední sobotu každý měsíc zachovány po dobu 12 měsíců
4. **Roční zásady uchovávání informací**: zálohy pořízené o půlnoci poslední sobotu každých března zachovány 10 let.

Celkový počet "body uchovávání informací" (body, ze kterých může zákazník obnovení dat) na předchozím obrázku je vypočítán takto:

* dva body na každý den pro sedm dní = 14 bodů obnovení
* dva body za týden pro čtyři týdny = 8 bodů obnovení
* dva body USD měsíčně po 12 měsíců = 24 bodů obnovení
* jeden bod za rok a na 10 let = 10 obnovení body

Celkový počet bodů obnovení je 56.

> [!NOTE]
> Zálohování Azure nemá omezení počtu bodů obnovení.
>
>

## <a name="advanced-configuration"></a>Pokročilá konfigurace
Kliknutím na **upravit** na předchozí obrazovce zákazníci mají další flexibilitu při zadání plány uchovávání informací.

![Úpravy](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Další kroky
Další informace o zálohování Azure najdete v tématu:

* [Seznámení s Azure Backup](backup-introduction-to-azure-backup.md)
* [Vyzkoušejte zálohování Azure](backup-try-azure-backup-in-10-mins.md)
