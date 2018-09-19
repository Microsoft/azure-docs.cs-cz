---
title: Řešení potíží s objekt, který se nesynchronizuje do Azure AD | Dokumentace Microsoftu
description: Řešit potíže způsobující objektu se nesynchronizuje do Azure AD.
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
ms.date: 08/10/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b66aeb0832058c56e63c56c0420c7793eb2a632a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312421"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Řešení potíží s objekt, který se nesynchronizuje do Azure AD

Pokud objekt nesynchronizuje podle očekávání do služby Azure AD, může být z několika důvodů. Pokud obdržíte chyba e-mailu od Azure AD nebo pokud se zobrazí chyba ve službě Azure AD Connect Health, pak si můžete přečíst [řešení potíží s chybami export](tshoot-connect-sync-errors.md) místo. Ale Pokud řešíte potíže, pokud objekt není ve službě Azure AD, pak toto téma je za vás. Popisuje, jak najít chyby v synchronizace Azure AD Connect místní komponenty.

>[!IMPORTANT]
>Pro Azure Active Directory (AAD) Connect nasazení s verzí 1.1.749.0 nebo vyšší, použít [úlohy řešení potíží s](tshoot-connect-objectsync.md) v průvodci k řešení potíží se synchronizací objektu. 

Najít chyby, kterou budete podívejte se na několik různých místech v následujícím pořadí:

