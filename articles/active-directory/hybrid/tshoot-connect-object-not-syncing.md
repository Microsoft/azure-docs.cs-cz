---
title: Poradce při potížích s objektem, který není synchronizován se službou Azure Active Directory | Dokumenty společnosti Microsoft
description: Poradce při potížích s objektem, který není synchronizován se službou Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253530"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Poradce při potížích s objektem, který není synchronizován se službou Azure Active Directory

Pokud se objekt nesynchronizuje podle očekávání se službou Microsoft Azure Active Directory (Azure AD), může to být z několika důvodů. Pokud jste obdrželi chybový e-mail z Azure AD nebo se zobrazí chyba ve službě Azure AD Connect Health, přečtěte si místo toho článek [Při synchronizaci přečtěte si chyby při potížích.](tshoot-connect-sync-errors.md) Ale pokud řešíte problém, kde objekt není ve službě Azure AD, tento článek je pro vás. Popisuje, jak najít chyby v místní součásti synchronizace Azure AD Connect.

>[!IMPORTANT]
>Pro nasazení Služby Azure AD Connect s verzí 1.1.749.0 nebo vyšší použijte [úlohu řešení potíží](tshoot-connect-objectsync.md) v průvodci k řešení problémů se synchronizací objektů. 

## <a name="synchronization-process"></a>Proces synchronizace

