---
title: StorSimple Virtual Array aktualizace 0,6 poznámky k verzi | Dokumentace Microsoftu
description: Pro StorSimple Virtual Array spuštění aktualizace 0.6 popisuje důležité otevřených problémů a jejich řešení.
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
ms.openlocfilehash: e984531feced2d61332e4c399848c12cd245a34a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870702"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Virtual Array aktualizace 0,6 poznámky

## <a name="overview"></a>Přehled

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Aktualizace 0.6 odpovídá verzi softwaru **10.0.10293.0**.

> [!IMPORTANT]
> - Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek. Podrobné pokyny o tom, jak použít aktualizaci, přejděte na [instalace aktualizace 0.6](storsimple-virtual-array-install-update-06.md).
>
> - Důrazně doporučujeme instalace aktualizace 0.6 okamžitě, protože obsahuje opravy zabezpečení.


## <a name="whats-new-in-the-update-06"></a>Co je nového v aktualizace 0.6
Aktualizace 0.6 je důležitá aktualizace a okamžitě nasadit. Tato aktualizace obsahuje následující opravy: 

- **Opravy zabezpečení Windows** – tato verze má **řeší důležité zabezpečení Windows**. Projděte si následující aktualizace zabezpečení pro další informace o řešení problémů zabezpečení a přidružené opravy:
    - [Prosinec 2016 pouze kvality zabezpečení aktualizace pro Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Březen 2017 pouze kvality zabezpečení aktualizace pro Windows 8.1 a Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9. května 2017 – KB4019213 (jen pro zabezpečení aktualizace)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Obnovit oprava** – v dřívějších verzích, byla chybu, která by jinak znemožňovaly obnovení dokončit. Tato chyba byla opravena v této verzi.


## <a name="issues-fixed-in-the-update-06"></a>Chyby opravené v aktualizace 0.6

Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Zabezpečení| Tato verze obsahuje důležité aktualizace zabezpečení Windows. Doporučujeme instalaci této aktualizace okamžitě.|
| 2 |Obnovení| Při obnovení byl spor, který by brání v dokončení úlohy obnovení. Oprava chyby řeší tento konflikt časování.|


## <a name="known-issues-in-the-update-06"></a>Známé problémy v aktualizace 0.6

Následující tabulka obsahuje souhrn známé problémy pro StorSimple Virtual Array a problémy uvedené verze z předchozích verzí.

| Ne. | Funkce | Problém | Alternativní řešení a poznámky |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení, vytvořené ve verzi preview, nejde aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí být převzetí služeb při selhání pro všeobecně dostupné verze pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datového disku |Po zřízení některých zadané velikosti datového disku a vytvoří odpovídající virtuální zařízení StorSimple, nesmí zvětšení nebo zmenšení datový disk. Pokus výsledky za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny nepříznivě ovlivnit operace zařízení. |Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místního webového uživatelského rozhraní |Pokud jsou povolené rozšířené funkce zabezpečení v aplikaci Internet Explorer (IE ESC), některé místního webového uživatelského rozhraní stránky jako je například Poradce při potížích nebo údržby nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v Internet Exploreru. |
| **5.** |Místního webového uživatelského rozhraní |Ve virtuálním počítači Hyper-V síťová rozhraní ve webovém uživatelském rozhraní se zobrazují jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazovat 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které využívají StorSimple vrstvených svazků se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple ovládání datových vrstev nebude fungovat. |Doporučené opatření patří: <br></br>Vypněte rozsah bajtů zamykání v logice aplikace.<br></br>Tlačítko Vložit data pro tuto aplikaci místně připojené svazky na rozdíl od vrstvených svazků.<br></br>*Výstražné*: Když je povolený zámek rozsah bajtů pomocí místně připojené svazky, místně vázaný svazek může být online, ještě předtím, než se obnovení dokončí. V takových případech Pokud obnovení probíhá, pak je musí počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory by mohlo způsobit zmenšení pomalé vrstvy. |Při práci s velkými soubory, doporučujeme vám, že je největší soubor menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeby, když nejsou žádná data ve sdílené složce. Toto využití je, protože zahrnuje jejich metadata použitá kapacita pro sdílené složky. | |
| **9.** |Zotavení po havárii |Lze provést pouze zotavení po havárii souborového serveru ke stejné doméně jako zdrojové zařízení. V této verzi nepodporuje zotavení po havárii do cílové zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v části [převzetí služeb při selhání a zotavení po havárii pro StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nejde spravovat prostřednictvím Azure Powershellu v této verzi. |Veškerá Správa virtuálního zařízení se provádí prostřednictvím webu Azure portal a místního webového uživatelského rozhraní. |
| **11.** |Změna hesla |Zařízení konzoly virtuálního pole přijímá pouze vstupu v en-us klávesnice formátu. | |
| **12.** |CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Tento problém je vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě Správce zařízení StorSimple a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné službou Windows systém (ENCRYPTING File) po zkopírování přes nebo uložený na výsledek StorSimple Virtual Array souboru server má nepodporovanou konfiguraci.  | |
| **17.** |Aktualizace |Pokud se zobrazí chyby kódu: 2359302 (šestnáctkově 0x240006) při pokusu o instalaci oprav hotfix pomocí místního uživatelského rozhraní, pak z toho vyplývá, že oprava hotfix nainstalovaná ve vašem zařízení.   | |

## <a name="next-step"></a>Další krok
[Instalace aktualizace 0.6](storsimple-virtual-array-install-update-06.md) na StorSimple Virtual Array.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na:

* [StorSimple Virtual Array Update 0,5 poznámky](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array aktualizace 0.4 poznámky](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 poznámky](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)

