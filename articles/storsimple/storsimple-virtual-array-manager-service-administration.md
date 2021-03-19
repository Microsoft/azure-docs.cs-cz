---
title: Správa virtuálního pole nástroje Microsoft Azure StorSimple Manager | Microsoft Docs
description: Přečtěte si, jak spravovat místní virtuální pole StorSimple pomocí služby StorSimple Správce zařízení v Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: cb5b6b1a2d890fe49d78da17d549c5676557b971
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654237"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Použití služby StorSimple Správce zařízení ke správě virtuálního pole StorSimple
![průběh procesu nastavení](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Přehled
Tento článek popisuje rozhraní služby StorSimple Správce zařízení Service, včetně toho, jak se k němu připojit a jaké jsou dostupné různé možnosti, a poskytuje odkazy na konkrétní pracovní postupy, které se dají provádět prostřednictvím tohoto uživatelského rozhraní.

Po přečtení tohoto článku budete umět:

* Připojení ke službě StorSimple Správce zařízení
* Navigace v uživatelském rozhraní StorSimple Správce zařízení
* Správa virtuálního pole StorSimple prostřednictvím služby StorSimple Správce zařízení

> [!NOTE]
> Pokud chcete zobrazit možnosti správy dostupné pro zařízení řady StorSimple 8000, přečtěte si část [použití služby StorSimple Manager ke správě zařízení StorSimple](./storsimple-8000-manager-service-administration.md).

## <a name="connect-to-the-storsimple-device-manager-service"></a>Připojení ke službě StorSimple Správce zařízení
Služba StorSimple Správce zařízení běží v Microsoft Azure a připojuje se k několika virtuálním polím StorSimple. Ke správě těchto zařízení používáte centrální portál Microsoft Azure běžící v prohlížeči. Pokud se chcete připojit ke službě StorSimple Správce zařízení, udělejte toto:

#### <a name="to-connect-to-the-service"></a>Připojení ke službě
1. Přejít na [https://portal.azure.com](https://portal.azure.com) .
2. Pomocí přihlašovacích údajů pro účet Microsoft se přihlaste k portál Microsoft Azure (nachází se v pravém horním rohu podokna).
3. Pokud chcete zobrazit všechny správce zařízení v daném předplatném, přejděte na > Procházet a přejděte na správce zařízení StorSimple.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Použití služby StorSimple Správce zařízení k provádění úloh správy
Následující tabulka obsahuje souhrn všech běžných úloh správy a složitých pracovních postupů, které se dají provádět v okně StorSimple Správce zařízení Service Summary. Tyto úkoly jsou uspořádány na základě oken, na kterých jsou iniciována.

Chcete-li získat další informace o jednotlivých pracovních postupech, klikněte na příslušný postup v tabulce.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>Pracovní postupy Správce zařízení StorSimple
| Pokud to chcete provést... | Použít tento postup |
| --- | --- |
| Vytvoření služby</br>Odstranění služby</br>Získání registračního klíče služby</br>Znovu vygenerovat registrační klíč služby |[Nasazení služby StorSimple Správce zařízení](storsimple-virtual-array-manage-service.md) |
| Zobrazení protokolů aktivit |[Použití přehledu služby StorSimple](storsimple-virtual-array-service-summary.md) |
| Deaktivace virtuálního pole</br>Odstranění virtuálního pole |[Deaktivace nebo odstranění virtuálního pole](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Zotavení po havárii a převzetí služeb při selhání zařízení</br>Předpoklady pro převzetí služeb při selhání</br>Zotavení po havárii pro provozní kontinuitu (BCDR)</br>Chyby při zotavení po havárii |[Zotavení po havárii a převzetí služeb při selhání zařízení pro virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) |
| Zálohování sdílených složek a svazků</br>Provedení ručního zálohování</br>Změna plánu zálohování</br>Zobrazit existující zálohy |[Zálohování virtuálního pole StorSimple](storsimple-virtual-array-backup.md) |
| Klonování sdílených složek ze zálohovacího skladu</br>Klonování svazků ze zálohovacího skladu</br>Obnovení na úrovni položek (pouze souborový server) |[Klonování ze zálohy vašeho virtuálního pole StorSimple](storsimple-virtual-array-clone.md) |
| Informace o účtech úložiště</br>Přidat účet úložiště</br>Úprava účtu úložiště</br>Odstranění účtu úložiště |[Správa účtů úložiště pro virtuální pole StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| O záznamech řízení přístupu</br>Přidat nebo upravit záznam řízení přístupu </br>Odstranění záznamu řízení přístupu |[Správa záznamů řízení přístupu pro virtuální pole StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Zobrazení podrobností o úloze |[Správa úloh virtuálních polí StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Konfigurace nastavení upozornění</br>Zobrazování oznámení o výstrahách</br>Správa výstrah</br>Procházení výstrah |[Zobrazení a správa výstrah pro virtuální pole StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Úprava hesla správce zařízení |[Změna hesla správce zařízení StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalovat aktualizace softwaru |[Aktualizace virtuálního pole](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Pro následující úlohy je nutné použít [místní webové uživatelské rozhraní](storsimple-ova-web-ui-admin.md) :
> 
> * [Načtení šifrovacího klíče dat služby](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Vytvoření balíčku pro podporu](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Zastavení a restartování virtuálního pole](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Další kroky
Informace o webovém uživatelském rozhraní a jeho použití najdete [v tématu použití webového uživatelského rozhraní StorSimple ke správě virtuálních polí StorSimple](storsimple-ova-web-ui-admin.md).