Než prozkoumáme problémy se synchronizací, pochopme proces synchronizace Azure AD Connect:

  ![Diagram procesu synchronizace azure a služby AD připojit](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Místo konektoru, tabulka v databázi
* **MV:** Metaverse – tabulka v databázi

### <a name="synchronization-steps"></a>**Kroky synchronizace**
Proces synchronizace zahrnuje následující kroky:

1. **Import ze ad:** Objekty služby Active Directory jsou přeneseny do služby Active Directory CS.

2. **Import z Azure AD:** Objekty Azure AD jsou přeneseny do Azure AD CS.

3. **Synchronizace:** Pravidla příchozí synchronizace a odchozí synchronizace jsou spouštěna v pořadí podle čísla priority, od nižší ch vyšší. Chcete-li zobrazit pravidla synchronizace, přejděte na Editor pravidel synchronizace z aplikací klasické pracovní plochy. Pravidla příchozí synchronizace přinášejí data z CS do MV. Pravidla odchozí synchronizace přesunout data z MV do CS.

4. **Export do ad:** Po synchronizaci jsou objekty exportovány ze služby Active Directory CS do služby Active Directory.

5. **Export do Azure AD:** Po synchronizaci se objekty exportují z Azure AD CS do Azure AD.

## <a name="troubleshooting"></a>Řešení potíží

Chcete-li najít chyby, podívejte se na několik různých míst v následujícím pořadí:

1. [Operace protokoly](#operations) najít chyby identifikované synchronizačního modulu během importu a synchronizace.
2. [Prostor konektoru](#connector-space-object-properties) najít chybějící objekty a chyby synchronizace.
3. [Metaverse](#metaverse-object-properties) najít problémy související s daty.

Před zahájením těchto kroků spusťte [Správce synchronizačních služeb.](how-to-connect-sync-service-manager-ui.md)

## <a name="operations"></a>Provoz
Karta **Operace** ve Správci synchronizačních služeb je místo, kde byste měli začít s odstraňováním potíží. Tato karta zobrazuje výsledky nejnovějších operací. 

![Snímek obrazovky správce synchronizačního servisu s vybranou kartou Operace](./media/tshoot-connect-object-not-syncing/operations.png)  

V horní polovině karty **Operace** jsou zobrazeny všechny spuštění v chronologickém pořadí. Ve výchozím nastavení protokol operací uchovává informace o posledních sedmi dnech, ale toto nastavení lze změnit pomocí [plánovače](how-to-connect-sync-feature-scheduler.md). Vyhledejte jakýkoli běh, který nezobrazuje stav **úspěchu.** Řazení můžete změnit kliknutím na záhlaví.

Sloupec **Stav** obsahuje nejdůležitější informace a zobrazuje nejzávažnější problém pro spuštění. Zde je stručný přehled nejběžnějších stavů v pořadí priorit šetření (kde * označuje několik možných chybových řetězců).

| Status | Poznámka |
| --- | --- |
| zastaveno-* |Běh nemohl být dokončen. K tomu může dojít například v případě, že vzdálený systém nezvládá a nelze jej kontaktovat. |
| limit zastavené chyby |Existuje více než 5 000 chyb. Spuštění bylo automaticky zastaveno z důvodu velkého počtu chyb. |
| dokončeno-\*-chyby |Spuštění bylo dokončeno, ale existují chyby (méně než 5 000), které by měly být prošetřeny. |
| dokončeno-\*-varování |Spuštění bylo dokončeno, ale některá data nejsou v očekávaném stavu. Pokud máte chyby, tato zpráva je obvykle pouze příznakem. Neprošetřujte upozornění, dokud nevyřešíte chyby. |
| úspěch |Žádné problémy. |

Když vyberete řádek, dolní část karty **Operace** se aktualizuje tak, aby zobrazovala podrobnosti o tomto spuštění. Na levé straně této oblasti můžete mít seznam s názvem **Krok #**. Tento seznam se zobrazí pouze v případě, že máte v doménové struktuře více domén a každá doména je reprezentována krokem. Název domény naleznete pod nadpisem **Oddíl**. V záhlaví **Statistika synchronizace** naleznete další informace o počtu zpracovaných změn. Výběrem odkazů získáte seznam změněných objektů. Pokud máte objekty s chybami, tyto chyby se zobrazí v záhlaví **Chyby synchronizace.**

### <a name="errors-on-the-operations-tab"></a>Chyby na kartě Operace
Pokud máte chyby, Správce služeb synchronizace zobrazí objekt v chybě i samotnou chybu jako odkazy, které poskytují další informace.

![Snímek obrazovky s chybami ve Správci synchronizačních služeb](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Začněte výběrem chybového řetězce. (Na předchozím obrázku je chybový řetězec **spuštěn synchronizum-pravidlo-chyba-funkce**.) Nejprve se zobrazí přehled objektu. Chcete-li zobrazit skutečnou chybu, vyberte **možnost Trasování zásobníku**. Toto trasování poskytuje informace na úrovni ladění chyby.

Klepněte pravým tlačítkem myši na pole **Informace o zásobníku volání,** klepněte na příkaz **Vybrat vše**a pak vyberte **příkaz Kopírovat**. Pak zkopírujte zásobník a podívejte se na chybu ve svém oblíbeném editoru, jako je poznámkový blok.

Pokud je chyba z **SyncRulesEngine**, informace zásobníku volání nejprve zobrazí všechny atributy na objektu. Posuňte se dolů, dokud se nezobrazí nadpis **InnerException =>**.  

  ![Snímek obrazovky správce synchronizačního servisu zobrazující informace o chybě pod nadpisem InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Řádek za nadpisem zobrazuje chybu. Na předchozím obrázku je chyba z pravidla vlastní synchronizace, které vytvořil a vytvořil společnost Fabrikam.

Pokud chyba neposkytuje dostatek informací, je čas podívat se na samotná data. Vyberte propojení s identifikátorem objektu a pokračujte v odstraňování potíží s [importovaným objektem konektoru](#cs-import).

## <a name="connector-space-object-properties"></a>Vlastnosti objektu prostoru konektoru
Pokud karta [**Operace**](#operations) nezobrazuje žádné chyby, postupujte podle objektu prostoru konektoru ze služby Active Directory do metaverse do Azure AD. V této cestě byste měli zjistit, kde je problém.

### <a name="searching-for-an-object-in-the-cs"></a>Hledání objektu v CS

Ve Správci synchronizačních služeb vyberte **konektory**, vyberte konektor služby Active Directory a vyberte **možnost Hledat místo konektoru**.

V poli **Obor** vyberte **pole RDN,** pokud chcete hledat v atributu Cn, nebo vyberte **DN nebo kotvu,** pokud chcete hledat v atributu **distinguishedName.** Zadejte hodnotu a vyberte **Hledat**. 
 
![Snímek obrazovky s hledáním prostoru spojnice](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Pokud hledací objekt nenajdete, je možné, že byl filtrován pomocí [filtrování založeného na doméně](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nebo [filtrování podle ou.](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) Chcete-li ověřit, zda je filtrování nakonfigurované podle očekávání, přečtěte si [článek Synchronizace připojení Azure AD: Konfigurace filtrování](how-to-connect-sync-configure-filtering.md).

Můžete provést další užitečné hledání výběrem konektoru Azure AD. V poli **Obor** vyberte **Čekající import**a zaškrtněte políčko **Přidat.** Toto hledání poskytuje všechny synchronizované objekty ve službě Azure AD, které nelze přidružit k místnímu objektu.  

![Snímek obrazovky se sirotky ve vyhledávání prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Tyto objekty byly vytvořeny jiným synchronizačním strojem nebo synchronizačním strojem s jinou konfigurací filtrování. Tyto osamocené objekty již nejsou spravovány. Zkontrolujte tento seznam a zvažte odebrání těchto objektů pomocí rutin [prostředí Azure AD PowerShell.](https://aka.ms/aadposh)

### <a name="cs-import"></a>Import CS
Při otevření objektu CS je nahoře několik karet. Karta **Import** zobrazuje data, která jsou připravena po importu.  

![Snímek obrazovky s oknem Vlastnosti objektu prostoru konektoru s vybranou kartou Import](./media/tshoot-connect-object-not-syncing/csobject.png)    

Sloupec **Stará hodnota** zobrazuje, co je aktuálně uloženo v aplikaci Connect, a sloupec Nová **hodnota** zobrazuje, co bylo přijato ze zdrojového systému a ještě nebylo použito. Pokud je chyba na objektu, změny nejsou zpracovány.

Karta **Chyba synchronizace** je viditelná v okně **Vlastnosti objektu prostoru konektoru** pouze v případě, že došlo k potížím s objektem. Další informace naleznete v [řešení potíží s chybami synchronizace na kartě **Operace** ](#errors-on-the-operations-tab).

![Snímek obrazovky s kartou Chyba synchronizace v okně Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>Cs linie
Karta **Lineage** v okně **Vlastnosti objektu prostoru spojnice ukazuje,** jak objekt prostoru spojnice souvisí s objektem metaverse. Můžete vidět, kdy spojnice naposledy importovala změnu z připojeného systému a která pravidla se použila k naplnění dat v metaverse.  

![Snímek obrazovky s kartou Lineage v okně Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na předchozím obrázku se ve sloupci **Akce** zobrazí pravidlo příchozí synchronizace s položkou **Provision**akce . To znamená, že tak dlouho, dokud tento objekt prostoru spojnice je k dispozici, metaverse objekt zůstane. Pokud seznam pravidel synchronizace místo toho zobrazuje pravidlo odchozí synchronizace s akce **Provision,** tento objekt je odstraněn při odstranění metaverse objektu.  

![Snímek obrazovky s oknem linie na kartě Linie v okně Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na předchozím obrázku můžete také vidět ve sloupci **PasswordSync,** že vstupní konektorový prostor může přispět změnami hesla, protože jedno pravidlo synchronizace má hodnotu **True**. Toto heslo se odesílá do služby Azure AD prostřednictvím pravidla odchozí.

Na kartě **Linie** se můžete dostat do metaverse výběrem [**vlastností objektu Metaverse**](#mv-attributes).

### <a name="preview"></a>Preview
V levém dolním rohu okna **Vlastnosti objektu prostoru konektoru** je tlačítko **Náhled.** Toto tlačítko vyberte, chcete-li otevřít stránku **Náhled,** kde můžete synchronizovat jeden objekt. Tato stránka je užitečná, pokud řešíte některé vlastní pravidla synchronizace a chcete zobrazit vliv změny na jeden objekt. Můžete vybrat **úplnou synchronizaci** nebo **synchronizaci delta**. Můžete také vybrat **generovat náhled**, který pouze udržuje změnu v paměti. Nebo vyberte **Potvrdit náhled**, který aktualizuje metaverse a fáze všechny změny cílové spojnice prostory.  

![Snímek obrazovky stránky Náhled s vybranou možností Start Preview](./media/tshoot-connect-object-not-syncing/preview.png)  

V náhledu můžete zkontrolovat objekt a zjistit, které pravidlo použité pro tok určitého atributu.  

![Snímek obrazovky stránky Náhled s importem toku atributů](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Protokol
Vedle tlačítka **Náhled** vyberte tlačítko **Protokol,** chcete-li otevřít stránku **Protokol.** Zde můžete vidět stav synchronizace hesel a historii. Další informace najdete [v tématu Poradce při potížích se synchronizací hash hesel pomocí synchronizace Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Vlastnosti objektu Metaverse
Obvykle je lepší začít hledat ze zdrojového prostoru konektoru služby Active Directory. Ale můžete také začít hledat z metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Hledání objektu v MV
Ve Správci synchronizačních služeb vyberte **Metaverse Search**, jako na následujícím obrázku. Vytvořte dotaz, o kterém víte, že najde uživatele. Vyhledejte běžné atributy, například **accountName** (**sAMAccountName**) a **userPrincipalName**. Další informace naleznete v [tématu Sync Service Manager Metaverse search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Snímek obrazovky správce synchronizačních služeb s vybranou kartou Metaverse Search](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

V okně **Výsledky hledání** klepněte na objekt.

Pokud jste objekt nenašli, ještě nedosáhl metaverse. Pokračujte v hledání objektu v [prostoru konektoru](#connector-space-object-properties)služby Active Directory . Pokud najdete objekt v prostoru konektoru služby Active Directory, může se stát, že dojde k chybě synchronizace, která blokuje přístup objektu do metaprostoru, nebo může být použit filtr oboru pravidla synchronizace.

### <a name="object-not-found-in-the-mv"></a>Objekt nebyl nalezen v MV
Pokud je objekt v cs služby Active Directory, ale není k dispozici v MV, použije se filtr oborů. Chcete-li se podívat na filtr oboru, přejděte do nabídky aplikace pro stolní počítače a vyberte **Editor pravidel synchronizace**. Pravidla platná pro objekt můžete filtrovat úpravou níže uvedeného filtru.

  ![Snímek obrazovky Editoru pravidel synchronizace zobrazující hledání pravidel příchozí synchronizace](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Zobrazte každé pravidlo v seznamu shora a zkontrolujte **filtr oborů**. V následujícím filtru oborů, pokud je hodnota **isCriticalSystemObject** null nebo FALSE nebo prázdná, je v oboru.

  ![Snímek obrazovky s filtrem oboru při hledání pravidel příchozí synchronizace](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Přejděte do seznamu [atributů importu CS](#cs-import) a zkontrolujte, který filtr blokuje přesunutí objektu do mv. V seznamu **atributů Prostoru konektoru** se zobrazí pouze nenulové a neprázdné atributy. Například pokud **isCriticalSystemObject** nezobrazí v seznamu, hodnota tohoto atributu je null nebo prázdné.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objekt nebyl nalezen ve službě Azure AD CS
Pokud objekt není k dispozici v prostoru konektoru Azure AD, ale je k dispozici v MV, podívejte se na filtr oborů odchozí pravidla odpovídající prostoru konektoru a zjistěte, pokud je objekt odfiltrován, protože [atributy MV](#mv-attributes) nesplňují kritéria.

Chcete-li se podívat na filtr odchozího oboru, vyberte příslušná pravidla pro objekt úpravou filtru níže. Prohlédněte si každé pravidlo a podívejte se na odpovídající hodnotu [atributu MV.](#mv-attributes)

  ![Snímek obrazovky s hledáním pravidel odchozí synchronizace v Editoru pravidel synchronizace](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>Atributy MV
Na kartě **Atributy** uvidíte hodnoty a konektory, které k nim přispěly.  

![Snímek obrazovky s oknem Vlastnosti objektu Metaverse s vybranou kartou Atributy](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Pokud se objekt nesynchronizuje, zeptejte se na následující otázky týkající se stavů atributů v metaverse:
- Je atribut **cloudFiltered** přítomen a nastaven na **Hodnotu True**? Pokud ano, byl filtrován podle kroků v [filtrování založeném na atributech](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Je atribut **sourceAnchor** přítomen? Pokud ne, máte topologii doménové struktury prostředků účtu? Pokud je objekt identifikován jako propojená poštovní schránka (atribut **msExchRecipientTypeDetails** má hodnotu **2**), **sourceAnchor** je přispívajídoménou s povoleným účtem služby Active Directory. Zkontrolujte, zda byl hlavní účet importován a synchronizován správně. Hlavní účet musí být uveden mezi [spojnicemi](#mv-connectors) objektu.

### <a name="mv-connectors"></a>MV konektory
Karta **Konektory** zobrazuje všechny mezery spojnic, které mají reprezentaci objektu. 
 
![Snímek obrazovky s oknem Vlastnosti objektu Metaverse s vybranou kartou Konektory](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Měli byste mít konektor pro:

- Každá doménová struktura služby Active Directory, ve které je uživatel zastoupen. Tato reprezentace může zahrnovat **foreignSecurityPrincipals** a **Contact** objekty.
- Konektor ve službě Azure AD.

Pokud vám chybí konektor azure ad, zkontrolujte část [atributy MV](#mv-attributes) ověřit kritéria pro zřizování do Azure AD.

Na kartě **Konektory** můžete také přejít na [objekt prostoru konektoru](#connector-space-object-properties). Vyberte řádek a klepněte na **příkaz Vlastnosti**.

## <a name="next-steps"></a>Další kroky
- Další informace o [synchronizaci Azure AD Connect](how-to-connect-sync-whatis.md).
- Další informace o [hybridní identitě](whatis-hybrid-identity.md).
