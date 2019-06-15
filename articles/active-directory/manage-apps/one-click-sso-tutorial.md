---
title: Nakonfigurujte jeden SSO klikněte na tlačítko pro vaši aplikaci z Galerie aplikací Azure AD | Dokumentace Microsoftu
description: Postup pro konfiguraci jednoho klikněte na možnost jednotného přihlašování pro vaši aplikaci z Galerie aplikací Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057845"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Jednu funkci klikněte na možnost jednotného přihlašování pro aplikace Galerie Azure AD

 V tomto kurzu se dozvíte, jak provádět jednu klikněte na možnost jednotného přihlašování pro všechny aplikace SAML, které poskytují uživatelské rozhraní pro konfiguraci jednotného přihlašování.

## <a name="introduction-to-one-click-sso"></a>Úvod do jednotného přihlašování jedním kliknutím

Jednu funkci klikněte na jednotné přihlašování se používá ke konfiguraci jednotné přihlašování pro aplikace z Galerie Azure AD, které podporují protokol SAML. Na stránce Konfigurace jednotného přihlašování k Azure AD jsme připravili tuto možnost, povolíte našim zákazníkům ke konfiguraci Azure AD metadat na straně aplikace automaticky. Cílem je pomáhat zákazníkům rychle nastavení jednotného přihlašování s minimálním manuální úsilí. 

## <a name="advantages-of-the-one-click-sso"></a>Výhod je, klikněte na možnost jednotného přihlašování

- Galerie aplikací, kde Zákazníci musí provést ruční nastavení na straně aplikace rychlé konfiguraci jednotného přihlašování.
- Další efektivní a přesné způsob konfigurace.
- Žádná komunikace partnera nebo podporu potřebné k navázání jako aplikace poskytuje uživatelské rozhraní pro konfiguraci SAML.

## <a name="prerequisites"></a>Požadavky

- Aplikace s přihlašovacími údaji správce, které chcete nakonfigurovat s jednotným Přihlašováním OneClick aktivní předplatné.
- **Moje rozšíření prohlížeče aplikace zabezpečené přihlašování** od společnosti Microsoft nainstalované v prohlížeči. Pokud chcete získat další informace o tomto rozšíření, podívejte se na to [odkaz](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-feature-step-by-step-details"></a>Jeden jednotného přihlašování k klikněte na podrobnosti o funkcích krok za krokem

1. Přidáte aplikaci z Galerie aplikací Azure AD.

2. Klikněte na jednotné přihlašování.

3. Klikněte na povolit jednotné přihlašování.

4. Naplnění povinné konfigurační hodnoty v části základní konfiguraci SAML.

    > [!NOTE] 
    > Pokud aplikace potřebuje konfigurace vlastních deklarací identity, nakonfigurujte je před provedením OneClick jednotného přihlašování.

5. Pokud jeden klikněte na možnost jednotného přihlašování funkce je implementována pro každou aplikaci Galerie, uvidíte následující obrazovku. Pokud **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** je ještě není nainstalované, nemusíte klikat na **nainstalovat rozšíření** možnost.

    ![Instalace rozšíření prohlížeče Moje aplikace zabezpečené přihlášení](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po přidání rozšíření do prohlížeče, klikněte na **název nastavení aplikace** které vás přesměrují na portálu pro správu aplikace. Budete muset přihlásit jako správce do aplikace.

    ![Název nastavení aplikace](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozšíření prohlížeče teď automaticky nakonfigurují aplikaci za vás. Žádá nejprve vaše potvrzení. Pokud chcete, aby bylo možné pokračovat. Klikněte na **Ano**.

    ![Ukládá automaticky vyplní data](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > Pokud všechny aplikace musí navíc nagivation nebo kroky, měli byste vidět správné zprávy s výzvou k provedení těchto kroků. 

8. Po dokončení konfigurace klikněte na tlačítko **Ok** a uložte změny.

    ![Uložit data automaticky vyplní](./media/one-click-sso-tutorial/save-data.png)

9. Zobrazí se místní zpráva úspěšné potvrzení a nastavení jednotného přihlašování se úspěšně nakonfigurovala. Potom můžete testovat aplikace.

    ![Nakonfigurovat jednotné přihlašování](./media/one-click-sso-tutorial/sso-configured.png)

10. Jakmile konfigurace byla úspěšně dokončena, aplikace bude odhlášen. a budete přesměrováni zpět na web Azure portal.

11. Klepnutím na tlačítko Test můžete otestovat jednotné přihlašování.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Jaká je Moje aplikace zabezpečené přihlašování rozšíření prohlížeče](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 