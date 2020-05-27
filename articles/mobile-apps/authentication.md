---
title: Přidejte do svých mobilních aplikací ověřování pomocí Visual Studio App Center a služeb Azure.
description: Přečtěte si o službách, jako je Visual Studio App Center, které vám pomůžou nastavit ověřování uživatelů a povolit mobilním aplikacím ověřování pomocí sociálních účtů, Azure Active Directory a vlastního ověřování.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: a20ec1039dc8fc477410891495762395b408f2d6
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870578"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Přidání ověřování a Správa identit uživatelů v mobilních aplikacích

Zobrazení uživatele a jejich chování v rámci vaší aplikace umožňuje vývojářům lépe zapojit uživatele tím, že pro ně vytvoří přizpůsobená prostředí. Bez ohledu na to, jestli jste vývojář aplikace, který vytváří aplikaci pro spolupráci pro uživatele v rámci vaší organizace, nebo vytváříte další platformu sociální sítě, potřebujete způsob, jak ověřovat uživatele a spravovat identity uživatelů. Služba správy identit je jednou z nejdůležitějších funkcí mobilní back-endové služby.

Pomocí následujících služeb můžete v mobilních aplikacích povolit ověřování uživatelů.

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) je služba správy identit B2C (Business-to-Consumer), kterou můžou vývojáři použít k ověřování svých zákazníků. Tato služba White-Label umožňuje vývojářům přizpůsobit a řídit, jak budou uživatelé zabezpečeně pracovat s webovými, stolními, mobilními nebo jednostránkovémi aplikacemi. Pomocí Azure AD B2C se uživatelé můžou zaregistrovat, přihlašovat, resetovat hesla a upravovat profily. Azure AD B2C implementuje formulář protokolů OpenID Connect a OAuth 2,0. 

**Klíčové funkce**
- Bezpečně ověřovat zákazníky pomocí preferovaného poskytovatele identity.
- Spravujte zákaznickou identitu a přístup.
- Získejte podporu přihlašování pro sociální média, jako je Facebook, GitHub, Google, LinkedIn, Twitter, WeChat a Weibo.
- Pomocí standardních protokolů, jako je OpenID Connect nebo SAML, se připojte k uživatelským účtům, které umožní správu identit na různých platformách.
- Poskytněte prostředí pro registraci a přihlašování v brandingu.
- Snadná integrace s databázemi CRM, nástroji marketingové analýzy a systémy ověřování účtů.
- Zaznamenání přihlašovacích údajů, preference a konverze dat pro zákazníky.

**Odkazy**
- [portál Azure](https://portal.azure.com/)
- [Dokumentace k Azure AD B2C](/azure/active-directory-b2c/)
- [Rychlé starty](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [ukázky](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je cloudová služba pro správu identit a přístupu od Microsoftu, která umožňuje vašim zaměstnancům přihlašovat se a získat přístup k těmto akcím:
- Externí prostředky, například systém Microsoft Office 365, Azure Portal a tisíce dalších aplikací SaaS (software jako služba).
- Interní prostředky, jako jsou aplikace ve vaší podnikové síti nebo intranetu, spolu s jakýmikoli cloudovou aplikací vyvinutou vaší organizací.

**Klíčové funkce**
- Bezproblémové a vysoce zabezpečený přístup připojením uživatelů k aplikacím, které potřebují.
- Komplexní ochrana identity a rozšířené zabezpečení pro identity a přístup na základě kontextu uživatele, umístění, zařízení, dat a aplikace.
- Tisíce předem integrovaných aplikací pro komerční i vlastní aplikace, jako je například Office 365, Salesforce.com a box.
- Možnost spravovat přístup ve velkém měřítku.

**Odkazy**
- [portál Azure](https://portal.azure.com/)
- [Co je Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Začínáme se službou Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rychlé starty](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)