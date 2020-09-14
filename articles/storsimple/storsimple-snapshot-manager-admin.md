---
title: Správa StorSimple Snapshot Manager | Microsoft Docs
description: Obsahuje přehled a odkazy na Další informace o úlohách správy řešení StorSimple Snapshot Manager a pracovních postupech.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: alkohli
ms.openlocfilehash: 16d782c6a25d8d2d8e1323b7cb683f41d8c99649
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054972"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Použití Snapshot Manager StorSimple ke správě řešení StorSimple

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který zjednodušuje správu ochrany a zálohování dat ve Microsoft Azure StorSimplem prostředí. Pomocí StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple data v datovém centru a v cloudu jako jediné integrované řešení úložiště, což zjednodušuje procesy zálohování a snižuje náklady.

Konzola pro správu StorSimple Snapshot Manager slouží k vytváření konzistentních záložních kopií místních a cloudových dat v daném okamžiku. Konzolu můžete například použít k těmto akcím:

* Nakonfigurujte, zálohujte a odstraňte svazky.
* Nakonfigurujte skupiny svazků, aby se zajistilo, že zálohovaná data jsou konzistentní vzhledem k aplikacím.
* Spravujte zásady zálohování tak, aby se data zálohovali podle předem určeného plánu.
* Vytvářejte nezávislé kopie dat, které mohou být uloženy v cloudu a používány pro zotavení po havárii.

Tento článek obsahuje odkazy na kurzy, které popisují StorSimple Snapshot Manager a jejich použití k dokončení úloh a pracovních postupů pro správu systému.

* Další informace o komponentách a architektuře StorSimple Snapshot Manager najdete v tématu [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Pokud chcete stáhnout StorSimple Snapshot Manager, navštivte [stránku StorSimple pro stažení Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Postup nasazení StorSimple Snapshot Manager najdete v [Snapshot Manager nasazení StorSimple](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Snapshot Manager StorSimple nemůžete použít ke správě Microsoft Azure StorSimple virtuálních polí (označují se také jako StorSimple místní virtuální zařízení).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager úlohy a pracovní postupy
Snapshot Manager StorSimple můžete použít ke sledování a správě aktuálních, plánovaných a dokončených úloh zálohování. StorSimple Snapshot Manager navíc poskytuje katalog až 64 dokončených záloh. Katalog můžete použít k vyhledání a obnovení svazků nebo jednotlivých souborů. 

| POKUD TO CHCETE PROVÉST... | POUŽÍT TENTO KURZ... |
|:--- |:--- |
| Další informace o StorSimple Snapshot Manager |[Co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Nainstalovat StorSimple Snapshot Manager<br>Přeinstalace StorSimple Snapshot Manager<br>Odebrat Snapshot Manager StorSimple |[Nasazení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Použití StorSimple Snapshot Manager nabídek a funkcí:<ul><li>Řádek nabídek</li><li>Panel nástrojů</li><li>Podokno oboru</li><li>Podokno výsledků</li><li>Podokno akcí</li><li>Navigace na klávesnici a zástupci</li></ul> |[Uživatelské rozhraní StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Používejte běžné funkce MMC, které jsou součástí StorSimple Snapshot Manager:<ul><li>Zobrazit</li><li>New Window from Here</li><li>Aktualizovat</li><li>Exportovat seznam</li><li>Nápověda</li></ul> |[Použití akcí v nabídce MMC v StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Přidání nebo nahrazení zařízení<br>Připojení zařízení<br>Ověřit importované skupiny svazků<br>Aktualizovat připojená zařízení<br>Ověření zařízení<br>Zobrazení podrobností o zařízení<br>Odstranění konfigurace zařízení<br>Změna hesla zařízení<br>Výměna neúspěšného zařízení<br> |[Připojení a Správa zařízení StorSimple pomocí Snapshot Manager StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Připojit svazky<br>Zobrazit informace o svazcích<br>Odstranění svazku<br>Znovu prohledat svazky<br>Konfigurace a zálohování základního svazku<br>Konfigurace a zálohování dynamického zrcadleného svazku |[Zobrazení a správa svazků pomocí Snapshot Manager StorSimple](storsimple-snapshot-manager-manage-volumes.md) |
| Zobrazit skupiny svazků<br>Vytvoření skupiny svazků<br>Zálohování skupiny svazků<br>Úprava skupiny svazků<br>Odstranění skupiny svazků |[Použití Snapshot Manager StorSimple k vytváření a správě skupin svazků](storsimple-snapshot-manager-manage-volume-groups.md) |
| Vytvoření zásady zálohování <br>Úprava zásady zálohování<br>Odstranění zásady zálohování |[Použití Snapshot Manager StorSimple k vytváření a správě zásad zálohování](storsimple-snapshot-manager-manage-backup-policies.md) |
| Zobrazení a správa naplánovaných úloh zálohování<br>Umožňuje zobrazit a spravovat nedávné úlohy zálohování.<br>Zobrazení a Správa aktuálně spuštěných úloh zálohování |[Zobrazení a správa úloh zálohování pomocí Snapshot Manager StorSimple](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Obnovení svazku<br>Klonování svazku nebo skupiny svazků<br>Odstranění zálohy<br>Obnovení souboru<br>Obnovení databáze Snapshot Manager StorSimple |[Použití Snapshot Manager StorSimple ke správě katalogu záloh](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Další kroky
[Stáhněte StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

