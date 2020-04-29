---
title: Registrace osobních zařízení v síti organizace – Azure AD
description: Zjistěte, jak zaregistrovat svoje osobní zařízení v síti vaší organizace, abyste měli přístup k chráněným prostředkům vaší organizace.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063862"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrace svého osobního zařízení v síti vaší organizace
Zaregistrujte své osobní zařízení (obvykle telefon nebo tablet) v síti vaší organizace. Po zaregistrování zařízení bude mít přístup k prostředkům s omezeným přístupem vaší organizace.

>[!Note]
>Tento článek používá pro demonstrační účely zařízení s Windows, ale můžete také registrovat zařízení s iOS, Androidem nebo macOS.

## <a name="what-happens-when-you-register-your-device"></a>Co se stane, když zaregistrujete zařízení
Při registraci zařízení v síti vaší organizace dojde k následujícím akcím:

- Systém Windows zaregistruje vaše zařízení v síti vaší organizace.

- V závislosti na volbách vaší organizace se může zobrazit výzva k nastavení dvoustupňového ověřování prostřednictvím [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) nebo [bezpečnostních údajů](user-help-security-info-overview.md).

- Volitelně můžete v závislosti na volbách vaší organizace automaticky zaregistrované v rámci správy mobilních zařízení, například Microsoft Intune. Další informace o registraci v Microsoft Intune najdete v tématu [registrace zařízení v Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Projdete přihlašovacím jménem a heslem svého pracovního nebo školního účtu.

## <a name="to-register-your-windows-device"></a>Registrace zařízení s Windows

Pomocí těchto kroků zaregistrujete své osobní zařízení v síti.

1. Otevřete **Nastavení**a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Vyberte možnost **přístup do práce nebo do školy**a pak vyberte **připojit** z **pracovní nebo školní** obrazovky.

    ![Přístup k pracovní nebo školní obrazovce s zvýrazněnou možností připojit](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na obrazovce **Přidat pracovní nebo školní účet** zadejte e-mailovou adresu svého pracovního nebo školního účtu a pak vyberte **Další**. Například, alain@contoso.com.

4. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak vyberte **Přihlásit**se.

5. Dokončete zbývající část procesu registrace, včetně schvalování žádosti o ověření identity (Pokud používáte dvoustupňové ověřování) a nastavte Windows Hello (v případě potřeby).

## <a name="to-verify-that-youre-registered"></a>Ověření, že jste zaregistrováni
Můžete se ujistit, že jste zaregistrováni, a to tak, že si prohlédněte nastavení.

1. Otevřete **Nastavení**a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Vyberte možnost **přístup do práce nebo do školy**a ujistěte se, že jste viděli svůj pracovní nebo školní účet.

    ![Přístup k pracovní nebo školní obrazovce s připojeným účtem contoso](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Další kroky
Po registraci osobního zařízení do sítě vaší organizace byste měli mít přístup k většině svých prostředků.

- Pokud vaše organizace požaduje, abyste se připojili k pracovnímu zařízení, přečtěte si téma [připojení pracovního zařízení k síti vaší organizace](user-help-join-device-on-network.md).



