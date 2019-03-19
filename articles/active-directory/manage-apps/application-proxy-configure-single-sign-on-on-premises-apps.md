---
title: SAML jednotného přihlašování pro místní aplikace využívající službu Azure Active Directory Application Proxy (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak poskytovat jednotné přihlašování pro místní aplikace publikované prostřednictvím Proxy aplikací, které jsou zabezpečené pomocí ověřování SAML.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef5679ba2a6a62955f5402e8bfaa4f1884df722d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840592"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML jednotného přihlašování k místním aplikacím přes Proxy aplikací (Preview)

Můžete zadat jednotné přihlašování (SSO) k místním aplikacím, které jsou zabezpečené pomocí ověřování SAML a poskytnout vzdálený přístup pro tyto aplikace prostřednictvím Proxy aplikací. Azure Active Directory (Azure AD) se ověřuje pomocí SAML jednotného přihlašování, k aplikaci pomocí účtu uživatele Azure AD. Azure AD komunikuje informace přihlašování k aplikaci pomocí připojení protokolu. Můžete také mapování uživatelů na konkrétní aplikační role na základě pravidel, které definujete váš SAML deklaracemi identity. Povolením Application Proxy kromě SAML SSO budou mít uživatelé externího přístupu k aplikaci a bezproblémové prostředí jednotného přihlašování.

Aplikace musí umět používat vydané tokeny SAML **Azure Active Directory**. Tato konfigurace se nevztahují na aplikace pomocí zprostředkovatele identity v místním prostředí. Pro tyto scénáře doporučujeme revize [zdroje pro migraci aplikací do služby Azure AD](migration-resources.md).

Jednotné přihlašování SAML s Proxy aplikací funguje taky s funkcí šifrování tokenu SAML. Další informace najdete v tématu [šifrování tokenu konfigurovat Azure AD SAML](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publikování místních aplikací pomocí Proxy aplikace

Než budete moct poskytovat jednotné přihlašování k místním aplikacím, ujistěte se, že jste povolili Proxy aplikací a máte nainstalovaný konektor. Zobrazit [přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikace ve službě Azure AD](application-proxy-add-on-premises-application.md) další jak.

Mějte následující když budete v průběhu kurzu:

* Publikování aplikace podle pokynů v tomto kurzu. Je nutné vybrat **Azure Active Directory** jako **předběžné ověření** metodu pro vaši aplikaci (kroku 4 [přidat místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Kopírovat **externí adresa URL** pro aplikaci.
* Jako osvědčený postup použijte vlastní domény, kdykoli je to možné optimalizované uživatelské prostředí. Další informace o [práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).
* Přidejte aspoň jednoho uživatele k aplikaci a ujistěte se, že testovací účet má přístup k místní aplikaci.

## <a name="set-up-saml-sso"></a>Nastavit jednotné přihlašování SAML

1. Na webu Azure Portal, vyberte **Azure Active Directory > podnikové aplikace** a vyberte aplikaci ze seznamu.
1. Z aplikace **přehled** stránce **jednotného přihlašování**.
1. Vyberte **SAML** jako metodu přihlašování.
1. V **nastavte si jednotné přihlašování pomocí SAML** stránky, upravte **základní konfiguraci SAML** dat a postupujte podle kroků v [základní konfiguraci SAML Enter](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) konfigurace založené na SAML ověřování pro aplikaci.

   * Ujistěte se, že **adresy URL odpovědi** kořenové odpovídá nebo je cesta pod **externí adresa URL** pro místní aplikace, který jste přidali pro vzdálený přístup prostřednictvím Proxy aplikace ve službě Azure AD.

     ![Zadejte základní údaje konfigurace SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

     > [!NOTE]
     > Pokud se očekává, že back-end aplikace **adresy URL odpovědi** být interní adresa URL, budete muset nainstalovat Moje aplikace zabezpečené přihlašování rozšíření v uživatelských zařízeních. Toto rozšíření automaticky přesměruje na příslušnou službu Proxy aplikace. Pokud chcete nainstalovat rozšíření, naleznete v tématu [Moje aplikace zabezpečené přihlašování rozšíření](../user-help/active-directory-saas-access-panel-introduction.md#my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Testování aplikace

Po dokončení těchto kroků, by vaše aplikace do provozu. Testování aplikace:

1. Otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace. 
1. Přihlaste se pomocí testovacího účtu, který jste přiřadili do aplikace.

## <a name="next-steps"></a>Další postup

- [Jak Proxy aplikací Azure AD poskytuje jednotné přihlašování?](application-proxy-single-sign-on.md)
- [Poradce při potížích s Proxy aplikací](application-proxy-troubleshoot.md)
