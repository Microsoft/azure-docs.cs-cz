---
title: Typy aplikací v 1.0 | Azure
description: Popisuje typy aplikací a scénářů podporovaných koncovým bodem Azure Active Directory v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c290cbf36fd53d5afb5fd805cda896fb6879bb4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154946"
---
# <a name="application-types-in-v10"></a>Typy aplikací v 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) podporuje ověřování pro celou řadu moderních architektur aplikací, všechny jsou založeny na standardních protokolech OAuth 2.0 nebo OpenID Connect.

Následující diagram znázorňuje scénáře a typy aplikací a jak lze přidat různé součásti:

![Typy aplikací a scénáře](./media/authentication-scenarios/application-types-scenarios.png)

Toto je pět scénářů primární aplikace podporovaných službou Azure AD:

- **[Jednostránková aplikace (SPA):](single-page-application.md)** Uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.
- **[Webový prohlížeč na webovou aplikaci](web-app.md)**: Uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.
- **[Nativní aplikace pro webové rozhraní API:](native-app.md)** Nativní aplikace, která běží na telefonu, tabletu nebo počítači, potřebuje ověřit uživatele, aby získala prostředky z webového rozhraní API zabezpečeného službou Azure AD.
- **[Webová aplikace do webového rozhraní API](web-api.md)**: Webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
- **[Daemon nebo serverová aplikace do webového rozhraní API](service-to-service.md)**: Daemon aplikace nebo serverové aplikace bez webového uživatelského rozhraní potřebuje získat prostředky z webového rozhraní API zabezpečené službou Azure AD.

