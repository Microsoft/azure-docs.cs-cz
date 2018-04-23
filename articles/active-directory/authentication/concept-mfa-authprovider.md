---
title: Začínáme s poskytovatelem ověřování Azure Multi-Factor Auth | Dokumentace Microsoftu
description: Naučte se vytvářet poskytovatele ověřování Azure Multi-Factor Auth.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: a2f4ece603cfd8e0ec891feafe344f27a2ead90a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Začínáme s poskytovatelem služby Azure Multi-Factor Authentication
Dvoustupňové ověřování je k dispozici ve výchozím nastavení pro globální správce, kteří mají uživatele služeb Azure Active Directory a Office 365. Pokud ale budete chtít využívat výhod [pokročilých funkcí](howto-mfa-mfasettings.md) měli byste si zakoupit plnou verzi Azure Multi-Factor Authentication (MFA).

Poskytovatel ověřování Azure Multi-Factor Auth umožňuje využít výhod funkcí poskytovaných plnou verzí Azure MFA. Je určený pro uživatele, kteří **nemají licence prostřednictvím Azure MFA, Azure AD Premium nebo Enterprise Mobility + Security (EMS)**.  Azure MFA, Azure AD Premium a EMS zahrnují plnou verzi Azure MFA ve výchozím nastavení. Pokud máte licence, nepotřebujete poskytovatele Azure Multi-Factor Auth.

Pokud si chcete stáhnout sadu SDK, budete potřebovat poskytovatele Azure Multi-Factor Auth.

> [!IMPORTANT]
> Proběhlo oznámení o zastarání sady Azure Multi-Factor Authentication Software Development Kit (SDK). Tato funkce se už pro nové zákazníky nepodporuje. Aktuální zákazníci mohou tuto sadu SDK používat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří.

> [!IMPORTANT]
>Pokud si chcete stáhnout sadu SDK, je třeba vytvořit poskytovatele Azure Multi-Factor Auth i v případě, že máte licence Azure MFA, AAD Premium nebo EMS.  Pokud vytvoříte poskytovatele Azure Multi-Factor Auth pro tento účel a máte už licence, nezapomeňte poskytovatele vytvořit podle modelu **Na povoleného uživatele**. Potom propojte poskytovatele s adresářem, ve kterém jsou uložené licence Azure MFA, Azure AD Premium nebo EMS. Tato konfigurace zajistí, že nebudete dostávat faktury, pokud nemáte více jedinečných uživatelů provádějících dvoustupňové ověřování než počet vlastněných licencí. 

## <a name="what-is-an-mfa-provider"></a>Co je poskytovatel MFA?

Pokud nemáte licence pro Azure Multi-Factor Authentication, můžete vytvořit poskytovatele ověřování tak, aby pro vaše uživatele vyžadoval dvoustupňové ověření.

Existují dva typy poskytovatelů ověřování. Rozdíl mezi nimi spočívá v tom, jak se účtuje předplatné Azure. Možnost podle ověření zjišťuje počet ověření za měsíc v rámci vašeho tenanta. Tato možnost je vhodná, pokud máte řadu uživatelů, kteří se ověřují jenom občas. Možnost podle uživatelů zjišťuje počet jednotlivců za měsíc, kteří ve vašem tenantovi provedli dvoustupňové ověření. Tato možnost je vhodná, pokud máte uživatelé s licencemi, ale potřebujete rozšířit vícefaktorového ověřování pro další uživatele nad rámec licenčních omezení.

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

Pokud je aktuální poskytovatel Multi-Factor Auth přidružený k adresáři Azure AD (označovaný také jako tenant Azure AD), můžete poskytovatele MFA bezpečně odstranit a vytvořit nového, který je propojený se stejným tenantem Azure AD. Alternativně, pokud jste zakoupili dostatek licencí MFA, Azure AD Premium nebo Enterprise Mobility + Security (EMS) k pokrytí všech uživatelů s povoleným vícefaktorovým ověřováním, můžete poskytovatele MFA odstranit úplně.

Pokud poskytovatel MFA není propojený s tenantem Azure AD nebo pokud propojujete poskytovatele MFA s jiným tenantem Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenesou. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

## <a name="next-steps"></a>Další kroky

[Konfigurace nastavení služby Multi-Factor Authentication](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Přidání poskytovatelů MFA"
