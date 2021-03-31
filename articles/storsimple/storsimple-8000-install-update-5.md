---
title: Instalace aktualizace 5 na zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak nainstalovat aktualizaci StorSimple 8000 Series Update 5 na zařízení StorSimple 8000 series.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: bbac6eade634ffcfdc47ae3d22b32e0bd429b7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85513172"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalace aktualizace Update 5 na zařízení StorSimple

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak nainstalovat Update 5 na zařízení StorSimple, na kterém běží starší verze softwaru, prostřednictvím Azure Portal a pomocí metody hotfix. Metoda hotfix se používá při pokusu o instalaci aktualizace Update 5 na zařízení, na kterém běží předběžná aktualizace 3 verze. Metoda hotfix se používá také v případě, že je brána nakonfigurovaná na jiném síťovém rozhraní než DATA 0 zařízení StorSimple a pokoušíte se aktualizovat z verze softwaru před aktualizací 1.

Aktualizace 5 zahrnuje software zařízení, ovladače Storport a Spaceport, aktualizace zabezpečení operačního systému a aktualizace operačního systému a aktualizace firmwaru disku.  Software zařízení, Spaceport, ovladače Storport, zabezpečení a další aktualizace operačního systému jsou nerušivé aktualizace. Nerušitelné nebo pravidelné aktualizace lze použít prostřednictvím Azure Portal nebo prostřednictvím metody opravy hotfix. Aktualizace firmwaru disku jsou rušivé aktualizace a používají se v případě, že je zařízení v režimu údržby prostřednictvím metody opravy hotfix pomocí rozhraní Windows PowerShell daného zařízení.

