---
title: Instalace aktualizace 4 na zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak nainstalovat StorSimple 8000 Series Update 4 na zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: ed6f9d58c5c54c88acf8e3a0e7fda7d2d65b8637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514322"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalace aktualizace Update 4 na zařízení StorSimple

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak nainstalovat Update 4 na zařízení StorSimple, na kterém běží starší verze softwaru, prostřednictvím Azure Portal a pomocí metody hotfix. Metoda hotfix se používá v případě, že je brána nakonfigurovaná na jiném síťovém rozhraní než DATA 0 zařízení StorSimple a pokoušíte se aktualizovat z verze softwaru před aktualizací 1.

Aktualizace 4 zahrnuje software zařízení, firmware USM, adaptér LSI a firmware, ovladače Storport a Spaceport, aktualizace zabezpečení operačního systému a hostitele dalších aktualizací operačního systému.  Software zařízení, firmware USM, Spaceport, Storport a další aktualizace operačního systému jsou nerušivé aktualizace. Nerušitelné nebo pravidelné aktualizace lze použít prostřednictvím Azure Portal nebo prostřednictvím metody opravy hotfix. Aktualizace firmwaru disku jsou rušivé aktualizace a dají se použít jenom prostřednictvím metody hotfix pomocí rozhraní Windows PowerShell zařízení.

> [!IMPORTANT]
> * Sada ručních a automatických předběžných kontrol se provádí před instalací k určení stavu zařízení z hlediska stavu hardwaru a připojení k síti. Tyto předběžné kontroly se provádějí jenom v případě, že se aktualizace použijí z Azure Portal.
> * Doporučujeme nainstalovat software a další běžné aktualizace prostřednictvím Azure Portal. Měli byste přejít jenom na rozhraní Windows PowerShellu zařízení (Pokud chcete nainstalovat aktualizace), pokud se na portálu nepovede ověření brány před aktualizací. V závislosti na verzi, ze které aktualizujete, můžou aktualizace nainstalovat 4 hodiny (nebo novější). Aktualizace režimu údržby se musí nainstalovat taky přes rozhraní Windows PowerShell zařízení. V případě aktualizací režimu údržby jsou aktualizace rušivé, což způsobí, že se vaše zařízení bude časem zpomalit.
> * Pokud se StorSimple nepovinná Snapshot Manager, ujistěte se, že jste před aktualizací zařízení upgradovali verzi Snapshot Manager na aktualizaci 4.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Nainstalujte aktualizaci 4 prostřednictvím Azure Portal
Provedením následujících kroků aktualizujte zařízení na [aktualizaci 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft z tohoto zařízení vyžádá další diagnostické informace. V důsledku toho je v případě, že náš provozní tým identifikuje zařízení, u kterých dochází k problémům, jsme lépe zakázali shromažďovat informace ze zařízení a diagnostikovat problémy. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Ověřte, že na zařízení běží **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. **Datum poslední aktualizace** by mělo být také změněno.

* Teď uvidíte, že jsou dostupné aktualizace režimu údržby (Tato zpráva se může dál zobrazovat po dobu až 24 hodin po instalaci aktualizací). Aktualizace režimu údržby jsou rušivé aktualizace, které vedou k výpadkům zařízení a dají se použít jenom přes rozhraní Windows PowerShell vašeho zařízení.

* Stáhněte aktualizace režimu údržby pomocí kroků uvedených v části [a Stáhněte si opravy hotfix](#to-download-hotfixes) , které budou hledat a stahovat KB4011837, které instalují aktualizace firmwaru disku (ostatní aktualizace již by nyní měly být nainstalovány). Postupujte podle kroků uvedených v části [instalace a ověření oprav hotfix v režimu údržby](#to-install-and-verify-maintenance-mode-hotfixes) a nainstalujte aktualizace režimu údržby.

## <a name="install-update-4-as-a-hotfix"></a>Instalace aktualizace Update 4 jako opravy hotfix
Doporučená metoda pro instalaci aktualizace Update 4 je prostřednictvím Azure Portal.

Tento postup použijte v případě, že při pokusu o instalaci aktualizací prostřednictvím Azure Portal dojde k selhání kontroly brány. Tato operace se nezdařila, protože máte bránu přiřazenou k síťovému rozhraní, které nepoužívá DATA 0 a vaše zařízení používá verzi softwaru před aktualizací 1.

Verze softwaru, které lze upgradovat pomocí metody hotfix, jsou:

* Aktualizace 0,1, 0,2, 0,3
* Aktualizace 1, 1,1, 1,2
* Aktualizace 2, 2,1, 2,2
* Aktualizace 3, 3,1


Metoda opravy hotfix zahrnuje následující tři kroky:

1. Stáhněte si opravy hotfix z katalogu Microsoft Update.
2. Nainstalujte a ověřte opravy hotfix v normálním režimu.
3. Nainstalujte a ověřte opravu hotfix v režimu údržby.

#### <a name="download-updates-for-your-device"></a>Stažení aktualizací pro vaše zařízení

Je nutné stáhnout a nainstalovat následující opravy hotfix v předepsaném pořadí a v navrhovaných složkách:

| Objednávka | KB | Popis | Typ aktualizace | Čas instalace |Nainstalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Aktualizace softwaru |Pravidelný <br></br>Bez přerušení |~ 25 minut |FirstOrderUpdate|
| 2a. |KB4011841 <br> KB4011842 |Ovladače LSI a aktualizace firmwaru <br> Aktualizace firmwaru USM (verze 3,38) |Pravidelný <br></br>Bez přerušení |~ 3 hodiny <br> (zahrnuje 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2b. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Balíček aktualizace zabezpečení operačního systému <br> Stáhnout Windows Server 2012 R2 |Pravidelný <br></br>Bez přerušení |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Balíček aktualizací operačního systému <br> Stáhnout Windows Server 2012 R2 |Pravidelný <br></br>Bez přerušení |- |SecondOrderUpdate|

Je také možné, že budete muset nainstalovat aktualizace firmwaru disku nad všemi aktualizacemi, které jsou uvedeny v předchozích tabulkách. Spuštěním rutiny můžete ověřit, zda potřebujete aktualizace firmwaru disku `Get-HcsFirmwareVersion` . Pokud používáte tyto verze firmwaru: `XMGJ` , `XGEG` , `KZ50` , `F6C2` , `VR08` , `N002` , `0106` , pak tyto aktualizace nemusíte instalovat.

| Objednávka | KB | Popis | Typ aktualizace | Čas instalace | Nainstalovat do složky|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmware disku |Údržba <br></br>Rušivé |~ 30 minut | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Tento postup je třeba provést pouze jednou, aby bylo možné použít aktualizaci 4. Pomocí Azure Portal můžete použít následné aktualizace.
> * Při aktualizaci z aktualizace 3 nebo 3,1 je celková doba instalace blízko 4 hodiny.
> * Než použijete tento postup k instalaci aktualizace, ujistěte se, že jsou oba řadiče zařízení online a že všechny hardwarové komponenty jsou v pořádku.

K stažení a instalaci oprav hotfix proveďte následující kroky.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [verzi Update 4](storsimple-update4-release-notes.md).

