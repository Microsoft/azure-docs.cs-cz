---
title: Azure AD Connect Health-Diagnostika chyb synchronizace duplicitních atributů | Microsoft Docs
description: Tento dokument popisuje proces diagnostiky chyb synchronizace duplicitních atributů a potenciální opravu scénářů osamoceného objektu přímo z Azure Portal.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2bd2e72b05cc01b1a351880d565323662635364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278679"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostika a oprava chyb synchronizace kvůli duplicitním atributům

## <a name="overview"></a>Přehled
Provedením jednoho kroku a zvýrazněním chyb synchronizace se Azure Active Directory (Azure AD) Connect Health zavádí samoobslužná náprava. Řeší chyby synchronizace duplicitních atributů a opravuje objekty, které jsou osamocené z Azure AD.
Funkce diagnostiky má tyto výhody:
- Poskytuje diagnostický postup, který zužuje chyby synchronizace duplicitních atributů. A poskytuje konkrétní opravy.
- Aplikuje opravu pro vyhrazené scénáře z Azure AD, aby se chyba vyřešila v jednom kroku.
- K povolení této funkce není nutný žádný upgrade nebo konfigurace.
Další informace o Azure AD najdete v tématu [synchronizace identity a odolnost duplicitních atributů](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problémy
### <a name="a-common-scenario"></a>Běžný scénář
Když dojde k chybám při synchronizaci **QuarantinedAttributeValueMustBeUnique** a **AttributeValueMustBeUnique** , je běžné vidět, že ve službě Azure AD dojde ke konfliktu adres **userPrincipalName** nebo **proxy** . Chyby synchronizace můžete vyřešit tak, že aktualizujete konfliktní zdrojový objekt z místní strany. Po další synchronizaci bude vyřešena chyba synchronizace. Například tento obrázek ukazuje, že dva uživatelé mají konflikt jejich **userPrincipalName**. Oba jsou **Jana. J \@ contoso.com**. Konfliktní objekty jsou v karanténě v Azure AD.

![Běžný scénář diagnostiky chyby synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scénář osamoceného objektu
V některých případech se může stát, že existující uživatel ztratí **zdrojové ukotvení**. Odstranění zdrojového objektu se stalo v místní službě Active Directory. Ale změna signálu odstranění se nikdy nesynchronizoval do Azure AD. K této ztrátě dochází z důvodů, jako jsou problémy s modulem synchronizace nebo migrace domény. Když se stejný objekt obnoví nebo znovu vytvoří, měl by být stávající uživatel uživatel, který se má synchronizovat ze **zdrojového ukotvení**. 

Pokud je stávající uživatel objektem pouze cloudu, můžete také zobrazit kolidujícího uživatele synchronizovaného se službou Azure AD. Uživatel nemůže být spárován se synchronizací s existujícím objektem. Neexistuje přímý způsob, jak přemapovat **zdrojové ukotvení**. Přečtěte si další informace o [stávající znalostní bázi](https://support.microsoft.com/help/2647098). 

Například stávající objekt v Azure AD zachovává licenci Jana. Nově synchronizovaný objekt s jinou **zdrojovou kotvou** se vyskytuje ve stavu duplicitního atributu ve službě Azure AD. Změny pro Jana v místní službě Active Directory se nepoužijí na původního uživatele Jana (existující objekt) ve službě Azure AD.  

![Scénář diagnostikování osamoceného objektu Chyba synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Kroky pro diagnostiku a řešení potíží v Connect Health 
Funkce diagnostiky podporuje uživatelské objekty s následujícími duplicitními atributy:

| Název atributu | Typy chyb synchronizace|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Pro přístup k této funkci se vyžaduje oprávnění **globálního správce** nebo oprávnění **Přispěvatel** z Azure RBAC.
>

Postupujte podle kroků z Azure Portal pro zúžení podrobností o chybách synchronizace a poskytněte konkrétnější řešení:

![Kroky diagnostiky chyby synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

V Azure Portal proveďte několik kroků k identifikaci konkrétních scénářů fixable:  
1.  Zkontrolujte sloupec **stav diagnostiky** . Stav ukazuje, zda je možný způsob, jak opravit chybu synchronizace přímo z Azure Active Directory. Jinými slovy, existuje tok řešení potíží, který může zúžit velikost případu a potenciálně ho opravit.

| Status | Co to znamená? |
| ------------------ | -----------------|
| Nezahájeno | Tento proces diagnostiky jste nenavštívili. V závislosti na výsledku diagnostiky existuje potenciální způsob, jak opravit chybu synchronizace přímo z portálu. |
| Vyžadována Ruční oprava | Chyba nevyhovuje kritériím dostupných oprav z portálu. Buď konfliktní typy objektů nejsou uživateli, nebo jste již provedli diagnostické kroky, a z portálu nebylo k dispozici žádné řešení pro opravu. V druhém případě je oprava z místní strany stále jedním z řešení. [Přečtěte si další informace o místních opravách](https://support.microsoft.com/help/2647098). | 
| Čeká na synchronizaci | Byla použita oprava. Portál čeká, než další synchronizační cyklus vymaže chybu. |

  >[!IMPORTANT]
  > Po každém synchronizačním cyklu se sloupec stav diagnostiky resetuje. 
  >

1. V podrobnostech o chybě vyberte tlačítko **Diagnostika** . Odpovíte na pár otázek a Identifikujte podrobnosti o chybě synchronizace. Odpovědi na otázky vám pomůžou identifikovat osamocený objekt Case.

1. Pokud se na konci diagnostiky zobrazí tlačítko **Zavřít** , na základě vašich odpovědí není dostupná žádná Rychlá oprava z portálu. Podívejte se na řešení zobrazené v posledním kroku. Opravy z místního prostředí jsou stále řešení. Vyberte tlačítko **Zavřít** . Stav aktuálních chybových přepínačů synchronizace, aby se **vyžadovala Ruční oprava**. Stav zůstane v průběhu aktuálního synchronizačního cyklu.

1. Po identifikaci osamoceného objektu je možné duplicitní atributy opravit přímo z portálu. Chcete-li spustit proces, vyberte tlačítko **použít opravu** . Stav aktuálních aktualizací chyb synchronizace na **probíhající synchronizaci**.

1. Po příštím cyklu synchronizace by se chyba měla odebrat ze seznamu.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpovědět na otázky ohledně diagnostiky 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Existuje uživatel ve vaší místní službě Active Directory?

Tato otázka se pokusí identifikovat zdrojový objekt stávajícího uživatele z místní služby Active Directory.  
1. Ověřte, zda Azure Active Directory má objekt se zadaným atributem **userPrincipalName**. Pokud ne, odpovězte **ne**.
2. Pokud k tomu dojde, ověřte, zda je objekt stále v oboru pro synchronizaci.  
   - Vyhledejte v prostoru konektoru služby Azure AD pomocí DN.
   - Pokud se objekt nachází v **nedokončeném stavu přidání** , odpovězte **ne**. Azure AD Connect nemůže připojit objekt k pravému objektu služby Azure AD.
   - Pokud se objekt nenajde, odpovězte **Ano**.

V těchto příkladech se dotaz pokusí zjistit, zda **Jana Jacksonův diagram** stále existuje v místní službě Active Directory.
V případě **běžných scénářů**se v místní službě Active Directory nacházejí oba uživatelé **Jana Johnsonem** a **Jan Jacksonův diagram** . Objekty v karanténě jsou dva různí uživatelé.

![Běžný scénář diagnostiky chyby synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

V **případě scénáře osamoceného objektu**je v místní službě Active Directory k dispozici pouze jeden uživatel **Jana Johnsonem** :

![Diagnostika chyby synchronizace osamocený objekt * uživatel existuje * scénář](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Patří oba tyto účty stejnému uživateli?
Tato otázka zkontroluje příchozího kolidujícího uživatele a stávající objekt uživatele v Azure AD, aby viděli, jestli patří stejnému uživateli.  
1. Konfliktní objekt je nově synchronizovaný Azure Active Directory. Porovnejte atributy objektů:  
   - Zobrazovaný název
   - Hlavní název uživatele
   - ID objektu
2. Pokud Azure AD je nedokáže porovnat, ověřte, jestli má služba Active Directory objekty s poskytnutým **UserPrincipalNames**. Pokud najdete oba, odpovězte **ne** .

V následujícím příkladu tyto dva objekty patří stejnému uživateli **Jan Johnsonem**.

![Diagnostika chyby synchronizace osamocený objekt * stejný uživatel * scénář](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co se stane po použití opravy ve scénáři osamocený objekt
Na základě odpovědí na předchozí otázky se zobrazí tlačítko **použít opravu** , pokud je k dispozici oprava z Azure AD. V tomto případě se místní objekt synchronizuje s neočekávaným objektem Azure AD. Tyto dva objekty jsou mapovány pomocí **zdrojového ukotvení**. Změna **použít opravu** přijímá tyto nebo podobné kroky:
1. Aktualizuje **zdrojový kotvu** na správný objekt ve službě Azure AD.
2. Odstraní konfliktní objekt v Azure AD, pokud je k dispozici.

![Diagnostikovat chybu synchronizace po opravě](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> Změna **použít opravu** se vztahuje pouze na případy osamoceného objektu.
>

Po předchozích krocích může uživatel získat přístup k původnímu prostředku, který je odkazem na existující objekt. Hodnota **stav diagnostiky** v zobrazení seznamu se aktualizuje na **čeká na synchronizaci**. Po další synchronizaci bude vyřešena chyba synchronizace. Funkce připojit stav již nebude zobrazovat vyřešenou chybu synchronizace v zobrazení seznamu.

## <a name="failures-and-error-messages"></a>Chyby a chybové zprávy
**Uživatel s konfliktním atributem je v Azure Active Directory měkký. Před opakováním zajistěte, aby byl uživatel pevným smazán.**  
Uživatel s konfliktním atributem v Azure AD by měl být vyčištěný předtím, než můžete použít opravu. Podívejte se, [jak trvale odstranit uživatele ve službě Azure AD](../fundamentals/active-directory-users-restore.md) , než zkusíte tuto opravu zkusit znovu. Uživatel se taky automaticky odstraní trvale po 30 dnech v tichém odstraněných stavech. 

**Aktualizace zdrojového kotvy na cloudový uživatel ve vašem tenantovi není podporovaná.**  
Cloudový uživatel ve službě Azure AD by neměl mít zdrojové ukotvení. Aktualizace zdrojového kotvy není v tomto případě podporována. V místním prostředí se vyžaduje ruční Oprava. 

## <a name="faq"></a>Časté otázky
**Č.** Co se stane, když dojde k chybě při **použití opravy** ?  
**Určitého.** Pokud je spuštění neúspěšné, je možné, že Azure AD Connect spouští chybu exportu. Aktualizujte stránku portálu a zkuste to znovu po další synchronizaci. Výchozí cyklus synchronizace je 30 minut. 


**Č.** Co když by měl **existující objekt** být objekt, který se má odstranit?  
**Určitého.** Pokud by měl být **existující objekt** odstraněn, proces nezahrnuje změnu **zdrojového ukotvení**. Obvykle ho můžete opravit z místní služby Active Directory. 


**Č.** Jaké oprávnění uživatel potřebuje k použití opravy?  
**Určitého.** **Globální správce**nebo **Přispěvatel** z Azure RBAC má oprávnění pro přístup k procesu diagnostiky a řešení potíží.


**Č.** Je nutné nakonfigurovat Azure AD Connect nebo aktualizovat agenta Azure AD Connect Health pro tuto funkci?  
**Určitého.** Ne, proces diagnostiky je kompletní cloudová funkce.


**Č.** Pokud je odstraněn stávající objekt, bude proces diagnostiky znovu aktivovat objekt.  
**Určitého.** Ne, Oprava nebude aktualizovat atributy objektů kromě **zdrojového ukotvení**.