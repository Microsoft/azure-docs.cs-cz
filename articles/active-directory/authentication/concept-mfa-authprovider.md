---
title: Poskytovatelé vícefaktorového autu Azure – Azure Active Directory
description: Kdy byste měli používat zprostředkovatele umítavého vztahu s Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309901"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kdy použít zprostředkovatele vícefaktorového ověřování Azure

> [!IMPORTANT]
> září 1st, 2018 již nemusí být vytvořeni noví poskytovatelé auth. Stávající zprostředkovatelé auth mohou být nadále používány a aktualizovány, ale migrace již není možná. Vícefaktorové ověřování bude i nadále dostupné jako funkce v licencích Azure AD Premium.

Dvoustupňové ověřování je k dispozici ve výchozím nastavení pro globální správce, kteří mají uživatele služeb Azure Active Directory a Office 365. Pokud ale budete chtít využívat výhod [pokročilých funkcí](howto-mfa-mfasettings.md) měli byste si zakoupit plnou verzi Azure Multi-Factor Authentication (MFA).

Azure Multi-Factor Auth Provider se používá k využití funkcí poskytovaných Azure Multi-Factor Authentication pro uživatele, kteří **nemají licence**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Upozornění týkající se sady Azure MFA SDK

Poznámka: Sada SDK byla zastaralá a bude pokračovat v práci až do 14. Po tomto datu se volání této sady SDK nezdaří.

## <a name="what-is-an-mfa-provider"></a>Co je poskytovatel MFA?

Existují dva typy poskytovatelů auth a rozdíl je kolem jak se účtuje vaše předplatné Azure. Možnost podle ověření zjišťuje počet ověření za měsíc v rámci vašeho tenanta. Tato možnost je vhodná, pokud máte řadu uživatelů, kteří se ověřují jenom občas. Možnost podle uživatelů zjišťuje počet jednotlivců za měsíc, kteří ve vašem tenantovi provedli dvoustupňové ověření. Tato možnost je vhodná, pokud máte uživatelé s licencemi, ale potřebujete rozšířit vícefaktorového ověřování pro další uživatele nad rámec licenčních omezení.

## <a name="manage-your-mfa-provider"></a>Správa poskytovatele MFA

Po vytvoření poskytovatele MFA není možné změnit model použití (na povoleného uživatele nebo na ověření).

Pokud jste zakoupili dostatek licencí, které by pokryly všechny uživatele, kteří jsou povoleni pro vícefaktorové povolení, můžete odstranit zprostředkovatele MFA úplně.

Pokud poskytovatel MFA není propojený s tenantem Azure AD nebo pokud propojujete poskytovatele MFA s jiným tenantem Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenesou. Také existující servery Azure MFA je třeba znovu aktivovat pomocí aktivačních pověření generovaných prostřednictvím zprostředkovatele MFA.

### <a name="removing-an-authentication-provider"></a>Odebrání zprostředkovatele ověřování

> [!CAUTION]
> Při odstranění zprostředkovatele ověřování není potvrzeno žádné potvrzení. Výběr **odstranit** je trvalý proces.

Zprostředkovatelé ověřování najdete na **webu Azure Portal** > **Azure Azure AD** > **Security** > **MFA** > **Providers**. Kliknutím na uvedené zprostředkovatele zobrazíte podrobnosti a konfigurace přidružené k tomuto poskytovateli.

Před odebráním zprostředkovatele ověřování si poznamenejte všechna přizpůsobená nastavení nakonfigurovaná ve vašem zprostředkovateli. Rozhodněte, jaká nastavení je třeba migrovat do obecných nastavení vícefaktorové informace od poskytovatele, a dokončete migraci těchto nastavení. 

Servery Azure MFA propojené s poskytovateli budou muset být znovu aktivovány pomocí přihlašovacích údajů generovaných v rámci nastavení serveru Azure **Portal** > **Azure Active Directory** > **Security** > **MFA** > **Server**. Před opětovnou aktivací je nutné odstranit `\Program Files\Multi-Factor Authentication Server\Data\` z adresáře na serverech Azure MFA ve vašem prostředí následující soubory:

- caCert řekl:
- cert
- skupinaCACert
- groupKey
- Groupname
- licenseKey
- pklíč

![Odstranění poskytovatele auth z webu Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Až potvrdíte, že všechna nastavení byla migrována, můžete přejít na **portál** > Azure**Azure AZD** > **Zprostředkovatelé** **mfa** > **zabezpečení** > služby Azure active directory a vybrat tři tečky **...** a vybrat **Odstranit**.

> [!WARNING]
> Odstraněním zprostředkovatele ověřování odstraníte všechny informace o vykazování přidružené k tomuto zprostředkovateli. Před odstraněním zprostředkovatele můžete chtít uložit sestavy aktivit.

> [!NOTE]
> Uživatelé se staršími verzemi aplikace Microsoft Authenticator a Azure MFA Server možná budou muset znovu zaregistrovat svou aplikaci.

## <a name="next-steps"></a>Další kroky

[Konfigurace nastavení služby Multi-Factor Authentication](howto-mfa-mfasettings.md)
