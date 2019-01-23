---
title: Konfigurace uživatelského spuštění aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak spravovat způsob, jakým uživatelé udělit souhlas s oprávnění aplikace. Činnost koncového uživatele můžete zjednodušit tím, že udělíte souhlas správce. Tyto metody se vztahují na všechny koncové uživatele ve vašem tenantovi Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 852188714a55fe461d0788e9f1926bcbf3dccc5e
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472700"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Nakonfigurovat způsob, jakým koncoví uživatelé souhlas k aplikaci v Azure Active Directory
Zjistěte, jak nakonfigurovat způsob, jakým uživatelé udělit souhlas s oprávnění aplikace. Činnost koncového uživatele můžete zjednodušit tím, že udělíte souhlas správce. Tento článek poskytuje různé způsoby, jak můžete nakonfigurovat souhlasu uživatele. Metody se vztahují na všechny koncové uživatele ve vašem tenantovi Azure Active Directory (Azure AD). 

Další informace o souhlas aplikacím, najdete v části [rozhraní Azure Active Directory pro udělování souhlasu](../develop/consent-framework.md).

## <a name="prerequisites"></a>Požadavky

Udělení souhlasu správce vyžaduje, abyste se přihlásit jako globální správce, správce aplikace nebo správce cloudové aplikace.

Pokud chcete omezit přístup k aplikacím, budete muset vyžadují přiřazení uživatelů a pak přiřaďte uživatele nebo skupiny k aplikaci.  Další informace najdete v tématu [metody pro přiřazení uživatelům a skupinám](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Udělit souhlas správce pro firemní aplikace na webu Azure Portal

Chcete-li udělit souhlas správce pro podnikové aplikace:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. Klikněte na tlačítko **všechny služby** v horní navigační nabídce vlevo. **Rozšíření Azure Active Directory** otevře.
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte **Azure Active Directory** položky.
4. V navigační nabídce klikněte na **podnikové aplikace**.
5. Klikněte na tlačítko **udělit souhlas správce**. Budete vyzváni k přihlášení k administraci aplikace.
6. Přihlaste se pomocí účtu, který má oprávnění udělit souhlas správce pro aplikaci. 
7. Souhlas s oprávnění aplikace.

Tato možnost funguje jenom v případě aplikace je: 

- Zaregistrovat ve vašem tenantovi nebo
- Zaregistrovaný v jiném tenantovi Azure AD a odsouhlasený minimálně jeden koncovým uživatelem. Jakmile se koncový uživatel vyjádřil souhlas aplikací, Azure AD jsou uvedeny v testované aplikaci **podnikových aplikací** na webu Azure Portal.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Udělit souhlas správce při registraci aplikace na webu Azure Portal

Chcete-li udělit souhlas správce při registraci aplikace: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Přejděte **registrace aplikací** okno.
3. Vyberte žádost o souhlas.
4. Vyberte **požadovaná oprávnění**.
5. Klikněte na tlačítko **udělit oprávnění** v horní části okna.


## <a name="grant-admin-consent-through-a-url-request"></a>Udělit souhlas správce prostřednictvím adresy URL požadavku

Chcete-li udělit souhlas správce prostřednictvím adresy URL požadavku:

1. Vytvořit žádost o *login.microsoftonline.com* konfigurací aplikace a připojení na `&prompt=admin_consent`. 
2. Po přihlášení pomocí přihlašovacích údajů správce, aplikace byl udělen souhlas pro všechny uživatele.


## <a name="force-user-consent-through-a-url-request"></a>Platnost souhlasu uživatele prostřednictvím adresy URL požadavku

Chcete-li vyžadovat koncovým uživatelům udělit souhlas s aplikaci pokaždé, když se ověřit, přidejte `&prompt=consent` ověřování adresa URL požadavku.

## <a name="next-steps"></a>Další postup

[Vyjádření souhlasu a integraci aplikací do Azure AD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Videí k Azure AD v2.0 a vyjádření souhlasu konvergované aplikace](../develop/active-directory-v2-scopes.md)

[StackOverflow pro Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
