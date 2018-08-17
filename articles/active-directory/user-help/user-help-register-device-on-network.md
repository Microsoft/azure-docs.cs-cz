---
title: Registrace osobních zařízení v síti vaší organizace – Azure Active Directory | Dokumentace Microsoftu
description: Informace o postupu registrace osobních zařízení v síti vaší organizace, aby měli přístup k chráněným prostředkům vaší organizace.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180745"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrace osobních zařízení v síti vaší organizace
Registrovat své osobní zařízení, obvykle telefon nebo tablet, v síti vaší organizace. Po registraci zařízení bude mít přístup k prostředkům vaší organizace s omezeným přístupem.

>[!Note]
>Tento článek používá zařízení s Windows pro demonstrační účely, ale budete taky moct registrovat zařízení se systémem iOS, Android a macOS.

## <a name="what-happens-when-you-register-your-device"></a>Co se stane při registraci zařízení
Když se registrace zařízení v síti vaší organizace, se stane následující akce:

- Windows zaregistruje zařízení v síti vaší organizace.

- Volitelně můžete na základě vaší organizace voleb, můžete být požádáni nastavit dvoustupňové ověřování pomocí kteréhokoliv [ověřování službou Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) nebo [bezpečnostní údaje](user-help-security-info-overview.md).

- Volitelně můžete podle volby vaší organizace, vám může být automaticky zaregistroval do správy mobilních zařízení, jako je například Microsoft Intune. Další informace o registraci v Microsoft Intune najdete v tématu [registrace zařízení v Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Přejdete prostřednictvím procesu přihlášení pomocí uživatelského jména a hesla pro váš osobní účet Microsoft.

## <a name="to-register-your-windows-device"></a>K registraci zařízení s Windows

Postupujte podle těchto kroků k registraci osobních zařízení ve vaší síti.

1. Otevřít **nastavení**a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Vyberte **e-mailu a účty**a pak vyberte **připojte se k účtu Microsoft**.

    ![E-mailu a účty a přidat propojení účtu Microsoft](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. Na **přidat účet Microsoft** obrazovky, zadejte e-mailovou adresu pro svůj osobní účet Microsoft.

    ![Přidání obrazovky účet Microsoft s e-mailem](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. Na **zadejte heslo** obrazovky, zadejte heslo pro svůj osobní účet Microsoft a potom vyberte **přihlášení**.

    ![Zadejte heslo obrazovky](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. Dokončete proces registrace, včetně schvalování žádosti o ověření identity (Pokud používáte dvoustupňové ověřování) a nastavení Windows Hello (v případě potřeby).

## <a name="to-make-sure-youre-registered"></a>Aby se zajistilo úspěšné registrace
Abyste měli jistotu, že budete zaregistrováni podle nastavení.

1. Otevřít **nastavení**a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Vyberte **e-mailu a účty**a ujistěte se, že se zobrazí váš osobní účet Microsoft.

    ![Přístup k obrazovce pracovním nebo školním účtem připojených contoso](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>Další postup
Až dokončíte registraci osobních zařízení k síti vaší organizace, byste měli mít přístup k maximální využití vašich prostředků.

- Pokud vaše organizace chce, abyste připojení pracovních zařízení, přečtěte si téma [připojení pracovních zařízení k síti vaší organizace](user-help-join-device-on-network.md).



