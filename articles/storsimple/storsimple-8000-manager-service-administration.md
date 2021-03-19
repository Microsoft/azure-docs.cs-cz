---
title: Správa služby StorSimple Správce zařízení | Microsoft Docs
description: Naučte se spravovat zařízení StorSimple pomocí služby StorSimple Správce zařízení v Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656817"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Použití služby StorSimple Správce zařízení ke správě zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje rozhraní služby StorSimple Správce zařízení Service, včetně toho, jak se k němu připojit, dostupné možnosti a odkazy na konkrétní pracovní postupy, které je možné provádět prostřednictvím tohoto uživatelského rozhraní. Tyto pokyny platí pro obojí. fyzické zařízení StorSimple a cloudové zařízení.

Po přečtení tohoto článku se naučíte:

* Připojení ke službě StorSimple Správce zařízení Service
* Správa zařízení StorSimple prostřednictvím služby StorSimple Správce zařízení

## <a name="connect-to-storsimple-device-manager-service"></a>Připojení ke službě StorSimple Správce zařízení Service

Služba StorSimple Správce zařízení běží v Microsoft Azure a připojuje se k více zařízením StorSimple. Ke správě těchto zařízení používáte centrální portál Microsoft Azure běžící v prohlížeči. Pokud se chcete připojit ke službě StorSimple Správce zařízení, udělejte toto:

