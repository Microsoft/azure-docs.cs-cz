---
title: Co je nového? Poznámky k verzi – Azure Active Directory | Microsoft Docs
description: Zjistěte, co je nového v Azure Active Directory; například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6da9f27e25fa5543f949716824f54af2b9f1ad1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132541"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového v Azure Active Directory?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` do čtečky informačního kanálu ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Služba Azure AD průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat. Pokud hledáte položky starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---
## <a name="december-2020"></a>Prosinec 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Verze Public Preview – registrace a přihlašování Azure AD B2Cho telefonu pomocí integrovaných zásad

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
B2C a přihlašování telefonem pomocí integrovaných zásad umožňuje správcům IT a vývojářům organizací povolit, aby se koncovým uživatelům přihlásili a přihlásili pomocí telefonního čísla v toku uživatelů. Pokud se chcete dozvědět víc, přečtěte si téma [Nastavení registrace telefonu a přihlášení pro toky uživatelů (Preview)](../../active-directory-b2c/phone-authentication-user-flows.md) .

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Obecná dostupnost – výchozí hodnoty zabezpečení jsou teď povolené pro všechny nové klienty ve výchozím nastavení.

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Aby se chránily uživatelské účty, budou mít všichni noví klienti vytvoření 12. listopadu 2020 a budou mít povolené výchozí hodnoty zabezpečení. Výchozí nastavení zabezpečení vynutilo více zásad, včetně:
- Vyžaduje, aby všichni uživatelé a správci se zaregistrovali pro MFA pomocí aplikace Microsoft Authenticator.
- Vyžaduje, aby při každém přihlašování používaly zásadní role správce k ověřování MFA. Všichni ostatní uživatelé budou v případě potřeby vyzváni k ověřování MFA. 
- Starší verze ověřování bude zablokována u tenanta. 

