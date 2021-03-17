---
title: Referenční informace o nastavení roamingu pro Windows 10 – Azure Active Directory
description: Nastavení, která budou roamingovaná nebo zálohovaná ve Windows 10 s ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85252963"
---
# <a name="windows-10-roaming-settings-reference"></a>Referenční informace k nastavení roamingu pro Windows 10

Následuje seznam nastavení, která budou v systému Windows 10 roamingovaná nebo zálohovaná. 

## <a name="devices-and-endpoints"></a>Zařízení a koncové body

V následující tabulce najdete souhrn typů zařízení a účtů, které jsou podporované architekturou synchronizace, zálohování a obnovení ve Windows 10.

| Typ účtu a operace | Plocha | Mobilní |
| --- | --- | --- |
| Azure Active Directory: synchronizace |Ano |No |
| Azure Active Directory: zálohování a obnovení |No |No |
| Účet Microsoft: synchronizace |Ano |Ano |
| Účet Microsoft: zálohování a obnovení |No |Ano |

## <a name="what-is-backup"></a>Co je zálohování?

Nastavení Windows se standardně synchronizuje, ale některá nastavení se zálohují jenom, jako je například seznam nainstalovaných aplikací na zařízení. Zálohování je jenom pro mobilní zařízení a v současnosti není k dispozici pro Enterprise State Roaming uživatele. Zálohování používá účet Microsoft a ukládá nastavení a data aplikací do OneDrivu. Pokud uživatel na zařízení zakáže synchronizaci pomocí aplikace nastavení, data aplikace, která se normálně synchronizují, se budou zálohovat jenom. K datům zálohy se dá během prvního spuštění nového zařízení dostat jenom prostřednictvím operace obnovení. Zálohy je možné zakázat prostřednictvím nastavení zařízení a je možné je spravovat a odstraňovat prostřednictvím účtu OneDrive uživatele.

## <a name="windows-settings-overview"></a>Přehled nastavení systému Windows

Pro koncové uživatele jsou k dispozici následující skupiny nastavení, které povolí nebo zakáže synchronizaci nastavení na zařízeních s Windows 10.

* Motiv: pozadí plochy, dlaždice uživatele, pozice hlavního panelu atd. 
* Nastavení aplikace Internet Explorer: historie procházení, typové adresy URL, oblíbené položky atd. 
* Hesla: Správce přihlašovacích údajů systému Windows, včetně profilů Wi-Fi 
* Jazykové předvolby: slovník pravopisu, systémová nastavení jazyka 
* Usnadnění přístupu: Předčítání, klávesnice na obrazovce, Lupa 
* Další nastavení Windows: informace o nastavení Windows
* Nastavení prohlížeče Microsoft Edge: oblíbené položky Microsoft Edge, seznam čtení a další nastavení

