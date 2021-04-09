---
title: Změnit režim zařízení StorSimple | Microsoft Docs
description: Popisuje režimy zařízení StorSimple a vysvětluje, jak použít Windows PowerShell pro StorSimple ke změně režimu zařízení.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017079"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Změna režimu zařízení na zařízení StorSimple

Tento článek poskytuje stručný popis různých režimů, ve kterých může vaše zařízení StorSimple pracovat. Vaše zařízení StorSimple může fungovat ve třech režimech: normální, údržba a obnovení.

Po přečtení tohoto článku budete znát:

* Jaké jsou režimy zařízení StorSimple
* Jak zjistit, v jakém režimu je zařízení StorSimple
* Změna z normálního režimu na režim údržby a *naopak*

Výše uvedené úlohy správy se dají provádět jenom přes rozhraní Windows PowerShell zařízení StorSimple.

## <a name="about-storsimple-device-modes"></a>O režimech zařízení StorSimple

Zařízení StorSimple může pracovat v režimu normálního, údržby nebo obnovení. Každý z těchto režimů je stručně popsán níže.

### <a name="normal-mode"></a>Normální režim

To je definováno jako normální provozní režim pro plně nakonfigurované zařízení StorSimple. Ve výchozím nastavení by mělo být zařízení v normálním režimu.

### <a name="maintenance-mode"></a>Režim údržby

Někdy může být nutné zařízení StorSimple umístit do režimu údržby. Tento režim vám umožňuje provádět údržbu zařízení a instalovat aktualizace, jako jsou třeba ty, které se týkají firmwaru disku.

Systém lze přepnout do režimu údržby pouze pomocí Windows PowerShell pro StorSimple. V tomto režimu jsou pozastaveny všechny požadavky na vstupně-výstupní operace. Také se zastaví služby, jako je například nestálá paměť s náhodným přístupem (NVRAM) nebo služba clusteringu. Oba řadiče se při zadání nebo ukončení tohoto režimu restartují. Po ukončení režimu údržby budou všechny služby obnoveny a měly by být v pořádku. Může to trvat několik minut.

> [!NOTE]
> **Režim údržby se podporuje jenom na správně fungujícím zařízení. Není podporován na zařízení, ve kterém jeden nebo oba řadiče nefungují.**


### <a name="recovery-mode"></a>Režim obnovení

Režim obnovení lze popsat jako "Nouzový režim systému Windows s podporou sítě". Režim obnovení zahájí tým podpora Microsoftu a umožňuje jim provádět diagnostiku v systému. Hlavním cílem režimu obnovení je načtení systémových protokolů.

Pokud systém přejde do režimu obnovení, měli byste se obrátit na podpora Microsoftu pro další kroky. Další informace najdete v [Podpora Microsoftu kontaktů](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Zařízení nemůžete umístit do režimu obnovení. Pokud je zařízení v chybném stavu, pokusí se režim obnovení získat zařízení do stavu, ve kterém je může podpora Microsoftu zaměstnanci prošetřit.**

## <a name="determine-storsimple-device-mode"></a>Určení režimu zařízení StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Určení současného režimu zařízení

1. Přihlaste se ke konzole sériového portu zařízení pomocí postupu v části [použití výstupu pro připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Podívejte se na informační zprávu v nabídce konzoly sériového portu zařízení. Tato zpráva explicitně určuje, jestli je zařízení v režimu údržby nebo obnovení. Pokud zpráva neobsahuje žádné konkrétní informace, které se týkají režimu systému, zařízení je v normálním režimu.

## <a name="change-the-storsimple-device-mode"></a>Změna režimu zařízení StorSimple

Zařízení StorSimple můžete umístit do režimu údržby (z normálního režimu) a provést údržbu nebo nainstalovat aktualizace režimu údržby. Chcete-li zadat nebo ukončit režim údržby, proveďte následující postupy.

> [!IMPORTANT]
> Před přechodem do režimu údržby ověřte, že oba řadiče zařízení jsou v pořádku, a to přístupem k **nastavení zařízení > stav hardwaru** pro vaše zařízení v Azure Portal. Pokud jeden nebo oba řadiče nejsou v pořádku, kontaktujte podpora Microsoftu pro další kroky. Další informace najdete v [Podpora Microsoftu kontaktů](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Přechod do režimu údržby

1. Přihlaste se ke konzole sériového portu zařízení pomocí postupu v části [použití výstupu pro připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo je: `Password1` .
3. Do příkazového řádku zadejte 
   
    `Enter-HcsMaintenanceMode`
4. Zobrazí se varovná zpráva s oznámením, že režim údržby narušuje všechny požadavky na vstupně-výstupní operace a naruší připojení k Azure Portal a budete vyzváni k potvrzení. Zadáním **Y** zadejte režim údržby.
5. Oba řadiče se restartují. Po dokončení restartování bude informační zpráva konzoly sériového portu označovat, že zařízení je v režimu údržby. Ukázkový výstup najdete níž.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Ukončení režimu údržby

1. Přihlaste se ke konzole sériového portu zařízení. Ověřte ze zprávy banner, že je zařízení v režimu údržby.
2. Na příkazovém řádku zadejte:
   
    `Exit-HcsMaintenanceMode`
3. Zobrazí se zpráva s upozorněním a potvrzovací zpráva. Chcete-li ukončit režim údržby, zadejte **Y** .
4. Oba řadiče se restartují. Po dokončení restartování se v hlavičce sériové konzoly zobrazí zpráva, že zařízení je v normálním režimu. Ukázkový výstup najdete níž.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak na zařízení StorSimple [použít normální aktualizace a režim údržby](storsimple-update-device.md) .

