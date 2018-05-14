---
title: Deklaracemi identity aplikace - Proxy aplikace Azure AD | Microsoft Docs
description: Jak publikovat místní aplikace ASP.NET, které přijímat deklarace identity služby AD FS pro vaši uživatelé zabezpečený vzdálený přístup.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 74af858c433db2a178d5fa346315e3f8a2dbd4f2
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Práce s deklaracemi identity aplikace v Proxy aplikace
[Deklaracemi identity aplikace](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) provést přesměrování na Security Token Service (STS). Služba tokenů zabezpečení požadavky přihlašovacích údajů od uživatele za token a pak přesměruje uživatele na aplikaci. Existuje několik způsobů, jak povolit Proxy aplikace pro práci s těmito přesměrování. Tento článek slouží ke konfiguraci nasazení pro deklaracemi identity aplikace. 

## <a name="prerequisites"></a>Požadavky
Zkontrolujte, že služba tokenů zabezpečení, které aplikace deklaracemi přesměruje na je k dispozici mimo vaší místní síti. Můžete zpřístupnit službu STS vystavení přes proxy server nebo tím, že se mimo připojení. 

## <a name="publish-your-application"></a>Publikování aplikace

1. Publikování aplikace podle pokynů v tématu [publikování aplikací pomocí Proxy aplikace](manage-apps/application-proxy-publish-azure-portal.md).
2. Přejděte na stránku aplikace v portálu a vyberte **jednotného přihlašování**.
3. Pokud jste zvolili **Azure Active Directory** jako vaše **metoda předběžného ověření**, vyberte **Azure AD jednotné přihlašování zakázáno** jako vaše **interní metodu ověřování**. Pokud jste zvolili **průchozí** jako vaše **metoda předběžného ověření**, nemusíte nic nezmění.

## <a name="configure-adfs"></a>Konfigurace služby AD FS

Služba AD FS můžete nakonfigurovat pro deklaracemi identity aplikace v jednom ze dvou způsobů. První je pomocí vlastní domény. Druhá je s WS-Federation. 

### <a name="option-1-custom-domains"></a>Možnost 1: Vlastní domény

Pokud všechny interní adresy URL pro vaše aplikace jsou plně kvalifikované názvy domény (FQDN), pak můžete nakonfigurovat [vlastní domény](manage-apps/application-proxy-configure-custom-domain.md) pro vaše aplikace. Použijte vlastní domény k vytvoření externí adresy URL, které jsou stejné jako interní adresy URL. Pokud vaše externí adresy URL neodpovídají vaše interní adresy URL, přesměrování služby tokenů zabezpečení fungovat, zda jsou vaši uživatelé místní nebo vzdálené. 

### <a name="option-2-ws-federation"></a>Možnost 2: WS-Federation

1. Otevřete modul Správa služby AD FS.
2. Přejděte na **vztahy důvěryhodnosti předávající strany**, klikněte pravým tlačítkem na aplikaci, kterou publikujete pomocí Proxy aplikace a zvolte **vlastnosti**.  

   ![Vztahy důvěryhodnosti předávající strany, klikněte pravým tlačítkem na název aplikace – snímek obrazovky](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. Na **koncové body** v části **typ koncového bodu**, vyberte **WS-Federation**.
4. V části **důvěryhodné adresy URL**, zadejte adresu URL, které jste zadali v Proxy aplikace v rámci **externí adresu URL** a klikněte na tlačítko **OK**.  

   ![Přidání koncového bodu - nastavit důvěryhodné adresy URL hodnotu – snímek obrazovky](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Další postup
* [Povolit jednotné přihlašování na](manage-apps/application-proxy-single-sign-on.md) pro aplikace, které nejsou pracujícím s deklaracemi
* [Povolení nativního klienta pro interakci s proxy aplikace](active-directory-application-proxy-native-client.md)


