---
title: Řešení potíží se selháním upgradujte zprostředkovatele služby Microsoft Azure Site Recovery | Dokumentace Microsoftu
description: Pochopení a
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: fc50be2a960784895947f3f154a0251f41716fc7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117298"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Řešení potíží se selháním upgradu Microsoft Azure Site Recovery Provider

Tento článek pomůže při řešení problémů, které může dojít k chybám při zprostředkovatele Microsoft Azure Site Recovery upgradovat.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Upgrade selže, vytváření sestav, že je již nainstalována nejnovější zprostředkovatele Site Recovery

Při upgradu aplikace Microsoft Azure Site Recovery Providera (DRA), upgrade sjednocené instalace selže a vydá chybovou zprávu:

Upgrade se nepodporuje, protože už je nainstalovaná novější verze tohoto softwaru.

Pokud chcete upgradovat, použijte následující kroky:

1. Stáhněte si Microsoft Azure Site Recovery sjednocené instalace:
   1. V části "Odkazy na aktuálně podporovaných kumulativních aktualizací" [aktualizací ve službě Azure Site Recovery služby](service-updates-how-to.md##links-to-currently-supported-update-rollups) článek, vyberte poskytovatele, do které provádíte upgrade.
   2. Na stránce Souhrn vyhledejte **aktualizovat informace o** části a stáhněte si kumulativní aktualizace pro Microsoft Azure Site Recovery sjednocené instalace.

2. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace. Extrahovat soubory instalačního programu z stáhnout pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q/x:&lt;cesta ke složce pro extrahované soubory&gt;.
    
    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. V příkazovém řádku přejděte do složky, do které jste extrahovali soubory a spusťte následující příkazy pro instalaci:
   
    CX_THIRDPARTY_SETUP. SOUBOR EXE /VERYSILENT /SUPPRESSMSGBOXES/NORESTART / UCX_SERVER_SETUP. SOUBOR EXE /VERYSILENT /SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Vraťte se do složky, do které jste stáhli sjednocené instalace a spuštění MicrosoftAzureSiteRecoveryUnifiedSetup.exe dokončit upgrade. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Upgrade nezdaří z důvodu složce 3. stran Probíhá přejmenování

Pro úspěch upgradu nesmí přejmenovat složku 3. stran.

K vyřešení daného problému.

1. Spusťte Editor registru (regedit.exe) a otevřete HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 větve.
1. Zkontrolujte `Build_Version` hodnotu klíče. Pokud se nastaví na nejnovější verzi, snižte číslo verze. Například, pokud je nejnovější verze 9.22. \* a `Build_Version` klíč nastavený na tuto hodnotu a pak snížit na 9.21.\*.
1. Stáhněte si nejnovější instalaci Microsoft Azure Site Recovery Unified:
   1. V části "Odkazy na aktuálně podporovaných kumulativních aktualizací" [aktualizací ve službě Azure Site Recovery služby](service-updates-how-to.md##links-to-currently-supported-update-rollups) článek, vyberte poskytovatele, do které provádíte upgrade.
   2. Na stránce Souhrn vyhledejte **aktualizovat informace o** části a stáhněte si kumulativní aktualizace pro Microsoft Azure Site Recovery sjednocené instalace.
1. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace a extract instalační soubory z položky ke stažení pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q/x:&lt;cesta ke složce pro rozbalené soubory&gt;.

    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. V příkazovém řádku přejděte do složky, do které jste extrahovali soubory a spusťte následující příkazy pro instalaci:
   
    CX_THIRDPARTY_SETUP. / NORESTART /SUPPRESSMSGBOXES /VERYSILENT EXE

1. Použijte Správce úloh můžete sledovat průběh instalace. Pokud proces CX_THIRDPARTY_SETUP. Už není viditelná ve Správci úloh EXE, pokračujte k dalšímu kroku.
1. Ověřte, že C:\thirdparty existuje a že složka obsahuje RRD knihovny.
1. Vraťte se do složky, do které jste stáhli sjednocené instalace a spuštění MicrosoftAzureSiteRecoveryUnifiedSetup.exe dokončit upgrade. 