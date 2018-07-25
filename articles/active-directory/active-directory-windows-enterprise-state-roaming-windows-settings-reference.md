---
title: Referenční příručka k nastavení roamingu Windows 10 | Dokumentace Microsoftu
description: Úplný seznam všech nastavení, které budou s roamingem nebo zálohování ve Windows 10.
services: active-directory
keywords: roaming, stav Enterprise windows cloud
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 9a06ff345d1bf91efd1abff9960a1a6f9a681dce
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223663"
---
# <a name="windows-10-roaming-settings-reference"></a>Referenční informace k nastavení roamingu pro Windows 10
Následuje úplný seznam všech nastavení, které budou s roamingem nebo zálohování ve Windows 10. 

## <a name="devices-and-endpoints"></a>Zařízení a koncových bodů
Najdete v následující tabulce najdete souhrn zařízení a typy účtů, které jsou podporovány synchronizace, zálohování, a obnovit framework v systému Windows 10.

| Typ účtu a operace | Plocha | Mobilní |
| --- | --- | --- |
| Azure Active Directory: synchronizace |Ano |Ne |
| Azure Active Directory: zálohování a obnovení |Ne |Ne |
| Účet Microsoft: synchronizace |Ano |Ano |
| Účet Microsoft: zálohování a obnovení |Ne |Ano |

## <a name="what-is-backup"></a>Co je zálohování?
Nastavení Windows synchronizovat obecně ve výchozím nastavení, ale některá nastavení jsou pouze zálohovat, jako je například seznam nainstalovaných aplikací na zařízení. Zálohování je pro mobilní zařízení pouze a aktuálně není k dispozici pro uživatele, Enterprise State Roaming. Zálohování pomocí účtu Microsoft a ukládají se nastavení a data aplikací do Onedrivu. Pokud uživatel zakáže synchronizaci v zařízení pomocí nastavení aplikace, data aplikací, které se obvykle synchronizuje se stane zálohování pouze. Data záloh je přístupný pouze prostřednictvím operace obnovení při prvním spuštění prostředí nového zařízení. Zálohování, můžete zakázat prostřednictvím nastavení zařízení a můžete samostatně spravovat a odstranit prostřednictvím účtu OneDrive.

## <a name="windows-settings-overview"></a>Přehled nastavení Windows
Následující nastavení skupiny jsou k dispozici pro koncovým uživatelům povolit nebo zakázat synchronizaci nastavení na zařízení s Windows 10.

* Motiv: na pozadí plochy, dlaždici uživatele, umístění hlavního panelu, atd. 
* Nastavení aplikace Internet Explorer: procházení historie zadané adresy URL, Oblíbené položky, atd. 
* Hesla: [schránku na pověření Windows](https://technet.microsoft.com/library/jj554668.aspx), včetně profilů sítě Wi-Fi 
* Jazykové předvolby: slovníku, nastavení jazyka systému 
* Usnadnění přístupu: Program Předčítání, klávesnice na obrazovce, Lupa 
* Další nastavení Windows: viz podrobnosti o nastavení Windows

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-syncyoursettings.png)

Synchronizace skupiny (oblíbených položek, čtení seznamu) nastavení prohlížeče Microsoft Edge můžete povolit nebo zakázat koncoví uživatelé prostřednictvím prohlížeče Microsoft Edge možnost nabídky nastavení.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-edge.png)

Pro Windows 10 verze 1803 nebo novější, Internet Explorer nastavení skupiny (Oblíbené položky, zadané adresy URL) synchronizaci můžete povolit nebo zakázat koncovými uživateli prostřednictvím možnosti nabídky nastavení aplikace Internet Explorer. 

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Podrobnosti o nastavení Windows
V následující tabulce, ostatní položky ve sloupci skupina nastavení odkazuje na nastavení, která se dají zakázat tak, že přejdete na Nastavení > účty > synchronizovat nastavení > Nastavení ostatní Windows. 

Vnitřní položky ve sloupci skupina nastavení naleznete nastavení a aplikace, které se dá deaktivovat jenom synchronizaci v aplikaci nebo vypnutí synchronizace pro celé zařízení pomocí správy mobilních zařízení (MDM) nebo nastavení zásad skupiny.
Nastavení, která není roaming nebo synchronizace nebude patřit do skupiny.

