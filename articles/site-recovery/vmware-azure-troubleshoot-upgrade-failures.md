---
title: Řešení potíží se selháním upgradujte zprostředkovatele služby Microsoft Azure Site Recovery | Dokumentace Microsoftu
description: Pochopení a
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: 5a328e64128503f98476deccf33a4395f99eeeb0
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985166"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Řešení potíží se selháním upgradujte zprostředkovatele služby Microsoft Azure Site Recovery

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

## <a name="upgrade-failure-due-to-the-thirdparty-folder-being-renamed"></a>Upgrade selhalo, protože se přejmenovat složku třetích stran

Pro úspěch upgradu nesmí přejmenovat složku třetích stran.

K vyřešení daného problému.

2. Spusťte Editor registru (regedit.exe) a otevřete HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 větve.
3. Zkontrolujte `Build_Version` hodnotu klíče. Pokud se nastaví na nejnovější verzi, snižte číslo verze. Například, pokud je nejnovější verze 9.22. \* a `Build_Version` klíč nastavený na tuto hodnotu a pak snížit na 9.21.\*.
4. Stáhněte si nejnovější instalaci Microsoft Azure Site Recovery Unified:
   1. V části "Odkazy na aktuálně podporovaných kumulativních aktualizací" [aktualizací ve službě Azure Site Recovery služby](service-updates-how-to.md##links-to-currently-supported-update-rollups) článek, vyberte poskytovatele, do které provádíte upgrade.
   2. Na stránce Souhrn vyhledejte **aktualizovat informace o** části a stáhněte si kumulativní aktualizace pro Microsoft Azure Site Recovery sjednocené instalace.
5. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace a extract instalační soubory z položky ke stažení pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q/x:&lt;cesta ke složce pro rozbalené soubory&gt;.

    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

4. V příkazovém řádku přejděte do složky, do které jste extrahovali soubory a spusťte následující příkazy pro instalaci:
   
    CX_THIRDPARTY_SETUP. / NORESTART /SUPPRESSMSGBOXES /VERYSILENT EXE

5. Použijte Správce úloh můžete sledovat průběh instalace. Pokud proces CX_THIRDPARTY_SETUP. Už není viditelná ve Správci úloh EXE, pokračujte k dalšímu kroku.
6. Ověřte, že C:\thirdparty existuje a že složka obsahuje RRD knihovny.
1. Vraťte se do složky, do které jste stáhli sjednocené instalace a spuštění MicrosoftAzureSiteRecoveryUnifiedSetup.exe dokončit upgrade. 