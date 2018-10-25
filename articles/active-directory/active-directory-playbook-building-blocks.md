---
title: Testování konceptu playbook stavebních bloků Azure Active Directory | Dokumentace Microsoftu
description: Prozkoumejte a rychle implementovat scénáře správy identit a přístupu
services: active-directory
keywords: Azure active directory, playbooku, testování konceptu, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: dstefan
ms.openlocfilehash: 7aa0f80b8a0b53d4c53cf633938765e820c641cf
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025319"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory doklad o playbook koncept: stavební bloky

## <a name="catalog-of-roles"></a>Katalog rolí

| Role | Popis | Testování konceptu (PoC) odpovědnosti |
| --- | --- | --- |
| **Architektura identity / vývojový tým** | Tento tým je obvykle ten, který navrhuje řešení implementuje prototypy, jednotky schválení a nakonec předá do operací | Poskytují prostředí a jsou ty vaše rozhodnutí vyzkoušet různé scénáře z hlediska správy |
| **On-Premises Identity provozní tým** | Spravuje jiná identita zdroje místní: doménové struktury služby Active Directory, adresáře LDAP, systémy oddělení Personalistiky a federačního zprostředkovatele Identity. | Poskytnutí přístupu k místním prostředkům, které jsou potřebné pro scénáře PoC.<br/>Jejich by měl podílejí co|
| **Technické vlastníky aplikace.** | Technické vlastníky různých cloudových aplikací a služeb, které se bude integrovat s Azure AD | Podrobnější informace najdete na aplikace SaaS (potenciálně instance pro účely testování) |
| **Globální správce Azure AD** | Spravuje konfiguraci Azure AD | Zadejte přihlašovací údaje pro službu synchronizace. Obvykle stejný týmu během PoC jako architektura Identity, ale samostatné fázi operace|
| **Databáze team** | Vlastníci databázové infrastruktury | Poskytují přístup k prostředí SQL (služby AD FS nebo služby Azure AD Connect) pro konkrétní scénář přípravy.<br/>Jejich by měl podílejí co |
| **Tým síťových** | Vlastníci síťové infrastruktury | Zadejte požadovaný přístup na úrovni sítě pro synchronizační servery správně přistupovat ke zdrojům dat a cloudovým službám (pravidla brány firewall, porty otevřít, pravidla IPSec atd.) |
| **Bezpečnostní tým** | Definuje bezpečnostní strategie, analyzuje zprávy o zabezpečení z různých zdrojů a postupuje podle zjištění. | Zajištění zabezpečení cílové scénáře hodnocení |

## <a name="common-prerequisites-for-all-building-blocks"></a>Běžné požadavky pro všechny stavební bloky

