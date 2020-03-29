---
title: Aplikace podporující deklarace identity – proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Jak publikovat místní ASP.NET aplikace, které přijímají deklarace identity Služby ADFS pro zabezpečený vzdálený přístup uživatelů.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbc5c356ea5a542fdc12b11aff236c56b146b3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68477239"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Práce s aplikacemi pracujícími s deklaracemi v proxy aplikaci
[Aplikace podporující deklarace identity](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) provádějí přesměrování na službu tokenů zabezpečení (STS). STS požaduje pověření od uživatele výměnou za token a potom přesměruje uživatele do aplikace. Existuje několik způsobů, jak povolit proxy aplikace pro práci s těmito přesměrováními. Tento článek slouží ke konfiguraci nasazení pro aplikace podporující deklarace identity. 

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že STS, které aplikace podporující deklarace identity přesměruje na je k dispozici mimo místní síť. Můžete zpřístupnit STS vystavením prostřednictvím proxy serveru nebo povolením externích připojení. 

## <a name="publish-your-application"></a>Publikování aplikace

1. Publikujte svou žádost podle pokynů popsaných v části [Publikovat aplikace pomocí proxy aplikace](application-proxy-add-on-premises-application.md).
2. Přejděte na stránku aplikace na portálu a vyberte **Možnost Jednotné přihlašování**.
3. Pokud jste jako **metodu předběžného ověřování** **zvolili Službu Azure Active Directory** , vyberte možnost Jednotné **přihlašování Azure AD jako zakázaná** jako **metoda interního ověřování**. Pokud jste jako **metodu předběžného ověřování**zvolili **passthrough** , nemusíte nic měnit.

## <a name="configure-adfs"></a>Konfigurace služby ADF

Službu ADFS pro aplikace podporující deklarace identity můžete nakonfigurovat jedním ze dvou způsobů. První je pomocí vlastních domén. Druhý je s WS-Federation. 

### <a name="option-1-custom-domains"></a>Možnost 1: Vlastní domény

Pokud jsou všechny interní adresy URL pro vaše aplikace plně kvalifikované názvy domén (Plně kvalifikované domény), můžete pro své aplikace nakonfigurovat [vlastní domény.](application-proxy-configure-custom-domain.md) Vlastní domény slouží k vytváření externích adres URL, které jsou stejné jako interní adresy URL. Pokud se externí adresy URL shodují s interními adresami URL, přesměrování STS fungují bez ohledu na to, zda jsou uživatelé místní nebo vzdálení. 

### <a name="option-2-ws-federation"></a>Možnost 2: WS-Federace

1. Otevřete správu adfs.
2. Přejděte na **seznam důvěryhodných certifikátů předávající strany**, klikněte pravým tlačítkem myši na aplikaci, kterou publikujete pomocí proxy aplikace, a zvolte **Vlastnosti**.  

   ![Předávající strana trusty pravým tlačítkem myši na název aplikace - snímek obrazovky](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Na kartě **Koncové body** v části **Typ koncového bodu**vyberte **ws-federation**.
4. V části **Důvěryhodná adresa URL**zadejte adresu URL, kterou jste zadali do proxy aplikace v části **Externí adresa URL,** a klepněte na **tlačítko OK**.  

   ![Přidání koncového bodu – nastavit hodnotu důvěryhodné adresy URL – snímek obrazovky](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Další kroky
* [Povolení jednotného přihlášení](configure-single-sign-on-non-gallery-applications.md) pro aplikace, které neupozorní na deklarace identity
* [Povolení interakce s aplikacemi proxy nativními klientskými aplikacemi](application-proxy-configure-native-client-application.md)


