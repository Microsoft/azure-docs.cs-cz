---
title: Jak nahradit páskovou infrastrukturu
description: Zjistěte, jak Azure Backup poskytuje sémantiku jako na pásce, která vám umožní zálohovat a obnovovat data v Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: aeda1cefc84d425855c40b793f8334936541e63f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425098"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Přesunutí dlouhodobého úložiště z pásky do cloudu Azure

Zákazníci Azure Backup and System Center Data Protection Manager můžou:

* Zálohujte data v plánech, které nejlépe vyhovují potřebám organizace.
* Uchovávejte záložní data po delší dobu.
* Vytvořte Azure součástí jejich dlouhodobé potřeby uchovávání (namísto pásky).

Tento článek vysvětluje, jak mohou zákazníci povolit zásady zálohování a uchovávání informací. Zákazníci, kteří používají pásky k řešení svých dlouhodobých potřeb uchovávání informací, mají nyní schůdnou alternativu s dostupností této funkce. Tato funkce je povolená v nejnovější verzi azure backup (která je k dispozici [zde](https://aka.ms/azurebackup_agent)). Zákazníci system center DPM musí před použitím aplikace DPM se službou Azure Backup aktualizovat alespoň na dpm 2012 R2 UR5.

## <a name="what-is-the-backup-schedule"></a>Co je plán zálohování?

Plán zálohování označuje frekvenci operace zálohování. Nastavení na následující obrazovce například označují, že zálohy jsou prováděny denně v 18:00 a o půlnoci.

![Denní plán](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Zákazníci mohou také naplánovat týdenní zálohování. Nastavení na následující obrazovce například označují, že zálohy jsou přijímána každou alternativní neděli & středu v 9:30 a 1:00.

![Týdenní plán](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Co jsou zásady uchovávání informací?

Zásady uchovávání informací určují dobu, po kterou musí být záloha uložena. Místo pouhého zadání "ploché zásady" pro všechny záložní body mohou zákazníci zadat různé zásady uchovávání informací na základě způsobu, kdy je záloha provedena. Například bod zálohování přijatý denně, který slouží jako provozní bod obnovení, je zachován po dobu 90 dnů. Bod zálohování přijatý na konci každého čtvrtletí pro účely auditu je zachován po delší dobu.

![Zásady uchovávání](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Celkový počet "retenčních bodů" uvedených v této politice je 90 (denní body) + 40 (jedno každé čtvrtletí po dobu 10 let) = 130.

## <a name="example--putting-both-together"></a>Příklad – Dát oba dohromady

![Ukázková obrazovka](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Denní zásady uchovávání informací**: Zálohy pořízené denně jsou uloženy po dobu sedmi dnů.
2. **Týdenní zásady uchovávání informací**: Zálohy pořízené o půlnoci a 18:00 v sobotu jsou zachovány po dobu čtyř týdnů.
3. **Měsíční zásady uchovávání informací**: Zálohy pořízené o půlnoci a 18:00 poslední sobotu v měsíci jsou zachovány po dobu 12 měsíců.
4. **Roční zásady uchovávání informací**: Zálohy pořízené o půlnoci poslední sobotu každého března jsou zachovány po dobu 10 let.

Celkový počet "retenčních bodů" (body, ze kterých může zákazník obnovit data) v předchozím diagramu se vypočítá takto:

* dva body denně po dobu sedmi dnů = 14 bodů obnovení
* dva body týdně po dobu čtyř týdnů = 8 bodů zotavení
* dva body za měsíc po dobu 12 měsíců = 24 bodů zotavení
* jeden bod za rok za 10 let = 10 bodů výtěžnosti

Celkový počet bodů obnovení je 56.

> [!NOTE]
> Pomocí služby Azure Backup můžete vytvořit až 9999 bodů obnovení na chráněnou instanci. Chráněná instance je počítač, server (fyzický nebo virtuální) nebo úloha zálohující do Azure.
>

## <a name="advanced-configuration"></a>Pokročilá konfigurace

Kliknutím na **tlačítko Změnit** na předchozí obrazovce mají zákazníci další flexibilitu při zadávání plánů uchovávání informací.

![Modify](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Backup najdete v tématu:

* [Seznámení s Azure Backup](backup-introduction-to-azure-backup.md)
* [Vyzkoušejte zálohování Azure](backup-try-azure-backup-in-10-mins.md)
