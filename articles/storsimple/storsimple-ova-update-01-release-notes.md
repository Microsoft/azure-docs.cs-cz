---
title: Aktualizace virtuálního pole StorSimple 0,2 & 0,1 poznámky k verzi
description: Popisuje kritické otevřené problémy a řešení pro virtuální pole StorSimple se spuštěnou aktualizací 0.2 a 0.1.
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
ms.openlocfilehash: 95386f36340aca470769c920e40bbb70e09d34fc
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397867"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0.2 a 0.1 poznámky k verzi
## <a name="overview"></a>Přehled
Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro aktualizace virtuálního pole Microsoft Azure StorSimple. (Virtuální pole Microsoft Azure StorSimple se také označuje jako místní virtuální zařízení StorSimple nebo Virtuální zařízení StorSimple.) 

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením virtuálního zařízení StorSimple pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Aktualizace 0.2 odpovídá verzi softwaru **10.0.10280.0**; Aktualizace 0.1 je verze **10.0.10279.0**. V následujících částech jsou uvedeny změny pro každou aktualizaci. 

> [!NOTE]
> Aktualizace jsou rušivé a restartují zařízení. Pokud vstupně-in/o probíhá, zařízení bude mít za následek prostoje.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problémy opravené v aktualizaci 0.2
Aktualizace 0.2 obsahuje všechny změny z aktualizace 0.1 kromě opravy popsané v následující tabulce:

| Funkce | Problém |
| --- | --- |
| Aktualizace |V poslední verzi aktualizace nebyly zjištěny automaticky na klasickém portálu Azure, takže jste museli použít místní webové uživatelské prostředí k instalaci aktualizací. Tento problém je vyřešen v této verzi. Po instalaci aktualizace 0.2 můžete nainstalovat budoucí aktualizace pomocí klasického portálu Azure. |

## <a name="whats-new-in-the-update-01"></a>Co je nového v aktualizaci 0.1
Aktualizace 0.1 obsahuje následující opravy a vylepšení chyb. 

* **Vylepšená odolnost proti chybám pro výpadky cloudu**: Tato verze obsahuje několik oprav chyb kolem zotavení po havárii, zálohování, obnovení a vrstvení v případě přerušení připojení ke cloudu. 
* **Zlepšený výkon obnovení**: Tato verze obsahuje opravy chyb, které výrazně zkrátily dobu dokončení úloh obnovení.
* **Automatická optimalizace rekultivace místa**: Při odstranění dat na tence zřízených svazcích je třeba uvolnit nepoužívané bloky úložiště. Tato verze vylepšila proces rekultivace prostoru z cloudu, což má za následek rychlejší dostupnost nevyužitého místa ve srovnání s předchozími verzemi.
* **Nové image virtuálních disků:** Nové virtuální pevné disky, VHDX a VMDK jsou teď dostupné přes klasický portál Azure. Tyto obrázky si můžete stáhnout a zřídit nová zařízení aktualizace 0.1.
* **Zlepšení přesnosti stavu úloh na portálu**: V dřívější verzi softwaru nebylo vykazování stavu úlohy na portálu podrobné. Tento problém je vyřešen v této verzi.
* **Zkušenosti s připojením k doméně**: Opravy chyb související s připojením k doméně a přejmenováním zařízení.

## <a name="issues-fixed-in-the-update-01"></a>Problémy opravené v aktualizaci 0.1
Následující tabulka obsahuje souhrn problémů opravených v této verzi.

| Ne. | Funkce | Problém |
| --- | --- | --- |
| 1 |Vmdk |V některých verzích vmware byl disk operačního systému považován za řídké, což způsobuje výstrahy a narušuje normální operace. To to bylo opraveno v této verzi. |
| 2 |Server iSCSI |V poslední verzi byl uživatel povinen zadat bránu pro každé povolené síťové rozhraní virtuálního zařízení StorSimple. Toto chování se v této verzi změní tak, aby uživatel musel nakonfigurovat alespoň jednu bránu pro všechna povolená síťová rozhraní. |
| 3 |Balíček podpory |V dřívější verzi softwaru kolekce balíčků podpory se nezdařila, pokud byly velikosti balíčků větší než 1 GB. Tento problém je vyřešen v této verzi. |
| 4 |Přístup ke cloudu |V poslední verzi, pokud StorSimple Virtual Array neměl připojení k síti a byl restartován, místní uI bude mít problémy s připojením. Tento problém je vyřešen v této verzi. |
| 5 |Monitorovací grafy |V předchozí verzi po převzetí služeb při selhání zařízení grafy využití kapacity cloudu zobrazí nesprávné hodnoty na klasickém portálu Azure. To je opraveno v aktuální verzi. |

## <a name="known-issues-in-the-update-01"></a>Známé problémy v aktualizaci 0.1
Následující tabulka obsahuje souhrn známých problémů pro virtuální pole StorSimple a obsahuje problémy s poznámkou k vydání z předchozích verzí. **Vydání problémů uvedené v této verzi jsou označeny hvězdičkou. Téměř všechny problémy v tomto seznamu se přenesly z ga verze StorSimple Virtual Array.**

