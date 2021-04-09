---
title: Zjištění aktuálního stavu externí spolupráce pomocí Azure Active Directory
description: Naučte se, jak zjistit aktuální stav vaší spolupráce.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553421"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>Zjištění aktuálního stavu externí spolupráce ve vaší organizaci 

Předtím, než zjistíte aktuální stav externí spolupráce, byste měli [určit požadované stav zabezpečení](1-secure-access-posture.md). Považujeme za požadavky vaší organizace na centralizované vs. delegovaný ovládací prvek a všechny relevantní cíle zásad správného řízení, zákon a dodržování předpisů. 

Jednotlivci ve vaší organizaci už pravděpodobně spolupracují s uživateli z jiných organizací. Spolupráce může být prostřednictvím funkcí v aplikacích produktivity, jako je Microsoft 365, e-mailem nebo jiným způsobem sdílení prostředků s externími uživateli. Pilíře vašeho plánu zásad správného řízení se budou považovat za zjišťování. 
*   Uživatelé, kteří zahajují externí spolupráci.
*   externí uživatelé a organizace, se kterými spolupracujete.
*   přístup je udělen externím uživatelům.


## <a name="users-initiating-external-collaboration"></a>Uživatelé, kteří zahajují externí spolupráci

Uživatelé, kteří zahajují externí spolupráci, nejlépe pochopili aplikace, které jsou relevantní pro externí spolupráci, a v případě, že by měl tento přístup končit. Porozumění těmto uživatelům vám pomůže určit, kdo má mít delegovaná oprávnění k pozvání externích uživatelů, vytváření balíčků pro přístup a kompletní kontroly přístupu.

Pokud chcete najít uživatele, kteří aktuálně spolupracují, přečtěte si téma [Microsoft 365 log audit pro aktivity sdílení a žádosti o přístup](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities). Můžete si také projít [protokol auditu Azure AD a podrobnější informace o tom, kdo uživatele B2B pozvaní](../external-identities/auditing-and-reporting.md) do vašeho adresáře.

## <a name="find-current-collaboration-partners"></a>Najít aktuální partnery pro spolupráci

Externí uživatelé můžou být [Uživatelé Azure AD B2B](../external-identities/what-is-b2b.md) (preferovat) s přihlašovacími údaji spravovanými partnerem nebo externími uživateli s místně zřízenými přihlašovacími údaji. Tito uživatelé jsou obvykle (ale ne vždy) označeny jako UserType Host. Můžete vytvořit výčet uživatelů typu Host prostřednictvím [rozhraní Microsoft Graph API](/graph/api/user-list?tabs=http), [powershellu](/graph/api/user-list?tabs=http)nebo [Azure Portal](../enterprise-users/users-bulk-download.md).

### <a name="use-email-domains-and-companyname-property"></a>Použití e-mailových domén a vlastností companyName

Externí organizace je možné určit pomocí názvů domén e-mailových adres externích uživatelů. Pokud se podporují poskytovatelé identity spotřebitelů, jako je Google, nemusí to být možné. V takovém případě doporučujeme napsat atribut companyName, který jednoznačně identifikuje externí organizaci uživatele.

### <a name="use-allow-or-deny-lists"></a>Použít seznamy povolených nebo zakázaných

Zvažte, jestli vaše organizace chce dovolit spolupráci jenom s konkrétními organizacemi. Případně zvažte, jestli vaše organizace chce zablokovat spolupráci s konkrétními organizacemi.  Na úrovni tenanta existuje [seznam povolených nebo odepřených](../external-identities/allow-deny-list.md)dat, který se dá použít k řízení celkových pozvání a uplatnění všech B2B bez ohledu na zdroj (např. týmy, SharePoint a Azure Portal).
Pokud používáte správu nároků, můžete také určit rozsah přístupových balíčků k podmnožině svých partnerů pomocí nastavení konkrétní propojené organizace, jak je uvedeno níže.


![Snímek obrazovky se seznamem povolených odepření při vytváření nového přístupového balíčku](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>Získání přístupu pro externí uživatele

Jakmile budete mít inventář externích uživatelů a organizací, můžete určit přístup k těmto uživatelům pomocí rozhraní Microsoft Graph API, abyste zjistili [členství ve skupině](/graph/api/resources/groups-overview) Azure AD nebo [přímé přiřazení aplikací](/graph/api/resources/approleassignment) ve službě Azure AD.


### <a name="enumerate-application-specific-permissions"></a>Zobrazení výčtu oprávnění specifických pro aplikaci

Může být také možné provést výčet oprávnění specifický pro aplikaci. Pomocí [tohoto skriptu](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)můžete například programově generovat sestavu oprávnění pro SharePoint Online.

Konkrétně můžete prozkoumat přístup ke všem vašim firemním a důležitým firemním aplikacím, abyste měli plně přehled o všech externích přístupech.

### <a name="detect-ad-hoc-sharing"></a>Zjistit sdílení ad hoc
Pokud to vaše e-mailové a síťové plány umožňují, můžete prozkoumat obsah sdílený prostřednictvím e-mailu nebo prostřednictvím neautorizovaných aplikací SaaS (software jako služba). [Ochrana před únikem informací Microsoft 365](/microsoft-365/compliance/data-loss-prevention-policies) pomáhá identifikovat, zabránit a monitorovat náhodné sdílení citlivých informací napříč vaší infrastrukturou Microsoft 365. [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) vám může pomáhat identifikovat používání neautorizovaných aplikací SaaS ve vašem prostředí.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Podívejte se na aktuální stav](2-secure-access-current-state.md) (jste tady.)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)