#### <a name="to-connect-to-the-service"></a>Připojení ke službě
1. Přejděte na [https://portal.azure.com/](https://portal.azure.com/) .
2. Pomocí přihlašovacích údajů pro účet Microsoft se přihlaste k portál Microsoft Azure (nachází se v pravém horním rohu podokna).
3. Přejděte dolů na levé navigační podokno, abyste měli přístup ke službě StorSimple Správce zařízení.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Správa zařízení StorSimple pomocí služby StorSimple Správce zařízení

Následující tabulka obsahuje souhrn všech běžných úloh správy a složitých pracovních postupů, které se dají provádět v uživatelském rozhraní služby StorSimple Správce zařízení. Tyto úlohy jsou uspořádány na základě oken uživatelského rozhraní, na kterých jsou iniciována.

Chcete-li získat další informace o jednotlivých pracovních postupech, klikněte na příslušný postup v tabulce.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>Pracovní postupy Správce zařízení StorSimple

| Pokud to chcete provést... | Použijte tento postup. |
| --- | --- |
| Vytvoření služby</br>Odstranění služby</br>Získat registrační klíč služby</br>Znovu vygenerovat registrační klíč služby |[Nasazení služby StorSimple Správce zařízení](storsimple-8000-manage-service.md) |
| Zobrazení protokolů aktivit |[Použití přehledu služby Správce zařízení StorSimple](storsimple-8000-service-dashboard.md) |
| Změna šifrovacího klíče dat služby</br>Zobrazit protokoly operací |[Použití řídicího panelu služby StorSimple Správce zařízení](storsimple-8000-service-dashboard.md) |
| Deaktivace zařízení</br>Odstranit zařízení |[Deaktivace nebo odstranění zařízení](storsimple-8000-deactivate-and-delete-device.md) |
| Informace o zotavení po havárii a převzetí služeb při selhání zařízení</br>Převzetí služeb při selhání fyzického zařízení</br>Převzetí služeb při selhání virtuálního zařízení</br>Zotavení po havárii pro provozní kontinuitu (BCDR) |[Převzetí služeb při selhání a zotavení po havárii pro zařízení StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Výpis záloh svazku</br>Vybrat zálohovací sklad</br>Odstraní zálohovací sklad. |[Správa záloh](storsimple-8000-manage-backup-catalog.md) |
| Klonování svazku |[Klonování svazku](storsimple-8000-clone-volume-u2.md) |
| Obnovit zálohovací sklad |[Obnovit zálohovací sklad](storsimple-8000-restore-from-backup-set-u2.md) |
| Informace o účtech úložiště</br>Přidat účet úložiště</br>Úprava účtu úložiště</br>Odstranění účtu úložiště</br>Střídání klíčů účtů úložiště |[Správa účtů úložiště](storsimple-8000-manage-storage-accounts.md) |
| Šablony šířky pásma</br>Přidat šablonu šířky pásma</br>Upravit šablonu šířky pásma</br>Odstranění šablony šířky pásma</br>Použít výchozí šablonu šířky pásma</br>Vytvoří celodenní šablonu šířky pásma, která začne v zadaném čase. |[Správa šablon šířky pásma](storsimple-8000-manage-bandwidth-templates.md) |
| O záznamech řízení přístupu</br>Vytvoření záznamu řízení přístupu</br>Upravit záznam řízení přístupu</br>Odstranění záznamu řízení přístupu |[Správa záznamů řízení přístupu](storsimple-8000-manage-acrs.md) |
| Zobrazení podrobností o úloze</br>Zrušení úlohy |[Správa úloh](storsimple-8000-manage-jobs-u2.md) |
| Zobrazování oznámení o výstrahách</br>Správa výstrah</br>Procházení výstrah |[Zobrazení a správa výstrah StorSimple](storsimple-8000-manage-alerts.md) |
| Vytváření grafů monitorování |[Monitorování zařízení StorSimple](./storsimple-8000-monitor-device.md) |
| Přidat kontejner svazků</br>Úprava kontejneru svazků</br>Odstranění kontejneru svazků |[Správa kontejnerů svazků](storsimple-8000-manage-volume-containers.md) |
| Přidat svazek</br>Úprava svazku</br>Přepnout svazek do offline režimu</br>Odstranění svazku</br>Monitorování svazku |[Správa svazků](storsimple-8000-manage-volumes-u2.md) |
| Úprava nastavení zařízení</br>Úprava nastavení času</br>Úprava nastavení DNS.md</br>Konfigurace síťových rozhraní |[Úprava konfigurace zařízení pro zařízení StorSimple](storsimple-8000-modify-device-config.md) |
| Zobrazit nastavení webového proxy serveru |[Konfigurace webového proxy serveru pro vaše zařízení](storsimple-8000-configure-web-proxy.md) |
| Úprava hesla správce zařízení</br>Upravit StorSimple Snapshot Manager heslo |[Změna hesel StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurace vzdálené správy |[Vzdálené připojení k zařízení StorSimple](storsimple-8000-remote-connect.md) |
| Konfigurace nastavení upozornění |[Zobrazení a správa výstrah StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurace protokolu CHAP pro zařízení StorSimple |[Konfigurace protokolu CHAP pro zařízení StorSimple](./storsimple-8000-configure-chap.md) |
| Přidání zásady zálohování</br>Přidat nebo upravit plán</br>Odstranění zásady zálohování</br>Provedení ručního zálohování</br>Vytvoření vlastní zásady zálohování s více svazky a plány |[Správa zásad zálohování](storsimple-8000-manage-backup-policies-u2.md) |
| Zastavení řadičů zařízení</br>Restartovat řadiče zařízení</br>Vypnutí řadičů zařízení</br>Resetování zařízení do výchozího továrního nastavení</br>(Výše jsou jenom pro místní zařízení) |[Správa kontroleru zařízení StorSimple](storsimple-8000-manage-device-controller.md) |
| Informace o hardwarových součástech StorSimple</br>Monitorování stavu hardwaru</br>(Výše jsou jenom pro místní zařízení) |[Monitorovat hardwarové součásti](storsimple-8000-monitor-hardware-status.md) |
| Vytvoření balíčku pro podporu |[Vytvoření a Správa balíčku pro podporu](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalovat aktualizace softwaru |[Aktualizace zařízení](storsimple-update-device.md) |

## <a name="next-steps"></a>Další kroky

Pokud dojde k jakýmkoli problémům s každodenním provozem zařízení StorSimple nebo pomocí kterékoli z jeho hardwarových komponent, přečtěte si téma:

* [Řešení potíží pomocí diagnostického nástroje](storsimple-8000-diagnostics.md)
* [Použít indikátory LED monitorování StorSimple](storsimple-monitoring-indicators.md)

Pokud nemůžete problémy vyřešit a potřebujete vytvořit žádost o služby, přečtěte si téma [kontakt podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).