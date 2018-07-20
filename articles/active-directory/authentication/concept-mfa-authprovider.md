---
title: Kdy a jak používat poskytovatele Azure Multi-Factor Auth?
description: Kdy byste měli použít poskytovatele ověřování s Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 4a6ce07bfe641d9efdbe0eac841bb4f27f468b34
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161460"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kdy použít poskytovatele Azure Multi-Factor Authentication

Dvoustupňové ověřování je k dispozici ve výchozím nastavení pro globální správce, kteří mají uživatele služeb Azure Active Directory a Office 365. Pokud ale budete chtít využívat výhod [pokročilých funkcí](howto-mfa-mfasettings.md) měli byste si zakoupit plnou verzi Azure Multi-Factor Authentication (MFA).

Poskytovatel ověřování Azure Multi-Factor Auth umožňuje využít výhod funkcí poskytovaných plnou verzí Azure MFA. Je pro uživatele, kteří **nemají licence prostřednictvím Azure MFA, Azure AD Premium nebo sady, které zahrnují Azure AD Premium nebo Azure MFA**. Azure MFA a Azure AD Premium zahrnují plnou verzi Azure MFA ve výchozím nastavení.

Pokud máte licence, které pokrývají všechny uživatele ve vaší organizaci, pak nepotřebujete poskytovatele Azure Multi-Factor auth. Vytvoření poskytovatele ověřování Azure Multi-Factor Authentication, pouze v případě, že budete taky muset zadat dvoustupňové ověřování pro uživatele, kteří nemají licence.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Upozornění související s Azure MFA SDK

Pokud si chcete stáhnout sadu SDK, budete potřebovat poskytovatele Azure Multi-Factor Auth. Poznámka: Sada SDK je zastaralá a již není podporována pro nové zákazníky a pouze budou nadále fungovat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří.

Chcete-li stáhnout sadu SDK, vytvořte poskytovatele Azure Multi-Factor Auth i v případě, že máte licence Azure MFA, AAD Premium nebo jiné sady. Pokud vytvoříte poskytovatele Azure Multi-Factor Auth pro tento účel a máte už licence, nezapomeňte poskytovatele vytvořit podle modelu **Na povoleného uživatele**. Potom propojte poskytovatele s adresáři, který obsahuje Azure MFA, Azure AD Premium nebo licence na ostatní sady. Tato konfigurace zajistí, že nebudete dostávat faktury, pokud nemáte více jedinečných uživatelů provádějících dvoustupňové ověřování než počet vlastněných licencí.

## <a name="what-is-an-mfa-provider"></a>Co je poskytovatel MFA?

Pokud nemáte licence pro ověřování Azure Multi-Factor Authentication, můžete vytvořit poskytovatele ověřování a vyžadovat dvoustupňové ověřování pro vaše uživatele.

Existují dva typy poskytovatelů ověřování, a rozdíl mezi nimi spočívá jak se vaše předplatné Azure účtuje. Možnost podle ověření zjišťuje počet ověření za měsíc v rámci vašeho tenanta. Tato možnost je vhodná, pokud máte řadu uživatelů, kteří se ověřují jenom občas. Možnost podle uživatelů zjišťuje počet jednotlivců za měsíc, kteří ve vašem tenantovi provedli dvoustupňové ověření. Tato možnost je vhodná, pokud máte uživatelé s licencemi, ale potřebujete rozšířit vícefaktorového ověřování pro další uživatele nad rámec licenčních omezení.

## <a name="create-an-mfa-provider"></a>Vytvoření poskytovatele MFA

Pomocí následujícího postupu vytvoříte poskytovatele služby Azure Multi-Factor Authentication na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **MFA Server** > **Poskytovatelé**.

   ![Poskytovatelé][Providers]

3. Vyberte **Přidat**.
4. Vyplňte následující pole a pak vyberte **Přidat**.
   - **Název** – Název poskytovatele.
   - **Model použití** – Zvolte jednu ze dvou možností:
      * Za ověření – nákupní model, který účtuje za ověření. Obvykle se používá pro scénáře, které používají Azure Multi-Factor Authentication v aplikaci zaměřené na spotřebitele.
      * Pro povolené uživatele – nákupní model, který účtuje za povoleného uživatele. Obvykle se používá pro přístup zaměstnanců k aplikacím, například Office 365. Tuto možnost vyberte, pokud máte uživatele, kteří už mají licence na Azure MFA.
   - **Předplatné** – Předplatné Azure, na které se budou účtovat aktivity dvoustupňového ověřování prostřednictvím poskytovatele.
   - **Adresář** – Tenant služby Azure Active Directory, ke kterému je poskytovatel přidružený.
      * K vytvoření poskytovatele nepotřebujete adresář Azure AD. Pokud plánujete stáhnout jenom Multi-Factor Authentication Server, ponechte toto pole prázdné.
      * Poskytovatel musí být přidružený k adresáři Azure AD, aby mohl využívat výhod pokročilých funkcí.
      * Ke každému adresáři Azure AD může být přidružený pouze jeden poskytovatel.

## <a name="manage-your-mfa-provider"></a>Správa poskytovatele MFA

Po vytvoření poskytovatele MFA není možné změnit model použití (na povoleného uživatele nebo na ověření). Můžete však poskytovatele MFA odstranit a potom vytvořit nového s jiným modelem použití.

Pokud je aktuální poskytovatel Multi-Factor Auth přidružený k adresáři Azure AD (označovaný také jako tenant Azure AD), můžete poskytovatele MFA bezpečně odstranit a vytvořit nového, který je propojený se stejným tenantem Azure AD. Případně pokud jste zakoupili dostatek MFA, Azure AD Premium nebo sady, které zahrnují licence Azure AD Premium nebo Azure MFA pro pokrytí všech uživatelů s povoleným vícefaktorovým Ověřováním, můžete můžete poskytovatele MFA odstranit úplně.

Pokud poskytovatel MFA není propojený s tenantem Azure AD nebo pokud propojujete poskytovatele MFA s jiným tenantem Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenesou. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv, telefonního hovoru a textové zprávy ověřování, ale oznámení mobilní aplikace přestanou fungovat pro všechny uživatele do opětovné aktivace mobilní aplikace.

## <a name="next-steps"></a>Další postup

[Konfigurace nastavení služby Multi-Factor Authentication](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Přidání poskytovatelů MFA"
