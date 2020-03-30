---
title: Registrace osobních zařízení v síti organizace – Azure AD
description: Přečtěte si, jak zaregistrovat osobní zařízení v síti vaší organizace, abyste měli přístup k chráněným prostředkům vaší organizace.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e2e6585bac100a09f3f98037e90b24738e22816f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063862"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrace osobního zařízení v síti organizace
Zaregistrujte své osobní zařízení (obvykle telefon nebo tablet) v síti vaší organizace. Po registraci bude mít zařízení přístup k omezeným prostředkům vaší organizace.

>[!Note]
>Tento článek používá zařízení s Windows pro demonstrační účely, ale můžete také zaregistrovat zařízení se systémem iOS, Android nebo macOS.

## <a name="what-happens-when-you-register-your-device"></a>Co se stane, když zařízení zaregistrujete
Při registraci zařízení v síti vaší organizace se budou akce:

- Systém Windows zaregistruje zařízení v síti vaší organizace.

- Volitelně můžete být na základě voleb vaší organizace požádáni o nastavení dvoustupňového ověření pomocí [vícefaktorového ověřování](multi-factor-authentication-end-user-first-time.md) nebo [bezpečnostních údajů](user-help-security-info-overview.md).

- Volitelně můžete být na základě možností vaší organizace automaticky zaregistrovaní do správy mobilních zařízení, jako je Microsoft Intune. Další informace o registraci v Microsoft Intune najdete v [tématu Registrace zařízení v Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Budete procházet procesem přihlášení pomocí uživatelského jména a hesla pro váš pracovní nebo školní účet.

## <a name="to-register-your-windows-device"></a>Registrace zařízení se systémem Windows

Chcete-li zaregistrovat své osobní zařízení v síti, postupujte takto.

1. Otevřete **Nastavení**a vyberte **Účty**.

    ![Účty na obrazovce Nastavení](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Vyberte **Přístup k práci nebo škole**a pak na obrazovce Práce nebo škola **accessu** vyberte **Připojit.**

    ![Přístup k pracovní nebo školní obrazovce se zvýrazněnou možností Připojit](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na obrazovce **Přidat pracovní nebo školní účet** zadejte svou e-mailovou adresu pracovního nebo školního účtu a pak vyberte **Další**. Například, alain@contoso.com.

4. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak **vyberte Přihlásit se**.

5. Dokončete zbytek procesu registrace, včetně schválení žádosti o ověření identity (pokud používáte dvoustupňové ověření) a nastavení Windows Hello (v případě potřeby).

## <a name="to-verify-that-youre-registered"></a>Ověření, že jste zaregistrováni
Můžete se ujistit, že jste registrováni, když se podíváte na vaše nastavení.

1. Otevřete **Nastavení**a vyberte **Účty**.

    ![Účty na obrazovce Nastavení](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Vyberte **Přístup k práci nebo škole**a ujistěte se, že vidíte svůj pracovní nebo školní účet.

    ![Přístup k pracovní nebo školní obrazovce s připojeným účtem contoso](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Další kroky
Po registraci osobního zařízení do sítě vaší organizace byste měli mít přístup k většině prostředků.

- Pokud vaše organizace chce, abyste se připojili k pracovnímu zařízení, přečtěte si informace [o připojení k pracovnímu zařízení v síti vaší organizace](user-help-join-device-on-network.md).



