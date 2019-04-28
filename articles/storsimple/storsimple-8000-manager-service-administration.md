---
title: Správa služby Správce zařízení StorSimple | Dokumentace Microsoftu
description: Další informace o správě zařízení StorSimple pomocí služby Správce zařízení StorSimple na webu Azure Portal.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723302"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Použití služby Správce zařízení StorSimple ke správě zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje rozhraní služby Správce zařízení StorSimple, včetně připojení, k dispozici různé možnosti, a odkazy na konkrétní pracovní postupy, které je možné provádět prostřednictvím tohoto uživatelského rozhraní. Tento návod se vztahuje na oba; cloudovým zařízením a fyzickým zařízením StorSimple.

Po přečtení tohoto článku se dozvíte, jak:

* Připojení ke službě Správce zařízení StorSimple
* Spravovat zařízení StorSimple pomocí služby Správce zařízení StorSimple

## <a name="connect-to-storsimple-device-manager-service"></a>Připojení ke službě Správce zařízení StorSimple

Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více zařízení StorSimple. Centrální portál Microsoft Azure spuštěné v prohlížeči použijete ke správě těchto zařízení. Pro připojení ke službě Správce zařízení StorSimple, postupujte takto.

#### <a name="to-connect-to-the-service"></a>Pro připojení ke službě
1. Přejděte do [ https://portal.azure.com/ ](https://portal.azure.com/).
2. Pomocí svých přihlašovacích údajů účtu Microsoft, přihlaste se k portálu Microsoft Azure (nachází se v pravé horní části podokna).
3. Posuňte se dolů levém navigačním podokně přístup ke službě Správce zařízení StorSimple.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Správa zařízení StorSimple pomocí služby Správce zařízení StorSimple

Následující tabulka uvádí přehled všechny běžné úlohy správy a komplexní pracovní postupy, které lze provést v rámci uživatelského rozhraní služby Správce zařízení StorSimple. Tyto úlohy jsou uspořádány podle okna uživatelského rozhraní, na kterých jsou inicializována.

Další informace o každém pracovním postupu klikněte na vhodný postup uvedený v tabulce.

#### <a name="storsimple-device-manager-workflows"></a>Správce zařízení StorSimple pracovních postupů

| Pokud chcete udělat... | Pomocí tohoto postupu. |
| --- | --- |
| Vytvoření služby</br>Odstranit službu</br>Získání registračního klíče služby</br>Znovu vygenerovat registrační klíč služby |[Nasazení služby Správce zařízení StorSimple](storsimple-8000-manage-service.md) |
| Zobrazení protokolů aktivit |[Použít souhrn služby Správce zařízení StorSimple](storsimple-8000-service-dashboard.md) |
| Změna šifrovacího klíče dat služby</br>Zobrazit protokoly operací |[Řídicí panel služby Správce zařízení StorSimple](storsimple-8000-service-dashboard.md) |
| Deaktivace zařízení</br>Odstranění zařízení |[Deaktivace a odstranění zařízení](storsimple-8000-deactivate-and-delete-device.md) |
| Další informace o havárii obnovení a zařízení převzetí služeb při selhání</br>Převzetí služeb při selhání do fyzického zařízení</br>Převzetí služeb při selhání virtuálního zařízení</br>Obchodní kontinuity podnikových procesů zotavení po havárii (BCDR) |[Převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Seznam zálohy svazku</br>Vyberte sadu záloh</br>Odstranit zálohovací sklad |[Správa záloh](storsimple-8000-manage-backup-catalog.md) |
| Klonování svazku |[Klonování svazku](storsimple-8000-clone-volume-u2.md) |
| Obnovit zálohovací sklad |[Obnovit zálohovací sklad](storsimple-8000-restore-from-backup-set-u2.md) |
| Informace o účtech úložiště</br>Přidání účtu úložiště</br>Upravit účet úložiště</br>Odstranění účtu úložiště</br>Obměna klíčů účtů úložiště |[Správa účtů úložiště](storsimple-8000-manage-storage-accounts.md) |
| Šablony šířky pásma</br>Přidat šablonu šířky pásma</br>Upravit šablonu šířky pásma</br>Odstranit šablonu šířky pásma</br>Použít výchozí šablonu šířky pásma</br>Vytvořit celodenní šablonu šířky pásma, který se spustí v zadaný čas |[Správa šablon šířky pásma](storsimple-8000-manage-bandwidth-templates.md) |
| O záznamy řízení přístupu</br>Vytvořit záznam řízení přístupu</br>Upravit záznam řízení přístupu</br>Odstranit záznam řízení přístupu |[Spravovat záznamy řízení přístupu](storsimple-8000-manage-acrs.md) |
| Zobrazení podrobností o úloze</br>Zrušení úlohy |[Správa úloh](storsimple-8000-manage-jobs-u2.md) |
| Zobrazování oznámení o výstrahách</br>Správa upozornění</br>Zkontrolujte výstrahy |[Umožňuje zobrazit a spravovat výstrahy StorSimple](storsimple-8000-manage-alerts.md) |
| Vytvoření grafů sledování |[Monitorování zařízení StorSimple](storsimple-monitor-device.md) |
| Přidat kontejner svazků</br>Upravit kontejner svazku</br>Odstraňování kontejneru svazků |[Správa kontejnerů svazků](storsimple-8000-manage-volume-containers.md) |
| Přidat svazek</br>Upravit svazek</br>Svazek převést do režimu offline</br>Odstranění svazku</br>Monitorování svazku |[Správa svazků](storsimple-8000-manage-volumes-u2.md) |
| Upravit nastavení zařízení</br>Upravit nastavení času</br>Upravit nastavení DNS.md</br>Nakonfigurujte síťová rozhraní |[Úprava konfigurace zařízení pro zařízení StorSimple](storsimple-8000-modify-device-config.md) |
| Zobrazení nastavení webového proxy serveru |[Konfigurace webového proxy serveru pro vaše zařízení](storsimple-8000-configure-web-proxy.md) |
| Změna hesla správce zařízení</br>Úprava hesla Snapshot Manageru zařízení StorSimple |[Změna hesel zařízení StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurace vzdálené správy |[Vzdálené připojení k zařízení StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurace nastavení výstrah |[Umožňuje zobrazit a spravovat výstrahy StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurace protokolu CHAP pro zařízení StorSimple |[Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-configure-chap.md) |
| Přidání zásady zálohování</br>Přidat nebo změnit plán</br>Odstraňování zásady zálohování</br>Proveďte ruční zálohování</br>Vytvoření vlastní zásady zálohování s více svazků a plány |[Správa zásad zálohování](storsimple-8000-manage-backup-policies-u2.md) |
| Zastavit řadiče zařízení</br>Restartování řadiče zařízení</br>Vypnutí řadiče zařízení</br>Obnovit v zařízení výchozí tovární nastavení</br>(Nad se pouze místní zařízení) |[Správa kontroleru zařízení StorSimple](storsimple-8000-manage-device-controller.md) |
| Další informace o StorSimple hardwarových součástí</br>Monitorování stavu hardwaru</br>(Nad se pouze místní zařízení) |[Monitorování hardwarových součástí](storsimple-8000-monitor-hardware-status.md) |
| Vytvořit balíček pro podporu |[Vytvoření a Správa balíčku pro podporu](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalace aktualizací softwaru |[Aktualizace zařízení](storsimple-update-device.md) |

## <a name="next-steps"></a>Další postup

Pokud zaznamenáte jakékoli problémy s každodenní operace zařízení StorSimple nebo některý z jeho hardwarové součásti, podívejte se na:

* [Řešení potíží pomocí diagnostického nástroje](storsimple-8000-diagnostics.md)
* [Pomocí monitorování indikátorů LED StorSimple](storsimple-monitoring-indicators.md)

Pokud nelze vyřešit problémy a potřebujete vytvořit žádost o službu, podívejte se na [kontaktujte podporu Microsoftu](storsimple-8000-contact-microsoft-support.md).

