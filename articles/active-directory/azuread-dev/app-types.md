---
title: Typy aplikací v v 1.0 | Azure
description: Popisuje typy aplikací a scénářů, které podporuje koncový bod Azure Active Directory v 2.0.
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
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88116440"
---
# <a name="application-types-in-v10"></a>Typy aplikací v v 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) podporuje ověřování pro celou řadu moderních architektur aplikací, které jsou založené na standardních protokolech OAuth 2,0 nebo OpenID Connect.

Následující diagram znázorňuje scénáře a typy aplikací a způsob, jakým lze přidat různé součásti:

![Typy aplikací a scénáře](./media/authentication-scenarios/application-types-scenarios.png)

Toto jsou pět scénářů primárních aplikací podporovaných službou Azure AD:

- **[Jednostránkové aplikace (Spa)](single-page-application.md)**: uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.
- **[Webový prohlížeč do webové aplikace](web-app.md)**: uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.
- **[Nativní aplikace do webového rozhraní API](native-app.md)**: nativní aplikace, která běží na telefonu, tabletu nebo počítači, musí ověřit uživatele a získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
- **[Webová aplikace do webového rozhraní API](web-api.md)**: webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
- **[Démon nebo serverová aplikace do webového rozhraní API](service-to-service.md)**: aplikace démona nebo serverová aplikace bez webového uživatelského rozhraní musí získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.

Pomocí odkazů můžete získat další informace o každém typu aplikace a porozumět scénářům vysoké úrovně předtím, než začnete pracovat s kódem. Můžete si také přečíst informace o rozdílech, které potřebujete vědět při psaní konkrétní aplikace, která funguje s koncovým bodem v 1.0 nebo v 2.0.

> [!NOTE]
> Koncový bod v 2.0 nepodporuje všechny scénáře a funkce služby Azure AD. Pokud chcete zjistit, jestli byste měli použít koncový bod v 2.0, přečtěte si o [omezeních v verzi 2.0](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json).

