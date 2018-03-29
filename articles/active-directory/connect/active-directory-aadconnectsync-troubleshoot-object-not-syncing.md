---
title: Řešení potíží s objekt, který není synchronizované s Azure AD | Microsoft Docs
description: Poradce při potížích se objekt není synchronizované s Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 05b60b091fe87620c88ad9eb761f1028c24d5730
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Řešení potíží s objekt, který není synchronizované s Azure AD

Pokud objekt nesynchronizuje podle očekávání do služby Azure AD, může být z několika důvodů. Pokud obdržíte e-mailu chyba z Azure AD nebo se zobrazí chyba v Azure AD Connect Health, přečtěte si [řešení chyb export](active-directory-aadconnect-troubleshoot-sync-errors.md) místo. Ale Pokud řešíte problém, kde se objekt se nenachází ve službě Azure AD, pak toto téma je pro vás. Popisuje hledání chyb v synchronizaci místní součást Azure AD Connect.

>[!IMPORTANT]
>Pro nasazení připojení Azure Active Directory (AAD) s verzí <verison> nebo vyšší, použijte [řešení potíží s úloh](active-directory-aadconnect-troubleshoot-objectsync.md) v průvodci k řešení potíží objekt synchronizace. 

Chyby najdete chcete podívat na několika různých místech v následujícím pořadí:

