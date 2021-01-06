---
title: Zabezpečený hybridní přístup k Azure AD s F5 | Microsoft Docs
description: F5 přístup správce zásad pro BIG-IP Access a Azure Active Directory Integration pro zabezpečený hybridní přístup
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5c960c7fbcc29d0aaea7511ba2187c916e84ab3
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935236"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>F5 přístup správce zásad pro BIG-IP Access a Azure Active Directory Integration pro zabezpečený hybridní přístup

Navýšení mobility a vyvíjející se hrozby v krajině znamená dodatečné kontroly přístupu k prostředkům a zásad správného řízení, vložení [nulového vztahu důvěryhodnosti](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) a středu všech moderních programů.
V Microsoftu a F5 si uvědomujeme, že tato digitální transformace je většinou jednoletá cesta k libovolné firmě, která může vystavovat důležité prostředky vystavené až do modernizace. Genesis za F5 BIG-IP a Azure Active Directory Secure Hybrid Access (SHA) se zaměřuje nejen na zlepšení vzdáleného přístupu k místním aplikacím, ale také při posílení celkového stav zabezpečení těchto ohrožených služeb.

V kontextu výzkumu se odhaduje, že 60-80% místních aplikací je starší než v podstatě, jinak řečeno, které nepodporují integraci přímo s Azure Active Directory (AD). Stejná studie také označila velký podíl těchto systémů na nižších verzích SAP, Oracle, společnost Sage a dalších dobře známých úlohách, které poskytují důležité služby.

Služba SHA adresuje toto nevidomé místo tím, že umožňuje organizacím nadále používat investice F5 pro zajištění vynikajících síťových a aplikačních dodávek. V kombinaci s Azure AD přemostění heterogenní aplikace na šířku s využitím moderní roviny řízení identit.

Přístup k předběžným ověřením Azure AD publikovaným službám BIG-IP přináší spoustu výhod:

- Ověřování bez hesla prostřednictvím klíčů [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install), [Fast identity online (Fido)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)a [ověřování na základě certifikátu](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)

