---
title: Poznámky k verzi StorSimple Virtual Array Update 1.0 | Dokumentace Microsoftu
description: Popisuje důležité otevřených problémů a jejich řešení pro StorSimple Virtual Array Update 1.0 spuštěna.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fdf37a8360ec69017458fabee2a9e16aa2c160aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789667"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>Poznámky k verzi StorSimple Virtual Array Update 1.0

## <a name="overview"></a>Přehled

Následující zpráva k vydání verze Identifikujte kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple Virtual Array aktualizace.

Zpráva k vydání verze se průběžně aktualizuje, a při zjištění zásadních problémů vyžadujících alternativní řešení, se přidají. Před nasazením StorSimple Virtual Array, pečlivě si prostudujte informace obsažené v poznámkách k verzi.

Odpovídá verzi softwaru Update 1.0 **10.0.10296.0**.

> [!IMPORTANT]
> - Aktualizace se jedná o narušující a restartujte zařízení. Pokud probíhají vstupně-výstupních operací, zařízení způsobí výpadek. Podrobné pokyny o tom, jak použít aktualizaci, přejděte na [instalace Update 1.0](storsimple-virtual-array-install-update-1.md).
>
> - Aktualizace 1 je prostřednictvím portálu Azure portal k dispozici pouze v případě aktualizace 0.6 na zařízení běží.

## <a name="whats-new-in-update-10"></a>Co je nového ve verzi Update 1.0

**Obsahuje změny související s ověřováním služby Správce zařízení StorSimple Update 1.0 a musí být nasazené na váš nejstarší.** Tato aktualizace obsahuje následující vylepšení a opravy chyb:

 - **Použití nástroje Azure Active Directory (AAD) k ověření pomocí služby Správce zařízení StorSimple** – od Update 1.0 a vyšší, Azure Active Directory se používá k ověření ve službě Správce zařízení StorSimple. Staré mechanismus ověřování se přestanou používat. prosince 2017. Všichni uživatelé musí obsahovat nové ověřovací adresy URL v jeho pravidlech brány firewall. Další informace najdete v části ověřování adresy URL uvedené v [požadavků na síť pro StorSimple Virtual Array](storsimple-ova-system-requirements.md).
 
    Pokud ověřovací adresa URL není zahrnut v pravidlech brány firewall, uživatelé uvidí kritickou výstrahu, která se jejich zařízení StorSimple nemohlo ověřit ve službě. Pokud se uživatelům zobrazuje toto upozornění, musí obsahovat nové adresy URL ověřování. Další informace najdete v části [StorSimple sítě výstrahy](storsimple-virtual-array-manage-alerts.md).

 - **Zlepšení výkonu** – několik oprav chyb, které jste dokončili ke zlepšení rychlosti cloudových čtení, in úroveň a úroveň činnostem. V důsledku toho se pro zařízení se server iSCSI a souboru zvýšil výkon zálohování a obnovení.

 - **Uvolňování paměti kolekce zlepšování** – tato verze obsahuje opravy chyb, které zlepšují výkon při účet zařízení a úložiště jsou ve dvou oblastech vzdálené cyklu uvolňování paměti.

 - **Protokolování zlepšování** – tato verze obsahuje vylepšení pro protokolování související s uvolňování paměti a vstupně-výstupní cestu.


## <a name="issues-fixed-in-update-10"></a>Chyby opravené v Update 1.0

Následující tabulka obsahuje souhrn chyby opravené v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Ověřování pomocí AAD| Tato verze obsahuje změny, které umožňuje AAD k ověřování pomocí Správce zařízení StorSimple.|
| 2 |Kolekce paměti| Tento problém byl nahlášen na zákazníka, kde jsou účty zařízení a úložiště v různých oblastech a zákazník hlášené chyby sítě a vliv na fakturaci. V této verzi se tento problém vyřešen. |
| 3 |Výkon| Tato verze obsahuje změny, které za následek obnovení nebo cloudových čtení/vrstvy v / vrstvy na zlepšení výkonu.|
| 4 |Aktualizace| Došlo k nějakému problému s aktualizací update v předchozí verzi, které vedlo k selhání zálohování na webu zákazníka. Tento problém je vyřešen v této verzi.|

## <a name="known-issues-in-update-10"></a>Známé problémy ve verzi Update 1.0

Následující tabulka obsahuje souhrn známé problémy pro StorSimple Virtual Array a problémy uvedené verze z předchozích verzí.

