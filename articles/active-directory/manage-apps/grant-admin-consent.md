---
title: Udělení souhlasu správce pro celé tenanta aplikaci – Azure AD
description: Naučte se, jak udělit aplikaci souhlas v celé klientovi, aby se koncovým uživatelům při přihlašování k aplikaci nezobrazovaly žádné výzvy k souhlasu.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374178"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Udělení souhlasu správce v rámci celého tenanta aplikaci

  Přečtěte si, jak udělit aplikaci souhlas správce na úrovni tenanta. Tento článek popisuje různé způsoby, jak toho dosáhnout.

Další informace o tom, jak se předávají aplikacím, najdete v tématu [Azure Active Directory souhlasu Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Požadavky

Udělení souhlasu správce pro celé tenanta vyžaduje, abyste se přihlásili jako uživatel, který je oprávněn k souhlasu jménem organizace. To zahrnuje správce [globálních správců](../roles/permissions-reference.md#global-administrator) a [privilegovaných rolí](../roles/permissions-reference.md#privileged-role-administrator)a pro některé aplikace, správce [aplikací](../roles/permissions-reference.md#application-administrator) a [správce cloudových aplikací](../roles/permissions-reference.md#cloud-application-administrator). Uživatel může být také autorizován pro udělení souhlasu v rámci tenanta, pokud se jim přiřadí [role vlastního adresáře](../roles/custom-create.md) , která zahrnuje [oprávnění pro udělení oprávnění aplikacím](../roles/custom-consent-permissions.md).

> [!WARNING]
> Udělení souhlasu správce na úrovni tenanta aplikaci udělí aplikaci a vydavateli aplikace přístup k datům vaší organizace. Pečlivě zkontrolujte oprávnění, která aplikace požaduje před udělením souhlasu.

> [!IMPORTANT]
> Pokud se aplikaci udělí souhlas správce na úrovni tenanta, všichni uživatelé se budou moct k aplikaci přihlásit, pokud není nakonfigurovaná tak, aby vyžadovala přiřazení uživatele. Chcete-li omezit, kteří uživatelé se mohou přihlašovat k aplikaci, vyžadovat přiřazení uživatele a pak přiřadit uživatele nebo skupiny k aplikaci. Další informace najdete v tématu [metody přiřazení uživatelů a skupin](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Udělit souhlas správce od Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Udělení souhlasu správce v podnikových aplikacích

Můžete udělit souhlas správce na úrovni tenanta prostřednictvím *podnikových aplikací* , pokud už je aplikace ve vašem tenantovi zřízená. Aplikaci můžete například zřídit ve vašem tenantovi, pokud aspoň jeden uživatel již souhlasil s aplikací. Další informace najdete v tématu [jak a proč se aplikace přidávají do Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Udělení souhlasu správce na úrovni tenanta k aplikaci uvedené v **podnikových aplikacích**:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../roles/permissions-reference.md#global-administrator), [správce aplikace](../roles/permissions-reference.md#application-administrator)nebo [správce cloudové aplikace](../roles/permissions-reference.md#cloud-application-administrator).
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

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../roles/permissions-reference.md#global-administrator), [správce aplikace](../roles/permissions-reference.md#application-administrator)nebo [správce cloudové aplikace](../roles/permissions-reference.md#cloud-application-administrator).
2. Vyberte **Azure Active Directory** pak **Registrace aplikací**.
3. Vyberte aplikaci, pro kterou chcete udělit souhlas správce na úrovni tenanta.
4. Vyberte **oprávnění rozhraní API** a pak klikněte na **udělit souhlas správce**.
5. Pečlivě zkontrolujte oprávnění, která aplikace vyžaduje.
6. Pokud souhlasíte s oprávněními, které aplikace vyžaduje, udělte souhlas. V takovém případě klikněte na tlačítko **Storno** nebo zavřete okno.

> [!WARNING]
> Udělení souhlasu správce na úrovni tenanta prostřednictvím **Registrace aplikací** odvolá všechna oprávnění, která byla dříve udělena v rámci tenanta. Oprávnění, která byla dříve udělena uživateli na jejich vlastním jménem, nebudou ovlivněna. 

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
> Udělení souhlasu správce na úrovni tenanta prostřednictvím této adresy URL odvolá všechna oprávnění, která byla dříve udělena v rámci tenanta. Oprávnění, která byla dříve udělena uživateli na jejich vlastním jménem, nebudou ovlivněna. 

## <a name="next-steps"></a>Další kroky

[Konfigurace způsobu vyjadřování souhlasu koncových uživatelů s aplikacemi](configure-user-consent.md)

[Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)

[Oprávnění a souhlas na platformě Microsoft identity](../develop/v2-permissions-and-consent.md)

[Azure AD v Microsoft Q&A](/answers/topics/azure-active-directory.html)