![Synchronizovat nastavení](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Tento článek se týká starší verze prohlížeče založeného na HTML v Microsoft Edge, který se spouští s Windows 10 v červenci 2015. Článek neplatí pro nový prohlížeč založený na Microsoft Edge chrom vydaný 15. ledna 2020. Další informace o chování synchronizace pro nové Microsoft Edge najdete v článku o [synchronizaci Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

Možnost Skupina nastavení prohlížeče Microsoft Edge (oblíbené položky, seznam čtení) může povolit nebo zakázat koncovým uživatelům pomocí možnosti nabídky nastavení prohlížeče Microsoft Edge.

![Účet](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Pro Windows 10 verze 1803 nebo novější se může synchronizace nastavení Internet Exploreru (oblíbené položky, typové adresy URL) povolit nebo zakázat pomocí možnosti nabídky nastavení aplikace Internet Explorer pro koncové uživatele. 

![Nastavení](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Podrobnosti nastavení systému Windows

V následující tabulce se ve sloupci skupina nastavení zobrazí další položky nastavení, které je možné zakázat, a to tak, že v části nastavení > účty > synchronizujete nastavení > ostatní nastavení systému Windows. 

Interní položky ve sloupci skupina nastavení odkazují na nastavení a aplikace, které se můžou zakázat jenom v synchronizaci v rámci samotné aplikace, nebo vypnutím synchronizace pro celé zařízení pomocí správy mobilních zařízení (MDM) nebo nastavení Zásady skupiny.
Nastavení, která se nevztahují k roamingu nebo synchronizaci, nepatří do skupiny.

| Nastavení | Plocha | Mobilní | Group (Skupina) |
| --- | --- | --- | --- |
| **Účty**: obrázek účtu |synchronizace |× |Motiv |
| **Účty**: Další nastavení účtu |X |X | |
| **Rozšířené mobilní širokopásmové**připojení: název sítě pro sdílení připojení k Internetu (umožňuje automatické zjišťování mobilních Wi-Fi hotspotů přes Bluetooth) |X |X |Hesla |
| **Data aplikací**: jednotlivé aplikace můžou synchronizovat data |synchronizovat zálohu |synchronizovat zálohu |internal |
| **Seznam aplikací**: seznam nainstalovaných aplikací |× |zálohování |Ostatní |
| **Bluetooth**: všechna nastavení Bluetooth |X |X | |
| **Příkazový řádek**: výchozí nastavení pro příkazový řádek |synchronizace |× |internal |
| **Přihlašovací údaje**: schránka na přihlašovací údaje |synchronizace |synchronizace |heslo |
| **Datum, čas a oblast**: Automatický čas (synchronizace v internetovém čase) |synchronizace |synchronizace |language |
| **Datum, čas a oblast**: 24 hodin |synchronizace |× |language |
| **Datum, čas a oblast**: datum a čas |synchronizace |× |language |
| **Datum, čas a oblast**: časové pásmo | |× |language |
| **Datum, čas a oblast**: letní čas |synchronizace |× |language |
| **Datum, čas a oblast**: země/oblast |synchronizace |× |language |
| **Datum, čas a oblast**: první den v týdnu |synchronizace |× |language |
| **Datum, čas a oblast**: formát oblasti (národní prostředí) |synchronizace |× |language |
| **Datum, čas a oblast**: krátké datum |synchronizace |× |language |
| **Datum, čas a oblast**: dlouhé datum |synchronizace |× |language |
| **Datum, čas a oblast**: krátký čas |synchronizace |× |language |
| **Datum, čas a oblast**: dlouhý čas |synchronizace |× |language |
| **Individuální nastavení plochy**: motiv plochy (pozadí, systémová barva, výchozí systémové zvuky, šetřič obrazovky) |synchronizace |× |Motiv |
| **Individuální nastavení plochy**: Tapeta prezentace |synchronizace |× |Motiv |
| **Individuální nastavení plochy**: nastavení hlavního panelu (pozice, automatické skrývání atd.) |synchronizace |× |Motiv |
| **Individuální nastavení plochy**: rozložení úvodní obrazovky |× |zálohování | |
| **Zařízení**: sdílené tiskárny, ke kterým jste se připojili |X |X |other |
| **Prohlížeč Microsoft Edge**: seznam pro čtení |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: oblíbené položky |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: hlavní lokality <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: typové adresy URL <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nastavení panelu Oblíbené položky <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: zobrazí tlačítko domů <sup> [[1]](#footnote-1) .</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: blokovat automaticky otevíraná okna blokování <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: Zeptejte se, co dělat s každým stažením <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nabídka pro ukládání hesel <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: odeslání požadavků do Not Track <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: uložit položky formuláře <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: umožňuje zobrazit hledání a návrhy webu jako typ <sup> [[1]](#footnote-1) .</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: předvolby souborů cookie <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: umožňuje webům ukládat chráněné licence na média v zařízení <sup> [[1]](#footnote-1) .</sup> |synchronizace |synchronizace |internal |
| **Prohlížeč Microsoft Edge**: nastavení čtečky obrazovky <sup> [[1]](#footnote-1)</sup> |synchronizace |synchronizace |internal |
| **Vysoký kontrast**: zapnuto nebo vypnuto |synchronizace |× |usnadnění přístupu |
| **Vysoký kontrast**: nastavení motivu |synchronizace |× |usnadnění přístupu |
| **Internet Explorer**: otevření karet (adresa URL a název) |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: seznam čtení |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: typové adresy URL |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: historie procházení |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: oblíbené položky |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: vyloučené adresy URL |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: domovské stránky |synchronizace |synchronizace |Internet Explorer |
| **Internet Explorer**: návrhy domény |synchronizace |synchronizace |Internet Explorer |
| **Klávesnice**: uživatelé můžou zapnout nebo vypnout klávesnici na obrazovce. |synchronizace |× |usnadnění přístupu |
| **Klávesnice**: zapnout funkci Ano (ve výchozím nastavení vypnuté) |synchronizace |× |usnadnění přístupu |
| **Klávesnice**: zapnout filtrování klíčů (ve výchozím nastavení vypnuté) |synchronizace |× |usnadnění přístupu |
| **Klávesnice**: zapnout přepínací klávesy (ve výchozím nastavení vypnuté) |synchronizace |× |usnadnění přístupu |
| **Internet Explorer**: jazyk domény: ČÍNŠTINA (CHS) QWERTY – povolit samoobslužné učení |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY – povolit dynamické řazení kandidátů |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY-char-set zjednodušená čínština |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY-char-set tradiční čínština |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY – přibližný pchin-jin |synchronizace |zálohování |Jazyk |
| **Jazyk**: CHS QWERTY – páry – přibližné |synchronizace |zálohování |Jazyk |
| **Jazyk**: CHS QWERTY – úplný pchin-jin |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY – dvojitý pchin-jin |synchronizace |× |Jazyk |
| **Jazyk**: CHS QWERTY – čtení automatických oprav |synchronizace |× |Jazyk |
| **Jazyk**: CHS, přepínací klávesa QWERTY-C/E, SHIFT |synchronizace |× |Jazyk |
| **Jazyk**: CHS, přepínací klávesa QWERTY-C/E, CTRL |synchronizace |× |Jazyk |
| **Jazyk**: CHS WUBI – režim zadávání jedním znakem |synchronizace |× |Jazyk |
| **Jazyk**: CHS WUBI – zobrazení zbývajícího kódování kandidáta |synchronizace |× |Jazyk |
| **Jazyk**: CHS WUBI-ZvukovýSignál, když 4-kódování je neplatné. |synchronizace |× |Jazyk |
| **Jazyk**: CHT Ču-to zahrnuje CJK-A |synchronizace |× |Jazyk |
| **Jazyk**: japonské editor IME – prediktivní psaní a vlastní slova |synchronizace |synchronizace |Jazyk |
| **Language**: Korejština (KOR) IME |X |X |Jazyk |
| **Jazyk**: rozpoznávání rukopisu |X |X |Jazyk |
| **Jazyk**: Profil jazyka |synchronizace |zálohování |Jazyk |
| **Jazyk**: Kontrola pravopisu – automatické opravy a zvýraznění chybných pravopisů |synchronizace |zálohování |Jazyk |
| **Jazyk**: seznam klávesnic |synchronizace |zálohování |Jazyk |
| **Zamykací obrazovka**: všechna nastavení zamykací obrazovky |X |X | |
| **Lupa**: zapnuto nebo vypnuto (přepínač Master) |X |X |Usnadnění přístupu |
| **Lupa**: zapnout nebo vypnout barvu inverze (ve výchozím nastavení vypnuté) |synchronizace |× |Usnadnění přístupu |
| **Lupa**: sledování – Sledujte fokus klávesnice |synchronizace |× |Usnadnění přístupu |
| **Lupa**: sledování – následovat ukazatel myši |synchronizace |× |Usnadnění přístupu |
| **Lupa**: spustit při přihlášení uživatele (ve výchozím nastavení vypnuté) |synchronizace |× |Usnadnění přístupu |
| **Myš**: Změna velikosti kurzoru myši |synchronizace |× |other |
| **Myš**: Změna barvy kurzoru myši |synchronizace |× |other |
| **Myš**: všechna ostatní nastavení |X |X | |
| **Narrator**: Snadné spuštění |synchronizace |× |Usnadnění přístupu |
| **Narrator**: uživatelé můžou měnit rozteč mluvené řeči. |synchronizace |× |Usnadnění přístupu |
| **Narrator**: uživatelé můžou zapnout nebo vypnout čtení tipů pro společné položky (ve výchozím nastavení zapnuté). |synchronizace |× |Usnadnění přístupu |
| **Narrator**: uživatelé můžou zapnout nebo vypnout, jestli můžou slyšet zadané znaky (ve výchozím nastavení zapnuté). |synchronizace |× |Usnadnění přístupu |
| **Narrator**: uživatelé můžou zapnout nebo vypnout, jestli můžou slyšet zadaná slova (ve výchozím nastavení zapnuté). |synchronizace |× |Usnadnění přístupu |
| **Narrator**(Předčítání): mít kurzor pro vložení ve výchozím nastavení. |synchronizace |× |Usnadnění přístupu |
| **Narrator**: povolení vizuálního zvýraznění kurzoru Narrator (ve výchozím nastavení zapnuté) |synchronizace |× |Usnadnění přístupu |
| **Narrator**: Přehrát zvukové signály (ve výchozím nastavení zapnuté) |synchronizace |× |Usnadnění přístupu |
| **Narrator**: aktivace kláves na dotykové klávesnici při zvedání prstu (ve výchozím nastavení vypnuté) |synchronizace |× |Usnadnění přístupu |
| **Usnadnění přístupu**: Nastavte tloušťku blikajícího kurzoru. |synchronizace |× |Usnadnění přístupu |
| **Usnadnění přístupu**: odebrání imagí na pozadí (ve výchozím nastavení vypnuté) |synchronizace |× |Usnadnění přístupu |
| **Napájení a režim spánku**: všechna nastavení |X |X | |
| **Přizpůsobení úvodní obrazovky**: Barva zvýraznění (jenom telefon) |× |synchronizace |Motiv |
| **Psaní**: slovník pravopisu |synchronizace |zálohování |Jazyk |
| **Psaní**: Automatické opravy chybně napsaného slova |synchronizace |zálohování |Jazyk |
| **Psaní**: zvýraznit slova s chybami pravopisu |synchronizace |zálohování |Jazyk |
| **Psaní**: Zobrazit návrhy textu při psaní |synchronizace |zálohování |Jazyk |
| **Psaní**: přidat mezeru po výběru návrhu textu |synchronizace |zálohování |Jazyk |
| **Psaní**: Přidání tečky po dvojitém klepnutí na mezerník |synchronizace |zálohování |Jazyk |
| **Psaní**: velká písmena prvního písmene každé věty |synchronizace |zálohování |Jazyk |
| **Psaní**: při dvojitém kliknutí na klávesu SHIFT používejte všechna velká písmena |synchronizace |zálohování |Jazyk |
| **Psaní**: přehrání klíčových zvuků při psaní |synchronizace |zálohování |Jazyk |
| **Psaní**: data individuálního nastavení pro dotykovou klávesnici |synchronizace |zálohování |Jazyk |
| **Wi-Fi**: profily Wi-Fi (jenom WPA) |synchronizace |synchronizace |Hesla |

###### <a name="footnote-1"></a>Poznámka pod čarou 1

Minimální podporovaná verze operačního systému pro Windows Creators Update (Build 15063). 

## <a name="next-steps"></a>Další kroky

Přehled najdete v tématu [Přehled služby Enterprise State Roaming](enterprise-state-roaming-overview.md).
