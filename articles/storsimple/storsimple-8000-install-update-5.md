---
title: Instalace aktualizace 5 na zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak nainstalovat aktualizaci StorSimple 8000 Series Update 5 do zařízení řady StorSimple 8000.
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
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267882"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalace aktualizace 5 do zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz vysvětluje, jak nainstalovat aktualizaci 5 na zařízení StorSimple se starší verzí softwaru prostřednictvím portálu Azure a pomocí metody opravy hotfix. Metoda opravy hotfix se používá při pokusu o instalaci aktualizace 5 do zařízení se systémem před aktualizací 3. Metoda opravy hotfix se používá také v případě, že je brána nakonfigurována v jiném síťovém rozhraní než data 0 zařízení StorSimple a pokoušíte se aktualizovat z verze softwaru před aktualizací 1.

Aktualizace 5 zahrnuje software zařízení, Storport a Spaceport, aktualizace zabezpečení operačního systému a aktualizace operačního systému a aktualizace firmwaru disku.  Software zařízení, spaceport, Storport, zabezpečení a další aktualizace operačního systému jsou nerušivé aktualizace. Nerušivé nebo pravidelné aktualizace lze použít prostřednictvím portálu Azure nebo prostřednictvím metody opravy hotfix. Aktualizace firmwaru disku jsou rušivé aktualizace a jsou použity, když je zařízení v režimu údržby pomocí metody opravy hotfix pomocí rozhraní prostředí Windows PowerShell zařízení.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a měla by být nainstalována okamžitě. Další informace naleznete v [tématu Aktualizace 5 poznámky k verzi](storsimple-update5-release-notes.md).
> * Sada ručních a automatických předběžných kontrol se provádí před instalací, aby se zjistil stav zařízení z hlediska stavu hardwaru a připojení k síti. Tyto předběžné kontroly se provádějí jenom v případě, že použijete aktualizace z portálu Azure.
> * Důrazně doporučujeme, abyste při aktualizaci zařízení s verzemi před aktualizací 3 nainstalovali aktualizace pomocí metody opravy hotfix. Pokud narazíte na nějaké problémy, [protokolovat lístek podpory](storsimple-8000-contact-microsoft-support.md).
> * Doporučujeme nainstalovat software a další pravidelné aktualizace prostřednictvím portálu Azure. Měli byste přejít pouze na rozhraní prostředí Windows PowerShell zařízení (k instalaci aktualizací) pouze v případě, že kontrola brány před aktualizací se nezdaří na portálu. V závislosti na verzi, ze které aktualizujete, může instalace aktualizací trvat 4 hodiny (nebo více). Aktualizace režimu údržby musí být nainstalovány prostřednictvím rozhraní prostředí Windows PowerShell zařízení. Vzhledem k tomu, že aktualizace režimu údržby jsou rušivými aktualizacemi, výsledkem jsou prostoje vašeho zařízení.
> * Pokud spuštění volitelného Správce snímků StorSimple, ujistěte se, že jste upgradovali verzi Správce snímků na aktualizaci 5 před aktualizací zařízení.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalace aktualizace 5 přes portál Azure
Chcete-li aktualizovat zařízení na [aktualizaci 5](storsimple-update5-release-notes.md), proveďte následující kroky.

> [!NOTE]
> Společnost Microsoft získává další diagnostické informace ze zařízení. V důsledku toho, když náš provozní tým identifikuje zařízení, která mají problémy, jsme lépe vybaveni ke shromažďování informací ze zařízení a diagnostice problémů.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ověřte, zda je v zařízení spuštěna **aktualizace řady StorSimple 8000 5 (6.3.9600.17845).** **Datum poslední aktualizace** by mělo být změněno.

Nyní uvidíte, že aktualizace režimu údržby jsou k dispozici (tato zpráva může nadále zobrazovat po dobu až 24 hodin po instalaci aktualizací). Kroky k instalaci aktualizace režimu údržby jsou podrobně popsány v další části.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalace aktualizace 5 jako opravy hotfix

