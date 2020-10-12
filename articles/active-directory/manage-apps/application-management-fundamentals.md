---
title: 'Správa aplikací: osvědčené postupy a doporučení | Microsoft Docs'
description: Naučte se osvědčené postupy a doporučení pro správu aplikací v Azure Active Directory. Přečtěte si, jak používat Automatické zřizování a publikování místních aplikací pomocí proxy aplikací.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: d32728c1f388e9013b922d1f60d30e65d350bbc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642431"
---
# <a name="application-management-best-practices"></a>Osvědčené postupy pro správu aplikací

Tento článek obsahuje doporučení a osvědčené postupy pro správu aplikací v Azure Active Directory (Azure AD), použití automatického zřizování a publikování místních aplikací pomocí proxy aplikací.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Doporučení pro cloudovou aplikaci a jednotné přihlašování
| Doporučení | Komentáře |
| --- | --- |
| Podívejte se na galerii aplikací Azure AD pro aplikace  | Azure AD obsahuje galerii obsahující tisíce předem integrovaných aplikací, které jsou povolené s podnikovým jednotným přihlašováním (SSO). Pokyny k instalaci pro konkrétní aplikaci najdete v [seznamu výukových kurzů](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)pro aplikace SaaS.  | 
| Použití federovaného jednotného přihlašování založené na SAML  | Když ji aplikace podporuje, použijte federované jednotné přihlašování založené na SAML s Azure AD místo jednotného přihlašování založeného na heslech a ADFS.  | 
| Použití SHA-256 pro podepsání certifikátu  | Služba Azure AD ve výchozím nastavení používá algoritmus SHA-256 k podepsání odpovědi SAML. Použijte SHA-256, pokud aplikace nevyžaduje SHA-1 (viz [možnosti podepisování certifikátů](certificate-signing-options.md) a [problém s přihlášením aplikací](application-sign-in-problem-application-error.md).)  | 
| Vyžadovat přiřazení uživatele  | Ve výchozím nastavení mají uživatelé přístup k podnikovým aplikacím bez jejich přiřazení. Pokud však aplikace zveřejňuje role nebo pokud chcete, aby se aplikace zobrazila na mých aplikacích uživatele, vyžadovat přiřazení uživatele. (Další informace najdete v tématu [pokyny pro vývojáře při integraci aplikací](developer-guidance-for-integrating-applications.md).)  | 
| Nasazení mých aplikací pro uživatele | [Moje aplikace](end-user-experiences.md) na `https://myapps.microsoft.com` webu je webový portál, který uživatelům poskytuje jediný bod záznamu pro své přiřazené cloudové aplikace. V případě přidání dalších možností, jako je Správa skupin a Samoobslužné resetování hesla, se uživatelé můžou v mých aplikacích najít. Viz [Plánování nasazení mých aplikací](access-panel-deployment-plan.md).
| Použít přiřazení skupiny  | Pokud je součástí vašeho předplatného, přiřaďte skupiny k aplikaci, abyste mohli delegovat průběžnou správu přístupu na vlastníka skupiny. (Další informace najdete v tématu [pokyny pro vývojáře při integraci aplikací](developer-guidance-for-integrating-applications.md).)   | 
| Vytvoření procesu pro správu certifikátů | Maximální doba života podpisového certifikátu je tři roky. Aby se zabránilo výpadku nebo minimalizoval výpadek z důvodu vypršení platnosti certifikátu, použijte k zajištění pečlivě monitorovaných oznámení o změnách souvisejících s certifikátem. |

## <a name="provisioning-recommendations"></a>Doporučení pro zřizování
| Doporučení | Komentáře |
| --- | --- |
| Použití kurzů k nastavení zřizování pro cloudové aplikace | Podrobné pokyny ke konfiguraci zřizování pro aplikaci Galerie, kterou chcete přidat, najdete v [seznamu kurzů aplikací SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) . |
| Ke sledování stavu použijte protokoly zřizování (Preview). | [Protokoly zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) poskytují podrobné informace o všech akcích prováděných službou zřizování, včetně stavu pro jednotlivé uživatele. |
| Přiřazení distribuční skupiny k e-mailu s oznámením zřizování | Chcete-li zvýšit viditelnost kritických výstrah odesílaných službou zřizování, přiřaďte distribuční skupinu k nastavení e-mailů s oznámením. |


