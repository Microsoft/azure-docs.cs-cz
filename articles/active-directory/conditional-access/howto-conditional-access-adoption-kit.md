---
title: Sada pro přijetí podmíněného přístupu – Azure Active Directory
description: Přijetí podmíněného přístupu Azure AD pro přístup k prostředkům
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
ms.openlocfilehash: daaced0aae595e839f2b2476562fc5caffb63640
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473932"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Přijetí podmíněného přístupu Azure AD

V rámci cloudového a cloudového světa můžou uživatelé přistupovat k prostředkům vaší organizace odkudkoli pomocí různých druhů zařízení a aplikací. Výsledkem je, že se zaměříte na to, kdo má přístup k prostředku, již není dostatek. Můžete určit, kdo má přístup a zjistit, kde je uživatel a jaké zařízení se používá, a mnohem víc.

Chcete-li poskytnout tento ovládací prvek, **podmíněný přístup Azure Active Directory (AD)** umožňuje zadat podmínky, které uživatel musí splnit pro přístup k aplikaci, například Multi-Factor Authentication (MFA). Použití zásad podmíněného přístupu řídí, jak autorizovaní uživatelé (uživatelé s uděleným přístupem ke cloudové aplikaci) přistupují ke cloudovým aplikacím za určitých podmínek. Další informace najdete v tématu věnovaném [podmíněnému přístupu v Azure Active Directory](overview.md) .

## <a name="key-benefits"></a>Klíčové výhody

Klíčové výhody použití podmíněného přístupu Azure AD:

* **Zvýšení produktivity:** Zásady podmíněného přístupu (CA) umožňují cílit na místo, kde se uživatelům zobrazí výzva k použití vícefaktorového ověřování, přístup je blokovaný nebo musí používat důvěryhodné zařízení. Můžete například nastavit zásady, jako je třeba vyžadovat, aby se uživatelé do aplikace při použití v podnikové síti nastavili na MFA. Snížení požadavků MFA zachovává uživatelům větší produktivitu než v okamžiku, kdy se přihlásí. Podmíněný přístup Azure AD navíc umožňuje zadat zásady na základě jednotlivých uživatelů a také vytvářet zásady pro konkrétní aplikace.
* **Řízení rizik:** Když povolíte zásady podmíněného přístupu, zajistíte ochranu identity v cloudu, možnosti řízení přístupu na základě rizik a nativní podporu Multi-Factor Authentication. Zaznamenání podmíněného přístupu pomocí Identity Protection umožňuje definovat, kdy je přístup k aplikaci blokovaný nebo ověřovaný.
* **Řešení dodržování předpisů a zásad správného řízení:** Žádosti o přístup k auditu a jejich schválení pro aplikaci a porozumění celkovému využití aplikací je pro Azure AD jednodušší, protože pro každou prováděnou žádost o přístup k aplikaci podporuje protokoly nativního auditu. Auditování zahrnuje identitu žadatele, požadované datum, obchodní odůvodnění, stav schválení a identitu schvalovatele. Tato data jsou také k dispozici z rozhraní API, které umožní import těchto dat do SIEM (Security incident) a pro sledování událostí.
* **Spravovat náklady:** Přesunutí zásad přístupu do Azure AD snižuje závislosti na vlastních nebo místních řešeních, jako je Active Directory Federation Services (AD FS) (ADFS) pro podmíněný přístup, což snižuje náklady na provozování této infrastruktury.

## <a name="customer-case-studies"></a>Zákaznické případové studie

Zjistěte, jak většina organizací používá podmíněný přístup Azure AD k definování a implementaci automatického rozhodování o řízení přístupu pro přístup ke cloudovým aplikacím na základě podmínek. Následující doporučené příběhy ukazují, jak se tyto požadavky zákazníků splní.

