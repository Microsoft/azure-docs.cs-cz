---
title: Azure Active Directory doklad koncept playbook stavební bloky | Microsoft Docs
description: Prozkoumejte a rychle implementovat scénáře identita a správa přístupu
services: active-directory
keywords: Azure active directory, scénářem, testování konceptu, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: 97a77de2fe1111f0a65b5325a5db96ad30f40f15
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory doklad o koncept playbook: stavební bloky

## <a name="catalog-of-roles"></a>Katalog rolí

| Role | Popis | Testování konceptu (PoC) zodpovědnosti |
| --- | --- | --- |
| **Architektura identity / vývojový tým** | Tento tým je obvykle ten, který navrhuje řešení, implementuje prototypy, jednotky schválení a nakonec předá k operacím | Poskytují prostředí a jsou ty, které vyhodnocení různé scénáře z hlediska správy |
| **Místní Identity provozní tým** | Spravuje jinou identitu zdroje místní: doménové struktury služby Active Directory, adresářů protokolu LDAP, HR systémy a poskytovatelů federace identit. | Poskytnout přístup k místní prostředky potřebné pro scénáře testování koncepce.<br/>Jejich by měl být zahrnut co nejméně|
| **Technické vlastníci aplikace** | Technické vlastníky různých cloudových aplikací a služeb, které se integrují s Azure AD | Obsahují podrobnosti o SaaS aplikace (potenciálně instance pro testování) |
| **Globální správce Azure AD** | Spravuje konfiguraci Azure AD | Zadejte přihlašovací údaje ke konfiguraci služby synchronizace. Obvykle stejné jako architektura Identity team během testování koncepce ale oddělte během fáze operace|
| **Databáze team** | Vlastníci databáze infrastruktury | Poskytovat přístup k prostředí SQL (služby AD FS nebo Azure AD Connect) pro konkrétní scénář přípravy.<br/>Jejich by měl být zahrnut co nejméně |
| **Tým síťových** | Vlastníci síťové infrastruktury | Zadejte požadovaný přístup na úrovni sítě pro synchronizační servery správně přistupovat ke zdrojům dat a cloudových služeb (pravidla brány firewall, porty otevřít, pravidla IPSec atd.) |
| **Bezpečnostní tým** | Definuje bezpečnostní strategie, analyzuje zabezpečení sestavy z různých zdrojů a postupuje podle zjištění. | Zadejte cíl zabezpečení vyhodnocení scénáře |

## <a name="common-prerequisites-for-all-building-blocks"></a>Běžné požadavky pro všechny stavební bloky

