---
title: Implementace služby Azure Active Directory PoC Playbook | Microsoft Docs
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
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 2f01bdee5ffcc9e2f48278c7209da40ef64cc030
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Ověření služby Azure Active Directory koncept Playbook: implementace

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation – synchronizace služby AD do Azure AD 

Hybridní identita je základem pro většinu podnikových zákazníků, kteří již mají místního adresáře. Cílem je záměrně zatěžovat jako méně času zde jako umožňuje zobrazit hodnotu skutečné scénáře přístupu a identit. 

| Scénář | Stavební bloky| 
| --- | --- |  
| [Rozšíření vaší identity místně do cloudu](#extending-your-on-premises-identity-to-the-cloud) | [Synchronizace adresáře – synchronizace hodnot Hash hesel](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Poznámka:**: Pokud už máte DirSync nebo ADSync nebo dřívější verze služby Azure AD Connect, tento krok je volitelný. Některé scénáře v této příručce může vyžadovat novější verze služby Azure AD Connect.  <br/>[Branding](active-directory-playbook-building-blocks.md#branding) | 
| [Přiřazování licencí Azure AD pomocí skupin](#assigning-azure-ad-licenses-using-groups) | [Na základě skupiny licencí](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Rozšíření vaší identity místně do cloudu 

1. Bob se správce Active Directory společnosti Contoso. Získá zadá požadavek na povolit identity jako služba pro sadu uživatelů. Po spuštění Průvodce Azure AD Connect, identity cíloví uživatelé, který je k dispozici v cloudu. 
2. Bob zobrazí Susie, jeden z cílových uživatelů přístupový panel Azure Active Directory a potvrďte, že uživatel provést ověření. Susie uvidí partnerské přihlašovací stránku a prázdný přístupový panel je připravené pro povolení přístupu budoucí aplikace.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Přiřazování licencí Azure AD pomocí skupin 

1. Bob se Azure AD globálního správce a chce přidělit licence Azure AD pro konkrétní skupinu uživatelů jako součást počáteční zavedení služby Azure AD.
2. Bob vytvoří skupinu pro uživatele pilotního nasazení. 
3. Bob přiřadí do skupiny licencí
4. Susie, jeden z informační pracovníci, se přidá do skupiny zabezpečení v rámci jeho pracovní funkce
5. Po určité době Susie má přístup k licencí Azure AD premium. Tím povolíte více scénářů POC později.

## <a name="theme---lots-of-apps-one-identity"></a>Motiv – velký počet aplikací, jednu identitu

| Scénář | Stavební bloky| 
| --- | --- |  
| [Integrace aplikace SaaS - federovaného jednotného přihlašování](#integrate-saas-applications---federated-sso) | [Konfigurace SaaS federovaného jednotného přihlašování](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Skupin, delegovat vlastnictví](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integrace aplikace SaaS – heslo jednotného přihlašování](#integrate-saas-applications---password-sso) | [Konfigurace jednotného přihlašování k SaaS heslo](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [Jednotné přihlašování a události životního cyklu Identity](#sso-and-identity-lifecycle-events) | [SaaS a životního cyklu Identity](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Zabezpečený přístup k sdílené účty](#secure-access-to-shared-accounts) | [SaaS sdílenou konfiguraci účtů](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Zabezpečený vzdálený přístup k místním aplikacím](#secure-remote-access-to-on-premises-applications) | [Konfigurace Proxy aplikace](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Synchronizace identit LDAP do služby Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Obecná konfigurace konektoru LDAP](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integrace aplikace SaaS - federovaného jednotného přihlašování 

1. Bob se Azure AD globálního správce a obdrží žádost z oddělení marketingu, abyste umožnili přístup k jejich Instance ServiceNow. Bob vyhledá podrobný kurz v dokumentaci k Azure AD a následujícího a delegaci přiřazení uživatelů na aplikaci na kevina, head Marketing týmu. 
2. Kevina přihlásí jako vlastník ServiceNow oprávnění a přiřadí Susie do aplikace. Kevina také oznámení, že profil Susie je automaticky vytvořen v ServiceNow
3. Susie je pracovník s informacemi z marketingového oddělení. Uživatel přihlásí do služby azure AD a vyhledá všechny aplikace SaaS, která je přiřazena k myapps portálu. Z tohoto místa se bezproblémově získá přístup k ServiceNow.
4. Marketingové oddělení chce audit, kdo měl přístup ServiceNow. Bob stáhne zprávu o činnosti a sdílet je s kevina prostřednictvím e-mailu.  

### <a name="sso-and-identity-lifecycle-events"></a>Jednotné přihlašování a události životního cyklu Identity

1. Susie trvá s absencí a podnikové zásady místní AD účet je dočasný zakázáno. Susie nyní nemůže přihlásit k Azure AD a proto nemůže přistupovat k ServiceNow. 
2. Susie provede laterální přesunu z marketingového oddělení prodeje. Kevina odebere ServiceNow přístup. Susie přihlásí myapps azure ad a Jana už se zobrazí na dlaždici ServiceNow. Po 10 minutách kevina potvrdí, že Susie účet byl zakázán z konzoly pro správu ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Integrace aplikace SaaS – heslo jednotného přihlašování

1. Bob nakonfiguruje přístup k Atlassian HipChat. HipChat má heslo jednotného přihlašování k integraci a udělit přístup k Susie
2. Susie přihlásí k portálu myapps a zobrazí odkaz ke stažení rozšíření prohlížeče Azure AD IE, které se soubory ke stažení
3. Po kliknutí na tlačítko, uživatel získá vyzváni k zadání pověření jeho HipChat uživatelské jméno a heslo. Jedná se o jednorázovou operaci, a po dokončení ho uživatel má přístup k HipChat
4. Několik dní později Susie otevře portál myapps a klikne na tlačítko HipChat znovu. Tento čas přibližně uživatel získá bezproblémový přístup
5. Kevina, vlastník HipChat aplikace chce audit, kdo měl přístup k aplikaci. Bob stáhne zprávu o auditu a sdílet je s kevina prostřednictvím e-mailu. 

### <a name="secure-access-to-shared-accounts"></a>Zabezpečený přístup k sdílené účty 

1. Bob se musí k zabezpečení sdílené Twitter popisovač pro členy týmu pro prodej. Mu přidá Twitter jako aplikace jednotného přihlašování a přiřadí ji k skupinu zabezpečení prodejní tým. Dostal přihlašovací údaje ke sdílenému účtu a mu poskytuje v systému. 
2. Sdílení Twitter pověření je již důvěryhodný kvůli více uživatelů vědomí. Bob umožňuje automatickou výměnu hesla služby Twitter.
3. Susie, člen týmu pro prodej, přihlásí k portálu myapps a zobrazí odkaz ke stažení rozšíření prohlížeče Azure AD IE. Jana nainstaluje.
4. Po kliknutí na tlačítko uživatel získat přístup přímo do služby Twitter. Jana neví heslo.
5. Arnold je také součástí prodejního týmu. Má stejné prostředí jako Susie v krocích 3 4
6. Oddělení prodeje chce audit, kdo měl přístup Twitter. Bob stáhne zprávu o činnosti a sdílet je s kevina prostřednictvím e-mailu. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Zabezpečený vzdálený přístup k místním aplikacím

1. Bob, Azure AD globálního správce, obdržel množství požadavků zaměstnancům přístup k několika užitečné místních prostředků, jako je například aplikace výdaje při práci vzdáleně. Mu odpovídá [Proxy aplikace dokumentaci](manage-apps/application-proxy-enable.md) k instalaci konektoru a publikovat výdaje jako aplikaci Proxy aplikace. 
2. Bob sdílet externí adresu URL aplikace výdaje s Susie, některého zaměstnance, kteří musí vzdáleného přístupu. Uživatel přistupuje k propojení a po ověření proti AAD, uživatel může pro přístup k aplikaci výdaje a pokračovat k dosažení produktivity. při vzdálené. 
3. Bob potom pokračuje publikování dalších místních aplikací pomocí stejný postup a udělení přístupu uživatelům podle potřeby. Přidá podmíněného přístupu a vícefaktorového ověřování pro citlivější aplikace, které mu publikuje zajistit další bezpečnostní.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Synchronizace identit LDAP do služby Azure AD

1. Bob společnost má infrastruktury komplexní identit. Většina uživatelů jsou zachována uvnitř Windows Server Active Directory Domain Services (přidá). Některé z nich, jsou spravovány systémem HR uvnitř Active Directory Lightweight Directory Services (ADLDS).
2. Bob za úkol povolení přístupu k aplikacím SaaS pro všechny uživatele (taky tyto není k dispozici v přidá).
3. Bob nakonfiguruje obecné konektor LDAP k získání dat z ADLDS v Azure AD Connect.
4. Bob vytvoří synchronizační pravidla, takže uživatelé LDAP se vyplní do úložiště Metaverse a do Azure AD
5. Synchronizované identity Susie se, že LDAP uživatel přistupuje k jeho pomocí aplikace SaaS



> [!IMPORTANT] 
> Toto je pokročilou konfiguraci nutnosti některé znalost FIM nebo MIM. Pokud se používá v produkčním prostředí, doporučujeme, aby dotazy týkající se tato konfigurace projít [Premier Support](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Motiv – zvýšení zabezpečení 

| Scénář | Stavební bloky| 
| --- | --- |  
| [Přístup k účtu správce zabezpečení](#secure-administrator-account-access) | [Azure MFA s telefonní hovory](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Zabezpečit přístup k aplikacím](#secure-access-to-applications) | [Podmíněný přístup pro aplikace SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Povolit jenom v době správy](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Ochrana identit podle rizik](#protect-identities-based-on-risk) | [Zjišťování rizikových událostí](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Nasazení zásad riziko přihlášení](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Ověřit bez hesla pomocí ověřování na základě certifikátů](#authenticate-without-passwords-using-certificate-based-authentication) | [Konfigurace ověřování na základě certifikátů](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Přístup k účtu správce zabezpečení

1. Robert je globální správce Azure AD. Zadá označila Stanislav jako spolusprávce služby. 
2. Bob nakonfiguruje účet na Stanislav vždy vyžadovat vícefaktorové ověřování pro zlepšení postavení zabezpečení
3. Stanislav přihlásí k portálu Azure a oznámení, které mu potřebuje k registraci jeho telefonní číslo a pokračujte v přihlášení
4. Další přihlášení z Stanislav jsou teď chráněné pomocí služby Multi-Factor Authentication a mohl nyní získá telefonní hovor ověřit svou identitu.

### <a name="secure-access-to-applications"></a>Zabezpečení přístupu k aplikacím

1. Kevina je vlastníkem obchodní ServiceNow. Společnost chce teď uživatelům se přihlásit pomocí vícefaktorového ověřování při přístupu k mimo podnikovou síť.
2. Bob, naše globální správce Azure AD přidá zásady podmíněného přístupu do aplikace ServiceNow jak zapnout MFA pro mimo přístup
3. Susie naše pracovník protokoly portálu Moje aplikace a kliknutím na dlaždici ServiceNow. Uživatel je nyní postiženy pomocí vícefaktorového ověřování.

### <a name="enable-just-in-time-jit-administration"></a>Povolit pouze v části Správa času (JIT)

1. Bob a Stanislav jsou globální správce Azure AD. Chtějí k povolení JIT přístup k role správy a zachovat záznamy na použití privilegované role.
2. Bob umožňuje PIM v klientovi Azure AD a že bude správce zabezpečení. Změní sám a členství v roli globálního správce pro Stanislava z trvalé na vhodné.
3. Bob a Stanislav teď vyžadují aktivaci jejich role prostřednictvím portálu Azure před provedením změny do konfigurace služby Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Ochrana identit podle rizik 

1. Susie, pokusy pracovník s informacemi o přihlášení z prohlížeče tor. 
2. Bob ověří řídicí panel Azure AD identity protection a zobrazí Susie na přihlášení z anonymních IP adresu. Zabezpečení tým chce výzvu, která uživatelé takových přistupuje pomocí vícefaktorového ověřování
3. Bob umožňuje zásady Azure AD Identity Protection vyzve vícefaktorového ověřování pro střední a vyšší riziko události
4. Čas přejde a Susie přihlásí z prohlížeče Tor znovu. Této doby, uživatel uvidí ověřovací test MFA

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Ověřit bez hesla pomocí ověřování na základě certifikátů

1. Robert je globální správce pro finanční instituce, která zakazuje použití hesel jako faktor ověřování pro své aplikace.
2. Bob umožňuje a vynucuje ověřování certifikátů na služby AD FS a Azure AD
3. Susie při přístupu k aplikaci je výzva k ověření pomocí certifikátu

## <a name="theme---scale-with-self-service"></a>Motiv – škálování s Self Service

| Scénář | Stavební bloky| 
| --- | --- |  
| [Samoobslužné resetování hesla](#self-service-password-reset) | [Samoobslužné resetování hesla](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Samoobslužné přístup k aplikacím](#self-service-access-to-applications) | [Samoobslužné přístup k aplikacím](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Samoobslužné resetování hesla 

1. Robert je globální správce Azure AD a umožňuje správu hesel samoobslužné služby podmnožině uživatelů, včetně Susie. 
2. Protokoly Susie v myapps portál a najdete zprávu zaregistrovat svůj informace o zabezpečení pro budoucí heslo resetovat události.
3. Rychlé dál několik dní Susie zapomene své heslo a obnoví prostřednictvím portálu Azure AD

### <a name="self-service-access-to-applications"></a>Samoobslužné přístup k aplikacím 

1. Kevina je vlastníkem obchodní ServiceNow. Chce uživatelům "zaregistrujte si" jej na vyžádání, místo přidávání je všechny najednou
2. Bob, naše globální správce Azure AD, upraví ServiceNow aplikace můžete povolit samoobslužné žádosti
3. Susie naše pracovník protokoly Moje aplikace portálu a klikne na tlačítko "Přidat další aplikace" a najdete v části ServiceNow jako jeden z doporučené aplikace. Jana poté přejde zpět na portál Moje aplikace a vidět ServiceNow aplikaci.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]