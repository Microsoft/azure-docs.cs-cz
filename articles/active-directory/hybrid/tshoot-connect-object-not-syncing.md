---
title: Řešení potíží s objektem, který se nesynchronizuje s Azure Active Directory | Microsoft Docs
description: Řešení potíží s objektem, který se nesynchronizuje s Azure Active Directory.
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
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413458"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Řešení potíží s objektem, který se nesynchronizuje s Azure Active Directory

Pokud se objekt nesynchronizuje podle očekávání s Microsoft Azure Active Directory (Azure AD), může to být z několika důvodů. Pokud jste obdrželi e-mailovou zprávu z Azure AD nebo se zobrazí chyba v Azure AD Connect Health, přečtěte si místo toho [chyby při odstraňování potíží](tshoot-connect-sync-errors.md) . Pokud ale řešíte potíže s problémem, kde objekt není ve službě Azure AD, je tento článek pro vás. Popisuje, jak najít chyby v místní součásti Azure AD Connect synchronizace.

>[!IMPORTANT]
>Pro nasazení Azure AD Connect s verzí 1.1.749.0 nebo novější použijte [úlohu řešení potíží](tshoot-connect-objectsync.md) v průvodci pro řešení potíží se synchronizací objektů. 

## <a name="synchronization-process"></a>Proces synchronizace

