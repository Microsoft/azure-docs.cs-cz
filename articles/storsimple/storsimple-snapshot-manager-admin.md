---
title: StorSimple Snapshot Manageru správy | Dokumentace Microsoftu
description: Poskytuje přehled a odkazy na další informace o úlohách správy řešení StorSimple Snapshot Manageru a pracovních postupů.
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
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630015"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Použití StorSimple Snapshot Manager ke správě vašeho řešení StorSimple

## <a name="overview"></a>Přehled
StorSimple Snapshot Manageru je modul snap-in konzoly Microsoft Management Console (MMC), který zjednodušuje ochranu dat a správy zálohování v prostředí Microsoft Azure StorSimple. Pomocí StorSimple Snapshot Manager můžete spravovat Microsoft Azure StorSimple data v datovém centru i v cloudu jako jedné integrované řešení úložiště, tedy zjednodušení procesů zálohování a snížení nákladů.

Konzolu pro centrální správu StorSimple Snapshot Manager umožňuje vytvářet konzistentní vzhledem k aplikacím, v okamžiku záložní kopie místní a Cloudová data. Například můžete použít konzolu pro:

* Konfigurace zálohování a odstraňte svazky.
* Konfigurace skupin svazků, aby zálohovaných dat je konzistentních s aplikací.
* Správa zásad zálohování tak, aby se data zálohují na předem určeného plánu.
* Vytvoření nezávislých kopií dat, která můžete v cloudu pro ukládání a používání pro zotavení po havárii.

Tento článek obsahuje odkazy na kurzy, které popisují StorSimple Snapshot Manageru a jak ji používat k dokončení úlohy systém správy a pracovních postupů.

* Další informace o StorSimple Snapshot Manageru komponent a architektury, najdete v části [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Chcete-li stáhnout StorSimple Snapshot Manageru, přejděte na [stránce pro stažení StorSimple Snapshot Manageru](https://www.microsoft.com/download/details.aspx?id=44220).
* Pro StorSimple Snapshot Manageru postupy nasazení, přejděte na [nasazení StorSimple Snapshot Manageru](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> StorSimple Snapshot Manageru nejde použít pro správu Microsoft Azure StorSimple virtuální pole (označované také jako StorSimple místní virtuální zařízení).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manageru úloh a pracovních postupů
StorSimple Snapshot Manager můžete použít ke sledování a správě aktuální, plánované a dokončení úlohy zálohování. Kromě toho StorSimple Snapshot Manageru poskytuje katalog až 64 dokončené zálohy. Můžete vyhledat a obnovit svazky nebo jednotlivé soubory katalogu. 

| POKUD CHCETE UDĚLAT... | POMOCÍ TOHOTO KURZU... |
|:--- |:--- |
| Další informace o StorSimple Snapshot Manageru |[Co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Nainstalovat Snapshot Manageru zařízení StorSimple<br>Přeinstalujte StorSimple Snapshot Manageru<br>Odebrat StorSimple Snapshot Manageru |[Nasazení StorSimple Snapshot Manageru](storsimple-snapshot-manager-deployment.md) |
| Použití StorSimple Snapshot Manageru nabídek a funkce:<ul><li>Panel nabídek</li><li>Panel nástrojů</li><li>Podokno přehledu</li><li>Podokno výsledků</li><li>Podokna akcí</li><li>Navigace pomocí kláves a klávesové zkratky</li></ul> |[Uživatelské rozhraní StorSimple Snapshot Manageru](storsimple-use-snapshot-manager.md) |
| Použijte běžné funkce konzoly MMC, které jsou zahrnuté ve StorSimple Snapshot Manageru:<ul><li>Zobrazení</li><li>Nové okno</li><li>Obnovení</li><li>Exportovat seznam</li><li>Nápověda</li></ul> |[Použijte nabídku akcí MMC prostřednictvím StorSimple Snapshot Manageru](storsimple-snapshot-manager-mmc-menu.md) |
| Přidat nebo nahradit zařízení<br>Připojení zařízení<br>Ověřte importované svazku skupiny<br>Aktualizovat připojená zařízení<br>Ověřování zařízení<br>Zobrazení podrobností o zařízení<br>Odstranění konfigurace zařízení<br>Změnit heslo zařízení<br>Nahraďte zařízení se nezdařilo<br> |[K připojení a správě zařízení StorSimple pomocí StorSimple Snapshot Manageru](storsimple-snapshot-manager-manage-devices.md) |
| Připojit svazky<br>Zobrazení informací o svazcích<br>Odstranění svazku<br>Znovu prohledat svazky<br>Konfigurace a zálohujte základního svazku<br>Konfigurace a zálohování dynamické zrcadlený svazek |[Pomocí StorSimple Snapshot Manageru k zobrazení a správa svazků](storsimple-snapshot-manager-manage-volumes.md) |
| Zobrazení skupin svazků<br>Vytvoření skupiny svazků<br>Zálohování skupiny svazků<br>Upravit skupiny svazků<br>Odstranění skupiny svazků |[Vytvoření a Správa skupin svazků pomocí StorSimple Snapshot Manageru](storsimple-snapshot-manager-manage-volume-groups.md) |
| Vytvoření zásady zálohování <br>Upravit zásady zálohování<br>Odstraňování zásady zálohování |[Vytvoření a Správa zásad zálohování pomocí StorSimple Snapshot Manageru](storsimple-snapshot-manager-manage-backup-policies.md) |
| Zobrazení a správa naplánovaných úloh zálohování<br>Umožňuje zobrazit a spravovat poslední úlohy zálohování<br>Umožňuje zobrazit a spravovat právě probíhajících úloh zálohování |[K zobrazení a Správa úloh zálohování pomocí StorSimple Snapshot Manageru](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Obnovit svazek<br>Klonovat svazek nebo skupiny svazků<br>Odstranit zálohu<br>Obnovení souboru<br>Obnovení databáze StorSimple Snapshot Manageru |[Použití StorSimple Snapshot Manageru Správa katalogu záloh](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Další postup
[Stáhněte si StorSimple Snapshot Manageru](https://www.microsoft.com/download/details.aspx?id=44220).

