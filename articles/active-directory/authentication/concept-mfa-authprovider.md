---
title: Poskytovatelé ověřování Azure Multi-Factor Authentication – Azure Active Directory
description: Kdy byste měli použít poskytovatele ověřování s Azure MFA?
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
ms.openlocfilehash: a7e4772ba2b3d8adf163d6f2932461e1e939ed84
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052439"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kdy použít poskytovatele služby Azure Multi-Factor Authentication

> [!IMPORTANT]
> Od 1. září 2018 se už možná nebudou vytvářet Noví zprostředkovatelé ověřování. Stávající zprostředkovatelé ověřování se můžou dál používat a aktualizovat, ale migrace už není možná. Multi-Factor Authentication bude i nadále k dispozici jako funkce v Azure AD Premium licencích.

Dvoustupňové ověřování je ve výchozím nastavení k dispozici pro globální správce, kteří mají Azure Active Directory a Microsoft 365 uživatele. Pokud ale budete chtít využívat výhod [pokročilých funkcí](howto-mfa-mfasettings.md) měli byste si zakoupit plnou verzi Azure Multi-Factor Authentication (MFA).

Poskytovatel Azure Multi-Factor auth se používá k využití funkcí poskytovaných službou Azure Multi-Factor Authentication pro uživatele, kteří nemají **licence**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Upozornění související se sadou Azure MFA SDK

Všimněte si, že sada SDK je zastaralá a bude i nadále fungovat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří.

## <a name="what-is-an-mfa-provider"></a>Co je poskytovatel MFA?

Existují dva typy zprostředkovatelů ověřování a rozdíl mezi tím, jak se účtuje vaše předplatné Azure. Možnost podle ověření zjišťuje počet ověření za měsíc v rámci vašeho tenanta. Tato možnost je vhodná, pokud máte řadu uživatelů, kteří se ověřují jenom občas. Možnost podle uživatelů zjišťuje počet jednotlivců za měsíc, kteří ve vašem tenantovi provedli dvoustupňové ověření. Tato možnost je vhodná, pokud máte uživatelé s licencemi, ale potřebujete rozšířit vícefaktorového ověřování pro další uživatele nad rámec licenčních omezení.

## <a name="manage-your-mfa-provider"></a>Správa poskytovatele MFA

Po vytvoření poskytovatele MFA není možné změnit model použití (na povoleného uživatele nebo na ověření).

Pokud jste si koupili dostatek licencí pro pokrytí všech uživatelů, kteří jsou povoleni pro MFA, můžete poskytovatele MFA odstranit úplně.

Pokud poskytovatel MFA není propojený s tenantem Azure AD nebo pokud propojujete poskytovatele MFA s jiným tenantem Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenesou. Existující servery Azure MFA se taky musí znovu aktivovat pomocí aktivačních přihlašovacích údajů vygenerovaných prostřednictvím poskytovatele MFA.

### <a name="removing-an-authentication-provider"></a>Odebrání poskytovatele ověřování

> [!CAUTION]
> Při odstraňování zprostředkovatele ověřování se nejedná o žádné potvrzení. Výběr možnosti **Odstranit** je trvalý proces.

Zprostředkovatele ověřování najdete v **Azure portal**  >  **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Zprostředkovatelé**zabezpečení MFA Azure Portal. Kliknutím na uvedené zprostředkovatele zobrazíte podrobnosti a konfigurace přidružené k tomuto poskytovateli.

Než odeberete poskytovatele ověřování, poznamenejte si všechna vlastní nastavení nakonfigurovaná ve vašem poskytovateli. Rozhodněte, jaká nastavení je potřeba migrovat do obecného nastavení MFA od svého poskytovatele a dokončete migraci těchto nastavení. 

Servery Azure MFA propojené s poskytovateli bude nutné znovu aktivovat pomocí přihlašovacích údajů vygenerovaných v části **Azure Portal**  >  **Azure Active Directory**  >  **zabezpečení**  >  **MFA**  >  **serveru**MFA. Před opětovnou aktivací se musí `\Program Files\Multi-Factor Authentication Server\Data\` v adresáři na serverech Azure MFA ve vašem prostředí odstranit následující soubory:

- caCert
- cert
- groupCACert
- Klíči skupiny
- Parametr
- Licenční klíč
- primární klíč

![Odstranění poskytovatele ověřování z Azure Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Po potvrzení, že se všechna nastavení migrují, můžete přejít na poskytovatele **Azure Portal**  >  **Azure Active Directory**  >  **Security**  >  **MFA**  >  **Providers** a vybrat tři tečky **...** a vybrat možnost **Odstranit**.

> [!WARNING]
> Odstranění poskytovatele ověřování odstraní všechny informace o vytváření sestav přidružené k tomuto poskytovateli. Před odstraněním poskytovatele můžete chtít sestavy aktivit Uložit.

> [!NOTE]
> Uživatelé se staršími verzemi aplikace Microsoft Authenticator a Azure MFA serveru můžou potřebovat znovu zaregistrovat svoji aplikaci.

## <a name="next-steps"></a>Další kroky

[Konfigurace nastavení služby Multi-Factor Authentication](howto-mfa-mfasettings.md)
