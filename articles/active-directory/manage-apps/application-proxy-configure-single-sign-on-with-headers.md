---
title: Jednotné přihlašování založené na hlavičkách pro místní aplikace s Aplikace Azure AD proxy
description: Naučte se poskytovat jednotné přihlašování k místním aplikacím zabezpečeným ověřováním pomocí hlaviček.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 813e360ffc6c78b52151af5b9867560ae6d58f2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198239"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy"></a>Jednotné přihlašování založené na hlavičkách pro místní aplikace s Aplikace Azure AD proxy

Proxy aplikace Azure Active Directory (Azure AD) nativně podporují přístup k jednotnému přihlašování k aplikacím, které používají hlavičky pro ověřování. V Azure AD můžete nakonfigurovat hodnoty hlaviček vyžadované vaší aplikací. Hodnoty hlaviček budou do aplikace odesílány prostřednictvím proxy aplikace. Mezi výhody použití nativní podpory pro ověřování na základě hlaviček pomocí proxy aplikací patří:  

* **Zjednodušení poskytování vzdáleného přístupu k místním aplikacím** – proxy aplikací umožňuje zjednodušit stávající architekturu vzdáleného přístupu. Můžete nahradit přístup k těmto aplikacím pomocí sítě VPN. Můžete také odebrat závislosti na místních řešeních identity pro ověřování. Pokud se přihlásíte k používání podnikových aplikací, nebudou si uživatelé moci všimnout žádné jiné. Můžou pořád pracovat odkudkoli na jakémkoli zařízení.  

* **Žádné další software ani změny v aplikacích** – můžete používat existující konektory proxy aplikací a nevyžadují instalaci žádného dalšího softwaru.  

