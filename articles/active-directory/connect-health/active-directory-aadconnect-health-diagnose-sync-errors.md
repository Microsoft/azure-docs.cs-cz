---
title: Azure AD Connect Health - diagnostikovat duplicitní atribut chyby synchronizace | Microsoft Docs
description: Tento dokument popisuje proces diagnostiku chyby synchronizace duplicitní atribut a potenciální opravu scénářů osamocený objekt přímo z portálu Azure.
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
ms.openlocfilehash: 4a6e0924492c26c9bad4ed0af207ad9764c3cc5c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831893"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Při diagnostice a řešení chyb synchronizace duplicitní atribut

## <a name="overview"></a>Přehled
Abyste měli na očích chyby synchronizace trvá dále jeden krok, Connect Health Azure Active Directory (Azure AD) představuje samoobslužnou nápravu. Ho odstraňování chyb duplicitní atribut synchronizace a opravy objekty, které jsou osamocené z Azure AD.
Funkci diagnostiku má tyto výhody:
- Poskytuje diagnostické procedury, která zužuje chyby synchronizace duplicitní atribut. A poskytuje konkrétní opravy.
- Oprava vyhrazené scénáře se vztahují z Azure AD, chcete-li vyřešit chyby v jediném kroku.
- Chcete-li povolit tuto funkci je zapotřebí žádné upgrade nebo konfigurace.
Další informace o Azure AD najdete v tématu [odolnosti Identity synchronizace a duplicitní atribut](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problémy
### <a name="a-common-scenario"></a>Běžný scénář
Když **QuarantinedAttributeValueMustBeUnique** a **AttributeValueMustBeUnique** chyby synchronizace dojít, je běžné zobrazíte **UserPrincipalName** nebo **Adresy proxy serveru** konflikt ve službě Azure AD. Chyby synchronizace může vyřešit tím, že aktualizuje konfliktní zdrojový objekt ze strany místně. Chyba synchronizace bude vyřešen po příští synchronizaci. Například tuto bitovou kopii znamená, že dva uživatelé mají problém s jejich **UserPrincipalName**. Jsou obě **Joe.J@contoso.com**. Konfliktní objekty jsou v karanténě ve službě Azure AD.

![Diagnostika běžný scénář Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scénář osamocený objekt
V některých případech je možné, že dojde ke ztrátě stávajícího uživatele **zdrojové ukotvení**. Došlo k odstranění zdrojového objektu v místní službě Active Directory. Ale změna odstranění signál tu nikdy synchronizován do služby Azure AD. Tato ztráta dochází například z důvodu nezdařené modul synchronizace nebo migraci domény. Pokud na stejný objekt získá obnovit nebo znovu vytvoří logicky, stávajícího uživatele by měla být uživatele na synchronizaci z **zdrojové ukotvení**. 

Když stávajícího uživatele je objekt jenom pro cloud, uvidíte také konfliktní uživatele synchronizovat se službou Azure AD. Uživatele nelze porovnat synchronizace pro existující objekt. Neexistuje žádný přímý způsob přemapování **zdrojové ukotvení**. Další informace [existující znalostní báze knowledge base](https://support.microsoft.com/help/2647098). 

Jako příklad existující objekt ve službě Azure AD zachovává licence Jan. Nově synchronizovaných objekt s jinou **zdrojové ukotvení** dochází ve stavu, duplicitní atribut ve službě Azure AD. Změny pro Jan v místní službě Active Directory se neaplikuje na Michalův původního uživatele (existující objekt) ve službě Azure AD.  

![Diagnostika scénář osamocený objekt Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostika a řešení potíží s kroky v Connect Health 
Funkce Diagnostikujte podporuje uživatelské objekty s následujícími duplicitní atributy:

| Název atributu | Typy chyb synchronizace|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Chcete-li používat tuto funkci, **globálního správce** oprávnění, nebo **Přispěvatel** je vyžadováno oprávnění z nastavení RBAC.
>

Postupujte podle kroků na portálu Azure a zúžit podrobnosti o chybě synchronizačního Zadejte konkrétnější řešení:

![Kroky pro diagnostiku Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Z portálu Azure proveďte pár kroků k identifikaci konkrétní opravitelné scénáře:  
1.  Zkontrolujte **diagnostikovat stav** sloupce. Ve stavu zobrazuje, pokud je možný způsob, jak opravit chyby synchronizace přímo ze služby Azure Active Directory. Jinými slovy, že existuje k řešení potíží toku můžete zúžit případ chyby a potenciálně opravit.
| Status | Co to znamená? |
| ------------------ | -----------------|
| Nezahájeno | Tento proces diagnostiku nebyly navštívil. V závislosti na diagnostiky výsledků je potenciální způsob, jak opravit chyby synchronizace přímo z portálu. |
| Vyžaduje se ruční oprava. | Chyba nevejde kritéria dostupných oprav z portálu. Buď konfliktní typy objektů nejsou uživatelé, nebo už se pomocí kroků diagnostiky a žádné řešení, opravte byla dostupná z portálu. V takovém případě opravu z místní straně je stále jedno z řešení. [Další informace o místní opravy](https://support.microsoft.com/help/2647098). | 
| Čeká se na synchronizaci. | Oprava byla použita. Na portálu čeká na další cyklus synchronizace vymazat chybu. |
  >[!IMPORTANT]
  > Sloupec stavu diagnostiky se obnoví po každém cyklu synchronizace. 
  >

2.  Vyberte **Diagnostikujte** tlačítko v části Podrobnosti o chybě. Budete odpovědět na několik otázek a určete podrobnosti o chybě synchronizace. Odpovědi na otázky pomoci při identifikaci případ osamocený objekt.

3.  Pokud **Zavřít** na konci diagnostiky, se zobrazí tlačítko neexistuje žádná oprava rychlé dostupná z portálu na základě vašich odpovědí. Naleznete řešení uvedené v předchozím kroku. Opravy z místního jsou stále řešení. Vyberte **Zavřít** tlačítko. Stav aktuální Chyba synchronizace se přepne do **Ruční oprava požadované**. Stav zůstane během aktuální synchronizace cyklu.

4.  Poté, co je označený osamocený objekt případ, můžete je vyřešit duplicitní atributy chyby synchronizace přímo z portálu. Chcete-li spustit proces, vyberte **použít opravte** tlačítko. Stav aktuální aktualizace Chyba synchronizace se **čekající na vyřízení synchronizace**.

5.  Po příštím synchronizačním cyklu chyba byste měli odebrat ze seznamu.

## <a name="how-to-answer-the-diagnosis-questions"></a>Postup odpovězte na otázky diagnostiku 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Neexistuje uživatel ve vaší místní službě Active Directory?

Tuto otázku se pokusí identifikovat zdrojový objekt existujícího uživatele z místní služby Active Directory.  
1.  Zkontrolujte, jestli má Azure Active Directory objekt poskytnutým **UserPrincipalName**. Pokud ne, odpovězte **ne**.
2.  Pokud ano, zkontrolujte, zda je objekt stále v oboru pro synchronizaci.  
  - Hledání v prostoru konektoru služby Azure AD pomocí názvu domény.
  - Pokud je objekt v nalezen **čekající přidání** stavu, odpovězte **ne**. Azure AD Connect se nemůže připojit objekt k objektu právo Azure AD.
  - Pokud není nalezen objekt, odpovězte **Ano**.

V těchto příkladech pokusí identifikovat Otázka zda **Jan Jackson** stále existuje v místní službě Active Directory.
Pro **běžný scénář**, jak uživatelé **Jan Janíček** a **Jan Jackson** se nacházejí v místní službě Active Directory. V karanténě objekty jsou dvě různé uživatele.

![Diagnostika běžný scénář Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

Pro **osamocený objekt scénář**, pouze jednoho uživatele **Jan Janíček** je k dispozici v místní službě Active Directory:

![Diagnostika osamocený objekt Chyba synchronizace * neexistuje uživatel * scénář](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Obě tyto účty patří do stejného uživatele?
Tento dotaz ověří příchozích konfliktní uživatelů a existující objekt uživatele ve službě Azure AD, které chcete zobrazit, pokud patří do stejného uživatele.  
1.  Objekt konfliktní je nově synchronizovat do Azure Active Directory. Porovnání atributy objektu:  
  - Zobrazovaný název
  - Hlavní název uživatele (UPN)
  - ID objektu
2.  Pokud Azure AD se nepodaří jejich porovnání, zkontrolujte, zda služby Active Directory má objekty poskytnutým **UserPrincipalNames**. Odpověď **ne** Pokud zjistíte, jak.

V následujícím příkladu se tyto dva objekty patří do stejného uživatele **Jan Janíček**.

![Diagnostika osamocený objekt Chyba synchronizace * stejné uživatele * scénář](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co se stane po oprava je použitá ve scénáři osamocený objekt
Podle odpovědi na otázky předchozí, uvidíte **použít opravte** tlačítko, je-li k dispozici z Azure AD opravu. V takovém případě objekt místní synchronizuje se neočekávané Azure AD objektu. Tyto dva objekty jsou mapovány pomocí **zdrojové ukotvení**. **Použít opravte** změnu trvá tyto nebo podobné kroky:
1. Aktualizace **zdrojové ukotvení** správný objekt ve službě Azure AD.
2. Odstraní konfliktní objektu ve službě Azure AD, pokud je k dispozici.

![Po potíže diagnostikovat chyby synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Použít opravte** změn se vztahuje pouze na osamocený objekt případech.
>

Po provedení předchozích kroků má uživatel přístup původní zdroj, který je odkaz na existující objekt. **Diagnostikovat stav** hodnotu v zobrazení seznamu aktualizace **čekající synchronizace**. Chyba synchronizace bude vyřešen po příští synchronizaci. Připojte stavu bude žádné delší zobrazit chyba přeložit synchronizace v zobrazení seznamu.


## <a name="faq"></a>Nejčastější dotazy
**Otázka:** Co se stane, když provádění **použít opravte** nezdaří?  
**Odpověď:** Pokud se spuštění nezdaří, je možné, že Azure AD Connect běží chybu export. Aktualizujte stránky portálu a opakujte po další synchronizaci. Výchozí synchronizačním cyklu je 30 minut. 


**Otázka:** Co když **existující objekt** by měla být objekt, který chcete odstranit?  
**Odpověď:** Pokud **existující objekt** by měl být odstraněn, není proces zahrnuje změnu **zdrojové ukotvení**. Obvykle můžete opravit z místní služby Active Directory. 


**Otázka:** Jaké oprávnění uživatel potřebuje ji nainstalovat?  
**Odpověď:** **Globální správce**, nebo **Přispěvatel** z nastavení RBAC, má oprávnění k přístupu diagnostiky a řešení potíží s procesem.


**Otázka:** Je nutné konfigurovat Azure AD Connect nebo aktualizovat agenta Azure AD Connect Health pro tuto funkci?  
**Odpověď:** Ne, proces diagnostiku je kompletní cloudové funkce.


**Otázka:** Pokud je existující objekt je logicky odstraněn, bude proces diagnostiku objektu znovu aktivovat?  
**Odpověď:** Ne, opravu nebude aktualizovat atributy objektu jiné než **zdrojové ukotvení**.