Než prozkoumáme synchronizaci problémů, Podívejme se na Azure AD Connect proces synchronizace:

  ![Diagram procesu synchronizace Azure AD Connect](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **Cs:** Místo konektoru, tabulka v databázi
* **MV:** Metaverse, tabulka v databázi

### <a name="synchronization-steps"></a>**Kroky synchronizace**
Proces synchronizace zahrnuje následující kroky:

1. **Importovat ze služby AD:** Objekty služby Active Directory se přenesou do služby Active Directory CS.

2. **Importovat z Azure AD:** Objekty Azure AD se přenesou do služby Azure AD CS.

3. **Synchronizace:** Pravidla pro příchozí synchronizaci a pravidla odchozí synchronizace se spouštějí v pořadí podle priority, od nižších po vyšší. Chcete-li zobrazit pravidla synchronizace, v aplikacích klasické pracovní plochy použijte Editor pravidel synchronizace. Pravidla pro příchozí synchronizaci přinášejí data z CS do MV. Pravidla odchozí synchronizace přesouvá data z MV do CS.

4. **Exportovat do AD:** Po synchronizaci se objekty exportují z Active Directory CS do Active Directory.

5. **Exportovat do služby Azure AD:** Po synchronizaci se objekty exportují z Azure AD CS do Azure AD.

## <a name="troubleshooting"></a>Poradce při potížích

Chcete-li zjistit chyby, podívejte se na několik různých míst v následujícím pořadí:

1. [Protokoly operací](#operations) k nalezení chyb identifikovaných synchronizačním modulem během importu a synchronizace.
2. [Místo konektoru](#connector-space-object-properties) pro vyhledání chybějících objektů a chyb synchronizace.
3. Úložiště [Metaverse](#metaverse-object-properties) pro hledání problémů souvisejících s daty.

Než začnete s tímto postupem, začněte [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) .

## <a name="operations"></a>Operace
Na kartě **operace** v Synchronization Service Manager je místo, kde byste měli začít řešit problémy. Tato karta zobrazuje výsledky z posledních operací. 

![Snímek obrazovky Synchronization Service Manager, na kterém je zobrazená vybraná karta operace](./media/tshoot-connect-object-not-syncing/operations.png)  

Horní polovina karty **operace** zobrazuje všechna spuštění v chronologickém pořadí. Ve výchozím nastavení protokol operací uchovává informace o posledních sedmi dnech, ale toto nastavení lze změnit pomocí [plánovače](how-to-connect-sync-feature-scheduler.md). Vyhledejte jakékoli spuštění, které nezobrazuje stav **úspěch** . Řazení můžete změnit kliknutím na záhlaví.

Sloupec **Status (stav** ) obsahuje nejdůležitější informace a zobrazuje nejzávažnější problém pro běh. Tady je stručný přehled nejběžnějších stavů v pořadí podle priority šetření (kde * označuje několik možných řetězců chyb.)

| Status | Komentář |
| --- | --- |
| zastaveno – * |Spuštění se nepovedlo dokončit. K tomu může dojít například v případě, že vzdálený systém nefunguje a nelze ho kontaktovat. |
| zastaveno – limit chyby |Došlo k více než 5 000 chybám. Běh se automaticky zastavil kvůli velkému počtu chyb. |
| dokončeno – \* chyby |Běh se dokončil, ale vyskytly se chyby (méně než 5 000), které by se měly prozkoumat. |
| dokončeno – \* Upozornění |Běh se dokončil, ale některá data nejsou v očekávaném stavu. Pokud máte chyby, tato zpráva je obvykle jenom příznakem. Nezkoumejte upozornění, dokud nevyřešíte chyby. |
| úspěch |Žádné problémy. |

Když vyberete řádek, v dolní části karty **operace** se aktualizuje, aby se zobrazily podrobnosti o tomto spuštění. Na levé straně této oblasti může být seznam s názvem **Krok #**. Tento seznam se zobrazí pouze v případě, že máte více domén v doménové struktuře a každá doména je reprezentována krokem. Název domény najdete pod **oddílem** nadpisu. Pod hlavičkou **Statistika synchronizace** můžete najít další informace o počtu zpracovaných změn. Kliknutím na odkazy zobrazíte seznam změněných objektů. Pokud máte objekty s chybami, zobrazí se tyto chyby pod hlavičkou **chyby synchronizace** .

### <a name="errors-on-the-operations-tab"></a>Chyby na kartě operace
Pokud máte chyby, Synchronization Service Manager zobrazí objekt chyba a samotnou chybu jako odkazy, které poskytují další informace.

![Snímek obrazovky s chybami v Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Začněte tím, že vyberete řetězec chyby. (Na předchozím obrázku je chybový řetězec **synchronizace-pravidlo-Chyba-funkce – aktivované**.) Nejprve se zobrazí přehled o objektu. Pokud se chcete podívat na skutečnou chybu, vyberte **trasování zásobníku**. Toto trasování poskytuje informace na úrovni ladění pro chybu.

Klikněte pravým tlačítkem myši na pole **informace o zásobníku volání** , klikněte na **Vybrat vše** a pak vyberte **Kopírovat**. Pak zkopírujte zásobník a podívejte se na chybu ve vašem oblíbeném editoru, jako je Poznámkový blok.

Pokud je chyba ze **SyncRulesEngine**, informace zásobníku volání nejprve zobrazí všechny atributy objektu. Posuňte se dolů, dokud se nezobrazí záhlaví **InnerException =>**.  

  ![Snímek obrazovky Synchronization Service Manager, který zobrazuje informace o chybě pod hlavičkou InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Řádek po záhlaví zobrazí chybu. Na předchozím obrázku je chyba z vlastního synchronizačního pravidla, které vytvořila společnost Fabrikam.

Pokud chyba neposkytuje dostatek informací, je čas podívat se na samotná data. Vyberte odkaz s identifikátorem objektu a pokračujte v řešení potíží s [importovaným objektem prostoru konektoru](#cs-import).

## <a name="connector-space-object-properties"></a>Vlastnosti objektu prostoru konektoru
Pokud karta [**operace**](#operations) neobsahuje žádné chyby, použijte objekt prostor konektoru z Active Directory do úložiště metaverse do Azure AD. V této cestě byste měli zjistit, kde je problém.

### <a name="searching-for-an-object-in-the-cs"></a>Hledání objektu v CS

V Synchronization Service Manager vyberte **konektory**, vyberte konektor služby Active Directory a vyberte **Hledat místo v konektoru**.

V poli **obor** vyberte možnost **RDN** , pokud chcete vyhledat atribut CN, nebo vyberte možnost **DN nebo kotva** , pokud chcete vyhledat atribut **rozlišujícího** atributu. Zadejte hodnotu a vyberte **Hledat**. 
 
![Snímek obrazovky s hledáním prostoru konektoru](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Pokud hledaný objekt nenajdete, možná se vyfiltroval pomocí [filtrování založeného na doméně](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nebo [filtrování na základě organizačních jednotek](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering). Pokud chcete ověřit, jestli je filtrování nakonfigurované podle očekávání, přečtěte si téma [Azure AD Connect Sync: Konfigurace filtrování](how-to-connect-sync-configure-filtering.md).

Můžete provést další užitečné hledání tak, že vyberete konektor Azure AD. V poli **obor** vyberte možnost **nedokončený import** a pak zaškrtněte políčko **Přidat** . Toto vyhledávání poskytuje všechny synchronizované objekty ve službě Azure AD, které nelze přidružit k místnímu objektu.  

![Snímek obrazovky osamocených míst v prostoru konektoru hledání](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Tyto objekty byly vytvořeny jiným modulem synchronizace nebo modulem synchronizace s jinou konfigurací filtrování. Tyto osamocené objekty již nejsou spravovány. Projděte si tento seznam a zvažte odebrání těchto objektů pomocí rutin [Azure AD PowerShellu](/previous-versions/azure/jj151815(v=azure.100)) .

### <a name="cs-import"></a>Import CS
Když otevřete objekt CS, v horní části je několik karet. Karta **Import** zobrazuje data, která jsou připravená po importu.  

![Snímek obrazovky okno Vlastnosti objektu prostoru konektoru s vybranou kartou import](./media/tshoot-connect-object-not-syncing/csobject.png)    

Sloupec **stará hodnota** zobrazuje informace o tom, co je aktuálně Uloženo v poli připojit, a sloupec **Nová hodnota** zobrazuje informace o tom, co bylo přijato ze zdrojového systému a dosud nebylo použito. Pokud v objektu dojde k chybě, změny se nezpracují.

Karta **chyb synchronizace** se zobrazí v okně **vlastnosti objektu prostoru konektoru** pouze v případě, že došlo k potížím s objektem. Další informace najdete [v tématu řešení potíží s chybami synchronizace na kartě **operace**](#errors-on-the-operations-tab).

![Snímek obrazovky karty Chyba synchronizace v objektu prostoru konektoru okno Vlastnosti](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS – řádek
Karta **se v okně** **vlastnosti objektu prostoru konektoru** zobrazuje, jak objekt prostoru konektoru souvisí s objektem úložiště metaverse. Můžete vidět, kdy konektor naposledy importoval změnu z připojeného systému a jaká pravidla se použili k naplnění dat v úložišti Metaverse.  

![Snímek obrazovky znázorňující kartu znak v prostoru konektoru okno Vlastnosti](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Na předchozím obrázku se ve sloupci **Akce** zobrazuje pravidlo příchozí synchronizace s akcí **zřizování**. To znamená, že pokud je tento objekt prostoru konektoru přítomen, zůstane objekt Metaverse. Pokud seznam pravidel synchronizace místo toho zobrazuje pravidlo odchozí synchronizace s akcí **zřízení** , tento objekt se odstraní při odstranění objektu Metaverse.  

![Snímek obrazovky okna se znakem konce řádku v objektu prostoru konektoru okno Vlastnosti](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Na předchozím obrázku můžete také ve sloupci **PasswordSync** zobrazit, že místo pro vstupní konektor může přispívat ke změnám hesla, protože jedno synchronizační pravidlo má hodnotu **true**. Toto heslo se odešle do služby Azure AD prostřednictvím odchozího pravidla.

**Na kartě zarážky se** můžete dostat do úložiště metaverse tím, že vyberete [**vlastnosti objektu úložiště metaverse**](#mv-attributes).

### <a name="preview"></a>Preview
V levém dolním rohu okna **vlastnosti objektu prostoru konektoru** je tlačítko **Náhled** . Kliknutím na toto tlačítko otevřete stránku **náhledu** , kde můžete synchronizovat jeden objekt. Tato stránka je užitečná, Pokud řešíte některá vlastní synchronizační pravidla a chcete zobrazit efekt změny u jednoho objektu. Můžete vybrat **úplnou synchronizaci** nebo **rozdílovou synchronizaci**. Můžete také vybrat možnost **Generovat náhled**, která zachovává pouze změnu paměti. Nebo vyberte možnost **Potvrdit náhled**, která aktualizuje úložiště metaverse a fáze všechny změny v cílových prostorech konektoru.  

![Snímek obrazovky se stránkou Preview s vybranou možnost začít Preview](./media/tshoot-connect-object-not-syncing/preview.png)  

Ve verzi Preview můžete zkontrolovat objekt a zjistit, které pravidlo bylo použito pro konkrétní tok atributů.  

![Snímek obrazovky se stránkou Preview, která zobrazuje tok atributů importu](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Protokol
Vedle tlačítka **Náhled** vyberte tlačítko **protokol** a otevřete stránku **protokol** . Tady vidíte stav synchronizace hesel a historii. Další informace najdete v tématu [řešení potíží se synchronizací hodnot hash hesel pomocí Azure AD Connect synchronizace](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Vlastnosti objektu úložiště metaverse
Je obvykle lepší začít vyhledávat ze zdrojového prostoru konektoru služby Active Directory. Můžete ale také začít vyhledávat z úložiště metaverse.

### <a name="searching-for-an-object-in-the-mv"></a>Hledání objektu v MV
V Synchronization Service Manager vyberte **Hledat v úložišti Metaverse**, jak je znázorněno na následujícím obrázku. Vytvoření dotazu, který znáte, zjistíte uživatele. Vyhledejte běžné atributy, jako je třeba **account** (**sAMAccountName**) a **userPrincipalName**. Další informace najdete v tématu [synchronizace Service Managerho vyhledávání v úložišti Metaverse](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Snímek obrazovky Synchronization Service Manager s vybranou kartou hledání Metaverse](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

V okně **výsledků hledání** klikněte na objekt.

Pokud jste objekt nenalezli, zatím se nedostal do úložiště metaverse. Pokračujte v hledání objektu v [prostoru konektoru](#connector-space-object-properties)služby Active Directory. Pokud najdete objekt v prostoru konektoru služby Active Directory, může dojít k chybě synchronizace, která blokuje příjem objektu do úložiště metaverse, nebo může být použita filtr oboru pravidla synchronizace.

### <a name="object-not-found-in-the-mv"></a>Objekt nebyl nalezen v MV.
Pokud je objekt ve službě Active Directory CS, ale není k dispozici v části MV, je použit filtr oborů. Pokud se chcete podívat na filtr oboru, přejděte do nabídky aplikace klasické pracovní plochy a vyberte **Editor pravidel synchronizace**. Filtrujte pravidla platná pro objekt úpravou filtru uvedeného níže.

  ![Snímek obrazovky s editorem synchronizačních pravidel, která zobrazuje hledání pravidel příchozí synchronizace](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Zobrazte všechna pravidla v seznamu výše a zkontrolujte **Filtr oborů**. V následujícím filtru oboru, pokud je hodnota **isCriticalSystemObject** null nebo false nebo je prázdná, je v rozsahu.

  ![Snímek obrazovky s filtrem oboru v hledání příchozího synchronizačního pravidla](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Přejít na seznam atributů pro [Import cs](#cs-import) a ověřte, který filtr blokuje přesunutí objektu na MV. V seznamu atributů **prostoru konektoru** se zobrazí pouze nenulové a neprázdné atributy. Například pokud se **isCriticalSystemObject** v seznamu nezobrazí, hodnota tohoto atributu je null nebo prázdná.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objekt se nenašel v Azure AD CS.
Pokud objekt není přítomen v prostoru konektoru služby Azure AD, ale nachází se v MV, podívejte se na filtr oboru odchozích pravidel odpovídajícího prostoru konektoru a zjistěte, zda je objekt vyfiltrován, protože [atributy MV](#mv-attributes) nesplňují kritéria.

Chcete-li se podívat na výstupní filtr rozsahu, vyberte následující pravidla pro objekt úpravou filtru níže. Zobrazte si všechna pravidla a podívejte se na odpovídající hodnotu [atributu MV](#mv-attributes) .

  ![Snímek obrazovky s vyhledáváním pravidel odchozí synchronizace v editoru pravidel synchronizace](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV – atributy
Na kartě **atributy** vidíte hodnoty a které konektory je přidaly.  

![Snímek obrazovky okno Vlastnosti objektu Metaverse s vybranou kartou atributy](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Pokud se objekt nesynchronizuje, požádejte o tyto otázky informace o stavech atributů v úložišti Metaverse:
- Je atribut přítomný **cloudFiltered** a je nastaven na **hodnotu true**? Pokud je, je filtrovaný podle kroků v [filtrování založeném na atributech](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Je přítomen atribut **sourceAnchor** ? Pokud ne, máte topologii doménové struktury prostředků účtů? Pokud je objekt identifikován jako propojená poštovní schránka (atribut **msExchRecipientTypeDetails** má hodnotu **2**), **sourceAnchor** ho do doménové struktury připnul s povoleným účtem služby Active Directory. Zajistěte, aby byl hlavní účet správně naimportovaný a synchronizovaný. Hlavní účet musí být uveden mezi [konektory](#mv-connectors) pro daný objekt.

### <a name="mv-connectors"></a>Konektory MV
Karta **konektory** zobrazuje všechny prostory konektorů, které mají reprezentace objektu. 
 
![Snímek obrazovky okno Vlastnosti objektu Metaverse s vybranou kartou konektory](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Měli byste mít konektor na:

- Každá doménová struktura služby Active Directory, ve které je uživatel zastoupen. Tato reprezentace může zahrnovat objekty **foreignSecurityPrincipals** a **Contact** .
- Konektor ve službě Azure AD.

Pokud konektor do Azure AD chybí, Projděte si část o [atributech MV](#mv-attributes) a ověřte kritéria pro zřizování do Azure AD.

Na kartě **konektory** můžete také přejít na [objekt prostoru konektoru](#connector-space-object-properties). Vyberte řádek a klikněte na **vlastnosti**.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Azure AD Connect synchronizaci](how-to-connect-sync-whatis.md).
- Přečtěte si další informace o [hybridní identitě](whatis-hybrid-identity.md).