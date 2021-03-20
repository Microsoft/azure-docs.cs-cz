---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93135324"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Aplikace cloudového HR pro Azure Active Directory zřizování uživatelů

V minulosti se zaměstnanci IT spoléhali na ruční metody pro vytváření, aktualizaci a odstraňování zaměstnanců. Používaly metody, jako je nahrání souborů CSV nebo vlastních skriptů k synchronizaci dat zaměstnanců. Tyto zřizovací procesy jsou náchylné k chybám, nezabezpečené a nenáročné na správu.

Aby bylo možné spravovat životní cykly identit zaměstnanců, dodavatelů nebo podmíněných pracovníků, [Služba zřizování uživatelů Azure Active Directory (Azure AD)](../articles/active-directory/app-provisioning/user-provisioning.md) nabízí integraci s aplikacemi pro lidské prostředky (HR) založené na cloudu. Mezi příklady aplikací patří Workday nebo SuccessFactors.

Azure AD pomocí této integrace povolí následující pracovní postupy aplikace cloudového HR:

- **Zřizování uživatelů pro službu Active Directory:** Zřídí vybrané sady uživatelů z aplikace cloudového HR do jedné nebo víc domén služby Active Directory.
- **Zřizování pouze cloudových uživatelů v Azure AD:** Ve scénářích, kdy se služba Active Directory nepoužívá, Zřiďte uživatele přímo z aplikace cloudového HR do Azure AD.
- **Zapište zpátky do aplikace cloudového HR:** Napište e-mailové adresy a atributy uživatelského jména z Azure AD zpátky do aplikace Cloude HR.


## <a name="enabled-hr-scenarios"></a>Povolené scénáře pro personální oddělení

Služba zřizování uživatelů Azure AD umožňuje automatizaci následujících scénářů správy životního cyklu identity na základě lidských zdrojů:

- **Nové zařazení zaměstnanců:** Když se do aplikace cloudového HR přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory a Azure AD s možností napsat zpátky e-mailovou adresu a atributy uživatelského jména do aplikace cloudového HR.
- **Aktualizace atributů a profilů zaměstnanců:** Když se v aplikaci cloudového HR aktualizuje záznam zaměstnance, jako je jméno, titul nebo manažer, jejich uživatelský účet se automaticky aktualizuje ve službě Active Directory a v Azure AD.
- **Ukončení zaměstnanců:** Když se zaměstnanec v aplikaci cloudového HR ukončí, je jeho uživatelský účet automaticky zakázaný ve službě Active Directory a Azure AD.
- **Pracovní zařazení zaměstnanců:** Po opětovném zařazení zaměstnance do aplikace cloudové HR se jejich starý účet může automaticky znovu aktivovat nebo znovu zřídit ve službě Active Directory a Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Pro kterou tuto integraci nejlépe vyhovuje?

Integrace aplikace pro cloudovou HR se zřizováním uživatelů Azure AD je ideální pro organizace, které:

- Chcete předem připravené cloudové řešení pro zřizování uživatelů v cloudu.
- Vyžaduje přímé zřizování uživatelů z aplikace cloudového HR do služby Active Directory nebo Azure AD.
- Vyžadovat, aby se uživatelé zřídili pomocí dat získaných z aplikace cloudového HR.
- Vyžadovat, aby se uživatelé synchronizovaly do jedné nebo víc doménových struktur služby Active Directory, domén a organizačních jednotek, a to na základě informací o změně zjištěných v aplikaci cloudového HR.
- K odeslání e-mailu použijte Office 365.


### <a name="key-benefits"></a>Klíčové výhody

Tato schopnost zajišťování IT na základě lidských zdrojů nabízí tyto významné obchodní výhody:

- **Zvýšení produktivity:** Teď můžete automatizovat přiřazování uživatelských účtů a licencí Office 365 a poskytovat přístup ke skupinám klíčů. Automatizace přiřazení poskytuje novým lidem okamžitý přístup ke svým nástrojům úloh a zvyšuje produktivitu.
- **Řízení rizik:** Zabezpečení můžete zvýšit automatizací změn na základě stavu zaměstnanců nebo členství ve skupinách pomocí toku dat z aplikace cloudového HR. Automatizace změn zajišťuje, že identity uživatelů a přístup k klíčovým aplikacím se automaticky aktualizují, když uživatelé přecházejí nebo odejdou z organizace.
- **Řešení dodržování předpisů a zásad správného řízení:** Azure AD podporuje protokoly nativního auditu pro požadavky uživatelů na zřizování prováděné aplikacemi ze zdrojového i cílového systému. Díky auditování můžete sledovat, kdo má přístup k aplikacím z jedné obrazovky.
- **Spravovat náklady:** Automatické zřizování snižuje náklady tím, že se vyhne neefektivitám a lidským chybám přidruženým k ručnímu zřizování. Díky starším a zastaralým platformám omezuje nutnost sestavování řešení pro zřizování uživatelů vytvořených v průběhu času.
