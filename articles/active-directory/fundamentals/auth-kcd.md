---
title: Omezené delegování protokolu Kerberos s Azure Active Directory
description: Pokyny pro architekturu k dosažení omezeného delegování protokolu Kerberos pomocí Azure Active Directory.
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
ms.openlocfilehash: a1c7aa4d2300a6dee44da067b122fc7af97f7aa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172852"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Ověřování systému Windows – omezené delegování protokolu Kerberos pomocí Azure Active Directory

Omezené delegování protokolu Kerberos (KCD) zajišťuje omezené delegování mezi prostředky a je založeno na názvech principů služby. Správce domény musí vytvořit delegování a je omezený na jedinou doménu. KCD založené na prostředku se často používají jako způsob poskytování ověřování protokolem Kerberos pro webovou aplikaci, která má uživatele ve více doménách v doménové struktuře služby Active Directory.

Proxy aplikací služby Azure Active Directory může poskytovat jednotné přihlašování (SSO) a vzdálený přístup k aplikacím založeným na KCD, které vyžadují lístek protokolu Kerberos pro přístup a omezené delegování protokolu Kerberos (KCD).

Připnete jednotné přihlašování k místním aplikacím KCD, které používají integrované ověřování systému Windows (IWA), přidělením oprávnění konektorům proxy aplikací k zosobnění uživatelů ve službě Active Directory. Konektor proxy aplikací používá toto oprávnění k posílání a přijímání tokenů jménem uživatelů.

## <a name="use-when"></a>Kdy použít

Je potřeba zajistit vzdálený přístup, chránit před předběžným ověřováním a poskytovat jednotné přihlašování k místním aplikacím IWA.

![Diagram architektury](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: přistupuje k starší aplikaci, kterou obsluhuje proxy aplikace.

* **Webový prohlížeč**: komponenta, s jakou uživatel pracuje, aby mohla přistupovat k externí adrese URL aplikace.

* **Azure AD**: ověřuje uživatele. 

* **Služba proxy aplikací**: slouží jako reverzní proxy server k odeslání požadavku od uživatele do místní aplikace. Je umístěný ve službě Azure AD. Proxy aplikací může taky vyhovět všem zásadám podmíněného přístupu.

* **Konektor proxy aplikací**: nainstalovaný místně na serverech Windows pro zajištění připojení k aplikaci. Vrátí odpověď do služby Azure AD. Provádí vyjednávání KCD se službou Active Directory a zosobňuje uživatele k získání tokenu protokolu Kerberos do aplikace.

* **Active Directory**: odešle token protokolu Kerberos pro aplikaci konektoru proxy aplikací.

* **Starší verze aplikací**: aplikace, které přijímají požadavky uživatelů od proxy aplikace. Starší verze aplikací vrátí odpověď konektoru proxy aplikací.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Implementace ověřování systému Windows (KCD) s Azure AD

* [Omezené delegování Kerberos pro jednotné přihlašování k aplikacím pomocí Proxy aplikací](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) 

* [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md)

