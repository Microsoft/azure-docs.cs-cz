---
title: Ověřování OAUTH 2,0 s Azure Active Directory
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
ms.openlocfilehash: ea22c4e5b363eaa3ecc2a736dfef714666310062
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114211"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Ověřování OAuth 2,0 s Azure Active Directory

OAuth 2,0 je průmyslový protokol pro autorizaci. Umožňuje uživateli udělit omezený přístup k chráněným prostředkům. Služba OAuth je navržená tak, aby fungovala speciálně s protokolem HTTP (Hypertext Transfer Protocol), odděluje roli klienta od vlastníka prostředku. Klient požaduje přístup k prostředkům řízeným vlastníkem prostředku a hostovaným serverem prostředků. Server prostředků vydá přístupové tokeny se schválením vlastníka prostředku. Klient používá přístupové tokeny pro přístup k chráněným prostředkům hostovaným serverem prostředků. 

OAuth 2,0 přímo souvisí s OpenID Connect (OIDC). Vzhledem k tomu, že OIDC je vrstva ověřování a autorizace postavená na OAuth 2,0, není zpětně kompatibilní s OAuth 1,0. Azure Active Directory (Azure AD) podporuje všechny toky OAuth 2,0. 

## <a name="use-when"></a>Použijte, když:

Pro obsáhlý klient & scénáře moderních aplikací a přístup k webovému rozhraní API RESTful.

![Diagram architektury](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: vyžádá službu z webové aplikace (aplikace). Uživatel je obvykle vlastníkem prostředku, který vlastní data a má oprávnění k tomu, aby klienti měli přístup k datům nebo prostředkům. 

* **Webový prohlížeč**: webový prohlížeč, se kterým uživatel pracuje, je klient OAuth. 

* **Webová aplikace**: webová aplikace nebo server prostředků, kde se nachází prostředek nebo data. Důvěřuje autorizačnímu serveru za účelem bezpečného ověřování a autorizace klienta OAuth. 

* **Azure AD**: Azure AD je autorizační Server, označovaný také jako zprostředkovatel identity (IDP). Bez bezpečného zpracování informací o uživatelích, jejich přístupu a vztahu důvěryhodnosti. Zodpovídá za vydávání tokenů, které udělují a odvolává přístup k prostředkům.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementace OAuth 2,0 s Azure AD

* [Integrace aplikací s Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Protokoly OAuth 2,0 a OpenID Connect na platformě Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Typy aplikací a OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
