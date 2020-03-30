---
title: Správa virtuálního pole Microsoft Azure StorSimple Manager | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat místní virtuální pole StorSimple pomocí služby StorSimple Device Manager na webu Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62123801"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Správa virtuálního pole StorSimple Pomocí služby StorSimple Device Manager
![tok procesu nastavení](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Přehled
Tento článek popisuje rozhraní služby StorSimple Device Manager, včetně toho, jak se k němu připojit a různé dostupné možnosti a poskytuje odkazy na konkrétní pracovní postupy, které lze provést prostřednictvím tohoto uživatelského rozhraní.

Po přečtení tohoto článku budete vědět, jak:

* Připojení ke službě StorSimple Device Manager
* Navigace v uzdu StorSimple Device Manager UI
* Správa virtuálního pole StorSimple prostřednictvím služby StorSimple Device Manager

> [!NOTE]
> Chcete-li zobrazit možnosti správy, které jsou k dispozici pro zařízení řady StorSimple 8000, přejděte na [službu StorSimple Manager ke správě zařízení StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Připojení ke službě StorSimple Device Manager
Služba StorSimple Device Manager běží v Microsoft Azure a připojuje se k několika virtuálním polím StorSimple. Ke správě těchto zařízení používáte centrální portál Microsoft Azure spuštěný v prohlížeči. Chcete-li se připojit ke službě StorSimple Device Manager, postupujte takto.

#### <a name="to-connect-to-the-service"></a>Připojení ke službě
1. Přejděte [https://ms.portal.azure.com](https://ms.portal.azure.com)na .
2. Pomocí přihlašovacích údajů k účtu Microsoft se přihlaste na portál Microsoft Azure (umístěný v pravém horním rohu podokna).
3. Přejděte na procházet --> "Filtr" na StorSimple Správce zařízení zobrazit všechny vaše správce zařízení v daném předplatném.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Použití služby StorSimple Device Manager k provádění úloh správy
V následující tabulce je uveden souhrn všech běžných úloh správy a složitých pracovních postupů, které lze provádět v rámci okna souhrnu služby StorSimple Device Manager. Tyto úkoly jsou uspořádány na základě čepelí, na kterých jsou zahájeny.

Další informace o jednotlivých pracovních postupech získáte klepnutím na příslušný postup v tabulce.

#### <a name="storsimple-device-manager-workflows"></a>Pracovní postupy Správce zařízení StorSimple
| Pokud chcete udělat ... | Použijte tento postup |
| --- | --- |
| Vytvoření služby</br>Odstranění služby</br>Získání registračního klíče služby</br>Obnovení registračního klíče služby |[Nasazení služby StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Zobrazit protokoly aktivit |[Použití souhrnu služby StorSimple](storsimple-virtual-array-service-summary.md) |
| Deaktivace virtuálního pole</br>Odstranění virtuálního pole |[Deaktivace nebo odstranění virtuálního pole](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Zotavení po havárii a převzetí služeb při selhání zařízení</br>Předpoklady převzetí služeb při selhání</br>Zotavení po havárii kontinuity provozu (BCDR)</br>Chyby během zotavení po havárii |[Zotavení po havárii a převzetí služeb při selhání zařízení pro vaše virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) |
| Zálohování sdílených složek a svazků</br>Ruční zálohování</br>Změna plánu zálohování</br>Zobrazit existující zálohy |[Zálohování virtuálního pole StorSimple](storsimple-virtual-array-backup.md) |
| Klonování sdílených složek ze zálohovacího skladu</br>Klonování svazků ze zálohovacího skladu</br>Obnovení na úrovni položky (pouze souborový server) |[Klonování ze zálohy virtuálního pole StorSimple](storsimple-virtual-array-clone.md) |
| O účtech úložiště</br>Přidání účtu úložiště</br>Úprava účtu úložiště</br>Odstranění účtu úložiště |[Správa účtů úložiště pro virtuální pole StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Záznamy řízení přístupu</br>Přidání nebo úprava záznamu řízení přístupu </br>Odstranění záznamu řízení přístupu |[Správa záznamů řízení přístupu pro virtuální pole StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Zobrazení podrobností o úloze |[Správa úloh virtuálního pole StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurace nastavení výstrah</br>Zobrazování oznámení o výstrahách</br>Správa upozornění</br>Kontrola upozornění |[Zobrazení a správa výstrah pro virtuální pole StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Změna hesla správce zařízení |[Změna hesla správce zařízení StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalace aktualizací softwaru |[Aktualizace virtuálního pole](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Místní webové [uživatelské prostředí](storsimple-ova-web-ui-admin.md) je nutné použít pro následující úkoly:
> 
> * [Načtení šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Vytvoření balíčku podpory](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zastavení a restartování virtuálního pole](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Další kroky
Informace o webovém uživatelském uživatelském prostředí a jeho použití naleznete v [části Použití webového uživatelského prostředí StorSimple ke správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

