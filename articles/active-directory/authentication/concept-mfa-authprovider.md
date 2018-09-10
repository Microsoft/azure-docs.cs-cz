---
title: Kdy a jak používat poskytovatele Azure Multi-Factor Auth?
description: Kdy byste měli použít poskytovatele ověřování s Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 8e77a33667bd6794f667348958e0edb9c6a8fb0d
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094973"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kdy použít poskytovatele Azure Multi-Factor Authentication

Dvoustupňové ověřování je k dispozici ve výchozím nastavení pro globální správce, kteří mají uživatele služeb Azure Active Directory a Office 365. Pokud ale budete chtít využívat výhod [pokročilých funkcí](howto-mfa-mfasettings.md) měli byste si zakoupit plnou verzi Azure Multi-Factor Authentication (MFA).

Poskytovatele Azure Multi-Factor Auth umožňuje využít výhod funkcí poskytovaných službou Azure Multi-Factor Authentication pro uživatele, kteří **nemají licence**.

Pokud máte licence, které pokrývají všechny uživatele ve vaší organizaci, pak nepotřebujete poskytovatele Azure Multi-Factor auth. Vytvoření poskytovatele ověřování Azure Multi-Factor Authentication, pouze v případě, že budete taky muset zadat dvoustupňové ověřování pro uživatele, kteří nemají licence.

> [!NOTE]
> Může vytvořit už nebude platit 1. září 2018 nové zprostředkovatelé vícefaktorového ověřování. Existující zprostředkovatelé vícefaktorového ověřování nadále používat a aktualizovat. Ověřování službou Multi-Factor Authentication bude i nadále k dispozici funkce v licence Azure AD Premium.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Upozornění související s Azure MFA SDK

Pokud si chcete stáhnout sadu SDK, budete potřebovat poskytovatele Azure Multi-Factor Auth. Poznámka: Sada SDK je zastaralá a již není podporována pro nové zákazníky a pouze budou nadále fungovat až do 14. listopadu 2018. Po tomto datu se volání této sady SDK nezdaří.

Chcete-li stáhnout sadu SDK, vytvořte poskytovatele Azure Multi-Factor Auth i v případě, že máte licence Azure MFA, AAD Premium nebo jiné sady. Pokud vytvoříte poskytovatele Azure Multi-Factor Auth pro tento účel a máte už licence, nezapomeňte poskytovatele vytvořit podle modelu **Na povoleného uživatele**. Potom propojte poskytovatele s adresáři, který obsahuje Azure MFA, Azure AD Premium nebo licence na ostatní sady. Tato konfigurace zajistí, že nebudete dostávat faktury, pokud nemáte více jedinečných uživatelů provádějících dvoustupňové ověřování než počet vlastněných licencí.

## <a name="what-is-an-mfa-provider"></a>Co je poskytovatel MFA?

Existují dva typy poskytovatelů ověřování, a rozdíl mezi nimi spočívá jak se vaše předplatné Azure účtuje. Možnost podle ověření zjišťuje počet ověření za měsíc v rámci vašeho tenanta. Tato možnost je vhodná, pokud máte řadu uživatelů, kteří se ověřují jenom občas. Možnost podle uživatelů zjišťuje počet jednotlivců za měsíc, kteří ve vašem tenantovi provedli dvoustupňové ověření. Tato možnost je vhodná, pokud máte uživatelé s licencemi, ale potřebujete rozšířit vícefaktorového ověřování pro další uživatele nad rámec licenčních omezení.

## <a name="manage-your-mfa-provider"></a>Správa poskytovatele MFA

Po vytvoření poskytovatele MFA není možné změnit model použití (na povoleného uživatele nebo na ověření).

Pokud jste zakoupili dostatek licencí pro pokrytí všech uživatelů s povoleným vícefaktorovým Ověřováním, můžete poskytovatele MFA odstranit úplně.

Pokud poskytovatel MFA není propojený s tenantem Azure AD nebo pokud propojujete poskytovatele MFA s jiným tenantem Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenesou. Stávající Azure MFA servery je také potřeba znovu aktivovat pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím poskytovatele MFA. Opětovná aktivace MFA serverů za účelem propojují se s poskytovatele MFA nebude mít vliv na telefonního hovoru a textové zprávy ověřování, ale oznámení mobilní aplikace přestanou fungovat pro všechny uživatele do opětovné aktivace mobilní aplikace.

## <a name="next-steps"></a>Další postup

[Konfigurace nastavení služby Multi-Factor Authentication](howto-mfa-mfasettings.md)
