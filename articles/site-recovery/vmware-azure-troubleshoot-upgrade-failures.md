---
title: Řešení potíží se selháním upgradu Microsoft Azure Site Recovery Providers | Microsoft Docs
description: Pochopení a
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: raynew
ms.openlocfilehash: 6bb8263e7291506be6af7d5af7eda5786274d73c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972633"
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

2. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace. Extrahujte instalační soubory ze souboru ke stažení pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: &lt;folder cesta pro extrahované soubory @ no__t-1.
    
    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: C:\Temp\Extracted

3. Na příkazovém řádku přejděte do složky, do které jste extrahovali soubory, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Vraťte se do složky, do které jste stáhli sjednocenou instalaci, a spuštěním MicrosoftAzureSiteRecoveryUnifiedSetup. exe dokončete upgrade. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Upgrade se nezdařil z důvodu přejmenování složky třetí strany.

Aby byl upgrade úspěšný, nesmí být složka třetí strany přejmenována.

K vyřešení tohoto problému.

1. Spusťte Editor registru (Regedit. exe) a otevřete větev HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Zkontrolujte hodnotu klíče `Build_Version`. Pokud je nastavená na nejnovější verzi, snižte číslo verze. Například pokud je nejnovější verze 9,22. \* a klíč `Build_Version` nastaven na tuto hodnotu, pak ho snižte na 9,21. \*.
1. Stáhněte si nejnovější Microsoft Azure Site Recovery Unified Setup:
   1. V části "odkazy na aktuálně podporované kumulativní aktualizace" [v článku aktualizace služby v Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) vyberte poskytovatele, kterého chcete upgradovat.
   2. Na stránce Souhrn vyhledejte část **informace o aktualizaci** a Stáhněte kumulativní aktualizaci pro Microsoft Azure Site Recovery Unified Setup.
1. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace, a extrahujte instalační soubory ze souboru ke stažení pomocí následujícího příkazu, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: &lt;folder cesta pro extrahovanou složku soubory @ no__t-1.

    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: C:\Temp\Extracted

1. Na příkazovém řádku přejděte do složky, do které jste extrahovali soubory, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. Pomocí Správce úloh můžete monitorovat průběh instalace. V případě procesu pro CX_THIRDPARTY_SETUP. Nástroj EXE již není ve Správci úloh viditelný, přejděte k dalšímu kroku.
1. Ověřte, zda existuje C:\thirdparty a zda složka obsahuje knihovny RRD.
1. Vraťte se do složky, do které jste stáhli sjednocenou instalaci, a spuštěním MicrosoftAzureSiteRecoveryUnifiedSetup. exe dokončete upgrade. 