| Ne. | Funkce | Problém | Alternativní řešení a poznámky |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální pole vytvořené ve verzi preview, nejde aktualizovat na podporovanou verzi obecné dostupnosti. |Tyto virtuální pole musí být převzetí služeb při selhání pro všeobecně dostupné verze pomocí pracovního postupu zotavení po havárii. |
| **2.** |Zřízené datového disku |Po zřízení některých zadané velikosti datového disku a vytvoří odpovídající StorSimple Virtual Array, nesmí zvětšení nebo zmenšení datový disk. Pokus výsledky za následek ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, použití zásad skupiny nepříznivě ovlivnit operace zařízení. |Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory a žádné objekty zásad skupiny (GPO) se použijí k němu. |
| **4.** |Místního webového uživatelského rozhraní |Pokud jsou povolené rozšířené funkce zabezpečení v aplikaci Internet Explorer (IE ESC), některé místního webového uživatelského rozhraní stránky jako je například Poradce při potížích nebo údržby nemusí fungovat správně. Tlačítka na těchto stránkách nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v Internet Exploreru. |
| **5.** |Místního webového uživatelského rozhraní |Ve virtuálním počítači Hyper-V síťová rozhraní ve webovém uživatelském rozhraní se zobrazují jako 10 GB/s rozhraní. |Toto chování je odraz technologie Hyper-V. Technologie Hyper-V vždy zobrazovat 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Rozsah bajtů uzamčení pro aplikace, které využívají StorSimple vrstvených svazků se nepodporuje. Pokud je povolený zámek rozsah bajtů, StorSimple ovládání datových vrstev nebude fungovat. |Doporučené opatření patří: <br></br>Vypněte rozsah bajtů zamykání v logice aplikace.<br></br>Tlačítko Vložit data pro tuto aplikaci místně připojené svazky na rozdíl od vrstvených svazků.<br></br>*Výstražné*: Když je povolený zámek rozsah bajtů pomocí místně připojené svazky, místně vázaný svazek může být online, ještě předtím, než se obnovení dokončí. V takových případech Pokud obnovení probíhá, pak je musí počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory by mohlo způsobit zmenšení pomalé vrstvy. |Při práci s velkými soubory, doporučujeme vám, že je největší soubor menší než velikost sdílené složky % 3. |
| **8.** |Použít kapacity pro sdílené složky |Může se zobrazit sdílet spotřeby, když nejsou žádná data ve sdílené složce. Toto využití je, protože zahrnuje jejich metadata použitá kapacita pro sdílené složky. | |
| **9.** |Zotavení po havárii |Lze provést pouze zotavení po havárii souborového serveru ke stejné doméně jako zdrojové zařízení. V této verzi nepodporuje zotavení po havárii do cílové zařízení v jiné doméně. |To je implementováno v novější verzi. Další informace najdete v části [převzetí služeb při selhání a zotavení po havárii pro StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Virtuální pole StorSimple, nejde spravovat pomocí Azure Powershellu v této verzi. |Veškerá Správa virtuálního zařízení se provádí prostřednictvím webu Azure portal a místního webového uživatelského rozhraní. |
| **11.** |Změna hesla |Zařízení konzoly virtuálního pole přijímá pouze vstupu v en-us klávesnice formátu. | |
| **12.** |CHAP |Přihlašovací údaje protokolu CHAP po vytvoření nelze odebrat. Kromě toho pokud změníte přihlašovací údaje protokolu CHAP, musíte převést svazky do režimu offline a pak přiřaďte je online se změna projevila. |Tento problém je vyřešen v pozdější verzi. |
| **13.** |iSCSI server |"Použili úložiště" zobrazí u svazku iSCSI, může být jiný ve službě Správce zařízení StorSimple a iSCSI hostitele. |Zobrazení systému souborů má hostitel iSCSI.<br></br>Zařízení se zobrazí bloky přidělí, když objem byl při maximální velikosti. |
| **14.** |Souborový server |Pokud má soubor ve složce alternativní datový Stream (reklamy) s ním spojená, reklamy nejsou zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |
| **15.** |Souborový server |Symbolické odkazy nejsou podporovány. | |
| **16.** |Souborový server |Soubory chráněné službou Windows systém (ENCRYPTING File) po zkopírování přes nebo uložený na výsledek StorSimple Virtual Array souboru server má nepodporovanou konfiguraci.  | |
| **17.** |Aktualizace |Pokud se zobrazí chyby kódu: 2359302 (šestnáctkově 0x240006) při pokusu o instalaci oprav hotfix pomocí místního uživatelského rozhraní, pak z toho vyplývá, že oprava hotfix nainstalovaná ve vašem zařízení.   | |
| **18.** |Aktualizace |Pokud instalace Update 1 na vaše virtuální pole pomocí místního webového uživatelského rozhraní, musíte zajistit, že používáte aktualizace 0.6. Pokud používáte verzi nižší než aktualizace 0.6, musíte nejprve nainstalovat aktualizace 0.6 a pak použít Update 1. Pokud přímo z verze před aktualizací 0,6 nainstalovat Update 1.0, pak bude chybí některé aktualizace a grafy monitorování nebude fungovat.   | |


## <a name="next-steps"></a>Další postup
[Instalace Update 1.0](storsimple-virtual-array-install-update-1.md) na StorSimple Virtual Array.

## <a name="references"></a>Odkazy
Hledáte starší poznámku k verzi? Přejít na:
*  [StorSimple Virtual Array Update 0,6 poznámky](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 poznámky](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array aktualizace 0.4 poznámky](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 poznámky](storsimple-ova-update-03-release-notes.md)
* [Poznámky k verzi StorSimple Virtual Array Update 0.1 a 0.2](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array obecné dostupnosti zpráva k vydání verze](storsimple-ova-pp-release-notes.md)
