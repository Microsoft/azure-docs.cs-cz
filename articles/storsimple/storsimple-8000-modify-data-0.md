---
title: Změnit nastavení DATA 0 na zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Naučte se, jak pomocí prostředí Windows PowerShell pro StorSimple překonfigurovat síťové rozhraní DATA 0 na vašem zařízení StorSimple.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631579"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Změna nastavení síťového rozhraní DATA 0 na zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Vaše zařízení Microsoft Azure StorSimple má šest síťových rozhraní, od DATA 0 po DATA 5. Rozhraní DATA 0 je vždy konfigurováno prostřednictvím rozhraní prostředí Windows PowerShell nebo sériové konzole a je automaticky povoleno v cloudu. Všimněte si, že nelze konfigurovat datové 0 síťové rozhraní prostřednictvím portálu Azure.

Rozhraní DATA 0 je nejprve konfigurováno pomocí průvodce instalací během počátečního nasazení zařízení StorSimple. Pokud je zařízení v provozním režimu, bude pravděpodobně nutné změnit konfiguraci nastavení DATA 0. Tento kurz obsahuje dvě metody pro úpravu nastavení sítě DATA 0, a to jak prostřednictvím prostředí Windows PowerShell pro StorSimple.

Po přečtení tohoto výukového programu, budete moci:

* Úprava nastavení sítě DATA 0 pomocí průvodce nastavením
* Změna nastavení sítě DATA `Set-HcsNetInterface` 0 pomocí rutiny

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Změna nastavení sítě DATA 0 pomocí průvodce instalací
Nastavení sítě DATA 0 můžete překonfigurovat připojením k rozhraní prostředí Windows PowerShell zařízení StorSimple a spuštěním relace průvodce instalací. Chcete-li upravit nastavení DATA 0, proveďte následující kroky:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Změna nastavení sítě DATA 0 pomocí Průvodce instalací
1. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo `Password1`je .
2. Na příkazovém řádku zadejte:
   
    `Invoke-HcsSetupWizard`
3. Zobrazí se průvodce nastavením, který vám pomůže nakonfigurovat rozhraní DATA 0 vašeho zařízení. Zadejte nové hodnoty pro adresu IP, bránu a masku sítě.

> [!NOTE]
> Ip adresy pevných řadičů bude nutné překonfigurovat prostřednictvím okna **nastavení sítě** zařízení StorSimple na webu Azure Portal. Další informace naleznete v [souboru Modify network interfaces](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Změna nastavení sítě DATA 0 pomocí rutiny Set-HcsNetInterface
Alternativní způsob, jak překonfigurovat datové rozhraní 0 `Set-HcsNetInterface` sítě je pomocí rutiny. Rutina je spuštěna z rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple. Při použití tohoto postupu lze zde také nakonfigurovat pevné IP adresy řadiče. Chcete-li upravit nastavení DATA 0, proveďte následující kroky: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Změna nastavení sítě DATA 0 pomocí rutiny Set-HcsNetInterface
1. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte heslo správce zařízení. Výchozí heslo `Password1`je .
2. Na příkazovém řádku zadejte:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Do šikmých závorek zadejte pro DATA 0 následující hodnoty:
   
   * Adresa protokolu IPv4
   * Brána IPv4
   * Maska podsítě IPv4
   * Pevná Adresa IPv4 pro řadič 0
   * Pevná Adresa IPv4 pro řadič 1
     
     Další informace o použití této rutiny naleznete v [prostředí Windows PowerShell for StorSimple cmdlet reference](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Další kroky
* Chcete-li konfigurovat jiná síťová rozhraní než DATA 0, můžete použít [konfigurovat nastavení sítě na webu Azure Portal](storsimple-8000-modify-device-config.md). 
* Pokud při konfiguraci síťových rozhraní narazíte na nějaké problémy, přečtěte [si článek Řešení potíží s nasazením](storsimple-troubleshoot-deployment.md).

