---
title: Poradce při potížích s upgradem zprostředkovatele obnovení webu Microsoft Azure
description: Vyřešte běžné problémy, ke kterým dochází při upgradu zprostředkovatele obnovení webu Microsoft Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893898"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Řešení potíží se selháním upgradu Microsoft Azure Site Recovery Provider

Tento článek vám pomůže vyřešit problémy, které mohou způsobit chyby během upgradu zprostředkovatele obnovení webu Microsoft Azure.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Upgrade se nezdaří s oznámením, že je již nainstalován nejnovější zprostředkovatel obnovení webu.

Při upgradu zprostředkovatele obnovení webu Microsoft Azure (DRA) se nezdaří upgrade sjednocené instalace a zobrazí se chybová zpráva:

Upgrade není podporován, protože vyšší verze softwaru je již nainstalována.

Chcete-li provést upgrade, postupujte takto:

1. Stáhněte si sjednocené nastavení obnovení webu Microsoft Azure:
   1. V části Odkazy na aktuálně podporované kumulativní aktualizace v [článku Aktualizace služby v](service-updates-how-to.md#links-to-currently-supported-update-rollups) článku Obnovení webu Azure vyberte zprostředkovatele, na kterého upgradujete.
   2. Na souhrnné stránce vyhledejte část **Informace o aktualizaci** a stáhněte si kumulativní aktualizaci pro sjednocené nastavení obnovení webu Microsoft Azure.

2. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli sjednocený instalační soubor. Extrahujte instalační soubory ze stahování pomocí následujícího příkazu MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q&lt;&gt;/x: cesta ke složce pro extrahované soubory .
    
    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. V příkazovém řádku přejděte do složky, do které jste soubory extrahovali, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Vraťte se do složky, do které jste stáhli sjednocené nastavení, a spusťte program MicrosoftAzureSiteRecoveryUnifiedSetup.exe, který dokončí upgrade. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Selhání upgradu z důvodu přejmenování složky třetí strany

Aby byl upgrade úspěšný, nesmí být složka třetí strany přejmenována.

Chcete-li problém vyřešit.

1. Spusťte Editor registru (regedit.exe) a otevřete větev HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Zkontrolujte `Build_Version` hodnotu klíče. Pokud je nastavena na nejnovější verzi, snižte počet verzí. Například pokud je nejnovější verze 9.22. \* a `Build_Version` klíč nastavený na tuto hodnotu, pak ji snižte na 9,21. \*.
1. Stáhněte si nejnovější sjednocené nastavení microsoft azure webu:
   1. V části Odkazy na aktuálně podporované kumulativní aktualizace v [článku Aktualizace služby v](service-updates-how-to.md#links-to-currently-supported-update-rollups) článku Obnovení webu Azure vyberte zprostředkovatele, na kterého upgradujete.
   2. Na souhrnné stránce vyhledejte část **Informace o aktualizaci** a stáhněte si kumulativní aktualizaci pro sjednocené nastavení obnovení webu Microsoft Azure.
1. Otevřete příkazový řádek a přejděte do složky, do které jste stáhli sjednocený instalační soubor, a extrahujte instalační soubory&lt;ze stahování pomocí&gt;následujícího příkazu MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x: cesta ke složce pro extrahované soubory .

    Příklad příkazu:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. V příkazovém řádku přejděte do složky, do které jste soubory extrahovali, a spusťte následující instalační příkazy:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Pomocí Správce úloh můžete sledovat průběh instalace. Když proces pro CX_THIRDPARTY_SETUP. EXE již není ve Správci úloh viditelná, přejděte k dalšímu kroku.
1. Ověřte, zda existuje c:\třetí strana a zda složka obsahuje knihovny RRD.
1. Vraťte se do složky, do které jste stáhli sjednocené nastavení, a spusťte program MicrosoftAzureSiteRecoveryUnifiedSetup.exe, který dokončí upgrade. 