Postupujte podle odkazů, abyste se dozvěděli více o jednotlivých typech aplikací a pochopili scénáře na vysoké úrovni, než začnete pracovat s kódem. Můžete se také dozvědět o rozdílech, které potřebujete vědět při psaní konkrétní aplikace, která pracuje s koncovým bodem v1.0 nebo v2.0 koncového bodu.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře a funkce Azure AD. Chcete-li zjistit, zda byste měli použít koncový bod v2.0, přečtěte si o [omezeních v2.0](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Můžete vytvořit některou z aplikací a scénářů popsaných zde pomocí různých jazyků a platforem. Všechny jsou zálohovány kompletní ukázky kódu k dispozici v průvodci ukázky kódu: [v1.0 ukázky kódu podle scénáře](sample-v1-code.md) a [v2.0 vzorky kódu podle scénáře](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Ukázky kódu můžete také stáhnout přímo z odpovídajících [ukázkových úložišť GitHub](https://github.com/Azure-Samples?q=active-directory).

Kromě toho pokud vaše aplikace potřebuje konkrétní kus nebo segment scénáře end-to-end, ve většině případů, že funkce lze přidat nezávisle. Například pokud máte nativní aplikace, která volá webové rozhraní API, můžete snadno přidat webovou aplikaci, která také volá webové rozhraní API.

## <a name="app-registration"></a>Registrace aplikací

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrace aplikace, která používá koncový bod Azure AD v1.0

Všechny aplikace, které outsourcují ověřování do Azure AD musí být registrovány v adresáři. Tento krok zahrnuje sdělit Azure AD o vaší aplikaci, včetně adresy URL, kde se nachází, URL pro odesílání odpovědí po ověření, URI k identifikaci vaší aplikace a další. Tyto informace jsou vyžadovány z několika klíčových důvodů:

* Azure AD potřebuje komunikovat s aplikací při zpracování přihlášení nebo výměnu tokenů. Informace předané mezi Azure AD a aplikací zahrnují následující:
  
  * **Identifikátor URI ID aplikace** – identifikátor aplikace. Tato hodnota se odesílá do služby Azure AD během ověřování k označení, pro kterou aplikaci volající chce token. Navíc tato hodnota je součástí tokenu tak, aby aplikace ví, že byl zamýšlený cíl.
  * **Adresa URL odpovědi** a **identifikátor URI přesměrování** – pro webové rozhraní API nebo webové aplikace je adresa URL odpovědi umístění, kde azure ad odešle odpověď ověřování, včetně tokenu, pokud bylo ověření úspěšné. Pro nativní aplikace identifikátor URI přesměrování je jedinečný identifikátor, na který Azure AD přesměruje uživatelského agenta v požadavku OAuth 2.0.
  * **ID aplikace** – ID pro aplikaci, která je generována službou Azure AD při registraci aplikace. Při vyžádání autorizačního kódu nebo tokenu se ID aplikace a klíč odesílají do služby Azure AD během ověřování.
  * **Klíč** – klíč, který se odesílá spolu s ID aplikace při ověřování azure ad volat webové rozhraní API.
* Azure AD musí zajistit, že aplikace má požadovaná oprávnění pro přístup k datům adresáře, další aplikace ve vaší organizaci a tak dále.

Podrobnosti najdete v tom, jak [zaregistrovat aplikaci](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplikace s jedním klientem a více klienty

Zřizování se stává jasnější, když pochopíte, že existují dvě kategorie aplikací, které lze vyvíjet a integrovat s Azure AD:

* **Aplikace jednoho tenanta** – aplikace jednoho klienta je určena pro použití v jedné organizaci. Obvykle se jedná o obchodní aplikace (LoB) napsané podnikovým vývojářem. K aplikaci jednoho klienta musí přistupovat pouze uživatelé v jednom adresáři a v důsledku toho je třeba pouze zřídit v jednom adresáři. Tyto aplikace jsou obvykle registrovány vývojářem v organizaci.
* **Víceklientská aplikace** – víceklientská aplikace je určena pro použití v mnoha organizacích, nikoli pouze v jedné organizaci. Obvykle se jedná o aplikace se softwarem jako službou (SaaS) napsané nezávislým dodavatelem softwaru (ISV). Víceklientské aplikace je třeba zřídit v každém adresáři, kde budou použity, což vyžaduje souhlas uživatele nebo správce k jejich registraci. Tento proces souhlasu se spustí, když byla aplikace zaregistrována v adresáři a je mu udělen přístup k rozhraní Graph API nebo jinému webovému rozhraní API. Když se uživatel nebo správce z jiné organizace zaregistruje k použití aplikace, zobrazí se jim dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje. Uživatel nebo správce pak může souhlasit s aplikací, která dává aplikaci přístup k uvedeným datům, a nakonec zaregistruje aplikaci ve svém adresáři. Další informace naleznete v [tématu Přehled rámce pro udělení souhlasu](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Další důležité informace při vývoji aplikací s jedním klientem nebo více klienty

Některé další důležité informace vznikají při vývoji víceklientské aplikace namísto aplikace jednoho klienta. Například pokud zpřístupňujete aplikaci uživatelům ve více adresářích, potřebujete mechanismus k určení, ve kterém tenantovi se nacházejí. Aplikace jednoho klienta potřebuje pouze hledat ve vlastním adresáři pro uživatele, zatímco víceklientské aplikace potřebuje k identifikaci konkrétního uživatele ze všech adresářů ve službě Azure AD. K provedení tohoto úkolu Azure AD poskytuje společný koncový bod ověřování, kde všechny víceklientské aplikace můžete přímé požadavky na přihlášení, namísto koncového bodu specifického pro klienta. Tento koncový `https://login.microsoftonline.com/common` bod je pro všechny adresáře ve službě Azure AD, zatímco koncový bod specifický pro klienta může být `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Společný koncový bod je obzvláště důležité vzít v úvahu při vývoji aplikace, protože budete potřebovat potřebnou logiku pro zpracování více klientů během přihlášení, odhlášení a ověření tokenu.

Pokud aktuálně vyvíjíte aplikaci jednoho klienta, ale chcete ji zpřístupnit mnoha organizacím, můžete snadno provést změny v aplikaci a její konfiguraci ve službě Azure AD, aby byla schopná více klienta. Kromě toho Azure AD používá stejný podpisový klíč pro všechny tokeny ve všech adresářích, ať už poskytujete ověřování v jednom tenantovi nebo víceklientské aplikaci.

Každý scénář uvedený v tomto dokumentu obsahuje pododdíl, který popisuje jeho požadavky na zřizování. Další informace o zřizování aplikace ve službě Azure AD a o rozdílech mezi aplikacemi s jedním a více klienty najdete v [tématu Integrace aplikací s Azure Active Directory.](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Pokračujte ve čtení pochopit běžné scénáře aplikací ve službě Azure AD.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [základech ověřování](v1-authentication-scenarios.md) azure ad
