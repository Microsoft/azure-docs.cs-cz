---
title: Nainstalujte na zařízení StorSimple 8000 series Update 5 | Dokumentace Microsoftu
description: Vysvětluje, jak nainstalovat StorSimple 8000 Series Update 5 na vašem zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d6e17c7609fd41b8f4457edda373f6882a1a9d2b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698706"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Nainstalujte na zařízení StorSimple Update 5

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak nainstalovat aktualizace 5 na zařízení StorSimple používá starší verzi softwaru prostřednictvím webu Azure portal a pomocí metody opravy hotfix. Oprava hotfix metoda se používá, když se pokoušíte nainstalovat na zařízení se systémem 3 verze před aktualizací Update 5. Metoda oprava hotfix se také používá při brány je nakonfigurovaný v síťovém rozhraní než DATA 0 zařízení StorSimple a se pokoušíte aktualizovat z verze 1 před aktualizací softwaru.

Aktualizace 5 zahrnuje software pro zařízení, ovladače Storport a Spaceport, aktualizace zabezpečení operačního systému a aktualizace operačního systému a aktualizace firmwaru disku.  Software pro zařízení, Spaceport, Storport, zabezpečení a dalších aktualizací operačního systému jsou nenarušující aktualizace. Nenarušující nebo pravidelné aktualizace lze použít prostřednictvím webu Azure portal nebo prostřednictvím metody opravy hotfix. Aktualizace firmwaru disku se o narušující aktualizace a použijí, jakmile je zařízení v režimu údržby prostřednictvím oprav hotfix metodu pomocí rozhraní Windows PowerShell zařízení.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a by měly být nainstalovány okamžitě. Další informace najdete v tématu [zpráva k vydání verze Update 5](storsimple-update5-release-notes.md).
> * Sada ručního a automatického předběžné kontroly se provádějí před instalací určit stav zařízení z hlediska hardwaru stavu a připojení k síti. Tyto předběžné kontroly jsou prováděny pouze v případě, že se aktualizace z webu Azure portal.
> * Důrazně doporučujeme, že při aktualizaci zařízení se systémem verze starší než Update 3 se po instalaci aktualizací pomocí metody opravy hotfix. Pokud budete mít nějaké problémy, [lístku podpory](storsimple-8000-contact-microsoft-support.md).
> * Doporučujeme nainstalovat software a další pravidelné aktualizace přes Azure portal. By měl pouze přejděte na rozhraní prostředí Windows PowerShell zařízení (pro instalaci aktualizací) Pokud se nezdaří Kontrola brány před aktualizací na portálu. V závislosti na verzi provádíte aktualizaci ze, instalace aktualizací může trvat 4 hodiny (nebo vyšší) k instalaci. Aktualizace režimu údržby musí být nainstalován prostřednictvím rozhraní Windows PowerShell zařízení. Jak aktualizace režimu údržby se o narušující aktualizace, tyto vést k výpadkům pro vaše zařízení.
> * Pokud používáte volitelné StorSimple Snapshot Manageru, upgradovat můžete mít vaše Snapshot Manageru verze Update 5 před aktualizací zařízení.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalace aktualizace 5 prostřednictvím webu Azure portal
Proveďte následující kroky a aktualizujete zařízení [aktualizace Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft si vyžádá dalších diagnostických informací ze zařízení. V důsledku toho pokud naše provozní tým identifikuje zařízení, která máte problémy, je lepší jsou shromažďovat informace ze zařízení a diagnostikovat problémy.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ověřte, že vaše zařízení běží **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. **Datum poslední aktualizace** by měl být upraven.

Nyní uvidíte, že jsou k dispozici aktualizace režimu údržby (Tato zpráva může i nadále bude zobrazovat u až 24 hodin, po instalaci aktualizací). V další části jsou podrobně popsané kroky pro instalaci aktualizace režimu údržby.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalace aktualizace 5 jako oprava hotfix

Verze softwaru, které je možné upgradovat pomocí metody opravy hotfix jsou:

* Aktualizace 0.1, 0.2 a 0.3
* Aktualizace 1, 1.1, 1.2
* Update 2, 2.1 nebo 2.2
* Aktualizací Update 3, 3.1
* Update 4

> [!NOTE] 
> Doporučenou metodou pro instalaci aktualizace Update 5 je prostřednictvím portálu Azure portal, při pokusu o aktualizaci z aktualizace Update 3 a novější verze. Při aktualizaci zařízení se systémem verze starší než Update 3 se pomocí tohoto postupu. Tento postup můžete použít také, pokud selže kontrola brány při pokusu o instalaci aktualizace na webu Azure portal. Kontrola neúspěšná, když máte bránu přiřazená 0 síťové rozhraní než DATA a na zařízení běží software verze starší než Update 1.

Metoda oprav hotfix zahrnuje následující tři kroky:

1. Stažení opravy hotfix z katalogu služby Microsoft Update.
2. Instalace a ověření oprav hotfix běžného režimu.
3. Instalace a ověření oprav hotfix režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro vaše zařízení

Je nutné stáhnout a nainstalovat následující opravy hotfix předepsané pořadí a navrhované složky:

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizace softwaru<br> Stáhněte si oba _HcsSoftwareUpdate.exe_ a _CisMSDAgent.exe_ |Pravidelné <br></br>Bez přerušení |~ 25 minut |FirstOrderUpdate|

Pokud aktualizace z zařízení s verzí Update 4, stačí nainstalovat kumulativní aktualizace operačního systému jako aktualizace druhého řádu.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Balíček kumulativních aktualizací operačního systému <br> Stáhněte si verzi systému Windows Server 2012 R2 |Pravidelné <br></br>Bez přerušení |- |SecondOrderUpdate|

Pokud instalace ze zařízení s aktualizací Update 3 nebo starší, nainstalujte následující kromě kumulativní aktualizace.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Adaptér LSI ovladače a firmware aktualizace <br> Aktualizace firmwaru USM (verze 3.38) |Pravidelné <br></br>Bez přerušení |~ 3 hodiny <br> (včetně 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Balíček aktualizace zabezpečení operačního systému <br> Stáhněte si verzi systému Windows Server 2012 R2 |Pravidelné <br></br>Bez přerušení |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Balíček aktualizací operačního systému <br> Stáhněte si verzi systému Windows Server 2012 R2 |Pravidelné <br></br>Bez přerušení |- |SecondOrderUpdate|


Budete také muset nainstalovat aktualizace firmwaru disku nad všechny aktualizace uvedené v předchozích tabulkách. Můžete ověřit, jestli potřebujete aktualizace firmwaru disku spuštěním `Get-HcsFirmwareVersion` rutiny. Pokud používáte tyto verze firmwaru: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, potom není potřeba instalovat tyto aktualizace.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace | Instalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmwaru disku |Údržba <br></br>Ničivé |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Pokud aktualizace Update 4, blíží k celkové instalaci 4 hodiny.
> * Než použijete tento postup můžete použít aktualizaci, ujistěte se, že oba kontrolery zařízení jsou online a všechny hardwarové komponenty jsou v pořádku.

Proveďte následující kroky ke stažení a instalaci opravy hotfix.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Další postup
Další informace o [Update 5 release](storsimple-update5-release-notes.md).

