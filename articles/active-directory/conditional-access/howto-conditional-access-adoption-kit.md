---
title: Podmíněný přístup k přijetí kit – Azure Active Directory
description: Využití podmíněného přístupu Azure AD pro přístup k prostředkům
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387570"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Přijetí podmíněný přístup Azure AD

Ve světě upřednostňujícím mobilní a cloud na prvním uživatelé měli přístup k prostředkům vaší organizace odkudkoli pomocí různých typů zařízení a aplikací. V důsledku toho stačí zaměříte na to, kdo má přístup k prostředku už není dostatečná. Můžete řídit, kdo má přístup a určit, kde uživatel je a jaké zařízení se používá a spoustu dalších věcí.

K poskytování tohoto ovládacího prvku **podmíněného přístupu Azure Active Directory (AD)** umožňuje zadat podmínky, každý uživatel, musí splňovat pro přístup k aplikaci, jako je ověřování službou Multi-Factor Authentication (MFA). Pomocí zásad podmíněného přístupu jak Autorizovaní uživatelé (uživatelé, kterým byl udělen přístup ke cloudové aplikaci) řídí přístup ke cloudovým aplikacím za určitých podmínek. Odkazovat na [co je podmíněný přístup v Azure Active Directory](overview.md#conditional-access-policies) Další informace.

## <a name="key-benefits"></a>Klíčové výhody

Jsou klíčové výhody používání podmíněného přístupu Azure AD:

* **Zvyšte produktivitu:** Zásady podmíněného přístupu (CA) můžete cílit na bod, ve kterém jsou uživatelé vyzváni k použít vícefaktorové ověřování, mají přístup zablokován nebo se musí používat důvěryhodná zařízení. Například můžete nastavit zásady, jako je například pouze by uživatelé museli vícefaktorové ověřování do aplikace, když mimo podnikovou síť. Snižuje požadavky na vícefaktorové ověřování zajišťuje uživatelům vyšší produktivitu, než pokud mají pro vícefaktorové ověřování pokaždé, když se přihlásí. Podmíněný přístup Azure AD navíc umožňuje určit zásady jednotlivým uživatelům a také vytvoří zásady specifické pro aplikace.
* **Řízení rizik:** Povolení zásad podmíněného přístupu umožňuje ochranu cloudových identit, možnosti řízení přístupu na základě rizik a podpora nativního ověřování službou Multi-Factor Authentication. Párování podmíněného přístupu s identity protection můžete zadat při přístupu k aplikaci je blokováno nebo gated.
* **Adresa dodržování předpisů a zásad správného řízení:** Auditování požadavky na přístup a jejich schvalování, aplikace a principy používání aplikací je jednodušší díky službě Azure AD, protože podporuje protokoly auditu nativní pro každou žádost o přístup aplikace provést. Auditování zahrnuje identitu žadatele o požadované datum, obchodní odůvodnění, stav schválení a schvalovatele identity. Tato data jsou také k dispozici z rozhraní API, která vám umožní import těchto dat do incidentu zabezpečení a monitorování událostí (SIEM) systému podle výběru.
* **Správa nákladů:** Přesunutí zásady přístupu ke službě Azure AD snižuje závislost na vlastní nebo v místním řešení, jako je Active Directory Federation Services (ADFS) pro podmíněný přístup, snížit náklady na provozování tuto infrastrukturu.

## <a name="customer-case-studies"></a>Zákaznické případové studie

Zjistěte, jak většina organizací pomocí podmíněného přístupu Azure AD definovat a implementovat rozhodovat o automatizovaného přístupu ke cloudovým aplikacím na základě podmínek. Následující doporučené scénáře ukazují, jak splnění těchto požadavků zákazníků.

* [**Wipro** jednotky mobilní produktivity s využitím nástroje zabezpečení Microsoft cloudu k vylepšení zákaznických implementací.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zásady podmíněného přístupu ve službě Azure AD povolili společnosti sdílet dokumenty, prostředky a aplikace s důvěryhodné entity mimo---, kteří můžou používat svoje vlastní přihlašovací údaje---zachováním potřebné kontroly nad své vlastní firemní data.
* [**Accenture** chrání jeho přesun do cloudu pomocí Microsoft Cloud App security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) vyhodnocování Accenture [Conditional Access App Control](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) funkce Cloud App Security, který používá Azure Active Adresář podmíněný přístup k přístupu k aplikacím brána na základě určitých podmínek. LePenske říká, že tato funkce může být užitečné, například povolení přístupu jen pro čtení souborů při zákazu stahování.
* [**Aramex** doručování omezené – globální společnost Logistika a doprava vytvoří office připojených ke cloudu s řešením pro správu identit a přístupu](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Zejména těžké se vzdálení zaměstnanci společnosti Aramex bylo zajistit zabezpečený přístup. Společnost je nyní uplatňováním podmíněného přístupu, aby tyto vzdálení zaměstnanci přistupují ke svým aplikacím SaaS z mimo síť. Pravidla podmíněného přístupu se rozhodněte, jestli chcete vynutit ověřování Multi-Factor Authentication, poskytuje pouze lidé správná přístupová práva.

Další informace týkající se zákazníky a partnery podmíněného přístupu Azure AD, najdete v tématu - [najdete v článku s Azure dělají úžasné věci lidi](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Oznámení

Azure AD obdrží vylepšení průběžně. Chcete-li udržovat aktuální stav pomocí nejnovější vývoj, přečtěte si téma [co je nového ve službě Azure Active Directory?](../fundamentals/whats-new.md)

Nejnovější blogy technické komunitě a divize Microsoft Identity:

* 24. září 2018 [podmíněným přístupem Azure Active Directory v Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21. září 2018, [podmíněného přístupu Azure AD vlastní ovládací prvky jsou ve verzi public preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21. září 2018 [je teď dostupná podpora podmíněného přístupu Azure AD pro omezený přístup pomocí Microsoft Cloud App Security](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21. září 2018, [podmíněný přístup Azure AD: Managed browser podporu pro platformy iOS/Android teď ve verzi preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21. září 2018, [podmíněného přístupu Azure AD pro kód země se ve verzi public preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21. září 2018, [Azure AD podmínky použití nyní k dispozici](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Studijní materiály

Postupujte podle níže odkazy, které získáte přehled o tom, jak služba Azure functions podmíněný přístup služby AD.

* Další informace "[co je podmíněný přístup v Azure Active Directory?](overview.md)"
* Znáte "[jaké jsou podmínky v Azure Active Directory podmíněného přístupu?](conditions.md)"
* Znáte "[co je podmínka umístění v Azure Active Directory podmíněného přístupu?](location-condition.md)"
* Znáte "[co jsou přístup ovládací prvky v Azure Active Directory podmíněného přístupu?](controls.md)"
* Vyhledejte "[co co Pokud je nástroj v Azure Active Directory podmíněného přístupu?"](what-if-tool.md)
* Postupujte podle [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md)

Kromě toho najdete pokyny, které chrání přístup ke všem službám, které jsou integrované s Azure Active Directory prostřednictvím následujících odkazů.

* [Co je základní ochranu (preview)?](baseline-protection.md) Základní ochranu zajistíte, že aspoň základní úroveň zabezpečení povolené ve vašem prostředí Azure Active Directory.
* [Identity a přístupu k zařízením konfigurace](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Popisuje, jak nakonfigurovat zabezpečený přístup ke cloudovým službám prostřednictvím řešení Enterprise Mobility + Security produktů díky implementaci doporučené prostředí a konfigurace, včetně předepsané sadu zásad podmíněného přístupu a souvisejících možností.
* [Referenční informace k nastavení Azure Active Directory podmíněného přístupu](technical-reference.md). Další informace:
   * Jaké aplikace používají podmíněný přístup?
   * Jaké služby jsou povolené při použití podmíněného přístupu?
* [Zapnout zásady podmíněného přístupu Azure Active Directory pro přístup k zabezpečené uživatele](https://www.youtube.com/watch?v=eLAYBwjCGoA). Podívejte se na tomto videu se dozvíte, jak podmíněný přístup hraje roli v úlohách jiné organizace a Mobility Suite.

### <a name="training-videos"></a>Školicí videa

**Podmíněný přístup v Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Podmíněný přístup podle zařízení**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Povolit Azure Active Directory pro podmíněný přístup pro přístup k zabezpečené uživatele**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Online kurzy

Další informace naleznete na následující kurzy podmíněného přístupu a další v [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Návrh správy identit v Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Tento kurz vás provede klíče položky, které potřebujete znát při návrhu řešení správy identit s Azure AD." Podmíněný přístup Azure AD se věnujeme v "Použití role a řízení přístupu pomocí služby Azure AD" modulu.

* Pluralsight.com: [Návrh ověřování pro Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Tento kurz vysvětluje, jak používat Azure AD pro všechny požadavky ověřování cloudového řešení." Podmíněný přístup Azure AD se věnujeme v modulu "Ověřování požadavků pro různé scénáře".

* Pluralsight.com: [Návrh autorizace pro Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Tento kurzu se dozvíte, jaké možnosti autorizace, které jsou k dispozici v Azure a Azure AD." Podmíněný přístup Azure AD se věnujeme v modulu "Autorizace pomocí Azure Resource Manageru a Azure AD".

### <a name="books"></a>Knihy

* O'Reilly - [implementace řešení Azure - druhé vydání.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Začít pracovat se službami Azure a zjistěte, jak je implementovat ve vaší organizaci. Podmíněný přístup Azure AD najdete v kapitole [nasazení a synchronizace služby Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [zvládnutí infrastrukturní služby Microsoft Azure](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Tady je vše, co je potřeba pochopit, vyhodnocení, nasadit a spravovat prostředí, které využívají Microsoft Azure."

## <a name="white-papers"></a>Dokumenty white paper

* Publikování 18. prosince 2018, [vytvořit strategie správy řízení odolné přístupu v Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Tento dokument obsahuje pokyny ke strategiím, což je organizace může přijmout zajistit odolnost, aby se snížilo riziko uzamčení při přerušení nepředvídatelné.

* Publikování 18. září 2018 [zdroje pro migraci aplikací do Azure Active Directory](../manage-apps/migration-resources.md)
   * Tento dokument White Paper obsahuje seznam prostředků, který vám pomůže při migraci přístup k aplikaci a ověřování do Azure Active Directory (Azure AD).

* Publikování 12. července 2018 [Azure zabezpečení a dodržování předpisů: PaaS webovou aplikaci hostování pro oficiální úlohy Spojené království](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Plány Azure se skládají z dokumentů s pokyny k a automatizace šablony, které nasazení cloudové architektury, které nabízejí řešení pro scénáře, které mají akreditace požadavky.

## <a name="guidance-for-it-administrators"></a>Pokyny pro správce IT

Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu. Odkazovat na [oprávnění role správce v Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Jako správce IT, použijte [podmíněného přístupu Azure AD](overview.md) vyžadují, aby uživatelé ověření pomocí Azure Multi-Factor Authentication, přihlášení z důvěryhodné sítě nebo důvěryhodné zařízení.

Tady jsou užitečné odkazy, které vám pomůžou začít:

* [Osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md)
* [Kontroly přístupu pomocí služby Azure AD ke správě uživatelů, které jsou vyloučené ze zásad podmíněného přístupu](../governance/conditional-access-exclusion.md)
* [Postup: Plánování nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md)
* [Rychlé zprovoznění: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace pomocí Azure Active Directory podmíněného přístupu](app-based-mfa.md)
* [Rychlé zprovoznění: Vyžadovat podmínky použití přijmout před přístupem k cloudových aplikací](require-tou.md)
* [Rychlé zprovoznění: Při zjištění ohrožení relace pomocí Azure Active Directory podmíněného přístupu blokovat přístup](app-sign-in-risk.md)
* [Nejčastější dotazy k Azure AD podmíněného přístupu](faqs.md)
   * Máte další otázky, můžete taky zobrazit [fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Pokud nemůžete najít odpověď na problém, jsou vždy k dispozici při další naše týmy podpory. Použití [požádejte podporu Microsoftu](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Kurzy

* [**Rychlý start: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace pomocí Azure Active Directory podmíněného přístupu**](app-based-mfa.md)
   * Tento rychlý start ukazuje, jak nakonfigurovat zásadu podmíněného přístupu Azure AD, která vyžaduje ověření službou Multi-Factor Authentication pro vybranou cloudovou aplikaci ve vašem prostředí.

* [**Rychlý start: Vyžadovat podmínky použití přijmout před přístupem k cloudových aplikací**](require-tou.md)
   * Tento rychlý start ukazuje, jak nakonfigurovat zásadu podmíněného přístupu Azure AD, které pro vybranou cloudovou aplikaci ve vašem prostředí vyžaduje podmínky použití přijmout.

* [**Rychlý start: Při zjištění ohrožení relace pomocí Azure Active Directory podmíněného přístupu blokovat přístup**](app-sign-in-risk.md)
   * Tento rychlý start ukazuje, jak nakonfigurovat zásady podmíněného přístupu, které blokuje u přihlášení při zjistil úroveň nakonfigurované rizika přihlášení.

* [Kurz: **Migrovat klasické zásady, která vyžaduje ověření službou Multi-Factor Authentication na webu Azure Portal**](policy-migration-mfa.md)
   * Tento kurz ukazuje, jak migrovat klasické zásady, která vyžaduje vícefaktorové ověřování (MFA) pro cloudové aplikace.

## <a name="end-user-readiness-and-communication"></a>Připravenost koncových uživatelů a komunikace

Podmíněný přístup použije další možnosti služby Azure AD, které může mít vliv na prostředí koncového uživatele. Například můžete použít Azure Multi-Factor authentication k povolení silného ověřování pro uživatele. V takovém případě budete používat šablony koncových uživatelů Azure mfa.

## <a name="next-steps"></a>Další postup

* Zahájení nasazení se [plánování dokumentaci k nasazení podmíněného přístupu](plan-conditional-access.md).
