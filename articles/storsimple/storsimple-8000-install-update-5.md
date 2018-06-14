---
title: Nainstalujte na zařízení řady StorSimple 8000 Update 5 | Microsoft Docs
description: Popisuje postup instalace zařízení StorSimple 8000 řady aktualizací 5 na vašem zařízení řady StorSimple 8000.
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
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2018
ms.locfileid: "28108716"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalace aktualizací 5 zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak nainstalovat aktualizace 5 na zařízení StorSimple se starší verzí softwaru prostřednictvím portálu Azure a pomocí metody oprav hotfix. Metoda opravy hotfix se používá, když se pokoušíte nainstalovat aktualizace 5 na zařízení s verzemi před aktualizací 3. Metoda opravy hotfix se používá také v případě brána je nakonfigurovaná na síťovém rozhraní než DATA 0 zařízení StorSimple a se pokoušíte aktualizovat z verze 1 před aktualizací softwaru.

Aktualizace 5 zahrnuje zařízení software, Storport a Spaceport, aktualizacemi zabezpečení operačního systému a aktualizace operačního systému a aktualizace firmwaru disku.  Software zařízení, Spaceport, Storport, zabezpečení a jiné aktualizace operačního systému jsou omezovaly aktualizace. Prostřednictvím portálu Azure nebo prostřednictvím metody opravu hotfix lze použít omezovaly nebo pravidelné aktualizace. Aktualizace firmwaru disku rušivý aktualizace a se použijí, když je zařízení v režimu údržby prostřednictvím oprav hotfix metodu, pomocí rozhraní Windows PowerShell zařízení.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a by měly být nainstalovány okamžitě. Další informace najdete v tématu [poznámky k verzi Update 5](storsimple-update5-release-notes.md).
> * Sadu ruční a Automatická předběžné kontroly se provádějí před instalací, který měl zjistit stav zařízení z hlediska hardwaru stavu a připojení k síti. Tyto předběžné kontroly se provádí pouze v případě, že použít aktualizace z portálu Azure.
> * Důrazně doporučujeme při aktualizaci zařízení se systémem verze starší než Update 3 se nainstalovat aktualizace pomocí metody oprav hotfix. Pokud narazíte na potíže, [protokolu lístek podpory](storsimple-8000-contact-microsoft-support.md).
> * Doporučujeme nainstalovat software a další pravidelné aktualizace prostřednictvím portálu Azure. Má jenom přejděte na rozhraní prostředí Windows PowerShell na zařízení (instalovat aktualizace) Pokud selže kontrola před aktualizací brány na portálu. V závislosti na verzi, ze kterého je aktualizován, instalace aktualizací může trvat 4 hodiny (nebo vyšší) k instalaci. Aktualizace režimu údržby musí být nainstalován prostřednictvím rozhraní Windows PowerShell zařízení. Jako rušivý aktualizace jsou aktualizace režimu údržby, tyto vést k výpadkům pro vaše zařízení.
> * Pokud běží volitelné Snapshot Manager zařízení StorSimple, zajistěte, aby upgradu vaší verzí Snapshot Manager aktualizace 5 před aktualizací zařízení.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalace aktualizací 5 prostřednictvím portálu Azure
Proveďte následující kroky k aktualizaci zařízení [aktualizace 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft vrátí jiné diagnostické informace ze zařízení. Výsledkem je když náš tým operations identifikuje zařízení, která došlo k potížím, jsme jsou lépe vybaveny shromažďovat informace ze zařízení a diagnostikovat problémy.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ověřte, zda je spuštěna vaše zařízení **StorSimple 8000 řady aktualizací 5 (6.3.9600.17845)**. **Datum poslední aktualizace** by měl být upraven.

Nyní uvidíte, že jsou k dispozici aktualizace režim údržby (Tato zpráva může nadále zobrazovat až 24 hodin po instalaci aktualizace). V následující části jsou podrobně popsané kroky pro instalaci aktualizace režimu údržby.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalace aktualizací 5 jako oprava hotfix

Verze softwaru, které lze upgradovat pomocí metody opravy hotfix jsou:

* Aktualizovat 0.1, 0.2 a 0.3
* Aktualizací 1, 1.1 a 1.2
* Aktualizace 2, 2.1, 2.2
* Aktualizace 3, verze 3.1
* Aktualizace 4

> [!NOTE] 
> Doporučujeme nainstalovat aktualizace 5 je prostřednictvím portálu Azure při pokusu o aktualizaci z Update 3 a vyšší verze. Při aktualizaci zařízení se systémem verze starší než Update 3 se pomocí tohoto postupu. Tento postup můžete také použít, pokud selže kontrola brány při pokusu o instalaci aktualizace prostřednictvím portálu Azure. Kontrola selže, pokud máte bránu přiřazené 0 síťové rozhraní bez dat a zařízení používá verzi softwaru starší než Update 1.

Metoda opravy hotfix zahrnuje následující tři kroky:

1. Stažení opravy hotfix z katalogu služby Microsoft Update.
2. Instalace a ověřte regulární režimu opravy hotfix.
3. Instalace a ověřte opravu hotfix režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro zařízení

Musíte stáhnout a nainstalovat následující opravy hotfix v předepsaných pořadí a navrhované složky:

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalace ve složce|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizace softwaru<br> Stáhněte si oba _HcsSoftwareUpdate.exe_ a _CisMSDAgent.exe_ |Regulární <br></br>Non-disruptive |~ 25 minut |FirstOrderUpdate|

Pokud aktualizace ze zařízení se systémem aktualizace 4, stačí nainstalovat kumulativní aktualizace operačního systému jako druhý pořadí aktualizací.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalace ve složce|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Balíčku kumulativní aktualizace operačního systému <br> Stáhnout verzi Windows Server 2012 R2 |Regulární <br></br>Non-disruptive |- |SecondOrderUpdate|

Pokud instalaci ze zařízení se systémem Update 3 nebo starší, nainstalujte následující kromě kumulativní aktualizace.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace |Instalace ve složce|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI ovladače a firmware aktualizace <br> Aktualizace firmwaru Seznam USM (verze 3.38) |Regulární <br></br>Non-disruptive |~ 3 hodiny <br> (včetně 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Balíček aktualizace zabezpečení operačního systému <br> Stáhnout verzi Windows Server 2012 R2 |Regulární <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Balíček aktualizace operačního systému <br> Stáhnout verzi Windows Server 2012 R2 |Regulární <br></br>Non-disruptive |- |SecondOrderUpdate|


Můžete také nainstalovat aktualizace firmwaru disku nad všechny aktualizace, které jsou uvedené v předchozí tabulky. Můžete ověřit, zda je nutné aktualizace firmwaru disku spuštěním `Get-HcsFirmwareVersion` rutiny. Pokud používáte tyto verze firmwaru: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, pak nemusíte tyto aktualizace nainstalujete.

| Pořadí | kB | Popis | Typ aktualizace | Čas instalace | Instalace ve složce|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware disku |Údržba <br></br>Rušivý |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Pokud aktualizaci z aktualizace 4, je čas celkový instalace blízké 4 hodiny.
> * Před použitím tohoto postupu k použití aktualizace, ujistěte se, že jak řadiče zařízení jsou online a jsou hardwarové součásti v pořádku.

Proveďte následující kroky ke stažení a instalaci opravy hotfix.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Další postup
Další informace o [verze aktualizací 5](storsimple-update5-release-notes.md).