- Nepodmíněný [přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) a [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

- [Ochrana identity](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#:~:text=Identity%20Protection%20is%20a%20tool%20that%20allows%20organizations,detection%20data%20to%20third-party%20utilities%20for%20further%20analysis) – Adaptivní řízení prostřednictvím profilace rizik uživatelů a relací

- [Zjištění nevrácených přihlašovacích údajů](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

- [Samoobslužné resetování hesla (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr)

- [Spolupráce s partnery](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users) – Správa nároků pro řízení přístupu k hostům

- [Cloud App Security (CASB)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) – pro kompletní zjišťování a řízení aplikací

- Monitorování hrozeb – [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) pro Advanced Threat Analytics

- [Portál Azure AD](https://azure.microsoft.com/features/azure-portal/) – jediná Řídicí rovina pro řízení identity a přístupu

## <a name="scenario-description"></a>Popis scénáře

Systém BIG-IP Service jako řadič pro doručování aplikací (ADC) a SSL-VPN poskytuje místní a vzdálený přístup ke všem typům služeb, včetně:

- Moderní a starší webové aplikace

- Aplikace nevyužívající Web

- Služby webového rozhraní API REST a SOAP

Jeho místní Traffic Manager (LTM) umožňuje zabezpečené publikování služeb prostřednictvím funkce reverzního proxy serveru. Promyšlený správce zásad přístupu (APM) ve stejnou chvíli rozšiřuje velkou IP adresu s bohatou sadou funkcí a povoluje federaci a jednotné přihlašování (SSO).

Integrace je založená na standardní federaci federace mezi APM a Azure AD, které jsou běžné pro většinu případů použití algoritmu SHA, které zahrnují [scénář SSL-VPN](f5-aad-password-less-vpn.md). Prostředky Security Assertion Markup Language (SAML), OAuth a Open ID Connect (OIDC) nejsou výjimkou, protože je lze zabezpečit pro vzdálený přístup. V některých případech se může stát, že se u služby BIG-IP pro přístup nulového vztahu důvěryhodnosti ke všem službám, včetně aplikací SaaS, přesytič.

Možnost integrace se službou Azure AD s velkou IP adresou umožňuje integraci protokolu, která je nutná pro zabezpečení starších nebo neazure integrovaných služeb s moderními ovládacími prvky, jako je například [ověřování bez hesla](https://www.microsoft.com/security/business/identity/passwordless) a [podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). V tomto scénáři i služba BIG-IP nadále plní svou roli jako reverzní proxy server, přičemž při použití předběžného ověřování a autorizace do služby Azure AD je k dispozici na jednotlivých službách.

Kroky 1-4 v diagramu znázorňují front-end výměnu před ověřením mezi uživatelem, velkými IP adresami a Azure AD v toku aktivovaném poskytovatelem služby. Kroky 5-6 ukazují následné rozšíření relace APM a jednotné přihlašování pro jednotlivé služby back-end.

![Obrázek znázorňuje architekturu na vysoké úrovni](./media/f5-aad-integration/integration-flow-diagram.png)

| Krok | Popis |
|:------|:-----------|
| 1. | Uživatel vybere ikonu aplikace na portálu a překládá adresu URL na SAML SP (BIG-IP). |
| 2. | Služba BIG-IP přesměruje uživatele na SAML IDP (Azure AD) pro předběžné ověření.|
| 3. | Azure AD zpracovává zásady podmíněného přístupu a [ovládací prvky relace](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) pro autorizaci.|
| 4. | Přesměruje uživatele zpět na BIG-IP, který prezentuje deklarace SAML vydané službou Azure AD. |
| 5. | BIG-IP žádosti o další informace o relacích, které se mají zahrnout do služby [SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) a [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) do publikované služby |
| 6. | BIG-IP přepošle požadavek klienta na back-end službu.

## <a name="user-experience"></a>Uživatelské prostředí

Bez ohledu na to, jestli je už přímý zaměstnanec, přidružená nebo spotřebitel, se s přihlášením k Office 365 už seznámili většina uživatelů, takže přístup ke službám BIG-IP přes SHA zůstane v podstatě známý.

Uživatelé teď naleznou své cloudové služby s velkou IP adresou konsolidovanou v sestavách  [MyApp](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) nebo [O365](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) spolu s funkcemi samoobslužné služby pro širší sadu služeb bez ohledu na typ zařízení nebo umístění. Uživatelé mohou dokonce i nadále přistupovat k publikovaným službám přímo prostřednictvím BIG-IPs proprietárního portálu Webtop, pokud jsou upřednostňovány. Když se odhlásíte, služba SHA zajišťuje ukončení relace uživatelů na obou koncích, velkých a koncových IP adres a Azure AD, což zajišťuje, že se služby budou plně chránit před neoprávněným přístupem.  

Dodávané snímky obrazovky jsou z portálu aplikace Azure AD, ke kterým uživatelé přistupují zabezpečeným způsobem, aby našli své publikované služby s velkými IP adresami a spravovaly jejich vlastnosti účtu.  

![Snímek obrazovky se zobrazí v galerii Woodgrove myapps](media/f5-aad-integration/woodgrove-app-gallery.png)

![Snímek obrazovky zobrazuje samoobslužnou stránku Woodgrove myaccounts](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Přehledy a analýzy

Role BIG-IP je pro každou firmu zásadní, takže nasazené instance s velkými IP adresami by měly být monitorovány, aby byly publikované služby vysoce dostupné, a to jak na úrovni SHA, tak i i v provozu.

K dispozici je několik možností pro protokolování událostí buď místně, nebo vzdáleně prostřednictvím řešení SIEM (Security Information and Event Management), které umožňuje ukládání mimo špičku a zpracovávání telemetrie. Vysoce efektivní řešení pro monitorování aktivit služby Azure AD a SHA, je použití [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) a [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview), společně nabízí:

- Podrobný přehled vaší organizace, potenciálně v různých cloudech a místních umístění, včetně infrastruktury BIG-IP

- Jednoduchá Řídicí rovina, která poskytuje kombinovaný pohled na všechny signály, zabránění spoléhání na složité a různorodé nástroje

![Obrázek ukazuje tok monitorování](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Požadavky

Integrace aplikace F5 BIG-IP s Azure AD pro službu SHA má následující požadavky:

- Instance BIG-IP F5 spuštěná na jedné z následujících platforem:

  - Fyzické zařízení

  - Virtuální edice hypervisoru, například Microsoft Hyper-V, VMware ESXi, Linux KVM a Citrix hypervisor

  - Cloudová virtuální edice, jako je Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, OpenStack a Google Cloud

    Skutečným umístěním instance BIG-IP může být místní nebo libovolná podporovaná cloudová platforma, včetně Azure, za předpokladu, že má připojení k Internetu, publikované prostředky a všechny další požadované služby, jako je Active Directory.  

- Aktivní licence pro APM BIG-IP APM prostřednictvím jedné z následujících možností:

   - ® Nejlepší sada pro BIG-IP

   - F5 pro správce zásad BIG-IP Access Manager™ samostatnou licenci

   - F5™ licence pro přístup správce zásad pro Big-IP Access Manager (APM) na stávající® místní Traffic Manager™ (LTM)

   - [Zkušební licence](https://www.f5.com/trial/big-ip-trial.php) ™ (APM) pro správce cloudového přístupu na základě Big IP adres 90

- Licencování Azure AD prostřednictvím jedné z následujících možností:

   - [Bezplatné předplatné](https://docs.microsoft.com/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) Azure AD poskytuje minimální základní požadavky pro implementaci služby SHA s ověřováním bez hesla.

   - [Předplatné Premium](https://azure.microsoft.com/pricing/details/active-directory/) poskytuje celou řadu dalších hodnot, včetně [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)a [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) .

K implementaci algoritmu SHA je potřeba žádné předchozí zkušenosti ani znalostní báze F5 s velkým objemem IP adres, ale doporučujeme, abyste familiarizingi pomocí terminologie F5 BIG-IP. F5's bohatou [znalostní bázi](https://www.f5.com/services/resources/glossary) je také dobrým místem, kde můžete začít sestavovat znalosti ve velkém protokolu IP.

## <a name="deployment-scenarios"></a>Scénáře nasazení

V následujících kurzech najdete podrobné pokyny k implementaci některých nejběžnějších vzorů pro BIG-IP a Azure AD SHA:

- [Názor F5 BIG-IP v nasazení Azure – Projděte si](f5-bigip-deployment-guide.md)

- [Aplikace F5 BIG-IP APM a Azure AD SSO k aplikacím Kerberos](https://docs.microsoft.com/azure/active-directory/saas-apps/kerbf5-tutorial#configure-f5-single-sign-on-for-kerberos-application)

- [Služba F5 BIG-IP APM a služba Azure AD SSO pro aplikace založené na hlavičkách](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial#configure-f5-single-sign-on-for-header-based-application)

- [Zabezpečení F5 BIG-IP SSL-VPN pomocí Azure AD SHA](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Další zdroje

- [Konec hesel, přejít bez hesla](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory zabezpečený hybridní přístup](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Microsoft Zero Trust Framework umožňující vzdálenou práci](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Začínáme s Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Další kroky

Zvažte spuštění služby SHA (ověření koncepce) pomocí existující infrastruktury pro velké IP adresy nebo nasazením zkušební instance. [Nasazení virtuálního počítače s velkou IP adresou (ve verzi) do Azure](f5-bigip-deployment-guide.md) trvá přibližně 30 minut. v takovém případě budete mít:

- Plně zabezpečená platforma pro modelování zkušebního konceptu SHA

- Předprodukční instance, plně zabezpečená platforma, která se používá k testování nových aktualizací systému BIG-IP a oprav hotfix

Ve stejnou chvíli byste měli určit jednu nebo dvě aplikace, které je možné cílit na publikování prostřednictvím služby BIG-IP a ochrany pomocí algoritmu SHA.  

Naším doporučením je začít s aplikací, která ještě není publikovaná přes velkou IP adresu, takže se vyhnete možnému výpadku produkčních služeb. Pokyny uvedené v tomto článku vám pomůžou seznámit se s obecným postupem pro vytváření různých objektů konfigurace BIG-IP a nastavení SHA. Jakmile budete hotovi, měli byste být schopni provést stejnou akci s jinými novými službami a také mít dostatek informací, aby bylo možné převést stávající publikované služby s velkým protokolem IP na službu SHA s minimálním úsilím.

Následující interaktivní příručka vás provede nejdůležitějším postupem pro implementaci služby SHA a zobrazení činnosti koncového uživatele.

[![Obrázek znázorňuje interaktivní vodítko pokrytí](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