## <a name="application-proxy-recommendations"></a>Doporučení pro proxy aplikace
| Doporučení | Komentáře |
| --- | --- |
| Použít proxy aplikace pro vzdálený přístup k interním prostředkům | Proxy aplikací se doporučuje pro poskytování přístupu vzdálených uživatelů k interním prostředkům, což nahrazuje potřebu sítě VPN nebo reverzního proxy serveru. Není určený pro přístup k prostředkům v rámci podnikové sítě, protože by mohla přidat latenci.
| Použití vlastních domén | Nastavte vlastní domény pro vaše aplikace (viz [Konfigurace vlastních domén](application-proxy-configure-custom-domain.md)), aby adresy URL pro uživatele a mezi aplikacemi fungovaly buď v rámci vaší sítě, nebo mimo ni. Budete také moci řídit branding a přizpůsobit své adresy URL.  Pokud používáte vlastní názvy domén, naplánujte získání veřejného certifikátu od důvěryhodné certifikační autority od jiného subjektu než Microsoftu. Azure Application proxy podporuje certifikáty založené na standardu, ([zástupné znaky](application-proxy-wildcard.md)) nebo San. (Viz [plánování aplikačního proxy serveru](application-proxy-deployment-plan.md).) |
| Synchronizovat uživatele před nasazením proxy aplikací | Před nasazením proxy aplikací synchronizujte identity uživatelů z místního adresáře nebo je vytvořte přímo ve službě Azure AD. Synchronizace identity umožňuje službě Azure AD předem ověřit uživatele předtím, než jim udělí přístup k aplikacím publikovaným proxy aplikací. Poskytuje taky potřebné informace o uživatelském identifikátoru k provedení jednotného přihlašování (SSO). (Viz [plánování aplikačního proxy serveru](application-proxy-deployment-plan.md).) |
| Postupujte podle našich tipů pro zajištění vysoké dostupnosti a vyrovnávání zatížení | Pokud se chcete dozvědět, jak přenos toků mezi uživateli, konektory proxy aplikací a back-endové aplikační servery, a získáte tipy pro optimalizaci výkonu a vyrovnávání zatížení, přečtěte si téma [Vysoká dostupnost a vyrovnávání zatížení konektorů a aplikací proxy aplikací](application-proxy-high-availability-load-balancing.md). |
| Použít více konektorů | Pro větší odolnost, dostupnost a škálování použijte dva nebo víc konektorů proxy aplikací (viz [konektory proxy aplikací](application-proxy-connectors.md)). Vytvořte skupiny konektorů a zajistěte, aby každá skupina konektorů měla aspoň dva konektory (optimální je tři konektory). |
| Najděte servery konektoru blízko aplikačním serverům a ujistěte se, že jsou ve stejné doméně. | Pro optimalizaci výkonu fyzicky Najděte Server konektoru blízko aplikačním serverům (viz téma [informace o topologii sítě](application-proxy-network-topology.md)). Servery konektorového serveru a webové aplikace by také měly patřit do stejné domény služby Active Directory nebo by měly zahrnovat důvěřující domény. Tato konfigurace je nutná pro jednotné přihlašování s integrovaným ověřováním systému Windows (IWA) a vynuceným delegováním protokolu Kerberos (KCD). Pokud jsou servery v různých doménách, budete muset použít delegování na základě prostředků pro jednotné přihlašování (viz [KCD pro jednotné přihlašování s aplikačním proxy serverem](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Povolit automatické aktualizace konektorů | Pro nejnovější funkce a opravy chyb povolte automatické aktualizace pro vaše konektory. Společnost Microsoft poskytuje přímou podporu pro nejnovější verzi konektoru a jednu verzi. (Viz [Historie verze vydaných verzí proxy aplikací](application-proxy-release-version-history.md).) |
| Obejít místní proxy server | Pro snazší údržbu Nakonfigurujte konektor tak, aby obcházel váš místní proxy server, aby se mohl přímo připojit ke službám Azure. (Viz [konektory proxy aplikací a proxy servery](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Použití služby Azure Proxy aplikací služby AD prostřednictvím služby Proxy webových aplikací | Pro většinu místních scénářů použijte Azure Proxy aplikací služby AD. Proxy webových aplikací je preferován pouze ve scénářích, které vyžadují proxy server pro AD FS a kde nemůžete použít vlastní domény v Azure Active Directory. (Viz [Migrace proxy aplikací](application-proxy-migration.md).) |
