---
title: Změna režimu zařízení StorSimple | Dokumentace Microsoftu
description: Popisuje režimy zařízení StorSimple a vysvětluje, jak pomocí prostředí Windows PowerShell pro StorSimple Změna režimu zařízení.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60576085"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Změna režimu zařízení na zařízení StorSimple

Tento článek obsahuje stručný popis v různých režimech, ve kterých můžete provozovat zařízení StorSimple. Zařízení StorSimple, můžou fungovat v tří režimů: Normální, údržby a obnovení.

Po přečtení tohoto článku, budete vědět:

* Co režim zařízení StorSimple
* Jak zjistit, které režimu zařízení StorSimple je v
* Změna z normálního režimu údržby a *naopak*

Výše uvedené úlohy správy je možné provádět pouze prostřednictvím rozhraní Windows PowerShell vašeho zařízení StorSimple.

## <a name="about-storsimple-device-modes"></a>Informace o režimech zařízení StorSimple

Zařízení StorSimple můžete pracovat v režimu normal, údržby nebo obnovení. Každá z těchto režimů stručně popsané níže.

### <a name="normal-mode"></a>Normálního režimu

Toto je definováno jako normální provozní režim pro to kompletně nakonfigurované zařízení StorSimple. Ve výchozím nastavení by měla být zařízení v normálním režimu.

### <a name="maintenance-mode"></a>Režim údržby

Zařízení StorSimple může někdy potřeba umístit do režimu údržby. Tento režim vám umožňuje provést údržbu na zařízení a instalace narušující aktualizace, jako je ta, která souvisí firmwaru disku.

V systému můžete převést do režimu údržby jenom přes Windows PowerShell pro StorSimple. V tomto režimu jsou pozastaveny všechny vstupně-výstupní požadavky. Zastaví se také služby, jako je paměť s náhodným přístupem stálé (paměti NVRAM) nebo službu clusteringu. Oba kontrolery se restartují, když zadáte nebo tento režim ukončit. Po ukončení režimu údržby, všechny služby bude pokračovat a musí být v pořádku. Může to trvat několik minut.

> [!NOTE]
> **Režim údržby je podporována pouze na zařízení správně funguje. Není podporována u zařízení, ve kterém jeden nebo oba kontrolery nefungují.**


### <a name="recovery-mode"></a>Režim obnovení

Režim obnovení lze popsat jako "Bezpečný režim pro Windows s podporou sítě". Režim obnovení zaujme týmu Microsoft Support a umožňuje jim k provedení diagnostiky v systému. Hlavním cílem režimu obnovení je pro načtení systémových protokolů.

Pokud váš systém přejde do režimu obnovení, měli byste požádat Microsoft Support pro další kroky. Další informace najdete v části [kontaktujte podporu Microsoftu](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Nelze umístit zařízení v režimu obnovení. Pokud zařízení je ve špatném stavu, režimu obnovení se pokusí získat zařízení do stavu, ve kterém pracovníci Microsoft Support jej můžete prozkoumat.**

## <a name="determine-storsimple-device-mode"></a>Určení režimu zařízení StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Chcete-li zjistit aktuální režimu zařízení

1. Přihlaste se k konzole sériového portu zařízení podle postupu v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Podívejte se na zprávě v nabídce konzoly sériového portu zařízení. Tato zpráva explicitně určuje, jestli je zařízení v režimu údržby nebo obnovení. Pokud zpráva neobsahuje žádné konkrétní informace týkající se režimu systému, zařízení je v normálním režimu.

## <a name="change-the-storsimple-device-mode"></a>Změna režimu zařízení StorSimple

Zařízení StorSimple můžete uvést do režimu údržby (z normálního režimu) provést údržbu nebo nainstalovat aktualizace režimu údržby. Proveďte následující postupy a zadejte skript nebo ukončete režim údržby.

> [!IMPORTANT]
> Před přechod do režimu údržby, ověřte, že oba kontrolery zařízení jsou v pořádku díky přístupu **nastavení zařízení > Stav hardwaru** pro vaše zařízení na portálu Azure portal. Pokud jeden nebo oba řadiče nejsou v pořádku, obraťte se na Microsoft Support pro další kroky. Další informace najdete v části [kontaktujte podporu Microsoftu](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Chcete-li spustit režim údržby

1. Přihlaste se k konzole sériového portu zařízení podle postupu v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**. Po zobrazení výzvy zadejte **hesla správce zařízení**. Je výchozí heslo: `Password1`.
3. Na příkazovém řádku zadejte 
   
    `Enter-HcsMaintenanceMode`
4. Zobrazí se upozornění oznamující, že režimu údržby se přerušit všechny vstupně-výstupní požadavky a server připojení k webu Azure portal a zobrazí se výzva k potvrzení. Typ **Y** do režimu údržby.
5. Oba kontrolery se restartuje. Po dokončení restartování konzoly sériového portu banner označí, že je zařízení v režimu údržby. Ukázkový výstup najdete níž.

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

#### <a name="to-exit-maintenance-mode"></a>Ukončete režim údržby

1. Přihlaste se ke konzole sériového portu zařízení. Zkontrolujte z zpráva hlavičky, která vaše zařízení je v režimu údržby.
2. Na příkazovém řádku zadejte:
   
    `Exit-HcsMaintenanceMode`
3. Zobrazí se zpráva s upozorněním a potvrzovací zpráva. Typ **Y** ukončete režim údržby.
4. Oba kontrolery se restartuje. Po dokončení restartování konzoly sériového portu banner označuje, že je zařízení v normálním režimu. Ukázkový výstup najdete níž.

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

## <a name="next-steps"></a>Další postup

Zjistěte, jak [režim aktualizace normální a údržba](storsimple-update-device.md) zařízení StorSimple.

