---
title: Instalace aktualizace 4 na zařízení StorSimple řady 8000 | Dokumentace Microsoftu
description: Vysvětluje, jak nainstalovat StorSimple 8000 Series Update 4 na vašem zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 57d6d63c55f8ad4da5d1905a1e209da454b0491c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630190"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalace aktualizace 4 na zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak instalace aktualizace 4 na zařízení StorSimple používá starší verzi softwaru prostřednictvím webu Azure portal a pomocí metody opravy hotfix. Oprava hotfix metoda se používá při brány je nakonfigurovaný v síťovém rozhraní než DATA 0 zařízení StorSimple a se pokoušíte aktualizovat z verze 1 před aktualizací softwaru.

Update 4 zahrnuje software pro zařízení, USM firmwaru, aktualizace zabezpečení operačního systému LSI pro ovladače a firmware, Storport a Spaceport a celou řadu dalších aktualizací operačního systému.  Software pro zařízení, USM firmwaru, Spaceport, Storport a jiné aktualizace operačního systému jsou nenarušující aktualizace. Nenarušující nebo pravidelné aktualizace lze použít prostřednictvím webu Azure portal nebo prostřednictvím metody opravy hotfix. Aktualizace firmwaru disku se o narušující aktualizace a lze použít pouze prostřednictvím oprav hotfix metodu pomocí rozhraní Windows PowerShell zařízení.

> [!IMPORTANT]
> * Sada ručního a automatického předběžné kontroly se provádějí před instalací určit stav zařízení z hlediska hardwaru stavu a připojení k síti. Tyto předběžné kontroly jsou prováděny pouze v případě, že se aktualizace z webu Azure portal.
> * Doporučujeme nainstalovat software a další pravidelné aktualizace přes Azure portal. By měl pouze přejděte na rozhraní prostředí Windows PowerShell zařízení (pro instalaci aktualizací) Pokud se nezdaří Kontrola brány před aktualizací na portálu. V závislosti na verzi provádíte aktualizaci ze, instalace aktualizací může trvat 4 hodiny (nebo vyšší) k instalaci. Aktualizace režimu údržby musí být nainstalován také prostřednictvím rozhraní Windows PowerShell zařízení. Jak aktualizace režimu údržby se o narušující aktualizace, tyto povede k výpadkům pro vaše zařízení.
> * Pokud používáte volitelné StorSimple Snapshot Manageru, upgradovat můžete mít vaše Snapshot Manageru verze Update 4 před aktualizací zařízení.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalace aktualizace 4 prostřednictvím webu Azure portal
Proveďte následující kroky a aktualizujete zařízení [s aktualizací Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft si vyžádá dalších diagnostických informací ze zařízení. V důsledku toho pokud naše provozní tým identifikuje zařízení, která máte problémy, je lepší jsou shromažďovat informace ze zařízení a diagnostikovat problémy. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Ověřte, že vaše zařízení běží **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. **Datum poslední aktualizace** také by měl být upraven.

* Nyní uvidíte, že jsou k dispozici aktualizace režimu údržby (Tato zpráva může i nadále bude zobrazovat u až 24 hodin, po instalaci aktualizací). Aktualizace režimu údržby se o narušující aktualizace způsobit výpadek zařízení, jež lze použít pouze prostřednictvím rozhraní Windows PowerShell vašeho zařízení.

* Stažení aktualizace režimu údržby pomocí kroků uvedených v [stažení opravy hotfix](#to-download-hotfixes) vyhledávat a stahovat KB4011837, které instaluje aktualizace firmwaru disku (jiné aktualizace by měla být nainstalovaná nyní). Postupujte podle kroků uvedených v [instalace a ověření oprav hotfix režimu údržby](#to-install-and-verify-maintenance-mode-hotfixes) do režimu údržby instalace aktualizací.

## <a name="install-update-4-as-a-hotfix"></a>Instalace aktualizace 4 jako oprava hotfix
Doporučujeme nainstalovat Update 4 je prostřednictvím portálu Azure portal.

Tento postup použijte, pokud se nezdaří Kontrola brány při pokusu o instalaci aktualizace na webu Azure portal. Kontrola selže, máte bránu přiřazená 0 síťové rozhraní než DATA a vaše zařízení používá verzi starší než Update 1.

Verze softwaru, které je možné upgradovat pomocí metody opravy hotfix jsou:

* Aktualizace 0.1, 0.2 a 0.3
* Aktualizace 1, 1.1, 1.2
* Update 2, 2.1 nebo 2.2
* Aktualizací Update 3, 3.1


Metoda oprav hotfix zahrnuje následující tři kroky:

1. Stažení opravy hotfix z katalogu služby Microsoft Update.
2. Instalace a ověření oprav hotfix běžného režimu.
3. Instalace a ověření oprav hotfix režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro vaše zařízení

Je nutné stáhnout a nainstalovat následující opravy hotfix předepsané pořadí a navrhované složky:

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Aktualizace softwaru |Pravidelné <br></br>Bez přerušení |~ 25 minut |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Adaptér LSI ovladače a firmware aktualizace <br> Aktualizace firmwaru USM (verze 3.38) |Pravidelné <br></br>Bez přerušení |~ 3 hodiny <br> (včetně 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398 KB3108381 <br> KB3205400 KB3142030 <br> KB3197873 KB3197873 <br> KB3192392 KB3153704 <br> KB3174644 KB3139914  |Balíček aktualizace zabezpečení operačního systému <br> Stažení Windows serveru 2012 R2 |Pravidelné <br></br>Bez přerušení |- |SecondOrderUpdate|
| 2C. |KB3210083 KB3103616 <br> KB3146621 KB3121261 <br> KB3123538 |Balíček aktualizací operačního systému <br> Stažení Windows serveru 2012 R2 |Pravidelné <br></br>Bez přerušení |- |SecondOrderUpdate|

Budete také muset nainstalovat aktualizace firmwaru disku nad všechny aktualizace uvedené v předchozích tabulkách. Můžete ověřit, jestli potřebujete aktualizace firmwaru disku spuštěním `Get-HcsFirmwareVersion` rutiny. Pokud používáte tyto verze firmwaru: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, potom není potřeba instalovat tyto aktualizace.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace | Instalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmwaru disku |Údržba <br></br>Ničivé |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Tento postup je třeba provést pouze jednou použít aktualizaci Update 4. Na webu Azure portal můžete použít následující aktualizace.
> * Pokud aktualizace Update 3 nebo 3.1, blíží k celkové instalaci 4 hodiny.
> * Než použijete tento postup můžete použít aktualizaci, ujistěte se, že oba kontrolery zařízení jsou online a všechny hardwarové komponenty jsou v pořádku.

Proveďte následující kroky ke stažení a instalaci opravy hotfix.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Další postup
Další informace o [vydání verze Update 4](storsimple-update4-release-notes.md).

