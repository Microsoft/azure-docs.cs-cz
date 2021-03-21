---
title: Zpráva k vydání verze s aktualizací StorSimple Virtual Array 0,6 | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací 0,6.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000908"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 0,6

## <a name="overview"></a>Přehled

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením StorSimple virtuálního pole pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,6 odpovídá **10.0.10293.0** verze softwaru.

> [!IMPORTANT]
> - Aktualizace jsou rušivé a restartují vaše zařízení. Pokud probíhá vstupně-výstupní operace, dojde k výpadku zařízení. Podrobné pokyny, jak použít aktualizaci, najdete v článku [instalace aktualizace 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Důrazně doporučujeme, abyste aktualizaci 0,6 nainstalovali hned, protože obsahuje důležité opravy zabezpečení.


## <a name="whats-new-in-the-update-06"></a>Co je nového v aktualizaci 0,6
Aktualizace 0,6 je kritická a měla by se nasadit hned. Tato aktualizace obsahuje následující opravy: 

- **Opravy zabezpečení systému Windows** – Tato verze obsahuje **důležité opravy zabezpečení systému Windows**. Další informace o problémech se zabezpečení a souvisejících opravách najdete v následujících aktualizacích zabezpečení:
    - [Pouze aktualizace kvality z prosince 2016 pro Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017 aktualizace kvality pouze zabezpečení pro Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9. května 2017 – KB4019213 (aktualizace pouze se zabezpečením)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Obnovení opravy** – v dřívějších verzích došlo k chybě, která by zabránila dokončení obnovení. Tato chyba byla opravena v této verzi.


## <a name="issues-fixed-in-the-update-06"></a>Problémy opravené v aktualizaci 0,6

Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| No. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zabezpečení| Tato verze obsahuje důležité aktualizace zabezpečení systému Windows. Tuto aktualizaci doporučujeme nainstalovat hned.|
| 2 |Obnovení| Během obnovení došlo ke konfliktu časování, která by zabránila dokončení úlohy obnovení. Oprava chyby řeší tuto podmínku časování.|


## <a name="known-issues-in-the-update-06"></a>Známé problémy v aktualizaci 0,6

Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje vydané verze zaznamenané z předchozích verzí.

| No. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořená ve verzi Preview nejde aktualizovat na podporovanou obecnou verzi dostupnosti. |U těchto virtuálních zařízení je potřeba převzít služby při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální zařízení StorSimple, nesmíte tento datový disk rozbalit ani zmenšit. Při pokusu o provedení dojde ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, může použití zásad skupiny negativně ovlivnit operaci zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory a že pro něj nejsou použité žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské rozhraní |Pokud jsou v aplikaci Internet Explorer povoleny rozšířené funkce zabezpečení (IE ESC), některé místní stránky webového uživatelského rozhraní, jako je například řešení potíží nebo údržba, nemusí správně fungovat. Tlačítka na těchto stránkách také nemusí fungovat. |Vypnout rozšířené funkce zabezpečení v Internet Exploreru. |
| **čl.** |Místní webové uživatelské rozhraní |Ve virtuálním počítači s technologií Hyper-V se síťová rozhraní ve webovém uživatelském rozhraní zobrazují jako rozhraní o 10 GB/s. |Toto chování je reflexe technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamykání rozsahu bajtů pro aplikace, které pracují se StorSimple vrstvenými svazky, se nepodporuje. Pokud je povolené zamykání rozsahu bajtů, nefunguje vrstvení StorSimple. |Doporučené míry zahrnují: <br></br>Vypněte uzamykání rozsahu bajtů v logice aplikace.<br></br>Vyberte, chcete-li umístit data pro tuto aplikaci na místně připnuté svazky na rozdíl od vrstveného svazku.<br></br>*Upozornění*: Pokud je povolené použití místně připojených svazků a uzamykání rozsahu bajtů, může být místně připojený svazek online, i když se obnovení nedokončí. V takových případech, pokud probíhá obnovení, je nutné počkat na dokončení obnovení. |
| **čl.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek zpomalení vrstvy. |Při práci s velkými soubory doporučujeme, aby byl největší soubor menší než 3% velikosti sdílené složky. |
| **8.** |Využitá kapacita pro sdílené složky |Pokud sdílená složka neobsahuje žádná data, může se zobrazit spotřeba sdílení. Tato spotřeba je způsobená tím, že využitá kapacita pro sdílené složky zahrnuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru můžete provést jenom se stejnou doménou, jakou má zdrojové zařízení. V této verzi není podporováno zotavení po havárii do cílového zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v [poznámkách k převzetí služeb při selhání a zotavení po havárii pro virtuální pole StorSimple](storsimple-virtual-array-failover-dr.md) . |
| **10pruhový.** |Azure PowerShell |Virtuální zařízení StorSimple se nedají spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by se měla provádět prostřednictvím Azure Portal a místního webového uživatelského rozhraní. |
| **odst.** |Změna hesla |Konzola zařízení Virtual Array podporuje pouze vstup ve formátu klávesnice en-US. | |
| **12,5.** |CHAP |Po vytvoření nelze odstranit přihlašovací údaje protokolu CHAP. Pokud navíc upravíte přihlašovací údaje protokolu CHAP, musíte svazky převést do režimu offline a pak je převést do režimu online, aby se změna projevila. |Tento problém je řešen v pozdější verzi. |
| **13,5.** |Server iSCSI |Zobrazené úložiště pro svazek iSCSI se může lišit v StorSimple Správce zařízení službě a hostiteli iSCSI. |Hostitel iSCSI má zobrazení systému souborů.<br></br>Zařízení vidí bloky přidělené v případě, že byl svazek v maximální velikosti. |
| **čtrnáct.** |Souborový server |Pokud k souboru ve složce je přidružen alternativní datový proud (ADS), nejsou tyto reklamy zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16bitovém.** |Souborový server |Soubory chráněné systémem Windows systém souborů EFS (Encrypting File System) (EFS) při zkopírování nebo uložení na souborový server virtuálních polí StorSimple mají za následek nepodporovanou konfiguraci.  | |
| **sedmnáct.** |Aktualizace |Pokud se při pokusu o instalaci opravy hotfix prostřednictvím místního uživatelského rozhraní zobrazí kód chyby: 2359302 (Hex 0x240006), znamená to, že oprava hotfix je už na vašem zařízení nainstalovaná.   | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizaci 0,6](storsimple-virtual-array-install-update-06.md) na virtuální pole StorSimple.

## <a name="references"></a>Reference
Hledáte starší poznámku k verzi? Přejít na:

* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,5](storsimple-virtual-array-update-05-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,4](storsimple-virtual-array-update-04-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,3](storsimple-ova-update-03-release-notes.md)
* [Zpráva k vydání verze pro StorSimple Virtual Array Update 0,1 a 0,2](storsimple-ova-update-01-release-notes.md)
* [Poznámky k verzi pro StorSimple Virtual Array General Availability]()