1. [Protokoly operací](#operations) pro vyhledání chyby identifikované synchronizační modul během importu a synchronizace.
2. [Prostoru konektoru](#connector-space-object-properties) pro vyhledání chybějící objekty a chyb synchronizace.
3. [Metaverse](#metaverse-object-properties) pro vyhledání problémů souvisejících s daty.

Spustit [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) před zahájením těchto kroků.

## <a name="operations"></a>Operace
Na kartě provoz Synchronization Service Manager je, kde byste měli začít s odstraňováním potíží. Operace karta zobrazuje výsledky z nejnovější operace.  
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/operations.png)  

V horní polovině zobrazuje všechna spuštění v chronologickém pořadí. Ve výchozím nastavení, operace přihlášení udržuje informace o posledních sedmi dnů, ale toto nastavení lze změnit pomocí [Plánovač](how-to-connect-sync-feature-scheduler.md). Chcete hledat jakékoli spuštění, který není uveden stav úspěšného dokončení. Můžete změnit kliknutím na záhlaví při řazení.

**Stav** sloupec je nejdůležitější informace a v něm nejzávažnějších problémů pro spuštění. Tady je stručný přehled nejběžnějších stavů v pořadí podle priority k prozkoumání (kde * označení několik možných chybové řetězce).

| Status | Poznámka |
| --- | --- |
| zastavené-* |Spuštění se nepovedlo dokončit. Například, pokud vzdálený systém je vypnutý a nelze kontaktovat. |
| Zastavit omezení chyb |Existuje více než 5 000 chyby. Spustit automaticky zastavila z důvodu velkého počtu chyb. |
| dokončené -\*– chyby |Dokončení běhu, ale nejsou chyby (méně než 5 000), které by mělo být vypátráno. |
| dokončené -\*– upozornění |Běh dokončen, ale některá data není v očekávaném stavu. Pokud už máte chyby, pak tato zpráva je obvykle pouze příznakem. Až vyřešíte chyby by neměl prozkoumat upozornění. |
| úspěch |Žádné problémy. |

Při výběru řádku dolní části aktualizuje zobrazíte podrobnosti o spuštění. Úplně vlevo dole, můžete mít přehled o tom, že **krok #**. Tento seznam se zobrazí, jenom Pokud máte více domén v doménové struktuře kde každou doménu představuje krok. Název domény najdete v části **oddílu**. V části **statistické údaje o synchronizaci**, najdete další informace o počtu změn, které byly zpracovány. Můžete kliknout na odkazy na získání seznamu změněné objekty. Pokud máte objektů s chybami, tyto chyby se zobrazí v části **chyby synchronizace**.

### <a name="troubleshoot-errors-in-operations-tab"></a>Řešení potíží s chybami v kartu operace
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Až budete mít chyby, objekt v chybě a vlastní chyba jsou odkazy, které poskytují další informace.

Začněte tím, že kliknete na řetězec chyby (**synchronizace pravidlo Chyba – funkce – aktivuje** na obrázku). Nejprve se zobrazí přehled o objektu. Pokud chcete zobrazit aktuální chyby, klikněte na tlačítko **trasování zásobníku**. Trasování poskytuje informace o úrovni ladění k chybě.

Klepnutí pravým tlačítkem myši v **informace v zásobníku volání** zvolte **Vybrat vše**, a **kopírování**. Pak můžete zkopírovat do zásobníku a podívejte se na chyby ve svém oblíbeném editoru, jako je například Poznámkový blok.

* Pokud je chyba z **SyncRulesEngine**, informace v zásobníku volání nejprve má seznam všech atributů objektu. Posuňte se dolů, dokud se nezobrazí záhlaví **u třídy InnerException = >**.  
  ![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
  Řádek po zobrazí chybu. Na obrázku výše chyba je z vlastního Fabrikam pravidlo synchronizace vytvoří.

Pokud vlastní chyba neposkytuje dostatek informací, je čas podívat se na vlastní data. Můžete kliknout na odkaz s identifikátorem objektu a odstraňování potíží [importovaný objekt prostoru konektoru](#cs-import).

## <a name="connector-space-object-properties"></a>Vlastnosti objektu prostoru konektoru
Pokud nemáte žádné chyby nalezené v [operace](#operations) kartu, dalším krokem je sledovat objekt prostoru konektoru služby Active Directory, do úložiště metaverse a do služby Azure AD. V této cestě měli byste najít, čem problém spočívá.

### <a name="search-for-an-object-in-the-cs"></a>Vyhledání objektu v CS

V **Synchronization Service Manager**, klikněte na tlačítko **konektory**, vyberte konektoru služby Active Directory, a **Search Connector Space**.

V **oboru**vyberte **relativní rozlišující** (Pokud chcete hledat v atributu CN) nebo **rozlišující název nebo ukotvení** (Pokud chcete vyhledat na atribut distinguishedName). Zadejte hodnotu a klikněte na tlačítko **hledání**.  
![Hledání prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Pokud nenajdete objekt jste hledali, pak ho může jsou filtrované s [filtrování podle domén](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nebo [filtrování podle organizační jednotky](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Přečtěte si [konfigurace filtrování](how-to-connect-sync-configure-filtering.md) tématu a ověřte, že filtrování je nakonfigurován podle očekávání.

Další užitečná hledání je konektor služby Azure AD, vyberte v **oboru** vyberte **čekajícího importu**a vyberte **přidat** zaškrtávací políčko. Toto vyhledávání poskytuje všechny synchronizované objekty ve službě Azure AD, který nemůže být přidružena místní objekt.  
![Oddělena hledání prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Tyto objekty se vytvořila další synchronizační modul nebo synchronizační modul s jinou konfiguraci filtrování. Toto zobrazení je uveden seznam **oddělena** už není spravované objekty. Je vhodné zkontrolovat tohoto seznamu a zvažte odebrání těchto objektů pomocí [Azure AD PowerShell](https://aka.ms/aadposh) rutiny.

### <a name="cs-import"></a>CS Import
Při otevření objektu cs existuje několik karet v horní části. **Importovat** karta zobrazuje data, která jsou určená po importu.  
![Objekt prostoru Konektoru](./media/tshoot-connect-object-not-syncing/csobject.png)    
**Stará hodnota** ukazuje, co je aktuálně uložené ve službě Connect a **novou hodnotu** co byla přijata ze zdrojového systému a ještě nebyla použita. Pokud dojde k chybě v objektu, nebudou zpracovány změny.

**Chyba**  
![Objekt prostoru Konektoru](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
**Chyba synchronizace** karta je viditelné pouze pokud je nějaký problém s objektem. Další informace najdete v tématu [řešení potíží s chybami synchronizace](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS rodokmenu
Na kartě rodokmenu ukazuje, jak objekt prostoru konektoru má vztah k objektu úložiště metaverse. Zobrazí se importu konektoru poslední změny z připojený systém a které pravidla používají k naplnění dat v úložišti metaverse.  
![CS rodokmenu](./media/tshoot-connect-object-not-syncing/cslineage.png)  
V **akce** sloupec, zobrazí se odebrat **příchozí** synchronizační pravidlo s akcí **zřízení**. Který označuje, dokud tento objekt prostoru konektoru je k dispozici, objektu úložiště metaverse zůstane. Pokud seznam pravidel synchronizace místo toho zobrazí pravidlo synchronizace se směrem **odchozí** a **zřízení**, znamená to, že tento objekt se odstraní při odstranění objektu úložiště metaverse.  
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Můžete také uvidíte **PasswordSync** sloupec, který může přispívat v prostoru konektoru příchozí změny hesla od jednoho pravidla synchronizace má hodnotu **True**. Toto heslo se pak posílají do Azure AD prostřednictvím odchozí pravidla.

Na kartě rodokmenu můžete získat do úložiště metaverse kliknutím [vlastnosti objektu úložiště Metaverse](#mv-attributes).

V dolní části všechny karty se nacházejí dvě tlačítka: **ve verzi Preview** a **protokolu**.

### <a name="preview"></a>Preview
Na stránce ve verzi preview se používá k synchronizaci jeden jeden objekt. To je užitečné, pokud řešíte některá vlastní synchronizační pravidla a chcete posoudit účinek parametru změnu u jednoho objektu. Můžete vybrat mezi **Full sync** a **rozdílová synchronizace**. Můžete také vybrat mezi **generovat ve verzi Preview**, který pouze sleduje změny v paměti, a **potvrzení změn ve verzi Preview**, který aktualizovat úložiště metaverse a zpracuje všechny změny prostor konektoru cíl.  
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/preview.png)  
Můžete si prohlédnout objekt a které pravidlo použito pro konkrétní atribut tok.  
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Protokol
Na stránce protokol umožňuje zobrazit stav synchronizace hesla a historie. Další informace najdete v tématu [řešit synchronizaci hodnot hash hesel](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Vlastnosti objektu úložiště Metaverse
Je obvykle vhodnější spustit vyhledávání od zdroje služby Active Directory [prostoru konektoru](#connector-space). Ale můžete také spustit vyhledávání od úložiště metaverse.

### <a name="search-for-an-object-in-the-mv"></a>Vyhledání objektu v MV
V **Synchronization Service Manager**, klikněte na tlačítko **vyhledávání Metaverse**. Vytvořte dotaz, které už znáte, vyhledá uživatele. Můžete vyhledat společné atributy, jako je například accountName (sAMAccountName) ani atribut userPrincipalName. Další informace najdete v tématu [vyhledávání Metaverse](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

V **výsledky hledání** okna, klikněte na objekt.

Pokud jste se nenašel objekt, pak jej zatím nedosáhly úložiště metaverse. Pokračujte ve vyhledávání u objektu ve službě Active Directory [prostoru konektoru](#connector-space-object-properties). Může dojít k chybě synchronizace, která blokuje objektu z přicházejících do úložiště metaverse nebo může být použit filtr.

### <a name="mv-attributes"></a>Atributy MV
Na kartě atributy hodnoty se zobrazí a které konektor přispět ho.  
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Pokud se objekt nesynchronizuje, podívejte se na následující atributy v úložišti metaverse:
- Je atribut **cloudFiltered** přítomny a nastaveny **true**? Pokud je pak byly filtrovány podle kroků v [atributů na základě filtrování](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Je atribut **sourceAnchor** k dispozici? Pokud ne, máte k dispozici doménovou strukturu prostředků účtu? Pokud objekt je označen jako propojená poštovní schránka (atribut **msExchRecipientTypeDetails** má hodnotu 2), pak sourceAnchor je přispěla doménová struktura s povolený účet služby Active Directory. Ujistěte se, že hlavní účet byl importován a správně synchronizovat. Hlavní účet musí být uvedené v [konektory](#mv-connectors) pro objekt.

### <a name="mv-connectors"></a>Konektory MV
Konektory karta zobrazuje všechny mezery konektoru, které mají reprezentaci objektu.  
![Správce synchronizace služby](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
Konektor, měli byste mít:

- Každé doménové struktuře služby Active Directory uživatele je vyjádřena v. Tento zápis mohou zahrnovat foreignSecurityPrincipals a kontakt objekty.
- Konektor ve službě Azure AD.

Pokud vám chybí konektor ke službě Azure AD, pak si můžete přečíst [MV atributy](#mv-attributes) ověřit kritéria pro zřizuje do služby Azure AD.

Na této kartě můžete také přejít na [objekt prostoru konektoru](#connector-space-object-properties). Vyberte řádek a klikněte na tlačítko **vlastnosti**.

## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
