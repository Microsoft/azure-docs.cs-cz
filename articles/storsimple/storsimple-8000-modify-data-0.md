---
title: Upravit DATA 0 nastavení na zařízení řady StorSimple 8000 | Microsoft Docs
description: Další informace o použití prostředí Windows PowerShell pro StorSimple překonfigurovat síťového rozhraní DATA 0 v zařízení StorSimple.
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
ms.openlocfilehash: 90df43e22f17fd32fe642514df098b72700e77af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927583"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Upravit nastavení 0 síťového rozhraní DATA ve vašem zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled

Zařízení s Microsoft Azure StorSimple má šest síťových rozhraní, z DATA 0 až DATA 5. DATA 0 rozhraní vždycky nakonfigurovaný pomocí rozhraní Windows PowerShell nebo konzole sériového portu a je automaticky povolenou podporu cloudu. Všimněte si, že nemůžete nakonfigurovat síťového rozhraní DATA 0 prostřednictvím portálu Azure.

DATA 0 rozhraní nejprve nakonfigurovaný pomocí Průvodce instalací během počáteční nasazení zařízení StorSimple. Když je zařízení v provozním režimu, bude pravděpodobně třeba překonfigurovat DATA 0 nastavení. V tomto kurzu nabízí dvě metody k úpravě DATA 0 síťová nastavení, jak pomocí prostředí Windows PowerShell pro StorSimple.

Po přečtení tohoto kurzu, budete moci:

* Upravit DATA 0 síťová nastavení prostřednictvím Průvodce instalací
* Upravit nastavení DATA 0 sítě prostřednictvím `Set-HcsNetInterface` rutiny

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Upravit nastavení DATA 0 sítě prostřednictvím Průvodce instalací
Můžete změnit konfiguraci dat nastavení 0 sítě pomocí připojení k rozhraní Windows PowerShell zařízení StorSimple a spuštění relace Průvodce instalací. Proveďte následující kroky k úpravě DATA 0 nastavení:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>K úpravě nastavení DATA 0 sítě prostřednictvím Průvodce instalací
1. V nabídce konzoly sériového portu, zvolte možnost 1, **přihlásit úplný přístup**. Po zobrazení výzvy zadejte **hesla správce zařízení**. Výchozí heslo je `Password1`.
2. Na příkazovém řádku zadejte:
   
    `Invoke-HcsSetupWizard`
3. Zobrazí se Průvodce instalací vám pomohou při konfiguraci DATA 0 rozhraní vašeho zařízení. Zadejte nové hodnoty pro adresu IP, brány a síťovou masku.

> [!NOTE]
> Opravené řadiče IP adres bude třeba překonfigurovat pomocí **nastavení síťového** okno zařízení StorSimple na portálu Azure. Další informace, přejděte na [upravit síťových rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Úprava nastavení DATA 0 sítě pomocí rutiny Set-HcsNetInterface
Alternativní způsob překonfigurovat DATA 0 síťové rozhraní je prostřednictvím `Set-HcsNetInterface` rutiny. Rutina se spustí z rozhraní Windows PowerShell zařízení StorSimple. Při použití tohoto postupu, pevné IP adresy řadiče také zde mohou být konfigurovány. Proveďte následující kroky k úpravě DATA 0 nastavení: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Úprava nastavení DATA 0 sítě pomocí rutiny Set-HcsNetInterface
1. V nabídce konzoly sériového portu, zvolte možnost 1, **přihlásit úplný přístup**. Po zobrazení výzvy zadejte heslo správce zařízení. Výchozí heslo je `Password1`.
2. Na příkazovém řádku zadejte:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    V lomené závorky zadejte následující hodnoty pro DATA 0:
   
   * Adresa IPv4
   * Brána IPv4
   * Maska podsítě IPv4
   * Opravené adresu IPv4 pro řadič 0
   * Opravené adresu IPv4 pro řadič 1
     
     Další informace o použití této rutiny, přejděte na [prostředí Windows PowerShell pro referenční informace o rutinách StorSimple](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Další kroky
* Ke konfiguraci síťových rozhraní než DATA 0, můžete použít [konfigurace nastavení sítě na portálu Azure](storsimple-8000-modify-device-config.md). 
* Pokud máte problémy při konfiguraci síťových rozhraní, podívejte se na [potíží s nasazením](storsimple-troubleshoot-deployment.md).