1. [Protokoly operací](#operations) pro vyhledání chyby identifikovaný synchronizační modul během importu a synchronizaci.
2. [Prostoru konektoru](#connector-space-object-properties) pro hledání chybějící objekty a chyby synchronizace.
3. [Úložiště metaverse](#metaverse-object-properties) pro vyhledání problémů souvisejících s daty.

Spustit [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) předtím, než začnete provádět tyto kroky.

## <a name="operations"></a>Operace
Karta operace v Synchronization Service Manager je, kde byste měli začít, vaše řešení potíží. Na kartě operations zobrazuje výsledky z nejnovější operace.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

V horní polovině ukazuje všechny běží v chronologickém pořadí. Ve výchozím nastavení, operace protokolu udržuje informace o posledních sedmi dnů, ale toto nastavení lze změnit pomocí [Plánovač](active-directory-aadconnectsync-feature-scheduler.md). Chcete-li vyhledat všechny spuštění, které nejsou zobrazeny stav úspěšného dokončení. Řazení kliknutím na záhlaví, můžete změnit.

**Stav** je nejdůležitější informace a ukazuje nejzávažnějšího problém pro spuštění. Zde je stručný nejběžnější stavů v pořadí podle priority prozkoumat (kde * znamenat několik řetězců možná chyba).

| Stav | Komentář |
| --- | --- |
| ukončeno-* |Spuštění se nepodařilo dokončit. Například pokud vzdálený systém je vypnutý a nelze kontaktovat. |
| stopped-error-limit |Existuje více než 5 000 chyby. Spustit automaticky zastavila z důvodu velkého počtu chyb. |
| dokončené -\*– chyby |Spustit dokončilo, ale nejsou chyby (méně než 5 000), které by se měly prozkoumat. |
| dokončené -\*– upozornění |Spustit dokončena, ale některá data, není v očekávaném stavu. Pokud máte chyby, pak tato zpráva je obvykle jenom příznakem. Dokud nebude mít řešit chyby, by neměl prozkoumat upozornění. |
| úspěch |Žádné problémy. |

Když vyberete řádek, aktualizuje dolní zobrazit podrobnosti, které používají. Na levém okraji dolní, můžete mít tom, že seznam **krok #**. Tento seznam se zobrazí, jenom Pokud máte více domén v doménové struktuře každou doménu, kde je reprezentována krok. Název domény naleznete v části **oddílu**. V části **Statistika synchronizace**, můžete najít další informace o počtu změn, které byly zpracovány. Můžete kliknutím na odkazy získat seznam změněných objektů. Pokud máte objektů s chybami, tyto chyby, zobrazí na **chyby synchronizace**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Řešení chyb v kartu operace
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
Až budete mít chyby, jsou objekt v chyba a vlastní chyba odkazy, které poskytují další informace.

Začněte tím, že kliknete na řetězec chyby (**synchronizační pravidlo Chyba – funkce aktivované** na obrázku). Nejprve se zobrazí přehled objektu. Chcete-li zobrazit aktuální chyby, klikněte na tlačítko **trasování zásobníku**. Trasování poskytuje informace o úrovni ladění chyby.

Kliknete pravým tlačítkem na v **informace v zásobníku volání** vyberte **Vybrat vše**, a **kopie**. Pak můžete zkopírovat zásobníku a podívejte se na chyby ve svém oblíbeném editoru, například Poznámkový blok.

* Pokud je chyba z **SyncRulesEngine**, pak informace zásobníku volání jako první má seznam všech atributů objektu. Posuňte se dolů, dokud neuvidíte záhlaví **ve vlastnosti InnerException = >**.  
  ![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  Řádek po zobrazí chyba. Na obrázku výše chyba je z vlastní Fabrikam synchronizační pravidlo vytvořit.

Pokud vlastní chyba není poskytnuta dostatek informací, je čas se podívat na samotná data. Můžete kliknutím na odkaz s identifikátor objektu a pokračovat v odstraňování potíží [konektoru místo importovaný objekt](#cs-import).

## <a name="connector-space-object-properties"></a>Vlastnosti objektu prostoru konektoru
Pokud nemáte žádné chyby nalezené ve [operations](#operations) kartě, pak v dalším kroku se podle objektu prostoru konektoru služby Active Directory, do úložiště metaverse a do Azure AD. V této cestě by měl zjistit, kde je problém.

### <a name="search-for-an-object-in-the-cs"></a>Vyhledejte objekt v CS

V **Synchronization Service Manager**, klikněte na tlačítko **konektory**, vyberte konektor služby Active Directory a **hledání prostoru konektoru**.

V **oboru**, vyberte **relativního Rozlišujícího** (Pokud chcete hledat na atribut CN) nebo **rozlišující název nebo ukotvení** (Pokud chcete hledat na atribut distinguishedName). Zadejte hodnotu a klikněte na tlačítko **vyhledávání**.  
![Hledání prostoru konektoru](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

Pokud nenajdete objekt hledáte, pak se může mít filtrované s [filtrování podle domén](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) nebo [založené na organizační jednotku filtrování](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). Pro čtení [konfigurace filtrování](active-directory-aadconnectsync-configure-filtering.md) tématu, ověřte, že je nakonfigurováno filtrování podle očekávání.

Další užitečné hledání je konektor služby Azure AD, vyberte v **oboru** vyberte **čekajícího importu**a vyberte **přidat** zaškrtávací políčko. Toto hledání získáte všechny synchronizované objekty ve službě Azure AD, která nemůže být přidružena objektu místní.  
![Oddělena hledání prostoru konektoru](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
Tyto objekty byla vytvořena jinou synchronizační modul nebo synchronizační modul s jinou konfiguraci filtrování. Toto zobrazení je seznam **oddělena** už není spravované objekty. Zkontrolujte tento seznam a zvažte odebrání těchto objektů s použitím [Azure AD PowerShell](https://aka.ms/aadposh) rutiny.

### <a name="cs-import"></a>CS Import
Při otevření objekt cs existuje několik karet v horní části. **Importovat** kartě se zobrazují data, která je vynášené po importu.  
![CS objektu](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
**Stará hodnota** znázorňuje, co je aktuálně uloženy v připojení a **nová hodnota** co byla přijata ze zdrojového systému a nebyla dosud nainstalována. Pokud dojde k chybě v objektu, nejsou zpracovány změny.

**Chyba**  
![CS objektu](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
**Chyba synchronizace** karta je zobrazeno, pokud došlo k potížím s daným objektem. Další informace najdete v tématu [řešení chyb synchronizace](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS rodokmenu
Na kartě rodokmenu ukazuje, jak objektu prostoru konektoru souvisí s objektu úložiště metaverse. Zobrazí se při změně konektor poslední importu z připojený systém a které pravidla používají k naplnění dat v úložišti metaverse.  
![CS rodokmenu](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
V **akce** sloupce, uvidíte je jedna **příchozí** synchronizační pravidlo s akcí **zřídit**. Který označuje, že tak dlouho, dokud tento objekt místa konektoru je k dispozici, objektu úložiště metaverse zůstane. Pokud v seznamu pravidel synchronizace místo toho jsou synchronizační pravidlo s směr **odchozí** a **zřídit**, znamená to, že tento objekt se odstraní při odstranění objektu úložiště metaverse.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
Můžete také zjistit v **PasswordSync** sloupec, který můžete přispívat prostoru konektoru příchozí změny hesla, protože jedno pravidlo synchronizace má hodnotu **True**. Toto heslo se pak posílají do Azure AD prostřednictvím odchozí pravidlo.

Na kartě rodokmenu můžete získat do úložiště metaverse kliknutím [vlastnosti objektu úložiště Metaverse](#mv-attributes).

V dolní části všechny karty jsou dvě tlačítka: **Preview** a **protokolu**.

### <a name="preview"></a>Předběžné
Stránku s náhledem se používá k synchronizaci jeden jednoho objektu. Je vhodné, pokud jsou některá vlastní synchronizační pravidla pro řešení potíží a chcete projevily změny na jednoho objektu. Můžete vybrat mezi **úplné synchronizace** a **rozdílová synchronizace**. Můžete také vybrat mezi **generovat Preview**, který pouze změny uchová paměti, a **potvrdit Preview**, který aktualizovat úložiště metaverse a zpracuje všechny změny do prostor konektoru cíl.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
Si můžete prohlédnout objekt a pravidlo, které se použijí pro tok konkrétní atribut.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>Protokol
Na stránce protokol se používá ke zjištění stavu synchronizace hesla a historie. Další informace najdete v tématu [řešení potíží s synchronizaci hodnoty hash hesla](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Vlastnosti objektu úložiště Metaverse
Je obvykle lepší zahájeno hledání ze zdroje služby Active Directory [prostoru konektoru](#connector-space). Ale můžete také spustit vyhledávání z úložiště metaverse.

### <a name="search-for-an-object-in-the-mv"></a>Vyhledejte objekt v MV
V **Synchronization Service Manager**, klikněte na tlačítko **hledání úložiště Metaverse**. Vytvořte dotaz, které znáte Vyhledá uživatele. Můžete vyhledat běžné atributy, jako například název účtu (sAMAccountName) a userPrincipalName. Další informace najdete v tématu [hledání úložiště Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md).
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

V **výsledky hledání** okně klikněte na objekt.

Pokud nebyla nalezena objektu, bylo nebyl ještě dosaženo úložiště metaverse. Pokračovat ve vyhledávání objektu ve službě Active Directory [prostoru konektoru](#connector-space-object-properties). Může dojít k chybě synchronizace, která blokuje objekt z přicházející do úložiště metaverse nebo může být použit filtr.

### <a name="mv-attributes"></a>Atributy MV
Na kartě atributy lze zobrazit hodnoty a které konektor podílí ho.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

Pokud není objekt synchronizace, podívejte se na následující atributy v úložišti metaverse:
- Je atribut **cloudFiltered** k dispozici a nastavte na **true**? Pokud je, pak filtrované podle kroků v [atributů na základě filtrování](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering).
- Je atribut **sourceAnchor** přítomen? Pokud ne, máte topologie doménové struktury prostředků účtu? Pokud se objekt identifikuje jako propojená poštovní schránka (atribut **msExchRecipientTypeDetails** má hodnotu 2), pak sourceAnchor je přispěli doménová struktura s aktivovaný účet služby Active Directory. Zajistěte, aby hlavní účet byl naimportovány a synchronizovány správně. Hlavní účet musí být uvedený v [konektory](#mv-connectors) pro objekt.

### <a name="mv-connectors"></a>Konektory MV
Konektory kartě se zobrazují všechny prostor konektoru, které mají reprezentaci objektu.  
![Správce synchronizace služby](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
Měli byste konektor pro:

- Každé doménové struktuře služby Active Directory uživatele je znázorněná. Tento zápis mohou zahrnovat foreignSecurityPrincipals a obraťte se na objekty.
- Konektor ve službě Azure AD.

Pokud chybí konektor služby Azure AD, přečtěte si [MV atributy](#MV-attributes) ověření kritéria pro se zřídí do služby Azure AD.

Na této kartě můžete také přejít k [objektu prostoru konektoru](#connector-space-object-properties). Vyberte řádek a klikněte na tlačítko **vlastnosti**.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
