---
title: Aplikace pracující s deklaracemi – Aplikace Azure AD proxy | Microsoft Docs
description: Jak publikovat místní ASP.NET aplikace, které přijímají deklarace služby AD FS pro zabezpečený vzdálený přístup vašich uživatelů.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5c840722ae6b03a0b8a7fa44e5999e14730d4f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997592"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Práce s aplikacemi pracujícími s deklaracemi v proxy aplikací
[Aplikace pracující s deklaracemi](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) provádějí přesměrování na službu tokenů zabezpečení (STS). Služba STS požaduje přihlašovací údaje od uživatele v Exchangi pro token a pak uživatele přesměruje na aplikaci. Existuje několik způsobů, jak povolit proxy aplikace pro práci s těmito přesměrování. V tomto článku můžete nakonfigurovat nasazení pro aplikace pracující s deklaracemi. 

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že služba STS, na kterou pracují deklarace, je k dispozici mimo místní síť. Službu STS můžete zpřístupnit tím, že ji vystavíte prostřednictvím proxy serveru nebo pokud povolíte vnější připojení. 

## <a name="publish-your-application"></a>Publikování aplikace

1. Publikujte aplikaci podle pokynů popsaných v tématu [publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md).
2. Na portálu přejděte na stránku aplikace a vyberte **jednotné přihlašování**.
3. Pokud jste jako **metodu předběžného ověření** zvolili **Azure Active Directory** , vyberte jako **metodu interního ověřování** možnost **jednotné přihlašování Azure AD** . Pokud jste jako **metodu předběžného ověření** zvolili možnost **Passthrough** , nemusíte nic měnit.

## <a name="configure-adfs"></a>Konfigurace služby ADFS

Službu AD FS můžete nakonfigurovat pro aplikace pracující s deklaracemi jedním ze dvou způsobů. První je pomocí vlastních domén. Druhá je s WS-Federation. 

### <a name="option-1-custom-domains"></a>Možnost 1: vlastní domény

Pokud jsou všechny interní adresy URL pro vaše aplikace plně kvalifikované názvy domén (FQDN), můžete pro své aplikace nakonfigurovat [vlastní domény](application-proxy-configure-custom-domain.md) . Pomocí vlastních domén můžete vytvořit externí adresy URL, které jsou stejné jako interní adresy URL. Když vaše externí adresy URL odpovídají vašim interním adresám URL, pak přesměrování služby STS funguje bez ohledu na to, jestli jsou vaši uživatelé místní nebo vzdálené. 

### <a name="option-2-ws-federation"></a>Možnost 2: WS-Federation

1. Otevřete správu služby AD FS.
2. Přejděte na **vztahy důvěryhodnosti předávající strany**, klikněte pravým tlačítkem na aplikaci, kterou publikujete s proxy aplikace, a vyberte **vlastnosti**.  

   ![Vztahy důvěryhodnosti předávající strany klikněte pravým tlačítkem myši na název aplikace – snímek obrazovky](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Na kartě **koncové body** v části **Typ koncového bodu** vyberte **WS-Federation**.
4. V části **důvěryhodná adresa URL** zadejte adresu URL, kterou jste zadali v proxy aplikaci v oblasti **externí adresa URL** , a klikněte na **OK**.  

   ![Přidání koncového bodu – nastavit důvěryhodnou hodnotu adresy URL – snímek obrazovky](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Další kroky
* [Povolit nativním klientským aplikacím pracovat s proxy aplikacemi](application-proxy-configure-native-client-application.md)