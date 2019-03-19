---
title: Správa Microsoft Azure StorSimple Manager Virtual Array | Dokumentace Microsoftu
description: Naučte se spravovat místní StorSimple Virtual Array pomocí služby Správce zařízení StorSimple na webu Azure Portal.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091864"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Použití služby Správce zařízení StorSimple ke správě StorSimple Virtual Array
![tok procesu instalace](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Přehled
Tento článek popisuje rozhraní služby Správce zařízení StorSimple, včetně postupu pro připojení a různé možnosti, které jsou k dispozici a poskytuje odkazy na konkrétní pracovní postupy, které je možné provádět prostřednictvím tohoto uživatelského rozhraní.

Po přečtení tohoto článku, budete vědět, jak:

* Připojení ke službě Správce zařízení StorSimple
* Přejděte uživatelské rozhraní Správce zařízení StorSimple
* Správa StorSimple Virtual Array prostřednictvím služby Správce zařízení StorSimple

> [!NOTE]
> Chcete-li zobrazit možnosti správy dostupné pro zařízení StorSimple 8000 series, přejděte na [služby StorSimple Manager použít ke správě zařízení StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Připojení ke službě Správce zařízení StorSimple
Služba Správce zařízení StorSimple běží v Microsoft Azure a připojí k více virtuálních polí StorSimple. Centrální portál Microsoft Azure spuštěné v prohlížeči použijete ke správě těchto zařízení. Pro připojení ke službě Správce zařízení StorSimple, postupujte takto.

#### <a name="to-connect-to-the-service"></a>Pro připojení ke službě
1. Přejděte do [ (Nastavení)https://ms.portal.azure.com](https://ms.portal.azure.com) (Integrace a služby).
2. Pomocí svých přihlašovacích údajů účtu Microsoft, přihlaste se k portálu Microsoft Azure (nachází se v pravé horní části podokna).
3. Přejděte do Procházet.--> "Filtrování" ve Správci zařízení StorSimple, chcete-li zobrazit všechny správce zařízení v rámci daného předplatného.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Použití služby Správce zařízení StorSimple k provádění úloh správy
Následující tabulka uvádí přehled všechny běžné úlohy správy a komplexní pracovní postupy, které lze provést v okně s přehledem služby Správce zařízení StorSimple. Tyto úlohy jsou uspořádány podle okna, ve kterých se zahájila.

Další informace o každém pracovním postupu klikněte na vhodný postup uvedený v tabulce.

#### <a name="storsimple-device-manager-workflows"></a>Správce zařízení StorSimple pracovních postupů
| Pokud chcete udělat... | Pomocí tohoto postupu |
| --- | --- |
| Vytvoření služby</br>Odstranit službu</br>Získání registračního klíče služby</br>Znovu vygenerovat registrační klíč služby |[Nasazení služby Správce zařízení StorSimple](storsimple-virtual-array-manage-service.md) |
| Zobrazení protokolů aktivit |[Použít souhrn služby StorSimple](storsimple-virtual-array-service-summary.md) |
| Deaktivace virtuálního pole</br>Odstranit virtuální pole |[Deaktivujte nebo odstraňte virtuální pole](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Po havárii pro obnovení a zařízení převzetí služeb při selhání</br>Požadavky na převzetí služeb při selhání</br>Obchodní kontinuity podnikových procesů zotavení po havárii (BCDR)</br>Chyby při zotavení po havárii |[Po havárii pro obnovení a zařízení převzetí služeb při selhání pro StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Zálohování sdílených složek a svazků</br>Proveďte ruční zálohování</br>Změnit plán zálohování</br>Zobrazení existujících záloh |[Proveďte zálohu StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Klonovat sdílené složky ze zálohovacího skladu</br>Klonování svazků ze zálohovacího skladu</br>Obnovení na úrovni položek (pouze pro souborový server) |[Klonování ze zálohy StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Informace o účtech úložiště</br>Přidání účtu úložiště</br>Upravit účet úložiště</br>Odstranění účtu úložiště |[Správa účtů úložiště pro StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| O záznamy řízení přístupu</br>Přidat nebo upravit záznam řízení přístupu </br>Odstranit záznam řízení přístupu |[Správa záznamy řízení přístupu pro StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Zobrazení podrobností o úloze |[Správa úloh StorSimple Virtual Array](storsimple-virtual-array-manage-jobs.md) |
| Konfigurace nastavení výstrah</br>Zobrazování oznámení o výstrahách</br>Správa upozornění</br>Zkontrolujte výstrahy |[Umožňuje zobrazit a spravovat výstrahy pro StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Změna hesla správce zařízení |[Změna hesla správce zařízení StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalace aktualizací softwaru |[Aktualizujte vaše virtuální pole](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Je nutné použít [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) pro následující úlohy:
> 
> * [Získání šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Vytvořit balíček pro podporu](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zastavit a znovu spustit virtuální pole](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Další postup
Informace o webovém uživatelském rozhraní a jak ji používat, přejděte na [Správa StorSimple Virtual Array pomocí webového uživatelského rozhraní StorSimple](storsimple-ova-web-ui-admin.md).

