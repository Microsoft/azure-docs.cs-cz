---
title: Kurz pro konfiguraci bez klíčů s využitím Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Kurz pro konfiguraci bez klíčů s Azure Active Directory B2C pro ověřování bez hesla
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b817cfc347ee79ff7c9cbb4124e3f2b7e4d2b7ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644251"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Kurz: konfigurace bez klíčů pomocí Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny k tomu, jak nakonfigurovat Azure Active Directory (AD) B2C s [bez klíčů](https://keyless.io/). Když Azure AD B2C jako poskytovatel identity, můžete integrovat bez klíčů se všemi vašimi zákaznickými aplikacemi a poskytnout uživatelům tak skutečné ověřování bez hesla.

Bez klíčů řešení **bez klíčů Zero-Knowledge biometrika (ZKB™)** poskytuje vícefaktorové ověřování bez hesla, které eliminuje podvod, útok phishing a opakované použití přihlašovacích údajů – to vše a zároveň zlepšuje zkušenosti zákazníků a ochranu osobních údajů.

## <a name="pre-requisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné Azure. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](./tutorial-create-tenant.md). Tenant musí být propojený s vaším předplatným Azure.

- Bez klíčů Cloud tenant získáte bezplatný [zkušební účet](https://keyless.io/go).

- Aplikace bez klíčů Authenticator nainstalovaná na zařízení uživatele.

## <a name="scenario-description"></a>Popis scénáře

Integrace bez klíčů zahrnuje tyto komponenty:

- Azure AD B2C – autorizační Server zodpovědný za ověření přihlašovacích údajů uživatele, označovaný také jako zprostředkovatel identity.

- Webové a mobilní aplikace – mobilní nebo webové aplikace, které se rozhodnete chránit pomocí bez klíčů a Azure AD B2C.

- Mobilní aplikace bez klíčů – mobilní aplikace bez klíčů se bude používat k ověřování pro aplikace s povoleným Azure AD B2C.

V následujícím diagramu architektury se zobrazuje implementace.

![Obrázek znázorňuje diagram architektury bez klíčů](./media/partner-keyless/keyless-architecture-diagram.png)

|Krok | Description |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatelé vyberou přihlášení, zaregistrují se a vstoupí do uživatelského jména.
| 2. | Aplikace pošle atributy uživatele Azure AD B2C k ověření identity.
| 3. | Azure AD B2C shromažďuje atributy uživatele a odesílá atributy, které bez klíčů k ověření uživatele prostřednictvím mobilní aplikace bez klíčů.
| 4. | Bez klíčů pošle nabízené oznámení na mobilní zařízení registrovaného uživatele, které zachovává ověřování osobních údajů ve formě biometrické kontroly obličeje.
| 5. | Jakmile uživatel odpoví na nabízené oznámení, uživateli se buď udělí nebo odepře přístup k aplikaci pro zákazníky, na základě výsledků ověření.

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Přidat nového zprostředkovatele identity

Chcete-li přidat nového zprostředkovatele identity, postupujte takto:

1. Přihlaste se k **[Azure Portal](https://portal.azure.com/#home)** jako globální správce vašeho tenanta Azure AD B2C.

2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho tenanta.

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.

4. Přejděte na **řídicí panel**  >  **Azure Active Directory B2C**  >   **poskytovatelé identit** .

5. Vyberte **Zprostředkovatelé identity**.

6. Vyberte **Přidat**.

### <a name="configure-an-identity-provider"></a>Konfigurace zprostředkovatele identity

Pokud chcete nakonfigurovat poskytovatele identity, postupujte takto:

1. Vyberte **poskytovatele identity typ**  >  **OpenID připojit (Náhled)**
2. Vyplňte formulář a nastavte zprostředkovatele identity:

   |Vlastnost | Hodnota |
   |:-----| :-----------|
   | Název   | Bez klíčů |
   | Adresa URL metadat | Vložte identifikátor URI hostované aplikace bez klíčů Authentication, po které následuje konkrétní cesta, například ' https://keyless.auth/.well-known/openid-configuration '. |
   | Tajný klíč klienta | Tajný kód přidružený k instanci ověřování bez klíčů – není stejný jako nakonfigurovaný předtím. Vložte složitý řetězec podle vašeho výběru. Tento tajný klíč se použije později v konfiguraci kontejneru bez klíčů.|
   | ID klienta | ID klienta Toto ID se použije později v konfiguraci kontejneru bez klíčů.|
   | Obor | OpenID |
   | Typ odpovědi | id_token |
   | Režim odezvy | form_post|

3. Vyberte **OK**.

4. Vyberte **mapovat deklarace identity zprostředkovatele identity**.

5. Vyplňte formulář pro mapování zprostředkovatele identity:

   |Vlastnost | Hodnota |
   |:-----| :-----------|
   | UserID    | Z předplatného |
   | Zobrazované jméno | Z předplatného |
   | Režim odezvy | Z předplatného |

6. Vyberte **Save (Uložit** ) a dokončete instalaci nového poskytovatele identity Open ID Connect (OIDC).

### <a name="create-a-user-flow-policy"></a>Vytvoření zásady toku uživatele

Teď byste měli vidět bez klíčů jako nového OIDC poskytovatele identity uvedeného v rámci vašich poskytovatelů identity B2C.

1. Ve vašem tenantovi Azure AD B2C v části **zásady** vyberte **toky uživatelů**.

2. Vyberte **Nový** tok uživatele.

3. Vyberte **zaregistrovat, přihlaste** se, vyberte **verzi** a pak vyberte **vytvořit**.

4. Zadejte **název** zásady.

5. V části zprostředkovatelé identit vyberte svého nově vytvořeného poskytovatele identity bez klíčů.

6. Nastavte parametry toku uživatele. Vložte název a vyberte poskytovatele identity, kterého jste vytvořili. Můžete také přidat e-mailovou adresu. V takovém případě Azure neprovede přesměrování přihlašovací procedury přímo na bez klíčů místo toho, aby se zobrazila obrazovka, kde si uživatel může vybrat možnost, kterou chce použít.

7. Pole **Multi-Factor Authentication** ponechte jako.

8. Vyberte možnost **vyhovět zásadám podmíněného přístupu** .

9. V části **atributy uživatele a deklarace identity tokenů** vyberte v možnosti shromáždit atribut **e-mailová adresa** . Můžete přidat všechny atributy, které Azure Active Directory mohou shromažďovat informace o uživateli vedle deklarace identity, které Azure AD B2C mohou vracet do klientské aplikace.

10. Vyberte **Vytvořit**.

11. Po úspěšném vytvoření vyberte nový **tok uživatele**.

12. Na levém panelu vyberte **deklarace identit aplikace**. V části Možnosti zaškrtněte políčko **e-mail** a vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte Architektura prostředí identity.

2. Vyberte dříve vytvořenou SignUpSignIn.

3. Vyberte spustit tok uživatele a vyberte nastavení:

   a. Aplikace: vyberte registrovanou aplikaci (ukázka je JWT).

   b. Adresa URL odpovědi: vyberte adresu URL pro přesměrování.

   c. Vyberte spustit tok uživatele.

4. Projděte si registrační tok a vytvořte účet.

5. Bez klíčů se bude volat během toku, po vytvoření atributu uživatele. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](./custom-policy-get-started.md?tabs=applications)