---
title: Kdy a jak používat poskytovatele Azure Multi-Factor auth? -Azure Active Directory
description: Kdy byste měli použít poskytovatele ověřování s Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399fccf9aaaeb9e252527e80a6549ee286bb1898
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369369"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kdy použít poskytovatele služby Azure Multi-Factor Authentication

Dvoustupňové ověřování je k dispozici ve výchozím nastavení pro globální správce, kteří mají uživatele služeb Azure Active Directory a Office 365. Pokud ale budete chtít využívat výhod [pokročilých funkcí](howto-mfa-mfasettings.md) měli byste si zakoupit plnou verzi Azure Multi-Factor Authentication (MFA).

Poskytovatel Azure Multi-Factor auth se používá k využití funkcí poskytovaných službou Azure Multi-Factor Authentication pro uživatele, kteří nemají **licence**.

> [!NOTE]
> Od 1. září 2018 se už možná nebudou vytvářet Noví zprostředkovatelé ověřování. Stávající zprostředkovatelé ověřování se můžou dál používat a aktualizovat, ale migrace už není možná. Multi-Factor Authentication bude i nadále k dispozici jako funkce v Azure AD Premium licencích.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Upozornění související se sadou Azure MFA SDK

Všimněte si, že sada SDK je zastaralá a bude i nadále fungovat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří.

## <a name="what-is-an-mfa-provider"></a>Co je poskytovatel MFA?

Existují dva typy zprostředkovatelů ověřování a rozdíl mezi tím, jak se účtuje vaše předplatné Azure. Možnost podle ověření zjišťuje počet ověření za měsíc v rámci vašeho tenanta. Tato možnost je vhodná, pokud máte řadu uživatelů, kteří se ověřují jenom občas. Možnost podle uživatelů zjišťuje počet jednotlivců za měsíc, kteří ve vašem tenantovi provedli dvoustupňové ověření. Tato možnost je vhodná, pokud máte uživatelé s licencemi, ale potřebujete rozšířit vícefaktorového ověřování pro další uživatele nad rámec licenčních omezení.

## <a name="manage-your-mfa-provider"></a>Správa poskytovatele MFA

Po vytvoření poskytovatele MFA není možné změnit model použití (na povoleného uživatele nebo na ověření).

Pokud jste si koupili dostatek licencí pro pokrytí všech uživatelů, kteří jsou povoleni pro MFA, můžete poskytovatele MFA odstranit úplně.

Pokud poskytovatel MFA není propojený s tenantem Azure AD nebo pokud propojujete poskytovatele MFA s jiným tenantem Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenesou. Existující servery Azure MFA se taky musí znovu aktivovat pomocí aktivačních přihlašovacích údajů vygenerovaných prostřednictvím poskytovatele MFA. Opětovná aktivace serverů MFA, aby je propojí se zprostředkovatelem MFA, nebude mít vliv na telefonní hovor a textové zprávy, ale oznámení mobilní aplikace přestanou fungovat pro všechny uživatele, dokud mobilní aplikace znovu neaktivujete.

### <a name="removing-an-authentication-provider"></a>Odebrání poskytovatele ověřování

> [!CAUTION]
> Při odstraňování zprostředkovatele ověřování se nejedná o žádné potvrzení. Výběr možnosti **Odstranit** je trvalý proces.

Zprostředkovatele ověřování najdete v **Azure Portal** > **Azure Active Directory** > zprostředkovatelé**MFA** > . Kliknutím na uvedené zprostředkovatele zobrazíte podrobnosti a konfigurace přidružené k tomuto poskytovateli.

Než odeberete poskytovatele ověřování, poznamenejte si všechna vlastní nastavení nakonfigurovaná ve vašem poskytovateli. Rozhodněte, jaká nastavení je potřeba migrovat do obecného nastavení MFA od svého poskytovatele a dokončete migraci těchto nastavení. 

Azure MFA servery propojené s poskytovateli bude nutné znovu aktivovat pomocí přihlašovacích údajů vygenerovaných v **Azure Portal** > **Azure Active Directory** > **nastavení serveru** **MFA** > . Před opětovnou aktivací se musí v `\Program Files\Multi-Factor Authentication Server\Data\` adresáři na serverech Azure MFA ve vašem prostředí odstranit následující soubory:

- caCert
- certifikační
- groupCACert
- Klíči skupiny
- Parametr
- Licenční klíč
- primární klíč

![Odstranění poskytovatele ověřování z Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Po potvrzení, že se všechna nastavení migrují, můžete přejít na **Azure Portal** > **Azure Active Directory** > zprostředkovatelé**vícefaktorového ověřování** > a vybrat tři tečky **...**  a vyberte **Odstranit**.

> [!WARNING]
> Odstranění poskytovatele ověřování odstraní všechny informace o vytváření sestav přidružené k tomuto poskytovateli. Před odstraněním poskytovatele můžete chtít sestavy aktivit Uložit.

> [!NOTE]
> Uživatelé se staršími verzemi aplikace Microsoft Authenticator a Azure MFA serveru můžou potřebovat znovu zaregistrovat svoji aplikaci.

## <a name="next-steps"></a>Další postup

[Konfigurace nastavení služby Multi-Factor Authentication](howto-mfa-mfasettings.md)
