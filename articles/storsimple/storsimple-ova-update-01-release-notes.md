---
title: StorSimple Virtual Array Update 0,2 & 0,1 – zpráva k vydání verze
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple s aktualizací Update 0,2 a 0,1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966152"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Zpráva k vydání verze pro StorSimple Virtual Array Update 0,2 a 0,1
## <a name="overview"></a>Přehled
Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro Microsoft Azure StorSimple aktualizace virtuálních polí. (Microsoft Azure StorSimple Virtual Array se také označuje jako místní virtuální zařízení StorSimple nebo virtuální zařízení StorSimple.) 

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením virtuálního zařízení StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0,2 odpovídá **10.0.10280.0** verze softwaru. Aktualizace 0,1 je **10.0.10279.0** verze. V následujících částech jsou uvedeny změny jednotlivých aktualizací. 

> [!NOTE]
> Aktualizace jsou rušivé a restartuje vaše zařízení. Pokud probíhá vstupně-výstupní operace, zařízení bude mít výpadek.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problémy opravené v aktualizaci 0,2
Aktualizace 0,2 zahrnuje všechny změny z aktualizace 0,1 kromě opravy popsané v následující tabulce:

| Příznak | Problém |
| --- | --- |
| Aktualizace |V poslední verzi se aktualizace na portálu Azure Classic nezjistily automaticky, takže k instalaci aktualizací bylo nutné použít místní webové uživatelské rozhraní. Tento problém je opravený v této verzi. Po instalaci aktualizace 0,2 můžete instalovat budoucí aktualizace pomocí portálu Azure Classic. |

## <a name="whats-new-in-the-update-01"></a>Co je nového v aktualizaci 0,1
Aktualizace 0,1 obsahuje následující opravy chyb a vylepšení. 

* **Vylepšení odolnosti proti výpadkům cloudu**: Tato verze obsahuje několik oprav chyb v případě zotavení po havárii, zálohování, obnovení a vrstvení v případě výpadku cloudového připojení. 
* **Vylepšený výkon obnovení**: Tato verze obsahuje opravy chyb, které výrazně vystřižené čas dokončení úloh obnovení.
* **Optimalizace automatizovaného opětovného získávání prostorů**: když se data odstraňují na dynamicky zajištěných svazcích, je nutné uvolnit nepoužívané bloky úložiště. Tato verze zlepšila proces opětovného získávání místa z cloudu, což znamená, že nevyužité místo bude v porovnání s předchozími verzemi rychlejší než k dispozici.
* **Nové image virtuálních disků**: k dispozici je teď nový virtuální pevný disk, VHDX a VMDK prostřednictvím portálu Azure Classic. Tyto Image si můžete stáhnout a zřídit tak nová zařízení s aktualizací Update 0,1.
* **Vylepšení přesnosti stavů úloh na portálu**: v dřívější verzi softwaru se vytváření sestav o stavu úloh na portálu nepodrobně nezobrazuje. Tento problém je vyřešen v této verzi.
* **Prostředí pro připojení k doméně**: opravy chyb související s připojením k doméně a přejmenováním zařízení.

## <a name="issues-fixed-in-the-update-01"></a>Problémy opravené v aktualizaci 0,1
Následující tabulka poskytuje souhrn chyb opravených v této verzi.

| Ne. | Příznak | Problém |
| --- | --- | --- |
| 1 |FORMÁTU |V některých verzích VMware se disk s operačním systémem zobrazil jako zhuštěný a způsobuje výstrahy a přerušení normálních operací. Tato verze byla opravena v této verzi. |
| 2 |Server iSCSI |V poslední vydané verzi musel uživatel zadat bránu pro každé povolené síťové rozhraní virtuálního zařízení StorSimple. Toto chování se v této verzi změní, takže uživatel musí nakonfigurovat aspoň jednu bránu pro všechna povolená síťová rozhraní. |
| 3 |Balíček pro podporu |V dřívější verzi softwaru se kolekce support Package nezdařila, pokud byly velikosti balíčků větší než 1 GB. Tento problém je opravený v této verzi. |
| 4 |Cloudový přístup |Pokud virtuální pole StorSimple nebylo v poslední verzi k dispozici v síti a bylo restartováno, mělo by to mít problémy s připojením k místnímu uživatelskému rozhraní. Tento problém je opravený v této verzi. |
| 5 |Grafy monitorování |V předchozí verzi, po převzetí služeb při selhání zařízení, se v grafech využití kapacity cloudu zobrazovaly nesprávné hodnoty na portálu Azure Classic. Toto je opraveno v aktuální verzi. |

