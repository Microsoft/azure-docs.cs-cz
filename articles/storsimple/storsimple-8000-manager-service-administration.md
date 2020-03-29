---
title: Správa služeb Správce zařízení StorSimple | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat zařízení StorSimple pomocí služby StorSimple Device Manager na webu Azure Portal.
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
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723302"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Správa zařízení StorSimple pomocí služby StorSimple Device Manager

## <a name="overview"></a>Přehled

Tento článek popisuje rozhraní služby StorSimple Device Manager, včetně toho, jak se k němu připojit, různé dostupné možnosti a odkazy na konkrétní pracovní postupy, které lze provést prostřednictvím tohoto uživatelského rozhraní. Tyto pokyny se vztahují na oba; fyzickézařízení StorSimple a cloudové zařízení.

Po přečtení tohoto článku se naučíte:

* Připojení ke službě StorSimple Device Manager
* Správa zařízení StorSimple prostřednictvím služby StorSimple Device Manager

## <a name="connect-to-storsimple-device-manager-service"></a>Připojení ke službě StorSimple Device Manager

Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Ke správě těchto zařízení používáte centrální portál Microsoft Azure spuštěný v prohlížeči. Chcete-li se připojit ke službě StorSimple Device Manager, postupujte takto.

#### <a name="to-connect-to-the-service"></a>Připojení ke službě
1. Přejděte [https://portal.azure.com/](https://portal.azure.com/)na .
2. Pomocí přihlašovacích údajů k účtu Microsoft se přihlaste na portál Microsoft Azure (umístěný v pravém horním rohu podokna).
3. Posunutím dolů v levém navigačním podokně přejděte ke službě StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Správa zařízení StorSimple pomocí služby StorSimple Device Manager

V následující tabulce je uveden souhrn všech běžných úloh správy a složitých pracovních postupů, které lze provádět v rámci uj. Tyto úkoly jsou uspořádány na základě blades ui, na kterém jsou zahájeny.

Další informace o jednotlivých pracovních postupech získáte klepnutím na příslušný postup v tabulce.

#### <a name="storsimple-device-manager-workflows"></a>Pracovní postupy Správce zařízení StorSimple

| Pokud chcete udělat ... | Použijte tento postup. |
| --- | --- |
| Vytvoření služby</br>Odstranění služby</br>Získat registrační klíč služby</br>Obnovit registrační klíč služby |[Nasazení služby Správce zařízení StorSimple](storsimple-8000-manage-service.md) |
| Zobrazit protokoly aktivit |[Použití souhrnu služby Správce zařízení StorSimple](storsimple-8000-service-dashboard.md) |
| Změna šifrovacího klíče dat služby</br>Zobrazit protokoly operací |[Použití řídicího panelu služby Správce zařízení StorSimple](storsimple-8000-service-dashboard.md) |
| Deaktivace zařízení</br>Odstranit zařízení |[Deaktivace nebo odstranění zařízení](storsimple-8000-deactivate-and-delete-device.md) |
| Informace o zotavení po havárii a převzetí služeb při selhání zařízení</br>Převzetí služeb při selhání fyzickému zařízení</br>Převzetí služeb při selhání na virtuální zařízení</br>Zotavení po havárii kontinuity provozu (BCDR) |[Převzetí služeb při selhání a zotavení po havárii pro vaše zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Seznam záloh pro svazek</br>Výběr zálohovací sady</br>Odstranění zálohovací sady |[Správa záloh](storsimple-8000-manage-backup-catalog.md) |
| Klonování svazku |[Klonování svazku](storsimple-8000-clone-volume-u2.md) |
| Obnovení zálohovací sady |[Obnovení zálohovací sady](storsimple-8000-restore-from-backup-set-u2.md) |
| O účtech úložiště</br>Přidání účtu úložiště</br>Úprava účtu úložiště</br>Odstranění účtu úložiště</br>Střídání klíčů účtů úložiště |[Správa účtů úložiště](storsimple-8000-manage-storage-accounts.md) |
| Šablony šířky pásma</br>Přidání šablony šířky pásma</br>Úprava šablony šířky pásma</br>Odstranění šablony šířky pásma</br>Použití výchozí šablony šířky pásma</br>Vytvoření celodenní šablony šířky pásma, která začíná v určený čas |[Správa šablon šířky pásma](storsimple-8000-manage-bandwidth-templates.md) |
| Záznamy řízení přístupu</br>Vytvoření záznamu řízení přístupu</br>Úprava záznamu řízení přístupu</br>Odstranění záznamu řízení přístupu |[Správa záznamů řízení přístupu](storsimple-8000-manage-acrs.md) |
| Zobrazení podrobností o úloze</br>Zrušení úlohy |[Správa úloh](storsimple-8000-manage-jobs-u2.md) |
| Zobrazování oznámení o výstrahách</br>Správa upozornění</br>Kontrola upozornění |[Zobrazení a správa výstrah StorSimple](storsimple-8000-manage-alerts.md) |
| Vytvoření monitorovacích grafů |[Sledování zařízení StorSimple](storsimple-monitor-device.md) |
| Přidání kontejneru svazku</br>Úprava kontejneru svazků</br>Odstranění kontejneru svazku |[Správa kontejnerů svazků](storsimple-8000-manage-volume-containers.md) |
| Přidání svazku</br>Úprava svazku</br>Přepne svazek offline</br>Odstranění svazku</br>Sledování hlasitosti |[Správa svazků](storsimple-8000-manage-volumes-u2.md) |
| Změna nastavení zařízení</br>Změna nastavení času</br>Změna nastavení DNS.md</br>Konfigurace síťových rozhraní |[Změna konfigurace zařízení pro zařízení StorSimple](storsimple-8000-modify-device-config.md) |
| Zobrazení nastavení webového proxy serveru |[Konfigurace webového proxy serveru pro vaše zařízení](storsimple-8000-configure-web-proxy.md) |
| Změna hesla správce zařízení</br>Změnit heslo správce snímků StorSimple |[Změna jednoduchých hesel](storsimple-8000-change-passwords.md) |
| Konfigurace vzdálené správy |[Vzdálené připojení k zařízení StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurace nastavení výstrah |[Zobrazení a správa výstrah StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurace protokolu CHAP pro zařízení StorSimple |[Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-configure-chap.md) |
| Přidání zásady zálohování</br>Přidání nebo úprava plánu</br>Odstranění zásad zálohování</br>Ruční zálohování</br>Vytvoření vlastní chodnících s více svazky a plány |[Správa zásad zálohování](storsimple-8000-manage-backup-policies-u2.md) |
| Zastavit ovladače zařízení</br>Restartování řadičů zařízení</br>Vypnutí řadičů zařízení</br>Obnovení továrního nastavení zařízení z výroby</br>(Výše uvedené jsou pouze pro místní zařízení) |[Správa řadiče zařízení StorSimple](storsimple-8000-manage-device-controller.md) |
| Další informace o hardwarových součástech StorSimple</br>Sledování stavu hardwaru</br>(Výše uvedené jsou pouze pro místní zařízení) |[Sledování hardwarových součástí](storsimple-8000-monitor-hardware-status.md) |
| Vytvoření balíčku podpory |[Vytvoření a správa balíčku podpory](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalace aktualizací softwaru |[Aktualizace zařízení](storsimple-update-device.md) |

## <a name="next-steps"></a>Další kroky

Pokud narazíte na jakékoli problémy s každodenním provozem zařízení StorSimple nebo s některou z jeho hardwarových součástí, přečtěte si:

* [Poradce při potížích pomocí nástroje Diagnostika](storsimple-8000-diagnostics.md)
* [Použití LED diod y indikátoru storSimple](storsimple-monitoring-indicators.md)

Pokud se vám nepodařilo problémy vyřešit a potřebujete vytvořit žádost o službu, [obraťte](storsimple-8000-contact-microsoft-support.md)se na podporu společnosti Microsoft .

