---
title: Řešení potíží s upgradem poskytovatele Microsoft Azure Site Recovery
description: Řešení běžných problémů, ke kterým dochází při upgradu poskytovatele Microsoft Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 5a6e4b415a9fe8ea80a84e415879df9d2f359478
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84434367"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Řešení potíží se selháním upgradu Microsoft Azure Site Recovery Provider

Tento článek vám pomůže vyřešit problémy, které mohou způsobit chyby během upgradu Microsoft Azure Site Recovery.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Upgrade neprojde hlášením, že je už nainstalovaný nejnovější poskytovatel Site Recovery.

Při upgradu zprostředkovatele Microsoft Azure Site Recovery Provider (DRA) se nezdařil upgrade sjednocené instalace a zobrazí se chybová zpráva:

Upgrade není podporován, protože je již nainstalována vyšší verze softwaru.

K upgradu použijte následující postup:

1. Stáhněte si Microsoft Azure Site Recovery Unified Setup:
   1. V části "odkazy na aktuálně podporované kumulativní aktualizace" [v článku aktualizace služby v Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) vyberte poskytovatele, kterého chcete upgradovat.
   2. Na stránce Souhrn vyhledejte část **informace o aktualizaci** a Stáhněte kumulativní aktualizaci pro Microsoft Azure Site Recovery Unified Setup.

2. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace. Extrahujte instalační soubory ze souboru ke stažení pomocí následujícího příkazu MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: &lt; cesta ke složce pro extrahované soubory &gt; .
    
    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: C:\Temp\Extracted

3. Na příkazovém řádku přejděte do složky, do které jste extrahovali soubory, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP.EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP.EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Vraťte se do složky, do které jste stáhli sjednocenou instalaci, a spusťte MicrosoftAzureSiteRecoveryUnifiedSetup.exe pro dokončení upgradu. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Upgrade se nezdařil z důvodu přejmenování složky třetí strany.

Aby byl upgrade úspěšný, nesmí být složka třetí strany přejmenována.

Pokud chcete tento problém vyřešit,

1. Spusťte Editor registru (regedit.exe) a otevřete větev HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Zkontrolujte `Build_Version` hodnotu klíče. Pokud je nastavená na nejnovější verzi, snižte číslo verze. Například pokud je nejnovější verze 9,22. \* a `Build_Version` nastavte klíč na tuto hodnotu a pak ho snižte na 9,21. \* .
1. Stáhněte si nejnovější Microsoft Azure Site Recovery Unified Setup:
   1. V části "odkazy na aktuálně podporované kumulativní aktualizace" [v článku aktualizace služby v Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) vyberte poskytovatele, kterého chcete upgradovat.
   2. Na stránce Souhrn vyhledejte část **informace o aktualizaci** a Stáhněte kumulativní aktualizaci pro Microsoft Azure Site Recovery Unified Setup.
1. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli soubor sjednocené instalace, a extrahujte instalační soubory ze souboru ke stažení pomocí následujícího příkazu MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: &lt; cesta ke složce pro extrahované soubory &gt; .

    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe/q/x: C:\Temp\Extracted

1. Na příkazovém řádku přejděte do složky, do které jste extrahovali soubory, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP.EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. Pomocí Správce úloh můžete monitorovat průběh instalace. Pokud proces pro CX_THIRDPARTY_SETUP.EXE již není viditelný ve Správci úloh, přejděte k dalšímu kroku.
1. Ověřte, zda existuje C:\thirdparty a zda složka obsahuje knihovny RRD.
1. Vraťte se do složky, do které jste stáhli sjednocenou instalaci, a spusťte MicrosoftAzureSiteRecoveryUnifiedSetup.exe pro dokončení upgradu.

## <a name="upgrade-failure-due-to-master-target-installation-failure"></a>Upgrade se nezdařil z důvodu chyby instalace hlavního cíle.

Při upgradu zprostředkovatele Microsoft Azure Site Recovery Provider (DRA) se instalace hlavního cíle nezdařila s chybou instalační umístění neexistuje nebo má 1 GB volného místa nebo neexistuje na pevné jednotce.

Příčinou může být hodnota null pro parametr v klíči registru. Řešení problému –

1. Spusťte Editor registru (regedit.exe) a otevřete větev HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\4.
1. Zkontrolujte hodnotu klíče ' InstallDirectory '. Pokud má hodnotu null, přidejte hodnotu aktuální instalační adresář.
1. Podobně otevřete větev HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5 v editoru registru.
1. Zkontrolujte hodnotu klíče InstallDirectory a přidejte hodnotu aktuální instalační adresář.
1. Spusťte znovu instalační program sjednocené instalace.