Verze softwaru, které lze inovovat pomocí metody opravy hotfix, jsou následující:

* Aktualizace 0.1, 0.2, 0.3
* Aktualizace 1, 1.1, 1.2
* Aktualizace 2, 2.1, 2.2
* Aktualizace 3, 3.1
* Aktualizace 4

> [!NOTE] 
> Doporučená metoda instalace aktualizace 5 je přes portál Azure při pokusu o aktualizaci z aktualizace 3 a novější verze. Při aktualizaci zařízení s verzemi před aktualizací 3 použijte tento postup. Tento postup můžete také použít, pokud při pokusu o instalaci aktualizací prostřednictvím portálu Azure neprojde kontrola brány. Kontrola se nezdaří, pokud máte bránu přiřazenou k síťovému rozhraní, které není datová, a v zařízení je spuštěna verze softwaru starší než aktualizace 1.

Metoda opravy hotfix zahrnuje následující tři kroky:

1. Stáhněte si opravy hotfix z katalogu Microsoft Update.
2. Nainstalujte a ověřte opravy hotfix v běžném režimu.
3. Nainstalujte a ověřte opravu hotfix v režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro vaše zařízení

Je nutné stáhnout a nainstalovat následující opravy hotfix v předepsaném pořadí a v navržených složkách:

| Objednání | KB | Popis | Typ aktualizace | Čas instalace |Instalace do složky|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizace softwaru<br> Stáhnout _soubor HcsSoftwareUpdate.exe_ i _soubor CisMSDAgent.exe_ |Regular <br></br>Nerušivé |~ 25 minut |FirstOrderUpdate|

Pokud se aktualizujete ze zařízení se spuštěnou aktualizací 4, stačí nainstalovat kumulativní aktualizace operačního systému jako aktualizace druhého řádu.

| Objednání | KB | Popis | Typ aktualizace | Čas instalace |Instalace do složky|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Balíček kumulativních aktualizací operačního systému <br> Stažení verze systému Windows Server 2012 R2 |Regular <br></br>Nerušivé |- |SecondOrderUpdate|

Pokud instalujete ze zařízení se spuštěnou aktualizací 3 nebo starší, nainstalujte kromě kumulativních aktualizací následující.

| Objednání | KB | Popis | Typ aktualizace | Čas instalace |Instalace do složky|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Aktualizace ovladačů a firmwaru LSI <br> Aktualizace firmwaru USM (verze 3.38) |Regular <br></br>Nerušivé |~ 3 hodiny <br> (zahrnuje 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Balíček aktualizací zabezpečení operačního systému <br> Stažení verze systému Windows Server 2012 R2 |Regular <br></br>Nerušivé |- |SecondOrderUpdate|
| 2d. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Balíček aktualizací operačního systému <br> Stažení verze systému Windows Server 2012 R2 |Regular <br></br>Nerušivé |- |SecondOrderUpdate|


Kromě všech aktualizací uvedených v předchozích tabulkách může být také nutné nainstalovat aktualizace firmwaru disku. Spuštěním rutiny můžete ověřit, zda `Get-HcsFirmwareVersion` potřebujete aktualizace firmwaru disku. Pokud používáte tyto verze `XMGJ`firmwaru: `VR08` `N003`, `0107` `XGEG`, `KZ50` `F6C2`, , , , , , není nutné tyto aktualizace instalovat.

| Objednání | KB | Popis | Typ aktualizace | Čas instalace | Instalace do složky|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware disku |Údržba <br></br>Rušivé |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Pokud se aktualizujete z aktualizace 4, celková doba instalace se blíží 4 hodinám.
> * Před použitím tohoto postupu k instalaci aktualizace se ujistěte, že jsou řadiče zařízení online a všechny hardwarové součásti jsou v pořádku.

Chcete-li stáhnout a nainstalovat opravy hotfix, proveďte následující kroky.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [verzi aktualizace 5](storsimple-update5-release-notes.md).