> [!IMPORTANT]
> * Aktualizace 5 je povinná aktualizace a měla by být nainstalovaná hned. Další informace najdete v [poznámkách k verzi Update 5](storsimple-update5-release-notes.md).
> * Sada ručních a automatických předběžných kontrol se provádí před instalací k určení stavu zařízení z hlediska stavu hardwaru a připojení k síti. Tyto předběžné kontroly se provádějí jenom v případě, že se aktualizace použijí z Azure Portal.
> * Důrazně doporučujeme, abyste při aktualizaci zařízení s verzemi staršími než Update 3 nainstalovali aktualizace pomocí metody hotfix. Pokud narazíte na nějaké problémy, [Zaprotokolujte lístek podpory](storsimple-8000-contact-microsoft-support.md).
> * Doporučujeme nainstalovat software a další běžné aktualizace prostřednictvím Azure Portal. Měli byste přejít jenom na rozhraní Windows PowerShellu zařízení (Pokud chcete nainstalovat aktualizace), pokud se na portálu nepovede ověření brány před aktualizací. V závislosti na verzi, ze které aktualizujete, můžou aktualizace nainstalovat 4 hodiny (nebo novější). Aktualizace režimu údržby musí být nainstalovány přes rozhraní Windows PowerShell zařízení. V případě aktualizací režimu údržby jsou aktualizace rušivé, což vede k tomu, že je pro vaše zařízení čas mimo provoz.
> * Pokud se StorSimple nepovinná Snapshot Manager, ujistěte se, že jste upgradovali Snapshot Manager verzi na aktualizaci 5 před aktualizací zařízení.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Nainstalujte aktualizaci 5 prostřednictvím Azure Portal
Provedením následujících kroků aktualizujte zařízení na [aktualizaci 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft z tohoto zařízení vyžádá další diagnostické informace. V důsledku toho je v případě, že náš provozní tým identifikuje zařízení, u kterých dochází k problémům, jsme lépe zakázali shromažďovat informace ze zařízení a diagnostikovat problémy.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ověřte, že na zařízení běží **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. **Datum poslední aktualizace** by mělo být změněno.

Teď uvidíte, že jsou dostupné aktualizace režimu údržby (Tato zpráva se může dál zobrazovat po dobu až 24 hodin po instalaci aktualizací). Postup instalace aktualizace režimu údržby jsou podrobně popsány v následující části.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalace aktualizace Update 5 jako opravy hotfix

Verze softwaru, které lze upgradovat pomocí metody hotfix, jsou:

* Aktualizace 0,1, 0,2, 0,3
* Aktualizace 1, 1,1, 1,2
* Aktualizace 2, 2,1, 2,2
* Aktualizace 3, 3,1
* Aktualizace 4

> [!NOTE] 
> Doporučená metoda pro instalaci aktualizace Update 5 je prostřednictvím Azure Portal při pokusu o aktualizaci z verze Update 3 a novější. Při aktualizaci zařízení s verzemi staršími než aktualizace 3 použijte tento postup. Tento postup můžete použít také v případě, že při pokusu o instalaci aktualizací prostřednictvím Azure Portal dojde k selhání kontroly brány. Tato chyba se nezdařila, pokud máte bránu přiřazenou k síťovému rozhraní, které nepoužívá DATA 0 a vaše zařízení používá verzi softwaru starší než Update 1.

Metoda opravy hotfix zahrnuje následující tři kroky:

1. Stáhněte si opravy hotfix z katalogu Microsoft Update.
2. Nainstalujte a ověřte opravy hotfix v normálním režimu.
3. Nainstalujte a ověřte opravu hotfix v režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro vaše zařízení

Je nutné stáhnout a nainstalovat následující opravy hotfix v předepsaném pořadí a v navrhovaných složkách:

| Objednávka | KB | Popis | Typ aktualizace | Čas instalace |Nainstalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Aktualizace softwaru<br> Stáhnout _HcsSoftwareUpdate.exe_ i _CisMSDAgent.exe_ |Pravidelný <br></br>Bez přerušení |~ 25 minut |FirstOrderUpdate|

Pokud aktualizujete ze zařízení se systémem Update 4, budete muset kumulativní aktualizace operačního systému instalovat jenom jako aktualizace pro druhé objednávky.

| Objednávka | KB | Popis | Typ aktualizace | Čas instalace |Nainstalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 2a. |KB4025336 |Balíček kumulativních aktualizací operačního systému <br> Stáhnout verzi Windows Serveru 2012 R2 |Pravidelný <br></br>Bez přerušení |- |SecondOrderUpdate|

Při instalaci ze zařízení se systémem Update 3 nebo starší verze nainstalujte kromě kumulativních aktualizací následující:

| Objednávka | KB | Popis | Typ aktualizace | Čas instalace |Nainstalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 2b. |KB4011841 <br> KB4011842 |Ovladače LSI a aktualizace firmwaru <br> Aktualizace firmwaru USM (verze 3,38) |Pravidelný <br></br>Bez přerušení |~ 3 hodiny <br> (zahrnuje 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Balíček aktualizace zabezpečení operačního systému <br> Stáhnout verzi Windows Serveru 2012 R2 |Pravidelný <br></br>Bez přerušení |- |SecondOrderUpdate|
| dvojrozměrné. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Balíček aktualizací operačního systému <br> Stáhnout verzi Windows Serveru 2012 R2 |Pravidelný <br></br>Bez přerušení |- |SecondOrderUpdate|


Je také možné, že budete muset nainstalovat aktualizace firmwaru disku nad všemi aktualizacemi, které jsou uvedeny v předchozích tabulkách. Spuštěním rutiny můžete ověřit, zda potřebujete aktualizace firmwaru disku `Get-HcsFirmwareVersion` . Pokud používáte tyto verze firmwaru: `XMGJ` , `XGEG` , `KZ50` , `F6C2` , `VR08` , `N003` , `0107` , pak tyto aktualizace nemusíte instalovat.

| Objednávka | KB | Popis | Typ aktualizace | Čas instalace | Nainstalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware disku |Údržba <br></br>Rušivé |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Při aktualizaci z aktualizace Update 4 je celková doba instalace blízko 4 hodiny.
> * Než použijete tento postup k instalaci aktualizace, ujistěte se, že jsou oba řadiče zařízení online a že všechny hardwarové komponenty jsou v pořádku.

K stažení a instalaci oprav hotfix proveďte následující kroky.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [verzi Update 5](storsimple-update5-release-notes.md).