## <a name="known-issues-in-the-update-01"></a>Známé problémy v aktualizaci 0,1
Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje vydané verze zaznamenané z předchozích verzí. **Verze, kterou jsme si poznamenali v této verzi, je označená hvězdičkou. Téměř všechny problémy v tomto seznamu se přenesly z verze GA služby StorSimple Virtual Array.**

| Ne. | Příznak | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořená ve verzi Preview nejde aktualizovat na podporovanou obecnou verzi dostupnosti. |U těchto virtuálních zařízení je potřeba převzít služby při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální zařízení StorSimple, nesmíte tento datový disk rozbalit ani zmenšit. Pokus o provedení této akce způsobí ztrátu všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Když je zařízení připojené k doméně, může použití zásad skupiny negativně ovlivnit operaci zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory a že pro něj nejsou použité žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské rozhraní |Pokud jsou v aplikaci Internet Explorer povoleny rozšířené funkce zabezpečení (IE ESC), některé místní stránky webového uživatelského rozhraní, jako je například řešení potíží nebo údržba, nemusí správně fungovat. Tlačítka na těchto stránkách také nemusí fungovat. |Vypnout rozšířené funkce zabezpečení v Internet Exploreru. |
| **čl.** |Místní webové uživatelské rozhraní |Ve virtuálním počítači s technologií Hyper-V se síťová rozhraní ve webovém uživatelském rozhraní zobrazují jako rozhraní o 10 GB/s. |Toto chování je reflexe technologie Hyper-V. Technologie Hyper-V vždy zobrazuje 10 GB/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamykání rozsahu bajtů pro aplikace, které pracují se StorSimple vrstvenými svazky, se nepodporuje. Pokud je povolené zamykání rozsahu bajtů, StorSimpleing nebude fungovat. |Doporučené míry zahrnují: <br></br>Vypněte uzamykání rozsahu bajtů v logice aplikace.<br></br>Vyberte, chcete-li umístit data pro tuto aplikaci na místně připnuté svazky na rozdíl od vrstveného svazku.<br></br>*Upozornění*: Pokud je povolené použití místně připnuté svazky a uzamykání rozsahu bajtů, pamatujte, že místně připojený svazek může být online, i když se obnovení nedokončí. V takových případech, pokud probíhá obnovení, je nutné počkat na dokončení obnovení. |
| **čl.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek zpomalení vrstvy. |Při práci s velkými soubory doporučujeme, aby byl největší soubor menší než 3% velikosti sdílené složky. |
| **8.** |Využitá kapacita pro sdílené složky |Může se zobrazit spotřeba sdílení při nedostatku dat na sdílené složce. Důvodem je to, že využitá kapacita pro sdílené složky zahrnuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru můžete provést jenom se stejnou doménou, jakou má zdrojové zařízení. V této verzi není podporováno zotavení po havárii do cílového zařízení v jiné doméně. |Tato akce bude implementována v pozdější verzi. |
| **10pruhový.** |Azure PowerShell |Virtuální zařízení StorSimple se nedají spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by se měla provádět prostřednictvím portálu Azure Classic a místního webového uživatelského rozhraní. |
| **odst.** |Změna hesla |Konzola zařízení Virtual Array podporuje pouze vstup ve formátu klávesnice en-US. | |
| **12,5.** |CHAP |Po vytvoření nelze odstranit přihlašovací údaje protokolu CHAP. Navíc platí, že pokud změníte přihlašovací údaje protokolu CHAP, budete muset svazky převést do režimu offline a pak je převést do režimu online, aby se změna projevila. |Ty se budou řešit v pozdější verzi. |
| **13,5.** |Server iSCSI |Použité úložiště pro svazek iSCSI se může lišit ve službě StorSimple Manager a hostiteli iSCSI. |Hostitel iSCSI má zobrazení systému souborů.<br></br>Zařízení vidí bloky přidělené v případě, že byl svazek v maximální velikosti. |
| **čtrnáct.** |Souborový server * |Pokud k souboru ve složce je přidružen alternativní datový proud (ADS), nejsou tyto reklamy zálohovány ani obnoveny prostřednictvím zotavení po havárii, klonování a obnovení na úrovni položek. | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizace](./storsimple-virtual-array-install-update-06.md) ve virtuálním poli StorSimple.