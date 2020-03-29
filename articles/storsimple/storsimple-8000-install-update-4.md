---
title: Instalace aktualizace 4 na zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak nainstalovat aktualizaci StorSimple 8000 Series Update 4 do zařízení řady StorSimple 8000.
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
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60860378"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalace aktualizace 4 do zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak nainstalovat aktualizaci 4 na zařízení StorSimple se starší verzí softwaru prostřednictvím portálu Azure a pomocí metody opravy hotfix. Metoda opravy hotfix se používá, pokud je brána nakonfigurována v jiném síťovém rozhraní než data 0 zařízení StorSimple a pokoušíte se aktualizovat z verze softwaru před aktualizací 1.

Aktualizace 4 obsahuje software zařízení, firmware USM, ovladač a firmware LSI, Storport a Spaceport, aktualizace zabezpečení operačního systému a řadu dalších aktualizací operačního systému.  Software zařízení, firmware USM, spaceport, Storport a další aktualizace operačního systému jsou aktualizace, které nejsou rušivé. Nerušivé nebo pravidelné aktualizace lze použít prostřednictvím portálu Azure nebo prostřednictvím metody opravy hotfix. Aktualizace firmwaru disku jsou rušivé aktualizace a lze je použít pouze pomocí metody opravy hotfix pomocí rozhraní prostředí Windows PowerShell zařízení.

> [!IMPORTANT]
> * Sada ručních a automatických předběžných kontrol se provádí před instalací, aby se zjistil stav zařízení z hlediska stavu hardwaru a připojení k síti. Tyto předběžné kontroly se provádějí jenom v případě, že použijete aktualizace z portálu Azure.
> * Doporučujeme nainstalovat software a další pravidelné aktualizace prostřednictvím portálu Azure. Měli byste přejít pouze na rozhraní prostředí Windows PowerShell zařízení (k instalaci aktualizací) pouze v případě, že kontrola brány před aktualizací se nezdaří na portálu. V závislosti na verzi, ze které aktualizujete, může instalace aktualizací trvat 4 hodiny (nebo více). Aktualizace režimu údržby musí být také nainstalovány prostřednictvím rozhraní prostředí Windows PowerShell zařízení. Vzhledem k tomu, že aktualizace režimu údržby jsou rušivými aktualizacemi, budou mít za následek prostoje vašeho zařízení.
> * Pokud používáte volitelný Správce snímků StorSimple, ujistěte se, že jste před aktualizací zařízení upgradovali verzi správce snímků na aktualizaci 4.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalace aktualizace 4 přes portál Azure
Chcete-li aktualizovat zařízení na [aktualizaci 4](storsimple-update4-release-notes.md), proveďte následující kroky.

> [!NOTE]
> Společnost Microsoft získává další diagnostické informace ze zařízení. V důsledku toho, když náš provozní tým identifikuje zařízení, která mají problémy, jsme lépe vybaveni ke shromažďování informací ze zařízení a diagnostice problémů. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Ověřte, zda je v zařízení spuštěna **aktualizace řady StorSimple 8000 4 (6.3.9600.17820).** **Datum poslední aktualizace** by mělo být také změněno.

* Nyní uvidíte, že aktualizace režimu údržby jsou k dispozici (tato zpráva může nadále zobrazovat po dobu až 24 hodin po instalaci aktualizací). Aktualizace režimu údržby jsou rušivé aktualizace, které mají za následek prostoje zařízení a lze je použít pouze prostřednictvím rozhraní prostředí Windows PowerShell vašeho zařízení.

* Stáhněte si aktualizace režimu údržby pomocí kroků uvedených v písmenu [a) a stáhněte si opravy hotfix](#to-download-hotfixes) pro vyhledání a stažení kb4011837, která nainstaluje aktualizace firmwaru disku (ostatní aktualizace by již měly být nainstalovány). Chcete-li nainstalovat aktualizace režimu údržby, postupujte podle kroků uvedených v [části Instalace a ověření oprav hotfix v režimu údržby.](#to-install-and-verify-maintenance-mode-hotfixes)

## <a name="install-update-4-as-a-hotfix"></a>Instalace aktualizace 4 jako opravy hotfix
Doporučená metoda instalace aktualizace 4 je přes portál Azure.

Tento postup použijte, pokud při pokusu o instalaci aktualizací přes portál Azure se vám nepodaří zkontrolovat bránu. Kontrola se nezdaří, protože máte bránu přiřazenou k síťovému rozhraní, které není datová no DATA 0, a zařízení používá verzi softwaru před aktualizací 1.

Verze softwaru, které lze inovovat pomocí metody opravy hotfix, jsou následující:

* Aktualizace 0.1, 0.2, 0.3
* Aktualizace 1, 1.1, 1.2
* Aktualizace 2, 2.1, 2.2
* Aktualizace 3, 3.1


Metoda opravy hotfix zahrnuje následující tři kroky:

1. Stáhněte si opravy hotfix z katalogu Microsoft Update.
2. Nainstalujte a ověřte opravy hotfix v běžném režimu.
3. Nainstalujte a ověřte opravu hotfix v režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro vaše zařízení

Je nutné stáhnout a nainstalovat následující opravy hotfix v předepsaném pořadí a v navržených složkách:

| Objednání | KB | Popis | Typ aktualizace | Čas instalace |Instalace do složky|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Aktualizace softwaru |Regular <br></br>Nerušivé |~ 25 minut |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Aktualizace ovladačů a firmwaru LSI <br> Aktualizace firmwaru USM (verze 3.38) |Regular <br></br>Nerušivé |~ 3 hodiny <br> (zahrnuje 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Balíček aktualizací zabezpečení operačního systému <br> Stažení systému Windows Server 2012 R2 |Regular <br></br>Nerušivé |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Balíček aktualizací operačního systému <br> Stažení systému Windows Server 2012 R2 |Regular <br></br>Nerušivé |- |SecondOrderUpdate|

Kromě všech aktualizací uvedených v předchozích tabulkách může být také nutné nainstalovat aktualizace firmwaru disku. Spuštěním rutiny můžete ověřit, zda `Get-HcsFirmwareVersion` potřebujete aktualizace firmwaru disku. Pokud používáte tyto verze `XMGJ`firmwaru: `VR08` `N002`, `0106` `XGEG`, `KZ50` `F6C2`, , , , , , není nutné tyto aktualizace instalovat.

| Objednání | KB | Popis | Typ aktualizace | Čas instalace | Instalace do složky|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmware disku |Údržba <br></br>Rušivé |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Tento postup je třeba provést pouze jednou použít aktualizaci 4. Na portálu Azure můžete použít následné aktualizace.
> * Pokud se aktualizujete z aktualizace 3 nebo 3.1, celková doba instalace se blíží 4 hodinám.
> * Před použitím tohoto postupu k instalaci aktualizace se ujistěte, že jsou řadiče zařízení online a všechny hardwarové součásti jsou v pořádku.

Chcete-li stáhnout a nainstalovat opravy hotfix, proveďte následující kroky.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [verzi aktualizace 4](storsimple-update4-release-notes.md).

