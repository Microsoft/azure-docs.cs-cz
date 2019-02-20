---
title: Řešení potíží s objekt, který se synchronizuje se službou Azure Active Directory | Dokumentace Microsoftu
description: Řešení potíží s objekt, který se synchronizuje se službou Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416917"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Řešení potíží s objekt, který se nesynchronizuje se službou Azure Active Directory

Pokud objekt nesynchronizuje podle očekávání s Microsoft Azure Active Directory (Azure AD), může být z několika důvodů. Pokud obdržíte chyba e-mailu od Azure AD nebo se zobrazí chyba ve službě Azure AD Connect Health, přečtěte si [řešení chyb při synchronizaci](tshoot-connect-sync-errors.md) místo. Ale Pokud řešíte potíže, pokud objekt není ve službě Azure AD, tento článek právě pro vás. Popisuje, jak najít chyby v místní komponenty synchronizace Azure AD Connect.

>[!IMPORTANT]
>Pro službu Azure AD Connect nasazení s verzí 1.1.749.0 nebo vyšší, použít [úlohy řešení potíží s](tshoot-connect-objectsync.md) v průvodci k řešení potíží s objekt synchronizace problémy. 

## <a name="synchronization-process"></a>Procesu synchronizace

Před prozkoumáme synchronizaci problémy, Pojďme se na Azure AD Connect synchronizuje procesu:

  ![Diagram procesu synchronizace Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Prostor konektoru, tabulku v databázi
* **MV:** Úložiště Metaverse tabulky v databázi

### <a name="synchronization-steps"></a>**Koky synchronizace**
Procesu synchronizace zahrnuje následující kroky:

1. **Import ze služby AD:** Objekty služby Active Directory jsou zařazení do systému Active Directory CS.

2. **Import ze služby Azure AD:** Azure AD objekty jsou zařazení do systému Azure AD CS.

3. **Synchronizace:** Pravidla synchronizace příchozích dat a pravidla odchozí synchronizace jsou spuštěny v pořadí podle priority číslo, od nižší, a umožňují vyšší. Chcete-li zobrazit synchronizační pravidla, přejděte na Editor pravidel synchronizace z desktopové aplikace. Pravidla synchronizace příchozích dat přenést data z CS k MV. Pravidla odchozí synchronizace přesun dat z MV ke službě CS.

4. **Export do AD:** Po synchronizaci, jsou objekty z Active Directory CS exportovat do služby Active Directory.

5. **Export do služby Azure AD:** Po synchronizaci, jsou objekty z Azure AD CS exportovat do služby Azure AD.

## <a name="troubleshooting"></a>Řešení potíží

Pokud chcete najít chyby, podívejte se na několik různých místech v následujícím pořadí:

1. [Protokoly operací](#operations) najít chyby identifikované modul Synchronizace během importu a synchronizace.
2. [Prostoru konektoru](#connector-space-object-properties) najít chybějící objekty a chyb synchronizace.
3. [Metaverse](#metaverse-object-properties) najít problémy související s daty.

Spustit [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) před zahájením těchto kroků.

## <a name="operations"></a>Operace
**Operace** kartu v Synchronization Service Manager je, kde byste měli začít s odstraňováním potíží. Tato karta zobrazuje výsledky z nejnovější operace. 

![Snímek obrazovky synchronizace portálu Service Manager, zobrazující zvolenou kartou operace](./media/tshoot-connect-object-not-syncing/operations.png)  

V horní polovině **operace** karta zobrazuje všechna spuštění v chronologickém pořadí. Ve výchozím nastavení, operace přihlášení udržuje informace o posledních sedmi dnů, ale toto nastavení lze změnit pomocí [Plánovač](how-to-connect-sync-feature-scheduler.md). Pohled pro žádné spuštění, který není uveden **úspěch** stav. Můžete změnit kliknutím na záhlaví při řazení.

**Stav** sloupec obsahuje nejdůležitější informace a v něm nejzávažnějších problémů pro spuštění. Tady je stručný přehled nejběžnějších stavů v pořadí podle priority šetření (kde * znamená několik možných chybové řetězce).

| Status | Poznámka |
| --- | --- |
| stopped-* |Spuštění nebylo možné dokončit. K tomu může dojít, například, pokud vzdálený systém je vypnutý a nelze kontaktovat. |
| stopped-error-limit |Existuje více než 5 000 chyby. Spustit automaticky zastavila z důvodu velkého počtu chyb. |
| dokončené -\*– chyby |Spuštění dokončil, ale nejsou chyby (méně než 5 000), které by mělo být vypátráno. |
| dokončené -\*– upozornění |Spuštění dokončil, ale některá data není v očekávaném stavu. Pokud máte chyby, tato zpráva je obvykle pouze příznakem. Upozornění není prozkoumat, dokud se odstranily chyby. |
| úspěch |Žádné problémy. |

Když vyberete řádek na konec **operace** kartu se aktualizuje a zobrazí podrobnosti, ve kterých běží. Na straně úplně vlevo této oblasti, může mít seznam s názvem **krok #**. Tento seznam se zobrazí jenom v případě, že máte více domén v doménové struktuře a každé doméně představuje krok. Název domény najdete v části **oddílu**. V části **statistické údaje o synchronizaci** záhlaví, můžete najít další informace o počtu změn, které byly zpracovány. Vyberte odkazy na získání seznamu změněné objekty. Pokud máte objektů s chybami, tyto chyby se zobrazí v části **chyby synchronizace** záhlaví.

### <a name="errors-on-the-operations-tab"></a>Chyby na kartě operace
Pokud máte chyby, Synchronization Service Manager zobrazí objekt s chybou a vlastní chyba jako odkazy, které poskytují další informace.

![Snímek obrazovky s chybami v Synchronization Service Manageru](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Začněte výběrem řetězec chyby. (Na předchozím obrázku je chybový řetězec **synchronizace pravidlo Chyba – funkce – aktivuje**.) Nejprve se zobrazí přehled o objektu. Pokud chcete zobrazit aktuální chyby, vyberte **trasování zásobníku**. Trasování obsahuje úroveň ladění informací chyby.

Klikněte pravým tlačítkem na **informace v zásobníku volání** klikněte **Vybrat vše**a pak vyberte **kopírování**. Pak zkopírujte do zásobníku a podívejte se na chyby ve svém oblíbeném editoru, jako je například Poznámkový blok.

Pokud je chyba z **SyncRulesEngine**, informace v zásobníku volání nejprve obsahuje všechny atributy objektu. Posuňte se dolů, dokud se nezobrazí záhlaví **u třídy InnerException = >**.  

  ![Snímek obrazovky z Synchronization Service Manager, zobrazují informace o chybě pod záhlavím u třídy InnerException = >](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Následující řádek záhlaví zobrazí chybu. Na předchozím obrázku chyba je z vlastního synchronizační pravidlo, které společnost Fabrikam vytvořili.

Pokud chyba neposkytuje dostatek informací, je čas podívat se na vlastní data. Vyberte odkaz s identifikátorem objektu a odstraňování potíží [importovaný objekt prostoru konektoru](#cs-import).

## <a name="connector-space-object-properties"></a>Vlastnosti objektu prostoru konektoru
Pokud [ **operace** ](#operations) karta zobrazuje žádné chyby, postupujte podle objekt prostoru konektoru Active Directory do úložiště metaverse do služby Azure AD. V této cestě měli byste najít, čem problém spočívá.

### <a name="searching-for-an-object-in-the-cs"></a>Vyhledání objektu v CS

Vyberte v Synchronization Service Manager **konektory**, vyberte konektor služby Active Directory a vyberte **Search Connector Space**.

V **oboru** vyberte **relativní rozlišující** Pokud chcete hledat v atributu CN, nebo vyberte **rozlišující název nebo ukotvení** Pokud chcete vyhledat **distinguishedName**  atribut. Zadejte hodnotu a vyberte **hledání**. 
 
![Snímek obrazovky hledání prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Pokud se nepodařilo najít objekt hledáte, je možná jsou filtrované s [filtrování podle domén](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nebo [filtrování podle organizační jednotky](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Pokud chcete ověřit, že filtrování je nakonfigurován podle očekávání, přečtěte si [synchronizace Azure AD Connect: Konfigurace filtrování](how-to-connect-sync-configure-filtering.md).

Další užitečná hledání můžete provést tak, že vyberete konektor služby Azure AD. V **oboru** vyberte **čekajícího importu**a pak vyberte **přidat** zaškrtávací políčko. Toto vyhledávání poskytuje všechny synchronizované objekty ve službě Azure AD, který nemůže být přidružena místní objekt.  

![Snímek obrazovky osamocené položky v hledání prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Tyto objekty vytvořil jiný modul synchronizace nebo modul Synchronizace s jinou konfiguraci filtrování. Tyto objekty osamocené se už spravují. Projděte si seznam a zvažte odebrání těchto objektů s použitím [Azure AD PowerShell](https://aka.ms/aadposh) rutiny.

### <a name="cs-import"></a>CS import
Při otevření objektu CS existuje několik karet v horní části. **Importovat** karta zobrazuje data, která jsou určená po importu.  

![Snímek obrazovky okna vlastnosti objektu prostoru konektoru, s vybranou kartou importu](./media/tshoot-connect-object-not-syncing/csobject.png)    

**Stará hodnota** sloupec zobrazuje, co je aktuálně uloženy ve připojit a **novou hodnotu** sloupec zobrazuje byla přijata ze zdrojového systému a ještě nebyla použita. Pokud dojde k chybě v objektu, změny nebudou zpracovány.

**Chyba synchronizace** karta se nezobrazuje **vlastnosti objektu prostoru konektoru** okno pouze v případě, že dojde k nějakému problému s objektem. Další informace najdete v tématu Jak [řešení chyb synchronizace na **operace** kartu](#errors-on-the-operations-tab).

![Snímek obrazovky na kartu chyby synchronizace v okně Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS rodokmenu
**Rodokmenu** kartu **vlastnosti objektu prostoru konektoru** okno zobrazuje, jak objekt prostoru konektoru má vztah k objektu úložiště metaverse. Zobrazí se po konektoru poslední importování změnu z připojený systém a které pravidla používají k naplnění dat v úložišti metaverse.  

![Snímek obrazovky zobrazující kartu rodokmenu v okně Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na předchozím obrázku **akce** sloupci se zobrazuje pravidlo příchozí synchronizace s akcí **zřízení**. Který označuje, dokud tento objekt prostoru konektoru je k dispozici, objektu úložiště metaverse zůstane. Pokud v seznamu synchronizačních pravidel se místo toho zobrazí pravidlo odchozí synchronizace s **zřízení** akce, tento objekt se odstraní při odstranění objektu úložiště metaverse.  

![Snímek obrazovky okna rodokmenu na kartě rodokmenu v okně Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na předchozím obrázku se také zobrazí v **PasswordSync** sloupec, který může přispívat v prostoru konektoru příchozí změny hesla od jedné synchronizační pravidlo obsahuje hodnotu **True**. Toto heslo je odesíláno do služby Azure AD pomocí odchozí pravidla.

Z **rodokmenu** kartu, můžete získat do úložiště metaverse tak, že vyberete [ **vlastnosti objektu úložiště Metaverse**](#mv-attributes).

### <a name="preview"></a>Preview
V levém horním rohu **vlastnosti objektu prostoru konektoru** je okno **ve verzi Preview** tlačítko. Klikněte na toto tlačítko otevřete **ve verzi Preview** stránku, kde můžete synchronizovat jednoho objektu. Tato stránka je užitečné, pokud řešíte některé vlastní synchronizační pravidla a chcete posoudit účinek parametru změnu u jednoho objektu. Můžete vybrat **Full sync** nebo **rozdílová synchronizace**. Můžete také vybrat **generovat ve verzi Preview**, který pouze sleduje změny v paměti. Nebo vyberte **potvrzení změn ve verzi Preview**, která aktualizuje úložiště metaverse a zpracuje všechny změny prostor konektoru cíl.  

![Snímek obrazovky stránky ve verzi Preview se spustit náhled vybrané](./media/tshoot-connect-object-not-syncing/preview.png)  

Ve verzi preview můžete zkontrolovat na objekt a zobrazit pravidlo, které u konkrétní atribut toku.  

![Snímek obrazovky stránky ve verzi Preview, Import toku atributů](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Protokol
Vedle položky **ve verzi Preview** tlačítka, vyberte **protokolu** tlačítko Otevřít **protokolu** stránky. Zde můžete zobrazit stav synchronizace hesla a historii. Další informace najdete v tématu [řešit synchronizaci hodnot hash hesel pomocí synchronizace Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Vlastnosti objektu úložiště Metaverse
Je obvykle vhodnější spustit hledání ze zdrojové prostoru konektoru Active Directory. Ale můžete také spustit vyhledávání od úložiště metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Vyhledání objektu v MV
Vyberte v Synchronization Service Manager **vyhledávání Metaverse**, jako v následujícím obrázku. Vytvořte dotaz, o kterém víte, že Vyhledá uživatele. Vyhledejte pro společné atributy, jako například **accountName** (**sAMAccountName**) a **userPrincipalName**. Další informace najdete v tématu [Správce služby synchronizace úložiště Metaverse search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Snímek obrazovky synchronizace portálu Service Manager, s vybranou kartou vyhledávání Metaverse](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

V **výsledky hledání** okna, klikněte na objekt.

Pokud jste se nenašel objekt, se zatím nedosáhly úložiště metaverse. Pokračujte ve vyhledávání u objektu ve službě Active Directory [prostoru konektoru](#connector-space-object-properties). Pokud narazíte na objekt v prostoru konektoru Active Directory, může dojít k chybě synchronizace, která je blokuje objektu z přicházejících do úložiště metaverse nebo může být použit filtr oborů synchronizační pravidlo.

### <a name="object-not-found-in-the-mv"></a>Objekt nebyl nalezen v MV
Pokud objekt je ve službě Active Directory CS, ale není k dispozici v MV, se použije filtr oborů. Podívejte se na filtr oborů, přejděte do nabídky aplikace klasické pracovní plochy a vyberte **Editor pravidel synchronizace**. Filtrovat pravidla pro objekt úpravou vidět na následujícím filtru.

  ![Snímek obrazovky z Editor pravidel synchronizace, zobrazuje hledání pravidla synchronizace příchozích dat](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Zobrazit všechna pravidla v seznamu výše a zkontrolujte **Scoping filtr**. V následující filtr oborů Pokud **isCriticalSystemObject** hodnota je null nebo FALSE nebo prázdná, se nachází v oboru.

  ![Snímek obrazovky filtr oborů při hledání pravidla synchronizace příchozích dat](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Přejděte [CS Import](#cs-import) atribut seznam a kontroly, který filtr blokuje objektu získat přechodem MV. **Prostoru konektoru** atribut v seznamu se zobrazí pouze atributy jinou hodnotu než null a není prázdná. Například pokud **isCriticalSystemObject** nezobrazuje v seznamu, hodnota tohoto atributu je null nebo prázdný.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objekt nebyl nalezen ve službě Azure AD CS
Pokud objekt není k dispozici v prostoru konektoru služby Azure AD, ale je k dispozici v MV, podívejte se na filtr oborů odchozího pravidla odpovídající prostor konektoru a zjistěte, pokud objekt je odfiltrována vzhledem k tomu, [MV atributy](#mv-attributes)nevyhovují kritériím.

Podívat se na odchozí filtr oborů, vyberte použít pravidla pro objekt úpravou vidět na následujícím filtru. Zobrazit každé pravidlo a podívejte se na odpovídající [MV atribut](#mv-attributes) hodnotu.

  ![Snímek obrazovky hledání pravidla odchozí synchronizace v editoru pravidel synchronizace](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributy MV
Na **atributy** kartu, zobrazí se hodnoty a konektorů, které přispěly je.  

![Snímek obrazovky okna vlastnosti objektu úložiště Metaverse na kartě atributy vybrané](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Pokud objekt nesynchronizuje, odpovědět na tyto otázky týkající se stavů atributů v úložišti metaverse:
- Je atribut **cloudFiltered** přítomny a nastaveny **True**? Pokud se jedná, byly filtrovány podle kroků v [založených na atributech filtrování](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Je atribut **sourceAnchor** k dispozici? Pokud ne, máte k dispozici doménovou strukturu prostředků účtu? Pokud objekt je označen jako propojená poštovní schránka (atribut **msExchRecipientTypeDetails** má hodnotu **2**), **sourceAnchor** je přispěla doménová struktura se Povolit účet služby Active Directory. Ujistěte se, že hlavní účet byl importován a správně synchronizovat. Hlavní účet musí být uvedena mezi [konektory](#mv-connectors) pro objekt.

### <a name="mv-connectors"></a>Konektory MV
**Konektory** karta zobrazuje všechny mezery konektoru, které mají reprezentaci objektu. 
 
![Snímek obrazovky okna vlastnosti objektu úložiště Metaverse s vybranou kartou konektorů](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Konektor, měli byste mít:

- Každé doménové struktuře služby Active Directory uživatele je vyjádřena v. Tento zápis mohou zahrnovat **foreignSecurityPrincipals** a **kontakt** objekty.
- Konektor ve službě Azure AD.

Pokud jste chybí konektor ke službě Azure AD, přečtěte si část na [MV atributy](#mv-attributes) ověřit kritéria pro zřizování do služby Azure AD.

Z **konektory** může taky přejít na kartu [objekt prostoru konektoru](#connector-space-object-properties). Vyberte řádek a klikněte na tlačítko **vlastnosti**.

## <a name="next-steps"></a>Další postup
- Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md).
- Další informace o [hybridní identita](whatis-hybrid-identity.md).