Toto jsou některé součásti potřebné pro všechny POC s Azure AD Premium.

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Klientovi Azure AD, které jsou definované s platným předplatným Azure | [Postup získání klienta Azure Active Directory](active-directory-howto-tenant.md)<br/>**Poznámka:** Pokud již máte v prostředí s licencí Azure AD Premium, můžete získat nulové cap předplatné přechodem na https://aka.ms/accessaad <br/>Další informace na: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ a https://technet.microsoft.com/library/dn832618.aspx |
| Definované a ověření domény | [Přidání vlastního názvu domény do Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Poznámka:** některé úlohy, jako například Power BI může mít zřízení klient služby azure AD v pozadí. Chcete-li zkontrolovat, pokud je přidružena k klienta pro danou doménu, přejděte na https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Je-li získat k úspěšné odpovědi, pak domény je již přiřazen k klienta a převzít kontrolu nad mohou být potřebné. Pokud ano, požádejte Microsoft o další pokyny. Další informace o převzetí možnosti v: [co je Samoobslužná registrace pro Azure?](active-directory-self-service-signup.md) |
| Azure AD Premium nebo EMS zkušební povoleno | [Azure Active Directory Premium zdarma pro jeden měsíc](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Přiřadili jste Azure AD Premium nebo licencí pro EMS PoC uživatelům | [Licence vy a vaši uživatelé v Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Přihlašovací údaje Azure AD globálního správce. | [Přiřazení rolí správce v Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Volitelné, ale důrazně ho doporučujeme: paralelní testovacího prostředí jako zálohu | [Požadavky pro Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Instalace nové synchronizace - synchronizace hodnot Hash hesel (PBS) - Directory

Přibližná doba na dokončeno: jednu hodinu, než menší než 1 000 uživatelů PoC

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Připojení serveru ke spuštění služby Azure AD | [Požadavky pro Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Cíloví uživatelé POC ve stejné doméně a součástí skupiny zabezpečení a organizační jednotky | [Vlastní instalace služby Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Azure AD Connect funkce, která je potřebná pro fázi testování Koncepce jsou identifikovány. | [Připojení služby Active Directory s Azure Active Directory – konfigurace synchronizace funkce](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Požadovaná pověření pro místní a cloudové prostředí  | [Azure AD Connect: Účty a oprávnění](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Stáhnout nejnovější verzi služby Azure AD Connect | [Stáhněte si Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Nainstalujte Azure AD Connect s ta nejjednodušší cesta: Express <br/>1. Filtrovat, aby cílové organizační jednotky a zkraťte dobu synchronizačním cyklu<br/>2. Vyberte cílovou sadu uživatelů v místní skupině.<br/>3. Nasazení funkce vyžaduje další POC motivy | [Azure AD Connect: Vlastní instalace: domény a organizační jednotky filtrování](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Vlastní instalace: filtrování na základě skupiny](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrování místních identit s Azure Active Directory: Konfigurace funkcí synchronizace](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Otevřít Azure AD Connect uživatelského rozhraní a zobrazit spuštěný profily dokončené (Import, synchronizace a export) | [Synchronizace Azure AD Connect: Plánovač](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Otevřete [portálu pro správu Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), přejděte do okna "Všichni uživatelé", přidat sloupec "Zdroj autority" a najdete v tématu, která se zobrazí uživatelům, správně označené jako pocházející od "Windows Server AD" | [Portál pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Požadavky

1. Podívejte se na důležité informace o zabezpečení synchronizace hodnot hash hesel [zde](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Pokud synchronizací hodnoty hash hesla pro uživatele pilotního nasazení produkční není platností možnost, pak zvažte následující možnosti:
   * Vytvoření testovacích uživatelů v doméně v produkčním prostředí. Ujistěte se, že nemáte synchronizovat jiný účet
   * Přesunout do prostředí UAT
2.  Pokud chcete, federační pokračovat, je vhodné pochopit, že náklady na přidružené zprostředkovatele Identity místní nad rámec ve fázi POC federované řešení a míře, které proti výhody, které hledáte:
    * Je důležité cesty, budete muset navrhnout pro zajištění vysoké dostupnosti
    * Je místní služby, které potřebujete k plánování kapacity
    * Je místní služby, které potřebujete k monitorování nebo udržovat nebo oprava

Další informace: [identity Principy Office 365 a Azure Active Directory - federované Identity](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Branding

Přibližná doba na dokončeno: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Prostředky (bitové kopie, loga, atd.); Pro nejlepší vizualizace Ujistěte se, že mají prostředky doporučené velikosti. | [Přidání firemního brandingu na přihlašovací stránku ve službě Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Volitelné: Pokud prostředí obsahuje server služby AD FS, přístup k serveru za účelem přizpůsobení webových motivů | [AD FS uživatele přihlásit přizpůsobení](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Klientský počítač k provedení přihlášení činnost koncového uživatele |  |
| Volitelné: Mobilních zařízení a ověřit činnost |  |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte na portál pro správu Azure AD | [Portál pro správu Azure AD - firemního brandingu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Nahrajte prostředků pro přihlašovací stránku (nejdůležitější logo, malé logo, popisky, atd.). Případně pokud máte služby AD FS, zarovnejte stejné prostředky s přihlašovací stránky služby AD FS | [Přidání firemního brandingu na vaše přihlášení a přístupový Panel stránky: přizpůsobitelné prvky](customize-branding.md) |
| Počkejte několik minut, aby tato změna se plně projeví |  |
| Přihlaste se pomocí přihlašovacích údajů uživatele POC do https://myapps.microsoft.com |  |
| Potvrďte vzhledu a chování v prohlížeči | [Přidání firemního brandingu na vaše přihlášení a přístupový Panel stránky](customize-branding.md) |
| Volitelně můžete potvrďte vzhledu a chování v jiných zařízení |  |

### <a name="considerations"></a>Požadavky

Pokud původní vzhledu a chování zůstane po přizpůsobení vyprázdnit mezipaměť klienta prohlížeče a zkuste operaci zopakovat.

## <a name="group-based-licensing"></a>Na základě skupiny licencí

Přibližná doba na dokončeno: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Všichni uživatelé POC jsou součástí skupiny zabezpečení (cloudové nebo místní) | [Vytvořte skupinu a přidejte členy v Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte do okna licencí na portálu správy Azure AD | [Portál pro správu Azure AD: Správa licencí](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Přiřadíte licence do skupiny zabezpečení s POC uživateli. | [Přiřazení licencí pro skupinu uživatelů ve službě Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Požadavky

V případě jakékoli problémy, přejděte na [scénáře, omezení a známé problémy s použití skupin ke správě licencování v Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Konfigurace SaaS federovaného jednotného přihlašování

Přibližná doba na dokončeno: 60 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Testovací prostředí aplikace SaaS, která je k dispozici. V této příručce použijeme ServiceNow jako příklad.<br/>Důrazně doporučujeme používat testovací instance minimalizovat tření na navigace existující data kvality a mapování. | Přejděte na https://developer.servicenow.com/app.do#! / domácí zahájíte proces získávání testovací instance |
| Přístup správce ke konzole pro správu ServiceNow | [Kurz: Azure Active Directory integrace s ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Cíl nastaví uživatelů přiřazení aplikace k. Skupinu zabezpečení obsahující uživatele PoC se doporučuje. <br/>Pokud vytváření skupiny není vhodná, přiřadíte přímo k aplikaci ve fázi poc pak uživatelům | [Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Sdílet kurzu pro všechny účastníky z Documentation společnosti Microsoft  | [Kurz: Azure Active Directory integrace s ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Nastavit pracovní schůzku a postupujte podle kroků kurzu s každou objektu actor. | [Kurz: Azure Active Directory integrace s ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Přiřadíte skupinu určené v požadavky aplikace. Pokud ve fázi POC podmíněného přístupu v oboru, můžete později, pokroku a přidat vícefaktorového ověřování a podobné. <br/>Všimněte si, že to bude nové proces zajišťování (Pokud je nakonfigurováno) |  [Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Vytvořte skupinu a přidejte členy v Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Pomocí Azure AD management Portal můžete přidat ServiceNow aplikace z Galerie| [Správa služby Azure AD portál: podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Co je nového v nástroji Správa podniková aplikace v Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| V okně "Jednotného přihlašování" aplikace ServiceNow povolte "na základě SAML přihlašování" |  |
| Vyplňte pole "Přihlašovací adresa URL" a "Identifikátor" s ServiceNow adresu URL<br/>Zaškrtněte políčko "Aktivujte nový certifikát"<br/>a uložte nastavení |  |
| Otevřete okno "Konfigurace ServiceNow" v dolní části panelu vlastní pokyny pro konfiguraci ServiceNow |  |
| Postupujte podle pokynů ke konfiguraci ServiceNow |  |
| V okně "Zřizování" aplikace ServiceNow povolte zřizování "Automatické" | [Správa zřizování pro podnikové aplikace na portálu Azure nový uživatelský účet](active-directory-enterprise-apps-manage-provisioning.md) |
| Zatímco bude zřizování dokončeno, počkejte několik minut.  Do té doby můžete zkontrolovat na zřizování sestavy |  |
| Přihlaste se k https://myapps.microsoft.com/ jako testovacího uživatele, který má přístup | [Co je na přístupovém panelu?](active-directory-saas-access-panel-introduction.md) |
| Klikněte na dlaždici pro aplikaci, kterou jste právě vytvořili. Potvrďte volbu přístup |  |
| Volitelně můžete zkontrolovat sestavy využití aplikace. Všimněte si, že se některé latenci, takže budete muset počkat, než určitou dobu sledovat provoz v sestavách. | [Přihlašovací aktivity sestav na portálu Azure Active Directory: využití spravovaných aplikací](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Zásady uchovávání sestav služby Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Požadavky

1. Výše [kurzu](active-directory-saas-servicenow-tutorial.md) odkazuje na staré Azure prostředí pro správu AD. Ale PoC je založena na [rychlý Start](active-directory-enterprise-apps-whats-new-azure-portal.md#quickstart-get-going-with-your-new-application-right-away) prostředí.
2. Pokud cílová aplikace se nenachází v galerii, můžete použít, "Přineste vlastní aplikace". Další informace: [co je nového v nástroji Správa podniková aplikace v Azure Active Directory: Přidat vlastní aplikace z jednoho místa](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Konfigurace jednotného přihlašování k SaaS heslo

Přibližná doba na dokončeno: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Testovací prostředí pro aplikace SaaS. Příklad přihlašování heslo je HipChat a Twitter. Pro všechny ostatní aplikace budete potřebovat přesnou adresu URL stránky s přihlášení formuláře html. | [Twitter na webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat na webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testovací účty pro aplikace. | [Zaregistrujte si služby Twitter.](https://twitter.com/signup?lang=en)<br/>[Zaregistrujte se zdarma: HipChat](https://www.hipchat.com/sign_up) |
| Cíl nastaví uživatelů přiřazení aplikace k. Skupiny zabezpečení obsažené uživatele se nedoporučuje. | [Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Místní správce přístupu k počítači a nasazení rozšíření Panel přístupu pro Internet Explorer, Chrome nebo Firefox | [Rozšíření přístup k panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístup k panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístup k panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Nainstalujte rozšíření prohlížeče | [Rozšíření přístup k panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístup k panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístup k panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Nakonfigurovat aplikaci z Galerie | [Co je nového v nástroji Správa podniková aplikace v Azure Active Directory: galerii nových a vylepšených aplikací](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Konfigurace hesla jednotného přihlašování | [Správa jednotného přihlašování pro podnikové aplikace na portálu Azure nové: založené na heslech přihlašování](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Přiřadit skupinu určené v požadované součásti aplikace | [Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Přihlaste se k https://myapps.microsoft.com/ jako testovacího uživatele, který má přístup |  |
| Klikněte na dlaždici pro aplikaci, kterou jste právě vytvořili. | [Co je na přístupovém panelu?: založené na heslech jednotné přihlašování bez zřizování identity](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zadejte přihlašovací údaje aplikací | [Co je na přístupovém panelu?: založené na heslech jednotné přihlašování bez zřizování identity](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zavřete prohlížeč a opakujte přihlášení. Uživatel nyní měli vidět bezproblémový přístup k aplikaci. |  |
| Volitelně můžete zkontrolovat sestavy využití aplikace. Všimněte si, že se některé latenci, takže budete muset počkat, než určitou dobu sledovat provoz v sestavách. | [Přihlašovací aktivity sestav na portálu Azure Active Directory: využití spravovaných aplikací](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Zásady uchovávání sestav služby Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Požadavky

Pokud cílová aplikace se nenachází v galerii, můžete použít, "Přineste vlastní aplikace". Další informace: [co je nového v nástroji Správa podniková aplikace v Azure Active Directory: Přidat vlastní aplikace z jednoho místa](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Mějte na paměti následující požadavky:
   * Aplikace by měl mít známých přihlašovací adresa URL
   * Na přihlašovací stránce by měl obsahovat formuláře HTML s jeden další textová pole, které rozšíření prohlížeče můžete automatické vyplnění. Minimálně měl by obsahovat uživatelské jméno a heslo.

## <a name="saas-shared-accounts-configuration"></a>SaaS sdílenou konfiguraci účtů

Přibližná doba na dokončeno: 30 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Seznam cíl aplikací a přesně přihlašovací adresy URL předem. Jako příklad můžete Twitter. | [Twitter na webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Zaregistrujte si služby Twitter.](https://twitter.com/signup?lang=en) |
| Sdílené přihlašovací údaje pro tuto aplikaci SaaS. | [Sdílení účtů pomocí služby Azure AD](active-directory-sharing-accounts.md)<br/>[Heslo převrácení pro Facebook, Twitter a LinkedIn nyní ve verzi preview služby azure AD automatizované! -Enterprise Mobility and Security Blog] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Přihlašovací údaje pro alespoň dva členy týmu, kteří budou mít přístup stejný účet. Musí být součástí skupiny zabezpečení. | [Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Místní správce přístupu k počítači a nasazení rozšíření Panel přístupu pro Internet Explorer, Chrome nebo Firefox | [Rozšíření přístup k panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístup k panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístup k panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Nainstalujte rozšíření prohlížeče | [Rozšíření přístup k panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístup k panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístup k panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Nakonfigurovat aplikaci z Galerie | [Co je nového v nástroji Správa podniková aplikace v Azure Active Directory: galerii nových a vylepšených aplikací](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Konfigurace hesla jednotného přihlašování | [Správa jednotného přihlašování pro podnikové aplikace na portálu Azure nové: založené na heslech přihlašování](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Aplikace přiřadit skupinu určené v požadavky při přiřazování jejich přihlašovací údaje | [Přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md) |
| Přihlaste se jako různým uživatelům přístup k aplikaci jako **stejné sdílené účtu.**  |  |
| Volitelně můžete zkontrolovat sestavy využití aplikace. Všimněte si, že se některé latenci, takže budete muset počkat, než určitou dobu sledovat provoz v sestavách. | [Přihlašovací aktivity sestav na portálu Azure Active Directory: využití spravovaných aplikací](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Zásady uchovávání sestav služby Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Požadavky

Pokud cílová aplikace se nenachází v galerii, můžete použít, "Přineste vlastní aplikace". Další informace: [co je nového v nástroji Správa podniková aplikace v Azure Active Directory: Přidat vlastní aplikace z jednoho místa](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Mějte na paměti následující požadavky:
   * Aplikace by měl mít známých přihlašovací adresa URL
   * Na přihlašovací stránce by měl obsahovat formuláře HTML s jeden další textová pole, které rozšíření prohlížeče můžete automatické vyplnění. Minimálně měl by obsahovat uživatelské jméno a heslo.

## <a name="app-proxy-configuration"></a>Konfigurace Proxy aplikace

Přibližná doba na dokončeno: 20 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Microsoft Azure AD basic nebo předplatné premium a adresář Azure AD, u kterého jste globální správce | [Edice služby Azure Active Directory](active-directory-whatis.md) |
| Webové aplikace hostované místní, které chcete nakonfigurovat pro vzdálený přístup |  |
| Serveru se systémem Windows Server 2012 R2 nebo Windows 8.1 nebo vyšší, na který nainstalujete konektor Proxy aplikace | [Pochopení konektory proxy aplikace služby Azure AD](manage-apps/application-proxy-connectors.md) |
| Pokud v cestě je brána firewall, ujistěte se, že je otevřete tak, aby konektor můžete provést požadavky HTTPS (TCP) proxy aplikace | [Povolení Proxy aplikace na portálu Azure: požadavky na Proxy aplikace](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Pokud vaše organizace používá proxy servery pro připojení k Internetu, proveďte podívejte se na blogu k vystavení práci se stávající místní servery proxy podrobnosti o tom, jak je nakonfigurovat | [Práce s existující místní proxy servery](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Instalace konektoru na serveru | [Povolení Proxy aplikace na portálu Azure: instalace a registrace konektoru](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| Publikovat místní aplikace ve službě Azure AD jako aplikace Proxy aplikace | [Publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md) |
| Přiřadit testovací uživatele | [Publikování aplikací pomocí proxy aplikace služby Azure AD: Přidání testovacího uživatele](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Volitelně můžete nakonfigurujte jeden přihlašování pro vaše uživatele | [Zadejte jednotné přihlašování s proxy aplikace služby Azure AD](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| Testování aplikace po přihlášení na portál MyApps jako přiřazený uživatel | https://myapps.microsoft.com |

### <a name="considerations"></a>Požadavky

1. Při doporučujeme uvedení konektor v podnikové síti, existují případy, když se zobrazí lepší výkon jeho umístění v cloudu. Další informace: [aspekty topologie sítě, při použití aplikace Proxy Azure Active Directory](manage-apps/application-proxy-network-topology.md)
2. Další podrobné informace o zabezpečení a jak to zejména zabezpečený vzdálený přístup poskytuje řešení udržováním pouze odchozí připojení najdete v tématu: [důležité informace o zabezpečení pro vzdálený přístup k aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Obecná konfigurace konektoru LDAP

Přibližná doba na dokončeno: 60 minut

> [!IMPORTANT]
> Toto je pokročilou konfiguraci nutnosti některé znalost FIM nebo MIM. Pokud se používá v produkčním prostředí, doporučujeme, aby dotazy týkající se tato konfigurace projít [Premier Support](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Instalace a konfigurace Azure AD Connect | Stavební blok: [synchronizace adresáře – synchronizace hodnot Hash hesel](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Požadavky na schůzku ADLDS instance | [Technické informace o obecné konektor LDAP: Přehled obecné konektor LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Seznam úloh, které uživatelé používají a atributy přidružené tyto úlohy | [Synchronizace Azure AD Connect: atributy se synchronizují do Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přidat generický konektor LDAP | [Technické informace o obecné konektor LDAP: vytvořit nový konektor](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| Vytvoření profilů spuštění pro vytvořený konektor (úplný import, Rozdílový import, úplná synchronizace, rozdílová synchronizace, export) | [Vytvořit profil spuštění agenta správy](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Použití konektorů s Azure AD Connect Sync Správce služeb](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Spustit úplný import profilu a ověřte, jestli jsou objekty v prostoru konektoru | [Vyhledejte objekt prostoru konektoru](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Použití konektorů s Azure AD Connect Sync Správce služeb: hledání prostoru konektoru](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Vytvoření pravidla synchronizace, tak, aby objektů v Metaverse nezbytné atributy pro úlohy | [Synchronizace Azure AD Connect: osvědčené postupy pro změnu výchozí konfigurace: změny pravidla synchronizace](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Synchronizace Azure AD Connect: Principy deklarativní zřizování](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Synchronizace Azure AD Connect: Principy výrazů deklarativní zřizování](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Spustit úplnou synchronizaci cyklu | [Synchronizace Azure AD Connect: Scheduler: spuštění plánovače](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| V případě problémů se řešení potíží | [Řešení potíží s objektem, který se nesynchronizuje do Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Ověřte, zda LDAP uživatel může přihlásit a přístup k aplikaci | https://myapps.microsoft.com |

### <a name="considerations"></a>Požadavky

> [!IMPORTANT]
> Toto je pokročilou konfiguraci nutnosti některé znalost FIM nebo MIM. Pokud se používá v produkčním prostředí, doporučujeme, aby dotazy týkající se tato konfigurace projít [Premier Support](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Skupin, delegovat vlastnictví

Přibližná doba na dokončeno: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Již nakonfigurována aplikace SaaS (Federovanému nebo heslo jednotné přihlašování) | Stavební blok: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |
| Skupina cloudu, kterému je přiřazen přístup k aplikaci v #1 je určena | Stavební blok: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) <br/>[Vytvořte skupinu a přidejte členy v Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Přihlašovací údaje pro vlastník skupiny jsou k dispozici | [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md) |
| Byla zjištěna přihlašovací údaje pro přístup aplikace k pracovníkovi s informacemi | [Co je na přístupovém panelu?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Identifikovat skupinu, kterému byla udělena přístup k aplikaci a nakonfigurovat vlastník daného skupiny| [Spravovat nastavení pro skupinu v Azure Active Directory ](active-directory-groups-settings-azure-portal.md) |
| Přihlaste se jako vlastník skupiny, najdete v části členství ve skupině skupiny kartě přístupového panelu | [Stránky Azure Active Directory, Správa skupin](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Přidat pracovník s informacemi, které chcete testovat |  |
| Přihlaste se jako pracovník, potvrďte, že dlaždice je k dispozici | [Co je na přístupovém panelu?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Požadavky

Pokud má aplikace zřizování povolené, může budete muset Počkejte několik minut pro zřizování dokončit před přístupem k aplikaci jako pracovník s informacemi.

## <a name="saas-and-identity-lifecycle"></a>SaaS a životního cyklu Identity

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Již nakonfigurována aplikace SaaS (Federovanému nebo heslo jednotné přihlašování) | Stavební blok: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |
| Skupina cloudu, kterému je přiřazen přístup k aplikaci v #1 je určena | Stavební blok: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) <br/>[Vytvořte skupinu a přidejte členy v Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Byla zjištěna přihlašovací údaje pro přístup aplikace k pracovníkovi s informacemi | [Co je na přístupovém panelu?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Odeberte uživatele ze skupiny, které aplikace je přiřazena k | [Správa členství ve skupině pro uživatele v klientovi služby Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Počkejte několik minut, než jeho rušení | [Automatické zřizování uživatelů aplikace SaaS ve službě Azure AD: Jak funguje automatické zřizování?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Na relace samostatné prohlížeče Přihlaste se jako pracovník Moje aplikace portál a potvrďte, že dlaždice chybí | http://myapps.microsoft.com |


### <a name="considerations"></a>Požadavky

Odvodit PoC scénáři a odešlé jednotky nebo absencí scénáře. Pokud uživatel získá zakázán v místní službě AD nebo odebrán, už je způsob, jak se přihlásit k aplikaci SaaS.

## <a name="self-service-access-to-application-management"></a>Samoobslužné přístup ke správě aplikací

Přibližná doba na dokončeno: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikace uživatelů POC, které bude požadovat přístup k aplikacím, jako součást skupiny zabezpečení | Stavební blok: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |
| Cílová aplikace nasazené | Stavební blok: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte do okna podnikové aplikace v portálu správy Azure AD | [Portál pro správu Azure AD: Podnikové aplikace, které](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Nakonfigurovat aplikaci z požadavky se samoobslužné služby | [Co je nového v nástroji Správa podniková aplikace v Azure Active Directory: Konfigurace přístupu k aplikaci Samoobslužné služby](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Přihlaste se jako pracovník na portál Moje aplikace | http://myapps.microsoft.com |
| Všimněte si, "+ Přidat aplikaci" tlačítko na op stránky. Umožňuje vám získat přístup k aplikaci |  |

### <a name="considerations"></a>Požadavky

Aplikace vybrali používají zřizování požadavky, takže okamžitě přejdete k aplikaci by mohly způsobit některé chyby. Pokud aplikace vybrali podporuje zřizování s azure ad a je nakonfigurován, tuto funkci můžete použít jako příležitost k zobrazení celého toku práce a provést tak kompletní. V tématu stavební blok pro [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) pro další doporučení

## <a name="self-service-password-reset"></a>Samoobslužné resetování hesla

Přibližná doba na dokončeno: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Povolte správu hesla pomocí samoobslužné služby ve vašem klientovi. | [Azure Active Directory resetování hesla pro správce IT](active-directory-passwords-update-your-own-password.md) |
| Povolte zpětný zápis hesla ke správě hesel z místní. Poznámka: to vyžaduje konkrétní Azure AD Connect verze | [Požadavky pro zpětný zápis hesla](authentication/howto-sspr-writeback.md) |
| Identifikujte PoC uživatele, kteří tuto funkci používat a ujistěte se, že jsou členy skupiny zabezpečení. Uživatelé musí být bez oprávnění správce na plně prezentují funkce | [Přizpůsobení: Správa hesel Azure AD: omezení přístupu k resetování hesla](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte na portál pro správu Azure AD: resetování hesla | [Portálu pro správu Azure AD: Resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Určí, že zásady resetování hesla. Pro účely testování Koncepce můžete použít telefonní hovor a otázek a odpovědí. Doporučujeme povolit registrace, která má být potřeba zadat v přihlášení na panel přístupu |  |
| Odhlaste se a přihlaste se jako pracovník s informacemi |  |
| Zadat data funkce samoobslužného resetování hesla, jak nakonfigurovat po jednotlivých krok 2 | https://aka.ms/ssprsetup |
| Zavřete prohlížeč |  |
| Začít od začátku procesu přihlášení jako pracovník s informacemi, které jste použili v kroku 4 |  |
| Resetování hesla | [Aktualizujte své heslo: resetování hesla](active-directory-passwords-update-your-own-password.md) |
| Zkuste se přihlásit pomocí nového hesla ke službě Azure AD i tak, aby místní prostředky |  |

### <a name="considerations"></a>Požadavky

1. Jestliže upgrade Azure AD Connect bude způsobit tření, zvažte použití proti cloudové účty ani ji nastavit jako ukázku proti samostatné prostředí
2. Správci mají různé zásady a resetování hesla pomocí účtu správce může nezanechávajícího skvrny ve fázi PoC a způsobit nejasnostem. Ujistěte se, že použijete k testování operací resetování běžného uživatelského účtu


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication s telefonní hovory

Přibližná doba na dokončeno: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikovat POC uživatele, kteří budou používat vícefaktorového ověřování  |  |
| Phone s funkčním příjem pro ověřovací test MFA  | [Co je Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte do okna "Uživatelé a skupiny" v portálu správy Azure AD | [Správy portálu Azure AD: Uživatelé a skupiny](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Zvolte okno "Všichni uživatelé" |  |
| V horním panelu zvolte tlačítko "Vícefaktorového ověřování" | Přímá adresa URL pro portál Azure MFA: https://aka.ms/mfaportal |
| V nastavení "User" Vyberte uživatele, ověření koncepce a povolení vícefaktorového ověřování | [Stavy uživatele ve službě Azure Multi-Factor Authentication](authentication/howto-mfa-userstates.md) |
| Přihlaste se jako PoC uživatelů a procházení procesem výš  |  |

### <a name="considerations"></a>Požadavky

1. Ve fázi PoC kroky v této stavebním blokem explicitně nastavení vícefaktorového ověřování pro uživatele na všechny přihlášení. Existují jiné nástroje, jako je například podmíněný přístup a ochrana Identity, které zaujmout MFA na více cílových scénářů. To bude vypadat přibližně vzít v úvahu při přesouvání z POC do produkčního prostředí.
2. Testování koncepce kroků v této stavebním blokem explicitně používají telefonní hovory jako metodu vícefaktorového ověřování pro expedience. Jak z POC přechod do produkčního prostředí, doporučujeme používat aplikace, jako [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) jako vaše druhý faktor, kdykoli je to možné.
Další informace: [koncept speciální publikace NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>MFA podmíněného přístupu pro aplikace SaaS

Přibližná doba na dokončeno: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikaci PoC uživatelů pro zásady. Tito uživatelé musí být ve skupině zabezpečení k určení rozsahu zásad podmíněného přístupu | [Konfigurace SaaS federovaného jednotného přihlašování](#saas-federated-sso-configuration) |
| Aplikace SaaS byl již nakonfigurován. |  |
| Uživatelé PoC jsou již přiřazena k aplikaci |  |
| Přihlašovací údaje, aby uživatel POC jsou k dispozici |  |
| Testování Koncepce uživatel je registrovaný pro MFA. Pomocí telefonu s funkčním příjem | https://aka.ms/ssprsetup |
| Zařízení v interní síti. IP adresou nakonfigurovanou v interní adresa rozsahu | Najít ip adresu: https://www.bing.com/search?q=what%27s+my+ip |
| Zařízení v externí síti (může být telefon pomocí rozumí provozovatel mobilní sítě.) |  |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte na portál pro správu Azure AD: okno podmíněného přístupu | [Portálu pro správu Azure AD: Podmíněný přístup](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Vytvoření zásady podmíněného přístupu:<br/>-Cíl PoC uživatele v části "Uživatelé a skupiny"<br/>-Cíl aplikací PoC v části "Cloudových aplikací"<br/>-Cílový všech umístění s výjimkou důvěryhodné ty, které jsou v části "Podmínky" -> "Umístění" **Poznámka:** důvěryhodné IP adresy se konfigurují v [portál vícefaktorového ověřování](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Vyžadovat vícefaktorové ověřování v části "Grant" | [Začínáme s podmíněným přístupem v Azure Active Directory: postup konfigurace zásad](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Přístup z aplikace uvnitř podnikové sítě | [Začínáme s podmíněným přístupem v Azure Active Directory: testování zásad](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Přístup k aplikaci z veřejné síti | [Začínáme s podmíněným přístupem v Azure Active Directory: testování zásad](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Požadavky

Pokud používáte federace, můžete použít místní zprostředkovatele Identity (IdP) ke komunikaci s deklaracemi identity stav uvnitř/mimo podnikovou síť. Tento postup můžete použít bez nutnosti ke správě seznamu IP adres, které může být složité k posuzování a správě ve velkých organizacích. V této instalaci potřebovat účet pro scénář "roamingu sítě" (uživatel, protokolování z interní sítě a při přihlášeného přepínače umístění, například v kavárně) a ujistěte se, že rozumíte důsledkům. Další informace: [zabezpečení cloudových prostředků s Azure Multi-Factor Authentication a AD FS: důvěryhodné IP adresy pro federované uživatele](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Správa privilegovaných identit (PIM)

Přibližná doba na dokončeno: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikovat globální správce, který bude součástí POC v PIM. | [Začít používat Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identifikovat globální správce, který se stane správce zabezpečení. | [Začít používat Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Různé správu role v Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md) |
| Volitelné: Ujistěte se, pokud globální správci e-mailu přístup k e-mailová oznámení v PIM výkonu | [Co je Azure AD Privileged Identity Management?: Konfigurace nastavení aktivace role](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přihlášení k https://portal.azure.com jako globální správce (GA) a bootstrap okně PIM. Globální správce, který provádí tento krok je nasazených jako správce zabezpečení.  Umožňuje volání této GA1 objektu actor | [Pomocí Průvodce zabezpečení v Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identifikujte globálního správce a přesunout z trvalé do vhodné. To by měl být samostatné správce ze používaný pro přehlednost v kroku 1. Umožňuje volání této GA2 objektu actor | [Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Co je Azure AD Privileged Identity Management?: Konfigurace nastavení aktivace role](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Nyní, přihlaste se jako GA2 k https://portal.azure.com a zkuste změnit "Nastavení uživatele". Všimněte si, že se některé možnosti jsou zobrazena šedě. | |
| Na nové kartě a ve stejné relaci jako krok 3, přejděte nyní na https://portal.azure.com a přidejte okně PIM do řídicího panelu. | [Postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management: Přidání aplikace Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Žádost o aktivaci role Globální správce | [Postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management: aktivovat roli](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Všimněte si, že pokud GA2 nikdy zaregistrovali do vícefaktorového ověřování, registrace pro Azure MFA bude nutné |  |
| Přejděte zpět na kartu původního v kroku 3 a klikněte na tlačítko Aktualizovat v prohlížeči. Všimněte si, že teď máte přístup k změna "Uživatelského nastavení" | |
| Volitelně Pokud globální správci e-mailu povolen, můžete zkontrolovat GA1 a GA2 pro složky Doručená pošta a oznámení, Probíhá aktivace role |  |
| 8 zkontrolujte historie auditu a pozorovat, že sestava potvrďte zvýšení úrovně GA2 zobrazí. | [Co je Azure AD Privileged Identity Management?: kontrolní aktivita role](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Požadavky

Tato funkce je součástí P2 Azure AD Premium nebo EMS E5

## <a name="discovering-risk-events"></a>Zjišťování rizikových událostí

Přibližná doba na dokončeno: 20 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Zařízení s prohlížečem Tor staženy a nainstalovány | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přístup k uživatelům testování Koncepce provést přihlášení | [Azure seznam strategií ochrany identit Active Directory](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Otevřete tor prohlížeče | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přihlaste se k https://myapps.microsoft.com pomocí uživatelského účtu POC | [Azure Active Directory Identity Protection playbook: simulaci rizikových událostí](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Počkejte 5 – 7 minut |  |
| Přihlaste se jako globální správce, aby https://portal.azure.com a otevřete okno Identity Protection | https://aka.ms/aadipgetstarted |
| Otevřete okno riziko události. Měli byste vidět položku v části "Přihlášení z anonymních IP adres"  | [Azure Active Directory Identity Protection playbook: simulaci rizikových událostí](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Požadavky

Tato funkce je součástí P2 Azure AD Premium nebo EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Nasazení zásad riziko přihlášení

Přibližná doba na dokončeno: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Zařízení s prohlížečem Tor staženy a nainstalovány | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přístup jako POC uživatel mohl přihlásit testování |  |
| Testování Koncepce uživatel je registrovaný pomocí vícefaktorového ověřování. Nezapomeňte použít telefon s funkčním příjem | Stavební blok: [Azure Multi-Factor Authentication s telefonní hovory](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přihlaste se jako globální správce, aby https://portal.azure.com a otevřete okno Identity Protection | https://aka.ms/aadipgetstarted |
| Povolte zásadu přihlášení riziko takto:<br/>– Přiřazené k: POC uživatele<br/>-Podmínky: Přihlášení riziko, střední a vyšší (přihlášení z anonymních umístění se považuje jako riziko střední úroveň)<br/>– Ovládací prvky: Vícefaktorové ověřování vyžadovat | [Azure Active Directory Identity Protection playbook: riziko přihlášení](active-directory-identityprotection-playbook.md) |
| Otevřete tor prohlížeče | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přihlaste se k https://myapps.microsoft.com pomocí uživatelského účtu PoC |  |
| Všimněte si ověřovací test MFA | [Přihlášení vyskytne s Azure AD Identity Protection: obnovení rizikové přihlášení](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Požadavky

Tato funkce je součástí Azure AD Premium P2 nebo EMS E5. Další informace o rizikových událostech navštivte: [rizikových událostí Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Konfigurace ověřování na základě certifikátů

Přibližná doba na dokončení: 20 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Zařízení s uživatelský certifikát zřízení (Windows, iOS nebo Android) z infrastruktury veřejných KLÍČŮ rozlehlé sítě | [Nasazení uživatelských certifikátů](https://msdn.microsoft.com/library/cc770857.aspx) |
| Azure AD domain sdružených se službou AD FS | [Azure AD Connect a federace](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Přehled služby Active Directory Certificate Services](https://technet.microsoft.com/library/hh831740.aspx)|
| Pro zařízení s iOS mají nainstalovanou aplikaci Microsoft Authenticator | [Začínáme s aplikací Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Povolení "Ověřování pomocí certifikátů" na služby AD FS | [Nakonfigurovat zásady ověřování: Globální konfigurace primární ověřování ve Windows serveru 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Volitelné: Povolení ověřování pomocí certifikátů ve službě Azure AD pro klienty protokolu Exchange Active Sync | [Začínáme s ověřováním pomocí certifikátů v Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Přejděte na Panel přístupu a ověřování pomocí certifikátu uživatele | https://myapps.microsoft.com |

### <a name="considerations"></a>Požadavky

Další informace o upozornění tohoto nasazení navštivte: [služby AD FS: ověření certifikátu s Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> U sebe uživatelský certifikát by měl být chráněn. Buď pomocí správy zařízení nebo pomocí kódu PIN v případě čipové karty.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
