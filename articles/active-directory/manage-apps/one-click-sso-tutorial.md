---
title: Konfigurace aplikace Azure Marketplace jedním kliknutím na jedno přihlášení (SSO) | Dokumenty společnosti Microsoft
description: Kroky pro konfiguraci s asoýrek pro vaši aplikaci jedním kliknutím z Azure Marketplace.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67872427"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfigurace aplikace jedním kliknutím pro jednotné přihlašování

 V tomto kurzu se dozvíte, jak provádět konfiguraci jednotného přihlašování (SSO) jedním kliknutím pro aplikace podporující SAML, Azure Active Directory (Azure AD) z Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Úvod k jednorázovému připomenutí při pořizování a připojetí

Funkce jednotného přihlašování jedním kliknutím je navržena tak, aby nakonfigurovala jednotné přihlašování pro aplikace Azure Marketplace, které podporují protokol SAML. Na stránce konfigurace služby Azure AD SSO tato možnost umožňuje automaticky nakonfigurovat metadata Azure AD na straně aplikace. Tímto způsobem můžete rychle nastavit přihlašování s minimálním ručním úsilím.

## <a name="advantages-of-one-click-sso"></a>Výhody jednorázového připomenutí při připomenutí

- Rychlá konfigurace přihlašování aplikací Azure Marketplace, které vyžadují ruční nastavení na straně aplikace.
- Efektivnější a přesnější konfigurace s připojou.
- Pro nastavení není nutná žádná partnerská komunikace ani podpora. Aplikace poskytuje ui pro konfiguraci SAML.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné aplikace ke konfiguraci pomocí služby SSO. Potřebujete také přihlašovací údaje správce.
- V prohlížeči je **nainstalováno rozšíření Moje aplikace zabezpečené přihlášení** od společnosti Microsoft. Další informace najdete v [tématu Přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Kroky konfigurace přistaň jedním kliknutím

1. Přidejte aplikaci z Azure Marketplace.

2. Vyberte **možnost Jednotné přihlašování**.

3. Vyberte **Povolit jednotné přihlašování**.

4. Naplnění povinných hodnot konfigurace v části **Základní konfigurace SAML.**

    > [!NOTE]
    > Pokud aplikace má vlastní deklarace identity, které je třeba nakonfigurovat, zpracovat je před provedením jedním kliknutím přihlašování.

5. Pokud je pro vaši aplikaci Azure Marketplace dostupná funkce přihlašované jedním kliknutím, zobrazí se následující obrazovka. Rozšíření prohlížeče Zabezpečené **přihlášení ke službě Moje aplikace** bude pravděpodobně nutné nainstalovat výběrem **možnosti Nainstalovat rozšíření**.

   ![Instalace rozšíření zabezpečeného přihlašovacího prohlížeče my Apps](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Po přidání rozšíření do prohlížeče vyberte **nastavit \<název\>aplikace**. Po přesměrování na portál pro správu aplikací se přihlaste jako správce.

   ![Nastavení názvu aplikace](./media/one-click-sso-tutorial/setup-sso.png)

7. Rozšíření prohlížeče automaticky konfiguruje sponese v aplikaci. Potvrďte výběrem možnosti **Ano**.

   ![Uložení automaticky vyplněných dat](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Pokud konfigurace přihlašuje služby Obecné zabezpečení pro vaši aplikaci vyžaduje další kroky, postupujte podle pokynů k provedení těchto kroků.

8. Po dokončení konfigurace vyberte **OK,** chcete-li změny uložit.

   ![Uložení automaticky vyplněných dat](./media/one-click-sso-tutorial/save-data.png)

9. Zobrazí se potvrzovací okno, které vás dozví, že nastavení připřiponete si úspěšně.

   ![Konfigurace přisávaní při schy](./media/one-click-sso-tutorial/sso-configured.png)

10. Po úspěšné konfiguraci jste odhlášeni z aplikace a vráceni na portál Azure.

11. Můžete vybrat **Test k** testování jednotného přihlášení.

## <a name="additional-resources"></a>Další zdroje

* [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Co je rozšíření prohlížeče Zabezpečené přihlášení ke službě Moje aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
