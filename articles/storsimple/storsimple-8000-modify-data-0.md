---
title: Úprava nastavení DATA 0 na zařízení řady StorSimple 8000 | Microsoft Docs
description: Naučte se používat Windows PowerShell pro StorSimple k překonfigurování síťového rozhraní DATA 0 na zařízení StorSimple.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961035"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Úprava nastavení síťového rozhraní DATA 0 na zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Vaše zařízení Microsoft Azure StorSimple má šest síťových rozhraní, od DATA 0 po DATA 5. Rozhraní DATA 0 se vždycky konfiguruje přes rozhraní Windows PowerShell nebo pomocí konzole sériového prostředí a automaticky se povoluje pro Cloud. Všimněte si, že nemůžete konfigurovat síťové rozhraní DATA 0 pomocí Azure Portal.

Rozhraní DATA 0 se před počátečním nasazením zařízení StorSimple nakonfiguruje pomocí Průvodce instalací. Když je zařízení v provozním režimu, možná budete muset změnit konfiguraci nastavení DATA 0. V tomto kurzu najdete dvě metody úpravy nastavení sítě DATA 0, a to prostřednictvím Windows PowerShell pro StorSimple.

Po přečtení tohoto kurzu budete moct:

* Úprava nastavení sítě DATA 0 pomocí Průvodce instalací
* Úprava nastavení sítě DATA 0 pomocí `Set-HcsNetInterface` rutiny

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Úprava nastavení sítě DATA 0 pomocí Průvodce instalací
Nastavení sítě DATA 0 můžete znovu nakonfigurovat připojením k rozhraní Windows PowerShell zařízení StorSimple a spuštěním relace Průvodce instalací. Chcete-li upravit nastavení DATA 0, proveďte následující kroky:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Úprava nastavení sítě DATA 0 pomocí Průvodce instalací
1. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte **heslo správce zařízení**. Výchozí heslo je `Password1` .
2. Na příkazovém řádku zadejte:
   
    `Invoke-HcsSetupWizard`
3. Zobrazí se Průvodce instalací, který vám umožní nakonfigurovat rozhraní DATA 0 vašeho zařízení. Zadejte nové hodnoty pro IP adresu, bránu a síťovou masku.

> [!NOTE]
> IP adresy pevných řadičů se musí překonfigurovat přes okno **nastavení sítě** zařízení StorSimple v Azure Portal. Další informace najdete v [úpravách síťových rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Úprava nastavení sítě DATA 0 pomocí rutiny Set-HcsNetInterface
Alternativní způsob, jak znovu nakonfigurovat síťové rozhraní DATA 0, je pomocí `Set-HcsNetInterface` rutiny. Rutina se spustí z rozhraní Windows PowerShell vašeho zařízení StorSimple. Pokud použijete tento postup, můžete nakonfigurovat také pevné IP adresy řadiče. Chcete-li upravit nastavení DATA 0, proveďte následující kroky: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Úprava nastavení sítě DATA 0 pomocí rutiny Set-HcsNetInterface
1. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**. Po zobrazení výzvy zadejte heslo správce zařízení. Výchozí heslo je `Password1` .
2. Na příkazovém řádku zadejte:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Do lomených závorek zadejte následující hodnoty pro DATA 0:
   
   * Adresa protokolu IPv4
   * Brána IPv4
   * Maska podsítě IPv4
   * Pevná IPv4 adresa pro řadič 0
   * Pevná adresa IPv4 pro řadič 1
     
     Další informace o použití této rutiny najdete v části [Windows PowerShell pro StorSimple Reference k rutinám](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)).

## <a name="next-steps"></a>Další kroky
* Pokud chcete nakonfigurovat jiná síťová rozhraní než DATA 0, můžete použít [nastavení konfigurace sítě v Azure Portal](storsimple-8000-modify-device-config.md). 
* Pokud při konfiguraci síťových rozhraní dojde k problémům, přečtěte si téma [řešení potíží s nasazením](./storsimple-8000-troubleshoot-deployment.md).