---
title: Mapování uživatelských deklarací identity spolupráce B2B – Služba Azure Active Directory
description: Přizpůsobte deklarace identity uživatelů, které jsou vydány v tokenu SAML pro uživatele služby Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273191"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapování deklarací identity pro spolupráci B2B ve službě Azure Active Directory

Azure Active Directory (Azure AD) podporuje přizpůsobení deklarací identity, které jsou vydané v tokenu SAML pro uživatele spolupráce B2B. Když se uživatel ověří v aplikaci, Azure AD vydá aplikaci token SAML, který obsahuje informace (nebo deklarace identity) o uživateli, který je jednoznačně identifikuje. Ve výchozím nastavení to zahrnuje uživatelské jméno uživatele, e-mailovou adresu, křestní jméno a příjmení.

Na [webu Azure Portal](https://portal.azure.com)můžete zobrazit nebo upravit deklarace identity, které jsou odesílány v tokenu SAML do aplikace. Chcete-li získat přístup k nastavení, vyberte aplikace **Azure Active Directory** > **Enterprise** > aplikaci, která je nakonfigurovaná pro jednotné přihlašování > **jednotné přihlašování**. Viz nastavení tokenu SAML v části **Atributy uživatele.**

![Zobrazuje atributy tokenu SAML v ui.](media/claims-mapping/view-claims-in-saml-token.png)

Existují dva možné důvody, proč může být nutné upravit deklarace identity vydané v tokenu SAML:

1. Aplikace vyžaduje jinou sadu identifikátorů URI deklarací nebo hodnot deklarací.

2. Aplikace vyžaduje NameIdentifier deklarace být něco jiného než hlavní název uživatele (UPN), který je uložen ve službě Azure AD.

Informace o přidávání a úpravách deklarací identity naleznete [v tématu Přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace ve službě Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Pro uživatele spolupráce B2B mapování NameID a UPN cross-tenant a jsou z bezpečnostních důvodů zabráněno.

## <a name="next-steps"></a>Další kroky

- Informace o vlastnostech uživatele spolupráce B2B naleznete [v tématu Vlastnosti uživatele spolupráce služby Azure Active Directory B2B](user-properties.md).
- Informace o uživatelských tokenech pro uživatele spolupráce B2B najdete [v tématu Principy uživatelských tokenů ve spolupráci azure ad b2b](user-token.md).

