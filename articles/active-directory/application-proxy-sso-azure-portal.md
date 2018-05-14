---
title: Jednotné přihlašování pro aplikace s proxy aplikace služby Azure AD | Microsoft Docs
description: Zapněte jedním přihlašování pro aplikace publikované místní s Azure AD Application Proxy na portálu Azure.
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
ms.date: 07/20/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 77e7288060abd2577867023a777eb3bdd4331e88
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Heslo vaulting pro jednotné přihlašování pomocí Proxy aplikace

Azure Proxy aplikace Active Directory vám pomůže zlepšit produktivitu tím, že publikujete místní aplikace tak, aby vzdálení zaměstnanci mít bezpečný přístup, je příliš. Na portálu Azure můžete také nastavíte jednotné přihlašování (SSO) pro tyto aplikace. Uživatelé potřebují pouze k ověření s Azure AD a přístupem podniková aplikace bez nutnosti znovu přihlásit.

Proxy aplikací podporuje několik [jednotné přihlašování režimy](manage-apps/application-proxy-single-sign-on.md). Založené na heslech přihlášení je určený pro aplikace, které používají kombinace uživatelského jména a hesla pro ověřování. Když konfigurujete založené na heslech přihlašování pro aplikace, uživatelé musí přihlásit k aplikaci místně jednou. Potom Azure Active Directory ukládá přihlašovací informace a automaticky poskytuje k aplikaci při vaši uživatelé k němu přístup vzdáleně. 

Má už máte publikována a testování vaší aplikace pomocí Proxy aplikací. Pokud ne, postupujte podle kroků v [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md) pak se vraťte se sem. 

## <a name="set-up-password-vaulting-for-your-application"></a>Nastavit heslo vaulting pro vaši aplikaci

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Vyberte **Azure Active Directory** > **podnikové aplikace, které** > **všechny aplikace**.
3. Ze seznamu vyberte aplikaci, kterou chcete nastavit pomocí jednotného přihlašování.  
4. Vyberte **jednotného přihlašování**.

   ![Vybrat jednotné přihlašování](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Pro režim jednotné přihlašování, zvolte **založené na heslech přihlašování**.
6. Přihlášení adresy URL zadejte adresu URL pro stránku, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení do aplikace mimo firemní síť. To může být externí adresu URL, kterou jste vytvořili při publikování aplikace prostřednictvím Proxy aplikace. 

   ![Zvolte založené na heslech přihlašování a zadejte adresu URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Vyberte **Uložit**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testování aplikace

Přejděte na externí adresu URL, kterou jste nakonfigurovali pro vzdálený přístup k vaší aplikaci. Přihlaste se pomocí přihlašovacích údajů pro tuto aplikaci (nebo pověření pro účet testů, které jste nastavili s přístupem). Jakmile se přihlásíte úspěšně, byste měli mít opuštění aplikace a vraťte bez opětovného zadávání přihlašovacích údajů. 

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o další způsoby, jak implementovat [jednotné přihlašování pomocí Proxy aplikace](manage-apps/application-proxy-single-sign-on.md)
- Další informace o [důležité informace o zabezpečení pro přístup k aplikacím vzdáleně pomocí proxy aplikace služby Azure AD](application-proxy-security-considerations.md)