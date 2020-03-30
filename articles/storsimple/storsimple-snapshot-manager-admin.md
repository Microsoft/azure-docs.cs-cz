---
title: Správa správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Obsahuje přehled a odkazy na další informace o úlohách správy a pracovních postupech správce snímků StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: twooley
ms.openlocfilehash: b8fe87a2df633af310bc661308813a60987e77d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933281"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Správa řešení StorSimple pomocí správce snímků StorSimple

## <a name="overview"></a>Přehled
StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který zjednodušuje ochranu dat a správu zálohování v prostředí Microsoft Azure StorSimple. Pomocí Správce snímků StorSimple můžete spravovat data Microsoft Azure StorSimple v datovém centru a v cloudu jako jediné integrované úložné řešení, čímž zjednodušíte procesy zálohování a snížíte náklady.

Centrální konzola pro správu Správce snímků StorSimple umožňuje vytvářet konzistentní záložní kopie místních a cloudových dat v okamžiku. Konzolu můžete například použít k:

* Konfigurace, zálohování a odstranění svazků.
* Nakonfigurujte skupiny svazků, abyste zajistili, že zálohovaná data budou konzistentní s aplikací.
* Spravujte zásady zálohování tak, aby data byla zálohována podle předem stanoveného plánu.
* Vytvořte nezávislé kopie dat, které mohou být uloženy v cloudu a použity pro zotavení po havárii.

Tento článek obsahuje odkazy na kurzy, které popisují StorSimple Snapshot Manager a jak ji použít k dokončení úloh správy systému a pracovních postupů.

* Další informace o součástech a architektuře Správce snímků StorSimple naleznete v tématu [Co je Správce snímků StorSimple?](storsimple-what-is-snapshot-manager.md) 
* Chcete-li stáhnout StorSimple Snapshot Manager, přejděte na [stránku pro stažení Správce snímků StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).
* Procedury nasazení správce snímků StorSimple najdete v části [Nasazení správce snímků StorSimple](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Správce snímků StorSimple nelze použít ke správě virtuálních polí Microsoft Azure StorSimple (označovaných také jako místní virtuální zařízení StorSimple).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Úlohy a pracovní postupy správce snímků StorSimple
Správce snímků StorSimple můžete použít ke sledování a správě aktuálních, naplánovaných a dokončených úloh zálohování. Kromě toho Správce snímků StorSimple poskytuje katalog až 64 dokončených záloh. Katalog můžete použít k vyhledání a obnovení svazků nebo jednotlivých souborů. 

| JESTLI TO CHCEŠ UDĚLAT... | POUŽÍT TENTO KURZ... |
|:--- |:--- |
| Další informace o Správci snímků StorSimple |[Co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Instalace Správce snímků StorSimple<br>Přeinstalovat Správce snímků StorSimple<br>Odebrat Správce snímků StorSimple |[Nasazení Správce snímků StorSimple](storsimple-snapshot-manager-deployment.md) |
| Použijte nabídky a funkce Správce snímků StorSimple:<ul><li>Nabídek</li><li>Panel nástrojů</li><li>Podokno Obor</li><li>Podokno Výsledky</li><li>Podokno Akce</li><li>Navigace na klávesnici a klávesové zkratky</li></ul> |[Uživatelské rozhraní Správce snímků StorSimple](storsimple-use-snapshot-manager.md) |
| Použijte běžné funkce konzoly MMC zahrnuté ve Správci snímků StorSimple:<ul><li>Zobrazení</li><li>New Window from Here</li><li>Obnovení</li><li>Exportovat seznam</li><li>Nápověda</li></ul> |[Použití akcí nabídky konzoly MMC ve Správci snímků StorSimple](storsimple-snapshot-manager-mmc-menu.md) |
| Přidání nebo nahrazení zařízení<br>Připojení zařízení<br>Ověření importovaných skupin svazků<br>Aktualizace připojených zařízení<br>Ověření zařízení<br>Zobrazení podrobností o zařízení<br>Odstranění konfigurace zařízení<br>Změna hesla zařízení<br>Nahrazení neúspěšného zařízení<br> |[Použití Správce snímků StorSimple k připojení a správě zařízení StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Připojit svazky<br>Zobrazení informací o svazcích<br>Odstranění svazku<br>Opětovné prohledání svazků<br>Konfigurace a zálohování základního svazku<br>Konfigurace a zálohování dynamického zrcadleného svazku |[Zobrazení a správa svazků pomocí Správce snímků StorSimple](storsimple-snapshot-manager-manage-volumes.md) |
| Zobrazit skupiny svazků<br>Vytvoření skupiny svazků<br>Zálohování skupiny svazků<br>Úprava skupiny svazků<br>Odstranění skupiny svazků |[Vytvoření a správa skupin svazků pomocí Správce snímků StorSimple](storsimple-snapshot-manager-manage-volume-groups.md) |
| Vytvoření zásad zálohování <br>Úprava zásad zálohování<br>Odstranění zásad zálohování |[Vytvoření a správa zásad zálohování pomocí správce snímků StorSimple](storsimple-snapshot-manager-manage-backup-policies.md) |
| Zobrazení a správa naplánovaných úloh zálohování<br>Zobrazení a správa posledních úloh zálohování<br>Zobrazení a správa aktuálně spuštěné úlohy zálohování |[Použití Správce snímků StorSimple k zobrazení a správě úloh zálohování](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Obnovení svazku<br>Klonování svazku nebo skupiny svazků<br>Odstranění zálohy<br>Obnovení souboru<br>Obnovení databáze Správce snímků StorSimple |[Správa katalogu záloh pomocí Správce snímků StorSimple](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Další kroky
[Stáhnout StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

