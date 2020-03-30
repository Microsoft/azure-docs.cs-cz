---
title: Azure AD Connect Health – diagnostika duplicitních chyb synchronizace atributů | Dokumenty společnosti Microsoft
description: Tento dokument popisuje proces diagnostiky duplicitních chyb synchronizace atributů a potenciální opravu scénářů osamocených objektů přímo z portálu Azure.
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
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48ed9abf3e088e2581a3dd81b7c89e6b99da3ceb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897194"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostika a oprava chyb synchronizace kvůli duplicitním atributům

## <a name="overview"></a>Přehled
Další krok ke zvýraznění chyb synchronizace azure active directory (Azure AD) Connect Health zavádí samoobslužné nápravy. Odstraňuje duplicitní chyby synchronizace atributů a opravuje objekty, které jsou osamocené z Azure AD.
Funkce diagnostiky má tyto výhody:
- Poskytuje diagnostický postup, který zužuje duplicitní chyby synchronizace atributů. A to dává konkrétní opravy.
- Platí opravu pro vyhrazené scénáře z Azure AD k vyřešení chyby v jednom kroku.
- K povolení této funkce není nutný žádný upgrade ani konfigurace.
Další informace o azure ad naleznete v [tématu synchronizace identit a odolnost proti duplicitní atribut](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problémy
### <a name="a-common-scenario"></a>Běžný scénář
Když **quarantinedAttributeValueMustMustUnique** a **AttributeValueMustMustUnique** chyby synchronizace dojít, je běžné zobrazit konflikt **UserPrincipalName** nebo **proxy adresy** v Azure AD. Chyby synchronizace můžete vyřešit aktualizací konfliktního zdrojového objektu z místní strany. Chyba synchronizace bude vyřešena po další synchronizaci. Tento obrázek například označuje, že dva uživatelé mají konflikt jejich **UserPrincipalName**. Oba jsou **Joe.J\@contoso.com**. Konfliktní objekty jsou umístěny do karantény ve službě Azure AD.

![Diagnostikovat běžný scénář chyby synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scénář osamoceného objektu
V některých případě může zjistit, že existující uživatel ztratí **zdrojové kotvy**. K odstranění zdrojového objektu došlo v místní službě Active Directory. Ale změna signálu odstranění nikdy synchronizovány do Služby Azure AD. Tato ztráta se stane z důvodů, jako jsou problémy s synchronizačním motorem nebo migrace domény. Když stejný objekt obnoví nebo znovu vytvoří, logicky existující uživatel by měl být uživatel synchronizovat ze **zdrojové kotvy**. 

Pokud je existující uživatel objekt pouze pro cloud, můžete také zobrazit konfliktní ho uživatele synchronizované s Azure AD. Uživatel nemůže být spárován v synchronizaci s existujícím objektem. Neexistuje žádný přímý způsob, jak přemapovat **zdrojovou kotvu**. Další informace o [existující znalostní bázi](https://support.microsoft.com/help/2647098). 

Jako příklad existující objekt ve službě Azure AD zachová licenci Joe. Nově synchronizovaný objekt s jinou **zdrojovou kotvou** se vyskytuje ve stavu duplicitního atributu ve službě Azure AD. Změny pro Joea v místním adresáři Active Directory se nepoužijí na Původního uživatele (existující ho objektu) Joea ve službě Azure AD.  

![Diagnostikovat scénář osamocených objektů při chybě synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostické kroky a kroky řešení potíží v části Stav připojení 
Funkce diagnostiky podporuje objekty uživatelů s následujícími duplicitními atributy:

| Název atributu | Typy chyb synchronizace|
| ------------------ | -----------------|
| UserPrincipalName | KaranténaAttributeValueMustMustOb nebo AttributeValueMustMustUnique nebo AttributeValueMustMustUnique | 
| ProxyAddresses | KaranténaAttributeValueMustMustOb nebo AttributeValueMustMustUnique nebo AttributeValueMustMustUnique | 
| Adresa SipProxyAddress | AttributeValueMustBeUnique | 
| Identifikátor onpremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Pro přístup k této funkci je vyžadováno oprávnění **globálního správce** nebo oprávnění **přispěvatele** z nastavení RBAC.
>

Podle pokynů z webu Azure Portal zúžíte podrobnosti o chybách synchronizace a poskytněte konkrétnější řešení:

![Synchronizovat kroky diagnostiky chyb](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Na webu Azure Portal udělejte několik kroků k identifikaci konkrétních opravitelných scénářů:  
1.  Zkontrolujte sloupec **Diagnostikovat stav.** Stav ukazuje, jestli existuje možný způsob, jak opravit chybu synchronizace přímo z Azure Active Directory. Jinými slovy, existuje tok řešení potíží, který může zúžit případ chyby a potenciálně jej opravit.

| Status | Co to znamená? |
| ------------------ | -----------------|
| Nespuštěno | Ještě jste nenavštívili tento diagnostický proces. V závislosti na výsledku diagnostiky existuje potenciální způsob, jak opravit chybu synchronizace přímo z portálu. |
| Je vyžadována ruční oprava. | Chyba neodpovídá kritériím dostupných oprav z portálu. Konfliktní typy objektů nejsou uživatelé nebo jste již prošli diagnostickými kroky a z portálu nebylo k dispozici žádné řešení opravy. V druhém případě je oprava z místní strany stále jedním z řešení. [Přečtěte si další informace o místních opravách](https://support.microsoft.com/help/2647098). | 
| Čekající synchronizace | Byla použita oprava. Portál čeká na další cyklus synchronizace vymazat chybu. |

  >[!IMPORTANT]
  > Sloupec stav diagnostiky se po každém cyklu synchronizace resetuje. 
  >

1. Pod podrobnostmi o chybě vyberte tlačítko **Diagnostikovat.** Odpovíte na několik otázek a identifikujete podrobnosti o chybě synchronizace. Odpovědi na otázky pomáhají identifikovat případ osamoceného objektu.

1. Pokud se na konci diagnostiky zobrazí tlačítko **Zavřít,** není na základě vašich odpovědí k dispozici žádná rychlá oprava z portálu. Viz řešení uvedené v posledním kroku. Opravy z místního prostředí jsou stále řešení. Vyberte tlačítko **Zavřít.** Stav aktuální chyby synchronizace se přepne na **ruční opravu požadováno**. Stav zůstane během aktuálního cyklu synchronizace.

1. Po identifikaci případu osamoceného objektu můžete opravit duplicitní atributy chyby synchronizace přímo z portálu. Chcete-li proces spustit, vyberte tlačítko **Použít opravu.** Stav aktuální chyby synchronizace se aktualizuje na **čekající synchronizaci**.

1. Po dalším cyklu synchronizace by měla být chyba odebrána ze seznamu.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpovědět na otázky diagnostiky 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Existuje uživatel ve vaší místní službě Active Directory?

Tato otázka se pokusí identifikovat zdrojový objekt existujícího uživatele z místní služby Active Directory.  
1. Zkontrolujte, jestli služba Azure Active Directory obsahuje objekt s poskytnutým **uživatelem UserPrincipalName**. Pokud ne, odpovězte **ne**.
2. Pokud ano, zkontrolujte, zda je objekt stále v oboru pro synchronizaci.  
   - Hledání v prostoru konektoru Azure AD pomocí DN.
   - Pokud je objekt nalezen ve stavu **Čekající přidání,** odpovězte **ne**. Azure AD Connect nemůže připojit objekt ke správnému objektu Azure AD.
   - Pokud objekt nebyl nalezen, odpovězte **Ano**.

V těchto příkladech se otázka pokusí zjistit, zda **joe jackson** stále existuje v místním adresáři Active Directory.
V **běžném scénáři**jsou uživatelé **Joe Johnson** a **Joe Jackson** přítomni v místním adresáři Active Directory. Objekty v karanténě jsou dva různí uživatelé.

![Diagnostikovat běžný scénář chyby synchronizace](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Pro **scénář osamocených objektů**je v místní službě Active Directory k dispozici pouze jeden uživatel **Joe Johnson:**

![Diagnostikovat chybu synchronizace osamocený objekt *existuje uživatel* scénář](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Patří oba tyto účty stejnému uživateli?
Tato otázka zkontroluje příchozí konfliktní uživatele a existující objekt uživatele ve službě Azure AD, aby zjistil, zda patří ke stejnému uživateli.  
1. Konfliktní objekt je nově synchronizován do služby Azure Active Directory. Porovnejte atributy objektů:  
   - Zobrazovaný název
   - Hlavní název uživatele
   - ID objektu
2. Pokud se službě Azure AD nepodaří je porovnat, zkontrolujte, zda služba Active Directory obsahuje objekty s poskytnutými **userprincipalnames**. Odpověď **Ne,** pokud najdete obojí.

V následujícím příkladu dva objekty patří stejnému uživateli **Joe Johnson**.

![Diagnostikovat chybu synchronizace osamocený objekt *stejný uživatel* scénář](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co se stane po použití opravy ve scénáři osamoceného objektu
Na základě odpovědí na předchozí otázky se zobrazí tlačítko **Použít opravu,** když je k dispozici oprava z Azure AD. V takovém případě místní objekt synchronizuje s neočekávaným objektem Azure AD. Tyto dva objekty jsou mapovány pomocí **zdrojové kotvy**. Změna **Použít opravu** provede tyto nebo podobné kroky:
1. Aktualizuje **zdrojovou kotvu** na správný objekt ve službě Azure AD.
2. Odstraní konfliktní objekt ve službě Azure AD, pokud je k dispozici.

![Diagnostikovat chybu synchronizace po opravě](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> Změna **Použít opravu** platí pouze pro případy osamocených objektů.
>

Po předchozích krocích může uživatel přistupovat k původnímu prostředku, což je odkaz na existující objekt. Hodnota **Diagnostikovat stav** v zobrazení seznamu se aktualizuje na **čekající synchronizaci**. Chyba synchronizace bude vyřešena po další synchronizaci. Stav připojení již nebude zobrazovat vyřešenou chybu synchronizace v zobrazení seznamu.

## <a name="failures-and-error-messages"></a>Chyby a chybové zprávy
**Uživatel s konfliktním atributem je ve službě Azure Active Directory obnovitelné. Ujistěte se, že uživatel je pevně odstraněn před opakováním.**  
Uživatel s konfliktním atributem ve službě Azure AD by měl být před použitím opravy vyčištěn. Podívejte [se, jak odstranit uživatele trvale ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) před opakováním opravy. Uživatel bude také automaticky trvale odstraněn po 30 dnech v obnovitelném odstraněném stavu. 

**Aktualizace zdrojové kotvy na cloudového uživatele ve vašem tenantovi není podporována.**  
Cloudový uživatel ve službě Azure AD by neměl mít zdrojovou kotvu. Aktualizace zdrojové kotvy není v tomto případě podporována. Ruční oprava je vyžadována z místních prostor. 

## <a name="faq"></a>Nejčastější dotazy
**Otázka:** Co se stane, pokud se nezdaří spuštění **apply fix?**  
**A.** Pokud se spuštění nezdaří, je možné, že Azure AD Connect běží chyba exportu. Aktualizujte stránku portálu a opakujte akci po další synchronizaci. Výchozí cyklus synchronizace je 30 minut. 


**Otázka:** Co když **existující objekt** by měl být objekt, který má být odstraněn?  
**A.** Pokud existující **objekt** by měl být odstraněn, proces nezahrnuje změnu **ukotvení zdroje**. Obvykle ji můžete opravit z místní služby Active Directory. 


**Otázka:** Jaké oprávnění uživatel potřebuje k použití opravy?  
**A.** **Globální správce**nebo **přispěvatel** z nastavení RBAC má oprávnění k přístupu k procesu diagnostiky a řešení potíží.


**Otázka:** Musím pro tuto funkci nakonfigurovat Azure AD Connect nebo aktualizovat agenta Azure AD Connect Health?  
**A.** Ne, proces diagnostiky je kompletní funkce založená na cloudu.


**Otázka:** Pokud je existující objekt obnovitelné odstranění, bude proces diagnostiky objekt znovu aktivní?  
**A.** Ne, oprava neaktualizuje jiné atributy objektu než **Zdrojová kotva**.
