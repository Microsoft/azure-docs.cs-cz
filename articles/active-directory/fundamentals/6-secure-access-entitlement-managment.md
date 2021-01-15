---
title: Správa externího přístupu pomocí Azure Active Directory správy nároků
description: Jak používat správu nároků Azure Active Directory jako součást celkového plánu zabezpečení externího přístupu.
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
ms.openlocfilehash: 9de0ce5e83b33cd793953e2b863f26dffafe58ee
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222390"
---
# <a name="manage-external-access-with-entitlement-management"></a>Správa externího přístupu se správou oprávnění 


[Správa nároků](../governance/entitlement-management-overview.md) je schopnost zásad správného řízení identity, která organizacím umožňuje spravovat životní cyklus identit a přístupu tím, že automatizuje pracovní postupy pro žádosti o přístup, přiřazení přístupu, recenze a vypršení platnosti. Správa nároků umožňuje delegovaným jiným uživatelům než správcům vytvářet [balíčky přístupu](../governance/entitlement-management-overview.md) , ke kterým můžou žádat přístup externí uživatelé z jiných organizací. Jeden a více pracovních postupů schvalování s více fázemi je možné nakonfigurovat k vyhodnocení požadavků a [zřízení](../governance/what-is-provisioning.md) uživatelů pro časově omezený přístup s opakovanými recenzemi. Správa nároků umožňuje zřizování a rušení zřízení externích účtů na základě zásad.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Klíčové pojmy pro povolení správy nároků

Následující klíčové koncepty jsou důležité pro pochopení správy nároků.

### <a name="access-packages"></a>Přístup k balíčkům

[Přístupový balíček](../governance/entitlement-management-overview.md) je základem správy nároků. Balíčky pro přístup jsou skupiny prostředků řízených zásadami, které uživatel potřebuje ke spolupráci na projektu, nebo dělat jiné úkoly. Například balíček pro přístup může zahrnovat:

* přístup ke konkrétním webům SharePointu.

* podnikové aplikace včetně vašich vlastních interních a SaaS aplikací, jako je Salesforce.

* Kanály Microsoft Teams.

* Microsoft 365 skupiny 

### <a name="catalogs"></a>Katalogy

Přístup k balíčkům se nachází v [katalozích](../governance/entitlement-management-catalog-create.md). Katalog vytvoříte v případě, že chcete seskupit související prostředky a přistupovat k balíčkům a delegovat možnost jejich správy. Nejdřív přidáte prostředky do katalogu a pak můžete tyto prostředky přidat do balíčků. Například můžete chtít vytvořit katalog "finance" a [delegovat jeho správu](../governance/entitlement-management-delegate.md) na člena finančního týmu. Tato osoba pak může [Přidat prostředky](../governance/entitlement-management-catalog-create.md), vytvořit balíčky pro přístup a spravovat schválení přístupu pro tyto balíčky.

Následující diagram znázorňuje typický životní cyklus zásad správného řízení pro externí uživatele, který získá přístup k balíčku přístupu, který má vypršení platnosti.

