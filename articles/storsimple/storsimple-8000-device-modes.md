---
title: Změnit režim zařízení StorSimple | Dokumenty společnosti Microsoft
description: Popisuje režimy zařízení StorSimple a vysvětluje, jak změnit režim zařízení pomocí prostředí Windows PowerShell pro StorSimple.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60576085"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Změna režimu zařízení na zařízení StorSimple

Tento článek obsahuje stručný popis různých režimů, ve kterých může zařízení StorSimple pracovat. Zařízení StorSimple může fungovat ve třech režimech: normální, údržba a obnovení.

Po přečtení tohoto článku budete vědět:

* Jaké jsou režimy zařízení StorSimple
* Jak zjistit, v jakém režimu je zařízení StorSimple
* Jak přejít z normálního režimu na režim údržby a *naopak*

Výše uvedené úlohy správy lze provádět pouze prostřednictvím rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple.

## <a name="about-storsimple-device-modes"></a>O režimech zařízení StorSimple

Zařízení StorSimple může pracovat v normálním režimu údržby nebo obnovení. Každý z těchto režimů je stručně popsán níže.

### <a name="normal-mode"></a>Normální režim

To je definováno jako normální provozní režim pro plně nakonfigurované zařízení StorSimple. Ve výchozím nastavení by mělo být zařízení v normálním režimu.

### <a name="maintenance-mode"></a>Režim údržby

Někdy může být nutné zařízení StorSimple umístit do režimu údržby. Tento režim umožňuje provádět údržbu zařízení a instalovat rušivé aktualizace, například aktualizace související s firmwarem disku.

Systém můžete převést do režimu údržby pouze prostřednictvím prostředí Windows PowerShell pro StorSimple. V tomto režimu jsou pozastaveny všechny vstupně-v.i. Zastaveny jsou také služby, jako je energeticky nezávislá paměť nvram (Random Access Memory) nebo clustering ová služba. Oba řadiče jsou restartovány při vstupu nebo ukončení tohoto režimu. Po ukončení režimu údržby budou všechny služby obnoveny a měly by být v pořádku. Může to trvat několik minut.

> [!NOTE]
> **Režim údržby je podporován pouze na správně fungujícím zařízení. Není podporována na zařízení, ve kterém jeden nebo oba řadiče nefungují.**


### <a name="recovery-mode"></a>Režim obnovení

Režim obnovení lze popsat jako "Nouzový režim systému Windows s podporou sítě". Režim obnovení zapojuje tým podpory společnosti Microsoft a umožňuje mu provádět diagnostiku v systému. Primárním cílem režimu obnovení je načíst systémové protokoly.

Pokud váš systém přejde do režimu obnovení, měli byste se obrátit na podporu společnosti Microsoft pro další kroky. Další informace naleznete v [odborné mši společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Zařízení nelze umístit do režimu obnovení. Pokud je zařízení ve špatném stavu, pokusí se režim obnovení převést zařízení do stavu, ve kterém jej pracovníci podpory společnosti Microsoft mohou zkontrolovat.**

## <a name="determine-storsimple-device-mode"></a>Určení režimu zařízení StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Určení aktuálního režimu zařízení

1. Přihlaste se k sériové konzoli zařízení podle kroků uvedených v [části Použití putty pro připojení ke sériové konzoli zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Podívejte se na bannerovou zprávu v nabídce sériové konzole zařízení. Tato zpráva výslovně označuje, zda je zařízení v režimu údržby nebo obnovení. Pokud zpráva neobsahuje žádné konkrétní informace týkající se systémového režimu, je zařízení v normálním režimu.

## <a name="change-the-storsimple-device-mode"></a>Změna režimu zařízení StorSimple

Zařízení StorSimple můžete umístit do režimu údržby (z normálního režimu) a provést aktualizace režimu údržby nebo nainstalovat. Pro vstup do režimu údržby nebo ukončení údržby proveďte následující postupy.

> [!IMPORTANT]
> Před vstupem do režimu údržby ověřte, že jsou oba řadiče zařízení v pořádku, a to tak, že na portálu Azure portal najdete **v nastavení zařízení > stavu hardwaru** pro vaše zařízení. Pokud jeden nebo oba řadiče nejsou v pořádku, obraťte se na podporu společnosti Microsoft pro další kroky. Další informace naleznete v [odborné mši společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Přepne se do režimu údržby

1. Přihlaste se k sériové konzoli zařízení podle kroků uvedených v [části Použití putty pro připojení ke sériové konzoli zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo je: `Password1`.
3. Na příkazovém řádku zadejte 
   
    `Enter-HcsMaintenanceMode`
4. Zobrazí se varovná zpráva s informací, že režim údržby naruší všechny vstupně-va požadavky a přeruší připojení k portálu Azure a budete vyzváni k potvrzení. Typ **Y** pro vstup do režimu údržby.
5. Oba řadiče se restartují. Po dokončení restartování bude nápis sériové konzole označovat, že zařízení je v režimu údržby. Ukázkový výstup najdete níž.

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

1. Přihlaste se k sériové konzoli zařízení. Ověřte z bannerové zprávy, že je vaše zařízení v režimu údržby.
2. Na příkazovém řádku zadejte:
   
    `Exit-HcsMaintenanceMode`
3. Zobrazí se varovná zpráva a potvrzovací zpráva. Typ **Y** pro ukončení režimu údržby.
4. Oba řadiče se restartují. Po dokončení restartování zobrazí nápis sériové konzoly, že zařízení je v normálním režimu. Ukázkový výstup najdete níž.

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

Přečtěte si, jak na zařízení StorSimple [použít normální aktualizace a aktualizace režimu údržby.](storsimple-update-device.md)

