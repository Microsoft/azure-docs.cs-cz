---
title: Správy pro service Manager zařízení StorSimple | Microsoft Docs
description: Informace o správě zařízení StorSimple pomocí služby StorSimple Manager zařízení na portálu Azure.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875036"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Použít službu StorSimple Manager zařízení ke správě zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje rozhraní služby StorSimple Manager zařízení, včetně toho, jak se připojit k jeho, různé možnosti, které jsou k dispozici a odkazy na konkrétní pracovní postupy, které je možné provádět prostřednictvím tohoto uživatelského rozhraní. Tyto pokyny platí pro obě; fyzického zařízení StorSimple a zařízení cloudu.

Po přečtení tohoto článku, se naučíte:

* Připojení ke službě StorSimple Manager zařízení
* Spravovat zařízení prostřednictvím služby StorSimple Manager zařízení StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Připojení ke službě StorSimple Manager zařízení

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Centrální portálu Microsoft Azure spuštěnému v prohlížeči používáte ke správě těchto zařízení. Pro připojení ke službě StorSimple Manager zařízení, postupujte takto.

#### <a name="to-connect-to-the-service"></a>Pro připojení ke službě
1. Přejděte na [https://portal.azure.com/](https://portal.azure.com/).
2. Pomocí svých přihlašovacích údajů účtu Microsoft, přihlaste se k portálu Microsoft Azure (nachází se v pravé horní části podokna).
3. Přejděte dolů v levém navigačním podokně přístup ke službě StorSimple Manager zařízení.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Spravovat zařízení StorSimple pomocí služby StorSimple Manager zařízení

Následující tabulka obsahuje souhrn všechny běžné úlohy správy a komplexní pracovní postupy, které lze provést v rámci služby Správce zařízení StorSimple uživatelského rozhraní. Tyto úlohy jsou uspořádány podle uživatelského rozhraní okna, ve kterých se spouští.

Další informace o každém pracovním postupu klikněte na tlačítko vhodný postup uvedený v tabulce.

#### <a name="storsimple-device-manager-workflows"></a>Pracovní postupy portálu Manager zařízení StorSimple

| Pokud chcete to udělat... | Pomocí tohoto postupu. |
| --- | --- |
| Vytvoření služby</br>Odstranění služby</br>Získat registrační klíč služby</br>Znovu vygenerovat registrační klíč služby |[Nasazení služby Správce zařízení StorSimple](storsimple-8000-manage-service.md) |
| Zobrazit protokoly aktivity |[Použít souhrn služby StorSimple Manager zařízení](storsimple-8000-service-dashboard.md) |
| Změna šifrovacího klíče dat služby</br>Zobrazit protokoly operací |[Pomocí řídicího panelu služby StorSimple Manager zařízení](storsimple-8000-service-dashboard.md) |
| Deaktivace zařízení</br>Odstranění zařízení |[Deaktivovat nebo odstranit zařízení](storsimple-8000-deactivate-and-delete-device.md) |
| Další informace o havárii obnovení a zařízení převzetí služeb při selhání</br>Převzetí služeb při selhání fyzického zařízení</br>Převzetí služeb při selhání virtuálního zařízení</br>Obchodní kontinuity zotavení po havárii (BCDR) |[Převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Zálohování seznamu pro svazek clusteru</br>Vyberte zálohovací sklad</br>Odstranit zálohovacího skladu |[Spravovat zálohy](storsimple-8000-manage-backup-catalog.md) |
| Klonování svazku |[Klonování svazku](storsimple-8000-clone-volume-u2.md) |
| Obnovení zálohovacího skladu |[Obnovení zálohovacího skladu](storsimple-8000-restore-from-backup-set-u2.md) |
| Informace o účtech úložiště</br>Přidání účtu úložiště</br>Upravit účet úložiště</br>Odstranění účtu úložiště</br>Střídání klíče účtů úložiště |[Správa účtů úložiště](storsimple-8000-manage-storage-accounts.md) |
| O šablonách pásma</br>Přidání šablony šířky pásma</br>Upravit šablonu šířky pásma</br>Odstranit šablonu šířky pásma</br>Použít výchozí šablonu šířky pásma</br>Vytvořit šablonu celodenní šířky pásma, která se spouští v zadanou dobu |[Správa šablon šířky pásma](storsimple-8000-manage-bandwidth-templates.md) |
| Informace o záznamech řízení přístupu</br>Vytvoření záznamů řízení přístupu</br>Upravit záznam řízení přístupu</br>Odstranit záznam řízení přístupu |[Spravovat přístup k záznamům v ovládací prvek](storsimple-8000-manage-acrs.md) |
| Zobrazení podrobností o úloze</br>Zrušení úlohy |[Správa úloh](storsimple-8000-manage-jobs-u2.md) |
| Zobrazování oznámení o výstrahách</br>Správa výstrah</br>Zkontrolujte výstrahy |[Zobrazovat a spravovat výstrahy StorSimple](storsimple-8000-manage-alerts.md) |
| Vytvářet monitorování grafy |[Monitorování zařízení StorSimple](storsimple-monitor-device.md) |
| Přidat kontejner svazků</br>Upravit kontejneru svazků</br>Odstranit kontejner svazků |[Správa kontejnerů svazků](storsimple-8000-manage-volume-containers.md) |
| Přidání svazku</br>Upravit svazku</br>Svazek převést do režimu offline</br>Odstranění svazku</br>Monitorování svazku |[Správa svazků](storsimple-8000-manage-volumes-u2.md) |
| Upravit nastavení zařízení</br>Upravit nastavení času</br>Upravit nastavení DNS.md</br>Nakonfigurujte rozhraní sítě |[Upravte konfiguraci zařízení pro zařízení StorSimple](storsimple-8000-modify-device-config.md) |
| Nastavení proxy serveru webové zobrazení |[Konfigurace webového proxy serveru pro zařízení](storsimple-8000-configure-web-proxy.md) |
| Změnit heslo správce zařízení</br>Změnit heslo Snapshot Manager zařízení StorSimple |[Změna hesel zařízení StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurace vzdálené správy |[Vzdálené připojení k zařízení StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurace nastavení výstrah |[Zobrazovat a spravovat výstrahy StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurace protokolů CHAP pro vaše zařízení StorSimple |[Konfigurace protokolů CHAP pro vaše zařízení StorSimple](storsimple-configure-chap.md) |
| Přidání zásady zálohování</br>Přidat nebo změnit plán</br>Odstranit zásady zálohování</br>Proveďte ruční zálohy</br>Vytvořit vlastní zásady zálohování s více svazků a plány |[Správa zásad zálohování](storsimple-8000-manage-backup-policies-u2.md) |
| Zastavit řadiče zařízení</br>Restartujte řadiče zařízení</br>Vypnutí řadiče zařízení</br>Obnovit výchozí tovární nastavení v zařízení</br>(Vyšší jsou pouze místní zařízení) |[Správa řadiče zařízení StorSimple](storsimple-8000-manage-device-controller.md) |
| Další informace o hardwarových součástí StorSimple</br>Monitorování stavu hardwaru</br>(Vyšší jsou pouze místní zařízení) |[Monitorování hardwarových součástí](storsimple-8000-monitor-hardware-status.md) |
| Vytvoření balíčku pro podporu |[Vytvoření a Správa balíčku pro podporu](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalovat aktualizace softwaru |[Aktualizace zařízení](storsimple-update-device.md) |

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s každodenní operace zařízení StorSimple nebo s žádným z jeho hardwarové součásti, podívejte se na:

* [Řešení potíží pomocí diagnostického nástroje](storsimple-8000-diagnostics.md)
* [Použít StorSimple monitorování kláves](storsimple-monitoring-indicators.md)

Pokud nelze vyřešit problémy a je potřeba vytvořit žádost o službu, podívejte se na [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

