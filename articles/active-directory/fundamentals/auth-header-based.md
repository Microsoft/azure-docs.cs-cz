---
title: Ověřování na základě hlaviček pomocí Azure Active Directory
description: Pokyny pro architekturu týkající se dosažení ověřování na základě hlaviček pomocí Azure Active Directory.
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
ms.openlocfilehash: 69e87fc919893a544f5d0b1b615a110f25486e57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168742"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Ověřování na základě hlaviček pomocí Azure Active Directory

Starší verze aplikací obvykle používají ověřování na základě hlaviček. V tomto scénáři se uživatel (nebo původc zprávy) ověřuje pro řešení zprostředkovatele identity. Zprostředkující řešení ověřuje uživatele a šíří požadovaná záhlaví HTTP (Hypertext Transfer Protocol) do cílové webové služby. Azure Active Directory (AD) podporuje tento model prostřednictvím služby proxy aplikace a integruje s dalšími řešeními síťového adaptéru. 

V našem řešení poskytuje proxy aplikací vzdálený přístup k aplikaci, ověřuje uživatele a předává hlavičky vyžadované aplikací. 

## <a name="use-when"></a>Kdy použít

Vzdálení uživatelé potřebují bezpečně jednotné přihlašování (SSO) do místních aplikací, které vyžadují ověřování na základě hlaviček.

![Ověřování na základě hlaviček bitové kopie architektury](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: přistupuje k starším aplikacím, které obsluhuje proxy aplikace.

* **Webový prohlížeč**: komponenta, s jakou uživatel pracuje, aby mohla přistupovat k externí adrese URL aplikace.

* **Azure AD**: ověřuje uživatele. 

* **Služba proxy aplikací**: slouží jako reverzní proxy server k odeslání požadavku od uživatele do místní aplikace. Je umístěný ve službě Azure AD a může taky vyhovět všem zásadám podmíněného přístupu.

* **Konektor proxy aplikací**: nainstalovaný místně na serverech Windows pro zajištění připojení k aplikacím. Používá pouze odchozí připojení. Vrátí odpověď do služby Azure AD.

* **Starší verze aplikací**: aplikace, které přijímají požadavky uživatelů od proxy aplikace. Starší verze aplikace obdrží požadované hlavičky HTTP k nastavení relace a vrácení odpovědi. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementace ověřování na základě hlaviček pomocí Azure AD

* [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikací v Azure AD](../manage-apps/application-proxy-add-on-premises-application.md)  

* [Ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací a PingAccess](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) 

* [Zabezpečení aplikací starší verze s využitím kontrolerů doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md)