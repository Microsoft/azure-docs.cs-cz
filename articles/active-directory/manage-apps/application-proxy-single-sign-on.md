---
title: Správa jednotného přihlašování pro proxy aplikace služby Azure AD | Microsoft Docs
description: Další informace o základní informace o jednotné přihlašování pomocí Proxy aplikace
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: barbkess
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1d31b11c3307cc2e54b91e68e1e1a3811ae2ef96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Jak Azure AD Application Proxy poskytovat jednotné přihlašování?

Jednotné přihlašování je klíčovým prvkem proxy aplikace služby Azure AD.  Poskytuje nejlepších výsledků, protože pouze mají vaši uživatelé se přihlásit k Azure Active Directory v cloudu. Po ověření do Azure Active Directory, konektor Proxy aplikace zpracovává ověřování do aplikace místně. Back-end aplikace nelze zjistit rozdíl mezi vzdáleného uživatele přihlásit pomocí Proxy aplikací a regulární použití na zařízení připojeném k doméně. 

Používání služby Azure Active Directory pro jednotné přihlašování pro vaše aplikace, budete muset vybrat možnost **Azure Active Directory** jako metoda předběžného ověřování. Pokud vyberete **průchozí** pak nemáte ověření do Azure Active Directory na všechny uživatele, ale jsou směrované přímo k aplikaci. Toto nastavení můžete nakonfigurovat při prvním publikování aplikace, nebo přejděte na aplikaci na portálu Azure a upravit nastavení Proxy aplikace. 

Pokud chcete zobrazit vaše volby jednotného přihlašování, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na **Azure Active Directory** > **podnikové aplikace, které** > **všechny aplikace**.
3. Vyberte aplikaci, jejíž přihlašování volby jednotného chcete spravovat.
4. Vyberte **jednotného přihlašování**.

   ![Jednotné přihlašování rozevírací nabídce](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

V rozevírací nabídce jsou pět možnosti pro jednotné přihlašování k vaší aplikaci:

* Jednotné přihlašování Azure AD je zakázané.
* Založené na heslech přihlášení
* Propojené přihlášení
* Integrované ověřování systému Windows
* Na základě záhlaví přihlášení

## <a name="azure-ad-single-sign-on-disabled"></a>Jednotné přihlašování Azure AD je zakázané.

Pokud nechcete použít integraci služby Azure Active Directory pro jednotné přihlašování k vaší aplikaci, vyberte **Azure AD jednotné přihlašování zakázáno**. Tuto možnost mohou uživatelé ověřit dvakrát. Nejprve ověření do Azure Active Directory a potom se přihlaste do vlastní aplikace. 

Tato možnost je vhodná, pokud vaše místní aplikace nevyžaduje uživatele bude možné ověřit, ale chcete přidat Azure Active Directory jako vrstva zabezpečení pro vzdálený přístup. 

## <a name="password-based-sign-on"></a>Založené na heslech přihlášení

Pokud chcete použít Azure Active Directory jako služba vault heslo pro místní aplikace, vyberte **založené na heslech přihlašování**. Tato možnost je vhodná, pokud vaše aplikace ověří uživatelské jméno a heslo pole se seznamem místo přístupové tokeny nebo hlavičky. S založené na heslech přihlašování uživatelé potřebovat pro přihlášení k aplikaci první čas přístup. Potom Azure Active Directory poskytuje uživatelské jméno a heslo jménem uživatele. 

Informace o nastavení založené na heslech přihlašování najdete v tématu [heslo vaulting pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-password-vaulting.md).

## <a name="linked-sign-on"></a>Propojené přihlášení

Pokud již máte jeden řešení přihlašování nastavení pro místních identit, zvolte **propojené přihlášení**. Tato možnost umožňuje využít existující řešení jednotného přihlašování k Azure Active Directory, ale stále bude uživatelům vzdálený přístup k aplikaci. 

Informace o propojené přihlášení (dříve označované jako existující jednotné přihlašování) najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

Pokud používáte integrované Authentication(IWA) Windows místním aplikacím nebo pokud chcete použít protokol Kerberos vynuceným delegování použitím (KCD) pro jednotné přihlašování, zvolte **integrované ověřování systému Windows**. Tato možnost uživatelé potřebují pouze k ověření služby Azure Active Directory a potom konektor Proxy aplikace zosobňuje uživatele získat token protokolu Kerberos a přihlaste se k aplikaci. 

Informace o nastavení integrované ověřování systému Windows najdete v tématu [omezené delegování Kerberos pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="header-based-sign-on"></a>Na základě záhlaví přihlášení 

Pokud vaše aplikace používat hlavičky pro ověřování, zvolte **na základě záhlaví přihlašování**. Tato možnost uživatelé třeba pouze ověřování Azure Active Directory. Partneři společnosti Microsoft službou ověřování třetích stran volá PingAccess, který přeložit přístupový token služby Azure Active Directory na formát hlavičky pro aplikaci. 

Informace o nastavení ověřování na základě záhlaví najdete v tématu [ověřování na základě záhlaví pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-ping-access.md).

## <a name="next-steps"></a>Další postup

- [Heslo vaulting pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Omezené delegování Kerberos pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ověřování na základě záhlaví pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-ping-access.md) 
