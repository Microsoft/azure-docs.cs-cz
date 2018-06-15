---
title: Správa Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Poskytuje přehled a odkazy na další informace o úlohách správy řešení StorSimple Snapshot Manager a pracovních postupů.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: a99b3d7336c3dc1a1f249915d6971a49f4b69be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875428"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Použití StorSimple Snapshot Manager ke správě vašeho řešení StorSimple

## <a name="overview"></a>Přehled
Snapshot Manager zařízení StorSimple je modul snap-in konzoly Microsoft Management Console (MMC), který zjednodušuje ochrany dat a správu záloh v prostředí Microsoft Azure StorSimple. S StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple data v datovém centru i v cloudu jako řešení jedné integrované úložiště, proto kvůli zjednodušení zálohování procesů a snížení nákladů.

Konzola pro centrální správu StorSimple Snapshot Manager umožňuje vytvářet konzistentní, v okamžiku záložní kopie místní a Cloudová data. Například můžete použít konzolu pro:

* Konfigurace, zálohovat a odstraňte svazky.
* Konfigurace skupin svazku zajistit zálohovaných dat je konzistentní s aplikací.
* Spravovat zásady zálohování tak, aby se data zálohují na předem určeného plánu.
* Vytvořte nezávislé kopie dat, které lze uložených v cloudu a použít pro zotavení po havárii.

Tento článek obsahuje odkazy na výukové programy, které popisují Snapshot Manager zařízení StorSimple a způsobu jeho použití k dokončení úlohy správy systému a pracovních postupů.

* Další informace o součástech Snapshot Manager zařízení StorSimple a architektura najdete v tématu [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Chcete-li stáhnout Snapshot Manager zařízení StorSimple, přejděte na [stránce pro stažení StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Postupy nasazení zařízení StorSimple Snapshot Manager, přejděte na [nasazení zařízení StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> StorSimple Snapshot Manager nelze použít pro správu Microsoft Azure StorSimple virtuální pole (také označované jako StorSimple místní virtuální zařízení).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Úlohy StorSimple Snapshot Manager a pracovních postupů
StorSimple Snapshot Manager můžete použít ke sledování a správě aktuální, plánované a dokončené úlohy zálohování. Kromě toho StorSimple Snapshot Manager nabízí katalog až 64 dokončené záloh. Katalog můžete použít k vyhledání a obnovit svazky nebo jednotlivé soubory. 

| POKUD CHCETE TO UDĚLAT... | TENTO KURZ POUŽIJTE... |
|:--- |:--- |
| Další informace o Snapshot Manager zařízení StorSimple |[Co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Nainstalujte Snapshot Manager zařízení StorSimple<br>Přeinstalujte Snapshot Manager zařízení StorSimple<br>Odebrat Snapshot Manager zařízení StorSimple |[Nasazení Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-deployment.md) |
| Použití StorSimple Snapshot Manager nabídky a funkce:<ul><li>Panel nabídek</li><li>Panel nástrojů</li><li>Podokno oboru</li><li>Podokno výsledků</li><li>Podokna akcí</li><li>Navigace klávesnice a zkratky</li></ul> |[Uživatelské rozhraní Snapshot Manager zařízení StorSimple](storsimple-use-snapshot-manager.md) |
| Použití běžných konzoly MMC funkcí obsažených v StorSimple Snapshot Manager:<ul><li>Zobrazení</li><li>Nové okno</li><li>Aktualizace</li><li>Export seznamu</li><li>Nápověda</li></ul> |[Pomocí akcí nabídce konzoly MMC v Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-mmc-menu.md) |
| Přidat nebo nahradit zařízení<br>Připojení zařízení<br>Ověřte importované svazku skupiny<br>Aktualizujte připojená zařízení<br>Ověřování zařízení<br>Zobrazení podrobností o zařízeních<br>Odstranění konfigurace zařízení<br>Změnit heslo k zařízení<br>Nahraďte zařízení se nezdařilo<br> |[Použít k připojení a správě zařízení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Připojit svazky<br>Zobrazení informací o svazcích<br>Odstranění svazku<br>Prohledat znovu svazky<br>Konfigurace a zálohovat vytvoření základního svazku<br>Konfigurace a zálohování dynamické zrcadleného svazku |[Pomocí StorSimple Snapshot Manager můžete zobrazit a spravovat svazky](storsimple-snapshot-manager-manage-volumes.md) |
| Zobrazit svazku skupiny<br>Vytvořte skupinu svazku<br>Zálohování svazku skupiny<br>Úprava skupiny svazku<br>Odstranit skupinu svazku |[Použít k vytvoření a Správa skupin svazku Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-manage-volume-groups.md) |
| Vytvořit zásady zálohování <br>Upravit zásady zálohování<br>Odstranit zásady zálohování |[Pomocí StorSimple Snapshot Manager vytvořit a spravovat zásady zálohování](storsimple-snapshot-manager-manage-backup-policies.md) |
| Zobrazit a spravovat naplánované úlohy zálohování<br>Zobrazit a spravovat poslední úlohy zálohování<br>Zobrazit a spravovat právě probíhajících úloh zálohování |[Pomocí StorSimple Snapshot Manager můžete zobrazit a spravovat úlohy zálohování](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Obnovení svazku<br>Klonování svazku nebo svazku skupiny<br>Odstranit zálohy<br>Obnovit soubor<br>Obnovit databázi Snapshot Manager zařízení StorSimple |[Použití StorSimple Snapshot Manager ke správě katalogu zálohování](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Další kroky
[Stáhnout Snapshot Manager zařízení StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

