---
title: Řešení potíží s upgradem poskytovatele Microsoft Azure Site Recovery
description: Řešení běžných problémů při upgradu poskytovatele Microsoft Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 73309103d422391aae3d72e3b0267d5d5a85515a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953758"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Řešení potíží se selháním upgradu Microsoft Azure Site Recovery Provider

Tento článek vám pomůže vyřešit problémy, které mohou způsobit chyby během upgradu Microsoft Azure Site Recovery.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Upgrade neprojde hlášením, že je už nainstalovaný nejnovější poskytovatel Site Recovery.

Při upgradu zprostředkovatele Microsoft Azure Site Recovery Provider (DRA) se nezdařil upgrade sjednocené instalace a zobrazí se chybová zpráva:

Upgrade není podporován, protože je již nainstalována vyšší verze softwaru.

K upgradu použijte následující postup:

1. Stáhněte si Microsoft Azure Site Recovery Unified Setup:
   1. V části "odkazy na aktuálně podporované kumulativní aktualizace" [v článku aktualizace služby v Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) vyberte poskytovatele, kterého chcete upgradovat.
   2. Na stránce Souhrn vyhledejte část **informace o aktualizaci** a Stáhněte kumulativní aktualizaci pro Microsoft Azure Site Recovery Unified Setup.

2. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace. Extrahujte instalační soubory ze souboru ke stažení pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;cestu ke složce extrahovaných souborů&gt;.
    
    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: C:\Temp\Extracted

3. Na příkazovém řádku přejděte do složky, do které jste extrahovali soubory, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Vraťte se do složky, do které jste stáhli sjednocenou instalaci, a spuštěním MicrosoftAzureSiteRecoveryUnifiedSetup. exe dokončete upgrade. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Upgrade se nezdařil z důvodu přejmenování složky třetí strany.

Aby byl upgrade úspěšný, nesmí být složka třetí strany přejmenována.

K vyřešení tohoto problému.

1. Spusťte Editor registru (Regedit. exe) a otevřete větev HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Zkontrolujte hodnotu `Build_Version` klíč. Pokud je nastavená na nejnovější verzi, snižte číslo verze. Například pokud je nejnovější verze 9,22.\* a klíč `Build_Version` na tuto hodnotu nastavte a pak ho snižte na 9,21.\*.
1. Stáhněte si nejnovější Microsoft Azure Site Recovery Unified Setup:
   1. V části "odkazy na aktuálně podporované kumulativní aktualizace" [v článku aktualizace služby v Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) vyberte poskytovatele, kterého chcete upgradovat.
   2. Na stránce Souhrn vyhledejte část **informace o aktualizaci** a Stáhněte kumulativní aktualizaci pro Microsoft Azure Site Recovery Unified Setup.
1. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace, a extrahujte instalační soubory ze souboru ke stažení pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;cestu ke složce extrahovaných souborů&gt;.

    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: C:\Temp\Extracted

1. Na příkazovém řádku přejděte do složky, do které jste extrahovali soubory, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. Pomocí Správce úloh můžete monitorovat průběh instalace. Při CX_THIRDPARTY_SETUP procesu. Nástroj EXE již není ve Správci úloh viditelný, přejděte k dalšímu kroku.
1. Ověřte, zda existuje C:\thirdparty a zda složka obsahuje knihovny RRD.
1. Vraťte se do složky, do které jste stáhli sjednocenou instalaci, a spuštěním MicrosoftAzureSiteRecoveryUnifiedSetup. exe dokončete upgrade. 