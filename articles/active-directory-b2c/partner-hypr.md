---
title: Kurz konfigurace Azure Active Directory B2C pomocí HYPR
titleSuffix: Azure AD B2C
description: Kurz konfigurace Azure Active Directory B2C s využitím Hypr pro opravdové silné ověřování zákazníků bez hesla
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953759"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci HYPR s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny ke konfiguraci Azure AD B2C pomocí [HYPR](https://get.hypr.com). Když Azure AD B2C jako poskytovatel identity, můžete integrovat HYPR se všemi vašimi zákaznickými aplikacemi a poskytnout uživatelům tak skutečné ověřování bez hesla. HYPR nahrazuje hesla s použitím šifrování veřejných klíčů, které eliminují podvod, útoky phishing a opakované použití přihlašovacích údajů.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](./tutorial-create-tenant.md). Tenant je propojený s vaším předplatným Azure.

- HYPR Cloud tenant získáte bezplatný [zkušební účet](https://get.hypr.com/free-trial).

- Mobilní zařízení uživatele registrované pomocí rozhraní REST API HYPR nebo HYPR Device Manager ve vašem tenantovi HYPR. K provedení této úlohy můžete například použít [sadu HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) .

## <a name="scenario-description"></a>Popis scénáře

Integrace HYRP zahrnuje tyto komponenty:

- Azure AD B2C – autorizační Server zodpovědný za ověření přihlašovacích údajů uživatele, označovaný také jako zprostředkovatel identity

- Webové a mobilní aplikace – mobilní nebo webové aplikace, které se rozhodnete chránit pomocí HYPR a Azure AD B2C. HYPR poskytuje také mobilní aplikaci, kterou můžete použít na platformách iOS a Android k ověřování bezplatných hesel.

- Mobilní aplikace HYPR – mobilní aplikace HYPR se dá použít ke spuštění této ukázky, pokud nechcete používat mobilní sady SDK ve vašich vlastních mobilních aplikacích.

- Rozhraní HYPR REST API – k registraci a ověřování uživatelských zařízení můžete použít rozhraní HYPR API. Tato rozhraní API najdete [tady](https://apidocs.hypr.com).

V následujícím diagramu architektury se zobrazuje implementace.

![Snímek obrazovky pro hypr – architektura – diagram](media/partner-hypr/hypr-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatelé vyberou přihlášení a zaregistrují se na stránku a zadají uživatelské jméno.
| 2. | Aplikace pošle atributy uživatele Azure AD B2C k identifikaci ověřování.
| 3. | Azure AD B2C shromažďuje atributy uživatele a odesílá atributy, které HYPR k ověření uživatele prostřednictvím mobilní aplikace HYPR.
| 4. | HYPR pošle nabízené oznámení zaregistrovanému uživateli na mobilní zařízení pro ověřování s certifikací online (FIDO). Může to být tisk prstem uživatele, biometrické nebo decentralizovaný PIN kód.  
| 5. | Po potvrzení nabízeného oznámení uživatelem je uživateli udělen nebo odepřen přístup k aplikaci pro zákazníky na základě výsledků ověřování.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurace zásad Azure AD B2C

1. Ve složce zásad otevřete [zásady Azure AD B2C HYPR](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) .

2. Pomocí tohoto [dokumentu](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) stáhněte [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) .

3. Nakonfigurujte zásady pro klienta Azure AD B2C.

>[!NOTE]
>Aktualizujte zadané zásady tak, aby se vztahovaly k vašemu konkrétnímu tenantovi.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace**: vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi**: vyberte **adresu URL pro přesměrování** .

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si registrační tok a vytvořte účet.

5. HYPR se bude volat během toku, po vytvoření atributu uživatele. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](./custom-policy-get-started.md?tabs=applications)