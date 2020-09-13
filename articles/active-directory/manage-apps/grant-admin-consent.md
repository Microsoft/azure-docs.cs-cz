---
title: Udělení souhlasu správce pro celé tenanta aplikaci – Azure AD
description: Naučte se, jak udělit aplikaci souhlas v celé klientovi, aby se koncovým uživatelům při přihlašování k aplikaci nezobrazovaly žádné výzvy k souhlasu.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: aafaeb1143049b14f0a2fe2d867a951355d1ba61
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667591"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Udělení souhlasu správce v rámci celého tenanta aplikaci

Naučte se, jak zjednodušit uživatelské prostředí tím, že aplikaci udělíte souhlas správce pro celé tenanta. Tento článek popisuje různé způsoby, jak toho dosáhnout. Metody se vztahují na všechny koncové uživatele v tenantovi Azure Active Directory (Azure AD).

Další informace o tom, jak se předávají aplikacím, najdete v tématu [Azure Active Directory souhlasu Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Požadavky

Udělení souhlasu správce pro celé tenanta vyžaduje, abyste se přihlásili jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [správce aplikace](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)nebo [správce cloudové aplikace](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Pokud se aplikaci udělí souhlas správce na úrovni tenanta, všichni uživatelé se budou moct k aplikaci přihlásit, pokud není nakonfigurovaná tak, aby vyžadovala přiřazení uživatele. Chcete-li omezit, kteří uživatelé se mohou přihlašovat k aplikaci, vyžadovat přiřazení uživatele a pak přiřadit uživatele nebo skupiny k aplikaci. Další informace najdete v tématu [metody přiřazení uživatelů a skupin](methods-for-assigning-users-and-groups.md).
>
> Role globálního správce je nutná k poskytnutí souhlasu správce pro oprávnění aplikace Microsoft Graph API.

> [!WARNING]
> Udělení souhlasu správce na úrovni tenanta aplikaci udělí aplikaci a vydavateli aplikace přístup k datům vaší organizace. Pečlivě zkontrolujte oprávnění, která aplikace požaduje před udělením souhlasu.
>
> Role globálního správce je nutná k poskytnutí souhlasu správce pro oprávnění aplikace Microsoft Graph API.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Udělit souhlas správce od Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Udělení souhlasu správce v podnikových aplikacích

Můžete udělit souhlas správce na úrovni tenanta prostřednictvím *podnikových aplikací* , pokud už je aplikace ve vašem tenantovi zřízená. Aplikaci můžete například zřídit ve vašem tenantovi, pokud aspoň jeden uživatel již souhlasil s aplikací. Další informace najdete v tématu [jak a proč se aplikace přidávají do Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Udělení souhlasu správce na úrovni tenanta k aplikaci uvedené v **podnikových aplikacích**:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [správce aplikace](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)nebo [správce cloudové aplikace](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Vyberte **Azure Active Directory** **podnikové aplikace**.
3. Vyberte aplikaci, pro kterou chcete udělit souhlas správce na úrovni tenanta.
4. Vyberte **oprávnění** a pak klikněte na **udělit souhlas správce**.
5. Pečlivě zkontrolujte oprávnění, která aplikace vyžaduje.
6. Pokud souhlasíte s oprávněními, které aplikace vyžaduje, udělte souhlas. V takovém případě klikněte na tlačítko **Storno** nebo zavřete okno.

> [!WARNING]
> Udělení souhlasu správce na úrovni tenanta prostřednictvím **podnikových aplikací** odvolá všechna oprávnění, která byla dříve udělena v rámci tenanta. Oprávnění, která byla dříve udělena uživateli na jejich vlastním jménem, nebudou ovlivněna. 

### <a name="grant-admin-consent-in-app-registrations"></a>Udělení souhlasu správce v Registrace aplikací

Pro aplikace, které vaše organizace vyvinula nebo které jsou zaregistrované přímo ve vašem tenantovi Azure AD, můžete taky udělit souhlas správce na úrovni tenanta od **Registrace aplikací** Azure Portal.

Udělení souhlasu správce na úrovni tenanta od **Registrace aplikací**:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [správce aplikace](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)nebo [správce cloudové aplikace](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Vyberte **Azure Active Directory** pak **Registrace aplikací**.
3. Vyberte aplikaci, pro kterou chcete udělit souhlas správce na úrovni tenanta.
4. Vyberte **oprávnění rozhraní API** a pak klikněte na **udělit souhlas správce**.
5. Pečlivě zkontrolujte oprávnění, která aplikace vyžaduje.
6. Pokud souhlasíte s oprávněními, které aplikace vyžaduje, udělte souhlas. V takovém případě klikněte na tlačítko **Storno** nebo zavřete okno.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Vytvoření adresy URL pro udělení souhlasu správce na úrovni tenanta

Při udělování souhlasu správce na úrovni tenanta pomocí výše popsané metody se otevře okno z Azure Portal, ve kterém se zobrazí výzva k souhlasu správce na úrovni tenanta. Pokud znáte ID klienta (označované také jako ID aplikace) aplikace, můžete vytvořit stejnou adresu URL pro udělení souhlasu správce na úrovni tenanta.

Adresa URL souhlasu správce na úrovni tenanta má následující formát:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

kde:

* `{client-id}` je ID klienta aplikace (označované také jako ID aplikace).
* `{tenant-id}` je ID tenanta nebo libovolný ověřený název domény vaší organizace.

Jako vždy pečlivě zkontrolujte oprávnění, která aplikace požaduje, před udělením souhlasu.

> [!WARNING]
> Udělení souhlasu správce na úrovni tenanta prostřednictvím této adresy URL odvolá všechna oprávnění, která byla dříve udělena v rámci tenanta. Oprávnění, která byla dříve ggranted uživateli na jejich vlastním jménem, nebudou ovlivněna. 

## <a name="next-steps"></a>Další kroky

[Konfigurace způsobu vyjadřování souhlasu koncových uživatelů s aplikacemi](configure-user-consent.md)

[Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)

[Oprávnění a souhlas na platformě Microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD v StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
