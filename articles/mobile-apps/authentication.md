---
title: Přidání ověřování do mobilních aplikací pomocí Visual Studia App Center a služeb Azure
description: Seznamte se se službami, jako je Visual Studio App Center, které pomáhají nastavit ověřování uživatelů a umožňují mobilním aplikacím ověřování pomocí účtů na sociálních sítích, Azure Active Directory a vlastního ověřování.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241039"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Přidání ověřování a správa identit uživatelů v mobilních aplikacích

Zobrazení uživatele a jeho chování v celé aplikaci umožňuje vývojářům lépe zaujmout uživatele vytvořením přizpůsobených prostředí pro ně. Ať už jste vývojář aplikací, který vytváří aplikaci pro spolupráci pro uživatele ve vaší organizaci nebo vytváříte další platformu sociálních sítí, potřebujete způsob, jak ověřovat uživatele a spravovat identity uživatelů. Služba správy identit je jednou z nejdůležitějších funkcí mobilní back-endové služby.

Pomocí následujících služeb povolte ověřování uživatelů v mobilních aplikacích.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) je cloudová služba správy identit, kterou můžou vývojáři používat k ověřování uživatelů a správě identit uživatelů. App Center Auth se také integruje s dalšími částmi Centra aplikací Visual Studia. Vývojáři mohou pomocí identity uživatele [zobrazit uživatelská data](/appcenter/data/index) v jiných službách a dokonce [odesílat nabízená oznámení uživatelům namísto jednotlivých zařízení](/appcenter/push/push-to-user#setting-user-identity). 

**Klíčové funkce**
- Běží na Azure Active Directory B2C (Azure AD B2C). 
    - Podniková třída.
    - Vysoce dostupné.
    - Bezpečné a globální služby.
- Přineste si vlastní identitu a možnost používat další oblíbené poskytovatele správy identit a přístupu, jako je Auth0 a Firebase.
- Podpora služby Azure Active Directory.
    - Připojte stávající klienty Azure AD. 
    - Povolte ověřování proti podnikové doméně.
    - Spravujte přístup k citlivým datům.
- Jednoduché uživatelské prostředí a magické prostředí sady SDK zabalením knihovny Microsoft Authentication Library pomocí sady Visual Studio App Center SDK.
- Podpora platformy pro iOS, Android, Xamarin a React Native.

**Odkazy**
- [Registrace pomocí Centra aplikací Visual Studia](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Začínáme s auth Centra aplikací](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) je služba správy identit mezi podniky a spotřebiteli (B2C), kterou můžou vývojáři použít k ověření svých zákazníků. Tato služba white-label umožňuje vývojářům přizpůsobit a řídit, jak uživatelé bezpečně pracují se svými webovými, desktopovými, mobilními nebo jednostránkovými aplikacemi. Pomocí Azure AD B2C můžou uživatelé zaregistrovat, přihlásit se, resetovat hesla a upravovat profily. Azure AD B2C implementuje formu protokolů OpenID Connect a OAuth 2.0. 

**Klíčové funkce**
- Bezpečně ověřujte zákazníky pomocí jejich preferovaného poskytovatele identity.
- Spravujte identitu a přístup zákazníka.
- Získejte podporu pro sociální média, jako je Facebook, GitHub, Google, LinkedIn, Twitter, WeChat a Weibo.
- Připojte se ke svým uživatelským účtům pomocí standardních protokolů, jako je OpenID Connect nebo SAML, abyste umožnili správu identit na různých platformách.
- Poskytněte značkové registraci a přihlašovací prostředí.
- Snadno se integrujte s databázemi CRM, nástroji pro marketingovou analýzu a systémy ověřování účtů.
- Zachyťte údaje o přihlášení, předvolbách a konverzích pro zákazníky.

**Odkazy**
- [Portál Azure](https://portal.azure.com/)
- [Dokumentace Azure AD B2C](/azure/active-directory-b2c/)
- [Rychlé starty](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [ukázky](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je cloudová služba správy identit a přístupu společnosti Microsoft, která vašim zaměstnancům pomáhá přihlašovat se a získat přístup k:
- Externí prostředky, jako je Microsoft Office 365, portál Azure a tisíce dalších aplikací softwaru jako služby (SaaS).
- Interní prostředky, jako jsou aplikace v podnikové síti a intranetu, spolu se všemi cloudovými aplikacemi vyvinutými vaší vlastní organizací.

**Klíčové funkce**
- Bezproblémový a vysoce bezpečný přístup připojením uživatelů k aplikacím, které potřebují.
- Komplexní ochrana identit a rozšířené zabezpečení identit a přístupu na základě kontextu uživatele, polohy, zařízení, dat a aplikací.
- Tisíce předem integrovaných aplikací pro komerční i vlastní aplikace, jako jsou Office 365, Salesforce.com a Box.
- Schopnost spravovat přístup ve velkém měřítku.

**Odkazy**
- [Portál Azure](https://portal.azure.com/)
- [Co je Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Začínáme se službou Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rychlé starty](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)