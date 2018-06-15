---
title: Správa Microsoft Azure StorSimple Manager virtuální pole | Microsoft Docs
description: Zjistěte, jak spravovat virtuální pole místního zařízení StorSimple pomocí služby StorSimple Manager zařízení na portálu Azure.
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
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875841"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Použít službu StorSimple Manager zařízení ke správě pole virtuální zařízení StorSimple
![tok procesu instalace](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Přehled
Tento článek popisuje rozhraní služby StorSimple Manager zařízení, včetně toho, jak se připojit k jeho a různé možnosti, které jsou k dispozici a poskytuje odkazy na konkrétní pracovní postupy, které je možné provádět prostřednictvím tohoto uživatelského rozhraní.

Po přečtení tohoto článku, budete vědět, jak:

* Připojit ke službě StorSimple Manager zařízení
* Přejděte zařízení StorSimple Manager uživatelského rozhraní
* Spravovat vaše virtuální zařízení StorSimple pole prostřednictvím služby StorSimple Manager zařízení

> [!NOTE]
> Chcete-li zobrazit dostupné pro zařízení řady StorSimple 8000 možnosti správy, přejděte na [použít službu StorSimple Manager ke správě zařízení StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Připojit ke službě StorSimple Manager zařízení
Service Manager zařízení StorSimple běží v Microsoft Azure a připojí k několik polí virtuální zařízení StorSimple. Centrální portálu Microsoft Azure spuštěnému v prohlížeči používáte ke správě těchto zařízení. Pro připojení ke službě StorSimple Manager zařízení, postupujte takto.

#### <a name="to-connect-to-the-service"></a>Pro připojení ke službě
1. Přejděte na [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Pomocí svých přihlašovacích údajů účtu Microsoft, přihlaste se k portálu Microsoft Azure (nachází se v pravé horní části podokna).
3. Přejděte na Procházet--> 'Filtr' ve Správci zařízení StorSimple, chcete-li zobrazit všechny správce zařízení v daném předplatném.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Použít službu StorSimple Manager zařízení k provádění úloh správy
Následující tabulka obsahuje souhrn všechny běžné úlohy správy a komplexní pracovní postupy, které lze provést v rámci okna souhrnu service Manager zařízení StorSimple. Tyto úlohy jsou uspořádány podle okna, ve kterých se spouští.

Další informace o každém pracovním postupu klikněte na tlačítko vhodný postup uvedený v tabulce.

#### <a name="storsimple-device-manager-workflows"></a>Pracovní postupy portálu Manager zařízení StorSimple
| Pokud chcete to udělat... | Pomocí tohoto postupu |
| --- | --- | --- |
| Vytvoření služby</br>Odstranění služby</br>Získání registračního klíče služby</br>Znovu vygenerovat registrační klíč služby |[Nasazení služby Správce zařízení StorSimple](storsimple-virtual-array-manage-service.md) |
| Zobrazit protokoly aktivity |[Použít souhrn služby StorSimple](storsimple-virtual-array-service-summary.md) |
| Deaktivace virtuálního pole</br>Odstranit virtuální pole |[Deaktivujte nebo odstraňte virtuální pole](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Po havárii obnovení a zařízení převzetí služeb při selhání</br>Požadavky převzetí služeb při selhání</br>Obchodní kontinuity zotavení po havárii (BCDR)</br>Chyby během zotavení po havárii |[Po havárii obnovení a zařízení převzetí služeb při selhání pro vaše pole virtuální zařízení StorSimple](storsimple-virtual-array-failover-dr.md) |
| Zálohování sdílených složek a svazků</br>Proveďte ruční zálohy</br>Změnit plán zálohování</br>Zobrazit existující zálohy |[Zálohování pole virtuální zařízení StorSimple](storsimple-virtual-array-backup.md) |
| Klonování sdílené složky ze zálohovacího skladu</br>Klonování svazků ze zálohovacího skladu</br>Obnovení na úrovni položek (pouze pro souborový server) |[Klonování ze záloh pole virtuální zařízení StorSimple](storsimple-virtual-array-clone.md) |
| Informace o účtech úložiště</br>Přidání účtu úložiště</br>Upravit účet úložiště</br>Odstranění účtu úložiště |[Správa účtů úložiště pro pole virtuální zařízení StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Informace o záznamech řízení přístupu</br>Přidat nebo upravit záznam řízení přístupu </br>Odstranit záznam řízení přístupu |[Spravovat záznamy řízení přístupu pro toto pole virtuální zařízení StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Zobrazení podrobností o úloze |[Při správě úloh pole virtuální zařízení StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurace nastavení výstrah</br>Zobrazování oznámení o výstrahách</br>Správa výstrah</br>Zkontrolujte výstrahy |[Zobrazovat a spravovat výstrahy pro toto pole virtuální zařízení StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Změnit heslo správce zařízení |[Změna hesla správce zařízení StorSimple virtuální pole](storsimple-virtual-array-change-device-admin-password.md) |
| Instalovat aktualizace softwaru |[Aktualizovat virtuální pole](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Je nutné použít [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) pro následující úkoly:
> 
> * [Načtení šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Vytvoření balíčku pro podporu](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zastavte a restartujte virtuální pole](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Další kroky
Informace o webového uživatelského rozhraní a způsobu jeho použití, přejděte na [používat StorSimple webového uživatelského rozhraní pro správu pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

