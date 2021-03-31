---
title: Mapování deklarací identity uživatelů spolupráce B2B – Azure Active Directory
description: Přizpůsobte deklarace identity uživatelů, které jsou vydány v tokenu SAML pro Azure Active Directory (Azure AD) uživatelé B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87908748"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapování deklarací identity uživatelů spolupráce B2B v Azure Active Directory

Azure Active Directory (Azure AD) podporuje přizpůsobení deklarací identity vystavených v tokenu SAML pro uživatele spolupráce B2B. Když se uživatel do aplikace ověří, služba Azure AD vydá token SAML pro aplikaci, která obsahuje informace (nebo deklarace identity) o uživateli, který je jednoznačně identifikuje. Ve výchozím nastavení sem patří uživatelské jméno, e-mailová adresa, jméno a příjmení.

V [Azure Portal](https://portal.azure.com)můžete zobrazit nebo upravit deklarace identity, které se odesílají v tokenu SAML do aplikace. Pro přístup k nastavení vyberte **Azure Active Directory**  >  **podnikové aplikace** > aplikaci, která je nakonfigurovaná pro jednotné přihlašování > **jednotné přihlašování**. Viz nastavení tokenu SAML v části **atributy uživatele** .

![Zobrazuje atributy tokenu SAML v uživatelském rozhraní.](media/claims-mapping/view-claims-in-saml-token.png)

Existují dva možné důvody, proč možná budete muset upravit deklarace identity, které se vydávají v tokenu SAML:

1. Aplikace vyžaduje jinou sadu identifikátorů URI deklarace identity nebo hodnot deklarací identity.

2. Aplikace vyžaduje, aby deklarace identity NameIdentifier byla jiná než hlavní název uživatele (UPN), který je uložený ve službě Azure AD.

Informace o tom, jak přidat a upravit deklarace identity, najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Pro uživatele spolupráce B2B se z bezpečnostních důvodů brání mapování NameID a mezi klienty hlavního názvu uživatele (UPN).

## <a name="next-steps"></a>Další kroky

- Informace o vlastnostech uživatele spolupráce B2B najdete v tématu [vlastnosti Azure Active Directoryho uživatele spolupráce B2B](user-properties.md).
- Informace o tokenech uživatelů pro uživatele spolupráce B2B najdete v tématu [vysvětlení tokenů uživatelů ve spolupráci Azure AD B2B](user-token.md).

