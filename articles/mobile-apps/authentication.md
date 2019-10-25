---
title: Přidejte do svých mobilních aplikací ověřování pomocí Visual Studio App Center a služeb Azure.
description: Přečtěte si o službách, jako je App Center, které vám pomůžou nastavit ověřování uživatelů a povolit mobilním aplikacím ověřování pomocí sociálních účtů, Azure Active Directory a vlastního ověřování.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795754"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Přidání ověřování a Správa identit uživatelů v mobilních aplikacích

Zobrazení uživatele a jejich chování v rámci vaší aplikace umožňuje vývojářům lépe zapojit uživatele tím, že pro ně vytvoří přizpůsobená prostředí. Bez ohledu na to, jestli jste vývojář aplikací pro uživatele ve vaší organizaci nebo vytváříte další platformu sociální sítě, budete potřebovat způsob, jak ověřovat uživatele a spravovat identity uživatelů. Používání služby správy identit je jednou z nejdůležitějších funkcí služby Mobile back-end.

Pomocí následujících služeb můžete v mobilních aplikacích povolit ověřování uživatelů.

## <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
[Ověřování pomocí App Center](/appcenter/auth/) je cloudová služba pro správu identit, která umožňuje vývojářům ověřovat uživatele a spravovat identity uživatelů. App Center ověřování se taky integruje s dalšími částmi App Center a umožňuje vývojářům využít identitu uživatele k [zobrazení uživatelských dat](/appcenter/data/index) v jiných službách a dokonce i [posílat nabízená oznámení uživatelům místo jednotlivých zařízení](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Klíčové funkce**
- **Používá se Azure Active Directory B2C (Azure AD B2C)** 
    - Na podnikové úrovni.
    - Vysoce dostupné.
    - Zabezpečená a globální služba.
- **Využijte vlastní identitu** a použijte jiné oblíbené poskytovatele pro správu identit a přístupu, jako je Auth0 a Firebase.
- **Podpora AAD** 
    - Připojte stávající klienty AAD. 
    - Povolte ověřování proti podnikové doméně.
    - Spravujte přístup k citlivým datům.
- **Jednoduché** prostředí pro uživatele a prostředí Magical SDK zabalením knihovny MSAL do sady App Center SDK.
- **Podpora platforem** – iOS, Android, Xamarin, reakce nativní.

**Odkazy**
- [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Začínáme s ověřováním App Center](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) je služba pro správu identit od společnosti, která umožňuje vývojářům ověřovat své zákazníky. Tato služba White-Label umožňuje vývojářům přizpůsobit a řídit, jak budou uživatelé zabezpečeně pracovat s webovými, stolními, mobilními nebo jednostránkovémi aplikacemi. Pomocí Azure AD B2C se uživatelé můžou zaregistrovat, přihlašovat, resetovat hesla a upravovat profily. Azure AD B2C implementuje formulář protokolů OpenID Connect a OAuth 2,0. 

**Klíčové funkce**
- Bezpečně ověřovat zákazníky pomocí preferovaného poskytovatele identity.
- **Správa identit a přístupu zákazníků**.
- Podporovaná sociální přihlášení, jako je Facebook, GitHub, Google, LinkedIn, Twitter, WeChat, Weibo.
- Připojte se k uživatelským účtům pomocí **standardních protokolů** , jako je OpenID Connect nebo SAML, a využijte možnost správy identit na různých platformách.
- Poskytněte prostředí pro registraci a přihlašování v brandingu.
- Snadná integrace s databázemi CRM, nástroji marketingové analýzy a systémy ověřování účtů.
- Zaznamenání dat o přihlášení, předvolbách a převodu pro zákazníky.

**Odkazy**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentace k Azure AD B2C](/azure/active-directory-b2c/)
- [Rychlé starty](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Ukázky](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je cloudová služba pro správu identit a přístupu od Microsoftu, která pomáhá přihlašovat a přistupovat k vašemu zaměstnanci.
- Externí prostředky, například systém Microsoft Office 365, Azure Portal a tisíce dalších aplikací SaaS.
- Interní prostředky, jako jsou aplikace ve vaší podnikové síti nebo intranetu, spolu s jakýmikoli cloudovou aplikací vyvinutou vaší organizací.

**Klíčové funkce**
- **Bezproblémové a vysoce zabezpečený přístup** připojením uživatelů k aplikacím, které potřebují.
- **Komplexní ochrana identity** a rozšířené zabezpečení pro identity a přístup na základě kontextu uživatele, umístění, zařízení, dat a aplikace.
- **Tisíce předem integrovaných aplikací** komerčních i vlastních aplikací, jako je například Office 365, Salesforce.com a box.
- **Správa přístupu se škálováním**.

**Odkazy**
- [Azure Portal](https://portal.azure.com/)
- [Co je Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Začínáme se službou Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rychlé starty](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)