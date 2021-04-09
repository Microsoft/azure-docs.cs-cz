---
title: Nastavení toku úprav profilu
titleSuffix: Azure AD B2C
description: Přečtěte si, jak nastavit tok úprav profilu v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f4f785feed022226ed533d378a8fa52080b9ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581894"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Nastavení toku úprav profilu v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Tok úprav profilu

Zásada úprav profilu umožňuje uživatelům spravovat své atributy profilu, jako je zobrazované jméno, příjmení, křestní jméno, město a další. Tok úprav profilu zahrnuje následující kroky: 

1. Registrace nebo přihlášení s využitím místního nebo sociálního účtu. Pokud je relace stále aktivní, Azure AD B2C autorizuje uživatele a přeskočí k dalšímu kroku.
1. Azure AD B2C přečte profil uživatele z adresáře a umožní uživateli upravit atributy.

![Tok úprav profilu](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Požadavky

Pokud jste to ještě neudělali, [Zaregistrujte webovou aplikaci v Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Vytvoření toku uživatele upravujícího profil

Pokud chcete uživatelům umožnit úpravy svého profilu v aplikaci, použijte uživatelský tok upravující profil.

1. V nabídce na stránce Přehled klienta Azure AD B2C vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte uživatelský tok **upravující profil** . 
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**.
1. Zadejte **název** toku uživatele. Například *profileediting1*.
1. V případě **zprostředkovatelů identity** vyberte **přihlášení k e-mailu**.
1. V případě **atributů uživatele** vyberte atributy, které má zákazník ve svém profilu upravovat. Vyberte například **Zobrazit více** a potom zvolte atributy i deklarace identity pro **zobrazované jméno** a **název úlohy**. Klikněte na **OK**.
1. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** a pak se přihlaste pomocí účtu, který jste vytvořili dříve.
1. Teď máte příležitost změnit zobrazované jméno a název úlohy pro uživatele. Klikněte na **Pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Vytvoření zásady upravování profilu

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat cesty uživatelů. Nabízíme počáteční balíčky s několika předem sestavenými zásadami, včetně registrace a přihlášení, resetování hesla a zásad úprav profilů. Další informace najdete v tématu [Začínáme s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Další kroky

* Přidejte [přihlášení pomocí zprostředkovatele sociální identity](add-identity-provider.md).