Další informace najdete v článku [co jsou výchozí hodnoty zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Obecná dostupnost – podpora pro skupiny s až 250 tisíc členy v AADConnect

**Zadejte:** Změněná funkce  
**Kategorie služby:** Služba AD Connect  
**Schopnost produktu:** Správa životního cyklu identit
 
Společnost Microsoft nasadila nový koncový bod (rozhraní API) pro Azure AD Connect, který vylepšuje výkon operací synchronizační služby Azure Active Directory. Když použijete nový [koncový bod v2](../hybrid/how-to-connect-sync-endpoint-api-v2.md), budete mít při exportu a importu do Azure AD patrné zvýšení výkonu. Tento nový koncový bod podporuje následující scénáře:

- Synchronizace skupin s až 250 tisíc členy
- Zisky z výkonu při exportu a importu do Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Obecná dostupnost – Správa opravňujících k dispozici pro klienty v Azure Čína cloudu

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 

Možnosti správy oprávnění jsou nyní k dispozici pro všechny klienty v cloudu Azure Čína. Informace najdete na webu [dokumentace zásad správného řízení identit](https://docs.azure.cn/zh-cn/active-directory/governance/) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – prosinec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](/azure/active-directory/saas-apps/papercut-cloud-print-management-provisioning-tutorial)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – prosinec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V prosinci 2020 jsme do Galerie aplikací přidali následující 18 nových aplikací s podporou federace:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB One právní blokování](https://www.zylab.com/en/product/legal-hold), [Průvodce](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [Vonage](../saas-apps/vonage-tutorial.md), [počet já v provozním řídicím panelu](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs znalostní báze](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd](../saas-apps/rightcrowd-workforce-management-tutorial.md)pracovní síly [, JLL TRIRIGA,](../saas-apps/jll-tririga-tutorial.md) [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb webové aplikace firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn talentů Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md) [, KFAdvance](../saas-apps/kfadvance-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti. https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Přechod na týmy přímo z portálu pro přístup

**Zadejte:** Změněná funkce  
**Kategorie služby:** **Schopnost produktu** správy přístupu uživatele: Správa nároků

Týmy teď můžete spouštět přímo z portálu pro přístup. Uděláte to tak, že se přihlásíte k [přístupu](https://myaccess.microsoft.com/), přejdete na **přístup k balíčkům** a pak přejdete na **aktivní** kartu a zobrazíte všechny balíčky pro přístup, ke kterým už máte přístup. Když rozbalíte balíček pro přístup a najedete na týmy, můžete ho spustit kliknutím na tlačítko **otevřít** . 

Pokud chcete získat další informace o používání portálu pro přístup, přejděte na [vyžádání přístupu k balíčku přístupu ve správě nároků služby Azure AD](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Veřejná verze Preview – druhý správce úrovně můžete nastavit jako alternativní schvalovatel.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků

Ve správě nároků je teď k dispozici další možnost v procesu schvalování. Pokud jako schvalovatele vyberete pro prvního schvalovatele možnost manažer, budete mít další možnost jako alternativního schvalovatele, která je dostupná pro výběr v poli alternativní schvalovatel. Vyberete-li tuto možnost, je nutné přidat záložního schvalovatele, kterému bude žádost předána, v případě, že systém nemůže najít správce druhé úrovně.

Další informace najdete [v části Změna nastavení schválení pro balíček přístupu v Azure AD – Správa nároků](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Listopad 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 a Standard 3DES

**Zadejte:** Plánování změn  
**Kategorie služby:** Všechny aplikace Azure AD  
**Schopnost produktu:** Zvyšování

Azure Active Directory vyřadí následující protokoly v Azure Active Directory celosvětových oblastech do 30. června 2021:

- TLS 1.0
- TLS 1.1
- šifrovací sada 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Ovlivněná prostředí jsou:
- Komerční cloud Azure
- Office 365 RSZ a TT

Související oznámení: kombinace všech klientských a serverových a prohlížečových serverů by měly používat protokol TLS 1,2 a moderní šifrovací sady k údržbě zabezpečeného připojení k Azure Active Directory pro Azure, Office 365 a služby Microsoft 365. Tato změna se týká [Azure Active Directory TLS 1,0 & 1,1 a vyřazení šifrovací sady 3DES v US gov cloudu](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – listopad 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V listopadu 2020 jsme do Galerie aplikací přidali následující 52 nových aplikací s podporou federace:

[Cestovní & Správa výdajů](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning pro výběr souborů](https://pmteam.itslearning.com/), [krize – řízení](../saas-apps/crises-control-tutorial.md) [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum – studijní ostrov](https://app.studyisland.com/cfw/login/), [Virtual rizikový Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-nábor](https://recruit.talview.com/login), překladatel v reálném čase, [Klaxoon](https://access.klaxoon.com/login), [podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [EN-Trak Experience Platform](https://portal.en-trak.app/) [,](../saas-apps/lucid-tutorial.md) [Appian](../saas-apps/appian-tutorial.md), Panorays [, Builterra,](https://portal.builterra.com/)Eva se [změnami](https://my.evacheckin.com/organization), [HowNow](../saas-apps/hownow-webapp-sso-tutorial.md), [WebApp](../saas-apps/sailpoint-identitynow-tutorial.md) [, Coupa](https://portal.brightbooking.eu/) [, čtyřkrát](../saas-apps/coupa-risk-assess-tutorial.md)[střed](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence aplikace zabezpečení](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [dopravné opatření](https://www.gpcsl.com/freight.html), terraformu [Cloud](../saas-apps/terraform-cloud-tutorial.md), RemotePC, Prolorus [,](https://www.teamgage.com/Account/ExternalLoginAzure) [digitálního podepisování](https://login.playsignage.com/login) [, Hirebridge,](../saas-apps/remotepc-tutorial.md) [Teamgage](../saas-apps/prolorus-tutorial.md), [Roadmunk ATS](../saas-apps/hirebridge-ats-tutorial.md) [, Procaire](../saas-apps/nature-research-tutorial.md), [eDriving](../saas-apps/roadmunk-tutorial.md), [slunce software Relations CRM](https://cloud.relations-crm.com/), [Gradle](../saas-apps/procaire-tutorial.md), [instruktor®: Business](https://www.edriving.com/), [Enterprise](https://gradle.com/) [](../saas-apps/panorays-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Veřejná verze Preview – vlastní role pro podnikové aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
 [Vlastní role RBAC pro delegovanou správu podnikových aplikací](../users-groups-roles/roles-custom-available-permissions.md) je teď ve verzi Public Preview. Tato nová oprávnění se vytvářejí na vlastní role pro správu registrace aplikací, což umožňuje detailní kontrolu nad tím, jaký má váš správce k přístupu. V průběhu času budou vydána další oprávnění pro delegování správy služby Azure AD.

Některé běžné scénáře delegování:
- přiřazení uživatelů a skupin, které mají přístup k aplikacím jednotného přihlašování založené na SAML
- vytváření aplikací Galerie Azure AD
- aktualizace a čtení základních konfigurací SAML pro aplikace jednotného přihlašování založené na SAML
- Správa podpisových certifikátů pro aplikace jednotného přihlašování založené na SAML
- aktualizace e-mailových adres oznámení o přihlášení k vypršení platnosti pro aplikace jednotného přihlašování založené na SAML
- aktualizace signatury tokenu SAML a algoritmu přihlášení pro aplikace jednotného přihlašování založené na SAML
- vytváření, odstraňování a aktualizace atributů a deklarací uživatelů pro aplikace jednotného přihlašování založené na SAML
- možnost zapnout, vypnout a restartovat úlohy zřizování
- aktualizace mapování atributů
- možnost číst nastavení zřizování přidružená k objektu
- možnost číst nastavení zřizování přidružená k vašemu objektu služby
- možnost autorizovat přístup k aplikaci pro zřizování

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Verze Public Preview – Azure Proxy aplikací služby AD nativně podporuje přístup s jednotným přihlašováním k aplikacím, které používají hlavičky pro ověřování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control
 
Proxy aplikace Azure Active Directory (Azure AD) nativně podporují přístup k jednotnému přihlašování k aplikacím, které používají hlavičky pro ověřování. V Azure AD můžete nakonfigurovat hodnoty hlaviček vyžadované vaší aplikací. Hodnoty hlaviček budou do aplikace odesílány prostřednictvím proxy aplikace. Další informace najdete v tématu [jednotné přihlašování založené na hlavičkách pro místní aplikace pomocí proxy serveru aplikace Azure AD](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) .
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Obecná dostupnost – registrace Azure AD B2Cho telefonu a přihlášení pomocí vlastních zásad

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Když se telefonní číslo a přihlášení k telefonnímu číslu, vývojáři a podniky můžou svým zákazníkům dovolit zaregistrovat se a přihlásit se pomocí jednorázového hesla, které se pošle na telefonní číslo uživatele přes SMS. Tato funkce také umožní zákazníkovi změnit své telefonní číslo, pokud ztratí přístup k telefonu. S výkonem vlastních zásad umožňují vývojářům a podnikům komunikovat svou značku prostřednictvím přizpůsobení stránky. Přečtěte si, jak [nastavit registraci a přihlašování telefonem pomocí vlastních zásad v Azure AD B2C](../../active-directory-b2c/phone-authentication.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – listopad 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic – Tac – mobilní zařízení](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Další informace najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../manage-apps/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Public Preview – e-mailová Sign-In s ProxyAddresses se teď nasazují prostřednictvím připraveného zavedení

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Správci klientů teď můžou k nasazení e-mailových Sign-In s ProxyAddresses do konkrétních skupin Azure AD použít dvoufázové zavedení. To může pomoci při vyzkoušení funkce před jejich nasazením do celého klienta prostřednictvím zásad zjišťování domovské sféry. Pokyny k nasazení e-mailových Sign-In s ProxyAddresses prostřednictvím připraveného zavedení najdete v [dokumentaci](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Omezená verze Preview – Diagnostika přihlášení

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
V úvodní verzi Preview diagnostické diagnostiky teď můžou správci zkontrolovat přihlašování uživatelů. Správci mohou získat kontext, konkrétní a relevantní podrobnosti a pokyny, co se stalo během přihlašování a jak opravit problémy. Diagnostika je k dispozici na úrovni služby Azure AD a v okně diagnostikovat a řešit podmíněný přístup. Diagnostickými scénáři popsanými v této verzi jsou podmíněný přístup, Multi-Factor Authentication a úspěšné přihlášení.

Další informace najdete v tématu [co je diagnostika přihlášení ve službě Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Vylepšené neznámé vlastnosti přihlášení

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

  Zjištění neznámých vlastností přihlášení bylo aktualizováno. Zákazníci si můžou všimnout většího vysokého rizika neznámých detekcí vlastností přihlášení. Další informace najdete v tématu [co je riziko?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Public Preview aktualizace agenta zřizování cloudu je teď k dispozici (verze: 1.1.281.0).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování cloudu Azure AD  
**Schopnost produktu:** Správa životního cyklu identit
 
Agent zřizování cloudu byl vydán ve verzi Public Preview a je nyní k dispozici prostřednictvím portálu. Tato verze obsahuje několik vylepšení, včetně podpory GMSA pro vaše domény, což zajišťuje lepší zabezpečení, lepší počáteční cyklus synchronizace a podporu velkých skupin. Další podrobnosti najdete v [historii](../app-provisioning/provisioning-agent-release-version-history.md) verzí pro vydání. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Koncový bod rozhraní API pro obnovení BitLockeru hned pod/informationProtection

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu k zařízením  
**Schopnost produktu:** Správa životního cyklu zařízení
 
Dřív bylo možné obnovit klíče nástroje BitLocker prostřednictvím koncového bodu/BitLocker. Nakonec už tento koncový bod nebudeme používat a zákazníci by měli začít spotřebovávat rozhraní API, které teď spadá do/informationProtection.. 

Změny v dokumentaci najdete v tématu [rozhraní API pro obnovení nástroje BitLocker](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) pro aktualizace dokumentace.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Obecná dostupnost podpory aplikačního proxy serveru pro webový klient služby Vzdálená plocha pro HTML5

**Zadejte:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control
 
Podpora Azure Proxy aplikací služby AD pro webový klient služby Vzdálená plocha (RDS) je teď obecně dostupná. Webový klient RDS umožňuje uživatelům přístup k infrastruktuře vzdálené plochy prostřednictvím libovolného prohlížeče podporujícího HTLM5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome a tak dále. Uživatelé můžou komunikovat se vzdálenými aplikacemi nebo počítači, jako by se k nim měli místní zařízení odkudkoli. 

Pomocí Azure Proxy aplikací služby AD můžete zvýšit zabezpečení nasazení služby Vzdálená plocha tím, že vynutíte předběžné ověřování a zásady podmíněného přístupu pro všechny typy bohatých klientských aplikací. Další informace najdete v tématu [publikování vzdálené plochy pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Nová rozšířená služba dynamické skupiny je v Public Preview

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 
Vylepšená Dynamická služba skupiny je teď v Public Preview. Noví zákazníci, kteří ve svých klientech vytvářejí dynamické skupiny, budou používat novou službu. Čas potřebný k vytvoření dynamické skupiny bude úměrný velikosti skupiny, která se vytváří místo velikosti tenanta. Tato aktualizace zvýší výkon pro velké klienty významně, když zákazníci vytvoří menší skupiny. 

Nová služba také chce dokončit přidávání a odebírání členů z důvodu změn atributů během několika minut. Selhání v jednom zpracování také neblokuje zpracování tenanta. Další informace o vytváření dynamických skupin najdete v naší [dokumentaci](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>Říjen 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Místní hybridní agenti Azure AD ovlivněné změnami certifikátu Azure TLS

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Platformy

Microsoft aktualizuje služby Azure tak, aby používaly certifikáty TLS z jiné sady kořenových certifikačních autorit (CAs). Tato aktualizace je způsobena aktuálními certifikáty certifikační autority, které nejsou v souladu s jedním z požadavků na základní hodnoty fóra pro certifikační autoritu nebo v prohlížeči. Tato změna ovlivní hybridní agenty Azure AD nainstalovanou místně, které mají posílená prostředí s pevným seznamem kořenových certifikátů a budou se muset aktualizovat, aby důvěřovala novým vystavitelům certifikátů.

Tato změna způsobí přerušení služby, Pokud neprovedete okamžitou akci. Mezi tyto agenty patří [konektory proxy aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) pro vzdálený přístup do místního prostředí, předávací agenty pro [ověřování](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , které umožňují vašim uživatelům přihlašovat se k aplikacím pomocí stejných hesel a agentům [verze Preview pro zřizování cloudu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , kteří provádějí AD synchronizaci služby Azure AD. 

Pokud máte prostředí s pravidly brány firewall nastavené tak, aby umožňovalo odchozí volání jenom na určitý seznam odvolaných certifikátů (CRL), budete muset použít následující seznam CRL a adresy URL protokolu OCSP. Úplné podrobnosti o změně a adresách zabezpečení seznamu CRL a protokolu OCSP pro povolení přístupu k nástroji najdete v tématu  [změny certifikátu Azure TLS](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Události zřizování se z protokolů auditu odeberou a publikují se výhradně do protokolů zřizování.

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Aktivita [služby SCIM Provisioning](../app-provisioning/user-provisioning.md) je zaznamenána v protokolech auditu i v protokolech zřizování. Patří sem aktivity, jako je vytvoření uživatele v ServiceNow, skupina v GSuite nebo import role z AWS. V budoucnu budou tyto události publikovány pouze v protokolech zřizování. Tato změna se provádí, aby nedocházelo k duplicitním událostem v protokolech a dodatečné náklady vzniklé zákazníky, které zabírají protokoly v Log Analytics. 

Až se datum dokončí, poskytneme aktualizaci. Tato zastaralost není plánována pro kalendářní rok 2020. 

> [!NOTE]
> To nemá vliv na žádné události v protokolech auditu mimo události synchronizace emitované službou zřizování. Události, jako je vytvoření aplikace, zásada podmíněného přístupu, uživatel v adresáři atd. budou nadále vydávány v protokolech auditu. [Přečtěte si další informace](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Místní hybridní agenti Azure AD ovlivněné změnami certifikátu TLS (Azure Transport Layer Security)

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Platformy
 
Microsoft aktualizuje služby Azure tak, aby používaly certifikáty TLS z jiné sady kořenových certifikačních autorit (CAs). Aktualizace se projeví v důsledku aktuálních certifikátů certifikační autority, které nenásledují po jednom z požadavků na základní hodnoty fóra pro certifikační autoritu nebo v prohlížeči. Tato změna ovlivní hybridní agenty Azure AD nainstalovanou místně, které mají posílená prostředí s pevným seznamem kořenových certifikátů. Tyto agenty bude nutné aktualizovat, aby důvěřovali novým vystavitelům certifikátů.

Tato změna způsobí přerušení služby, Pokud neprovedete okamžitou akci. Mezi tyto agenty patří: 
- [Konektory proxy aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) pro vzdálený přístup k místnímu přístupu 
- [Předávací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenti pro ověřování umožňující uživatelům přihlašovat se k aplikacím pomocí stejných hesel
- Agenti ve [verzi Preview pro zřizování cloudu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , kteří synchronizují služby Azure AD. 

Pokud máte prostředí s pravidly brány firewall nastavené tak, aby umožňovalo odchozí volání jenom na konkrétní stažení seznamu odvolaných certifikátů (CRL), budete muset použít seznam CRL a adresy URL protokolu OCSP. Úplné podrobnosti o změně a adresách zabezpečení seznamu CRL a protokolu OCSP pro povolení přístupu k nástroji najdete v tématu  [změny certifikátu Azure TLS](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 a šifrování 3DES v US Gov cloudu

**Zadejte:** Plánování změn  
**Kategorie služby:** Všechny aplikace Azure AD  
**Schopnost produktu:** Zvyšování
 
Azure Active Directory zařadí následující protokoly do 31. března 2021:
- TLS 1.0
- TLS 1.1
- šifrovací sada 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Všechny kombinace klient-server a prohlížeč-Server by měly používat protokol TLS 1,2 a moderní šifrovací sady k údržbě zabezpečeného připojení k Azure Active Directory pro Azure, Office 365 a služby Microsoft 365.

Ovlivněná prostředí jsou:
- US Gov Azure
- [Office 365 RSZ High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Přiřazení aplikací k rolím v rozsahu AU a Object

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Tato funkce umožňuje přiřadit aplikaci (SPN) k roli správce v oboru jednotky pro správu. Další informace najdete v tématu [přiřazení vymezených rolí k jednotce pro správu](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Nyní můžete zakázat a odstranit uživatele typu Host, pokud jim dojde k odepření přístupu k prostředku.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
Zakázat a odstranit je pokročilý ovládací prvek kontrol přístupu Azure AD, který organizacím umožňuje lépe spravovat externí hosty ve skupinách a aplikacích. Pokud dojde k odepření hostů při kontrole přístupu, **zakážete a odstraníte** automaticky blokování těchto přihlášení po dobu 30 dnů. Po 30 dnech se úplně odeberou z tenanta.

Další informace o této funkci najdete v tématu [zakázání a odstranění externích identit pomocí kontrol přístupu Azure AD (Preview)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Tvůrci kontroly přístupu můžou přidat vlastní zprávy e-mailů k kontrolorům.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
V rámci kontrol přístupu ke službě Azure AD teď správci vytvářející recenze můžou napsat vlastní zprávu kontrolorům. Revidující uvidí zprávu v e-mailu, které obdrží, a vyzve k dokončení kontroly. Další informace o použití této funkce najdete v části Krok 14 oddílu [vytvoření jednoho nebo více kontrol přístupu](../governance/create-access-review.md#create-one-or-more-access-reviews) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – říjen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Pomocník pro integraci pro Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Prostředí Pomocník pro integraci (Preview) je teď k dispozici pro Azure AD B2C Registrace aplikací. Toto prostředí vám pomůže s konfigurací aplikace pro běžné scénáře. Přečtěte si další informace o [osvědčených postupech a doporučeních platformy Microsoft Identity Platform](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Zobrazení ID šablony role v Azure Portal uživatelském rozhraní

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure  
**Schopnost produktu:** Access Control
 

Teď si můžete zobrazit ID šablony každé role Azure AD v Azure Portal. V Azure AD vyberte  **Popis** vybrané role. 

Doporučujeme, aby zákazníci používali ID šablon rolí ve svém skriptu PowerShellu a kódu místo zobrazovaného názvu. ID šablony role je podporované pro použití pro objekty [directoryRoles](/graph/api/resources/directoryrole) a [rutiny roledefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Další informace o ID šablon rolí najdete v tématu [ID šablon rolí](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Konektory rozhraní API pro Azure AD B2C uživatelské toky pro registraci jsou teď ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 

Konektory API jsou teď k dispozici pro použití s Azure Active Directory B2C. Konektory API umožňují používat webová rozhraní API k přizpůsobení uživatelských toků registrace a integraci s externími cloudových systémů. Konektory rozhraní API můžete použít k těmto akcím:

- Integrace s vlastními pracovními postupy schvalování
- Ověřit data vstupu uživatele
- Přepsat atributy uživatele 
- Spustit vlastní obchodní logiku 

 Další informace najdete v části [Použití konektorů rozhraní API k přizpůsobení a rozšiřování registrační](../../active-directory-b2c/api-connectors-overview.md) dokumentace.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Vlastnost State pro připojené organizace ve správě nároků

**Zadejte:** Nová funkce  
**Kategorie služby:** **Schopnost produktu** Správa adresářů: Správa nároků
 

 Všechny připojené organizace teď budou mít další vlastnost s názvem "State". Stav bude řídit způsob, jakým se připojená organizace použije v zásadách, které odkazují na "všechny nakonfigurované propojené organizace". Hodnota bude buď "nakonfigurovaná" (což znamená, že organizace je v oboru zásad, které používají klauzuli All) nebo "navržené" (což znamená, že organizace není v oboru).  

Ručně vytvořené propojené organizace budou mít nastavené výchozí nastavení. Mezitím budou automaticky vytvořená (vytvořená prostřednictvím zásad, které umožňují jakémukoli uživateli z Internetu vyžadovat přístup) výchozí nastavení "navrženo".  Všechny připojené organizace vytvořené před září 9 2020 budou nastavené na nakonfigurováno. Správci mohou tuto vlastnost podle potřeby aktualizovat. [Přečtěte si další informace](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory externích identit teď má pro B2C nastavení Premium Advanced Security.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
K dispozici jsou nově podmíněný přístup na základě rizik a funkce detekce rizik ochrany Identity Protection v [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Díky těmto pokročilým funkcím zabezpečení můžou zákazníci teď:
- Využijte inteligentní přehledy k vyhodnocení rizik s B2C aplikacemi a koncovými uživatelskými účty. Mezi detekce patří neobvyklá cesta, anonymní IP adresy, IP adresy propojené malware a Azure AD Threat Intelligence. K dispozici jsou také sestavy založené na portálu a rozhraní API.
- Automaticky řeší rizika konfigurací zásad adaptivního ověřování pro uživatele B2C. Vývojáři a správci aplikací můžou zmírnit riziko v reálném čase tím, že požadují vícefaktorové ověřování (MFA) nebo blokují přístup v závislosti na zjištěné úrovni rizik uživatelů, s dalšími dostupnými ovládacími prvky na základě umístění, skupiny a aplikace.
- Integrujte s Azure AD B2C toky uživatelů a vlastní zásady. Podmínky se můžou aktivovat z vestavěných toků uživatelů v Azure AD B2C nebo je můžete začlenit do vlastních zásad B2C. Stejně jako u dalších aspektů toku uživatele B2C je možné přizpůsobit zasílání zpráv o činnostech koncového uživatele. Přizpůsobení je na základě možností hlasu, značky a zmírnění rizik v organizaci.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – říjen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V říjnu 2020 jsme do Galerie aplikací přidali následující 27 nových aplikací s podporou federace:

[Sentry](../saas-apps/sentry-tutorial.md) [Bumblebee – produktivita superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys cloud integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [portál pro zóny](https://portail.zonetechnologie.com/signin), [Beautiful.AI](../saas-apps/beautiful.ai-tutorial.md), [Datawiza pro přístup k](https://console.datawiza.com/)poskytovateli přístupu, [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [ecoChallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [připomenutí schůzky](https://app.appointmentreminder.co.nz/account/login), [Cloud. trh](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetings](https://app.greetly.com/) [, OrgVitality](../saas-apps/orgvitality-sso-tutorial.md) [, Starmind](../saas-apps/starmind-tutorial.md) [,](https://hrm.workstem.com/login) [maloobchodního](../saas-apps/retail-zipline-tutorial.md) [vedení](../saas-apps/loop-flow-crm-tutorial.md) [, Workstem](../saas-apps/web-cargo-air-tutorial.md) [, Zipline](../saas-apps/hoxhunt-tutorial.md) [, Hoxhunt](../saas-apps/mevisio-tutorial.md) [, MEVISIO](../saas-apps/samsara-tutorial.md) [,](../saas-apps/nimbus-tutorial.md) [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Protokoly zřizování se teď dají streamovat do Log Analytics.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 

Publikujte protokoly zřizování do Log Analytics za účelem:
- Ukládat protokoly zřizování po dobu delší než 30 dnů
- Definování vlastních výstrah a oznámení
- Sestavování řídicích panelů pro vizualizaci protokolů
- Provádění složitých dotazů k analýze protokolů 

Informace o použití této funkce najdete v tématu [Vysvětlení způsobu, jakým se zřizování integruje s protokoly Azure monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Pro vlastníky aplikací se teď můžou zobrazit protokoly zřizování.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Nyní můžete vlastníkům aplikace dovolit monitorovat aktivity pomocí služby zřizování a řešit problémy bez poskytnutí privilegované role nebo zrušení kritického bodu. [Přečtěte si další informace](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Přejmenování 10 Azure Active Directory rolí

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure  
**Schopnost produktu:** Access Control
 
Některé předdefinované role Azure Active Directory (AD) mají názvy, které se liší od těch, které se zobrazují v centru pro správu Microsoft 365, na portálu Azure AD a na Microsoft Graph. Tato nekonzistence může způsobit problémy v automatizovaných procesech. V této aktualizaci přejmenováváme 10 názvů rolí, aby se zajistila konzistence. Nové názvy rolí mají následující tabulka:

![Tabulka nových názvů rolí](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C podpora toku kódu ověřování pro jednostránkové pomocí MSAL JS 2. x

**Zadejte:** Změněná funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
MSAL.js verze 2. x teď zahrnuje podporu toku autorizačního kódu pro jednostránkové webové aplikace (jednostránkové). Azure AD B2C teď bude podporovat použití typu aplikace SPA pro Azure Portal a používání MSAL.jsho toku autorizačního kódu s PKCE pro jednostránkové aplikace. Díky tomu bude jednostránkové používat Azure AD B2C k udržování jednotného přihlašování s novějšími prohlížeči a respektování novějších doporučení protokolu ověřování. Začněte s [registrací jednostránkové aplikace (Spa) v Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) kurzu.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aktualizace zapamatování Multi-Factor Authentication (MFA) na nastavení důvěryhodného zařízení

**Zadejte:** Změněná funkce  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

Nedávno jsme aktualizovali [zapamatovatelné Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) na funkci důvěryhodného zařízení a rozšířili jsme ověřování po dobu až 365 dní. Licence pro Azure Active Directory (Azure AD) Premium můžou používat taky [zásady četnosti přihlašování](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) , které poskytují větší flexibilitu pro nastavení opětovného ověřování.

Pro optimální činnost koncového uživatele doporučujeme použít četnost přihlášení k podmíněnému přístupu pro prodloužení životnosti relací v důvěryhodných zařízeních, umístěních nebo relacích s nízkým rizikem jako alternativu k možnosti Zapamatovat MFA v nastavení důvěryhodného zařízení. Pokud chcete začít, přečtěte si naše [nejnovější pokyny k optimalizaci prostředí pro opakované ověření](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Září 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Povědomí o zabezpečení Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Zřizování cloudu se Public Preview aktualizovat.

**Zadejte:** Nová funkce  
**Kategorie služby:** **Schopnost produktu** Azure AD zřizování cloudu: Správa životního cyklu identit
 
Azure AD Connect aktualizace cloudového zřizování ve verzi Public Preview dvě hlavní vylepšení vyvinutá na základě zpětné vazby od zákazníků: 

- Možnosti mapování atributů prostřednictvím Azure Portal

    Díky této funkci můžou správci IT mapovat atributy uživatelů, skupin nebo kontaktů ze služby AD na Azure AD pomocí různých typů mapování, které jsou dnes přítomné. Mapování atributů je funkce používaná ke standardizaci hodnot atributů, které se přenášejí ze služby Active Directory na Azure Active Directory. Jedna z nich může určit, jestli má být hodnota atributu přímo namapována z AD do služby Azure AD, nebo použít výrazy k transformaci hodnot atributů při zřizování uživatelů. [Další informace](../cloud-provisioning/how-to-attribute-mapping.md)

- Zřizování na vyžádání nebo testování uživatelského prostředí

    Po nastavení konfigurace můžete chtít ověřit, zda transformace uživatele pracuje podle očekávání, a teprve potom ji použít pro všechny uživatele v oboru. Díky zřizování na vyžádání můžou správci IT zadat rozlišující název (DN) uživatele AD a zjistit, jestli se synchronizují podle očekávání. Zřizování na vyžádání poskytuje skvělý způsob, jak zajistit, že mapování atributů, které jste dříve pracovali, podle očekávání. [Další informace](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Auditované obnovení BitLockeru v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu k zařízením  
**Schopnost produktu:** Správa životního cyklu zařízení
 
Když správci IT nebo koncoví uživatelé čtou klíče pro obnovení nástroje BitLocker, ke kterým mají přístup, Azure Active Directory nyní vygeneruje protokol auditu, který zachycuje přístup k obnovovacímu klíči. Stejný audit poskytuje podrobnosti o zařízení, ke kterému byl klíč BitLocker přidružený.

Koncoví uživatelé mají [přístup ke svým klíčům pro obnovení prostřednictvím svého účtu](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Správci IT můžou získat přístup k klíčům pro obnovení prostřednictvím [rozhraní API pro obnovení pomocí obnovovacího klíče BitLockeru ve verzi beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) nebo prostřednictvím portálu Azure AD. Další informace najdete v tématu [zobrazení nebo kopírování klíčů nástroje BitLocker na portálu Azure AD](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Integrovaná role Správce zařízení Teams

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Uživatelé s rolí [Správce zařízení týmů](../roles/permissions-reference.md#teams-devices-administrator) můžou spravovat [zařízení certifikovaná](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) v centru pro správu týmů. 

Tato role umožňuje uživateli zobrazit všechna zařízení v jednoduchém přehledu s možností vyhledávání a filtrování zařízení. Uživatel může také zkontrolovat podrobnosti každého zařízení, včetně přihlášeného účtu a značka a modelu zařízení. Uživatel může změnit nastavení na zařízení a aktualizovat verze softwaru. Tato role neuděluje oprávnění kontrolovat aktivity týmů a zavolá kvalitu zařízení.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Pokročilé možnosti dotazů pro objekty adresáře

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí
 
Všechny nové možnosti dotazů, které jsou představené pro objekty adresáře v rozhraních API Azure AD, jsou teď dostupné v koncovém bodu verze 1.0 a připravené k výrobě. Vývojáři mohou pomocí standardních operátorů OData spočítat, Hledat, filtrovat a řadit objekty adresáře a související odkazy.

Další informace najdete v dokumentaci [zde](https://aka.ms/BlogPostMezzoGA)a můžete také poslat názor na tento [stručný průzkum](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Public Preview: vyhodnocení průběžného přístupu pro klienty, kteří konfigurují zásady podmíněného přístupu

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vyhodnocování průběžného přístupu (CAE) je teď k dispozici ve verzi Public Preview pro klienty Azure AD se zásadami podmíněného přístupu. V CAE se kritické události a zásady zabezpečení vyhodnocují v reálném čase. To zahrnuje zakázání účtu, resetování hesla a změnu umístění. Další informace najdete v tématu [vyhodnocení průběžného přístupu](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Verze Public Preview: vyžádání dalších otázek pro přístup uživatelů k balíčku pro zlepšení rozhodnutí o schválení

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Správci teď můžou vyžadovat, aby si uživatelé, kteří žádají o přístup k balíčku, odpověděli na další otázky mimo obchodní odůvodnění na portálu pro správu nároků služby Azure AD. Odpovědi uživatelů se pak zobrazí schvalovatelům, kteří jim pomohou zajistit přesnější rozhodnutí o schválení přístupu. Další informace najdete v tématu [shromáždění dalších informací o žadatelích ke schválení (Preview)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Verze Public Preview: Rozšířená správa uživatelů

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Správa uživatelů
 

Portál Azure AD je aktualizovaný, aby bylo snazší najít uživatele na stránkách všichni uživatelé a odstranění uživatelé. Změny ve verzi Preview zahrnují: 
- Více viditelných vlastností uživatele včetně ID objektu, stavu synchronizace adresáře, typu vytvoření a vystavitele identity.
- Hledání teď umožňuje kombinované hledání názvů, e-mailů a ID objektů.
- Rozšířené filtrování podle typu uživatele (Member, Guest a None), stav synchronizace adresářů, typ vytvoření, název společnosti a název domény.
- Nové možnosti řazení ve vlastnostech, jako je název, hlavní název uživatele a datum odstranění.
- Nový celkový počet uživatelů, kteří se aktualizují pomocí všech hledání nebo filtrů.

Další informace najdete [v tématu vylepšení správy uživatelů (Preview) v Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nové pole poznámky pro podnikové aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** **Funkce produktu** podnikové aplikace: jednotné přihlašování

Můžete přidat volné textové poznámky k podnikovým aplikacím. Můžete přidat všechny relevantní informace, které vám pomohou aplikace manažera v rámci podnikových aplikací. Další informace najdete v tématu [rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V září 2020 jsme do Galerie aplikací přidali následující 34 nových aplikací s podporou federace:

[VMware Horizon – Unified Access Gateway](), [Pulse Secure PC](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [vhodnosti pro pohlaví](https://app.genderfitness.com/), [Coeo portál](https://my.coeo.com/), [gramaticky](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Secure a](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [identity & Access Management](https://my.priva.com/), [nitro pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md) [,](https://wisteconline.com/auth/oidc) [zabezpečené podepisování Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), zabezpečené podepisování [Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)

Dokumentaci ke všem aplikacím můžete najít také zde: https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nová role delegování ve správě nároků v Azure AD: přístup k balíčku přiřazení balíčků

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Do správy opravňujících k Azure AD se přidala nová role správce přiřazení balíčku přístupu, která poskytuje přesnější oprávnění ke správě přiřazení. Nyní můžete delegovat úkoly na uživatele v této roli, kteří mohou delegovat správu pro přístup balíčku k vlastníkovi firmy. Správce přiřazení balíčku pro přístup ale nemůže změnit zásady balíčků přístupu ani jiné vlastnosti, které jsou nastavené správci. 

S touto novou rolí budete mít k výhodu nejmenších oprávnění potřebných k delegování správy přiřazení a udržování administrativního řízení pro všechny ostatní konfigurace balíčků přístupu. Další informace najdete v tématu [role správy oprávnění](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Změny v toku připojování Privileged Identity Management

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
V minulosti byl vyžadován souhlas uživatele s připojováním k Privileged Identity Management (PIM) a postup připojování v okně PIM, které zahrnovalo registraci v Azure AD MFA. Díky nedávné integraci PIM v okně role a správci služby Azure AD odebíráme toto prostředí. Každý tenant s platnou licencí P2 se automaticky připojí k PIM.

Připojování k PIM nemá žádný přímý negativní dopad na vašeho tenanta. Můžete očekávat následující změny:
- Další možnosti přiřazení, jako je aktivní vs. s nárokem v podobě počátečního a koncového času, když provedete přiřazení v okně role PIM nebo Azure AD a správci. 
- Další mechanismy vytváření oborů, jako jsou jednotky pro správu a vlastní role, se zavedly přímo do prostředí pro přiřazení. 
- Pokud jste správcem globálního správce nebo privilegovaného správce role, můžete začít s několika dalšími e-maily, jako je týdenní přehled o PIM. 
- V protokolu auditu souvisejícím s přiřazením role můžete také vidět instanční objekt MS-PIM. Tato očekávaná změna by neměla mít vliv na běžný pracovní postup.

 Další informace najdete v tématu [Začínáme používat Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Správa nároků na Azure AD: ve výchozím nastavení se nyní zobrazuje podokno vybrat prostředky balíčku přístupu. aktuálně se jedná o prostředky, které jsou aktuálně ve vybraném katalogu.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 

V toku vytváření balíčku přístupu se změna chování podokna vybrat na kartě role prostředků. V současné době je výchozím chováním zobrazení všech prostředků vlastněných uživatelem a prostředky přidanými do vybraného katalogu. 

Toto prostředí se změní tak, aby se ve výchozím nastavení zobrazovaly jenom prostředky aktuálně přidané do katalogu, aby uživatelé mohli snadno vybírat prostředky z katalogu. Tato aktualizace vám pomůže s zjistitelnými prostředky, které se mají přidat do balíčků pro přístup, a snižuje riziko neúmyslného přidání prostředků vlastněných uživatelem, kteří nejsou součástí katalogu. Další informace najdete v tématu [Vytvoření nového balíčku pro přístup ve správě nároků ve službě Azure AD](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Srpen 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualizace požadavků na bránu firewall pro Azure Multi-Factor Authentication Server

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Od 1. října 2020 budou požadavky na bránu firewall pro Azure MFA Server vyžadovat další rozsahy IP adres.

Pokud máte odchozí pravidla brány firewall ve vaší organizaci, aktualizujte pravidla tak, aby servery MFA mohly komunikovat se všemi potřebnými rozsahy IP adres. Rozsahy IP adres najdete v části [požadavky na bránu firewall pro Azure Multi-Factor Authentication Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Nadcházející změny uživatelského prostředí v hodnocení identity Secure

**Zadejte:** Plánování změn  
**Kategorie služby:** **Schopnost produktu** Identity Protection: zabezpečení identity & Protection

Aktualizujeme portál pro zabezpečený skóre identit, který bude zarovnaný se změnami zavedenými v [nové verzi](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)Microsoft Secure skore. 

Verze Preview se změnami bude k dispozici na začátku září. Změny ve verzi Preview zahrnují:
- "Bezpečné skóre identity" přejmenováno na "zabezpečené skóre pro identitu" pro přizpůsobení značky s Microsoft Secure skore
- Body jsou normalizovány na standardní měřítko a nahlášené v procentech namísto bodů.

V této verzi Preview můžou zákazníci přepínat mezi stávajícím prostředím a novým prostředím. Tato verze Preview bude poslední až do konce listopadu 2020. Po verzi Preview budou zákazníci automaticky přesměrováni na nové prostředí pro uživatelské prostředí.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nová omezená oprávnění k přístupu hosta v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Access Control   
**Schopnost produktu:** Správa uživatelů

Aktualizovali jsme oprávnění na úrovni adresáře pro uživatele typu Host. Tato oprávnění umožňují správcům vyžadovat další omezení a ovládací prvky při přístupu k externímu uživateli typu Host. Správci teď můžou přidat další omezení přístupu externích hostů k informacím o profilu a členství uživatelů a skupin. Díky této funkci veřejné verze Preview můžou zákazníci spravovat přístup externích uživatelů ve velkém měřítku tím, že zakazují členství ve skupině, včetně omezení uživatelů typu Host, aby viděli členství skupin, ve kterých jsou.

Další informace najdete v tématu [omezená oprávnění k přístupu hosta](../enterprise-users/users-restrict-guest-permissions.md) a [výchozí oprávnění uživatelů](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Obecná dostupnost rozdílových dotazů u instančních objektů

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí
 
Microsoft Graph rozdílový dotaz teď podporuje typ prostředku v v 1.0:
- Instanční objekt

Klienti teď můžou sledovat změny těchto prostředků efektivně a poskytovat nejlepší řešení pro synchronizaci změn těchto prostředků s místním úložištěm dat. Informace o tom, jak nakonfigurovat tyto prostředky v dotazu, najdete v tématu [použití rozdílového dotazu ke sledování změn v Microsoft Graphch datech](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Obecná dostupnost rozdílových dotazů pro oAuth2PermissionGrant

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí

Microsoft Graph rozdílový dotaz teď podporuje typ prostředku v v 1.0:
- OAuth2PermissionGrant

Klienti teď můžou sledovat změny těchto prostředků efektivně a poskytovat nejlepší řešení pro synchronizaci změn těchto prostředků s místním úložištěm dat. Informace o tom, jak nakonfigurovat tyto prostředky v dotazu, najdete v tématu [použití rozdílového dotazu ke sledování změn v Microsoft Graphch datech](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – srpen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V srpnu 2020 jsme do Galerie aplikací přidali následující 25 nových aplikací s podporou federace:

[Backup365](https://portal.backup365.io/login), [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [konektor Enlyft Dynamics 365](http://enlyft.com/), [Serraview místo pro využívání softwarových řešení](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [viditelně](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum – hodnocení přesná cesta](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), WireWheel, [ZIX dodržování předpisů a zachytávání](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GreenLight podnikových ovládacích prvků](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [,](https://goto.bpanda.com/login)Genetec, [volný prostor](https://www.clearance.network/) [,](../saas-apps/isams-tutorial.md) [VeraSMART, Amiko](../saas-apps/wirewheel-tutorial.md), [](https://nestiolistings.com/sso/oidc/azure/authorize/) [Twingate,](../saas-apps/verasmart-tutorial.md) [Scalefusion](https://amiko.web.rivero.app/) [,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Bpanda,](https://auth.twingate.com/signup) [](https://app.vivun.com/dashboard/calendar/connect)pro [koncové uživatele](https://www.wandera.com/) [, Vivun](https://scalefusion.com/users/sign_in/)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Doménové struktury prostředků jsou teď dostupné pro Azure služba AD DS 

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services   
**Schopnost produktu:** Azure AD Domain Services
 
Schopnost doménových struktur prostředků v Azure AD Domain Services je teď všeobecně dostupná. Nyní můžete povolit autorizaci bez synchronizace hodnot hash hesel, aby bylo možné použít Azure AD Domain Services, včetně autorizace pomocí čipové karty. Další informace najdete v tématu [Koncepty a funkce sady replik pro Azure Active Directory Domain Services (Preview)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Podpora místní repliky pro Azure služba AD DS spravované domény je teď k dispozici.

**Zadejte:** Nová funkce   
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services
 
Spravovanou doménu můžete rozšířit tak, aby měla více než jednu sadu replik na jeden tenant služby Azure AD. Sady replik se dají přidat do všech partnerských virtuálních sítí v libovolné oblasti Azure, která podporuje Azure AD Domain Services. Další sady replik v různých oblastech Azure poskytují geografickou obnovu po havárii pro starší aplikace, pokud oblast Azure přejde do režimu offline. Další informace najdete v tématu [Koncepty a funkce sady replik pro Azure Active Directory Domain Services (Preview)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Obecná dostupnost služby Azure AD moje Sign-Ins

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Prostředí koncového uživatele
 
Azure AD moje Sign-Ins je nová funkce, která umožňuje podnikovým uživatelům zkontrolovat historii přihlášení, aby zkontrolovala jakoukoli neobvyklou aktivitu. Kromě toho tato funkce umožňuje koncovým uživatelům hlásit zprávu "This jsem já" nebo "to jsem já" na podezřelých aktivitách. Další informace o použití této funkce najdete v tématu [zobrazení a hledání poslední přihlašovací aktivity na stránce Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Zřizování uživatelů SAP SuccessFactors na základě lidských zdrojů v Azure AD je teď všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Službu SAP SuccessFactors teď můžete integrovat jako autoritativní zdroj identity s Azure AD a automatizovat kompletní životní cyklus identity pomocí událostí souvisejících s hodinami, jako jsou noví zaměstnanci a ukončení, při zřizování a rušení zřizování účtů v Azure AD. 

Další informace o tom, jak nakonfigurovat příchozí zřizování SAP SuccessFactors do služby Azure AD, najdete v kurzu [konfigurace SAP SuccessFactors pro zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Vlastní otevřené ID připojit podporu MS Graph API pro Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Dřív se vlastní Open ID Connect Provider mohl přidat nebo spravovat jenom prostřednictvím Azure Portal. Nyní mohou zákazníci Azure AD B2C přidávat a spravovat prostřednictvím rozhraní API beta verze Microsoft Graph. Informace o tom, jak nakonfigurovat tento prostředek pomocí rozhraní API, najdete v tématu [identityProvider Resource Type](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Přiřazení předdefinovaných rolí Azure AD ke skupinám cloudu

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control

Pomocí této nové funkce teď můžete přiřadit předdefinované role Azure AD ke skupinám cloudu. Můžete například přiřadit roli správce služby SharePoint k Contoso_SharePoint_Admins skupině. Správce osobních informací můžete použít také k seskupení oprávněných členů role místo udělení přístupu. Informace o tom, jak nakonfigurovat tuto funkci, najdete v tématu [použití skupin cloudu ke správě přiřazení rolí v Azure Active Directory (Preview)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Předdefinovaná role Insights Business vedoucího společnosti je teď dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Uživatelé v roli vedoucí firmy Insights mají přístup k sadě řídicích panelů a přehledů prostřednictvím [aplikace M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). To zahrnuje plný přístup ke všem řídicím panelům a prezentovaným přehledům a funkcím průzkumu dat. Uživatelé v této roli ale nemají přístup k nastavení konfigurace produktu, což je zodpovědností role správce Insights. Další informace o této roli najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Integrovaná role správce Insights je teď dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Uživatelé v roli správce Insights mají přístup k plné sadě možností správy v [aplikaci M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Uživatel v této roli může číst informace o adresáři, monitorovat stav služby, lístky podpory souborů a přistupovat k aspektům nastavení pro správce Insights. Další informace o této roli najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Správce aplikace a správce cloudových aplikací můžou spravovat vlastnosti rozšíření aplikací.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Dříve mohl [vlastnost Extension](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)spravovat jenom globální správce. Nyní tuto funkci povolujeme také pro Správce aplikací a správce cloudových aplikací.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>1.1.1301.0 4.6.263.0 a konektory MIM 2016 SP2

**Zadejte:** Změněná funkce  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit

[Kumulativní balíček oprav hotfix (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) je k dispozici pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Tento balíček kumulativní aktualizace obsahuje aktualizace pro MIM CM, Správce synchronizace MIM a komponenty PAM. Kromě toho sestavování obecných konektorů MIM 1.1.1301.0 zahrnuje aktualizace konektoru grafu.

---
 
## <a name="july-2020"></a>Červenec 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Jako správce IT chci cílit na klientské aplikace pomocí podmíněného přístupu.

**Zadejte:** Plánování změn   
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
V případě, že je v podmíněném přístupu dostupná verze GA klientských aplikací, nové zásady se teď ve výchozím nastavení použijí pro všechny klientské aplikace. Patří sem starší klienti ověřování. Existující zásady zůstanou beze změny, ale přepínač *Konfigurovat ano/ne* se odebere z existujících zásad, aby bylo možné snadno zjistit, které klientské aplikace jsou zásadami aplikovány. 

Při vytváření nové zásady se ujistěte, že vyloučíte uživatele a účty služeb, které pořád používají starší verze ověřování. Pokud to neuděláte, budou zablokovány. [Přečtěte si další informace](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Nadcházející opravy dodržování předpisů SCIM

**Zadejte:** Plánování změn  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD využívá standard SCIM pro integraci s aplikacemi. Naše implementace standardu SCIM se vyvíjí a očekáváme, že provedeme změny v našem chování, jak provedeme operace opravy, a také nastavit vlastnost "aktivní" na prostředku. [Přečtěte si další informace](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Nastavení vlastník skupiny na portálu pro správu Azure se změní.

**Zadejte:** Plánování změn  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Na portálu pro správu Azure a přístupovém panelu můžete nakonfigurovat nastavení vlastníka na stránce Obecné nastavení skupiny, aby se omezila oprávnění přiřazování vlastníků k omezené skupině uživatelů. Brzy bude možné přiřazovat oprávnění vlastníka skupiny nejen na těchto dvou portálech UX, ale taky vymáhat zásady na back-endu, aby poskytovaly konzistentní chování v rámci koncových bodů, jako je například PowerShell a Microsoft Graph. 

Začneme deaktivovat aktuální nastavení pro zákazníky, kteří ho nepoužívají, a nabídne vám možnost určit v následujících několika měsících možnost oboru uživatelů pro oprávnění vlastníka skupiny. Pokyny k aktualizaci nastavení skupiny najdete v tématu Úprava informací o skupině pomocí [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Služba Azure Active Directory Registration Service končí na podporu TLS 1,0 a 1,1

**Zadejte:** Plánování změn  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Platformy

Protokol TLS (Transport Layer Security) 1,2 a servery aktualizací a klienti budou brzy komunikovat se službou Azure Active Directory Device Registration. Podpora TLS 1,0 a 1,1 pro komunikaci se službou Device Registration Service pro Azure AD vyřadí:
- 31. srpna 2020 ve všech cloudech v svrchovaném moři (na úrovni RSZ, DoD atd.)
- 30. října 2020 ve všech komerčních cloudech

[Přečtěte si další informace](../devices/reference-device-registration-tls-1-2.md) o TLS 1,2 pro službu Azure AD Registration Service.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Přihlašování Windows Hello pro firmy zobrazené v protokolech přihlášení Azure AD

**Zadejte:** Určí  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Windows Hello pro firmy umožňuje koncovým uživatelům přihlašovat se k počítačům s Windows pomocí gesta (například PIN nebo biometriky). Správci Azure AD můžou chtít odlišit přihlášení Windows Hello pro firmy od jiných přihlášení k Windows v rámci cesty organizace k ověřování pomocí hesla. 

Správci teď můžou zjistit, jestli ověřování Windows používalo Windows Hello pro firmy, a to tak, že na kartě Podrobnosti ověření pro přihlašovací událost Windows v okně Azure AD Sign-Ins v Azure Portal. Ověřování Windows Hello pro firmy bude v poli Metoda ověřování zahrnovat "WindowsHelloForBusiness". Další informace o interpretaci Sign-Inch protokolů najdete v [dokumentaci k přihlašovacím protokolům](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Opravy chování při odstraňování skupin a zlepšení výkonu

**Zadejte:** Určí  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Když jste dřív změnili skupinu z "in-Scope" na "out-of-Scope" a správce klikl na restart před dokončením změny, objekt skupiny se neodstraní. Objekt skupiny se teď odstraní z cílové aplikace, když se dostane mimo rozsah (zakázaný, odstraněný, nepřiřazený nebo neprojde filtr oboru). [Přečtěte si další informace](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Public Preview: Správci teď můžou do e-mailu přidat vlastní obsah při vytváření kontroly přístupu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
Při vytvoření nové kontroly přístupu obdrží kontrolor e-mail s žádostí o dokončení kontroly přístupu. Řada našich zákazníků požádala o možnost přidat vlastní obsah do e-mailu, jako jsou kontaktní údaje nebo jiný další podpůrný obsah, který vás provede kontrolorem. 

V rámci veřejné verze Preview můžou správci zadat vlastní obsah e-mailu odeslanému revidujícím přidáním obsahu v části Upřesnit v tématu kontroly přístupu Azure AD. Pokyny k vytváření kontrol přístupu najdete v tématu [Vytvoření kontroly přístupu skupin a aplikací v prohlídekch Azure AD Access](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Tok autorizačního kódu pro jednostránkové aplikace k dispozici

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Vývojářské prostředí
 
Vzhledem k tomu, že se v moderních prohlížečích používají omezení souborů cookie třetích stran, jako je Safari ITP, bude muset jednostránkové používat tok autorizačního kódu místo implicitního toku pro udržování jednotného přihlašování a MSAL.js v 2. x teď bude podporovat tok autorizačního kódu. 

Existují odpovídající aktualizace Azure Portal, takže můžete zabezpečené ověřování hesla aktualizovat tak, aby bylo typu "Spa", a používat tok kódu ověřování. Další pokyny najdete v tématech [přihlášení uživatelů a získání přístupového tokenu v zabezpečeném kódu v JavaScriptu pomocí toku kódu ověřování](../develop/quickstart-v2-javascript-auth-code.md) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Služba Azure Proxy aplikací služby AD nyní podporuje webového klienta vzdálené plochy.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Služba Azure Proxy aplikací služby AD nyní podporuje webového klienta vzdálené plochy (RDS). Webový klient RDS umožňuje uživatelům přístup k infrastruktuře vzdálené plochy prostřednictvím libovolného prohlížeče podporujícího HTLM5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome atd. Uživatelé můžou komunikovat se vzdálenými aplikacemi nebo počítači, jako by se k nim měli místní zařízení odkudkoli. Pomocí Azure Proxy aplikací služby AD můžete zvýšit zabezpečení nasazení RDS tím, že vynucujete zásady podmíněného ověřování a podmíněného přístupu pro všechny typy bohatých klientských aplikací. Pokyny najdete v tématu [publikování vzdálené plochy pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Nové generace Azure AD B2C toky uživatelů ve verzi Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Zjednodušené uživatelské prostředí nabízí paritu funkcí s funkcemi verze Preview a je domovkou pro všechny nové funkce. Uživatelé budou moci povolit nové funkce v rámci stejného toku uživatelů a snížit tak nutnost vytvářet více verzí s každou novou verzí funkce. Nakonec nově uživatelsky přívětivé uživatelské prostředí zjednodušuje výběr a vytváření toků uživatelů. Vyzkoušejte si to hned [vytvořením toku uživatele](../../active-directory-b2c/tutorial-create-user-flows.md). 

Další informace o tokůch uživatelů najdete [v tématu verze toku uživatele v Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červenci 2020 jsme do Galerie aplikací přidali následující 55 nových aplikací s podporou federace:

[CLAP své ruce](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor trezor](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepční](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [mince konstrukcí Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile, Wootric](../saas-apps/titanfile-tutorial.md) [, SolarWinds](../saas-apps/wootric-tutorial.md) [Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText adresářových služeb](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md) [, BlogIn, IntSights](../saas-apps/blogin-tutorial.md) [, KPIFIRE](../saas-apps/intsights-tutorial.md) [, TextLine](../saas-apps/kpifire-tutorial.md) [,](../saas-apps/textline-tutorial.md) [Cloud Academy – SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [ChatWork](../saas-apps/chatwork-tutorial.md) [, CloudSign,](../saas-apps/cloudsign-tutorial.md) [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan jednotné přihlašování ke službě data hub](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [výstup](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [pravá ruka kyberbezpečnosti ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [lenses.IO](../saas-apps/lensesio-tutorial.md), [chvilka](../saas-apps/momenta-tutorial.md), [zvýšení](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme robot](https://tellme365liteweb.azurewebsites.net/), [inspirovat](https://app.inspiresoftware.com/), [Maverics identity Orchestrator – konektor SAML](https://www.strata.io/identity-fabric/), [Smartschool (systém pro správu školy)](https://smartschoolz.com/login), [Zepto – inteligentní Timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [jednotné přihlašování browserstackem](../saas-apps/browserstack-single-sign-on-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – červenec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro nově integrovanou [výuku LinkedInu](../saas-apps/linkedin-learning-provisioning-tutorial.md)aplikace.

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Zobrazení přiřazení rolí napříč všemi obory a možnost je stáhnout do souboru CSV

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Nyní můžete zobrazit přiřazení rolí napříč všemi obory role na kartě role a správci na portálu Azure AD. Tato přiřazení rolí můžete také stáhnout pro každou roli do souboru CSV. Pokyny k zobrazení a přidání přiřazení rolí najdete v tématu [zobrazení a přiřazení rolí správce v Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication Software Development (Azure MFA SDK) – vyřazení

**Zadejte:** Zastaralé  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vývoj softwaru Azure Multi-Factor Authentication (Azure MFA SDK) dosáhl konce životnosti v listopadu 14 2018, jak je uvedeno poprvé v listopadu 2017. Microsoft vypíná platnost služby SDK až do 30. září 2020. Všechna volání, která sada SDK provede, selžou.

Pokud vaše organizace používá sadu Azure MFA SDK, je nutné migrovat do 30. září 2020:
- Azure MFA SDK pro MIM: Pokud používáte sadu SDK s MIM, měli byste migrovat na Azure MFA Server a aktivovat Privileged Access Management (PAM) podle těchto [pokynů](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK pro vlastní aplikace: Zvažte integraci vaší aplikace do služby Azure AD a použití podmíněného přístupu k prosazování MFA. Pokud chcete začít, podívejte se na tuto [stránku](../manage-apps/plan-an-application-integration.md). 

---

 
