---
title: Odkaz na nastavení roamingu ve Windows 10 – Azure Active Directory
description: Nastavení, která se budou potulovat nebo zálohovat v systému Windows 10 pomocí ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7abc402f1fc2e449e7aac5effdb01b6b941100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672631"
---
# <a name="windows-10-roaming-settings-reference"></a>Referenční informace k nastavení roamingu pro Windows 10

Následuje seznam nastavení, která budou přebujela nebo zálohovala v systému Windows 10. 

## <a name="devices-and-endpoints"></a>Zařízení a koncové body

V následující tabulce najdete souhrn zařízení a typů účtů, které podporuje architektura synchronizace, zálohování a obnovení v systému Windows 10.

| Typ a operace účtu | Aplikace klasické pracovní plochy | Mobilní zařízení |
| --- | --- | --- |
| Azure Active Directory: synchronizace |Ano |Ne |
| Azure Active Directory: zálohování a obnovení |Ne |Ne |
| Účet Microsoft: synchronizace |Ano |Ano |
| Účet Microsoft: zálohování/obnovení |Ne |Ano |

## <a name="what-is-backup"></a>Co je záloha?

Nastavení systému Windows se obvykle synchronizují ve výchozím nastavení, ale některá nastavení jsou pouze zálohována, například seznam nainstalovaných aplikací v zařízení. Záloha je pouze pro mobilní zařízení a momentálně není k dispozici pro uživatele podnikového programu. Zálohování používá účet Microsoft a ukládá nastavení a data aplikací na OneDrive. Pokud uživatel zakáže synchronizaci v zařízení pomocí aplikace Nastavení, data aplikace, která se normálně synchronizují, se stanou pouze zálohami. K datům zálohování lze přistupovat pouze prostřednictvím operace obnovení během prvního spuštění nového zařízení. Zálohy lze zakázat pomocí nastavení zařízení a lze je spravovat a odstraňovat prostřednictvím účtu OneDrive uživatele.

## <a name="windows-settings-overview"></a>Přehled nastavení systému Windows

Následující skupiny nastavení jsou k dispozici pro koncové uživatele, aby povolili nebo zakázali synchronizaci nastavení na zařízeních s Windows 10.

* Téma: pozadí plochy, dlaždice uživatele, pozice na hlavním panelu atd. 
* Nastavení aplikace Internet Explorer: historie procházení, zadané adresy URL, oblíbené položky atd. 
* Hesla: Správce přihlašovacích údajů systému Windows, včetně profilů Wi-Fi 
* Jazykové předvolby: slovník pravopisu, nastavení systémového jazyka 
* Usnadnění přístupu: předčítání, klávesnice na obrazovce, lupa 
* Další nastavení Windows: viz Podrobnosti o nastavení systému Windows
* Nastavení prohlížeče Microsoft Edge: Oblíbené položky microsoft edge, seznam k přečtení a další nastavení