* **Dostupný seznam atributů a transformací** – všechny dostupné hodnoty hlaviček jsou založené na standardních deklaracích vydaných službou Azure AD. Všechny atributy a transformace, které jsou k dispozici pro [konfiguraci deklarací pro aplikace SAML nebo OIDC](../develop/active-directory-saml-claims-customization.md#attributes) , jsou také k dispozici pro použití jako hodnoty záhlaví. 

## <a name="pre-requisites"></a>Požadavky
Než začnete s jednotným přihlašováním pro aplikace ověřování založené na hlavičkách, ujistěte se, že je prostředí připravené s následujícími nastaveními a konfiguracemi:
- Je nutné povolit proxy aplikace a nainstalovat konektor, který obsahuje řádek webu pro aplikace. Podívejte se na kurz [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikací](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) a Naučte se, jak připravit místní prostředí, nainstalovat a zaregistrovat konektor a otestovat konektor. 

## <a name="supported-capabilities"></a>Podporované funkce

V následující tabulce jsou uvedeny běžné funkce vyžadované pro aplikace ověřování založené na hlavičkách, které jsou podporované proxy aplikací. 

|Požadavek   |Popis|
|----------|-----------|
|Federované jednotné přihlašování |V režimu před ověřením jsou všechny aplikace chráněné pomocí ověřování Azure AD a umožňují uživatelům používat jednotné přihlašování. |
|Vzdálený přístup |Proxy aplikací umožňuje vzdálený přístup k aplikaci. Uživatelé mají přístup k aplikaci z Internetu v jakémkoli prohlížeči pomocí externí adresy URL. Proxy aplikací není určeno pro použití podnikového přístupu. |
|Integrace na základě hlaviček |Proxy aplikace provádí integraci jednotného přihlašování s Azure AD a pak předá identitu nebo jiná data aplikace jako hlavičky HTTP do aplikace. |
|Autorizace aplikací |Společné zásady je možné zadat na základě přistupované aplikace, členství uživatele ve skupinách a dalších zásad. Ve službě Azure AD se zásady implementují pomocí [podmíněného přístupu](../conditional-access/overview.md). Zásady autorizace aplikací se vztahují jenom na počáteční požadavek na ověření. |
|Podrobné ověřování |Zásady je možné definovat pro vynucení přidání ověřování, například pro získání přístupu k citlivým prostředkům. |
|Jemně odstupňovaná autorizace |Poskytuje řízení přístupu na úrovni URL. Přidané zásady je možné vyhovět na základě adresy URL, ke které přistupovali. Interní adresa URL nakonfigurovaná pro aplikaci definuje obor aplikace, na kterou se zásada aplikuje. Zásada konfigurovaná pro nejpodrobnější cestu je vynutila.  |

> [!NOTE] 
> Tento článek obsahuje funkce, které se připojují k aplikacím ověřování na základě hlaviček ve službě Azure AD pomocí proxy aplikací a je doporučeným vzorem. Alternativně existuje také model integrace, který používá PingAccess s Azure AD k povolení ověřování na základě hlaviček. Další podrobnosti najdete v tématu [ověřování na základě hlaviček pro jednotné přihlašování s proxy aplikací a PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Jak to funguje

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Princip jednotného přihlašování založeného na hlavičkách pomocí proxy aplikací" lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. Správce přizpůsobí mapování atributů vyžadované aplikací na portálu Azure AD. 
2. Když uživatel přistupuje k aplikaci, proxy aplikace ověří, že se uživatel ověřuje pomocí Azure AD. 
3. Cloudová služba proxy aplikací má informace o požadovaných atributech. Proto služba načte odpovídající deklarace identity z tokenu ID přijatého během ověřování. Služba pak hodnoty přeloží do požadovaných hlaviček protokolu HTTP jako součást požadavku konektoru. 
4. Požadavek se pak předává spolu s konektorem, který se pak předává do back-endu aplikace. 
5. Aplikace obdrží hlavičky a podle potřeby může tyto hlavičky použít. 

## <a name="publish-the-application-with-application-proxy"></a>Publikování aplikace s využitím proxy aplikací

1. Publikujte aplikaci podle pokynů popsaných v tématu [publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - Hodnota interní adresy URL určuje rozsah aplikace. Pokud nakonfigurujete hodnotu interní adresy URL na kořenové cestě aplikace, pak všechny podřízené cesty pod kořenem budou mít stejnou konfiguraci záhlaví a další konfiguraci aplikace. 
    - Vytvořte novou aplikaci, abyste nastavili jinou konfiguraci hlaviček nebo přiřazení uživatelů pro podrobnější cestu, než je nakonfigurovaná aplikace. V nové aplikaci nakonfigurujte interní adresu URL s konkrétní cestou, kterou potřebujete, a pak nakonfigurujte konkrétní záhlaví potřebná pro tuto adresu URL. Proxy aplikace bude vždycky odpovídat nastavení konfigurace na nejpodrobnější cestu nastavenou pro aplikaci. 

2. Jako metodu předběžného ověření vyberte **Azure Active Directory**   . **** 
3. Přiřaďte testovacího uživatele tak, že přejdete na **uživatele a skupiny** a přiřadíte příslušné uživatele a skupiny. 
4. Otevřete prohlížeč a přejděte na **externí adresu URL**   z nastavení proxy aplikace. 
5. Ověřte, zda se můžete připojit k aplikaci. I když se můžete připojit, nemáte k této aplikaci přístup, protože hlavičky nejsou nakonfigurované. 

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování 
Předtím, než začnete s jednotným přihlašováním pro aplikace založené na hlavičkách, byste už měli mít nainstalovaný konektor proxy aplikací a tento konektor má přístup k cílovým aplikacím. Pokud ne, postupujte podle kroků v [kurzu: Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md)   pak se vraťte sem. 

1. Jakmile se vaše aplikace objeví v seznamu podnikových aplikací, vyberte ji a vyberte **jednotné přihlašování**. 
2. Nastavte režim jednotného přihlašování na **základě hlaviček**. 
3. V části základní konfigurace se **Azure Active Directory** vybere jako výchozí. 
4. Vyberte možnost upravit tužku v záhlavích ke konfiguraci hlaviček pro odeslání do aplikace. 
5. Vyberte **Přidat novou hlavičku**. Zadejte **název** záhlaví a vyberte buď **atribut** , **transformaci** a vyberte z rozevíracího seznamu, který bude vaše aplikace potřebovat.  
    - Další informace o seznamu dostupných atributů najdete v tématu [přizpůsobení deklarací – atributy](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Další informace o seznamu dostupných transformací najdete v tématu [přizpůsobení deklarací – transformace deklarací identity](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - Můžete také přidat **záhlaví skupiny**, aby bylo možné odeslat všechny skupiny, do kterých uživatel patří, nebo skupiny přiřazené k aplikaci jako záhlaví. Další informace o konfiguraci skupin jako hodnoty najdete v tématu [Konfigurace deklarací identity skupin pro aplikace](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Vyberte Uložit. 

## <a name="test-your-app"></a>Testování aplikace 

Až dokončíte všechny tyto kroky, vaše aplikace by měla být spuštěná a dostupná. Testování aplikace: 
1. Otevřete nové okno prohlížeče nebo privátního prohlížeče, abyste měli jistotu, že se předchozí hlavičky v mezipaměti vymažou. Pak z nastavení proxy aplikace přejděte na **externí adresu URL**   .
2. Přihlaste se pomocí testovacího účtu, který jste přiřadili k aplikaci. Pokud se k aplikaci můžete pomocí jednotného přihlašování (SSO) načíst a přihlásit se k ní, budete připraveni. 

## <a name="considerations"></a>Požadavky

- Proxy aplikací slouží k poskytování vzdáleného přístupu k aplikacím v místním prostředí nebo v privátním cloudu. U proxy aplikací se nedoporučuje zpracovávat přenosy z interní sítě z podnikové sítě.
- Přístup k aplikacím ověřování na základě hlaviček by měl být omezený jenom na provoz z konektoru nebo na jiné povolené řešení ověřování založené na hlavičkách. To se obvykle provádí díky omezení přístupu k síti na aplikaci pomocí brány firewall nebo omezení IP adresy na aplikačním serveru.

## <a name="next-steps"></a>Další kroky

- [Co je jednotné přihlašování?](what-is-single-sign-on.md)
- [Co je aplikace proxy?](what-is-application-proxy.md)
- [Série rychlý Start při správě aplikací](view-applications-portal.md)
