---
title: Mapování v Azure Active Directory deklarace identity uživatele spolupráce B2B | Microsoft Docs
description: Přizpůsobte deklarace identity uživatelů, které jsou vystavené v tokenu SAML pro uživatele B2B Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f2617fb79fc20fcb385c7e2fc3596245ab18caba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapování v Azure Active Directory deklarace identity uživatele spolupráce B2B

Přizpůsobují se deklarace identity, které jsou vystavené v tokenu SAML pro uživatele spolupráce B2B Azure podporuje služby Active Directory (Azure AD). Když se uživatel přihlásí k aplikaci, Azure AD vydá SAML token na aplikaci, která obsahuje informace (nebo deklarace identity) o uživatele, který jednoznačně identifikuje je. Ve výchozím nastavení jedná se o uživatelské jméno, e-mailová adresa, jméno a příjmení uživatele.

V [portál Azure](https://portal.azure.com), můžete zobrazit nebo upravit deklarace identity, které se odesílají v tokenu SAML k aplikaci. Pro přístup k nastavení, vyberte **Azure Active Directory** > **podnikové aplikace, které** > aplikaci, která je nakonfigurována pro jednotné přihlašování > **jednotného přihlašování** . Najdete v části nastavení tokenu SAML v **uživatelské atributy** části.

![Zobrazuje atributy tokenu SAML v uživatelském rozhraní](media/claims-mapping/view-claims-in-saml-token.png)

Existují dvě možné důvody, proč je potřeba upravit deklarace identity, které jsou vystavené v tokenu SAML:

1. Aplikace vyžaduje jinou sadu identifikátory URI nebo hodnoty deklarací.

2. Aplikace vyžaduje NameIdentifier deklarace identity na něco jiného než hlavní název uživatele (UPN), uložená ve službě Azure AD.

Informace o tom, jak přidávat a upravovat deklarací identity najdete v tématu [přizpůsobení deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Pro spolupráci B2B uživatelů, mapování NameID a UPN mezi klienta nelze z bezpečnostních důvodů.

## <a name="next-steps"></a>Další postup

- Informace o vlastnosti uživatele spolupráce B2B najdete v tématu [vlastnosti uživatele spolupráce Azure Active Directory s B2B](user-properties.md).
- Informace o tokeny uživatele pro uživatele spolupráce B2B najdete v tématu [pochopit tokeny uživatele v spolupráce Azure AD B2B](user-token.md).