![Synchronizovat nastavení](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Tento článek se týká prohlížeče microsoft edge legacy založeného na HTML spuštěném v systému Windows 10 v červenci 2015. Tento článek se nevztahuje na nový prohlížeč založený na microsoft edge chromu vydaném 15. Další informace o chování synchronizace pro nový microsoft edge naleznete v článku [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

Skupina nastavení prohlížeče Microsoft Edge (oblíbené položky, seznam k přečtení) může být povolena nebo zakázána koncovými uživateli prostřednictvím možnosti nabídky Nastavení prohlížeče Microsoft Edge.

![Účet](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Pro Windows 10 verze 1803 nebo novější může být synchronizace nastavení aplikace Internet Explorer (oblíbené položky, zadané adresy URL) povolena nebo zakázána koncovými uživateli prostřednictvím možnosti nabídky Nastavení aplikace Internet Explorer. 

![Nastavení](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Podrobnosti o nastavení systému Windows

V následující tabulce ostatní položky ve sloupci Skupina nastavení odkazují na nastavení, která lze zakázat, přejděte do nastavení > účty > Synchronizovat nastavení > ostatní nastavení windows. 

Interní položky ve sloupci Skupina nastavení odkazují na nastavení a aplikace, které lze zakázat pouze v synchronizaci v samotné aplikaci nebo zakázáním synchronizace pro celé zařízení pomocí nastavení správy mobilních zařízení (MDM) nebo zásad skupiny.
Nastavení, která se netoulají nebo nesynchronizují, nebudou patřit do skupiny.

| Nastavení | Aplikace klasické pracovní plochy | Mobilní zařízení | Skupina |
| --- | --- | --- | --- |
| **Účty**: obrázek účtu |synchronizace |× |Motiv |
| **Účty**: další nastavení účtu |× |× | |
| **Pokročilé mobilní širokopásmové připojení**: Název sítě pro sdílení připojení k internetu (umožňuje automatické zjišťování mobilních Wi-Fi hotspotů přes Bluetooth) |× |× |Hesla |
| **Data aplikací**: jednotlivé aplikace mohou synchronizovat data |synchronizace zálohy |synchronizace zálohy |internal |
| **Seznam aplikací**: seznam nainstalovaných aplikací |× |zálohování |Ostatní |
| **Bluetooth**: všechna nastavení Bluetooth |× |× | |
| **Příkazový řádek**: Nastavení příkazového řádku "Výchozí" |synchronizace |× |internal |
| **Pověření**: Schůdka pověření |synchronizace |synchronizace |heslo |
| **Datum, čas a oblast**: automatický čas (synchronizace času v Internetu) |synchronizace |synchronizace |language |
| **Datum, čas a oblast**: 24hodinová doba |synchronizace |× |language |
| **Datum, čas a oblast**: datum a čas |synchronizace |× |language |
| **Datum, čas a oblast**: časové pásmo | |× |language |
| **Datum, čas a oblast**: letní čas |synchronizace |× |language |
| **Datum, čas a oblast**: země/oblast |synchronizace |× |language |
| **Datum, čas a oblast**: první den v týdnu |synchronizace |× |language |
| **Datum, čas a oblast**: formát oblasti (národní prostředí) |synchronizace |× |language |
| **Datum, čas a oblast**: krátké datum |synchronizace |× |language |
| **Datum, čas a oblast**: dlouhé datum |synchronizace |× |language |
| **Datum, čas a oblast**: krátký čas |synchronizace |× |language |
| **Datum, čas a oblast**: dlouhá doba |synchronizace |× |language |
| **Přizpůsobení plochy**: desktop téma (pozadí, barva systému, výchozí systémové zvuky, spořič obrazovky) |synchronizace |× |Motiv |
| **Přizpůsobení plochy**: tapeta prezentace |synchronizace |× |Motiv |
| **Přizpůsobení plochy**: nastavení hlavního panelu (pozice, automatické skrytí atd.) |synchronizace |× |Motiv |
| **Přizpůsobení plochy:** rozložení úvodní obrazovky |× |zálohování | |
| **Zařízení:** sdílené tiskárny, ke kterých jste se připojili |× |× |ostatní |
| **Prohlížeč Microsoft Edge**: seznam k přečtení |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: oblíbené položky |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nejlepší weby <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: zadané adresy URL <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nastavení panelu oblíbených položek <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: zobrazení domovského tlačítka <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: blokování automaticky otevíraných míst <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: zeptejte se mě, co dělat s každým <sup> [stažením [1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nabídka ukládání hesel <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: odeslat nesledovat požadavky <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: uložení položek formuláře <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: zobrazit návrhy vyhledávání a webu při psaní <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: předvolba cookies <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: umožňuje webům ukládat licence chráněných médií na mém zařízení <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nastavení čtečky obrazovky <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Vysoký kontrast**: Zapnutí nebo vypnutí |synchronizace |× |snadný přístup |
| **Vysoký kontrast**: Nastavení motivu |synchronizace |× |snadný přístup |
| **Internet Explorer**: otevřít karty (URL a název) |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: seznam k přečtení |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: zadané adresy URL |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: historie procházení |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: oblíbené položky |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: vyloučené adresy URL |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: domovské stránky |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: návrhy domén |synchronizace |synchronizace |Internet Explorer |
| **Klávesnice**: uživatelé mohou zapnout/vypnout klávesnici na obrazovce |synchronizace |× |snadný přístup |
| **Klávesnice:** zapnutí sticky ano (ve výchozím nastavení vypnuto) |synchronizace |× |snadný přístup |
| **Klávesnice:** zapnutí kláves filtru (ve výchozím nastavení vypnuto) |synchronizace |× |snadný přístup |
| **Klávesnice:** zapnutí přepínacích kláves (ve výchozím nastavení vypnuto) |synchronizace |× |snadný přístup |
| **Internet Explorer**: doména Jazyk: Čínština (CHS) QWERTY - povolit samostudium |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - umožňují dynamické hodnocení kandidátů |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - znaková zjednodušená čínština |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - znaková tradiční čínština |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - fuzzy pinyin |synchronizace |zálohování |Jazyk |
| **Jazyk**: CHS QWERTY - fuzzy páry |synchronizace |zálohování |Jazyk |
| **Jazyk**: CHS QWERTY - plný pinyin |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - dvojitý pinyin |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - čtení automatické korekce |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - C/E přepínač, shift |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY - C/E přepínač, Ctrl |synchronizace |× |Jazyk |
| **Jazyk**: CHS WUBI - režim vstupu s jedním znakem |synchronizace |× |Jazyk |
| **Jazyk**: CHS WUBI - zobrazit zbývající kódování kandidáta |synchronizace |× |Jazyk |
| **Jazyk**: CHS WUBI - pípnutí, když je 4-kódování neplatné |synchronizace |× |Jazyk |
| **Jazyk**: CHT Bopomofo - patří CJK Ext-A |synchronizace |× |Jazyk |
| **Jazyk**: Japonský ime - prediktivní psaní a vlastní slova |synchronizace |synchronizace |Jazyk |
| **Jazyk**: IME pro korejštinu (KOR) |× |× |Jazyk |
| **Jazyk**: rozpoznávání rukopisu |× |× |Jazyk |
| **Jazyk**: jazykový profil |synchronizace |zálohování |Jazyk |
| **Jazyk**: kontrola pravopisu - automatické opravy a zvýraznění překlepů |synchronizace |zálohování |Jazyk |
| **Jazyk**: seznam klávesnic |synchronizace |zálohování |Jazyk |
| **Zamykací obrazovka**: všechna nastavení zamykací obrazovky |× |× | |
| **Lupa**: zapnutí nebo vypnutí (hlavní přepínač) |× |× |Usnadnění přístupu |
| **Lupa**: zapnutí nebo vypnutí barvy inverze (ve výchozím nastavení vypnuto) |synchronizace |× |Usnadnění přístupu |
| **Lupa**: sledování - sledujte fokus klávesnice |synchronizace |× |Usnadnění přístupu |
| **Lupa**: sledování - postupujte podle kurzoru myši |synchronizace |× |Usnadnění přístupu |
| **Lupa**: spustí se, když se uživatelé přihlásí (ve výchozím nastavení vypnuto) |synchronizace |× |Usnadnění přístupu |
| **myš:** změnit velikost kurzoru myši |synchronizace |× |ostatní |
| **myš:** změnit barvu kurzoru myši |synchronizace |× |ostatní |
| **Myš**: všechna ostatní nastavení |× |× | |
| **Předčítání**: rychlé spuštění |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: Uživatelé mohou změnit řečnickou výšku předčítání |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: Uživatelé mohou zapnout nebo vypnout rady pro čtení předčítání pro běžné položky (ve výchozím nastavení zapnuto) |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: Uživatelé mohou zapnout nebo vypnout, zda slyší zadané znaky (ve výchozím nastavení zapnuto) |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: Uživatelé mohou zapnout nebo vypnout, zda slyší zadávaná slova (ve výchozím nastavení zapnutá) |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: mají vložit kurzor za Předčítání (ve výchozím nastavení) |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: povolit vizuální zvýraznění kurzoru Předčítání (ve výchozím nastavení zapnuto) |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: přehrávání zvukových signálů (ve výchozím nastavení zapnuto) |synchronizace |× |Usnadnění přístupu |
| **Předčítání**: aktivace kláves na dotykové klávesnici při zvednutí prstu (ve výchozím nastavení vypnutá) |synchronizace |× |Usnadnění přístupu |
| **Usnadnění přístupu:** nastavte tloušťku blikajícího kurzoru |synchronizace |× |Usnadnění přístupu |
| **Snadný přístup**: odstranění obrázků na pozadí (ve výchozím nastavení vypnuto) |synchronizace |× |Usnadnění přístupu |
| **Napájení a spánek**: všechna nastavení |× |× | |
| **Přizpůsobení úvodní obrazovky:** barva zvýraznění (pouze telefon) |× |synchronizace |Motiv |
| **Psaní**: slovník pravopisu |synchronizace |zálohování |Jazyk |
| **Psaní**: automatické opravy chybně napsané slovo |synchronizace |zálohování |Jazyk |
| **Psaní**: zvýraznění chybně napsaných slov |synchronizace |zálohování |Jazyk |
| **Psaní**: zobrazení textových návrhů při psaní |synchronizace |zálohování |Jazyk |
| **Psaní**: přidání mezery po výběru návrhu textu |synchronizace |zálohování |Jazyk |
| **Psaní**: přidejte tečku po poklepání na mezerník |synchronizace |zálohování |Jazyk |
| **Psaní na stroji**: první písmeno každé věty |synchronizace |zálohování |Jazyk |
| **Psaní**: Při poklepání na klávesu Shift použijte všechna velká písmena |synchronizace |zálohování |Jazyk |
| **Psaní**: přehrání zvuků kláves při psaní |synchronizace |zálohování |Jazyk |
| **Psaní**: data přizpůsobení pro dotykovou klávesnici |synchronizace |zálohování |Jazyk |
| **Wi-Fi**: Profily Wi-Fi (pouze WPA) |synchronizace |synchronizace |Hesla |

###### <a name="footnote-1"></a>Poznámka pod čarou 1

Minimální podporovaná verze operačního systému Windows Creators Update (Sestavení 15063). 

## <a name="next-steps"></a>Další kroky

Přehled najdete v tématu [Přehled podnikového státního roamingu](enterprise-state-roaming-overview.md).
