---
title: Typy aplikací v v1.0 | Azure
description: Popisuje typy aplikací a scénáře podporované koncovým bodem v2.0 Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 5a8e43934393c2e6a97b81809284781a5c207e41
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816340"
---
# <a name="application-types-in-v10"></a>Typy aplikací v v1.0

Azure Active Directory (Azure AD) podporuje ověřování pro celou řadu architektur moderních aplikací, všechny z nich založené na standardních oborových protokolů OAuth 2.0 nebo OpenID Connect.

Následující diagram znázorňuje scénáře a typy aplikací, a jak je možné přidat různé součásti:

![Typy aplikací a scénáře](./media/authentication-scenarios/application_types_and_scenarios.png)

Toto jsou pěti primární aplikace scénáře podporované službou Azure AD:

- **[Jednostránkové aplikace (SPA)](single-page-application.md)**: uživatel potřebuje pro přihlášení k jednostránková aplikace, která je zabezpečena pomocí služby Azure AD.
- **[Webový prohlížeč na webovou aplikaci](web-app.md)**: uživatel potřebuje pro přihlášení k webové aplikaci, která je zabezpečena pomocí služby Azure AD.
- **[Nativní aplikace pro webové rozhraní API](native-app.md)**: nativní aplikaci, která běží na telefonu, tabletu nebo počítači potřebuje ověřit uživatele k získání zdroje z webového rozhraní API, která je zabezpečena pomocí služby Azure AD.
- **[Webové aplikace k webovému rozhraní API](web-api.md)**: webová aplikace je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.
- **[Proces démon nebo server aplikace webového rozhraní API](service-to-service.md)**: aplikace proces démon nebo serverovou aplikaci s žádné webové uživatelské rozhraní je potřeba získat prostředky z webového rozhraní API zabezpečené pomocí Azure AD.

V odkazech na další informace o jednotlivých typech aplikací a pochopit scénáře vysoké úrovně, než se pustíte do práce s kódem. Můžete také informace o rozdíly, na které je potřeba vědět při zápisu konkrétní aplikace, která funguje s koncový bod verze 1.0 nebo koncový bod verze 2.0.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny funkce a scénáře služby Azure AD. Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).

