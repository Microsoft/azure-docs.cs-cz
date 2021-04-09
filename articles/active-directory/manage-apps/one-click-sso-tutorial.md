---
title: Konfigurace jednotného přihlašování (SSO) vaší aplikace Azure Marketplace jedním kliknutím | Microsoft Docs
description: Postup pro konfiguraci jednotného přihlašování (SSO) pro vaši aplikaci na Azure Marketplace.
services: active-directory
documentationCenter: na
author: kenwith
manager: daveba
ms.reviewer: kenwith
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f705e587010d956c29a71127e97dd85cb517ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258248"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfigurace aplikace jedním kliknutím jednotného přihlašování

 V tomto kurzu se dozvíte, jak v Azure Marketplace provést konfiguraci jednotného přihlašování (SSO) pro aplikace založené na SAML, Azure Active Directory (Azure AD).

## <a name="introduction-to-one-click-sso"></a>Úvod k jednotnému přihlašování jedním kliknutím

Funkce jednotného přihlašování jedním kliknutím je určená ke konfiguraci jednotného přihlašování pro Azure Marketplace aplikace, které podporují protokol SAML. Na stránce konfigurace jednotného přihlašování služby Azure AD vám tato možnost umožňuje automaticky konfigurovat metadata Azure AD na straně aplikace. Tímto způsobem můžete rychle nastavit jednotné přihlašování s minimálním ručním úsilím.

## <a name="advantages-of-one-click-sso"></a>Výhody jednotného přihlašování jedním kliknutím

- Rychlá konfigurace jednotného přihlašování Azure Marketplace aplikací, které vyžadují ruční instalaci na straně aplikace
- Efektivnější a přesná konfigurace jednotného přihlašování.
- Pro instalaci není nutná žádná komunikace s partnerem ani podpora. Aplikace poskytuje uživatelské rozhraní pro konfiguraci SAML.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné aplikace, které se má nakonfigurovat pomocí jednotného přihlašování. Budete také potřebovat přihlašovací údaje správce.
- **Rozšíření zabezpečené přihlašování aplikace** z Microsoftu nainstalované v prohlížeči Další informace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="one-click-sso-configuration-steps"></a>Kroky pro konfiguraci jednotného přihlašování jedním kliknutím

1. Přidejte aplikaci z Azure Marketplace.

2. Vyberte **jednotné přihlašování**.

3. Vyberte **Povolit jednotné přihlašování**.

4. Naplňte hodnoty povinných konfigurací do **základního konfiguračního oddílu SAML** .

    > [!NOTE]
    > Pokud má aplikace vlastní deklarace identity, které je třeba nakonfigurovat, zpracujte je před provedením jednotného přihlašování jedním kliknutím.

5. Pokud je pro vaši aplikaci Azure Marketplace dostupná funkce jednotného přihlašování (SSO), zobrazí se následující obrazovka. Je možné, že bude nutné nainstalovat **rozšíření prohlížeče pro zabezpečené přihlašování k aplikacím** výběrem možnosti **nainstalovat rozšíření**.

   ![Nainstalovat zabezpečené přihlašovací rozšíření prohlížeče mých aplikací](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po přidání rozšíření do prohlížeče vyberte možnost **instalace \<Application Name\>**. Až budete přesměrováni na portál pro správu aplikací, přihlaste se jako správce.

   ![Nastavit název aplikace](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozšíření prohlížeče automaticky nakonfiguruje jednotné přihlašování pro aplikaci. Potvrďte výběrem možnosti **Ano**.

   ![Ukládání automaticky vyplněných dat](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Pokud konfigurace jednotného přihlašování pro aplikaci vyžaduje další kroky, postupujte podle pokynů k provedení těchto kroků.

8. Po dokončení konfigurace kliknutím na **tlačítko OK** změny uložte.

   ![Uložit automaticky vyplněná data](./media/one-click-sso-tutorial/save-data.png)

9. V potvrzovacím okně se zobrazí informace o tom, že nastavení jednotného přihlašování je úspěšně nakonfigurované.

   ![Nakonfigurované jednotné přihlašování](./media/one-click-sso-tutorial/sso-configured.png)

10. Po úspěšném dokončení konfigurace se odhlásíte z aplikace a vrátíte se do Azure Portal.

11. Můžete vybrat **test** pro otestování jednotného přihlašování.

## <a name="additional-resources"></a>Další zdroje informací

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md)
* [Jaké jsou rozšíření prohlížeče pro zabezpečené přihlašování k aplikacím?](../user-help/my-apps-portal-end-user-access.md)
