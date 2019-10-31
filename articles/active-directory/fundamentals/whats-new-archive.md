---
title: Archivujte, co je nového v Azure Active Directory? | Microsoft Docs
description: Poznámky k verzi s novinkami v části Přehled této sady obsahu obsahují 6 měsíců aktivity. Po 6 měsících budou položky odebrány z hlavního článku a vloženy do tohoto článku archivu.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fcf1e5966be7708e7c6278839cbfbe5d65cdffa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149129"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archivujte, co je nového v Azure Active Directory?

Část [co je nového ve službě Azure Active Directory?](whats-new.md) v článku poznámky k verzi obsahuje aktualizace za posledních šest měsíců, i když tento článek obsahuje všechny starší informace.

Co je nového v Azure Active Directory? Poznámky k verzi obsahují informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

---

## <a name="april-2019"></a>Duben 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nové zjišťování analýz Azure AD Threat je teď k dispozici jako součást Azure AD Identity Protection

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

Detekce analýzy hrozeb Azure AD je teď k dispozici jako součást aktualizované Azure AD Identity Protection funkce. Tato nová funkce pomáhá indikovat neobvyklou aktivitu uživatelů pro konkrétního uživatele nebo aktivitu, která je konzistentní se známými vzory útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu.

Další informace o aktualizované verzi Azure AD Identity Protection najdete na blogu o [čtyřech hlavních Azure AD Identity Protection vylepšeních, která jsou teď ve verzi Public Preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a na [Azure Active Directory Identity Protection (aktualizované)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) předmětu. Další informace o detekci analýzy hrozeb v Azure AD najdete v článku [Azure Active Directory Identity Protection detekce rizik](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Služba Azure AD nároking Management je teď dostupná (verze Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Zásady správného řízení identity  
**Schopnost produktu:** Zásady správného řízení identity

Správa nároků služby Azure AD, která je teď ve verzi Public Preview, pomáhá zákazníkům delegovat správu balíčků přístupu, který definuje, jak můžou zaměstnanci a obchodní partneři požádat o přístup, kteří musí schvalovat a jak dlouho mají přístup. Přístup k balíčkům může spravovat členství v Azure AD a skupinách Office 365, přiřazování rolí v podnikových aplikacích a přiřazování rolí pro weby SharePointu Online. Přečtěte si další informace o správě nároků v tématu [Přehled správy nároků služby Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Další informace o šířce funkcí Azure AD Identity Governance, včetně Privileged Identity Management, kontrolách přístupu a podmínek použití, najdete v tématu [co je Azure AD identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Protokoly aktivit Azure AD jsou teď dostupné v Azure Monitor (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Abychom vám pomohli vyřešit svůj názor na vizualizace pomocí protokolů aktivit Azure AD, zavádíme nové funkce Insights v Log Analytics. Tato funkce vám pomůže získat přehled o prostředcích Azure AD pomocí našich interaktivních šablon nazývaných sešity. Tyto předem připravené sešity můžou poskytovat podrobné informace pro aplikace nebo uživatele a zahrnovat:

- **Přihlášení.** Obsahuje podrobné informace o aplikacích a uživatelích, včetně umístění pro přihlášení, klientského operačního systému nebo klienta a verze prohlížeče a počtu úspěšných nebo neúspěšných přihlášení.

- **Starší verze ověřování a podmíněný přístup.** Obsahuje podrobné informace o aplikacích a uživatelích využívajících starší ověřování, včetně Multi-Factor Authenticationho použití aktivovaných zásadami podmíněného přístupu, aplikacích, které používají zásady podmíněného přístupu a tak dále.

- **Analýza neúspěšných přihlášení.** Pomáhá určit, jestli dochází k chybám přihlášení kvůli akci uživatele, problémům se zásadami nebo vaší infrastruktuře.

- **Vlastní sestavy.** Můžete vytvořit nové nebo upravit existující sešity, které vám pomůžou přizpůsobit funkci Insights pro vaši organizaci.

Další informace najdete v tématu [použití Azure Monitorch sešitů pro sestavy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – duben 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V dubnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Signing](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), MileIQ [, PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [EduBrite](https://mileiq.onelink.me/991934284/7e980085), [](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [RStudio LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)a [Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (jednotné přihlašování založené na rolích)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Správa Certent akcií](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nová možnost četnosti kontrol přístupu a výběr více rolí

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity

Nové aktualizace v recenzích přístupu Azure AD umožňují:

- Kromě dříve existujících možností týdně, měsíčně, čtvrtletně a ročně můžete změnit četnost kontrol přístupu na **částečně ročně**.

- Při vytváření jedné kontroly přístupu vyberte víc rolí Azure AD a prostředků Azure. V této situaci se všechny role nastavují se stejným nastavením a všichni revidující se budou oznamovat současně.

Další informace o tom, jak vytvořit kontrolu přístupu, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect se převádějí e-mailové systémy upozornění, odesílají zákazníkům nové e-mailové informace o odesílateli.

**Zadejte:** Změněná funkce  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platformy

Azure AD Connect právě přechází na naše systémy e-mailových výstrah, které si můžou někteří zákazníci zobrazit jako nového e-mailového odesílatele. Pokud to chcete vyřešit, musíte do seznamu povolených organizací přidat `azure-noreply@microsoft.com`, nebo nebudete moct nadále přijímat důležité výstrahy ze sady Office 365, Azure nebo služby synchronizace.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Změny přípony hlavního názvu uživatele (UPN) jsou teď mezi federované domény v Azure AD Connect úspěšné.

**Zadejte:** Určí  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platformy

Teď můžete úspěšně změnit příponu UPN uživatele z jedné federované domény na jinou federované domény v Azure AD Connect. Tato oprava znamená, že během synchronizačního cyklu nebo při obdržení e-mailu s oznámením, "nelze aktualizovat tento objekt v Azure Active Directory, nemusíte mít nadále zkušenosti s chybovou zprávou FederatedDomainChangeError, protože atribut [ FederatedUser. UserPrincipalName], není platný. Aktualizujte hodnotu v místních adresářových službách.

Další informace najdete v tématu [řešení chyb během synchronizace](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zvýšené zabezpečení pomocí zásad podmíněného přístupu na základě ochrany aplikací ve službě Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Podmíněný přístup na základě ochrany aplikací je teď k dispozici pomocí zásad **vyžadovat ochranu aplikací** . Tato nová zásada pomáhá zvýšit zabezpečení vaší organizace tím, že pomáhá zabránit:

- Uživatelé získají přístup k aplikacím bez Microsoft Intune licence.

- Uživatelé nemůžou získat Microsoft Intune zásady ochrany aplikací.

- Uživatelé získají přístup k aplikacím bez nakonfigurovaného Microsoft Intune zásady ochrany aplikací.

Další informace najdete v tématu [Postup při vyžadování zásad ochrany aplikací pro cloudový přístup k aplikaci pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nová podpora jednotného přihlašování a podmíněného přístupu Azure AD v Microsoft Edge (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Vylepšili jsme podporu Azure AD pro Microsoft Edge, včetně poskytování nové podpory pro jednotné přihlašování a podmíněný přístup Azure AD. Pokud jste už dřív používali Microsoft Intune Managed Browser, teď můžete místo toho použít Microsoft Edge.

Další informace o nastavení a správě zařízení a aplikací pomocí podmíněného přístupu najdete v tématu [vyžadování spravovaných zařízení pro cloudovou aplikaci přístup s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) a [vyžadování schválených klientských aplikací pro přístup k cloudovým aplikacím s podmíněným přístupem. ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Další informace o správě přístupu pomocí Microsoft Edge se zásadami Microsoft Intune najdete v tématu [Správa přístupu k Internetu pomocí Microsoft Intune prohlížeče chráněného zásadami](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Březen 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Architektura prostředí identit a podpora vlastních zásad v Azure Active Directory B2C je teď k dispozici (GA)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

V Azure AD B2C teď můžete vytvářet vlastní zásady, včetně následujících úloh, které jsou podporované v škále a v rámci naší smlouvy SLA pro Azure:

- Vytvoření a nahrání cest uživatelů s vlastním ověřováním pomocí vlastních zásad.

- Popište cesty uživatelů krok za krokem jako výměny mezi poskytovateli deklarací identity.

- Definujte podmíněné větvení v cestě uživatele.

- Transformujte a mapujte deklarace identity pro použití v rozhodnutích a komunikacích v reálném čase.

- Ve svých uživatelských cestách pro ověřování použijte REST API služby s povoleným použitím. Například s poskytovateli e-mailu, CRMs a proprietárními autorizačními systémy.

- Federovat se zprostředkovateli identity, kteří jsou kompatibilní s protokolem OpenIDConnect. Například s více klienty Azure AD, poskytovateli účtů sociálních sítí nebo poskytovateli dvojúrovňového ověřování.

Další informace o vytváření vlastních zásad najdete v tématu [poznámky pro vývojáře pro vlastní zásady v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) a čtení [blogového příspěvku Alex Simon, včetně případných studií](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – březen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V březnu 2019 jsme do Galerie aplikací přidali tyto 14 nových aplikací s podporou federace:

[ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [auditování na základě vysvětlení systém](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool výkonové otázky](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/),, [Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizonts](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [ú](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové konektory zřizování Zscaler a Atlassian v galerii Azure AD – březen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Automatizace vytváření, aktualizace a odstraňování uživatelských účtů pro následující aplikace:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tři](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), Atlassian [Cloud](https://aka.ms/atlassianCloudProvisioning)

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Obnovení a Správa odstraněných skupin Office 365 na portálu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Odstraněné skupiny Office 365 teď můžete zobrazit a spravovat z portálu Azure AD. Tato změna vám pomůže zjistit, které skupiny jsou k dispozici pro obnovení, spolu s tím, že vám umožní trvale odstranit všechny skupiny, které nepotřebuje vaše organizace.

Další informace najdete v tématu [obnovení skupin s ukončenou platností nebo odstraněných](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Pro místní aplikace zabezpečené službou SAML v Azure AD prostřednictvím proxy aplikací (Public Preview) je teď k dispozici jednotné přihlašování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Teď můžete pro místní aplikace ověřené pomocí SAML a vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikací poskytnout jednotné přihlašování (SSO). Další informace o tom, jak nastavit jednotné přihlašování SAML v místních aplikacích, najdete v tématu [jednotné přihlašování SAML pro místní aplikace s proxy aplikací (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientské aplikace v cyklech žádostí budou přerušeny, aby se zlepšila spolehlivost a činnost koncového uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Klientské aplikace mohou v krátké době nesprávně vydávat stovky stejných žádostí o přihlášení. Tyto požadavky, ať už jsou úspěšné nebo ne, přispívají ke zhoršení uživatelského prostředí a zvýšenému zatížení pro IDP, zvyšují latenci pro všechny uživatele a snižují dostupnost IDP.

Tato aktualizace pošle chybu `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` klientským aplikacím, které vydávají duplicitní žádosti několikrát v krátké době, mimo rozsah běžné operace. Klientské aplikace, které narazí na tento problém, by měly zobrazit interaktivní výzvu a vyžadovat, aby se uživatel znovu přihlásil. Další informace o této změně a o tom, jak opravit aplikaci, pokud dojde k této chybě, najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nové protokoly auditu – uživatelské prostředí teď k dispozici

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Vytvořili jsme novou stránku **protokolů auditu** Azure AD, které vám pomůžou zlepšit čitelnost a jak hledat vaše informace. Pokud chcete zobrazit stránku nové **protokoly auditu** , vyberte v části **aktivita** v Azure AD možnost **protokoly auditu** .

![Stránka nové protokoly auditu s ukázkovými informacemi](media/whats-new/audit-logs-page.png)

Další informace o nové stránce **protokoly auditu** najdete [v tématu sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nová upozornění a pokyny, které vám pomůžou zabránit nechtěnému uzamknutí správce z chybně konfigurovaných zásad podmíněného přístupu

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Abychom správcům zabránili v náhodném uzamykání ze svých klientů prostřednictvím nesprávně nakonfigurovaných zásad podmíněného přístupu, vytvořili jsme nová upozornění a aktualizované pokyny v Azure Portal. Další informace o nových pokynech najdete v tématu [co jsou závislosti služby v Azure Active Directory podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Vylepšené používání funkcí pro koncové uživatele na mobilních zařízeních

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Aktualizovali jsme naše stávající prostředí s využitím, abychom vylepšili, jak si vyzkoušíte používání a souhlasíte s podmínkami použití v mobilním zařízení. Nyní se můžete přiblížit nebo oddálit, přejít zpátky, stáhnout informace a vybrat hypertextové odkazy. Další informace o aktualizovaných podmínek použití najdete v tématu [funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nové prostředí pro stažení protokolů aktivit služby Azure AD k dispozici

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete stahovat velké objemy protokolů aktivit přímo z Azure Portal. Tato aktualizace vám umožní:

- Stáhněte si až 250 000 řádků.

- Po dokončení stahování se zobrazí oznámení.

- Přizpůsobte název souboru.

- Určete výstupní formát buď JSON, nebo CSV.

Další informace o této funkci najdete v tématu [rychlý Start: stažení sestavy auditu pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zásadní změna: aktualizace pro vyhodnocení podmínek pomocí protokolu Exchange ActiveSync (EAS)

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Access Control

Právě probíhá aktualizace způsobu, jakým protokol Exchange ActiveSync (EAS) vyhodnocuje následující podmínky:

- Umístění uživatele v závislosti na zemi, oblasti nebo IP adrese

- Riziko přihlášení

- Platforma zařízení

Pokud jste tyto podmínky dříve používali v rámci zásad podmíněného přístupu, mějte na paměti, že chování podmínky se může změnit. Pokud jste například v zásadě používali podmínku umístění uživatele v zásadě, můžete zjistit, že se zásady nyní přeskočí v závislosti na umístění uživatele.

---

## <a name="february-2019"></a>Únor 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurovatelné šifrování tokenů SAML Azure AD (Public Preview) 

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Teď můžete nakonfigurovat libovolnou podporovanou aplikaci SAML, aby přijímala šifrované tokeny SAML. Při konfiguraci a použití s aplikací Azure AD šifruje emitované kontrolní výrazy SAML pomocí veřejného klíče získaného z certifikátu uloženého v Azure AD.

Další informace o konfiguraci šifrování tokenu SAML najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu pro skupiny nebo aplikace pomocí kontrol přístupu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Řádnou

Teď můžete do jedné kontroly přístupu Azure AD přidat více skupin nebo aplikací pro členství ve skupině nebo přiřazení aplikace. Kontroly přístupu s více skupinami nebo aplikacemi se nastavují pomocí stejného nastavení a všichni zahrnutí recenzenti se oznamují současně.

Další informace o tom, jak vytvořit kontrolu přístupu pomocí kontrol přístupu ke službě Azure AD, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – únor 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V únoru 2019 jsme do Galerie aplikací přidali tyto 27 nových aplikací s podporou federace:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE,](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial) [Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [Stack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Kliknutí na oprávnění, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismických procesů](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [sdílení](https://www.shareadream.org/how-it-works)cloudu, [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [znalostní báze LMS odkudkoli](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [organizační areál](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [portál Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud podle Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivita platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Rozšířená kombinovaná registrace MFA/SSPR

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

V reakci na zpětnou vazbu od zákazníků jsme vylepšili kombinované možnosti registrace MFA/SSPR, které pomáhají uživatelům rychleji registrovat své bezpečnostní údaje pro MFA i SSPR. 

**Chcete-li zapínat rozšířené možnosti pro uživatele dnes, postupujte podle následujících kroků:**

1. Jako globální správce nebo Správce uživatelů se přihlaste k Azure Portal a přejděte do **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce ve verzi Preview**. 

2. V části **Uživatelé, kteří můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení – možnost aktualizovat** , vyberte, pokud chcete zapnout funkce pro **vybranou skupinu uživatelů** nebo pro **všechny uživatele**.

V průběhu několika následujících týdnů odebereme možnost zapnout pro klienty, kteří ještě nemají zapnuté, možnost zapínat staré prostředí Preview s možností registrace pro MFA/SSPR.

**Pokud chcete zjistit, jestli se ovládací prvek pro vašeho tenanta odebere, postupujte podle těchto kroků:**

1. Jako globální správce nebo Správce uživatelů se přihlaste k Azure Portal a přejděte do **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce ve verzi Preview**.  

2. Pokud jsou **Uživatelé, kteří můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení** , nastavené na možnost **žádné**, bude možnost odebrána z vašeho tenanta.

Bez ohledu na to, jestli jste dříve zapnuli staré prostředí pro náhled registrace MFA/SSPR pro uživatele, nebo ne, se stará prostředí vypne v budoucím datu. Z tohoto důvodu důrazně doporučujeme, abyste se co nejdříve přesunuli na nové a rozšířené možnosti.

Další informace o tom, jak rozšířené možnosti registrace, najdete v tématu o tom, jaká vylepšení se týkají [služby Azure AD kombinovaná MFA a registrace resetování hesel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aktualizované prostředí pro správu zásad pro toky uživatelů

**Zadejte:** Změněná funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Aktualizovali jsme proces vytváření a správy zásad pro toky uživatelů (dříve označované jako integrované zásady). Toto nové prostředí je teď výchozím nastavením pro všechny klienty Azure AD.

Pomocí ikon úsměvu nebo zamračení v oblasti **poslat zpětnou vazbu** v horní části obrazovky portálu můžete zadat další názory a návrhy.

Další informace o novém prostředí pro správu zásad najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Zvolit konkrétní verze prvků stránky poskytované Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Nyní můžete zvolit konkrétní verzi prvků stránky, které poskytuje Azure AD B2C. Výběrem konkrétní verze můžete otestovat své aktualizace, než se zobrazí na stránce, a můžete dosáhnout předvídatelného chování. Kromě toho teď můžete vyjádřit výslovný souhlas s tím, že vynutili konkrétní verze stránek, aby bylo možné přizpůsobit JavaScript. Pokud chcete tuto funkci zapnout, ve vašich uživatelských tocích klikněte na stránku **vlastností** .

Další informace o volbě konkrétní verze prvků stránky najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurovatelné požadavky na heslo pro koncové uživatele pro B2C (GA)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Pro koncové uživatele teď můžete nastavit složitost hesla vaší organizace, místo abyste museli používat vaše nativní zásady hesel Azure AD. V okně **vlastností** toků uživatelů (dříve označované jako integrované zásady) můžete zvolit složitost hesla **jednoduchého** nebo **silného**, nebo můžete vytvořit **vlastní** sadu požadavků.

Další informace o konfiguraci požadavků na složitost hesla najdete [v tématu Konfigurace požadavků na složitost pro hesla v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nové výchozí šablony pro prostředí pro ověřování vlastních značek

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Můžete použít naše nové výchozí šablony, které jsou umístěné v okně **rozložení stránky** vašich uživatelských toků (dříve označované jako předdefinované zásady), a vytvořit tak pro uživatele vlastní prostředí pro ověřování značkou.

Další informace o používání šablon najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Leden 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Spolupráce služby Active Directory B2B s použitím jednorázového ověřování pomocí hesla (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Představili jsme JEDNORÁZOVé ověřování pomocí hesla pro uživatele typu Host B2B, kteří se nemůžou ověřit jiným způsobem jako Azure AD, účet Microsoft (MSA) nebo Google Federation. Tato nová metoda ověřování znamená, že uživatelé typu Host nemusejí vytvářet nové účet Microsoft. Místo toho může uživatel typu Host při uplatnění pozvánky nebo přístupu ke sdílenému prostředku požádat o dočasný kód, který se pošle e-mailové adrese. Pomocí tohoto dočasného kódu se uživatel typu Host může i nadále přihlašovat.

Další informace najdete v tématu [e-mailové ověřování heslem jednorázového hesla (Preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) a blogu. [Azure AD zajišťuje bezproblémové sdílení a spolupráci pro všechny uživatele s libovolným účtem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie pro Azure Proxy aplikací služby AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Zavedli jsme tři nové nastavení souborů cookie, která jsou k dispozici pro vaše aplikace, které jsou publikované prostřednictvím proxy aplikací:

- **Použijte soubor cookie pouze s protokolem HTTP.** Nastaví příznak **HttpOnly** pro přístup k proxy aplikací a soubory cookie relace. Zapnutím tohoto nastavení získáte další výhody zabezpečení, jako je například pomoc při kopírování a úpravách souborů cookie prostřednictvím skriptování na straně klienta. Pro přidané výhody doporučujeme zapnout tento příznak (vyberte **Ano**).

- **Použijte zabezpečený soubor cookie.** Nastaví **zabezpečený** příznak pro přístup k proxy aplikací a soubory cookie relace. Zapnutím tohoto nastavení získáte další výhody zabezpečení tím, že zajistíte, aby se soubory cookie přenesly přes zabezpečené kanály TLS, jako je například HTTPS. Pro přidané výhody doporučujeme zapnout tento příznak (vyberte **Ano**).

- **Použijte trvalý soubor cookie.** Zabrání vypršení platnosti přístupových souborů cookie, když je webový prohlížeč zavřený. Tyto soubory cookie jsou poslední po dobu životnosti přístupového tokenu. Soubory cookie se ale resetují, pokud je dosažený čas vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **ne**. Zapněte jenom nastavení pro starší aplikace, které nesdílejí soubory cookie mezi procesy.

Další informace o nových souborech cookie najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Dostupné nové federované aplikace v galerii aplikací Azure AD – leden 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do ledna 2019 jsme do Galerie aplikací přidali tyto 35 nové aplikace s podporou federace:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talentůová paleta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [informační CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco deštník](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [připomenutí vypršení platnosti](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [prohlížeč instavů](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [ Příkazy](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [funkční](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease ](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [arů pro Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pro Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial) [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [na návštěvu](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn trajekt Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Vylepšení nových Azure AD Identity Protection (verze Public Preview)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí oznamujeme, že jsme do nabídky Azure AD Identity Protection Public Preview přidali následující vylepšení, včetně těchto:

- Aktualizované a více integrovaných uživatelských rozhraní

- Další rozhraní API

- Vylepšené hodnocení rizik prostřednictvím strojového učení

- Zarovnání celého produktu v rámci rizikových uživatelů a rizikových přihlášení

Další informace o vylepšeních najdete v tématu [co je Azure Active Directory Identity Protection (Aktualizováno)?](https://aka.ms/IdentityProtectionDocs) k získání dalších informací a ke sdílení nápadů můžete využít výzvy v rámci produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce zámku aplikace pro Microsoft Authenticator aplikaci na zařízeních s iOS a Androidem

**Zadejte:** Nová funkce  
**Kategorie služby:** Aplikace Microsoft Authenticator  
**Schopnost produktu:** Zabezpečení identity & ochrana

Chcete-li zachovat jednorázová hesla, informace o aplikaci a nastavení aplikace, můžete zapnout funkci zámku aplikace v aplikaci Microsoft Authenticator. Zapnutí zámku aplikace znamená, že budete požádáni o ověření pomocí PIN kódu nebo biometriky pokaždé, když otevřete aplikaci Microsoft Authenticator.

Další informace najdete v tématu [Nejčastější dotazy k aplikaci Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozšířené možnosti exportu Azure AD Privileged Identity Management (PIM)

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management

Správci Privileged Identity Management (PIM) teď můžou exportovat všechna aktivní a oprávněná přiřazení rolí pro konkrétní prostředek, který zahrnuje přiřazení rolí pro všechny podřízené prostředky. Dřív bylo obtížné, aby správci získali úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek.

Další informace najdete v tématu [zobrazení historie aktivit a auditu pro role prostředků Azure v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/prosinec 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé odebrání z oboru synchronizace už nepřešli na účty jenom pro Cloud.

**Zadejte:** Určí  
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Službě

>[!Important]
>Z důvodu této opravy jsme slyšeli a porozuměli vašemu frustrace. Proto jsme tuto změnu vrátili až do doby, kdy ji můžeme pro implementaci ve vaší organizaci usnadnit.

Opravili jsme chybu, ve které by příznak nastavení dirsyncenabled uživatele byl chybně přepnut na **hodnotu false** , pokud byl objekt Active Directory Domain Services (služba AD DS) vyloučen z oboru synchronizace a následně přesunut do koše v Azure AD v následujících případech: synchronizační cyklus. V důsledku této opravy platí, že pokud je uživatel vyloučen z rozsahu synchronizace a následně obnoven ze složky Koš služby Azure AD, uživatelský účet zůstane synchronizovaný z místní služby AD, jak je očekáván a nemůže být spravován v cloudu, protože jeho zdroj autority (SoA) zůstává jako místní služba AD.

Před touto opravou došlo k problému, když byl příznak nastavení dirsyncenabled přepnut na hodnotu false. Došlo k chybnému dojmu o tom, že se tyto účty převedly na objekty pouze cloudu a že je možné spravovat účty v cloudu. Účty ale pořád uchovávají svůj záznam SoA jako místní a všechny synchronizované vlastnosti (atributy stínů) pocházející z místní služby AD. Tento stav způsobilo více problémů v Azure AD a dalších cloudových úlohách (jako je Exchange Online), u kterých se tyto účty očekávaly jako synchronizované z AD, ale teď se chovají stejně jako účty jenom pro Cloud.

V současné době je jediným způsobem, jak skutečně převést účet synchronizovaných z AD na účet jenom pro Cloud, zakázáním DirSync na úrovni tenanta, což spustí back-end operaci přenosu prostředku SoA. Tento typ změny SoA vyžaduje (ale neplatí jenom pro) čištění všech místních atributů v relaci (například LastDirSyncTime a stínových atributů) a odesílání signálu do dalších cloudových úloh, aby měl příslušný objekt převedený jenom na cloudový účet. .

Tato oprava proto brání přímým aktualizacím atributu ImmutableID uživatele synchronizovaného ze služby AD, který v některých scénářích v minulosti byl nutný. V rámci návrhu ImmutableID objektu v Azure AD, jak název naznačuje, má být neměnné. Pro řešení těchto scénářů jsou k dispozici nové funkce implementované v Azure AD Connect Health a klientovi Azure AD Connect synchronizace.

- **ImmutableID aktualizace velkého rozsahu pro mnoho uživatelů v dvoufázovém přístupu**
  
  Například je třeba provést zdlouhavou migraci služba AD DS mezi doménovými strukturami. Řešení: použijte Azure AD Connect ke **konfiguraci kotvy zdrojového kódu** a, když uživatel migruje, zkopíruje existující hodnoty ImmutableID z Azure AD do atributu ms-DS-Consistency-GUID místního uživatele služba AD DS v nové doménové struktuře. Další informace najdete v tématu [použití MS-DS-ConsistencyGuid jako sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Rozsáhlé aktualizace ImmutableID pro mnoho uživatelů v jednom snímku**

  Například při implementaci Azure AD Connect uděláte chybu a teď potřebujete změnit atribut SourceAnchor. Řešení: zakažte DirSync na úrovni tenanta a vymažte všechny neplatné hodnoty ImmutableID. Další informace najdete v tématu [vypnutí synchronizace adresářů pro Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Přesouhlasení místního uživatele s existujícím uživatelem v Azure AD** Například uživatel, který byl znovu vytvořen v služba AD DS vygeneruje duplicitní hodnotu v účtu Azure AD, místo aby se přeshodoval s existujícím účtem služby Azure AD (osamocený objekt). Řešení: k přemapování zdrojového ukotvení/ImmutableID použijte Azure AD Connect Health v Azure Portal. Další informace najdete v tématu [scénář osamoceného objektu](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zásadní změna: aktualizace schématu auditu a přihlášení prostřednictvím Azure Monitor

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

V současné době publikujete datové proudy protokolu auditu i přihlašování prostřednictvím Azure Monitor, takže můžete hladce integrovat soubory protokolů pomocí nástrojů pro SIEM nebo pomocí Log Analytics. V závislosti na vaší zpětné vazbě a při přípravě na obecné oznámení o dostupnosti této funkce provádíme v našem schématu následující změny. Tyto změny schématu a související aktualizace dokumentace budou provedeny první týden v lednu.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nové pole **typu operace** , které poskytne typ operace prováděné na prostředku. Například **přidejte**, **aktualizujte**nebo **odstraňte**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněná pole ve schématu auditu
Ve schématu auditu se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Kategorie|Toto bylo pole **název služby** . Teď je to pole **kategorie auditu** . **Název služby** byl přejmenován na pole **loggedByService** .|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetResources|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásady</li><li>Aplikace</li><li>Uživatel</li><li>Skupina</li></ul>|
|loggedByService|Poskytuje název služby, která vygenerovala protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek protokolů auditu. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1\. místo</li></ul>|<ul><li>Úspěch</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněná pole ve schématu přihlášení
Ve schématu přihlášení se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Toto bylo pole **conditionalaccessPolicies** . Teď je to pole **appliedConditionalAccessPolicies** .|Beze změny|Beze změny|
|conditionalAccessStatus|Poskytuje výsledek stavu zásad podmíněného přístupu při přihlášení. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1\. místo</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek jednotlivých stavů zásad podmíněného přístupu při přihlášení. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1\. místo</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v tématu [Interpretace schématu protokolu auditu Azure AD v Azure monitor (Preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema) .

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení Identity Protection pro model strojového učení pod dohledem a modul hodnocení rizik

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Hodnocení rizik

Vylepšení pro uživatele, kteří souvisejí s ochranou identity, a modul hodnocení rizik přihlašování, mohou pomoci zlepšit přesnost a rozsah rizik uživatelů. Správci mohou všimnout, že úroveň rizika uživatele již není přímo propojena s úrovní rizika konkrétních detekcí a že dojde ke zvýšení počtu a úrovně rizik přihlašování.

Detekce rizik se teď vyhodnocuje pomocí modelu strojového učení pod dohledem, který počítá riziko pro uživatele pomocí dalších funkcí přihlášení uživatele a vzoru detekce. Na základě tohoto modelu může správce najít uživatele s vysokým rizikovým skóre, a to i v případě, že zjištění přidružená tomuto uživateli mají nízké nebo střední riziko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci můžou resetovat svoje vlastní heslo pomocí aplikace Microsoft Authenticator (Public Preview).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Správci Azure AD teď můžou resetovat svoje vlastní heslo pomocí oznámení aplikace Microsoft Authenticator nebo kódu z jakékoli aplikace pro mobilní data nebo hardwarového tokenu. Aby mohli správci resetovat svoje vlastní heslo, bude teď moct použít dvě z následujících metod:

- Oznámení aplikace Microsoft Authenticator

- Jiná aplikace Mobile Authenticator/kód hardwarového tokenu

- E-mail

- Telefonní hovor

- Textová zpráva

Další informace o použití aplikace Microsoft Authenticator k resetování hesel najdete v tématu [Samoobslužné resetování hesla v Azure AD – mobilní aplikace a SSPR (Preview)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr) .

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role správce cloudového zařízení služby Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Řízení přístupu

Správci můžou k nové roli správce cloudového zařízení přiřadit uživatele, aby mohli provádět úlohy správce cloudového zařízení. Uživatelé s rolí správce cloudových zařízení můžou povolit, zakázat a odstranit zařízení ve službě Azure AD, společně s možnostmi číst klíče Windows 10 BitLocker (pokud jsou k dispozici) v Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí časového razítka nové aktivity ve službě Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Správa životního cyklu zařízení

Uvědomujeme si, že v průběhu času musíte aktualizovat a vyřadit zařízení organizace ve službě Azure AD, abyste se vyhnuli používání zastaralých zařízení ve vašem prostředí. Pro pomoc s tímto procesem Azure AD teď aktualizuje vaše zařízení pomocí nového časového razítka aktivity, které vám pomůže se správou životního cyklu zařízení.

Další informace o tom, jak získat a použít toto časové razítko, najdete v tématu [Postupy: Správa zastaralých zařízení v Azure AD.](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci můžou od uživatelů vyžadovat, aby na každém zařízení přijali podmínky použití.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou
 
Správci teď můžou zapnout možnost **vyžadovat, aby uživatelé souhlasí na všech možnostech zařízení** , aby vaši uživatelé mohli přijmout vaše podmínky použití na všech zařízeních, která používají ve vašem tenantovi.

Další informace najdete v [části používání podmínek použití funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)v rámci zařízení.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci můžou nakonfigurovat podmínek použití, jejichž platnost vyprší na základě plánu opakování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou
 

Správci teď můžou zapnout možnost souhlasu s **vypršením platnosti** , aby vyprší platnost podmínek použití pro všechny vaše uživatele na základě zadaného plánu opakování. Plán může být jednou ročně, každý rok, čtvrtletně nebo měsíčně. Po vypršení platnosti podmínek použití musí uživatelé znovu přijmout.

Další informace najdete v [části Přidání podmínek použití funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci můžou nakonfigurovat platnost podmínek použití na základě plánu každého uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Správci teď můžou zadat dobu, po kterou musí uživatel znovu přijmout podmínky použití. Správci můžou například určit, že uživatelé musí znovu přijmout podmínky použití každých 90 dní.

Další informace najdete v [části Přidání podmínek použití funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nové e-maily Azure AD Privileged Identity Management (PIM) pro Azure Active Directory role

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Zákazníci, kteří používají Azure AD Privileged Identity Management (PIM), teď můžou dostávat týdenní e-mailové zprávy o Digest, včetně následujících informací za posledních sedm dní:

- Přehled hlavních přiřazení s nárokem a trvalými rolemi

- Počet uživatelů, kteří aktivují role

- Počet uživatelů přiřazených k rolím v PIM

- Počet uživatelů přiřazených k rolím mimo PIM

- Počet uživatelů, kteří jsou trvalé, v PIM

Další informace o PIM a dostupných e-mailových oznámeních najdete [v tématu e-mailová oznámení v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencování na základě skupin je teď všeobecně dostupné.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

Licencování na základě skupin je ve verzi Public Preview a je teď všeobecně dostupná. V rámci tohoto obecného vydání jsme tuto funkci udělali lépe škálovatelnou a přidali jsme možnost znovu zpracovat přiřazení licencování na základě skupin pro jednoho uživatele a možnost používat licencování na základě skupin s licencemi Office 365 E3/a3.

Další informace o licencování na základě skupin najdete [v tématu Co je licencování na základě skupin v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – listopad 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V listopadu 2018 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [gettam](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [nekonečné areály](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [unášený](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial) [Zenegy pro firmu Central 365](https://accounting.zenegy.com/), [EverBridge členský portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [aplikace Plex – klasický test ](https://test.plexonline.com/signon) [Aplikace objektů plex – Classic](https://www.plexonline.com/signon), [aplikace Plex – test uživatelského rozhraní](https://test.cloud.plex.com/sso), [aplikace Plex – UX](https://cloud.plex.com/sso), [aplikace Plex – IAM](https://accounts.plex.com/), [řemesla – záznamy o péči, docházka, & finanční sledování](https://getcrafts.ca/craftsregistration) 

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Říjen 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Protokoly Azure AD teď pracují s Azure Log Analytics (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že teď můžete svoje protokoly Azure AD přeslat do Azure Log Analytics! Tato funkce, která je nejvhodnější, vám pomůže zajistit ještě lepší přístup k analýzám vaší firmy, provozu a zabezpečení a také způsob, jak vám pomůže monitorovat infrastrukturu. Další informace najdete v tématu [protokoly aktivit Azure Active Directory v Azure Log Analytics nyní dostupném](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – říjen 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V říjnu 2018 jsme do Galerie aplikací přidali tyto 14 nových aplikací s podporou federace:

[Body ocenění](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler 3](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [hodnocení](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-mailových oznámení

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Azure AD Domain Services poskytuje výstrahy na Azure Portal o nepřesných konfiguracích nebo problémech se spravovanou doménou. Tyto výstrahy obsahují podrobné návody, pomocí kterých můžete zkusit problémy vyřešit, aniž byste museli kontaktovat podporu.

Od října budete moct přizpůsobit nastavení oznámení pro spravovanou doménu, takže když dojde k novým výstrahám, pošle se na určenou skupinu lidí e-mail, takže se nemusíte neustále kontrolovat portál, aby se aktualizace zobrazovaly.

Další informace najdete v tématu [Nastavení oznámení v Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portál Azure AD podporuje používání doménového rozhraní API ForceDelete k odstraňování vlastních domén. 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa adresářů  
**Schopnost produktu:** Službě

S radostí oznamujeme, že teď můžete pomocí rozhraní ForceDelete Domain API odstranit vlastní názvy domén pomocí asynchronního přejmenování odkazů, jako jsou uživatelé, skupiny a aplikace z vlastního názvu domény (contoso.com) zpátky na počáteční výchozí název domény ( contoso.onmicrosoft.com).

Tato změna vám pomůže rychleji odstranit vlastní názvy domén, pokud vaše organizace už nepoužívá název, nebo pokud potřebujete název domény použít pro jinou službu Azure AD.

Další informace najdete v tématu [odstranění vlastního názvu domény](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Září 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualizovaná oprávnění role správce pro dynamické skupiny

**Zadejte:** Určí  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Opravili jsme problém, takže konkrétní role správce teď můžou vytvářet a aktualizovat dynamická pravidla členství, aniž by museli být vlastníkem skupiny.

Role jsou:

- Globální správce

- Správce Intune

- Správce uživatele

Další informace najdete v tématu [Vytvoření dynamické skupiny a kontroly stavu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) .

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Zjednodušené nastavení konfigurace jednotného přihlašování (SSO) pro některé aplikace třetích stran

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Uvědomujeme si, že nastavení jednotného přihlašování (SSO) pro aplikace SaaS (software jako služba) může být náročné vzhledem k jedinečné povaze jednotlivých konfigurací aplikací. Sestavili jsme zjednodušené prostředí pro konfiguraci, které automaticky vyplní nastavení konfigurace jednotného přihlašování pro tyto aplikace SaaS třetích stran:

- Zendesk

- ArcGis online

- Jamf Pro

Pokud chcete začít používat toto prostředí jedním kliknutím, přejděte na stránku konfigurace **Azure Portal** > **SSO** pro danou aplikaci. Další informace najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) .

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – kde se vaše data nacházejí? Page

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** GoLocal

Vyberte oblast vaší společnosti z **Azure Active Directory – kde se nachází vaše data na stránce, kde se nachází vaše** datové centrum Azure pro všechny služby Azure AD. Informace můžete filtrovat podle konkrétních služeb Azure AD pro oblast vaší společnosti.

Pro přístup k této funkci a další informace najdete v tématu [Azure Active Directory – kde se nacházejí vaše data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>K dispozici je nový plán nasazení pro přístupový panel Moje aplikace.

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ

Podívejte se na nový plán nasazení, který je k dispozici pro přístupový panel Moje aplikace (https://aka.ms/deploymentplans).
Přístupový panel Moje aplikace poskytuje uživatelům jediné místo pro vyhledání a přístup ke svým aplikacím. Tento portál také poskytuje uživatelům možnosti samoobslužných služeb, jako je třeba vyžadování přístupu k aplikacím a skupinám nebo Správa přístupu k těmto prostředkům jménem jiné.

Další informace najdete v tématu [co je to portál moje aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nová karta Poradce při potížích a podpoře na stránce protokoly přihlášení Azure Portal

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Nová karta **Poradce při potížích a podpoře** na stránce pro **přihlášení** Azure Portal má pomoct správcům a pracovníkům podpory řešit problémy související s přihlášeními Azure AD. Tato nová karta poskytuje kód chyby, chybovou zprávu a doporučení pro nápravu (pokud existuje), které vám pomůžou problém vyřešit. Pokud tento problém nemůžete vyřešit, poskytujeme vám také nový způsob vytvoření lístku podpory pomocí prostředí **pro kopírování do schránky** , které vyplní pole s **ID žádosti** a **datem (UTC)** pro soubor protokolu ve vašem lístku podpory.  

![Přihlašovací protokoly zobrazující novou kartu](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Rozšířená podpora vlastních vlastností rozšíření používaných k vytváření pravidel dynamického členství

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

V této aktualizaci teď můžete kliknout na odkaz **získat vlastnosti vlastního rozšíření** z Tvůrce pravidla dynamické skupiny uživatelů, zadat jedinečné ID aplikace a získat úplný seznam vlastností vlastního rozšíření, které se použijí při vytváření dynamického pravidla členství pro uživatele. Tento seznam je také možné aktualizovat, aby získal všechny nové vlastnosti rozšíření pro danou aplikaci.

Další informace o použití vlastních vlastností rozšíření pro pravidla dynamického členství najdete v tématu [vlastnosti rozšíření a vlastnosti vlastního rozšíření](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) .

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

V seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement)jsou následující aplikace:

- Microsoft To-Do

- Microsoft Stream

Další informace:

- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nová podpora samoobslužného resetování hesla z zamykací obrazovky Windows 7/8/8.1

**Zadejte:** Nová funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů

Po nastavení této nové funkce se uživatelům zobrazí odkaz pro resetování hesla z **zamykací** obrazovky zařízení se systémem Windows 7, Windows 8 nebo Windows 8.1. Kliknutím na tento odkaz uživatel prochází stejným tokem resetování hesla jako ve webovém prohlížeči.

Další informace najdete v tématu [Jak povolit resetování hesla ze systému Windows 7, 8 a 8,1](https://aka.ms/ssprforwindows78) .

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Upozornění na změnu: autorizační kódy už nebudou dostupné pro opakované použití. 

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Od 15. listopadu 2018 přestane Azure AD přijímat dříve použité ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá přenést službu Azure AD v souladu se specifikací OAuth a vynucuje se v koncových bodech V1 a v2.

Pokud vaše aplikace znovu používá autorizační kódy k získání tokenů pro více prostředků, doporučujeme použít kód k získání obnovovacího tokenu a pak pomocí tohoto aktualizačního tokenu získat další tokeny pro jiné prostředky. Autorizační kódy se dají použít jenom jednou, ale aktualizace tokenů se dají použít víckrát napříč několika prostředky. Aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá invalid_grant chybu.

U těchto a dalších změn souvisejících s protokoly si prohlédněte [úplný seznam novinek pro ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – září 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do září 2018 jsme do Galerie aplikací přidali tyto 16 nových aplikací s podporou federace:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [vyhovující náborový software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), JOIN.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [RACKSPACE SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO pro Azure, SurveyMonkey, [Svoláváme](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Podpora dalších metod transformace deklarací identity

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Zavedli jsme nové metody transformace deklarace identity, ToLower () a ToUpper (), které je možné použít na tokeny SAML z **konfigurační stránky jednotného přihlašování** založené na SAML.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) .

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualizované uživatelské rozhraní konfigurace aplikace založené na SAML (Preview)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Jako součást aktualizovaného uživatelského rozhraní konfigurace aplikace založeného na SAML získáte:

- Aktualizovaný návod pro konfiguraci aplikací založených na SAML.

- Lepší přehled o tom, co v konfiguraci chybí nebo je nesprávné.

- Možnost přidat více e-mailových adres pro oznámení o certifikátu vypršení platnosti.

- Nové metody transformace deklarace identity, ToLower () a ToUpper () a další.

- Způsob, jak nahrát vlastní podpisový certifikát tokenu pro podnikové aplikace.

- Způsob nastavení formátu NameID pro aplikace SAML a způsob nastavení hodnoty NameID jako přípon adresářů.

Pokud chcete zapnout toto aktualizované zobrazení, klikněte na odkaz **vyzkoušet náš nový zážitek** z horní části stránky **jednotného přihlašování** . Další informace najdete v tématu [kurz: Konfigurace jednotného přihlašování založeného na SAML pro aplikaci s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Srpen 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Změny Azure Active Directory rozsahy IP adres

**Zadejte:** Plánování změn  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Platformy

Zavádíme větší rozsahy IP adres do Azure AD, což znamená, že pokud jste nakonfigurovali rozsahy IP adres Azure AD pro brány firewall, směrovače nebo skupiny zabezpečení sítě, budete je muset aktualizovat. Tuto aktualizaci provádíme, takže nebudete muset znovu měnit konfiguraci rozsahu IP adres brány firewall, směrovače ani skupiny zabezpečení sítě, když Azure AD přidá nové koncové body. 

Síťové přenosy se v následujících dvou měsících pohybují do těchto nových rozsahů. Pokud chcete pokračovat v nepřerušované službě, musíte tyto aktualizované hodnoty přidat k IP adresám před 10. září 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Důrazně doporučujeme, abyste staré rozsahy IP adres neodebrali, dokud se veškerý síťový provoz nepřesunul do nových rozsahů. Aktualizace informací o přesunutí a zjištění, kdy můžete odebrat staré rozsahy, najdete v tématu [adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Upozornění na změnu: autorizační kódy už nebudou dostupné pro opakované použití. 

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Od 15. listopadu 2018 přestane Azure AD přijímat dříve použité ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá přenést službu Azure AD v souladu se specifikací OAuth a vynucuje se v koncových bodech V1 a v2.

Pokud vaše aplikace znovu používá autorizační kódy k získání tokenů pro více prostředků, doporučujeme použít kód k získání obnovovacího tokenu a pak pomocí tohoto aktualizačního tokenu získat další tokeny pro jiné prostředky. Autorizační kódy se dají použít jenom jednou, ale aktualizace tokenů se dají použít víckrát napříč několika prostředky. Aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá invalid_grant chybu.

U těchto a dalších změn souvisejících s protokoly si prohlédněte [úplný seznam novinek pro ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Sblížená Správa informací o zabezpečení pro samoobslužné heslo (SSPR) a Multi-Factor Authentication (MFA)

**Zadejte:** Nová funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů
 
Tato nová funkce pomáhá lidem spravovat své bezpečnostní údaje (například telefonní číslo, mobilní aplikace atd.) pro SSPR a MFA v jednom umístění a prostředí. ve srovnání s dřív, kde byla provedena ve dvou různých umístěních.

Toto sblížené prostředí funguje taky pro lidi, kteří používají SSPR nebo MFA. Pokud navíc vaše organizace nevynutila ověřování MFA nebo SSPR, můžou si dál zaregistrovat jakékoli metody zabezpečení MFA nebo SSPR, které vaše organizace povoluje, na portálu moje aplikace.

Toto je veřejná verze Preview. Správci můžou nové prostředí zapnout (Pokud je potřeba) pro vybranou skupinu nebo pro všechny uživatele v tenantovi. Další informace o sblížení prostředí najdete na blogu věnovaném [sbližování zkušeností](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) .

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nové nastavení souborů cookie jen pro protokol HTTP v aplikacích proxy aplikace Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Ve vašich aplikacích proxy aplikací se zavolalo nové nastavení s názvem **soubory cookie jen pro http** . Toto nastavení pomáhá zajistit dodatečné zabezpečení zahrnutím příznaku HTTPOnly do hlavičky HTTP Response pro přístup k proxy aplikací a souborů cookie relace, zastavením přístupu k souboru cookie ze skriptu na straně klienta a dalším zabráněním akcím, jako je kopírování nebo Úprava souboru cookie. I když jste tento příznak ještě nepoužili, soubory cookie se vždycky šifrují a přenášejí pomocí připojení SSL, které vám pomůžou chránit před nesprávnými úpravami.

Toto nastavení není kompatibilní s aplikacemi, které používají ovládací prvky ActiveX, jako je třeba Vzdálená plocha. Pokud jste v této situaci, doporučujeme toto nastavení vypnout.

Další informace o nastavení souborů cookie pouze pro protokol HTTP najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) pro prostředky Azure podporují typy prostředků skupiny pro správu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Nastavení aktivace za běhu a přiřazení se teď dají použít u typů prostředků skupiny pro správu, stejně jako jste už u předplatných, skupin prostředků a prostředků (například virtuálních počítačů, App Services a dalších). Kromě toho může kdokoli s rolí, která poskytuje přístup správcům pro skupinu pro správu, zjišťovat a spravovat tyto prostředky v PIM.

Další informace o prostředcích PIM a Azure najdete v tématu [zjišťování a Správa prostředků Azure pomocí Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Přístup k aplikaci (Preview) poskytuje rychlejší přístup k portálu Azure AD.

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Když dnes aktivujete roli pomocí PIM, může to trvat déle než 10 minut, než se oprávnění projeví. Pokud se rozhodnete použít přístup k aplikaci, který je aktuálně ve verzi Public Preview, můžou správci získat přístup k portálu Azure AD hned po dokončení žádosti o aktivaci.

Přístup k aplikaci v současné době podporuje jenom prostředí portálu Azure AD a prostředky Azure. Další informace o přístupu PIM a aplikace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – srpen 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V srpnu 2018 jsme do Galerie aplikací přidali tyto 16 nových aplikací s podporou federace:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline bez vazby](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Omáčk Labs – mobilní a webové testování](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [konektor meta sítě](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [způsob, jakým](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial)je, [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [prohlavní (od Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentace](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial) [N2F – sestavy výdajů](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Nativní podpora Tableau je teď dostupná v Azure Proxy aplikací služby AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Díky naší aktualizaci z OpenID se připojte k protokolu OAuth 2,0 Code pro náš protokol předběžného ověřování, už nemusíte provádět žádnou další konfiguraci, abyste mohli Tableau použít s proxy aplikací. Tato změna protokolu také pomáhá proxy aplikací lépe podporovat moderní aplikace pomocí pouze přesměrování HTTP, která jsou běžně podporována v jazycích JavaScript a HTML.

Další informace o naší nativní podpoře pro Tableau najdete v tématu [Azure proxy aplikací služby AD nyní s nativní podporou Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nová podpora pro přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B v Azure Active Directory (Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Když nastavíte federaci s Google ve vaší organizaci, můžete pozvaní uživatelé Gmail přihlašovat ke sdíleným aplikacím a prostředkům pomocí svého stávajícího účtu Google, aniž byste museli vytvářet osobní účet Microsoft (účty spravované služby) nebo účet Azure AD.

Toto je veřejná verze Preview. Další informace o Google Federation najdete v tématu [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Červenec 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Vylepšení Azure Active Directory e-mailových oznámení

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa životního cyklu identit
 
Azure Active Directory (Azure AD) – e-maily teď při odeslání z následujících služeb obsahují aktualizovaný návrh a také změny e-mailové adresy odesílatele a zobrazovaného jména odesílatele:
 
- Kontroly přístupu Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Oznámení o certifikátu vypršení platnosti podnikové aplikace
- Oznámení služby zřizování podnikových aplikací
 
E-mailová oznámení budou odeslána z následující e-mailové adresy a zobrazovaného jména:

- E-mailová adresa: azure-noreply@microsoft.com
- Zobrazovaný název: Microsoft Azure
 
Příklad některých nových návrhů e-mailů a další informace najdete v tématu [e-mailová oznámení v Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné prostřednictvím Azure Monitor

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Protokoly aktivit Azure AD jsou teď dostupné ve verzi Public Preview pro Azure Monitor (služba Azure pro monitorování v rámci platformy). Azure Monitor nabízí dlouhodobou dobu uchovávání a bezproblémové integrace, kromě těchto vylepšení:

- Dlouhodobé uchovávání směrováním souborů protokolů do vlastního účtu úložiště Azure.

- Bezproblémová integrace SIEM, aniž byste museli psát nebo udržovat vlastní skripty.

- Bezproblémová integrace s vašimi vlastními řešeními, analytickými nástroji nebo řešeními pro správu incidentů.

Další informace o těchto nových funkcích najdete v článku [protokoly aktivit služby Azure AD ve službě Azure monitor Diagnostics jsou teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) a v naší dokumentaci [Azure Active Directory protokoly aktivit v Azure monitor (Preview)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informace o podmíněném přístupu přidávané do sestavy přihlášení k Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Tato aktualizace vám umožní zjistit, které zásady se vyhodnotí, když se uživatel přihlásí společně s výsledkem zásady. Sestava nyní zahrnuje typ klientské aplikace používané uživatelem, takže můžete identifikovat starší verze protokolu. V položkách sestavy se teď dají vyhledat ID korelace, které se dá najít v uživatelské chybové zprávě a dá se použít k identifikaci a řešení potíží se shodným přihlašovacím požadavkem.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Zobrazit starší ověřování prostřednictvím protokolů aktivit přihlášení

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Díky zavedení pole **klientské aplikace** v protokolech aktivit přihlašování zákazníci teď můžou zobrazit uživatele, kteří používají starší verze ověřování. Zákazníci budou mít k těmto informacím přístup pomocí přihlašovacích údajů MS Graph API nebo prostřednictvím protokolů aktivit přihlašování na portálu Azure AD, kde můžete pomocí ovládacího prvku **klientská aplikace** filtrovat starší verze ověřování. Další podrobnosti najdete v dokumentaci.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červenci 2018 jsme do Galerie aplikací přidali tyto 16 nových aplikací s podporou federace:

[Centrum pro inovace](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [konkrétní jednotné přihlašování pro správce](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC přípravy, [IPASS SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [záznam dění na více než matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool, Sjednocená učebna, [Blažková pro zprovoznění](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar vzdálená podpora](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX ](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Představte si webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [základ dovedností](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nová integrace aplikací SaaS pro zřizování uživatelů – červenec 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebírání identit uživatelů v aplikacích SaaS, jako jsou Dropbox, Salesforce, ServiceNow a další. Pro červenec 2018 jsme přidali podporu zřizování uživatelů pro následující aplikace v galerii aplikací Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusně](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Připojení stavu k synchronizaci – jednodušší způsob, jak opravit osamocené a duplicitní chyby synchronizace atributů

**Zadejte:** Nová funkce  
**Kategorie služby:** Služba AD Connect  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Azure AD Connect Health přináší samoobslužnou nápravu, která vám umožní zvýrazňovat a opravovat chyby synchronizace. Tato funkce řeší chyby synchronizace duplicitních atributů a opravuje objekty, které jsou osamocené z Azure AD. Tato diagnóza má následující výhody:

- Zúží chyby synchronizace duplicitních atributů a poskytuje konkrétní opravy.

- Aplikuje opravu vyhrazených scénářů služby Azure AD a řeší chyby v jednom kroku.

- K zapnutí a použití této funkce není nutný žádný upgrade nebo konfigurace.

Další informace najdete v tématu [Diagnostika a náprava chyb synchronizace duplicitních atributů](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors) .

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuální aktualizace prostředí pro přihlášení k Azure AD a MSA

**Zadejte:** Změněná funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Ověřování uživatelů

Aktualizovali jsme uživatelské rozhraní pro online služby přihlašovací prostředí Microsoftu, například pro Office 365 a Azure. Tato změna způsobí, že obrazovky budou méně zaplněny a přehlednější. Další informace o této změně najdete v článku o [nadcházejících vylepšeních na blogu věnovaném přihlašovacím zkušenostem služby Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nové vydání Azure AD Connect – červenec 2018

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

Nejnovější vydaná verze Azure AD Connect zahrnuje: 

- Opravy chyb a aktualizace podpory 

- Obecná dostupnost integrace příkazového testu federovat

- Aktualizace nejnovějšího klienta SQL 2012 

Další informace o této aktualizaci najdete v tématu [Azure AD Connect: Historie vydání verze.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizace podmínek použití uživatelského rozhraní pro koncové uživatele

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Aktualizujeme řetězec přijetí v uživatelském rozhraní podmínky použití koncového uživatele.

**Aktuální text.** Aby bylo možné získat přístup k prostředkům [tenant], musíte přijmout podmínky použití.<br>**Nový text.** Aby bylo možné získat přístup k prostředku [tenant], je nutné přečíst si podmínek použití.

**Aktuální text:** Zvolíte-li možnost přijmout, znamená to, že souhlasíte se všemi výše uvedenými podmínkami použití.<br>**Nový text:** Kliknutím na přijmout potvrďte, že jste si přečetli a rozumíte podmínky použití.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Předávací ověřování podporuje starší protokoly a aplikace

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Předávací ověřování teď podporuje starší protokoly a aplikace. Následující omezení jsou nyní plně podporovaná:

- Přihlášení uživatelů ke starším klientským aplikacím Office, Office 2010 a Office 2013 bez nutnosti moderního ověřování.

- Přístup k informacím o sdílení kalendáře a volném a zaneprázdněném čase v hybridních prostředích Exchange v Office 2010.

- Přihlášení uživatelů k klientským aplikacím Skypu pro firmy bez nutnosti moderního ověřování.

- Přihlášení uživatelů do prostředí PowerShell verze 1,0.

- Apple Program registrace zařízení (DEP), používá pomocníka s nastavením iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Sblížená Správa informací o zabezpečení pro Samoobslužné resetování hesla a Multi-Factor Authentication

**Zadejte:** Nová funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů

Tato nová funkce umožňuje uživatelům spravovat své bezpečnostní údaje (například telefonní číslo, e-mailovou adresu, mobilní aplikaci atd.) pro Samoobslužné resetování hesla (SSPR) a Multi-Factor Authentication (MFA) v jednom prostředí. Uživatelé už nebudou muset registrovat stejné bezpečnostní údaje pro SSPR a MFA ve dvou různých prostředích. Toto nové prostředí platí také pro uživatele, kteří mají buď SSPR nebo MFA.

Pokud organizace nevynucována registrace MFA nebo SSPR, můžou si uživatelé zaregistrovat svoje bezpečnostní údaje prostřednictvím portálu **Moje aplikace** . Odtud si uživatelé můžou zaregistrovat jakékoli metody, které jsou povolené pro MFA nebo SSPR. 

Toto je veřejná verze Preview. Správci můžou nové prostředí zapnout (Pokud je potřeba) pro vybranou skupinu uživatelů nebo všechny uživatele v tenantovi.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Použití aplikace Microsoft Authenticator k ověření vaší identity při resetování hesla

**Zadejte:** Změněná funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů

Tato funkce umožňuje neoprávněným správcům ověřovat své identity během obnovování hesla pomocí oznámení nebo kódu z Microsoft Authenticator (nebo jakékoli jiné ověřovací aplikace). Když správci tuto metodu samoobslužného resetování hesla zapnou, uživatelé, kteří si zaregistrují mobilní aplikaci prostřednictvím aka.ms/mfasetup nebo aka.ms/setupsecurityinfo, můžou svou mobilní aplikaci použít jako metodu ověření při resetování hesla.

Oznámení mobilní aplikace lze zapnout pouze v rámci zásady, která vyžaduje dvě metody resetování hesla.

---

## <a name="june-2018"></a>Červen 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Upozornění na změnu: Oprava zabezpečení pro delegovaný tok autorizace pro aplikace s využitím rozhraní API protokolů aktivit Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Z důvodu našeho silnějšího vynucování zabezpečení jsme museli provést změnu oprávnění pro aplikace, které používají delegovaný tok autorizace pro přístup k [rozhraním API protokolů aktivit služby Azure AD](https://aka.ms/aadreportsapi). Tato změna se projeví v **26. června 2018**.

Pokud některá z vašich aplikací používá rozhraní API protokolů aktivit Azure AD, zajistěte, aby se aplikace po změně nepřerušila.

**Aktualizace oprávnění aplikace**

1. Přihlaste se k Azure Portal, vyberte **Azure Active Directory**a pak vyberte **Registrace aplikací**.
2. Vyberte aplikaci, která používá rozhraní API protokolů aktivit Azure AD, vyberte **Nastavení**, vyberte **požadovaná oprávnění**a pak vyberte rozhraní **Windows Azure Active Directory** API.
3. V oblasti **delegovaná oprávnění** v okně **Povolit přístup** zaškrtněte políčko u **číst data adresáře** a pak vyberte **Uložit**.
4. Vyberte **udělit oprávnění**a pak vyberte **Ano**.
    
    >[!Note]
    >Abyste mohli udělit oprávnění k aplikaci, musíte být globálním správcem.

Další informace najdete v části [udělení oprávnění](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) pro přístup k rozhraní API pro vytváření sestav Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurace nastavení TLS pro připojení ke službám Azure AD pro PCI DSS dodržování předpisů

**Zadejte:** Nová funkce  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Platformy

TLS (Transport Layer Security) je protokol, který poskytuje ochranu osobních údajů a dat mezi dvěma komunikujícími aplikacemi a jedná se o široce nasazený protokol zabezpečení, který se dnes používá.

[Rada standardů zabezpečení sběrnice PCI](https://www.pcisecuritystandards.org/) zjistila, že je třeba zakázat dřívější verze TLS a SSL (Secure SOCKETS Layer) (SSL), a to v souladu s tím, že zajišťují nové a bezpečnější protokoly aplikací, od **30. června 2018**. Tato změna znamená, že pokud se připojíte ke službám Azure AD a vyžadujete PCI DSS dodržování předpisů, je nutné zakázat TLS 1,0. K dispozici je více verzí protokolu TLS, ale protokol TLS 1,2 je nejnovější verze dostupná pro Azure Active Directory služby. Důrazně doporučujeme přesunovat se přímo na protokol TLS 1,2 pro kombinace klienta i serveru a prohlížeče/serveru.

Neaktuální prohlížeče nemusí podporovat novější verze TLS, jako je například TLS 1,2. Pokud chcete zjistit, které verze TLS podporuje váš prohlížeč, přejděte na web [Qualys SSL Labs](https://www.ssllabs.com/) a klikněte na **testovat prohlížeč**. Doporučujeme upgradovat na nejnovější verzi webového prohlížeče a nejlépe povolit pouze TLS 1,2.

**Povolení protokolu TLS 1,2 podle prohlížeče**

- **Microsoft Edge a Internet Explorer (obě se nastavují pomocí Internet Exploreru)**

    1. Otevřete Internet Explorer, vyberte **nástroje** > **Možnosti Internetu** > **Upřesnit**.
    2. V oblasti **zabezpečení** vyberte **použít TLS 1,2**a pak vyberte **OK**.
    3. Zavřete všechna okna prohlížeče a restartujte aplikaci Internet Explorer. 

- **Google Chrome**

    1. Otevřete Google Chrome, do adresního řádku zadejte *Chrome://Settings/* a stiskněte **ENTER**.
    2. Rozbalte **Rozšířené** možnosti, klikněte na oblast **systému** a vyberte **Otevřít nastavení proxy serveru**.
    3. V poli **Vlastnosti Internetu** vyberte kartu **Upřesnit** , v oblasti **zabezpečení** vyberte možnost **použít TLS 1,2**a pak vyberte **OK**.
    4. Zavřete všechna okna prohlížeče a restartujte Google Chrome.

- **Mozilla Firefox**

    1. Otevřete Firefox, do adresního řádku zadejte *About: config* a potom stiskněte klávesu **ENTER**.
    2. Vyhledejte termín, *TLS*a pak vyberte položku **Security. TLS. Version. Max** .
    3. Nastavte hodnotu na **3** , pokud chcete, aby prohlížeč používal až verzi TLS 1,2, a pak vyberte **OK**.

        >[!NOTE]
        >Firefox verze 60,0 podporuje TLS 1,3, takže můžete také nastavit hodnotu Security. TLS. Version. Max na **4**.

    4. Zavřete všechna okna prohlížeče a restartujte Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do června 2018 jsme do Galerie aplikací přidali tyto 15 nových aplikací s podporou federace:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Vyrovnávání hudby](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), obchodní [aplikace s povoleným tokenem SAML 1,1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [nálada](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), automatického [úkolu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [zálohování koncových bodů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh sítě](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [Místní SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [předvídatelná sada CX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrana heslem Azure AD je dostupná ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Ověřování uživatelů

Pomocí ochrany heslem v Azure AD můžete eliminovat snadné odhadované heslo z vašeho prostředí. Tato hesla se neodstraňují, což pomáhá snížit riziko napadení útokem typu spreje hesla.

Konkrétně ochrana heslem Azure AD vám pomůže:

- Chraňte účty vaší organizace v Azure AD i ve službě Active Directory (AD) ve Windows serveru. 
- Zabrání uživatelům používat hesla na seznamu více než 500 nejčastěji používaných hesel a více než 1 000 000 nahrazování znaků u těchto hesel.
- Spravujte ochranu heslem Azure AD z jednoho místa na portálu Azure AD pro Azure AD i místní Windows Server AD.

Další informace o ochraně heslem Azure AD najdete v tématu [odstranění chybných hesel ve vaší organizaci](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu všichni hosté vytvořená během vytváření podmínek použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Při vytváření podmínek použití se vytvoří taky nová šablona zásad podmíněného přístupu pro všechny hosty a všechny aplikace. Tato nová šablona zásad aplikuje nově vytvořené podmínky použití, což zjednodušuje proces vytváření a vynucení hostů.

Další informace najdete v tématu [funkce Azure Active Directory podmínky použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu vytvořená během vytváření podmínek použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Při vytváření podmínek použití je vytvořena také nová šablona zásad podmíněného přístupu "vlastní". Tato nová šablona zásad vám umožní vytvořit podmínky použití a pak hned přejít do okna pro vytvoření zásady podmíněného přístupu, aniž byste museli ručně procházet portálem.

Další informace najdete v tématu [funkce Azure Active Directory podmínky použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nové a komplexní pokyny k nasazení služby Azure Multi-Factor Authentication

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vydali jsme nové podrobné pokyny, jak nasadit Azure Multi-Factor Authentication (MFA) ve vaší organizaci.

Průvodce nasazením MFA zobrazíte tak, že přejdete do úložiště průvodce [nasazením identity](https://aka.ms/DeploymentPlans) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům při nasazení, použijte [formulář zpětné vazby plánu nasazení](https://aka.ms/deploymentplanfeedback). Pokud máte nějaké dotazy týkající se průvodců nasazením, kontaktujte nás na adrese [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Role správy aplikací delegovaných službou Azure AD jsou ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Access Control

Správci teď můžou delegovat úlohy správy aplikací bez přiřazení role globálního správce. Nové role a možnosti jsou:

- **Nové standardní role správce Azure AD:**

    - **Správce aplikace.** Uděluje možnost spravovat všechny aspekty všech aplikací, včetně registrace, nastavení jednotného přihlašování, přiřazení a Licencování aplikací, nastavení proxy aplikací a souhlasu (s výjimkou prostředků Azure AD).

    - **Správce cloudové aplikace.** Udělí všem schopnostem správce aplikace, s výjimkou proxy aplikací, protože neposkytuje místní přístup.

    - **Vývojář aplikace** Udělí možnost vytvářet registrace aplikací i v případě, že je vypnutá možnost **Povolit uživatelům registraci aplikací** .

- **Vlastnictví (nastavení registrace jednotlivých aplikací a aplikace na podnikové úrovni, podobně jako u procesu vlastnictví skupiny:**
 
    - **Vlastník registrace aplikace** Umožňuje spravovat všechny aspekty vlastní registrace aplikace, včetně manifestu aplikace a přidání dalších vlastníků.

    - **Vlastník podnikové aplikace** Uděluje možnost spravovat spoustu aspektů vlastněných podnikových aplikací, včetně nastavení jednotného přihlašování, přiřazení aplikací a souhlasu (s výjimkou prostředků Azure AD).

Další informace o veřejné verzi Preview najdete v tématu [role správy delegovaných aplikací Azure AD ve verzi Public Preview.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) webový. Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Květen 2018

### <a name="expressroute-support-changes"></a>Změny podpory ExpressRoute

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Platformy  

Software jako nabídka služeb, jako je například Azure Active Directory (Azure AD), je navržený tak, aby fungoval nejlépe přímo přes Internet, aniž by to vyžadovalo ExpressRoute nebo jiné privátní tunely VPN. V důsledku toho od **1. srpna 2018**zastavíme podporu ExpressRoute pro služby Azure AD s využitím veřejných partnerských vztahů Azure a komunit Azure v partnerském vztahu Microsoftu. Jakékoli služby, na které se tato změna týká, může znamenat, že se provoz Azure AD postupně přesouvá z ExpressRoute na Internet.

I když měníme naši podporu, ví, že stále existují situace, kdy možná budete muset pro svůj ověřovací provoz použít vyhrazenou sadu okruhů. Z tohoto důvodu bude Azure AD dál podporovat omezení rozsahu IP adres na základě klientů pomocí ExpressRoute a služeb, které už jsou v partnerském vztahu Microsoftu, s "jinými komunitními službami Office 365 online". Pokud jsou ovlivněné vaše služby, ale vyžadujete ExpressRoute, musíte provést následující:

- **Pokud pracujete ve veřejném partnerském vztahu Azure.** Přejděte na partnerský vztah Microsoftu a zaregistrujte se do **jiné komunity Office 365 Online Services (12076:5100)** . Další informace o tom, jak přejít z veřejného partnerského vztahu Azure na partnerský vztah Microsoftu, najdete v článku [přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Pokud jste v partnerském vztahu Microsoftu.** Zaregistrujte se do **jiné komunity služeb Office 365 online (12076:5100)** . Další informace o požadavcích směrování najdete v [části Podpora komunit protokolu BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) v článku požadavky na směrování ExpressRoute.

Pokud musíte i nadále používat vyhrazené okruhy, budete muset kontaktovat tým účtu Microsoft o tom, jak získat autorizaci k používání **jiné komunity služeb Office 365 online (12076:5100)** . Prověřená deska spravovaná sadou MS Office ověří, zda potřebujete tyto okruhy, a ujistěte se, že rozumíte technickým dopadům jejich uchování. Neoprávněná předplatná, která se pokoušejí vytvořit filtry tras pro Office 365, obdrží chybovou zprávu. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph rozhraní API pro scénáře správy pro podmínky použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Vývojářské prostředí
 
Přidali jsme Microsoft Graph rozhraní API pro operace správy podmínek použití Azure AD. Můžete vytvářet, aktualizovat a odstraňovat podmínek použití objektu.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Přidání koncového bodu Azure AD s více klienty jako zprostředkovatele identity v Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Pomocí vlastních zásad teď můžete do Azure AD B2C přidat běžný koncový bod Azure AD jako zprostředkovatele identity. To vám umožní mít pro všechny uživatele Azure AD, kteří se přihlásí k vašim aplikacím, jediný bod. Další informace najdete v tématu [Azure Active Directory B2C: povolení přihlášení uživatelů k víceklientskému zprostředkovateli identit Azure AD pomocí vlastních zásad](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Použití interních adres URL k přístupu k aplikacím odkudkoli pomocí našeho přihlašovacího rozšíření moje aplikace a Azure Proxy aplikací služby AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Uživatelé teď můžou k aplikacím přistupovat prostřednictvím interních adres URL i v případě, že jsou mimo podnikovou síť pomocí rozšíření pro zabezpečené přihlášení k aplikacím pro Azure AD. To bude fungovat u všech aplikací, které jste publikovali pomocí Azure Proxy aplikací služby AD, v jakémkoli prohlížeči, který má nainstalované rozšíření prohlížeče přístupového panelu. Funkce přesměrování adresy URL se automaticky povolí po přihlášení uživatele k rozšíření. Rozšíření je k dispozici ke stažení v [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – data v Evropě pro zákazníky v Evropě

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** GoLocal

Zákazníci v Evropě vyžadují, aby jejich data zůstaly v Evropě a nereplikují se mimo Evropská datacentra pro ochranu osobních údajů a Evropské zákony. Tento [článek](https://go.microsoft.com/fwlink/?linkid=872328) obsahuje podrobné informace o tom, jaké informace o identitě se budou ukládat v rámci Evropy, a poskytuje také podrobnosti o informacích, které se uloží mimo Evropská datová centra. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nová integrace aplikací SaaS pro zřizování uživatelů – květen 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebírání identit uživatelů v aplikacích SaaS, jako jsou Dropbox, Salesforce, ServiceNow a další. Pro květen 2018 jsme přidali podporu zřizování uživatelů pro následující aplikace v galerii aplikací Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Základ – OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v tématu [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Kontroly přístupu skupin a přístupu k aplikacím v Azure AD teď poskytují opakované recenze.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Řádnou
 
Kontrola přístupu skupin a aplikací je teď obecně dostupná jako součást Azure AD Premium P2.  Správci budou moct nakonfigurovat kontroly přístupu pro členství ve skupinách a přiřazení aplikací tak, aby se automaticky opakovaly v pravidelných intervalech, například měsíčně nebo čtvrtletně.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Protokoly aktivit Azure AD (přihlášení a audit) jsou teď dostupné prostřednictvím MS graphu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Protokoly aktivit Azure AD, které obsahují protokoly přihlášení a auditu, jsou teď k dispozici v MS graphu. Pro přístup k těmto protokolům jsme nastavili dva koncové body prostřednictvím MS graphu. Pokud chcete začít, podívejte se na naše [dokumenty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) pro programový přístup k rozhraním API pro generování sestav Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Vylepšení prostředí pro uplatnění B2B a opuštění organizace

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

**Doba uplatnění:** Po sdílení prostředku s uživatelem typu Host pomocí rozhraní B2B API – nemusíte posílat speciální e-mail s pozvánkou. Ve většině případů může uživatel typu Host získat přístup k prostředku a provede se v prostředí pro vyplacení za běhu. Žádný další vliv v důsledku nezmeškaných e-mailů. Nemusíte už nic dalšího zeptat na uživatele typu Host. po kliknutí na odkaz na toto uplatnění vás systém poslal? ". To znamená, že když SPO použije správce pozvání – cloudové přílohy můžou mít stejnou kanonickou adresu URL pro všechny uživatele – interní a externí – v jakémkoli stavu uplatnění.

**Moderní prostředí pro uplatnění:** Žádná další cílová stránka pro vyplacení obrazovky S prohlášením o zásadách ochrany osobních údajů v organizaci se uživatelům zobrazí moderní možnosti souhlasu, stejně jako u aplikací třetích stran.

**Uživatelé typu Host můžou organizaci opustit:** Jakmile se vztah uživatele s organizací převezme na více, můžou ho opustit. Žádné další volání správce pro pozvání k odebrání se neprovádí, žádné další vyzvednutí lístků podpory.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V květnu 2018 jsme do Galerie aplikací přidali tyto 18 nových aplikací s podporou federace:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty řídí, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), MONTAGE [online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [ARC Publikování – SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [hejno](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [ReviewSnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nové podrobné pokyny k nasazení pro Azure Active Directory

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě
 
Nové, podrobné pokyny k nasazení Azure Active Directory (Azure AD), včetně samoobslužného resetování hesla (SSPR), jednotného přihlašování (SSO), podmíněného přístupu (CA), proxy aplikací, zřizování uživatelů Active Directory Federation Services (AD FS) (ADFS) pro Předávací ověřování (PTA) a ADFS na synchronizaci hodnot hash hesel (KOSMETICE).

Průvodce nasazením zobrazíte tak, že přejdete do úložiště [Průvodce nasazením identity](https://aka.ms/DeploymentPlans) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům při nasazení, použijte [formulář zpětné vazby plánu nasazení](https://aka.ms/deploymentplanfeedback). Pokud máte nějaké dotazy týkající se průvodců nasazením, kontaktujte nás na adrese [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vyhledávání podnikových aplikací – načtení dalších aplikací

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Nedaří se vám najít vaše aplikace nebo instanční objekty? Přidali jsme možnost načíst další aplikace v seznamu všechny aplikace v podnikových aplikacích. Ve výchozím nastavení se zobrazuje 20 aplikací. Teď můžete kliknout na **načíst další** a zobrazit další aplikace. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Vydaná verze AADConnect obsahuje veřejnou verzi Preview integrace s PingFederate, důležitými aktualizacemi zabezpečení, mnoha opravami chyb a novými skvělými novými nástroji pro řešení potíží. 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Služba AD Connect  
**Schopnost produktu:** Správa životního cyklu identit
 
Vydaná verze AADConnect obsahuje veřejnou verzi Preview integrace s PingFederate, důležitými aktualizacemi zabezpečení, mnoha opravami chyb a novými skvělými novými nástroji pro řešení potíží. Poznámky k verzi najdete [tady](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Kontroly přístupu ke službě Azure AD: automatické použití

**Zadejte:** Změněná funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Řádnou

Kontroly přístupu skupin a aplikací jsou teď obecně dostupné jako součást Azure AD Premium P2. Správce se může nakonfigurovat tak, aby automaticky pro tuto skupinu nebo aplikaci použili změny kontrolora při dokončení kontroly přístupu. Správce může také určit, co se stane s pokračováním přístupu uživatele, pokud kontroloři nereagovali, odebrali přístup, zanechali přístup nebo mají doporučení k systému. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokeny ID se už nedají vracet pomocí response_mode dotazů pro nové aplikace. 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Aplikace vytvořené od 25. dubna 2018 již nebudou moci vyžádat **id_token** pomocí **dotazu** response_mode.  Tím se dokončí služba Azure AD inline se specifikacemi OIDC a pomůže vám omezit plochu pro útoky na vaše aplikace.  Aplikace vytvořené před 25. dubna 2018 nejsou zablokovány pomocí **dotazu** response_mode s response_typeem **id_token**.  Při požadavku na id_token z AAD se vrátila Chyba **AADSTS70007: dotaz není při požadavku na token podporovanou hodnotou response_mode**.

**Fragment** a **form_post** response_modes i nadále fungují – při vytváření nových aplikačních objektů (například pro využití proxy aplikací) zajistěte použití jednoho z těchto response_modes ještě předtím, než vytvoří novou aplikaci.  

---
 
## <a name="april-2018"></a>Duben 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Přístupový token Azure AD B2C je GA.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C 

Přístup k webovým rozhraním API se teď dá zabezpečit Azure AD B2C pomocí přístupových tokenů. Funkce se přesouvá z verze Public Preview na GA. Prostředí uživatelského rozhraní pro konfiguraci Azure AD B2Cch aplikací a webových rozhraní API bylo vylepšeno a bylo provedeno další menší vylepšení.
 
Další informace najdete v tématu [Azure AD B2C: vyžádání přístupových tokenů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testování konfigurace jednotného přihlašování pro aplikace založené na SAML

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Při konfiguraci aplikací jednotného přihlašování založeného na SAML budete moci otestovat integraci na stránce konfigurace. Pokud během přihlašování dojde k chybě, můžete zadat chybu v prostředí testování a Azure AD vám poskytne postup řešení pro vyřešení konkrétního problému.

Další informace:

- [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak ladit jednotné přihlašování založené na SAML pro aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Služba Azure AD terms of use teď má vytváření sestav pro jednotlivé uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 
Správci teď můžou vybrat konkrétní podmínky použití a zobrazit všechny uživatele, kteří se k danému podmínky použitíu poslali, a datum a čas, ke kterému došlo.

Další informace najdete v tématu [funkce Azure AD terms of use](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: riziková IP adresa pro AD FS uzamknutí ochrany extranetu 

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Monitorování & vytváření sestav

Funkce Connect Health teď podporuje detekci IP adres, které překročí prahovou hodnotu neúspěšných přihlášení U/P na každou hodinu nebo každý den. K dispozici jsou tyto funkce:

- Obsáhlá sestava ukazující IP adresu a počet neúspěšných přihlášení vygenerovaných každou hodinu a každý den pomocí přizpůsobitelné prahové hodnoty.
- Výstrahy na základě e-mailu, které ukazují, kdy konkrétní IP adresa překročila prahovou hodnotu neúspěšných přihlášení U/P za hodinu a každý den.
- Možnost stažení, která provede podrobnou analýzu dat

Další informace najdete v tématu [riziková zpráva protokolu IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Snadná konfigurace aplikace se souborem metadat nebo adresou URL

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Na stránce podnikové aplikace mohou správci nahrát soubor metadat SAML pro konfiguraci přihlašování založeného na SAML pro aplikace pro galerii AAD a mimo galerii.

Navíc můžete použít adresu URL federačních metadat služby Azure AD ke konfiguraci jednotného přihlašování s cílovou aplikací.

Další informace najdete v tématu [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Služba Azure AD Podmínky použití je teď všeobecně dostupná

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 

Azure AD terms of use se přesunuly z verze Public Preview na všeobecně dostupné.

Další informace najdete v tématu [funkce Azure AD terms of use](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolí nebo zablokuje pozvánky uživatelům B2B z konkrétních organizací.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Teď můžete určit, které partnerské organizace chcete sdílet, a spolupracovat s nimi ve spolupráci Azure AD B2B. Chcete-li to provést, můžete zvolit vytvoření seznamu konkrétních domén povolení nebo odepření. Když je doména zablokovaná pomocí těchto možností, zaměstnanci už nebudou moct posílat pozvánky lidem v této doméně.

To vám pomůže řídit přístup k vašim prostředkům a zároveň zajistit hladké prostředí pro schválené uživatele.

Tato funkce spolupráce B2B je dostupná pro všechny Azure Active Directory zákazníky a dá se používat ve spojení s Azure AD Premium funkcemi, jako je podmíněný přístup a ochrana identity, pro podrobnější kontrolu nad tím, kdy a jak se budou externí uživatelé přihlašovat. v a získáte přístup.

Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V dubnu 2018 jsme do Galerie aplikací přidali tyto 13 nových aplikací s podporou federace:

Kritérium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamický signál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [organizační diagram nyní](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial) [ Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Policy, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Udělení přístupu k místním aplikacím uživatelům B2B v Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Jako organizace, která používá funkce spolupráce B2B v Azure Active Directory (Azure AD) k pozvání uživatelů typu host z partnerských organizací do služby Azure AD, teď můžete uživatelům B2B poskytnout přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování systému Windows (IWA) s omezeným delegováním protokolu Kerberos (KCD).

Další informace najdete v tématu [udělení přístupu k místním aplikacím uživatelům B2B v Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Získat kurzy pro integraci jednotného přihlašování z Azure Marketplace

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** integrace třetích stran

Pokud aplikace, která je uvedena na [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) , podporuje jednotné přihlašování založené na SAML, kliknutím na **získat teď** získáte kurz integrace přidružený k této aplikaci. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Rychlejší výkon automatického zřizování uživatelů Azure AD pro aplikace SaaS

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Dříve mohli zákazníci, kteří používají Azure Active Directory konektory zřizování uživatelů pro aplikace SaaS (například Salesforce, ServiceNow a box), mít pomalý výkon, pokud jejich klienti Azure AD uvedli více než 100 000 kombinovaných uživatelů a skupiny a používali přiřazení uživatelů a skupin k určení uživatelů, kteří mají být zřízeni.

Od 2. dubna 2018 se do služby zřizování Azure AD nasadila významná vylepšení výkonu, která významně zkracují dobu potřebnou k provádění počátečních synchronizací mezi Azure Active Directory a cílovými SaaS aplikacemi.

V důsledku toho mnoho zákazníků, kteří používali počáteční synchronizaci s aplikacemi, které trvalo mnoho dnů nebo nikdy nedokončili, je teď dokončené do několika minut nebo hodin.

Další informace najdete v tématu [co se stane při zřizování?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobslužné resetování hesla z zamykací obrazovky Windows 10 pro počítače připojené k hybridní službě Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů
 
Aktualizovali jsme funkci Windows 10 SSPR tak, aby zahrnovala podporu pro počítače, které jsou připojené k hybridní službě Azure AD. Tato funkce je k dispozici ve Windows 10 RS4 umožňuje uživatelům resetovat heslo z zamykací obrazovky počítače s Windows 10. Tato funkce může využívat uživatelé, kteří jsou povoleni a registrováni pro Samoobslužné resetování hesla.

Další informace najdete v tématu [resetování hesla Azure AD na přihlašovací obrazovce](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Březen 2018
 
### <a name="certificate-expire-notification"></a>Oznámení o vypršení platnosti certifikátu

**Zadejte:** Určí  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Azure AD pošle oznámení, když brzy vyprší platnost certifikátu pro galerii nebo aplikaci mimo galerii. 

Někteří uživatelé neobdrželi oznámení pro podnikové aplikace nakonfigurované pro jednotné přihlašování založené na SAML. Tento problém byl vyřešen. Azure AD odesílá oznámení o vypršení platnosti certifikátů do 7, 30 a 60 dnů. Tuto událost můžete zobrazit v protokolech auditu. 

Další informace:

- [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Poskytovatelé identit na Twitteru a GitHubu v Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Nyní můžete do Azure AD B2C přidat Twitter nebo GitHub jako zprostředkovatele identity. Twitter se přesouvá z verze Public Preview na GA. GitHub se uvolňuje ve verzi Public Preview.

Další informace najdete v tématu [co je spolupráce B2B Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Omezení přístupu přes prohlížeč pomocí Intune Managed Browser s podmíněným přístupem založeným na aplikaci Azure AD pro iOS a Android

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
**Nyní ve verzi Public Preview!**

**Intune MANAGED Browser jednotné přihlašování:** Vaši zaměstnanci můžou používat jednotné přihlašování v rámci nativních klientů (například Microsoft Outlook) a Intune Managed Browser pro všechny aplikace připojené ke službě Azure AD.

**Intune Managed Browser podpora podmíněného přístupu:** Nyní můžete vyžadovat, aby zaměstnanci používali spravované prohlížeče Intune s využitím zásad podmíněného přístupu na základě aplikace.

Další informace najdete v našem [blogovém příspěvku](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Další informace:

- [Nastavení podmíněného přístupu na základě aplikace](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurace zásad spravovaného prohlížeče](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Rutiny proxy aplikací v modulu PowerShell GA

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control
 
Podpora pro rutiny proxy aplikací je teď v modulu PowerShell GA. To vyžaduje, abyste si udrželi přehled o modulech PowerShellu – Pokud se stanete více než rokem, některé rutiny můžou přestat fungovat. 

Další informace najdete v tématu [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Bezproblémové jednotné přihlašování podporuje nativní klienty Office 365 pomocí neinteraktivního protokolu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Uživatel, který používá nativní klienty Office 365 (verze 16.0.8730. xxxx a vyšší), získá pro vás tiché přihlašování pomocí bezproblémového jednotného přihlašování. Tato podpora je poskytována přidáním neinteraktivního protokolu (WS-Trust) do služby Azure AD.

Další informace najdete v tématu [jak se přihlašuje na nativním klientovi s bezproblémovým PŘIhlašováním](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work) .
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Pokud aplikace odesílá žádosti o přihlášení do koncových bodů tenanta služby Azure AD, získá uživatelům tiché přihlašování s bezproblémové jednotné přihlašování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Když aplikace (například `https://contoso.sharepoint.com`) odesílá žádosti o přihlášení do koncových bodů tenanta Azure AD – to znamená `https://login.microsoftonline.com/contoso.com/<..>` nebo `https://login.microsoftonline.com/<tenant_ID>/<..>` – místo společného koncového`https://login.microsoftonline.com/common/<...>`bodu služby Azure AD, se uživatelům zobrazí tiché přihlašování s bezproblémové jednotné přihlašování.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>K zavedení bezproblémového jednotného přihlašování je potřeba přidat jenom jednu adresu URL služby Azure AD místo dvou adres URL do nastavení zóny intranetu uživatelů.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
K zavedení bezproblémového jednotného přihlašování pro vaše uživatele musíte do nastavení zóny intranetu uživatele přidat jenom jednu adresu URL služby Azure AD pomocí zásad skupiny ve službě Active Directory: `https://autologon.microsoftazuread-sso.com`. Dřív museli zákazníci přidat dvě adresy URL.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V březnu 2018 jsme do Galerie aplikací přidali tyto 15 nových aplikací s podporou federace:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant podle FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inmrknutí, [amplituda](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [ Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM je všeobecně dostupná pro prostředky Azure.

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Pokud používáte Azure AD Privileged Identity Management pro role adresáře, můžete teď pro role prostředků Azure, jako jsou například předplatná, skupiny prostředků, Virtual Machines a všechny podporované prostředky, použít možnosti přístupu a přiřazování na základě času PIM. Azure Resource Manager. Vyjistěte Multi-Factor Authentication při aktivaci rolí za běhu a naplánujte aktivace v koordinaci se schválenými okny změn. Kromě toho tato verze přidává vylepšení, která nejsou během veřejné verze Preview k dispozici, včetně aktualizovaného uživatelského rozhraní, pracovních postupů schválení a možnosti prodloužit platnost rolí, jejichž platnost brzy vyprší a obnoví.

Další informace najdete v tématu [prostředky PIM pro Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) .
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Přidání volitelných deklarací identit do tokenů aplikací (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Vaše aplikace Azure AD teď může vyžádat vlastní nebo volitelné deklarace identity v JWTs nebo tokenech SAML.  Jedná se o deklarace identity uživatele nebo tenanta, které nejsou ve výchozím nastavení v tokenu zahrnuty, z důvodu velikosti nebo omezení použitelnosti.  Tato verze je v současnosti ve verzi Public Preview pro aplikace Azure AD v koncových bodech v 1.0 a v 2.0.  V dokumentaci naleznete informace o tom, jaké deklarace identity je možné přidat a jak upravit manifest aplikace pro vyžádání.  

Další informace najdete v tématu [volitelné deklarace identity ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD podporuje PKCE pro bezpečnější toky OAuth.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Dokumentace k Azure AD se aktualizovala tak, aby poznamenala podporu pro PKCE, která umožňuje bezpečnější komunikaci během procesu udělení autorizačního kódu OAuth 2,0.  V koncových bodech v 1.0 a v 2.0 jsou podporovány jak S256, tak i prostých code_challenges. 

Další informace najdete v tématu [vyžádání autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Podpora zřizování všech hodnot atributů uživatelů dostupných v Get_Workers rozhraní API pro Workday

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Verze Public Preview pro příchozí zřizování z Workday do Active Directory a Azure AD teď podporuje možnost extrahovat a zřídit všechny hodnoty atributů dostupné v rozhraní Get_Workers API pro Workday. Tím se přidá podpora stovky dalších standardních a vlastních atributů nad rámec těch dodaných s počáteční verzí konektoru pro příchozí zřizování Workday.

Další informace najdete v tématu [přizpůsobení seznamu atributů uživatele Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes) .

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Změna členství ve skupině z dynamického na statickou a naopak

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 
Je možné změnit způsob správy členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID v systému, takže všechny existující odkazy na skupinu jsou stále platné. Vytvoření nové skupiny by vyžadovalo aktualizaci těchto odkazů.
Aktualizovali jsme centrum pro správu Azure AD, abychom tuto funkci podporovali. Zákazníci teď můžou převést existující skupiny z dynamického členství na přiřazené členství a naopak. Existující rutiny prostředí PowerShell jsou také stále k dispozici.

Další informace najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Vylepšení chování při odhlášení pomocí bezproblémového jednotného přihlašování

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Dříve i v případě, že se uživatelé explicitně odhlásili z aplikace zabezpečené službou Azure AD, budou automaticky znovu podepsáni pomocí bezproblémového jednotného přihlašování, pokud se pokusí o přístup k aplikaci Azure AD znovu v rámci jejich Corpnet ze svých zařízení připojených k doméně. S touto změnou se podporuje odhlášení.  Díky tomu mohou uživatelé zvolit stejný nebo jiný účet služby Azure AD, aby se mohli znovu přihlásit pomocí bezproblémového přihlašování (SSO).

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) .
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Vydaná verze konektoru proxy aplikací 1.5.402.0

**Zadejte:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Tato verze konektoru je postupně zahrnuta do listopadu. Tato nová verze konektoru obsahuje následující změny:

- Konektor nyní nastaví soubory cookie na úrovni domény namísto úrovně subdomény. Tím se zajistí plynulejší přístup k jednotnému přihlašování a vyhne se redundantním výzvám ověřování.
- Podpora pro požadavky na kódování v bloku
- Vylepšené monitorování stavu konektoru 
- Několik oprav chyb a vylepšení stability

Další informace najdete v tématu [vysvětlení konektorů služby Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Únor 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Vylepšená navigace pro správu uživatelů a skupin

**Zadejte:** Plánování změn  
**Kategorie služby:** Správa adresářů  
**Schopnost produktu:** Službě

Navigační prostředí pro správu uživatelů a skupin bylo zjednodušené. Nyní můžete přejít z přehledu adresáře přímo na seznam všech uživatelů, s jednodušším přístupem k seznamu odstraněných uživatelů. Můžete také přejít z přehledu adresáře přímo do seznamu všech skupin s jednodušším přístupem k nastavením správy skupin. A také ze stránky přehled adresáře můžete vyhledat uživatele, skupinu, podnikovou aplikaci nebo registraci aplikace. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostupnost přihlášení a sestav auditu v Microsoft Azure provozovaných společností 21Vianet (Azure Čína 21Vianet)

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure Stack  
**Schopnost produktu:** Monitorování & vytváření sestav

Sestavy protokolu aktivit služby Azure AD jsou teď dostupné v Microsoft Azure provozovaných pomocí instancí 21Vianet (Azure Čína 21Vianet). K dispozici jsou tyto protokoly:

- **Protokoly aktivit přihlášení** – obsahuje všechny protokoly přihlášení přidružené k vašemu tenantovi.

- **Samoobslužné protokoly pro audit hesel** – obsahuje všechny protokoly auditu SSPR.

- **Protokoly auditu správy adresářů** – zahrnuje všechny protokoly auditu související se správou adresářů, jako je Správa uživatelů, Správa aplikací a další.

Pomocí těchto protokolů můžete získat přehled o tom, jak vaše prostředí funguje. Poskytnutá data vám umožní:

- Určete, jak vaše aplikace a služby využívají vaše uživatelé.

- Řešení problémů, které uživatelům brání v práci.

Další informace o tom, jak tyto sestavy používat, najdete v tématu [Azure Active Directory vytváření sestav](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Použití role čtenář sestav (role bez správce) k zobrazení sestav aktivit služby Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

V rámci zpětné vazby od zákazníků po povolení přístupu k protokolům aktivit Azure AD uživatelům, kteří jsou v roli Čtenář sestav, povolili uživatelům, kteří jsou v rámci této Azure Portal, přístup k přihlašovacím a auditovým aktivitám v rámci a používání našich rozhraní API pro grafy. 

Další informace o tom, jak tyto sestavy používat, najdete v tématu [Azure Active Directory vytváření sestav](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Deklarace ČísloZaměstnance je dostupná jako atribut uživatele a identifikátor uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Pro uživatele a hosty B2B v přihlašovacích aplikacích založených na SAML můžete nakonfigurovat **ČísloZaměstnance** jako identifikátor uživatele a atribut uživatele pro uživatele v rámci aplikace pro přihlašování pomocí SAML.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Zjednodušená správa aplikací pomocí zástupných znaků v Azure Proxy aplikací služby AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Ověřování uživatelů
 
Abychom usnadnili nasazení aplikace a snížili režijní náklady na správu, teď podporujeme možnost publikování aplikací pomocí zástupných znaků. Chcete-li publikovat aplikaci se zástupnými znaky, můžete postupovat podle standardního toku publikování aplikace, ale použít zástupný znak v interních a externích adresách URL.

Další informace najdete v tématu [aplikace se zástupnými znaky v proxy aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard) .

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nové rutiny, které podporují konfiguraci proxy aplikací

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Platformy

Nejnovější verze modulu AzureAD PowerShell Preview obsahuje nové rutiny, které zákazníkům umožňují konfigurovat aplikace proxy aplikací pomocí PowerShellu.

Nové rutiny jsou: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nové rutiny, které podporují konfiguraci skupin

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Platformy

Nejnovější verze modulu AzureAD PowerShell obsahuje rutiny pro správu skupin ve službě Azure AD. Tyto rutiny byly dřív dostupné v modulu AzureADPreview a teď se přidají do modulu AzureAD.

Rutiny skupiny, které jsou nyní vydány pro obecnou dostupnost: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset – AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>K dispozici je nová verze Azure AD Connect.

**Zadejte:** Nová funkce  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platformy
 
Azure AD Connect je preferovaný nástroj pro synchronizaci dat mezi Azure AD a místními zdroji dat, včetně Windows Server Active Directory a LDAP.

>[!Important]
>Toto sestavení zavádí změny pravidla schématu a synchronizace. Služba Azure AD Connect Synchronization spouští úplný postup importu a úplné synchronizace po upgradu. Informace o tom, jak toto chování změnit, najdete v tématu [jak pozdržet úplnou synchronizaci po upgradu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Tato verze obsahuje následující aktualizace a změny:

**Opravené problémy**

- Oprava okna časování na úkolech na pozadí pro stránku filtrování oddílů při přechodu na další stránku

- Opravili jsme chybu, která způsobila porušení přístupu během vlastní akce ConfigDB.

- Opravili jsme chybu pro obnovení z časového limitu připojení SQL.

- Opravili jsme chybu, kdy se u certifikátů se zástupnými znaky sítě SAN nedaří ověřit kontrolu požadavků.

- Opravili jsme chybu, která způsobí selhání MIIServer. exe během exportu konektoru AAD.

- Opravili jsme chybu, při které se při spuštění do řadiče domény přihlásil špatný pokus o změnu konfigurace pomocí Průvodce AAD Connect.

**Nové funkce a vylepšení**
 
- Telemetrie aplikací – správci můžou tuto třídu dat zapnout nebo vypnout.

- Data o stavu Azure AD – správci musí navštívit portál stavu, aby mohli řídit jeho nastavení stavu. Po změně zásady služby ji budou agenti číst a vymáhat.

- Pro inicializaci stránky byly přidány akce konfigurace zpětného zápisu zařízení a indikátor průběhu.

- Vylepšená Obecná diagnostika se sestavou HTML a úplným shromažďováním dat v sestavě typu ZIP/HTML.

- Zlepšila se spolehlivost automatického upgradu a přidala se další telemetrie, aby bylo zajištěno, že je možné určit stav serveru.

- Omezte oprávnění dostupná pro privilegované účty na účtu konektoru služby AD. Pro nové instalace omezuje Průvodce oprávnění, která mají privilegované účty v účtu MSOL po vytvoření účtu MSOL. Změny mají vliv na rychlé instalace a vlastní instalace pomocí automatického vytváření účtu.

- Změnil instalační program, aby při čisté instalaci AADConnect nevyžadoval oprávnění správce.

- Nový nástroj pro řešení potíží se synchronizací pro určitý objekt. V současné době nástroj kontroluje následující věci:

    - Hodnota UserPrincipalName nesouhlasí mezi synchronizovaným objektem uživatele a uživatelským účtem v Tenantovi Azure AD.
  
    - Pokud je objekt filtrován z synchronizace z důvodu filtrování domény
  
    - Pokud je objekt filtrován z synchronizace kvůli filtrování organizační jednotky (OU)

- Nový nástroj pro synchronizaci hodnoty hash aktuálního hesla uložené v místní službě Active Directory pro konkrétní uživatelský účet. Nástroj nevyžaduje změnu hesla. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikace podporující zásady Intune App Protection přidané pro použití s podmíněným přístupem na základě aplikace Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Přidali jsme další aplikace, které podporují podmíněný přístup na základě aplikace. Nyní můžete získat přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD pomocí těchto schválených klientských aplikací.

Do konce února budou přidány následující aplikace:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Další informace:

- [Požadavek na schválenou aplikaci klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Podmínky použití aktualizace na mobilní prostředí 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování

Po zobrazení podmínek použití teď můžete kliknout na možnost **mít problémy se zobrazením? Klikněte sem**. Kliknutím na tento odkaz otevřete na svém zařízení nativně požadavky na použití. Bez ohledu na velikost písma v dokumentu nebo na velikosti obrazovky můžete dokument podle potřeby zvětšit a přečíst. 

---
 
## <a name="january-2018"></a>Leden 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace 

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V lednu 2018 byly do Galerie aplikací přidány následující nové aplikace s podporou federace:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust software pro správu ochrany osobních údajů](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federovaný adresář a [věrnost NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Přihlášení pomocí zjištěného dalšího rizika

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Přehled, který získáte pro zjištění zjištěného rizika, je svázán s vaším předplatným služby Azure AD. V případě Azure AD Premiumho P2 Edition získáte nejpodrobnější informace o všech základních detekcích.

U edice Azure AD Premium P1 se detekce, která není pokrytá vaší licencí, zobrazuje jako přihlášení k detekci rizik s dalšími zjištěnými riziky.

Další informace najdete v tématu [Azure Active Directory detekci rizik](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Skrýt aplikace Office 365 z přístupových panelů koncového uživatele

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ

Teď můžete lépe spravovat, jak se aplikace Office 365 zobrazují na přístupových panelech uživatelů pomocí nového nastavení uživatele. Tato možnost je užitečná pro omezení počtu aplikací v přístupových panelech uživatele, pokud upřednostňujete, aby se na portálu Office zobrazovaly jenom aplikace Office. Nastavení se nachází v **nastavení uživatele** a je označeno, **Uživatelé mohou na portálu Office 365 zobrazit pouze aplikace Office 365**.

Další informace najdete v tématu [skrytí aplikace z uživatelského prostředí v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblémové přihlašování k aplikacím povoleným pro jednotné přihlašování pomocí hesla přímo z adresy URL aplikace 

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ

Rozšíření prohlížeče moje aplikace je teď k dispozici prostřednictvím pohodlnýho nástroje, který poskytuje možnost jednotného přihlašování mých aplikací jako zástupce v prohlížeči. Po instalaci se uživateli ve svém prohlížeči zobrazí ikona dlaždice, která jim umožní rychlý přístup k aplikacím. Uživatelé teď můžou využít výhody:

- Možnost přímého přihlašování k aplikacím založeným na JEDNOTNÉm přihlašování na přihlašovací stránce aplikace
- Spuštění libovolné aplikace pomocí funkce rychlého vyhledávání
- Zástupci naposledy použitých aplikací z rozšíření
- Rozšíření je k dispozici pro Microsoft Edge, Chrome a Firefox.
 
Další informace najdete v tématu [zabezpečený přihlašovací rozšíření pro moje aplikace](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Prostředí pro správu Azure AD v portál Azure Classic bylo vyřazeno

**Zadejte:** Zastaralé   
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Službě

Od 8. ledna 2018 bylo prostředí pro správu Azure AD na portálu Azure Classic vyřazeno. K tomu došlo ve spojení s vyřazením portálu Azure Classic. V budoucnu byste měli použít [Centrum pro správu Azure AD](https://aad.portal.azure.com) pro veškerou správu Azure AD založenou na portálu.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webový portál PhoneFactor je vyřazený.

**Zadejte:** Zastaralé  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Službě
 
Od 8. ledna 2018 byl webový portál PhoneFactor vyřazený. Tento portál byl použit pro správu MFA serveru, ale tyto funkce byly přesunuty do Azure Portal na adrese portal.azure.com. 

Konfigurace MFA se nachází tady: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Zastaralé sestavy služby Azure AD

**Zadejte:** Zastaralé  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Správa životního cyklu identit  


Díky obecné dostupnosti nové konzoly pro správu Azure Active Directory a novým rozhraním API, která jsou teď k dispozici pro sestavy aktivit a zabezpečení, jsou rozhraní API sestav v rámci koncového bodu "/Reports" vyřazena od 31. prosince 2017.

**Co je k dispozici?**

V rámci přechodu na novou konzolu správce jsme vytvořili 2 nová rozhraní API k načtení protokolů aktivit Azure AD. Nová sada rozhraní API nabízí lepší funkce pro filtrování a řazení, které poskytují lepší aktivity auditu a přihlašování. Data dříve dostupná prostřednictvím sestav zabezpečení teď můžou být přístupná prostřednictvím rozhraní API detekce rizik ochrany identity v Microsoft Graph.

Další informace:

- [Začínáme s rozhraním API pro vytváření sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Prosince 2017

### <a name="terms-of-use-in-the-access-panel"></a>Podmínky použití na přístupovém panelu

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 
Teď můžete přejít na přístupový panel a zobrazit si dřív přijaté podmínkami použití.

Postupujte následovně:

1. Přejít na [portál MyApp](https://myapps.microsoft.com)a přihlásit se.

2. V pravém horním rohu vyberte své jméno a pak v seznamu vyberte možnost **profil** . 

3. V **profilu**vyberte možnost **přezkoumat podmínek použití**. 

4. Teď si můžete projít podmínkami použití, které jste přijali. 

Další informace najdete v tématu [funkce Azure AD terms of use (Preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nové prostředí pro přihlášení k Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Ověřování uživatelů
 
Uživatelská rozhraní systému identit Azure AD a účet Microsoft se přepracovali tak, aby měly konzistentní vzhled a chování. Kromě toho stránka pro přihlášení k Azure AD nejprve shromažďuje uživatelské jméno a za ním následuje přihlašovací údaje na druhé obrazovce.

Další informace najdete v tématu [nové přihlášení k Azure AD je teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Méně výzev k přihlášení: nové prostředí pro přihlášení k Azure AD, které je přihlášené

**Zadejte:** Nová funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Ověřování uživatelů
 
Zaškrtávací políčko **zůstat** přihlášení na přihlašovací stránce služby Azure AD bylo nahrazeno novou výzvou, která se zobrazí po úspěšném ověření. 

Pokud odpovíte **Ano** na tuto výzvu, služba vám nabídne trvalou obnovovací token. Toto chování se shoduje s tím, jak jste zaškrtli políčko **zůstat přihlášeni** ve starém prostředí. Pro federované klienty se tato výzva zobrazí po úspěšném ověření pomocí federované služby.

Další informace najdete v tématu [méně výzev k přihlášení: nové prostředí "zůstat přihlášené" pro Azure AD je ve verzi Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Přidání konfigurace, která vyžaduje rozšíření podmínek použití před přijetím

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 
Možnost správců vyžaduje, aby uživatelé před přijetím podmínek rozšířili podmínkami použití.

Vyberte **zapnout** nebo **vypnout** , pokud chcete, aby uživatelé rozšířili podmínkami použití. Nastavení **zapnuto** vyžaduje, aby uživatelé před přijetím zobrazili podmínek použití.

Další informace najdete v tématu [funkce Azure AD terms of use (Preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktivace s vymezeným oborem pro přiřazení oprávněných rolí

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Pomocí vymezené aktivace můžete aktivovat opravňující přiřazení rolí prostředků Azure s menší autonomií, než je původní výchozí nastavení přiřazení. Příkladem je, že jste přiřazeni jako vlastník předplatného ve vašem tenantovi. S vymezenou aktivací můžete roli vlastníka aktivovat až pro pět prostředků obsažených v rámci předplatného (například skupiny prostředků a virtuální počítače). Rozsah aktivace může snížit možnost provádění nechtěných změn v kritických prostředcích Azure.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nové federované aplikace v galerii aplikací Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

Do prosince 2017 jsme do Galerie aplikací přidali tyto nové aplikace s podporou federace:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital prezentace](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image funguje](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Integrace Azure AD](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [jednotné přihlašování SAML pro Bamboo podle rezoluce GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO pro Bitbucket podle rezoluce GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy integrace služby Azure AD.

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Pracovní postupy schvalování pro role adresáře Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Pracovní postup schvalování pro role adresáře Azure AD je všeobecně dostupný.

Pomocí schvalovacího pracovního postupu můžou správci privilegovaných rolí vyžadovat, aby před použitím privilegované role požádali o aktivaci role. Zodpovědnost schválení může delegovat více uživatelů a skupin. Způsobilí členové role obdrží oznámení po dokončení schválení a jejich role je aktivní.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Předávací ověřování: podpora Skypu pro firmy

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Předávací ověřování teď podporuje přihlášení uživatelů k klientským aplikacím Skypu pro firmy, které podporují moderní ověřování, včetně online a hybridních topologií. 

Další informace najdete v tématu [topologie Skypu pro firmy podporované moderním ověřováním](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizace Azure AD Privileged Identity Management pro službu Azure RBAC (Preview)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Díky aktualizaci Public Preview Azure AD Privileged Identity Management (PIM) pro Azure založené na rolích Access Control (RBAC) můžete teď:

* Používejte jen dostatečně správu.
* Vyžaduje schválení pro aktivaci rolí prostředků.
* Naplánujte budoucí aktivaci role, která vyžaduje schválení pro role Azure AD a Azure RBAC.
 
Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Listopadu 2017
 
### <a name="access-control-service-retirement"></a>Vyřazení služby Access Control

**Zadejte:** Plánování změn  
**Kategorie služby:** Služba Access Control  
**Schopnost produktu:** Služba Access Control 

Azure Active Directory Access Control (označované také jako služba Access Control) budou vyřazeny v 2018. Další informace, které obsahují podrobné pokyny k migraci plánu a vysoké úrovně, budou uvedené v následujících několika týdnech. Komentáře na této stránce můžete ponechat s případnými dotazy týkajícími se služby Access Control a člen týmu je bude odpovídat.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Omezit přístup prohlížeče k Intune Managed Browser 

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

Přístup z prohlížeče k Office 365 a k dalším cloudovým aplikacím připojeným k Azure AD můžete omezit pomocí Intune Managed Browser jako schválené aplikace. 

Pro podmíněný přístup na základě aplikace teď můžete nakonfigurovat tuto podmínku:

**Klientské aplikace:** Prohlížeee

**Jaký je účinek změny?**

V dnešní době je přístup při použití této podmínky zablokován. Pokud je verze Preview k dispozici, veškerý přístup bude vyžadovat použití aplikace Managed Browser. 

Podívejte se na tuto funkci a další informace v nadcházejících blogů a poznámkách k verzi. 

Další informace najdete v tématu [podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

V seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)jsou následující aplikace:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Další informace:

- [Požadavek na schválenou aplikaci klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Podpora podmínek použití v různých jazycích

**Zadejte:** Nová funkce    
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování

Správci teď mohou vytvořit nové podmínkami použití, které obsahují více dokumentů PDF. Tyto dokumenty PDF můžete označit odpovídajícím jazykem. Uživatelům se v závislosti na svých preferencích zobrazí PDF s odpovídajícím jazykem. Pokud se neshoduje, zobrazí se výchozí jazyk.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stav klienta zpětného zápisu hesla v reálném čase

**Zadejte:** Nová funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Nyní můžete zkontrolovat stav místního klienta zpětného zápisu hesla. Tato možnost je k dispozici v části **Místní integrace** stránky pro [resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Pokud dojde k problémům s připojením k místnímu zpětnému zápisu, zobrazí se chybová zpráva, která vám poskytne:

- Informace o tom, proč se nemůžete připojit k místnímu klientovi zpětného zápisu.
- Odkaz na dokumentaci, která vám pomůže při řešení problému. 

Další informace najdete v tématu věnovaném [místní integraci](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Podmíněný přístup na základě aplikace Azure AD 
 
**Zadejte:** Nová funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Zabezpečení identity a ochrana

Teď můžete omezit přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD na [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , které podporují zásady ochrany aplikací Intune pomocí [podmíněného přístupu založeného na aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zásady ochrany aplikací Intune se používají ke konfiguraci a ochraně firemních dat v těchto klientských aplikacích.

Kombinací [aplikací na základě](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) zásad podmíněného přístupu [na základě zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) získáte flexibilitu při ochraně dat pro osobní zařízení a zařízení společnosti.

Pro použití podmíněného přístupu na základě aplikace jsou teď dostupné následující podmínky a ovládací prvky:

**Podporovaná podmínka platformy**

- iOS
- Android

**Podmínky klientských aplikací**

- Mobilní aplikace a klienti klasické pracovní plochy

**Řízení přístupu**

- Vyžadovat klientskou aplikaci schválenou

Další informace najdete v tématu [podmíněný přístup založený na aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Správa zařízení Azure AD v Azure Portal

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Zabezpečení identity a ochrana

Teď můžete najít všechna vaše zařízení připojená k Azure AD a aktivity související se zařízením na jednom místě. Existují nové možnosti správy, které vám umožní spravovat všechny identity a nastavení vašich zařízení v Azure Portal. V této verzi můžete:

- Zobrazí všechna zařízení, která jsou k dispozici pro podmíněný přístup ve službě Azure AD.
- Zobrazení vlastností, které zahrnují vaše zařízení připojená k hybridní službě Azure AD.
- Najděte si klíče BitLockeru pro zařízení připojená k Azure AD, Spravujte svoje zařízení v Intune a další.
- Správa nastavení týkajících se zařízení v Azure AD

Další informace najdete v tématu [Správa zařízení pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Podpora macOS jako platformy zařízení pro podmíněný přístup Azure AD 

**Zadejte:** Nová funkce    
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana 

V rámci zásad podmíněného přístupu Azure AD teď můžete zahrnout (nebo vyloučit) macOS jako podmínku pro platformu zařízení. Díky přidání macOS k podporovaným platformám zařízení můžete:

- **Registrace a Správa zařízení macOS pomocí Intune** Podobně jako u jiných platforem, jako je iOS a Android, je k dispozici aplikace Portál společnosti, která macOS umožňuje sjednocené registrace. Pomocí nové aplikace Portál společnosti pro macOS můžete zaregistrovat zařízení v Intune a zaregistrovat ho ve službě Azure AD.
- **Zajistěte, aby zařízení macOS vyhovovala zásadám dodržování předpisů vaší organizace definovaným v Intune.** V Intune na Azure Portal teď můžete nastavit zásady dodržování předpisů pro zařízení macOS. 
- **Omezte přístup k aplikacím ve službě Azure AD tak, aby splňovala jenom vyhovující zařízení macOS.** Při vytváření zásad podmíněného přístupu se macOS jako samostatná možnost platformy zařízení. Nyní můžete vytvářet zásady podmíněného přístupu specifické pro macOS pro cílovou aplikaci určenou v Azure.

Další informace:

- [Vytvoření zásady dodržování předpisů pro zařízení s macOS pomocí Intune](https://aka.ms/macoscompliancepolicy)
- [Podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozšíření serveru NPS (Network Policy Server) pro Azure Multi-Factor Authentication 

**Zadejte:** Nová funkce    
**Kategorie služby:**  Multi-Factor Authentication  
**Schopnost produktu:** Ověřování uživatelů

Rozšíření serveru NPS (Network Policy Server) pro Azure Multi-Factor Authentication do vaší ověřovací infrastruktury přidávají cloudové možnosti Multi-Factor Authentication pomocí stávajících serverů. S rozšířením serveru NPS (Network Policy Server) můžete do stávajícího toku ověřování přidat ověřování pomocí telefonního hovoru, textové zprávy nebo aplikace pro telefon. Nemusíte instalovat, konfigurovat a udržovat nové servery. 

Toto rozšíření bylo vytvořeno pro organizace, které chtějí chránit připojení k virtuální privátní síti bez nutnosti nasazení Multi-Factor Authentication Server Azure. Rozšíření serveru NPS (Network Policy Server) funguje jako adaptér mezi POLOMĚRem a cloudovou Multi-Factor Authentication Azure, která poskytuje druhý faktor ověřování pro federované nebo synchronizované uživatele.

Další informace najdete v tématu [integrace stávající infrastruktury serveru NPS (Network Policy Server) s Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Obnovit nebo trvale odebrat odstraněné uživatele

**Zadejte:** Nová funkce    
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Službě 

V centru pro správu Azure AD teď můžete:

- Obnovení odstraněného uživatele. 
- Trvale odstraní uživatele.

**Vyzkoušejte:**

1. V centru pro správu Azure AD vyberte [Všichni uživatelé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) v části **Spravovat** . 

2. V seznamu **Zobrazit** vyberte **Nedávno odstraněné uživatele**. 

3. Vyberte jednoho nebo více nedávno odstraněných uživatelů a pak je buď obnovte, nebo je trvale odstraňte.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD
 
**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

Do seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)byly přidány následující aplikace:

- Microsoft Planner
- Azure Information Protection 

Další informace:

- [Požadavek na schválenou aplikaci klienta](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Použití "nebo" mezi ovládacími prvky v zásadách podmíněného přístupu 

**Zadejte:** Změněná funkce    
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana
 
Nyní můžete použít "nebo" (pro řízení podmíněného přístupu vyžadovat jeden z vybraných ovládacích prvků). Tuto funkci můžete použít k vytvoření zásad pomocí "nebo" mezi ovládacími prvky přístupu. Pomocí této funkce můžete například vytvořit zásadu, která vyžaduje, aby se uživatel přihlásil pomocí Multi-Factor Authentication "nebo" na zařízení vyhovujícím předpisům.

Další informace najdete v tématu věnovaném [ovládacím prvkům v Azure AD podmíněný přístup](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregace zjišťování rizik v reálném čase

**Zadejte:** Změněná funkce    
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity a ochrana

V Azure AD Identity Protection se teď všechny detekce rizik v reálném čase, které pocházejí ze stejné IP adresy v daném dni, agreguje pro každý typ detekce rizik. Tato změna omezuje množství zjištěných detekcí rizik bez změny zabezpečení uživatele.

Základní zjišťování v reálném čase funguje při každém přihlášení uživatele. Pokud máte zásady zabezpečení rizik přihlašování nastavené tak, aby Multi-Factor Authentication nebo zablokovaly přístup, pořád se aktivuje při každé rizikové přihlašování.
 
---
 
## <a name="october-2017"></a>Říjen 2017

### <a name="deprecate-azure-ad-reports"></a>Zastaralé sestavy služby Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Správa životního cyklu identit  

Azure Portal vám poskytne:

- Nová konzola pro správu služby Azure AD.
- Nová rozhraní API pro aktivity a sestavy zabezpečení.
 
Z důvodu těchto nových schopností byly rozhraní API sestav pod koncovým bodem/Reports vyřazena 10. prosince 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatické zjišťování polí přihlašování

**Zadejte:** Určí   
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Jednotné přihlašování  

Azure AD podporuje automatické rozpoznávání polí pro aplikace, které vykreslí pole uživatelské jméno a heslo HTML. Tyto kroky jsou popsány v tématu [Automatické zachytávání přihlašovacích polí pro aplikaci](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tuto možnost můžete najít přidáním aplikace *mimo galerii* na stránce **podnikové aplikace** v [Azure Portal](https://aad.portal.azure.com). Kromě toho můžete v této nové aplikaci nakonfigurovat režim **jednotného přihlašování** na **jednotné přihlašování založené na heslech**, zadat adresu URL webu a pak stránku uložit.
 
Kvůli problému se službou byla tato funkce dočasně zakázána. Problém byl vyřešen a znovu je k dispozici automatické rozpoznávání polí pro přihlášení.

---

### <a name="new-multi-factor-authentication-features"></a>Nové funkce Multi-Factor Authentication

**Zadejte:** Nová funkce  
**Kategorie služby:** Multi-Factor Authentication  
**Schopnost produktu:** Zabezpečení identity a ochrana  

Multi-Factor Authentication (MFA) je podstatnou součástí ochrany vaší organizace. Aby se přihlašovací údaje lépe přizpůsoboval a bylo tak plynulější, byly přidány následující funkce: 

- Výsledky Multi-Factor Challenge jsou přímo integrované do sestavy přihlášení služby Azure AD, která zahrnuje programový přístup k výsledkům vícefaktorového ověřování.
- Konfigurace MFA je podrobněji integrovaná do prostředí konfigurace Azure AD v Azure Portal.

V této veřejné verzi Preview jsou Správa MFA a vytváření sestav integrovaná součástí základních možností konfigurace služby Azure AD. Teď můžete spravovat funkce portálu pro správu MFA v rámci prostředí Azure AD.

Další informace najdete v tématu [referenční informace pro generování sestav MFA v Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Podmínky použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování  

Podmínky použití Azure AD můžete použít k předložení informací, jako jsou relevantní právní omezení pro dodržování právních předpisů nebo dodržování předpisů pro uživatele.

Pomocí podmínek použití Azure AD můžete v těchto scénářích:

- Obecné podmínek použití pro všechny uživatele ve vaší organizaci
- Konkrétní požadavky na použití na základě atributů uživatele (například lékaři a zdravotní sestry nebo domácí vs. mezinárodní zaměstnanci prováděné dynamickými skupinami)
- Konkrétní pravidla použití pro přístup k obchodním aplikacím s vysokým dopadem, jako je Salesforce

Další informace najdete v tématu věnovaném [podmínkám použití Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Vylepšení Privileged Identity Management

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management  

Pomocí Azure AD Privileged Identity Management můžete v rámci organizace spravovat, řídit a monitorovat přístup k prostředkům Azure (Preview):

- Předplatná
- Skupiny prostředků
- Virtuální počítače 

Všechny prostředky v rámci Azure Portal, které používají funkci Azure RBAC, můžou využívat všechny možnosti správy zabezpečení a životního cyklu, které Azure AD Privileged Identity Management nabízí.

Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Revize přístupu

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Dodržování  

Organizace můžou pomocí kontrol přístupu (Preview) efektivně spravovat členství ve skupinách a přístup k podnikovým aplikacím: 

- Můžete znovu certifikovat přístup uživatelů typu host pomocí kontrol jejich přístupu k aplikacím a jejich členství ve skupinách. Recenzenti se můžou efektivně rozhodnout, jestli má hostům pokračovat v přístupu na základě přehledů poskytovaných přezkoumáním přístupu.
- Pomocí kontrol přístupu je možné provést opětovnou certifikaci přístupu k aplikacím a členství ve skupinách i pro zaměstnance.

Kontroly přístupu lze shromažďovat v programech sledujících dodržování předpisů a přístup k citlivým aplikacím v rámci organizace.

Další informace najdete v tématu kontroly [přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Skrýt aplikace třetích stran z mých aplikací a spouštěče aplikací pro Office 365

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Jednotné přihlašování  

Teď můžete lépe spravovat aplikace, které se zobrazují na portálech uživatelů, prostřednictvím nové vlastnosti **Skrýt aplikaci** . V případech, kdy se dlaždice aplikací zobrazují pro back-endové služby nebo pro duplikované dlaždice a v případě spouštěčů aplikací uživatelů bez zbytečného spouštění, můžete aplikace skrýt. Přepínač je v části **vlastnosti** aplikace třetí strany a je označen jako **viditelný pro uživatele?** Aplikaci můžete také programově skrýt prostřednictvím prostředí PowerShell. 

Další informace najdete v tématu [skrytí aplikace třetí strany od uživatele ze zkušeností uživatelů v Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co je k dispozici?**

 V rámci přechodu na novou konzolu správce jsou k dispozici dvě nová rozhraní API pro načítání protokolů aktivit služby Azure AD. Nová sada rozhraní API nabízí lepší funkce pro filtrování a řazení, které poskytují lepší aktivity auditu a přihlašování. Data dříve dostupná prostřednictvím sestav zabezpečení jsou teď přístupná prostřednictvím rozhraní API detekce rizik ochrany identity v Microsoft Graph.


## <a name="september-2017"></a>Září 2017

### <a name="hotfix-for-identity-manager"></a>Oprava hotfix pro nástroj Identity Manager

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správce identit  
**Schopnost produktu:** Správa životního cyklu identit  

Balíček kumulativní aktualizace hotfix (Build 4.4.1642.0) je k dispozici od 25. září 2017 pro nástroj Identity Manager 2016 Service Pack 1. Tento balíček Shrnutí:

- Řeší problémy a přidává vylepšení.
- Je kumulativní aktualizace, která nahrazuje všechny aktualizace Identity Manager 2016 Service Pack 1 až po Build 4.4.1459.0 pro Identity Manager 2016. 
- Vyžaduje, abyste měli nástroj Identity Manager 2016 Build 4.4.1302.0. 

Další informace najdete v tématu [kumulativní balíček oprav hotfix (Build 4.4.1642.0) pro nástroj Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
