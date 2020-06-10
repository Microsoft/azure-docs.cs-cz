---
title: Nové prostředí Registrace aplikací v Azure AD B2C
description: Úvod do nového prostředí pro registraci aplikací v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6b6294abe986115d86826fee8aad09f468b3d651
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84627996"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Nové prostředí Registrace aplikací pro Azure Active Directory B2C

Nové prostředí **[Registrace aplikací](https://aka.ms/b2cappregistrations)** pro Azure Active Directory B2C (Azure AD B2C) je teď všeobecně dostupné. Pokud máte zkušenosti s **aplikacemi** pro registraci aplikací pro Azure AD B2C, označované jako "starší prostředí", tato příručka vám pomůže začít používat nové prostředí.

## <a name="overview"></a>Přehled
Dřív jste museli spravovat aplikace Azure AD B2C spotřebiteli odděleně od ostatních aplikací, které používají starší verze prostředí. Který na různých místech v Azure znamenal jiné prostředí pro vytváření aplikací.

Nové prostředí zobrazuje všechny registrace Azure AD B2C aplikací a registrace aplikací Azure AD na jednom místě a poskytuje konzistentní způsob jejich správy. Aby bylo možné vytvořit aplikaci pro zákazníky pro správu aplikace s oprávněním Microsoft Graph pro správu prostředků, stačí se naučit pouze jeden způsob, jak provádět věci.

Nové prostředí můžete oslovit tak, že přejdete na **Registrace aplikací** v tenantovi Azure AD B2C z **Azure AD B2C** nebo **Azure Active Directory** služeb v Azure Portal.

Prostředí Azure AD B2C Registrace aplikací vychází z obecného prostředí pro [registraci aplikací](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) pro libovolného TENANTA Azure AD, ale je přizpůsobené pro Azure AD B2C klienty.

## <a name="whats-not-changing"></a>Co se nemění?
- Vaše aplikace a související konfigurace se dají najít tak, jak jsou, v novém prostředí. Nemusíte znovu registrovat aplikace a uživatelé vašich aplikací se nemusí znovu přihlašovat. 

> [!NOTE]
> Pokud chcete zobrazit všechny dříve vytvořené aplikace, přejděte do okna **Registrace aplikací** a vyberte kartu **všechny aplikace** . Zobrazí se aplikace vytvořené ve starší verzi prostředí, nové prostředí a ty, které se vytvořily ve službě Azure AD.

## <a name="key-new-features"></a>Klíčové nové funkce

-   **Seznam sjednocené aplikace** zobrazuje všechny aplikace, které se ověřují pomocí Azure AD B2C a Azure AD na jednom místě. Kromě toho můžete využívat funkce, které jsou už dostupné pro aplikace Azure AD, včetně údajů **o datu vytvoření** , **certifikátů & tajných** kódů, panelu hledání a mnohem víc.

-   **Registrace kombinované aplikace** umožňuje rychlou registraci aplikace, ať už se jedná o zákaznickou aplikaci nebo aplikaci pro přístup k Microsoft Graph.

- Podokno **koncové body** vám umožní rychle identifikovat relevantní koncové body pro váš scénář, včetně konfigurace OpenID Connect, metadat SAML, rozhraní Microsoft Graph API a [koncových bodů toku uživatele OAuth 2,0](tokens-overview.md#endpoints). 

- **Oprávnění API** a **vystavení rozhraní API** poskytují rozsáhlejší správu rozsahu, oprávnění a souhlasu. Nyní můžete k aplikaci přiřadit také oprávnění MS Graph a Azure AD Graph.

-   **Vlastníci** a **manifest** jsou nyní k dispozici pro aplikace, které se ověřují pomocí Azure AD B2C. Můžete přidat vlastníky pro své registrace a přímo upravit vlastnosti aplikace [pomocí editoru manifestu](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Nové podporované typy účtů

V novém prostředí vyberete typ účtu podpory z následujících možností:
- Účty pouze v tomto organizačním adresáři.
- Účty v jakémkoli adresáři organizace (libovolný adresář služby Azure AD – víceklientské).
- Účty v libovolném organizačním adresáři nebo jakémkoli zprostředkovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.

Chcete-li porozumět různým typům účtů, vyberte možnost **Potřebuji mi** vybrat v možnosti vytváření. 

Ve starší verzi prostředí se aplikace vždycky vytvořily jako aplikace s přístupem zákazníků. U těchto aplikací je typ účtu nastavený na **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**. 
> [!NOTE]
> Tato možnost vyžaduje, aby bylo možné spouštět Azure AD B2C toků uživatelů pro ověřování uživatelů pro tuto aplikaci. Naučte se [, jak zaregistrovat aplikaci pro použití s toky uživatelů.](tutorial-register-applications.md)

Tuto možnost můžete použít také k použití Azure AD B2C jako poskytovatele služby SAML. [Přečtěte si další informace](identity-provider-adfs2016-custom.md).

## <a name="applications-for-devops-scenarios"></a>Aplikace pro scénáře DevOps
Další typy účtů můžete použít k vytvoření aplikace pro správu DevOpsch scénářů, jako je například použití Microsoft Graph k nahrání zásad architektury pro rozhraní identity nebo zřízení uživatelů. Naučte [se, jak registrovat aplikaci Microsoft Graph pro správu prostředků Azure AD B2C](microsoft-graph-get-started.md).

Možná se nezobrazí všechna Microsoft Graph oprávnění, protože mnohé z těchto oprávnění se nevztahují na uživatele Azure B2C Consumer. [Přečtěte si další informace o správě uživatelů pomocí Microsoft Graph](manage-user-accounts-graph-api.md).  

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Souhlas správce a obory offline_access + OpenID  
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Obor **OpenID** je nezbytný, aby Azure AD B2C mohl podepisovat uživatele do aplikace. Pro vydávání tokenů aktualizace pro uživatele je potřeba **offline_access** rozsah. Tyto obory se ve výchozím nastavení dostaly do výše uvedeného souhlasu správce. Nyní můžete snadno přidávat oprávnění pro tyto obory t během procesu vytváření tím, že vyberete možnost **udělit souhlas správcům oprávnění OpenID a offline_access** . Jinak se oprávnění Microsoft Graph dá přidat k souhlasu správce v nastavení **oprávnění API** pro existující aplikaci.

Přečtěte si další informace o [oprávněních a souhlasu](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformy/ověřování: adresy URL odpovědí/identifikátory URI pro přesměrování
Ve starší verzi prostředí byly různé typy platforem spravované v rámci **vlastností** jako adresy URL odpovědí pro webové aplikace/rozhraní API a identifikátor URI pro přesměrování pro nativní klienty. "Nativní klienti" se označují také jako "veřejné klienty" a zahrnují aplikace pro iOS, macOS, Android a další typy mobilních a desktopových aplikací. 

V novém prostředí se adresy URL odpovědí a identifikátory URI pro přesměrování označují jako identifikátory URI přesměrování a dají se najít v části **ověřování** aplikace. Registrace aplikací nejsou omezeny buď na webovou aplikaci, nebo na rozhraní API, nebo na nativní aplikaci. Stejnou registraci aplikace můžete použít pro všechny tyto typy platforem tak, že zaregistrujete příslušné identifikátory URI pro přesměrování. 

Identifikátory URI pro přesměrování musí být přidružené k typu aplikace, a to buď web, nebo Public (mobilní zařízení a Desktop). [Další informace o identifikátorech URI pro přesměrování](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Jestli má být aplikace považována za veřejného klienta za běhu z typu platformy identifikátoru URI přesměrování, pokud je to možné. Nastavení **považovat aplikaci jako veřejné klienta** by mělo být nastavené na *Ano* pro toky, které nemůžou používat identifikátor URI přesměrování, například ROPC toky.

Platformy **iOS/MacOS** a **Android** jsou typem veřejného klienta. Poskytují snadný způsob konfigurace aplikací pro iOS/macOS nebo Android s odpovídajícími identifikátory URI pro přesměrování pro použití s MSAL. Přečtěte si další informace o [možnostech konfigurace aplikace](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Certifikáty aplikací & tajných kódů

V novém prostředí místo klíčů použijete okno **certifikáty & tajných** **kódů**ke správě certifikátů a tajných klíčů. Přihlašovací údaje umožňují aplikacím identifikovat samy sebe k ověřovací službě při přijímání tokenů na webovém umístění s adresou (pomocí schématu protokolu HTTPS). Při ověřování vůči službě Azure AD doporučujeme použít certifikát místo tajného klíče klienta pro scénáře přihlašovacích údajů klienta. Certifikáty se nedají použít k ověřování proti Azure AD B2C.


## <a name="features-not-available-in-azure-ad-b2c-tenants"></a>Funkce, které nejsou dostupné v Azure AD B2Cch klientech
Pro Azure AD B2C klienty neplatí následující možnosti registrace aplikace Azure AD:
- **Role a správci** – vyžaduje Azure AD Premium licenci P1 nebo P2, která není aktuálně k dispozici pro Azure AD B2C.
- **Branding** – uživatelské rozhraní a přizpůsobení uživatelského rozhraní je nakonfigurované v prostředí **brandingu společnosti** nebo v rámci toku uživatele. Naučte se [přizpůsobit uživatelské rozhraní v Azure Active Directory B2C](customize-ui-overview.md).
- **Ověřování domény vydavatele** – vaše aplikace je zaregistrovaná v *. onmicrosoft.com*, což není ověřená doména. Doména vydavatele se navíc primárně používá pro udělení souhlasu uživatele, která se nevztahují na Azure AD B2C aplikace pro ověřování uživatelů. [Přečtěte si další informace o doméně vydavatele](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain).
- **Konfigurace tokenu** – token se konfiguruje jako součást uživatelského toku, nikoli aplikace.
- Prostředí **rychlý Start** v současnosti není pro Azure AD B2C klienty k dispozici.
- Okno **Pomocník pro integraci** není pro Azure AD B2C klienty aktuálně k dispozici.


## <a name="limitations"></a>Omezení
Nové prostředí má následující omezení:
- V současné době Azure AD B2C nerozlišuje mezi schopnostmi vydávat přístup nebo tokeny ID pro implicitní toky; Oba typy tokenů jsou k dispozici pro implicitní tok udělení, pokud je v okně **ověřování** vybraná možnost **tokeny ID** .
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Změna hodnoty pro podporované účty není v uživatelském rozhraní podporována. Pokud přecházíte mezi jedním klientem Azure AD a více klienty, budete muset použít manifest aplikace.

## <a name="next-steps"></a>Další kroky

Začněte s novým prostředím pro registraci aplikací:
* Zjistěte [, jak zaregistrovat webovou aplikaci](tutorial-register-applications.md) .
* Zjistěte [, jak zaregistrovat webové rozhraní API](add-web-api-application.md) .
* Naučte [se registrovat nativní klientskou aplikaci](add-native-application.md) .
* Naučte [se, jak registrovat aplikaci Microsoft Graph pro správu prostředků Azure AD B2C](microsoft-graph-get-started.md).
* Naučte [se používat Azure AD B2C jako poskytovatele služby SAML.](identity-provider-adfs2016-custom.md)
* Další informace o [typech aplikací](application-types.md)