| Nastavení | Plocha | Mobilní | Skupina |
| --- | --- | --- | --- |
| **Účty**: obrázek účtu |Synchronizace |X |Motiv |
| **Účty**: Další nastavení účtu |X |X | |
| **Pokročilé mobilního širokopásmového připojení**: sdílení (umožňuje automatické zjišťování mobilní sítě Wi-Fi hotspotům přes Bluetooth) název síťového připojení k Internetu |X |X |Hesla |
| **Data aplikací**: jednotlivými aplikacemi můžete synchronizovat data |synchronizace zálohování |synchronizace zálohování |Interní |
| **Seznam aplikací**: seznam nainstalovaných aplikací |X |zálohování |Ostatní |
| **Bluetooth**: všechna nastavení Bluetooth |X |X | |
| **Příkazový řádek**: "Výchozí" nastavení příkazového řádku |Synchronizace |X | |
| **Přihlašovací údaje**: přihlašovacích údajů schránky |Synchronizace |Synchronizace |heslo |
| **Datum, čas a oblasti**: Automatický čas (Internet čas synchronizace) |Synchronizace |Synchronizace |Jazyk |
| **Datum, čas a oblasti**: 24 hodin |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: datum a čas |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: časové pásmo | |X |Jazyk |
| **Datum, čas a oblasti**: letní čas |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: země/oblast |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: první den v týdnu |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: oblast formát (národní prostředí) |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: krátké datum |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: louhý formát data |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: krátkého formátu času |Synchronizace |X |Jazyk |
| **Datum, čas a oblasti**: dlouhý čas |Synchronizace |X |Jazyk |
| **Přizpůsobení plochy**: motiv plochy (na pozadí, systémovou barvou, výchozí systémové zvuky, spořič obrazovky) |Synchronizace |X |Motiv |
| **Přizpůsobení plochy**: prezentace tapety |Synchronizace |X |Motiv |
| **Přizpůsobení plochy**: nastavení hlavního panelu (pozice, automatického schovávání atd.) |Synchronizace |X |Motiv |
| **Přizpůsobení plochy**: rozložení obrazovky start |X |zálohování | |
| **Zařízení**: sdílené tiskárny, které jste se připojili k |X |X |ostatní |
| **Prohlížeč Microsoft Edge**: čtení seznamu |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: oblíbených položek |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: top lokality <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: zadané adresy URL <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: oblíbených položek panelu nastavení <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: Zobrazit tlačítko Domů <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: blokovat automaticky otevíraná okna <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: dotaz na co dělat s každého stažení <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: nabízejí pro ukládání hesel <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: odeslání není sledování žádostí o <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: uložit formuláře položky <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: zobrazovat návrhy vyhledávání a lokality během zadávání <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: soubory cookie předvoleb <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: nechat lokality uložit licence chráněné médií v zařízení <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Prohlížeč Microsoft Edge**: čtečky obrazovky nastavení <sup> [[1]](#footnote-1)</sup> |Synchronizace |Synchronizace |Interní |
| **Vysoký kontrast –**: zapnutí nebo vypnutí |Synchronizace |X |usnadnění přístupu |
| **Vysoký kontrast –**: nastavení motiv |Synchronizace |X |usnadnění přístupu |
| **Aplikace Internet Explorer**: otevřete karty (adresu URL a název) |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: čtení seznamu |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: zadané adresy URL |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: historie procházení |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: Oblíbené položky |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: vyloučené adresy URL |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: domovské stránky |Synchronizace |Synchronizace |Internet Explorer |
| **Aplikace Internet Explorer**: domény návrhy |Synchronizace |Synchronizace |Internet Explorer |
| **Klávesnice**: uživatelé můžou zapnout/vypnout program klávesnice na obrazovce |Synchronizace |X |usnadnění přístupu |
| **Klávesnice**: zapněte vždy navrchu Ano (ve výchozím nastavení vypnuté) |Synchronizace |X |usnadnění přístupu |
| **Klávesnice**: Zapnutí filtru klíče (ve výchozím nastavení vypnuté) |Synchronizace |X |usnadnění přístupu |
| **Klávesnice**: zapněte přepínací tlačítko klíče (ve výchozím nastavení vypnuté) |Synchronizace |X |usnadnění přístupu |
| **Aplikace Internet Explorer**: domény jazyka: čínština (CHS) QWERTY - povolit samoobslužné učení |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY - povolit dynamické Release candidate hodnocení |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY - char-set zjednodušená čínština |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY - char-set tradiční čínština |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY - jin přibližných shod |Synchronizace |zálohování |Jazyk |
| **Jazyk**: CHS QWERTY - přibližných shod páry |Synchronizace |zálohování |Jazyk |
| **Jazyk**: CHS QWERTY - jin úplné |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY - jin double |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY – čtení automatickou opravu |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY – klíč přepínače C/E, shift |Synchronizace |X |Jazyk |
| **Jazyk**: CHS QWERTY - key přepínače C/E, Ctrl |Synchronizace |X |Jazyk |
| **Jazyk**: CHS WUBI – vstupní režim jednoho znaku |Synchronizace |X |Jazyk |
| **Jazyk**: CHS WUBI - zobrazit zbývající kódování zájemce |Synchronizace |X |Jazyk |
| **Jazyk**: CHS WUBI – zvukový signál při psaní kódu 4 je neplatný |Synchronizace |X |Jazyk |
| **Jazyk**: CHT Ču – zahrnout CJK A ext, přípona |Synchronizace |X |Jazyk |
| **Jazyk**: editoru IME pro japonštinu – prediktivní psaní a vlastních slov |Synchronizace |Synchronizace |Jazyk |
| **Jazyk**: korejština (KOR) as |X |X |Jazyk |
| **Jazyk**: rozpoznávání textu psaného rukou |X |X |Jazyk |
| **Jazyk**: profil jazyka |Synchronizace |zálohování |Jazyk |
| **Jazyk**: Kontrola pravopisu – automatické opravy a zvýraznění pravopisné chyby |Synchronizace |zálohování |Jazyk |
| **Jazyk**: seznam klávesnice |Synchronizace |zálohování |Jazyk |
| **Zamknout obrazovku**: všechny zamknutí obrazovky nastavení |X |X | |
| **Lupa**: zapnutí nebo vypnutí (hlavní přepnutí) |X |X |Usnadnění přístupu |
| **Lupa**: zapněte nebo vypněte inverzi barev (ve výchozím nastavení vypnuté) |Synchronizace |X |Usnadnění přístupu |
| **Lupa**: sledování – postupujte podle fokus klávesnice |Synchronizace |X |Usnadnění přístupu |
| **Lupa**: sledování - ukazatele myši |Synchronizace |X |Usnadnění přístupu |
| **Lupa**: spuštění při přihlášení uživatele (ve výchozím nastavení vypnuté) |Synchronizace |X |Usnadnění přístupu |
| **Myši**: Změna velikosti ukazatele myši |Synchronizace |X |ostatní |
| **Myši**: Změna barvy kurzoru myši |Synchronizace |X |ostatní |
| **Myši**: všechna ostatní nastavení |X |X | |
| **Program Předčítání**: snadného spuštění |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: uživatelé mohou změnit program Předčítání mluvený od |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: uživatelé můžou zapnout nebo vypnout program Předčítání čtení pomocných parametrů pro společné položky (na ve výchozím nastavení) |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: uživatelé můžou zapnout nebo vypnout, jestli jsou slyšet znakům (na ve výchozím nastavení) |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: uživatelé můžou zapnout nebo vypnout Určuje, zda lze poslouchat typu slova (na ve výchozím nastavení) |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: mít vložit kurzor po Předčítání (na ve výchozím nastavení) |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: Povolit visual zvýraznění Předčítání kurzoru (na ve výchozím nastavení) |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: přehrávání zvuku pomůcky (na ve výchozím nastavení) |Synchronizace |X |Usnadnění přístupu |
| **Program Předčítání**: Aktivace klávesy na klávesnici pro dotykové ovládání, pokud lift prstu (ve výchozím nastavení vypnuté) |Synchronizace |X |Usnadnění přístupu |
| **Usnadnění přístupu**: Nastavte tloušťku pozici blikajícího kurzoru |Synchronizace |X |Usnadnění přístupu |
| **Usnadnění přístupu**: Odeberte obrázky na pozadí (ve výchozím nastavení vypnuté) |Synchronizace |X |Usnadnění přístupu |
| **Napájení a režimu spánku**: všechna nastavení |X |X | |
| **Přizpůsobení obrazovky Start**: zvýraznění barvy (pouze telefonní) |X |Synchronizace |Motiv |
| **Zadáním**: slovníku |Synchronizace |zálohování |Jazyk |
| **Zadáním**: Automatické opravy hledaná slova |Synchronizace |zálohování |Jazyk |
| **Zadáním**: zvýrazňovat slova s překlepem |Synchronizace |zálohování |Jazyk |
| **Zadáním**: zobrazovat návrhy textu při psaní |Synchronizace |zálohování |Jazyk |
| **Zadáním**: přidejte mezeru po mám zvolit návrh textu |Synchronizace |zálohování |Jazyk |
| **Zadáním**: přidejte tečku po můžu dvojitého klepnutí MEZERNÍK |Synchronizace |zálohování |Jazyk |
| **Zadáním**: velké první písmeno první písmeno každého věty. |Synchronizace |zálohování |Jazyk |
| **Zadáním**: používejte všechna velká písmena I dvojitého klepnutí klávesy shift |Synchronizace |zálohování |Jazyk |
| **Zadáním**: přehrávání zvuků klíče při psaní |Synchronizace |zálohování |Jazyk |
| **Zadáním**: data individuálního nastavení pro dotykové klávesnice |Synchronizace |zálohování |Jazyk |
| **Wi-Fi**: profilů sítě Wi-Fi (pouze WPA) |Synchronizace |Synchronizace |Hesla |

###### <a name="footnote-1"></a>Poznámka pod čarou 1
Minimální podporovaná verze operačního systému Windows Creators Update (sestavení 15063). 

## <a name="related-topics"></a>Související témata
* [Cestovní přehled stavu Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
* [Povolit enterprise state roaming v Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Nastavení a datovému roamingu – nejčastější dotazy](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Skupina zásad a nastavení MDM pro nastavení synchronizace](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Řešení potíží](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