Můžete vyvíjet aplikace a scénáře popsané tady pomocí různé jazyky a platformy. Tyto jsou všechno se opírá kompletní kód ukázky, které jsou k dispozici v příručce ukázky kódu: [v1.0 ukázky kódu podle scénáře](sample-v1-code.md) a [ukázky kódu v2.0 podle scénáře](sample-v2-code.md). Můžete také stáhnout ukázky kódu přímo z odpovídající [ukázkové úložiště GitHub](https://github.com/Azure-Samples?q=active-directory).

Kromě toho zda vaše aplikace potřebuje konkrétní nebo segment začátku do konce scénáře, ve většině případů, které tuto funkci lze přidat nezávisle na sobě. Například pokud máte nativní aplikaci, která volá webové rozhraní API, můžete snadno přidat webovou aplikaci, která volá webové rozhraní API také.

## <a name="app-registration"></a>Registrace aplikace

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrace aplikace, která používá koncový bod Azure AD verze 1.0

Všechny aplikace, které outsources ověřování do služby Azure AD musí být zaregistrovaný v adresáři. Tento krok zahrnuje informacemi o vaší aplikaci, včetně adresy URL, kde je umístěn, adresa URL pro odeslání odpovědi po ověření, identifikátor URI k identifikaci vaší aplikace a další služby Azure AD. Tyto informace jsou nezbytné pro několik zásadních důvodů:

* Služba Azure AD potřebuje ke komunikaci s aplikací při zpracování přihlašování nebo výměnou tokenů. Předávat mezi službami Azure AD a aplikace zahrnují následující:
  
  * **Identifikátor URI ID aplikace** -identifikátor pro aplikaci. Tato hodnota posílá do služby Azure AD během ověřování k označení, kterou aplikaci volající požaduje token. Kromě toho tato hodnota zahrnuje v tokenu, aby aplikace věděla, že byla zamýšleným cílem.
  * **Adresa URL pro odpověď** a **identifikátor URI pro přesměrování** – pro webové rozhraní API nebo webovou aplikaci, adresa URL odpovědi je umístění, kam Azure AD pošle odpověď ověřování, včetně token, pokud je ověření proběhlo úspěšně. Pro nativní aplikace identifikátor URI pro přesměrování je jedinečný identifikátor, na který Azure AD přesměruje uživatelského agenta v požadavku OAuth 2.0.
  * **ID aplikace** – ID aplikace, který je generován při registraci aplikace Azure AD. Pokud se požaduje token nebo autorizační kód, ID aplikace a klíč se odesílají do služby Azure AD během ověřování.
  * **Klíč** – klíč, který se zasílá společně s ID aplikace při ověřování do služby Azure AD k volání webového rozhraní API.
* Azure AD je potřeba zajistit, že aplikace má požadovaná oprávnění pro přístup k datům adresáře, jiné aplikace ve vaší organizaci a tak dále.

Podrobnosti, zjistěte, jak [registrace aplikace ke koncovému bodu Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Aplikace pro jednoho tenanta a více tenantů

Zřizování se změní volbu, pokud víte, že existují dvě kategorie aplikací, které mohou být vyvinuté a integrovat se službou Azure AD:

* **Jednoduché aplikace s tenanty** -jednoho tenanta aplikaci je určena pro použití v jedné organizaci. Toto jsou obvykle-obchodní (LoB) aplikace napsané tak podnikový vývojář. Jednoho tenanta aplikaci pouze musí mít přístup uživatelé v jednom adresáři, a proto potřebuje pouze zřídit v jednom adresáři. Tyto aplikace jsou běžně registrovány vývojáři, kteří v organizaci.
* **Aplikace s více tenanty** – aplikace s více tenanty určena pro použití v mnoha organizacích, ne jenom jedné organizace. Toto jsou obvykle software-as-a-service (SaaS) aplikací autorem nezávislý výrobce softwaru (ISV). Aplikace s více tenanty potřeba ho zřídit v každém adresáři, kde se využijí, který vyžaduje souhlas správce nebo uživatele k registraci je. Tento proces souhlas začne, když aplikace byl zaregistrován v adresáři a je jim přístup k rozhraní Graph API nebo možná jiného webového rozhraní API. Pokud uživatel nebo správce z jiné organizace zaregistruje k používání aplikace, zobrazí se dialogové okno se zobrazí oprávnění, která aplikace požaduje instalaci. Uživatel nebo správce můžete pak souhlas s aplikací, které poskytuje přístup k aplikaci do uvedeného data a nakonec zaregistruje aplikaci do svého adresáře. Další informace najdete v tématu [přehled architektury souhlas](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Další aspekty při vývoji jeden tenant nebo víceklientské aplikace

Několik dalších důležitých informací ke kterým dochází při vývoji aplikace s více tenanty místo jednoho tenanta aplikaci. Například pokud vaše aplikace jsou zpřístupníte uživatelům v různých adresářích, je třeba mechanismus pro určení kterého tenanta ve kterých se nacházejí. Jednoho tenanta aplikace potřebuje pouze hledat v jeho vlastní adresáře pro uživatele, zatímco aplikace s více tenanty je potřeba určit konkrétní uživatele ze všech adresářů ve službě Azure AD. Chcete-li provést tuto úlohu, Azure AD poskytuje společný koncový bod ověřování kde jakékoli aplikace s více tenanty může směrovat požadavky na přihlášení, místo konkrétního klienta endpoint. Tento koncový bod je https://login.microsoftonline.com/common pro všechny adresáře ve službě Azure AD, že koncový bod specifickým pro tenanta může být https://login.microsoftonline.com/contoso.onmicrosoft.com. Společný koncový bod je obzvláště důležité vzít v úvahu při vývoji vaší aplikace, protože je budete potřebovat logiku potřebnou pro zpracování více tenantů během přihlášení, odhlášení a ověření tokenu.

Pokud jsou aktuálně vývoj aplikace s jedním tenantem, ale mají být k dispozici pro mnoho organizací, můžete snadno provedete změny aplikace a jeho konfigurace ve službě Azure AD, aby více tenantů podporuje. Kromě toho Azure AD používá stejný podpisový klíč pro všechny tokeny ve všech adresářích, jestli poskytujete ověřování v jednom tenantovi nebo aplikace s více tenanty.

Každý scénář v tomto dokumentu obsahuje dílčí část, která popisuje požadavky na jeho zřizování. Podrobnější informace o zřizování aplikace ve službě Azure AD a jaký je rozdíl mezi aplikací jeden a více tenantů, naleznete v tématu [integrace aplikací s Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) Další informace. Pokračujte ve čtení o běžných scénářů aplikací ve službě Azure AD.

## <a name="next-steps"></a>Další postup

- Další informace o jiné služby Azure AD [základy ověřování](authentication-scenarios.md)
