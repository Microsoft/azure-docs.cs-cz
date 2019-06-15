---
title: Upravit DATA 0 nastavení na zařízení StorSimple řady 8000 | Dokumentace Microsoftu
description: Zjistěte, jak změnit konfiguraci síťové rozhraní DATA 0 v zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631579"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>Úprava nastavení DATA 0 síťového rozhraní na zařízení StorSimple 8000 series

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple zařízení má šest síťových rozhraní, z dat 0 až DATA 5. DATA 0 rozhraní se vždy konfiguruje prostřednictvím rozhraní Windows Powershellu nebo konzoly sériového portu a je automaticky povolenou podporu cloudu. Všimněte si, že nemůžete nakonfigurovat síťového rozhraní DATA 0 na webu Azure portal.

DATA 0 rozhraní je nejprve nakonfigurovat prostřednictvím Průvodce instalací během počáteční nasazení zařízení StorSimple. Když je zařízení v provozním režimu, je nutné překonfigurovat DATA 0 nastavení. Tento kurz obsahuje dvě metody pro změny dat 0 síťových nastavení, i přes Windows PowerShell pro StorSimple.

Po přečtení tohoto kurzu, budete moct:

* Upravit DATA 0 nastavení prostřednictvím Průvodce nastavením sítě
* Úprava nastavení DATA 0 sítě prostřednictvím `Set-HcsNetInterface` rutiny

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Úprava nastavení DATA 0 sítě pomocí Průvodce instalací
Nastavení DATA 0 sítě můžete překonfigurovat tak, že připojení k rozhraní Windows PowerShell vašeho zařízení StorSimple a spuštění relace Průvodce instalací. Proveďte následující kroky a upravovat DATA 0 nastavení:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Chcete-li změnit nastavení DATA 0 sítě pomocí Průvodce instalací
1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**. Po zobrazení výzvy zadejte **hesla správce zařízení**. Výchozí heslo je `Password1`.
2. Na příkazovém řádku zadejte:
   
    `Invoke-HcsSetupWizard`
3. Pomoc s konfigurací DATA 0 se zobrazí Průvodce instalací rozhraní vašeho zařízení. Zadejte nové hodnoty pro IP adresu, brány a síťovou masku.

> [!NOTE]
> Kontrolery pevné IP adresy se bude třeba překonfigurovat prostřednictvím **nastavení sítě** okno zařízení StorSimple na webu Azure Portal. Další informace najdete v části [změnit síťová rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces).

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Úprava nastavení DATA 0 sítě pomocí rutiny Set-HcsNetInterface
Existuje alternativní způsob překonfigurovat DATA 0 je síťové rozhraní prostřednictvím `Set-HcsNetInterface` rutiny. Rutina se spustí z rozhraní Windows PowerShell vašeho zařízení StorSimple. Při použití tohoto postupu, pevné IP adresy řadiče lze také nastavit tady. Proveďte následující kroky, chcete-li změnit údaj 0 nastavení: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Chcete-li změnit nastavení DATA 0 sítě pomocí rutiny Set-HcsNetInterface
1. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**. Po zobrazení výzvy zadejte heslo správce zařízení. Výchozí heslo je `Password1`.
2. Na příkazovém řádku zadejte:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    V ostrými závorkami zadejte následující hodnoty pro DATA 0:
   
   * Adresa protokolu IPv4
   * Brána IPv4
   * Maskou podsítě typu IPv4
   * Oprava IPv4 adresu pro řadič 0
   * Oprava IPv4 adresu pro řadič 1
     
     Další informace o použití této rutiny najdete v části [prostředí Windows PowerShell pro StorSimple Reference k rutinám](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Další postup
* Ke konfiguraci síťových rozhraní než DATA 0, můžete použít [konfigurace nastavení sítě na webu Azure Portal](storsimple-8000-modify-device-config.md). 
* Pokud zaznamenáte problémy při konfiguraci síťových rozhraní, podívejte se na [řešení problémů s nasazením](storsimple-troubleshoot-deployment.md).

