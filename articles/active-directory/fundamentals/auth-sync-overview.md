---
title: Přehled protokolu Azure Active Directory ověřování a synchronizace
description: Pokyny pro architekturu při dosahování tohoto vzoru ověřování
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114274"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Azure Active Directory integrace se staršími protokoly ověřování a synchronizace

Microsoft Azure Active Directory (Azure AD) umožňuje integraci s mnoha protokoly ověřování a synchronizace. Integrace ověřování vám umožní používat službu Azure AD a její funkce zabezpečení a správy s minimálními nebo žádnými změnami aplikací, které používají starší metody ověřování. Integrace synchronizace umožňuje synchronizovat data uživatelů a skupin do služby Azure AD a pak možnosti správy služby Azure AD. Některé způsoby synchronizace také umožňují automatizované zřizování.

## <a name="authentication-patterns"></a>Vzory ověřování

Následující tabulka uvádí vzory ověřování a jejich možnosti. Vyberte název vzorů ověřování, který chcete zobrazit.

* Podrobný popis

* Kdy ji použít

* Diagram architektury

* Vysvětlení systémových komponent

* Odkazy pro implementaci integrace

 

| Vzory ověřování| Authentication| Autorizace| Multi-factor Authentication| Podmíněný přístup |
| - |- | - | - | - |
| [Ověřování na základě hlaviček](auth-header-based.md)|![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [Ověřování protokolu LDAP](auth-ldap.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| | |  |
| [Ověřování OAuth 2,0](auth-oauth2.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [OIDC ověřování](auth-oidc.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [Ověřování pomocí jednotného přihlašování založené na heslech](auth-password-based-sso.md )| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [Ověřování RADIUS]( auth-radius.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| | ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [Služby Brána vzdálené plochy](auth-remote-desktop-gateway.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [Ověřování SAML](auth-saml.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |
| [Ověřování systému Windows – omezené delegování protokolu Kerberos](auth-kcd.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Vzorce synchronizace

Následující tabulka uvádí způsoby synchronizace a jejich možnosti. Vyberte název vzoru, který chcete zobrazit.

* Podrobný popis

* Kdy ji použít

* Diagram architektury

* Vysvětlení systémových komponent

* Odkazy pro implementaci integrace



| Vzor synchronizace| Synchronizace adresářů| Zřizování uživatelů |
| - | - | - |
| [Synchronizace adresářů](sync-directory.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)|  |
| [Synchronizace protokolu LDAP](sync-ldap.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)|  |
| [Synchronizace SCIM](sync-scim.md)| ![značka zaškrtnutí](./media/authentication-patterns/check.png)| ![značka zaškrtnutí](./media/authentication-patterns/check.png) |

