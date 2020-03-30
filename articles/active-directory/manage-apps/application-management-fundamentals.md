---
title: 'Správa aplikací: Osvědčené postupy a doporučení | Dokumenty společnosti Microsoft'
description: Seznamte se s doporučenými postupy a doporučeními pro správu aplikací ve službě Azure Active Directory. Přečtěte si o používání automatického zřizování a publikování místních aplikací pomocí proxy aplikace.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085288"
---
# <a name="application-management-best-practices"></a>Osvědčené postupy pro správu aplikací
Tento článek obsahuje doporučení a osvědčené postupy pro správu aplikací ve službě Azure Active Directory (Azure AD), pomocí automatického zřizování a publikování místních aplikací pomocí proxy aplikace.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Doporučení pro cloudovou aplikaci a jednotné přihlášení
| Doporučení | Komentáře |
| --- | --- |
| Kontrola aplikací pro aplikace Azure AD  | Azure AD má galerii, která obsahuje tisíce předem integrovaných aplikací, které jsou povolené pomocí enterprise jednotného přihlašování (SSO). Pokyny k nastavení pro konkrétní aplikace najdete v [tématu Seznam výukových programů pro aplikace SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Použití federovaného přiřazuje k samol  | Když to aplikace podporuje, použijte federované, saml založené přihlašované zabezpečení s Azure AD namísto hesla založené přihlašují hesla a ADFS.  | 
| Použití SHA-256 pro podepisování certifikátů  | Azure AD používá algoritmus SHA-256 ve výchozím nastavení k podepsání odpovědi SAML. Sha-256 použijte, pokud aplikace nevyžaduje SHA-1 (viz [Možnosti podepisování certifikátů](certificate-signing-options.md) a [problém s přihlášením k aplikaci](application-sign-in-problem-application-error.md).)  | 
| Vyžadovat přiřazení uživatele  | Ve výchozím nastavení mohou uživatelé přistupovat k podnikovým aplikacím, aniž by jim byly přiřazeny. Pokud však aplikace zpřístupňuje role nebo pokud chcete, aby se aplikace zobrazila na přístupovém panelu uživatele, vyžáděte přiřazení uživatele. (Informace o integraci aplikací naleznete [v pokynech pro vývojáře](developer-guidance-for-integrating-applications.md).)  | 
| Nasazení přístupového panelu Moje aplikace uživatelům | [Přístupový](end-user-experiences.md) panel `https://myapps.microsoft.com` na je webový portál, který poskytuje uživatelům jeden vstupní bod pro jejich přiřazené cloudové aplikace. Při přidávání dalších funkcí, jako je správa skupin a samoobslužné resetování hesla, mohou je uživatelé najít na přístupovém panelu. Viz [Plánování nasazení přístupového panelu](access-panel-deployment-plan.md).
| Použít přiřazení skupiny  | Pokud je zahrnuto v předplatném, přiřaďte skupiny k aplikaci, abyste mohli delegovat průběžnou správu přístupu na vlastníka skupiny. (Informace o integraci aplikací naleznete [v pokynech pro vývojáře](developer-guidance-for-integrating-applications.md).)   | 
| Vytvoření procesu správy certifikátů | Maximální životnost podpisového certifikátu je tři roky. Chcete-li zabránit nebo minimalizovat výpadek z důvodu vypršení platnosti certifikátu, použijte role a seznamy distribuce e-mailů, abyste zajistili, že oznámení o změnách souvisejících s certifikátem jsou pečlivě sledována. |

## <a name="provisioning-recommendations"></a>Doporučení pro zřizování
| Doporučení | Komentáře |
| --- | --- |
| Nastavení zřizování pomocí cloudových aplikací pomocí kurzů | Podrobné pokyny ke konfiguraci zřizování pro aplikaci galerie, kterou chcete přidat, najdete v [kurzech aplikace Seznam SaaS.](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) |
| Použití protokolů zřizování (náhled) ke sledování stavu | [Protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) poskytují podrobnosti o všech akcích prováděných službou zřizování, včetně stavu pro jednotlivé uživatele. |
| Přiřazení distribuční skupiny k e-mailu s oznámením zřizování | Chcete-li zvýšit viditelnost kritických výstrah odeslaných zřizovací službou, přiřaďte distribuční skupinu k nastavení E-maily s oznámením. |


## <a name="application-proxy-recommendations"></a>Doporučení proxy aplikace
| Doporučení | Komentáře |
| --- | --- |
| Použití proxy aplikace pro vzdálený přístup k interním prostředkům | Proxy aplikace se doporučuje pro poskytování přístupu vzdáleným uživatelům k interním prostředkům a nahrazení potřeby VPN nebo reverzního proxy serveru. Není určen pro přístup k prostředkům z podnikové sítě, protože může přidat latenci.
| Použití vlastních domén | Nastavte vlastní domény pro vaše aplikace (viz [Konfigurace vlastních domén),](application-proxy-configure-custom-domain.md)aby adresy URL pro uživatele a mezi aplikacemi fungovaly z vaší sítě i zvnějšku. Budete také moci ovládat svou značku a přizpůsobit adresy URL.  Při použití vlastních názvů domén plánujete získat veřejný certifikát od důvěryhodné certifikační autority jiného typu než Microsoft. Azure Application Proxy podporuje standardní ([zástupný znak](application-proxy-wildcard.md)) nebo certifikáty založené na protokolu SAN. (Viz [Plánování proxy aplikace](application-proxy-deployment-plan.md).) |
| Synchronizace uživatelů před nasazením proxy aplikace | Před nasazením proxy aplikace synchronizujte identity uživatelů z místního adresáře nebo je vytvořte přímo ve službě Azure AD. Synchronizace identit umožňuje Azure AD předem ověřit uživatele před udělením přístupu k aplikacím publikovaným proxy aplikací. Poskytuje také informace o identifikátoru uživatele potřebné k provedení jednotného přihlašování (SSO). (Viz [Plánování proxy aplikace](application-proxy-deployment-plan.md).) |
| Postupujte podle našich tipů pro vysokou dostupnost a vyrovnávání zatížení | Informace o tom, jak toky provozu mezi uživateli, konektory proxy aplikací a servery back-end aplikací a tipy pro optimalizaci výkonu a vyrovnávání zatížení, naleznete v [tématu Vysoká dostupnost a vyrovnávání zatížení konektorů a aplikací proxy aplikací](application-proxy-high-availability-load-balancing.md). |
| Použití více konektorů | Použijte dva nebo více konektorů proxy aplikací pro větší odolnost, dostupnost a škálování (viz [Konektory proxy aplikace).](application-proxy-connectors.md) Vytvořte skupiny konektorů a ujistěte se, že každá skupina konektorů má alespoň dva konektory (tři konektory jsou optimální). |
| Vyhledejte servery konektorů v blízkosti aplikačních serverů a ujistěte se, že jsou ve stejné doméně | Chcete-li optimalizovat výkon, fyzicky vyhledejte konektorový server v blízkosti aplikačních serverů (viz [Důležité informace o topologii sítě](application-proxy-network-topology.md)). Servery konektorových serverů a webových aplikací by také měly patřit do stejné domény služby Active Directory nebo by měly span trusting doménami. Tato konfigurace je vyžadována pro přihlašování s integrovaným ověřováním systému Windows (IWA) a delegováním s vazbou protokolu Kerberos (KCD). Pokud jsou servery v různých doménách, budete muset použít delegování založené na prostředcích pro jednotné přihlašování (viz [KCD pro jednotné přihlašování pomocí proxy aplikace).](application-proxy-configure-single-sign-on-with-kcd.md) |
| Povolení automatických aktualizací pro konektory | Povolte automatické aktualizace konektorů pro nejnovější funkce a opravy chyb. Společnost Microsoft poskytuje přímou podporu pro nejnovější verzi konektoru a jednu verzi před. (Viz [Historie verzí aplikace Proxy](application-proxy-release-version-history.md).) |
| Obejít místní proxy server | Pro snadnější údržbu nakonfigurujte konektor tak, aby obcoval místní proxy server, aby se přímo připojil ke službám Azure. (Viz [Konektory aplikačního proxy a proxy serverů](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Použití proxy aplikace Azure AD přes proxy webové aplikace | Použijte proxy aplikace Azure AD pro většinu místních scénářů. Proxy webové aplikace je upřednostňovaný jenom ve scénářích, které vyžadují proxy server pro službu AD FS a kde ve službě Azure Active Directory nelze používat vlastní domény. (Viz [Migrace proxy aplikace](application-proxy-migration.md).) |