* [Společnost **Wipro** zabezpečí mobilní produktivitu pomocí nástrojů Microsoft Cloud Security Tools a vylepšuje zapojení zákazníků.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Zásady podmíněného přístupu ve službě Azure AD povolily společnosti sdílet dokumenty, prostředky a aplikace s důvěryhodnými externími entitami---, kdo může používat vlastní přihlašovací údaje---a přitom zachovat kontrolu nad svými vlastními podnikovými daty.
* [**Accenture** chrání svůj přechod do cloudu pomocí Microsoft Cloud App Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture vyhodnocuje funkci [řízení podmíněného přístupu k aplikacím](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) Cloud App Security, která používá Azure Active Directory podmíněný přístup k bráně. přístup k aplikaci na základě určitých podmínek. LePenske říká, že tato funkce může být užitečná pro, řekněme, že povolíte přístup k souborům jen pro čtení a zakážete stahování.
* [ **Aramex** Delivery s omezeným přístupem – globální logistika a dopravní společnost vytváří Office propojené s cloudem a řešení pro správu identit a přístupu](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Zajištění bezpečného přístupu bylo obzvláště obtížné u vzdálených zaměstnanců Aramex. Společnost teď používá podmíněný přístup, aby tyto vzdálené zaměstnance měli přístup k jejich SaaS aplikacím mimo síť. Pravidlo podmíněného přístupu určí, jestli se má vyhodnotit Multi-Factor Authentication a že jenom ta, která mají správný přístup.

Další informace o zákaznících a partnerských zkušenostech s podmíněným přístupem Azure AD najdete v [článku o tom, co dělají lidé s Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Oznámení

Služba Azure AD průběžně přijímá vylepšení. Pokud chcete mít přehled o nejnovějším vývoji, přečtěte si téma [co je nového v Azure Active Directory?](../fundamentals/whats-new.md)

Poslední Blogy pro technickou komunitu a oddělení identity Microsoftu:

* 24. září 2018 [Azure Active Directory podmíněný přístup v Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21. září 2018 [jsou vlastní ovládací prvky podmíněného přístupu Azure AD ve verzi Public Preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/) .
* 21. září 2018 [je teď dostupná podpora podmíněného přístupu Azure AD pro omezený přístup s Microsoft Cloud App Security](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/) .
* 21. září 2018, [podmíněný přístup Azure AD: podpora spravovaného prohlížeče pro platformy iOS/Android je teď ve verzi Preview.](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21. září 2018, [podmíněný přístup Azure AD pro kódy zemí je ve verzi Public Preview](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/) .
* 21. září 2018, [dnes k dispozici Azure AD terms-of-use](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Studijní materiály

Pokud chcete získat přehled o funkcích podmíněného přístupu Azure AD, postupujte podle následujících odkazů.

* Přečtěte si o[tom, co je podmíněný přístup v Azure Active Directory?](overview.md)
* Zjistěte[, jaké jsou podmínky v Azure Active Directory podmíněný přístup?](conditions.md)
* Informace[o tom, co je podmínka umístění v Azure Active Directory podmíněný přístup?](location-condition.md)
* Informace o[tom, co jsou ovládací prvky přístupu v Azure Active Directory podmíněný přístup?](controls.md)
* Najděte "co[je to, co když nástroj používá Azure Active Directory podmíněný přístup?"](what-if-tool.md)
* Dodržujte [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md)

Kromě toho si přečtěte následující odkazy, kde najdete pokyny k ochraně přístupu ke všem službám, které jsou integrované s Azure Active Directory.

* [Konfigurace identit a přístupu k zařízením](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). V této části najdete popis postupu konfigurace zabezpečeného přístupu ke cloudovým službám prostřednictvím Enterprise Mobility + Security produktů implementací doporučeného prostředí a konfigurace, včetně předepsané sady zásad podmíněného přístupu a souvisejících možností.
* [Odkaz na nastavení Azure Active Directory podmíněného přístupu](technical-reference.md) Seznámit
   * Jaké aplikace používají podmíněný přístup?
   * Které služby jsou povoleny s podmíněným přístupem?
* [Povolte Azure Active Directory podmíněný přístup pro zabezpečený přístup uživatelů](https://www.youtube.com/watch?v=eLAYBwjCGoA). Podívejte se na toto video, kde se dozvíte, jak podmíněný přístup přehrává roli v dalších úlohách Enterprise a mobility Suite.

### <a name="training-videos"></a>Školicí videa

**Podmíněný přístup v Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Podmíněný přístup podle zařízení**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Povolit Azure Active Directory pro podmíněný přístup pro zabezpečený přístup uživatelů**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Online kurzy

Přečtěte si následující kurzy podmíněného přístupu a další informace o [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Návrh správy identit v Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Tento kurz vás provede klíčovými položkami, které potřebujete znát k návrhu řešení správy identit pomocí Azure AD." Podmíněný přístup Azure AD je popsaný v tématu použití rolí a Access Control s modulem Azure AD.

* Pluralsight.com: [Návrh ověřování pro Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Tento kurz vysvětluje, jak pomocí Azure AD řešit všechny požadavky na cloudové ověřování." Podmíněný přístup Azure AD je popsaný v tématu požadavky na ověřování v různých scénářích.

* Pluralsight.com: [autorizace návrhu pro Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Tento kurz učí možnosti autorizace dostupné v Azure a Azure AD." Podmíněný přístup Azure AD je popsaný v tématu "autorizace pomocí Azure Resource Manager a Azure AD".

### <a name="books"></a>Knihy

* O'Reilly – [implementace řešení Azure – Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * Začínáme se službami Azure a Naučte se, jak je implementovat ve vaší organizaci. Podmíněný přístup Azure AD je popsaný v kapitole [nasazení a synchronizace Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* [Služby infrastruktury Wiley-Microsoft Azure pro architekty: návrh cloudových řešení](https://www.wiley.com/Microsoft+Azure+Infrastructure+Services+for+Architects%3A+Designing+Cloud+Solutions-p-9781119596547)
   * "Tady je vše, co potřebujete, abyste pochopili, vyhodnotili, nasadili a zachovali prostředí, která využívají Microsoft Azure."

## <a name="white-papers"></a>Whitepapery

* Publikováno 18. prosince 2018, [vytvoření strategie správy odolného řízení přístupu pomocí Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Tento dokument obsahuje pokyny o strategiích, které může organizace přijmout pro zajištění odolnosti proti riziku uzamčení během nepředvídatelných přerušení.

* Publikováno 18. září 2018, [prostředky pro migraci aplikací do Azure Active Directory](../manage-apps/migration-resources.md)
   * Tento dokument white paper obsahuje seznam prostředků, které vám pomůžou migrovat přístup k aplikacím a ověřování pro Azure Active Directory (Azure AD).

* Publikováno 12. července 2018 [podrobný plán zabezpečení a dodržování předpisů Azure: PaaS pro hostování webových aplikací pro Velkou Británii pro oficiální úlohy](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Plány Azure se skládají z doprovodnéch dokumentů a šablon automatizace, které nasazují cloudové architektury, a nabízejí řešení scénářů, které mají požadavky na akreditaci nebo dodržování předpisů.

## <a name="guidance-for-it-administrators"></a>Doprovodné materiály pro správce IT

Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce, správce zabezpečení nebo správce podmíněného přístupu. Přečtěte si téma [oprávnění role správce v Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

As an IT administrator, use [Azure AD Conditional Access](overview.md) to require users to authenticate using Azure Multi-Factor Authentication, sign in from a trusted network, or trusted device.

Here are useful links to help you get started:

* [Best practices for Conditional Access in Azure Active Directory](best-practices.md)
* [Use Azure AD access reviews to manage users that have been excluded from Conditional Access policies](../governance/conditional-access-exclusion.md)
* [How To: Plan your Conditional Access deployment in Azure Active Directory](plan-conditional-access.md)
* [Quickstart: Require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md)
* [Quickstart: Require terms of use to be accepted before accessing cloud apps](require-tou.md)
* [Quickstart: Block access when a session risk is detected with Azure Active Directory Conditional Access](app-sign-in-risk.md)
* [Azure AD Conditional Access FAQs](faqs.md)
   * For additional questions, you can also view the [MSDN forum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * If you cannot find the answer to a problem, our support teams are always available to assist you further. Use [Contact Microsoft support](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Kurzy

* [**Quickstart: Require MFA for specific apps with Azure Active Directory Conditional Access**](app-based-mfa.md)
   * This quickstart shows how to configure an Azure AD Conditional Access policy that requires multi-factor authentication for a selected cloud app in your environment.

* [**Quickstart: Require terms of use to be accepted before accessing cloud apps**](require-tou.md)
   * This quickstart shows how to configure an Azure AD Conditional Access policy that requires a ToU to be accepted for a selected cloud app in your environment.

* [**Quickstart: Block access when a session risk is detected with Azure Active Directory Conditional Access**](app-sign-in-risk.md)
   * This quickstart shows how to configure a Conditional Access policy that blocks a sign-in when a configured sign-in risk level has been detected.

* [Tutorial: **Migrate a classic policy that requires multi-factor authentication in the Azure portal**](policy-migration-mfa.md)
   * This tutorial shows how to migrate a classic policy that requires multi-factor authentication (MFA) for a cloud app.

## <a name="end-user-readiness-and-communication"></a>End-user readiness and communication

Conditional Access uses other Azure AD capabilities that may affect the end user experience. For example, you can use Azure Multi-factor authentication to enable strong authentication for users. In that case, you will use the end-user templates of Azure MFA.

## <a name="next-steps"></a>Další kroky

* Start your deployment with the [Conditional Access deployment planning documentation](plan-conditional-access.md).
