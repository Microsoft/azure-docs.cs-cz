---
title: Uživatele spolupráce B2B mapování deklarací v Azure Active Directory | Dokumentace Microsoftu
description: Přizpůsobení deklarací identity uživatele, které jsou vystavených v tokenu SAML pro uživatele Azure Active Directory (Azure AD) B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 7a06afd7e6e295510f393177d9828fe6dd7a3c0e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428899"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Uživatele spolupráce B2B mapování deklarací v Azure Active Directory

Přizpůsobování deklarací identity, které jsou vystavených v tokenu SAML pro uživatele spolupráce B2B Azure Active Directory (Azure AD) podporuje. Když se uživatel přihlásí k aplikaci, Azure AD vydá SAML token aplikaci, která obsahuje informace (nebo deklarace identity) informace o uživateli, který jednoznačně identifikuje je. Ve výchozím nastavení to zahrnuje uživatelské jméno, e-mailová adresa, jméno a příjmení uživatele.

V [webu Azure portal](https://portal.azure.com), při prohlížení nebo úpravách deklarace identity, které se odesílají v tokenu SAML pro aplikaci. Chcete-li získat přístup k nastavení, vyberte **Azure Active Directory** > **podnikové aplikace** > aplikaci, která je nakonfigurována pro jednotné přihlašování > **jednotného přihlašování** . Podívejte se na nastavení tokenu SAML v **atributy uživatele** části.

![V uživatelském rozhraní jsou uvedeny atributy tokenu SAML](media/claims-mapping/view-claims-in-saml-token.png)

Existují dva možné důvody, proč může být potřeba upravit deklarace, které jsou vystavených v tokenu SAML:

1. Aplikace vyžaduje jinou sadu identifikátorů URI nebo hodnoty deklarací.

2. Aplikace vyžaduje deklarace identity NameIdentifier na něco jiného než hlavní název uživatele (UPN), která je uložená ve službě Azure AD.

Informace o tom, jak přidávat a upravovat deklarací identity najdete v tématu [přizpůsobování deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Spolupráci B2B z bezpečnostních důvodů nemají uživatelé mapování NameID a hlavní název uživatele mezi tenanty.

## <a name="next-steps"></a>Další postup

- Informace o vlastnosti uživatele spolupráce B2B najdete v tématu [vlastnosti uživatele spolupráce Azure Active Directory s B2B](user-properties.md).
- Informace o tokeny uživatele pro uživatele spolupráce B2B najdete v tématu [pochopit tokeny uživatele v Azure AD B2B collaboration](user-token.md).

