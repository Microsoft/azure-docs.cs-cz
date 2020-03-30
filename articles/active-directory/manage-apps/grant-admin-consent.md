---
title: Udělit souhlas správce pro celý klient s aplikací – Azure AD
description: Zjistěte, jak udělit souhlas celého klienta s aplikací, aby koncoví uživatelé nebyli při přihlášení k aplikaci vyzváni k udělení souhlasu.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480915"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Udělení souhlasu správce pro celý klient s aplikací

Zjistěte, jak zjednodušit uživatelské prostředí udělením souhlasu správce pro celý klient k aplikaci. Tento článek poskytuje různé způsoby, jak toho dosáhnout. Metody platí pro všechny koncové uživatele ve vašem tenantovi Azure Active Directory (Azure AD).

Další informace o souhlasu s aplikacemi najdete v [tématu Rozhraní pro souhlas služby Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Požadavky

Udělení souhlasu správce pro celý klient vyžaduje, abyste se [přihlásili](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)jako globální správce , [správce aplikací](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)nebo [správce cloudových aplikací](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Pokud byla aplikaci udělen souhlas správce pro celý klient, všichni uživatelé se budou moci přihlásit k aplikaci, pokud nebyla nakonfigurována tak, aby vyžadovala přiřazení uživatele. Chcete-li omezit, kteří uživatelé se mohou přihlásit k aplikaci, vyžadovat přiřazení uživatele a pak přiřadit uživatele nebo skupiny k aplikaci. Další informace naleznete [v tématu Metody přiřazování uživatelů a skupin](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> Udělení souhlasu správce pro celý klient aplikaci udělí aplikaci a vydavateli aplikace přístup k datům vaší organizace. Před udělením souhlasu pečlivě zkontrolujte oprávnění, která aplikace požaduje.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Udělení souhlasu správce z webu Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Udělení souhlasu správce v podnikových aplikacích

Souhlas správce pro celý klient můžete udělit prostřednictvím *podnikových aplikací,* pokud už byla aplikace zřízena ve vašem tenantovi. Například aplikace může být zřízena ve vašem tenantovi, pokud alespoň jeden uživatel již souhlasil s aplikací. Další informace najdete v tématu [Jak a proč se aplikace přidávají do služby Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Udělení souhlasu správce pro celý klient aplikaci uvedené v **podnikových aplikacích**:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [správce aplikací](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)nebo [správce cloudových aplikací](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Vyberte **Azure Active Directory** a pak podnikové **aplikace**.
3. Vyberte aplikaci, které chcete udělit souhlas správce pro celý klient.
4. Vyberte **Oprávnění** a klikněte na **Udělit souhlas správce**.
5. Pečlivě zkontrolujte oprávnění, která aplikace vyžaduje.
6. Pokud souhlasíte s oprávněními, která aplikace vyžaduje, udělte souhlas. Pokud ne, klepněte na **tlačítko Storno** nebo zavřete okno.

### <a name="grant-admin-consent-in-app-registrations"></a>Udělení souhlasu správce v registracích aplikací

Pro aplikace, které vaše organizace vyvinula nebo které jsou registrované přímo ve vašem tenantovi Azure AD, můžete také udělit souhlas správce pro celý klient z **registrací aplikací** na webu Azure Portal.

Udělení souhlasu správce pro celý klient z **registrací aplikací**:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako [globální správce](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [správce aplikací](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)nebo [správce cloudových aplikací](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Vyberte **Azure Active Directory** a pak registrace **aplikací**.
3. Vyberte aplikaci, které chcete udělit souhlas správce pro celý klient.
4. Vyberte **oprávnění rozhraní API** a klikněte na **Udělit souhlas správce**.
5. Pečlivě zkontrolujte oprávnění, která aplikace vyžaduje.
6. Pokud souhlasíte s oprávněními, která aplikace vyžaduje, udělte souhlas. Pokud ne, klepněte na **tlačítko Storno** nebo zavřete okno.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Vytvoření adresy URL pro udělení souhlasu správce pro celý klient

Při udělování souhlasu správce pro celý tenant a to pomocí obou výše popsaných metod se otevře okno z portálu Azure, které vyzve k udělení souhlasu správce pro celý klient. Pokud znáte ID klienta (označované také jako ID aplikace) aplikace, můžete vytvořit stejnou adresu URL, která udělí souhlas správce pro celý klient.

Adresa URL souhlasu správce pro celý klient se řídí následujícím formátem:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

kde:

* `{client-id}`je ID klienta aplikace (označované také jako ID aplikace).
* `{tenant-id}`je ID klienta vaší organizace nebo jakýkoli ověřený název domény.

Jako vždy pečlivě zkontrolujte oprávnění, která aplikace požaduje před udělením souhlasu.

## <a name="next-steps"></a>Další kroky

[Konfigurace způsobu, jakým koncoví uživatelé uzaměňují souhlas s aplikacemi](configure-user-consent.md)

[Konfigurace pracovního postupu souhlasu správce](configure-admin-consent-workflow.md)

[Oprávnění a souhlas v platformě microsoft identity](../develop/active-directory-v2-scopes.md)

[Azure AD na StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
