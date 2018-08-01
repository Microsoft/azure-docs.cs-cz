---
title: Správa jednotného přihlašování pro Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Další informace o základní informace o jednotné přihlašování s Proxy aplikací
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: dbdbe8b83af20f66ad2cc99d2a5665262479b4a9
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364144"
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Jak Proxy aplikací Azure AD poskytuje jednotné přihlašování?

Jednotné přihlašování je klíčovým prvkem Proxy aplikací Azure AD.  Poskytuje nejlepší uživatelské prostředí, protože pouze mají vaši uživatelé k přihlášení k Azure Active Directory v cloudu. Jakmile ověřování do Azure Active Directory, konektor Proxy aplikací zpracovávat ověřování pro místní aplikace. Back-end aplikace nemůže zjistit rozdíl mezi vzdáleného uživatele přihlášení prostřednictvím Proxy aplikací a běžným používáním na zařízení připojeném k doméně. 

Použití Azure Active Directory pro jednotné přihlašování pro vaše aplikace, budete muset vybrat **Azure Active Directory** jako metoda předběžného ověřování. Pokud vyberete **průchozí** pak uživatelům není vůbec ověření do služby Azure Active Directory, ale jsou přesměrováni přímo na aplikaci. Toto nastavení můžete nakonfigurovat při prvním publikování aplikace, nebo přejděte do vaší aplikace na webu Azure Portal a upravit nastavení Proxy aplikací. 

Pokud chcete zobrazit vaše volby jednotného přihlašování, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace**.
3. Vyberte aplikaci, jejíž jednotné přihlašování možnosti chcete spravovat.
4. Vyberte **jednotného přihlašování**.

   ![Rozevírací nabídka jednotného přihlašování](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

V rozevírací nabídce zobrazuje pět možností pro jednotné přihlašování pro vaši aplikaci:

* Jednotné přihlašování Azure AD je zakázané.
* Přihlašování na základě hesla
* Propojené přihlašování
* Integrované ověřování systému Windows
* Přihlašování na základě záhlaví

## <a name="azure-ad-single-sign-on-disabled"></a>Jednotné přihlašování Azure AD je zakázané.

Pokud nechcete, aby k používání integrace služby Azure Active Directory pro jednotné přihlašování pro vaši aplikaci, zvolte **Azure AD jednotné přihlašování zakázáno**. Tato možnost aktivní mohou uživatelé dvojí ověření. Nejprve ověřování do Azure Active Directory a pak se přihlaste do vlastní aplikace. 

Tato možnost je vhodná, pokud vaše místní aplikace nevyžaduje, aby uživatelé k ověření, ale je třeba přidat Azure Active Directory jako vrstvu zabezpečení pro vzdálený přístup. 

## <a name="password-based-sign-on"></a>Přihlašování na základě hesla

Pokud chcete použít Azure Active Directory jako služba vault heslo pro místní aplikace, zvolte **přihlašování na základě heslo**. Tato možnost je vhodná, pokud vaše aplikace se ověřuje pomocí uživatelského jména a hesla pole se seznamem místo přístupové tokeny nebo záhlaví. Založené na heslech přihlašování uživatelé potřebovat k přihlášení k aplikaci při prvním času jsou k němu přístup. Po tomto poskytuje Azure Active Directory uživatelské jméno a heslo jménem uživatele. 

Informace o nastavení přihlašování na základě hesla najdete v tématu [heslo vaulting pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md).

## <a name="linked-sign-on"></a>Propojené přihlašování

Pokud již máte jedno řešení přihlašování – nastavení pro svoje místní identity, zvolte **propojené přihlašování**. Tato možnost umožňuje využít stávající řešení jednotného přihlašování Azure Active Directory, ale stále poskytuje uživatelům vzdálený přístup k aplikaci. 

Informace o propojené přihlašování (dříve označované jako existující jednotné přihlašování), najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

Pokud vaše místní aplikace používat integrované Authentication(IWA) Windows nebo pokud chcete použít protokol Kerberos omezené delegování (KCD) pro jednotné přihlašování, zvolte **integrované ověřování Windows**. Tato možnost uživatelé potřebují jenom k ověření do služby Azure Active Directory a pak konektor Proxy aplikací zosobňuje uživatele získat token protokolu Kerberos a přihlaste se k aplikaci. 

Informace o nastavení integrované ověřování Windows, naleznete v tématu [omezeného delegování protokolu Kerberos pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="header-based-sign-on"></a>Přihlašování na základě záhlaví 

Pokud vaše aplikace k ověřování používají hlavičky, zvolte **přihlašování na základě záhlaví**. Pomocí této možnosti uživatelům potřeba jenom ověřování Azure Active Directory. Partneři Microsoftu pomocí služby ověřování třetích stran volat PingAccess, který přeložit přístupový token Azure Active Directory na formát hlavičky pro aplikaci. 

Informace o nastavení ověřování založené na hlavičkách najdete v tématu [ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md).

## <a name="next-steps"></a>Další postup

- [Heslo vaulting pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Omezené delegování protokolu Kerberos pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md) 