Tady jsou některé požadavky potřebné pro všechny POC s Azure AD Premium.

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Tenant Azure AD definované s platným předplatným Azure | [Získání tenanta služby Azure Active Directory](develop/quickstart-create-new-tenant.md)<br/>**Poznámka:** Pokud již máte prostředí s licencí Azure AD Premium, žádný limit předplatného můžete získat tak, že přejdete na https://aka.ms/accessaad <br/>Další informace najdete na: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ a https://technet.microsoft.com/library/dn832618.aspx |
| Definice a ověření domény | [Přidání vlastního názvu domény do Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Poznámka:** některé úlohy, jako jsou Power BI může mít zřízení tenanta služby azure AD na pozadí. Zkontrolujte, jestli je dané domény přidružené k tenantovi, přejděte na https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Je-li získat úspěšné odpovědi, pak doména je už přiřazený k tenantovi a převzít kontrolu nad může být nutné. Pokud ano, pro další informace získáte od Microsoftu. Další informace o převzetí možnosti v: [co je Samoobslužná registrace pro Azure?](users-groups-roles/directory-self-service-signup.md) |
| Azure AD Premium nebo EMS zkušební povoleno | [Azure Active Directory Premium zdarma po dobu jednoho měsíce](https://azure.microsoft.com/trial/get-started-active-directory/) |
| PoC uživatelům přiřadíte licence EMS nebo Azure AD Premium | [Licence sami sebe i vaše uživatele v Azure Active Directory](active-directory-licensing-get-started-azure-portal.md) |
| Přihlašovací údaje Azure AD globálního správce | [Přiřazení rolí správce v Azure Active Directory](users-groups-roles/directory-assign-admin-roles.md) |
| Volitelné, ale důrazně ho doporučujeme: paralelní testovacího prostředí jako záložní | [Požadavky pro Azure AD Connect](hybrid/how-to-connect-install-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Nová instalace synchronizace - synchronizace hodnot Hash hesel (PHS) - Directory

Přibližný čas dokončení: jednu hodinu, než méně než 1000 uživateli současně PoC

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Připojte server ke spuštění služby Azure AD | [Požadavky pro Azure AD Connect](hybrid/how-to-connect-install-prerequisites.md) |
| Cíloví uživatelé POC ve stejné doméně a součást zabezpečení skupiny a organizační jednotky | [Vlastní instalace služby Azure AD Connect](hybrid/how-to-connect-install-custom.md#domain-and-ou-filtering) |
| Azure AD Connect funkce, která je potřebné pro testování Konceptu jsou označeny. | [Připojení Active Directory s Azure Active Directory – konfigurace synchronizace funkcí](hybrid/how-to-connect-install-roadmap.md#configure-sync-features) |
| Požadovaná pověření pro místní a cloudové prostředí  | [Azure AD Connect: Účty a oprávnění](hybrid/reference-connect-accounts-permissions.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Stáhněte si nejnovější verzi Azure AD Connect | [Stáhněte si Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Instalace služby Azure AD Connect pomocí nejjednodušší cesta: Express <br/>1. Filtrovat k cíli organizační jednotky, aby se minimalizoval čas cyklus synchronizace<br/>2. Zvolte cíl sadu uživatelů v místní skupině.<br/>3. Nasazení funkce vyžadované jiné POC motivy | [Azure AD Connect: Vlastní instalace: domény a organizační jednotky filtrování](hybrid/how-to-connect-install-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: Vlastní instalace: filtrování na základě skupiny](hybrid/how-to-connect-install-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Integrování místních identit Azure Active Directory: Konfigurace synchronizačních funkcí](hybrid/how-to-connect-install-roadmap.md#configure-sync-features) |
| Otevřete Azure AD Connect uživatelského rozhraní a zobrazit spuštěné profily dokončené (Import, synchronizace a export) | [Synchronizace Azure AD Connect: Plánovač](hybrid/how-to-connect-sync-feature-scheduler.md) |
| Otevřít [portálu pro správu Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), přejděte do okna "Všechny uživatele", přidejte sloupec "Zdroj autority" a, že se zobrazí uživatelům, správně označené jako pocházející z "Windows Server AD" | [Portál pro správu Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Požadavky

1. Podívejte se na aspekty zabezpečení synchronizace hodnot hash hesel [tady](hybrid/how-to-connect-password-hash-synchronization.md).  Pokud synchronizace hodnot hash hesel pro uživatele pilotního nasazení produkčního prostředí není platností možnost, pak zvažte následující možnosti:
   * Vytvoření testovacích uživatelů do domény v produkčním prostředí. Ujistěte se, že nesynchronizovat jiný účet
   * Přesunout do prostředí UAT
2.  Pokud chcete vykonávat federation, je vhodné pochopit, že náklady na přidružený k federované řešení místního zprostředkovatele Identity nad rámec testování Konceptu a vyhodnocování, které podle výhod, které hledáte:
    * Jde na kritické cestě, takže budete muset návrh pro zajištění vysoké dostupnosti
    * Je to místní služba, které potřebujete k plánování kapacity
    * Je to místní služba, které potřebujete k monitorování/udržovat/patch

Další informace: [identity Principy Office 365 a Azure Active Directory – Federated Identity](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Branding

Přibližný čas dokončení: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Prostředky (obrázky, loga, atd.); Pro nejlepší vizualizaci Ujistěte se, že máte prostředky doporučené velikosti. | [Přidání firemního brandingu na přihlašovací stránku ve službě Azure Active Directory](active-directory-branding-custom-signon-azure-portal.md) |
| Volitelné: Pokud prostředí obsahuje server služby AD FS, přístup k serveru, aby vlastní webový motiv | [AD FS uživatele přihlásit vlastního nastavení](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Klientského počítače provádět činnost koncového uživatele přihlášení |  |
| Volitelné: Mobilní zařízení k ověření prostředí |  |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte na portálu pro správu Azure AD | [Portál pro správu Azure AD – vlastní firemní Branding](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Nahrávejte prostředky pro přihlašovací stránku (hero logo, malé logo, popisky, atd.). Případně pokud máte službu AD FS, zarovnání stejné prostředky s přihlašovací stránky služby AD FS | [Přidat vlastní firemní branding přihlašování a přístupového panelu stránky: přizpůsobitelné prvky](fundamentals/customize-branding.md) |
| Počkejte pár minut, než se změna se plně projeví |  |
| Přihlaste se pomocí přihlašovacích údajů uživatele POC do https://myapps.microsoft.com |  |
| Potvrďte vzhledu a chování v prohlížeči | [Přidat vlastní firemní branding přihlašování a přístupového panelu stránky](fundamentals/customize-branding.md) |
| Volitelně můžete potvrďte vzhledu a chování v jiných zařízení |  |

### <a name="considerations"></a>Požadavky

Pokud původní vzhledu a chování zůstane po přizpůsobení vyprázdnit mezipaměť prohlížeče klienta a zkuste operaci zopakovat.

## <a name="group-based-licensing"></a>Na základě skupiny licencí

Přibližný čas dokončení: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Všichni uživatelé POC jsou součástí skupiny zabezpečení (Cloudová nebo místní) | [Vytvoření skupiny a přidání členů v Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte do okna licence v portálu pro správu Azure AD | [Portál pro správu Azure AD: licencování](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Přiřazení licencí ke skupině zabezpečení s uživateli POC. | [Přiřazení licencí ke skupině uživatelů v Azure Active Directory](users-groups-roles/licensing-groups-assign.md) |

### <a name="considerations"></a>Požadavky

V případě jakékoli problémy, přejděte na [scénářů, omezení a známé problémy s použití skupin pro správu licencování v Azure Active Directory](users-groups-roles/licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Konfigurace SaaS federovaného jednotného přihlašování

Přibližný čas dokončení: 60 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Testovací prostředí aplikace SaaS, která je k dispozici. V této příručce použijeme jako příklad ServiceNow.<br/>Důrazně doporučujeme používat instanci testu chcete-li minimalizovat případná problémová místa pro přechod existující kvalitu dat a mapování. | Přejděte na https://developer.servicenow.com/app.do#! / domácí zahájíte proces načítání instance testu |
| Přístup správce ke konzole pro správu ServiceNow | [Kurz: Integrace Azure Active Directory s použitím ServiceNow](saas-apps/servicenow-tutorial.md) |
| Cílové sadu uživatelů do přiřazení aplikace k. Doporučuje se skupinu zabezpečení obsahující uživatele PoC. <br/>Pokud se vytváření skupiny není proveditelné, zařaďte uživatelům přímo do aplikace pro testování konceptu | [Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Sdílet kurzu pro všechny objekty actor z Documentation Microsoftu  | [Kurz: Integrace Azure Active Directory s použitím ServiceNow](saas-apps/servicenow-tutorial.md) |
| Nastavit pracovní schůzku a postupujte podle kroků kurzu se každý objekt actor. | [Kurz: Integrace Azure Active Directory s použitím ServiceNow](saas-apps/servicenow-tutorial.md) |
| Aplikaci můžete přiřadíte ke skupině identifikované v rámci požadavků. Pokud testování Konceptu podmíněného přístupu v oboru, můžete opakování, který později a přidat vícefaktorové ověřování a podobně. <br/>Všimněte si, že to bude spouštět v procesu zřízení (je-li konfigurováno) |  [Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) <br/>[Vytvoření skupiny a přidání členů v Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Správa služby Azure AD Portal slouží k přidání ServiceNow aplikaci z Galerie| [Správa služby Azure AD Portal: podnikových aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Kurz: Integrace Azure Active Directory s použitím ServiceNow](saas-apps/servicenow-tutorial.md) |
| V okně aplikace ServiceNow "Jednotné přihlašování" zapnout "založené na SAML Sign-on" |  |
| Vyplňte pole "Přihlášení na URL" a "Identifikátor" s adresou URL vašeho ServiceNow<br/>Zaškrtnutím políčka "Nastavit nový certifikát jako aktivní"<br/>a uložte nastavení |  |
| Otevře se okno "Konfigurovat ServiceNow" v dolní části panelu zobrazíte přizpůsobené pokyny, jak je možné nakonfigurovat ServiceNow |  |
| Postupujte podle pokynů ke konfiguraci ServiceNow |  |
| V okně aplikace ServiceNow "Zřizování" zapnout "Automatické" zřizování | [Správa uživatelského účtu zřizování pro podnikové aplikace na novém portálu Azure Portal](manage-apps/configure-automatic-user-provisioning-portal.md) |
| Počkejte pár minut bude zřizování dokončeno.  Do té doby můžete zkontrolovat na sestavy o zřizování |  |
| Přihlaste se k https://myapps.microsoft.com/ jako testovací uživatel, který má přístup | [Co je na přístupovém panelu?](user-help/active-directory-saas-access-panel-introduction.md) |
| Kliknutím na dlaždici aplikace, kterou jste právě vytvořili. Ověření přístupu |  |
| Volitelně můžete zkontrolovat sestavy využití aplikací. Všimněte si, že není určitou latenci, takže budete muset počkat určitou dobu sledovat provoz v sestavách. | [Sestavy aktivit přihlašování na portálu Azure Active Directory: použití spravovaných aplikací](reports-monitoring/concept-sign-ins.md#usage-of-managed-applications)<br/>[Zásady uchovávání sestav služby Azure Active Directory](reports-monitoring/reference-reports-data-retention.md) |

### <a name="considerations"></a>Požadavky

1. Pokud je cílová aplikace není k dispozici v galerii, můžete použít, "Přineste si vlastní aplikaci". Další informace: [konfiguraci aplikací, které nejsou v galerii aplikací Azure AD](manage-apps/configure-single-sign-on-non-gallery-applications.md).

## <a name="saas-password-sso-configuration"></a>Konfigurace jednotného přihlašování hesla SaaS

Přibližný čas dokončení: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Testovací prostředí pro aplikace SaaS. Příklad jednotné přihlašování heslem je HipChat a Twitter. Pro jakékoli jiné aplikace budete potřebovat přesnou adresu URL stránky pomocí přihlašovacího formuláře html. | [Na webu Microsoft Azure Marketplace na twitteru](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat na webu Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testovací účty pro aplikace. | [Zaregistrujte si Twitter](https://twitter.com/signup?lang=en)<br/>[Zaregistrovat si bezplatný: Hipchatu](https://www.hipchat.com/sign_up) |
| Cílové sadu uživatelů do přiřazení aplikace k. Skupiny zabezpečení obsažené uživatelé se doporučuje. | [Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Oprávnění místního správce přístup k počítači a nasazení pro aplikaci Internet Explorer, Chrome nebo Firefox rozšíření přístupového panelu | [Rozšíření přístupového panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístupového panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístupového panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Instalace rozšíření prohlížeče | [Rozšíření přístupového panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístupového panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístupového panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Nakonfigurovat aplikaci z Galerie | [Kurz: Konfigurace založené na SAML jednotného přihlašování](manage-apps/configure-single-sign-on-portal.md) |
| Konfigurace heslem jednotného přihlašování | [Správa jednotného přihlašování pro podnikové aplikace na novém portálu Azure Portal: přihlašování založené na heslech](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).|
| Přiřadit skupinu určené v požadavcích aplikace | [Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Přihlaste se k https://myapps.microsoft.com/ jako testovací uživatel, který má přístup |  |
| Kliknutím na dlaždici aplikace, kterou jste právě vytvořili. | [Co je na přístupovém panelu?: založené na heslech jednotné přihlašování bez zřizování identity](user-help/active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zadat přihlašovací údaje, které aplikace | [Co je na přístupovém panelu?: založené na heslech jednotné přihlašování bez zřizování identity](user-help/active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zavřete prohlížeč a opakujte přihlášení. Uživatel by měl nyní zobrazit bezproblémový přístup k aplikaci. |  |
| Volitelně můžete zkontrolovat sestavy využití aplikací. Všimněte si, že není určitou latenci, takže budete muset počkat určitou dobu sledovat provoz v sestavách. | [Sestavy aktivit přihlašování na portálu Azure Active Directory: použití spravovaných aplikací](reports-monitoring/concept-sign-ins.md#usage-of-managed-applications)<br/>[Zásady uchovávání sestav služby Azure Active Directory](reports-monitoring/reference-reports-data-retention.md) |

### <a name="considerations"></a>Požadavky

Pokud je cílová aplikace není k dispozici v galerii, můžete použít, "Přineste si vlastní aplikaci". Další informace: [konfiguraci aplikací, které nejsou v galerii aplikací Azure AD](manage-apps/configure-single-sign-on-non-gallery-applications.md)

 Mějte na paměti následující požadavky:
   * Aplikace by měla mít známé přihlašovací adresa URL.
   * Přihlašovací stránka by měla obsahovat s jeden další textová pole, které rozšíření prohlížeče mohou automaticky vyplnit pole formuláře HTML. Minimálně musí obsahovat uživatelské jméno a heslo.

## <a name="saas-shared-accounts-configuration"></a>Konfigurace sdílené účty SaaS

Přibližný čas dokončení: 30 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Seznam cílové aplikace a přesně přihlašovací adresy URL předem. Například můžete použít Twitter. | [Na webu Microsoft Azure Marketplace na twitteru](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Zaregistrujte si Twitter](https://twitter.com/signup?lang=en) |
| Sdílené přihlašovací údaje pro tuto aplikaci SaaS. | [Sdílení účtů pomocí Azure AD](active-directory-sharing-accounts.md)<br/>[Azure AD automatizované heslo převrácení pro Facebook, Twitter a LinkedIn teď ve verzi preview! – Enterprise Mobility and Security Blog](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Přihlašovací údaje pro alespoň dva členy týmu, kteří budou přistupovat k stejný účet. Musí být součástí skupiny zabezpečení. | [Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Oprávnění místního správce přístup k počítači a nasazení pro aplikaci Internet Explorer, Chrome nebo Firefox rozšíření přístupového panelu | [Rozšíření přístupového panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístupového panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístupového panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Instalace rozšíření prohlížeče | [Rozšíření přístupového panelu pro aplikaci Internet Explorer](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Rozšíření přístupového panelu pro Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Rozšíření přístupového panelu pro Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Nakonfigurovat aplikaci z Galerie | [Kurz: Konfigurace založené na SAML jednotného přihlašování pro aplikaci](manage-apps/configure-single-sign-on-portal.md) |
| Konfigurace heslem jednotného přihlašování | [Správa jednotného přihlašování pro podnikové aplikace na novém portálu Azure Portal: přihlašování založené na heslech](manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).|
| Přiřadit aplikace do skupiny podle požadavků při přiřazování jejich přihlašovacích údajů | [Přiřadit uživatele nebo skupiny k podnikové aplikace v Azure Active Directory](manage-apps/assign-user-or-group-access-portal.md) |
| Přihlaste se jako různí uživatelé tuto aplikaci přístup, jako **sdílí stejný účet.**  |  |
| Volitelně můžete zkontrolovat sestavy využití aplikací. Všimněte si, že není určitou latenci, takže budete muset počkat určitou dobu sledovat provoz v sestavách. | [Sestavy aktivit přihlašování na portálu Azure Active Directory: použití spravovaných aplikací](reports-monitoring/concept-sign-ins.md#usage-of-managed-applications)<br/>[Zásady uchovávání sestav služby Azure Active Directory](reports-monitoring/reference-reports-data-retention.md) |


### <a name="considerations"></a>Požadavky

Pokud je cílová aplikace není k dispozici v galerii, můžete použít, "Přineste si vlastní aplikaci". Další informace: [nakonfigurovat jednotné přihlašování k aplikacím, které nejsou v galerii aplikací Azure AD](manage-apps/configure-single-sign-on-non-gallery-applications.md)

 Mějte na paměti následující požadavky:
   * Aplikace by měla mít známé přihlašovací adresa URL
   * Přihlašovací stránka by měla obsahovat s jeden další textová pole, které rozšíření prohlížeče mohou automaticky vyplnit pole formuláře HTML. Minimálně musí obsahovat uživatelské jméno a heslo.

## <a name="app-proxy-configuration"></a>Konfigurace Proxy aplikací

Přibližný čas dokončení: 20 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Microsoft Azure AD basic nebo předplatné premium a adresář služby Azure AD, u kterého jste globální správce | [Edice Azure Active Directory](fundamentals/active-directory-whatis.md) |
| Webové aplikace hostované v místním prostředí, který chcete nakonfigurovat pro vzdálený přístup |  |
| Serveru se systémem Windows Server 2012 R2 nebo Windows 8.1 nebo novější, na který nainstalujete konektor Proxy aplikace | [Principy konektorů Proxy aplikací Azure AD](manage-apps/application-proxy-connectors.md) |
| Pokud je v cestě brána firewall, ujistěte se, že je otevřete tak, aby konektor může provádět požadavky prostřednictvím protokolu HTTPS (TCP) Proxy aplikací | [Povolení Proxy aplikace na webu Azure Portal: požadavky na Proxy aplikace](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Pokud vaše organizace používá proxy servery pro připojení k Internetu, proveďte najdete v blogovém příspěvku s existující místní proxy server pro podrobnosti o tom, jak je nakonfigurovat pracovní | [Práce s existující místní proxy servery](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Nainstalujte konektor na server | [Povolení Proxy aplikace na webu Azure Portal: instalace a registrace konektoru](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| Publikování aplikace v místním prostředí ve službě Azure AD jako aplikace Proxy aplikací | [Publikování aplikací pomocí Proxy aplikací Azure AD](manage-apps/application-proxy-publish-azure-portal.md) |
| Přiřadit testovací uživatele | [Publikování aplikací pomocí Proxy aplikací Azure AD: Přidání testovacího uživatele](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Volitelně můžete nakonfigurujte jednotné přihlašování prostředí pro uživatele | [Poskytuje jednotné přihlašování s Azure AD Application Proxy](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| Testování aplikace po přihlášení k portálu MyApps jako přiřazený uživatel | https://myapps.microsoft.com |

### <a name="considerations"></a>Požadavky

1. Přestože doporučujeme uvedení konektoru v podnikové síti, existují případy, když se zobrazí lepší výkon, že ho umístíte do cloudu. Další informace: [aspekty topologie sítě, při použití Azure Active Directory Application Proxy](manage-apps/application-proxy-network-topology.md)
2. Další podrobné informace o zabezpečení a jak to zejména zabezpečený vzdálený přístup poskytuje řešení pouze udržováním odchozího připojení naleznete v tématu: [aspekty zabezpečení pro vzdálený přístup k aplikací s využitím Azure AD Application Proxy](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Obecný konektor LDAP konfigurace

Přibližný čas dokončení: 60 minut

> [!IMPORTANT]
> Toto je pokročilou konfiguraci, které vyžadují některé znalost FIM nebo MIM. Je-li použít v produkčním prostředí, kterými byste dotazy týkající se této konfigurace projít [Premier Support](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Azure AD Connect, instalace a konfigurace | Stavebním blokem: [synchronizace adresáře – synchronizace hodnot Hash hesel](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Požadavky na schůzku ADLDS instance | [Technické reference pro obecný konektor LDAP: přehled obecný konektor LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Seznam úloh, které uživatelé používají a atributy přidružené k tyto úlohy | [Synchronizace Azure AD Connect: atributy synchronizované do Azure Active Directory](hybrid/reference-connect-sync-attributes-synchronized.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přidat obecný konektor LDAP | [Technické reference pro obecný konektor LDAP: vytvořit nový konektor](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| Vytvoření profilů spuštění pro vytvořený konektor (úplný import, Rozdílový import, úplná synchronizace, rozdílová synchronizace, export) | [Vytvoření profilu spuštění agenta správy](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Používání konektorů s Azure AD Connect Sync Service Manager](hybrid/how-to-connect-sync-service-manager-ui-connectors.md)|
| Spustit úplný import profilu a ověřte, že jsou objekty v prostoru konektoru | [Vyhledejte objekt prostoru konektoru](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Používání konektorů s Azure AD Connect Sync Service Manager: Search Connector Space](hybrid/how-to-connect-sync-service-manager-ui-connectors.md#search-connector-space) |
| Vytvoření pravidla synchronizace tak, aby objektů v Metaverse atributy vyžadované pro úlohy | [Synchronizace Azure AD Connect: osvědčené postupy pro změnu výchozí konfigurace: změní na synchronizační pravidla](hybrid/how-to-connect-sync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Synchronizace Azure AD Connect: Principy deklarativní zřizování](hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)<br/>[Synchronizace Azure AD Connect: Principy výrazů deklarativního zřizování](hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |
| Spustí cyklus úplné synchronizace | [Synchronizace Azure AD Connect: Plánovač: spustit Plánovač](hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler) |
| V případě problémů se řešení potíží | [Řešení potíží s objektem, který se nesynchronizuje do Azure AD](hybrid/tshoot-connect-object-not-syncing.md) |
| Ověřte, že LDAP uživatel může přihlásit a přístup k aplikaci | https://myapps.microsoft.com |

### <a name="considerations"></a>Požadavky

> [!IMPORTANT]
> Toto je pokročilou konfiguraci, které vyžadují některé znalost FIM nebo MIM. Je-li použít v produkčním prostředí, kterými byste dotazy týkající se této konfigurace projít [Premier Support](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Skupin, delegovat vlastnictví

Přibližný čas dokončení: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Aplikace SaaS (jednotného přihlašování k federovanému nebo jednotného přihlašování hesla) už nakonfigurovala | Stavebním blokem: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |
| Je identifikován cloudu skupiny, který je přiřazen přístup k aplikaci v #1 | Stavebním blokem: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) <br/>[Vytvoření skupiny a přidání členů v Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Přihlašovací údaje pro vlastníka skupiny jsou k dispozici | [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](fundamentals/active-directory-manage-groups.md) |
| Byla zjištěna přihlašovací údaje pro přístup k aplikacím pracovníkovi s informacemi | [Co je na přístupovém panelu?](user-help/active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Identifikovat skupinu, která získala přístup k aplikaci a nakonfigurovat vlastník dané skupiny| [Správa nastavení pro skupinu v Azure Active Directory ](fundamentals/active-directory-groups-settings-azure-portal.md) |
| Přihlaste se jako vlastník skupiny najdete v tématu členství ve skupinách v kartě skupiny z přístupového panelu | [Azure Active Directory skupiny Management stránky](https://account.activedirectory.windowsazure.com/r#/groups) |
| Přidat pracovníkovi s informacemi, které chcete testovat |  |
| Přihlaste se jako informační pracovník, abyste potvrdili, že tato dlaždice je k dispozici | [Co je na přístupovém panelu?](user-help/active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Požadavky

Pokud má aplikace zřizování povolená, můžete potřebovat Počkejte několik minut, než se zřízení dokončí před přístupem k aplikaci jako informační pracovník.

## <a name="saas-and-identity-lifecycle"></a>SaaS a životního cyklu identit

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Aplikace SaaS (jednotného přihlašování k federovanému nebo jednotného přihlašování hesla) už nakonfigurovala | Stavebním blokem: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |
| Je identifikován cloudu skupiny, který je přiřazen přístup k aplikaci v #1 | Stavebním blokem: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) <br/>[Vytvoření skupiny a přidání členů v Azure Active Directory](fundamentals/active-directory-groups-create-azure-portal.md) |
| Byla zjištěna přihlašovací údaje pro přístup k aplikacím pracovníkovi s informacemi | [Co je na přístupovém panelu?](user-help/active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Odebrání uživatele ze skupiny přiřazené k aplikaci | [Správa členství ve skupinách pro uživatele ve vašem tenantovi Azure Active Directory](fundamentals/active-directory-groups-members-azure-portal.md) |
| Počkejte několik minut, než se jeho rušení | [Automatické zřizování uživatelů aplikace SaaS ve službě Azure AD: Jak funguje automatické zřizování?](manage-apps/user-provisioning.md#how-does-automatic-provisioning-work) |
| Na relace samostatné prohlížeče Přihlaste se jako informační pracovník na portálu Moje aplikace a potvrďte tuto dlaždici chybí | http://myapps.microsoft.com |


### <a name="considerations"></a>Požadavky

Potom údaje Extrapolujte PoC scénář, který má odešlé jednotky a/nebo absencí scénáře. Pokud uživatel získá zakázán v místní služby AD nebo odebrali, již není možné přihlásit k aplikaci SaaS.

## <a name="self-service-access-to-application-management"></a>Samoobslužný přístup ke správě aplikací

Přibližný čas dokončení: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikace uživatele služby POC, které se požadují přístup k aplikacím, jako součást zabezpečení skupiny | Stavebním blokem: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |
| Cílová aplikace nasazení | Stavebním blokem: [Konfigurace jednotného přihlašování federovaného SaaS](#saas-federated-sso-configuration) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte do okna podnikové aplikace v portálu pro správu Azure AD | [Portálu pro správu Azure AD: Podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Nakonfigurovat samoobslužné aplikace v požadavcích | [Konfigurace samoobslužného přístupu k aplikacím](manage-apps/manage-self-service-access.md) |
| Přihlaste se jako informační pracovník na portálu Moje aplikace | http://myapps.microsoft.com |
| Všimněte si, že "+ Přidat aplikaci" tlačítko na op stránky. Použijte ho k získání přístupu k aplikaci |  |

### <a name="considerations"></a>Požadavky

Zvolené aplikace může mít zřizování požadavky, tak bude okamžitě aplikace může způsobit nějaké chyby. Pokud aplikace zvolit podporuje zřizování s využitím azure ad a je nakonfigurován, tuto funkci můžete použít jako příležitost k zobrazení celého toku práce komplexní. Zobrazit stavební blok pro [SaaS federované jednotné přihlašování konfigurace](#saas-federated-sso-configuration) Další doporučení

## <a name="self-service-password-reset"></a>Samoobslužné resetování hesla

Přibližný čas dokončení: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Povolte správu hesla pomocí samoobslužné služby ve vašem tenantovi. | [Azure Active Directory – heslo resetoval správce IT](user-help/active-directory-passwords-update-your-own-password.md) |
| Povolte zpětný zápis hesla ke správě místních hesel. Poznámka: to vyžaduje, aby konkrétní služby Azure AD Connect verze | [Požadavky pro zpětný zápis hesla](authentication/howto-sspr-writeback.md) |
| Identifikujte PoC uživatele, kteří budou tuto funkci použít a ujistěte se, že jsou členy skupiny zabezpečení. Uživatelé musí být bez oprávnění správce k prezentaci plně možnosti | [Přizpůsobení: Správa hesel Azure AD: omezení přístupu k resetování hesla](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte na portálu pro správu Azure AD: resetování hesla | [Portál pro správu Azure AD: Resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Určete že zásady pro resetování hesla. Pro účely POC můžete použít telefonních hovorů a funkce Q & A. Doporučuje povolit registraci vyžadovat přihlášení na přístupovém panelu |  |
| Odhlaste se a přihlaste se jako pracovník s informacemi |  |
| Zadat data pro samoobslužné resetování hesla nakonfigurované na krok 2 | https://aka.ms/ssprsetup |
| Zavřete prohlížeč |  |
| Začněte proces přihlášení jako pracovníkovi s informacemi, které jste použili v kroku 4 |  |
| Resetovat heslo | [Aktualizace vlastního hesla: resetování hesla](user-help/active-directory-passwords-update-your-own-password.md) |
| Zkuste se přihlásit pomocí nového hesla ke službě Azure AD stejně jako místní prostředky |  |

### <a name="considerations"></a>Požadavky

1. Pokud upgradu Azure AD Connect bude způsobit případná problémová místa, zvažte použití proti cloudové účty nebo nastavte ji na ukázku proti samostatné prostředí
2. Správci, které mají různé zásady a pomocí účtu správce na resetování hesla můžou taint testování konceptu a způsobit zmatení. Ujistěte se, že používáte běžný uživatelský účet a otestujte operace resetování


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication s telefonních hovorů

Přibližný čas dokončení: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikovat POC uživatele, kteří budou používat vícefaktorové ověřování  |  |
| Telefon s dobrým recepci ověřovacím testem MFA  | [Co je Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte do okna "Uživatele a skupiny" v portálu pro správu Azure AD | [Azure AD Management Portal: Uživatelé a skupiny](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Zvolte možnost "Všechny uživatele" okno |  |
| V horní části panelu zvolte tlačítko "Ověřování službou Multi-Factor Authentication" | Přímá adresa URL pro portál Azure MFA: https://aka.ms/mfaportal |
| V nastavení "Uživatelského" Vyberte PoC uživatelů a povolit pro vícefaktorové ověřování | [Stavy uživatele ve službě Azure Multi-Factor Authentication](authentication/howto-mfa-userstates.md) |
| Přihlaste se jako uživatel PoC a názorný postup vás provede procesem výš  |  |

### <a name="considerations"></a>Požadavky

1. Testování konceptu kroky v tomto stavebním blokem explicitním nastavením MFA pro uživatele na všechna přihlášení. Existují jiné nástroje jako je například podmíněný přístup a ochrana Identity, které kontaktovat MFA na více cílových scénářů. Bude jím něco, co zvážit při přesouvání z POC do produkčního prostředí.
2. PoC kroky v tomto stavebním blokem jsou jako metody vícefaktorového ověřování pro expedience explicitně pomocí telefonních hovorů. Jak přejít od Koncepce do produkčního prostředí doporučujeme používat aplikace, jako [Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) jako váš druhý faktor, kdykoli je to možné.
Další informace: [koncept speciální publikace NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Vícefaktorové ověřování, podmíněného přístupu pro aplikace SaaS

Přibližný čas dokončení: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikujte uživatele PoC cílit na zásady. Tito uživatelé by měl být ve skupině zabezpečení, pokud chcete určit obor zásady podmíněného přístupu | [Konfigurace SaaS federovaného jednotného přihlašování](#saas-federated-sso-configuration) |
| SaaS aplikace byl již nakonfigurován. |  |
| Uživatelé PoC jsou už přiřazené k aplikaci |  |
| Přihlašovací údaje, aby uživatel POC jsou k dispozici |  |
| Uživatel POC je zaregistrovaný pro MFA. Pomocí telefonu s dobrým příjem | https://aka.ms/ssprsetup |
| Zařízení v interní síti. IP adresu nakonfigurovanou v rozsahu adres interní | Najdete vaše ip adresa: https://www.bing.com/search?q=what%27s+my+ip |
| Zařízení v externí síti (může být telefonu pomocí dopravce mobilní sítě) |  |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přejděte na portálu pro správu Azure AD: okna podmíněného přístupu | [Portálu pro správu Azure AD: Podmíněný přístup](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Vytvoření zásad podmíněného přístupu:<br/>-Target PoC uživatele v části "Uživatele a skupiny"<br/>-Target PoC aplikace v části "Cloudových aplikací"<br/>-Cíl všechna místa s výjimkou důvěryhodné ty v části "Podmínky" -> "Umístění" **Poznámka:** důvěryhodné IP adresy se konfigurují v [portál vícefaktorového ověřování](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-Vyžadovat vícefaktorové ověřování v části "Udělení" | [Vytvořte zásadu podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa#create-your-conditional-access-policy) |
| Přístup z aplikace uvnitř firemní sítě. | [Otestovat své zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa#test-your-conditional-access-policy) |
| Přístup k aplikaci z veřejné síti | [Otestovat své zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa#test-your-conditional-access-policy) |

### <a name="considerations"></a>Požadavky

Pokud používáte federaci, můžete ke komunikaci stav uvnitř nebo mimo podnikovou síť s deklaracemi místního zprostředkovatele Identity (IdP). Tento postup můžete použít bez nutnosti spravovat seznam IP adres, které můžou být složité vyhodnocovat a spravovat ve velkých organizacích. V nastavení potřebujete účet pro scénář "roamingu sítě" (uživatel protokolování z interní sítě a při přihlášení přepínače umístění, například v kavárně) a ujistěte se, že chápete důsledky. Další informace: [zabezpečení cloudových prostředků pomocí ověřování Azure Multi-Factor Authentication a AD FS: důvěryhodné IP adresy pro federované uživatele](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Přibližný čas dokončení: 15 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Identifikujte globálního správce, který bude součástí POC PIM | [Začněte používat Azure AD Privileged Identity Management](privileged-identity-management/pim-getting-started.md) |
| Identifikujte globálního správce, který se stane správce zabezpečení | [Začněte používat Azure AD Privileged Identity Management](privileged-identity-management/pim-getting-started.md)<br/> [Různé role pro správu v Azure Active Directory PIM](privileged-identity-management/pim-roles.md) |
| Volitelné: Potvrzení, pokud globální Správci mají přístup k e-mailu k e-mailových oznámení v PIM | [Co je Azure AD Privileged Identity Management?: Konfigurace nastavení aktivace rolí](privileged-identity-management/pim-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přihlaste se k https://portal.azure.com jako globální správce (GA) a bootstrap okno PIM. Globální správce, který provádí tento krok je nasazený jako správce zabezpečení.  Pojmenujme tuto GA1 objektu actor | [Pomocí Průvodce zabezpečení v Azure AD Privileged Identity Management](privileged-identity-management/pim-security-wizard.md) |
| Identifikujte globální správce a přesunout z trvalé na oprávněné. To by měl být samostatný správce, než jaký se používá v kroku 1 pro přehlednost. Pojmenujme tuto GA2 objektu actor | [Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele](privileged-identity-management/pim-how-to-add-role-to-user.md)<br/>[Co je Azure AD Privileged Identity Management?: Konfigurace nastavení aktivace rolí](privileged-identity-management/pim-configure.md#configure-the-role-activation-settings)  |
| Nyní, přihlaste se jako GA2 k https://portal.azure.com a zkuste změnit "Nastavení". Všimněte si, některé možnosti jsou zobrazena šedě. | |
| Na nové kartě a ve stejné relaci v kroku 3 vyhledejte nyní https://portal.azure.com a přidání PIM okno na řídicí panel. | [Zahájení práce s PIM](privileged-identity-management/pim-getting-started.md) |
| Žádost o aktivaci role Globální správce | [Postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management: aktivaci role](privileged-identity-management/pim-how-to-activate-role.md#activate-a-role) |
| Všimněte si, že pokud GA2 se nikdy zaregistrovali pro vícefaktorové ověřování, registrace pro vícefaktorové ověřování Azure bude nutné |  |
| Přejděte zpět na původní kartu v kroku 3 a klikněte na tlačítko Aktualizovat v prohlížeči. Všimněte si, že máte teď přístup k nastavení "uživatel" | |
| Volitelně Pokud globální Správci mají zapnutým e-mailem, můžete zkontrolovat GA1 a GA2 vaší doručené pošty a zobrazit tato zpráva role aktivuje |  |
| 8 zkontrolujte historie auditu a podívejte se, že sestava potvrďte zvýšení GA2 zobrazí. | [Co je Azure AD Privileged Identity Management?: kontrolní aktivita role](privileged-identity-management/pim-configure.md#review-role-activity) |

### <a name="considerations"></a>Požadavky

Tato funkce je součástí Azure AD Premium P2 a/nebo EMS E5

## <a name="discovering-risk-events"></a>Zjišťování rizikových událostí

Přibližný čas dokončení: 20 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Zařízení s prohlížečem Tor, stahování a instalace | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přístup k přihlášení uživatele POC | [Playbook Azure Active Directory Identity Protection](identity-protection/playbook.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Otevřít tor prohlížeče | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přihlaste se k https://myapps.microsoft.com pomocí uživatelského účtu POC | [Playbook Azure Active Directory Identity Protection: simulace rizikových událostí](identity-protection/playbook.md#simulating-risk-events) |
| Počkejte 5-7 minut |  |
| Přihlaste se jako globální správce, aby https://portal.azure.com a otevřete okno Identity Protection | https://aka.ms/aadipgetstarted |
| Otevře se okno rizikové události. Měli byste vidět položku "Přihlášení z anonymních IP adres"  | [Playbook Azure Active Directory Identity Protection: simulace rizikových událostí](identity-protection/playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Požadavky

Tato funkce je součástí Azure AD Premium P2 a/nebo EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Nasazení zásady rizik přihlašování

Přibližný čas dokončení: 10 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Zařízení s prohlížečem Tor, stahování a instalace | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přístup jako uživatele POC protokolu v testování |  |
| Uživatel POC je zaregistrován s MFA. Nezapomeňte použít telefon s dobrým příjem | Stavebním blokem: [ověřování Azure Multi-Factor Authentication s telefonních hovorů](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Přihlaste se jako globální správce, aby https://portal.azure.com a otevře se okno Identity Protection | https://aka.ms/aadipgetstarted |
| Zásady rizik přihlašování povolte následujícím způsobem:<br/>-Přiřazeno: uživatel POC<br/>-Podmínky: Rizika přihlašování střední a vyšší (přihlášení z anonymní umístění se bude považovat za jako střední riziko úroveň)<br/>– Ovládací prvky: Vyžadovat vícefaktorové ověřování | [Playbook Azure Active Directory Identity Protection: riziko přihlášení](identity-protection/playbook.md) |
| Otevřít tor prohlížeče | [Stáhněte si Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Přihlaste se k https://myapps.microsoft.com pomocí uživatelského účtu PoC |  |
| Všimněte si, že ověřovací test MFA | [Přihlašovací prostředí v Azure AD Identity Protection: obnovení riziková přihlášení](identity-protection/flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Požadavky

Tato funkce je součástí Azure AD Premium P2 a/nebo EMS E5. Další informace o rizikových událostech najdete tady: [rizikových událostech Azure Active Directory](reports-monitoring/concept-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Konfigurace ověřování na základě certifikátů

Přibližný čas dokončení: 20 minut

### <a name="pre-requisites"></a>Požadavky

| Předpoklad | Zdroje a prostředky |
| --- | --- |
| Zařízení s uživatelský certifikát zřízení (Windows, iOS nebo Android) z infrastruktury veřejných KLÍČŮ rozlehlé sítě | [Nasazení uživatelských certifikátů](https://msdn.microsoft.com/library/cc770857.aspx) |
| Doména služby Azure AD Federovaná pomocí služby AD FS | [Azure AD Connect a federace](hybrid/how-to-connect-fed-whatis.md)<br/>[Přehled služby Active Directory Certificate Services](https://technet.microsoft.com/library/hh831740.aspx)|
| Pro zařízení s Iosem mají nainstalovanou aplikaci Microsoft Authenticator | [Začínáme s aplikací Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Kroky

| Krok | Zdroje a prostředky |
| --- | --- |
| Povolit "Ověřování certifikátu" na služby AD FS | [Konfigurace zásad ověřování: Globálně konfigurace primární ověřování ve Windows serveru 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Volitelné: Povolení ověřování pomocí certifikátu ve službě Azure AD pro klienty protokolu Exchange Active Sync | [Začínáme s ověřováním pomocí certifikátů v Azure Active Directory](./authentication/active-directory-certificate-based-authentication-get-started.md) |
| Přejděte na Panel přístupu a ověřování pomocí certifikátu uživatele | https://myapps.microsoft.com |

### <a name="considerations"></a>Požadavky

Další informace o upozornění tímto nasazením najdete tady: [služby AD FS: ověření certifikátu s využitím Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Kdo vlastní certifikát uživatele by měly mít ochranu. Buď tím, že spravuje zařízení nebo PIN kód pro případ čipové karty.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
