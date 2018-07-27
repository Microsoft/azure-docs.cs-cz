---
title: Azure AD Connect Health – Diagnostika duplicitní atribut chyby synchronizace | Dokumentace Microsoftu
description: Tento dokument popisuje proces Diagnostika chyb synchronizace duplicitním atributem a potenciální opravu osamocený objekt scénářů přímo z portálu Azure portal.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 3659572f46ae82d39a6c53246db2b6a536be32c8
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282936"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostikovat a opravovat chyby synchronizace duplicitním atributem

## <a name="overview"></a>Přehled
Zvýrazněte chyby synchronizace trvá jeden krok dále, Azure Active Directory (Azure AD) Connect Health zavádí samoobslužnou nápravu. Odstraňování potíží s duplicitním atributem chyby synchronizace a opravy objekty, které jsou osamocené ze služby Azure AD.
Funkce diagnostiky má tyto výhody:
- Poskytuje diagnostické procedury, která se může zúžit chyby synchronizace duplicitní atribut. A navíc nabízí konkrétní opravy.
- Oprava pro vyhrazené scénáře platí ze služby Azure AD k vyřešení chyby v jediném kroku.
- Žádný upgrade nebo konfigurace je potřeba povolit tuto funkci.
Další informace o službě Azure AD najdete v tématu [Identity synchronizace a odolnost duplicitních atributů](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problémy
### <a name="a-common-scenario"></a>Běžný scénář
Když **QuarantinedAttributeValueMustBeUnique** a **AttributeValueMustBeUnique** stát chyby synchronizace, je běžně setkat **UserPrincipalName** nebo **Adresy proxy serveru** konflikt ve službě Azure AD. Chyby synchronizace možná půjde vyřešit aktualizací konfliktní objekt zdroje ze strany místní. Chyba synchronizace bude vyřešen po příští synchronizaci. Například tento obrázek označuje, že dva uživatelé mají ke konfliktu jejich **UserPrincipalName**. Obě jsou **Joe.J@contoso.com**. Konfliktní objekty jsou v karanténě ve službě Azure AD.

![Diagnostika běžný scénář Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scénář osamocený objekt
V některých případech můžete zjistit, že dojde ke ztrátě stávajícího uživatele **zdrojové ukotvení**. V místní službě Active Directory došlo k odstranění zdrojového objektu. Ale změna odstranění signálu je teď nikdy synchronizovány do Azure AD. To se stane, například z důvodu nezdařené modul synchronizace nebo migraci domény. Pokud na stejný objekt získá obnovit nebo znovu vytvořit logicky, stávajícího uživatele by měl být uživateli na synchronizaci z **zdrojové ukotvení**. 

Když stávajícího uživatele je objekt jenom pro cloud, uvidíte také konfliktní uživatele synchronizovány se službou Azure AD. Uživatele nelze porovnat synchronizované na existující objekt. Neexistuje žádný přímý způsob, jak namapovat **zdrojové ukotvení**. Přečtěte si více o [existující znalostní báze](https://support.microsoft.com/help/2647098). 

Existující objekt ve službě Azure AD jako příklad, zachová licence Joe. Nově synchronizovaných objekt s jiným **zdrojové ukotvení** dochází v duplicitním atributem stavu ve službě Azure AD. Změny provedené u Joe v místní službě Active Directory se nepoužije pro původního uživatele Michalův (existující objekt) ve službě Azure AD.  

![Diagnostika scénář osamocený objekt Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostika a řešení potíží ve službě Connect Health 
Funkce Diagnostika podporuje uživatelské objekty s následujícími duplicitní atributy:

| Název atributu | Typy chyb synchronizace|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Pro přístup k této funkci **globálního správce** oprávnění, nebo **Přispěvatel** je vyžadované oprávnění na základě nastavení RBAC.
>

Postupujte podle kroků na webu Azure Portal a zúžit podrobnosti o chybě synchronizace poskytující konkrétnější řešení:

![Kroky pro diagnostiku Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Na webu Azure Portal proveďte několik kroků k identifikaci konkrétní opravitelné scénáře:  
1.  Zkontrolujte, **diagnostikovat stav** sloupce. Stav se zobrazí, pokud je možný způsob, jak opravit chybu při synchronizaci přímo ze služby Azure Active Directory. Jinými slovy, řešení problémů s tok existuje, který můžete zúžit případě chyba a potenciálně ho opravit.
| Status | Co znamená? |
| ------------------ | -----------------|
| Nezahájeno | Jste nenavštívili tento proces diagnostiku. V závislosti na výsledek diagnostiky je potenciální způsob, jak opravit chybu synchronizace přímo z portálu. |
| Vyžaduje se ruční oprava. | Chyba nevejde kritéria dostupných oprav z portálu. Buď konfliktní typy objektů nejsou uživatelů, nebo jste už provedli postup diagnostiky a nemusí se řešit oprava nebyl k dispozici z portálu. V takovém případě oprava ze strany místní je stále jedno z řešení. [Přečtěte si víc o místní opravy](https://support.microsoft.com/help/2647098). | 
| Čeká se na synchronizaci. | Oprava byla použita. Na portálu je čekání na další cyklus synchronizace vymazat chybu. |
  >[!IMPORTANT]
  > Sloupec stavu diagnostiky se resetuje za každý cyklus synchronizace. 
  >

2.  Vyberte **Diagnostika** tlačítko v části Podrobnosti o chybě. Seznámíte se odpovědět na několik otázek a identifikovat podrobnosti o chybě synchronizace. Odpovědi na otázky identifikovat s případem osamocený objekt.

3.  Pokud **Zavřít** tlačítko se zobrazí na konci diagnostiky, neexistuje žádná Rychlá oprava dostupná z portálu, na základě vašich odpovědí. Odkazovat na řešení uvedené v předchozím kroku. Opravy v místním jsou stále řešení. Vyberte **Zavřít** tlačítko. Stav aktuální Chyba synchronizace se přepne do **vyžaduje se Ruční oprava**. Stav zůstane během aktuální cyklus synchronizace.

4.  Poté, co je označený osamocený objekt případ, můžete opravit duplicitní atributy chyby synchronizace přímo z portálu. Chcete-li spustit proces, vyberte **použít opravu** tlačítko. Stav aktuálních aktualizací chyb synchronizace na **čekající synchronizace**.

5.  Po další cyklus synchronizace by měla být chybu odebrat ze seznamu.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpovědět na otázky diagnostiky 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Uživatel existuje v místním Active Directory?

Tento dotaz se pokusí identifikovat zdrojový objekt stávajícího uživatele z místní služby Active Directory.  
1.  Zkontrolujte, jestli má Azure Active Directory pomocí zadaného objektu **UserPrincipalName**. Pokud ne, odpovězte **ne**.
2.  Pokud ano, zkontrolujte, zda je objekt stále v oboru synchronizace.  
  - Vyhledejte v prostoru konektoru Azure AD pomocí DN.
  - Pokud je objekt zjištěn **čekající přidání** stavu, odpovězte **ne**. Azure AD Connect nemůže připojit objekt k objektu přímo Azure AD.
  - Pokud se objekt nenajde, odpovězte **Ano**.

V těchto příkladech na otázku, pokusí se určit, zda **Joe Jackson** stále existuje v místním Active Directory.
Pro **běžný scénář**, oba uživatelé **Joe Johnsonem** a **Joe Jackson** se nacházejí v místní službě Active Directory. Dva různí uživatelé jsou v karanténě objekty.

![Diagnostika běžný scénář Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

Pro **osamocený objekt scénář**, pouze jednoho uživatele **Joe Johnsonem** je k dispozici v místní službě Active Directory:

![Diagnostika osamocený objekt synchronizace chyba * neexistuje uživatel * scénář](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Obě tyto účty patří do stejného uživatele?
Tento dotaz kontroluje příchozí konfliktní uživatele a existující objekt uživatele ve službě Azure AD, pokud chcete zobrazit, pokud patří do stejného uživatele.  
1.  Konfliktní objekt je nově synchronizovat do Azure Active Directory. Porovnání atributů objektů:  
  - Zobrazovaný název
  - Hlavní název uživatele (UPN)
  - ID objektu
2.  Pokud Azure AD nepodaří porovnat, zkontrolujte, zda služba Active Directory obsahuje objekty pomocí zadaných **názvy userPrincipalName**. Odpověď **ne** Pokud zjistíte, jak.

V následujícím příkladu se tyto dva objekty patří do stejného uživatele **Joe Johnsonem**.

![Diagnostika osamocený objekt synchronizace chyba * stejný uživatel * scénář](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co se stane po opravy je použít ve scénáři osamocený objekt
Na základě odpovědí na předchozí otázky, zobrazí se vám **použít opravu** tlačítko opravu je dostupná z Azure AD. V takovém případě se místní objekt synchronizuje s neočekávaným Azure AD objektu. Dva objekty se mapují pomocí **zdrojové ukotvení**. **Použít opravu** změnit tyto přebírá nebo podobných kroků:
1. Aktualizace **zdrojové ukotvení** správný objekt ve službě Azure AD.
2. Odstraní konfliktní objekt ve službě Azure AD, pokud je k dispozici.

![Diagnostikujte chybu při synchronizaci po opravy](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Použít opravu** změna se vztahuje pouze na osamocený objekt případy.
>

Po provedení předchozích kroků má uživatel přístup původní zdroj, který je odkaz na existující objekt. **Diagnostikovat stav** aktualizuje hodnotu v zobrazení seznamu **čeká se na synchronizaci**. Chyba synchronizace bude vyřešen po příští synchronizaci. Připojení stavu se žádné delší zobrazit chyba přeložit synchronizace v zobrazení seznamu.

## <a name="failures-and-error-messages"></a>Chyby a chybové zprávy
**Uživatele s konfliktním atributem je obnovitelné odstranění v Azure Active Directory. Zkontrolujte, že uživatel je obtížné odstranit dřív, než zkuste to znovu.**  
Uživatel s konfliktním atributem ve službě Azure AD by měl vyčistit, než použijete opravu. Podívejte se na [jak trvale odstranit uživatele ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) než to zkusíte znovu vyřešit. Uživatel také se automaticky odstraní trvale po 30 dnech ve stavu obnovitelného odstraněno. 

**Aktualizace zdrojového ukotvení pro cloudové uživatele ve vašem tenantovi se nepodporuje.**  
Cloudových uživatelů ve službě Azure AD by neměl mít zdrojové ukotvení. Aktualizace zdrojového ukotvení není v tomto případě nepodporuje. Ruční oprava nic dalšího v místním prostředí. 

## <a name="faq"></a>Nejčastější dotazy
**Otázka:** Co se stane, když provádění **použít opravu** nezdaří?  
**Odpověď:** Pokud se nezdaří spuštění, je možné, že Azure AD Connect je spuštěná chybu exportu. Aktualizujte stránku portálu a zkuste to znovu po příští synchronizaci. Cyklus synchronizace výchozí hodnota je 30 minut. 


**Otázka:** Co když **existující objekt** by měla být objekt, který chcete odstranit?  
**Odpověď:** Pokud **existující objekt** by měl být odstraněn, proces nezahrnuje změnu **zdrojové ukotvení**. Obvykle můžete vyřešit z místní služby Active Directory. 


**Otázka:** Chcete-li použít opravu jaká oprávnění potřebuje uživatel?  
**Odpověď:** **Globální správce**, nebo **Přispěvatel** z nastavení RBAC v má oprávnění pro přístup k Diagnostika a řešení potíží s procesem.


**Otázka:** Je nutné nakonfigurovat službu Azure AD Connect nebo aktualizace agenta Azure AD Connect Health pro tuto funkci?  
**Odpověď:** Ne, proces diagnostiku je kompletní cloudové funkce.


**Otázka:** Pokud existující objekt je obnovitelné odstranění, bude proces diagnostiku objektu znovu aktivovat?  
**Odpověď:** Ne, opravy nebude aktualizovat atributy objektů jiných než **zdrojové ukotvení**.