![Diagram cyklu zásad správného řízení externích uživatelů](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Externí přístup samoobslužné služby

Pomocí [portálu Azure AD](../governance/entitlement-management-request-access.md) pro přístup k balíčkům přístupu můžete získat přístup k externím uživatelům, aby si mohli vyžádat přístup. Zásady určují, kdo může žádat o přístup k balíčku. Určujete, kdo smí žádat o přístup k balíčku:

* Konkrétní [propojené organizace](../governance/entitlement-management-organization.md)

* Všechny nakonfigurované připojené organizace

* Všichni uživatelé ze všech organizací

* Členové nebo uživatelé typu host už ve vašem tenantovi

### <a name="approvals"></a>Schvalování   
Přístup k balíčkům může zahrnovat povinné schválení přístupu. **Vždy implementujte procesy schvalování pro externí uživatele**. Schválení může být schválení jedním nebo několika fázemi. Schválení jsou určena zásadami. Pokud ke stejnému balíčku potřebují přístup interní i externí uživatelé, pravděpodobně nastavíte různé zásady přístupu pro různé kategorie propojených organizací a pro interní uživatele.

### <a name="expiration"></a>Konec platnosti  
Přístup k balíčkům může zahrnovat datum vypršení platnosti. Vypršení platnosti může být nastaveno na určitý den nebo uživateli udělit konkrétní počet dní pro přístup. Po vypršení platnosti balíčku pro přístup a uživatel nemá k dispozici jiný přístup, objekt uživatele hosta B2B, který představuje uživatele, lze odstranit nebo zablokovat při přihlašování. Doporučujeme, abyste vynutili vypršení platnosti balíčků přístupu pro externí uživatele. Ne všechny balíčky přístupu mají vypršení platnosti. Pro ty, které nemusíte, zkontrolujte, že provádíte kontroly přístupu.

### <a name="access-reviews"></a>Kontroly přístupu

Přístup k balíčkům může vyžadovat pravidelné kontroly [přístupu](../governance/manage-guest-access-with-access-reviews.md), které vyžadují vlastníka balíčku nebo zmocnění k tomu, aby uživatelé měli přístup k nepřetržité potřebě uživatelů. 

Než nastavíte kontrolu, určete následující.

* Kdo

   * Jaká jsou kritéria pro pokračování přístupu?

   * Kdo jsou určení kontroloři?

* Jak často mají naplánované recenze probíhat?

   * Mezi integrované možnosti patří měsíční, čtvrtletní, dlouhodobě nebo každý rok. 

   * Pro balíčky, které podporují externí přístup, doporučujeme čtvrtletní a častější. 

 

> [!IMPORTANT]
> Kontroly přístupu balíčků přístupu kontrolují jenom přístup udělený prostřednictvím správy nároků. Proto je nutné nastavit další procesy pro kontrolu jakéhokoli přístupu poskytnutého externím uživatelům mimo správu nároků.

Další informace o kontrolách přístupu najdete v tématu [Plánování nasazení kontrol přístupu Azure AD](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Použití automatizace ve správě nároků

[Funkce správy nároků můžete provádět pomocí Microsoft Graph](https://docs.microsoft.com/graph/tutorial-access-package-api), včetně

* [Spravovat balíčky pro přístup](https://docs.microsoft.com/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Správa kontrol přístupu](https://docs.microsoft.com/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Správa připojených organizací](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Spravovat nastavení správy nároků](https://docs.microsoft.com/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Doporučení 

Doporučujeme, abyste zvyklí řídit externí přístup se správou nároků.

**Pro projekty s jedním nebo více obchodními partnery [vytvořte a používejte přístupové balíčky](../governance/entitlement-management-access-package-create.md) a zajistěte uživatelům přístup k prostředkům na základě těchto obchodních partnerů**. 

* Pokud již máte uživatele B2B v adresáři, můžete je také přímo přiřadit k příslušným balíčkům pro přístup.

* Přístup můžete přiřadit v [Azure Portal](../governance/entitlement-management-access-package-assignments.md)nebo prostřednictvím [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Pomocí nastavení zásad správného řízení identity Odeberte uživatele z adresáře, když vyprší platnost jejich balíčků pro přístup**.

![Snímek obrazovky s konfigurací řízení životního cyklu externích uživatelů](media/secure-external-access/6-manage-external-lifecycle.png)

Tato nastavení se vztahují pouze na uživatele, kteří byli připojeni prostřednictvím správy nároků.

**[Delegovaná Správa katalogů a přístup k balíčkům](../governance/entitlement-management-delegate.md) vlastníkům firmy a další informace o tom, kdo má mít přístup**

![Snímek obrazovky s konfigurací katalogu.](media/secure-external-access/6-catalog-management.png)

**[Vynutili vypršení platnosti balíčků přístupu](../governance/entitlement-management-access-package-lifecycle-policy.md) , ke kterým mají přístup externí uživatelé.**


![Snímek obrazovky s konfigurací vypršení platnosti balíčku pro přístup](media/secure-external-access/6-access-package-expiration.png)

* Pokud znáte koncové datum balíčku přístupu založeného na projektu, použijte k nastavení konkrétního data datum zapnuto. 

* V opačném případě doporučujeme, abyste vypršení platnosti ne365 dnů, pokud se nejedná o neznámou dobu zapojení do více let.

* Umožněte uživatelům, aby rozšířili přístup.

* Vyžaduje schválení pro udělení tohoto rozšíření.

**[Vyvynuťte kontroly přístupu balíčků](../governance/manage-guest-access-with-access-reviews.md) , aby nedocházelo k nevhodnému přístupu pro hosty.**

![Snímek obrazovky s vytvořením nového balíčku pro přístup](media/secure-external-access/6-new-access-package.png)

* Vymáhat recenze čtvrtletně.

* Pro projekty citlivé na dodržování předpisů nastavte kontrolory na konkrétní revidující místo automatického přezkoumání externích uživatelů. Pro uživatele, kteří mají přístup k správcům balíčků, je vhodným místem pro zahájení kontrolorů. 

* U méně citlivých projektů se uživatelům, kteří si samy kontrolují, omezí zátěž v organizaci, aby odebrala přístup od uživatelů, kteří už nejsou v jejich domovské organizaci.

Další informace najdete v tématu [řízení přístupu pro externí uživatele ve správě nároků Azure AD](../governance/entitlement-management-external-users.md) . 

### <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou nároků](6-secure-access-entitlement-managment.md) (jste tady.)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)

 

 
