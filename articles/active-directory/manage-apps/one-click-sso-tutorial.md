---
title: Jedním kliknutím, jednotné přihlašování (SSO) konfiguraci vaší aplikace na webu Azure Marketplace | Dokumentace Microsoftu
description: Postup pro konfiguraci jednotného přihlašování jedním kliknutím pro vaši aplikaci na Azure Marketplace.
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
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872427"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Aplikaci jedním kliknutím konfiguraci jednotného přihlašování

 V tomto kurzu se dozvíte, jak k provádění jedním kliknutím, jednotné přihlašování (SSO) konfigurace SAML – podpora, aplikace Azure Active Directory (Azure AD) na Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Úvod do jednotného přihlašování jedním kliknutím

Funkce jednotného přihlašování jedním kliknutím slouží ke konfiguraci jednotného přihlašování pro aplikace z Azure Marketplace, které podporují protokol SAML. Na stránce Konfigurace jednotného přihlašování k Azure AD tato možnost umožňuje automaticky konfigurovat metadat služby Azure AD na straně aplikace. Tímto způsobem můžete rychle nastavit jednotné přihlašování s minimálním úsilím ruční.

## <a name="advantages-of-one-click-sso"></a>Výhody jednotného přihlašování jedním kliknutím

- Rychlé konfiguraci jednotného přihlašování k Azure Marketplace aplikací, které vyžadují ruční nastavení na straně aplikace.
- Efektivní a přesné Konfigurace jednotného přihlašování.
- Žádná komunikace partnera nebo podporu potřebné k instalaci. Aplikace poskytuje uživatelské rozhraní pro konfiguraci SAML.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné aplikace nakonfigurovat s jednotným Přihlašováním. Budete potřebovat přihlašovací údaje správce.
- **Moje aplikace zabezpečené přihlašování rozšíření** od společnosti Microsoft nainstalované v prohlížeči. Další informace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Postup konfigurace jednotného přihlašování jedním kliknutím

1. Přidáte aplikace z Azure Marketplace.

2. Vyberte **jednotného přihlašování**.

3. Vyberte **povolit jednotné přihlašování**.

4. Naplnění povinné konfigurační hodnoty v **základní konfiguraci SAML** oddílu.

    > [!NOTE]
    > Pokud aplikace má vlastní deklarace identity, které je potřeba nakonfigurovat, než se pustíte do jednotného přihlašování jedním kliknutím jejich zpracování.

5. Pokud funkce jednotného přihlašování jedním kliknutím k dispozici pro vaši aplikaci Azure Marketplace, uvidíte následující obrazovku. Možná budete muset nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** tak, že vyberete **nainstalovat rozšíření**.

   ![Instalace rozšíření prohlížeče Moje aplikace zabezpečené přihlášení](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po přidání rozšíření do prohlížeče, vyberte **nastavení \<název_aplikace\>** . Jakmile budete přesměrováni na portál pro správu aplikací, přihlaste se jako správce.

   ![Název nastavení aplikace](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozšíření prohlížeče automaticky nakonfiguruje jednotné přihlašování v aplikaci. Potvrďte výběrem **Ano**.

   ![Ukládá se automaticky vyplní data](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Pokud konfigurace jednotného přihlašování pro vaše aplikace vyžaduje další kroky, postupovat podle pokynů k provedení kroků.

8. Po dokončení konfigurace, vyberte **OK** a uložte změny.

   ![Uložit data automaticky vyplní](./media/one-click-sso-tutorial/save-data.png)

9. Okno s potvrzením zobrazuje s oznámením, že nastavení jednotného přihlašování se úspěšně nakonfigurovala.

   ![Nakonfigurovat jednotné přihlašování](./media/one-click-sso-tutorial/sso-configured.png)

10. Po úspěšné konfiguraci provedete z aplikace odhlásí a vrátí na webu Azure portal.

11. Můžete vybrat **testování** otestovat jednotné přihlašování.

## <a name="additional-resources"></a>Další zdroje

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Co je rozšíření prohlížeče Moje aplikace zabezpečené přihlašování?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