| Ne. | Funkce | Problém | Řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Aktualizace |Virtuální zařízení vytvořená ve verzi preview nelze aktualizovat na podporovanou verzi obecné dostupnosti. |Tato virtuální zařízení musí být převzetí služeb při selhání pro vydání obecné dostupnosti pomocí pracovního postupu zotavení po havárii (DR). |
| **2.** |Zřízený datový disk |Jakmile zřídíte datový disk určité zadané velikosti a vytvoříte odpovídající virtuální zařízení StorSimple, nesmíte datový disk rozbalit ani zmenšit. Pokud se o to pokusíte, dojde ke ztrátě všech dat v místních vrstvách zařízení. | |
| **3.** |Zásady skupiny |Pokud je zařízení připojeno k doméně, použití zásad skupiny může nepříznivě ovlivnit provoz zařízení. |Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce (OU) pro službu Active Directory a nejsou na něj použity žádné objekty zásad skupiny (GPO). |
| **4.** |Místní webové uživatelské prostředí |Pokud jsou v aplikaci Internet Explorer (IE ESC) povoleny rozšířené funkce zabezpečení, nemusí některé místní webové stránky uživatelského rozhraní, například Poradce při potížích nebo údržba, pracovat správně. Tlačítka na těchto stránkách také nemusí fungovat. |Vypněte rozšířené funkce zabezpečení v aplikaci Internet Explorer. |
| **5.** |Místní webové uživatelské prostředí |Ve virtuálním počítači Hyper-V jsou síťová rozhraní ve webovém uživatelském rozhraní zobrazena jako rozhraní 10 Gb/s. |Toto chování je odrazem Hyper-V. Technologie Hyper-V vždy zobrazuje 10 Gb/s pro virtuální síťové adaptéry. |
| **6.** |Vrstvené svazky nebo sdílené složky |Uzamčení rozsahu bajtů pro aplikace, které pracují s vrstvenými svazky StorSimple, není podporováno. Pokud je povoleno uzamčení rozsahu bajtů, storSimple vrstvení nebude fungovat. |Mezi doporučená opatření patří: <br></br>Vypněte uzamčení rozsahu bajtů v logice aplikace.<br></br>Zvolte, zda chcete umístit data pro tuto aplikaci do místně vázaných svazků na rozdíl od vrstvených svazků.<br></br>*Upozornění:* Pokud je povoleno použití místně vázaných svazků a zamykání rozsahu bajtů, uvědomte si, že místně vázaný svazek může být online ještě před dokončením obnovení. V takových případech pokud probíhá obnovení, musíte počkat na dokončení obnovení. |
| **7.** |Vrstvené sdílené složky |Práce s velkými soubory může mít za následek pomalé vrstvy ven. |Při práci s velkými soubory doporučujeme, aby největší soubor byl menší než 3 % velikosti sdílené složky. |
| **8.** |Použitá kapacita pro sdílené složky |Spotřeba sdílení se může zobrazit v případě, že neexistují žádná data o sdílené složce. Důvodem je, že použitá kapacita pro sdílené složky obsahuje metadata. | |
| **9.** |Zotavení po havárii |Zotavení po havárii souborového serveru lze provést pouze ve stejné doméně jako zdrojové zařízení. Zotavení po havárii na cílové zařízení v jiné doméně není v této verzi podporováno. |To bude implementováno v novější verzi. |
| **10.** |Azure PowerShell |Virtuální zařízení StorSimple nelze spravovat prostřednictvím Azure PowerShell v této verzi. |Veškerá správa virtuálních zařízení by měla být provedena prostřednictvím klasického portálu Azure a místního webového uživatelského prostředí. |
| **11.** |Změna hesla |Konzola zařízení virtuálního pole přijímá vstup pouze ve formátu klávesnice en-US. | |
| **12.** |CHAP |Pověření chap po vytvoření nelze odebrat. Navíc pokud změníte pověření chap, budete muset převést svazky do režimu offline a převést je do režimu online pro změnu se projeví. |Ty budou řešeny v pozdější verzi. |
| **13.** |Server iSCSI |Použité úložiště zobrazené pro svazek iSCSI se může lišit ve službě StorSimple Manager a v hostiteli iSCSI. |Hostitel iSCSI má zobrazení souborového systému.<br></br>Zařízení vidí bloky přidělené, když byl svazek v maximální velikosti. |
| **14.** |Souborový server* |Pokud je k souboru ve složce přidružen alternativní datový proud (ADS), není služba ADS zálohována ani obnovena prostřednictvím zotavení po havárii, klonování a obnovení úrovně položky. | |

## <a name="next-step"></a>Další krok
[Nainstalujte aktualizace](storsimple-ova-install-update-01.md) do virtuálního pole StorSimple.