Můžete vyvíjet libovolné aplikace a scénáře popsané tady pomocí různých jazyků a platforem. Všechny jsou zajištěny úplnými ukázkami kódu, které jsou k dispozici v průvodci ukázkami kódu: [v 1.0 ukázky kódu podle scénáře](sample-v1-code.md) a [v 2.0 ukázky kódu podle scénáře](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Ukázky kódu si také můžete stáhnout přímo z odpovídajících [ukázek úložišť pro GitHub](https://github.com/Azure-Samples?q=active-directory).

Kromě toho, pokud vaše aplikace potřebuje konkrétní část nebo segment kompletního scénáře, ve většině případů je možné přidat funkce nezávisle. Například pokud máte nativní aplikaci, která volá webové rozhraní API, můžete snadno přidat webovou aplikaci, která také volá webové rozhraní API.

## <a name="app-registration"></a>Registrace aplikace

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrace aplikace, která používá koncový bod Azure AD v 1.0

Všechny aplikace, které ověřování pomocí zdrojů do Azure AD musí být zaregistrované v adresáři. Tento krok zahrnuje službu Azure AD o vaší aplikaci, včetně adresy URL, kde se nachází, adresy URL pro odeslání odpovědí po ověření, identifikátoru URI k identifikaci vaší aplikace a dalších. Tyto informace jsou vyžadovány pro několik hlavních důvodů:

* Služba Azure AD potřebuje komunikovat s aplikací při manipulaci s přihlašováním nebo výměnou tokenů. Mezi službou Azure AD a aplikací jsou předané tyto informace:
  
  * Identifikátor **URI ID aplikace** – identifikátor pro aplikaci. Tato hodnota se během ověřování pošle do Azure AD, aby označovala aplikaci, pro kterou volající potřebuje token. Tato hodnota je navíc obsažena v tokenu, takže aplikace ví, že se jedná o zamýšlený cíl.
  * Adresa URL **odpovědi** a **identifikátor URI pro přesměrování** – pro webové rozhraní API nebo webovou aplikaci je adresa URL odpovědi umístění, kde služba Azure AD odešle odpověď na ověření, včetně tokenu, pokud bylo ověření úspěšné. U nativních aplikací je identifikátor URI přesměrování jedinečným identifikátorem, na který Azure AD přesměruje uživatelského agenta v žádosti OAuth 2,0.
  * **ID aplikace** – ID pro aplikaci, která je generována službou Azure AD při registraci aplikace. Při vyžádání autorizačního kódu nebo tokenu se ID aplikace a klíč pošle do služby Azure AD během ověřování.
  * **Klíč** – klíč, který se pošle spolu s ID aplikace při ověřování ve službě Azure AD za účelem volání webového rozhraní API.
* Azure AD musí zajistit, aby měla aplikace potřebná oprávnění pro přístup k datům adresáře, dalším aplikacím ve vaší organizaci atd.

Další podrobnosti najdete v článku o [registraci aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Klienti s jedním klientem a víceklientské aplikace

Zřizování se může vyjasnit, když rozumíte tomu, že existují dvě kategorie aplikací, které je možné vyvíjet a integrovat s Azure AD:

* **Samostatná aplikace tenanta** – jedna klientská aplikace je určená pro použití v jedné organizaci. Obvykle se jedná o obchodní aplikace napsané podnikovým vývojářem. Jediná aplikace tenanta musí být přístupná jenom uživatelům v jednom adresáři a v důsledku toho se musí zřídit jenom v jednom adresáři. Tyto aplikace jsou obvykle registrovány vývojářem v organizaci.
* **Víceklientské aplikace** – víceklientské aplikace je určena pro použití v mnoha organizacích, nikoli jenom v jedné organizaci. Jsou to obvykle aplikace typu software jako služba (SaaS) napsané nezávislým dodavatelem softwaru (ISV). Víceklientské aplikace je potřeba zřídit v každém adresáři, kde se budou používat, což vyžaduje souhlas uživatele nebo správce při jejich registraci. Tento postup souhlasu začíná, když je aplikace registrovaná v adresáři a má přístup k Graph API nebo možná jiné webové rozhraní API. Když se uživatel nebo správce z jiné organizace přihlásí k používání aplikace, zobrazí se dialogové okno, ve kterém se zobrazují oprávnění, která aplikace vyžaduje. Uživatel nebo správce pak může udělit souhlas s aplikací, který aplikaci udělí přístup k uvedeným datům, a nakonec registruje aplikaci do svého adresáře. Další informace najdete v tématu [Přehled rozhraní pro vyjádření souhlasu](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Další předpoklady při vývoji jednoho nebo více tenantů aplikací

Při vývoji víceklientské aplikace místo jediné klientské aplikace vznikají nějaké další okolnosti. Pokud například aplikaci zpřístupníte uživatelům ve více adresářích, budete potřebovat mechanismus, který určí, v jakém tenantovi se nachází. Jediná klientská aplikace potřebuje vyhledat konkrétního uživatele pouze ve svém vlastním adresáři, zatímco aplikace pro více tenantů musí určit určitého uživatele ze všech adresářů ve službě Azure AD. K provedení této úlohy Azure AD poskytuje běžný koncový bod ověřování, ve kterém může libovolná aplikace pro více tenantů směrovat požadavky na přihlášení namísto koncového bodu specifického pro tenanta. Tento koncový bod je `https://login.microsoftonline.com/common` pro všechny adresáře ve službě Azure AD, zatímco koncový bod specifický pro tenanta může být `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Běžný koncový bod je obzvláště důležitý při vývoji vaší aplikace, protože budete potřebovat logiku potřebnou k tomu, aby během přihlašování, odhlašování a ověřování tokenu zpracovala více tenantů.

Pokud aktuálně vyvíjíte jednu aplikaci tenanta, ale chcete ji zpřístupnit mnoha organizacím, můžete v Azure AD snadno provádět změny aplikace a její konfigurace, aby bylo možné ji využívat pro více tenantů. Kromě toho Azure AD používá stejný podpisový klíč pro všechny tokeny ve všech adresářích bez ohledu na to, jestli poskytujete ověřování v jednom tenantovi nebo víceklientské aplikaci.

Každý scénář uvedený v tomto dokumentu obsahuje dílčí oddíl, který popisuje jeho požadavky na zřizování. Podrobnější informace o zřizování aplikace v Azure AD a rozdílech mezi jednotlivými a více tenantů aplikacemi najdete v tématu [Integrace aplikací s Azure Active Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) pro další informace. Pokračujte ve čtení a seznamte se s běžnými scénáři aplikací ve službě Azure AD.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [základech ověřování](v1-authentication-scenarios.md) Azure AD