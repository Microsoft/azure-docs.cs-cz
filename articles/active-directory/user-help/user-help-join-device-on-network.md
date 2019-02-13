---
title: Připojení pracovních zařízení k síti vaší organizace – Azure Active Directory | Dokumentace Microsoftu
description: Informace o připojení pracovních zařízení k síti vaší organizace.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: b433d8556377206ea53ece2764e251dcb266eb95
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192349"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Připojení pracovních zařízení k síti vaší organizace
Připojte se k zařízení s Windows 10 vlastněných společností práce k síti vaší organizace, budete mít přístup k prostředkům potenciálně s omezeným přístupem.

## <a name="what-happens-when-you-join-your-device"></a>Co se stane, když svoje zařízení připojíte
Když zařízení s Windows 10 se připojení k síti vaší organizace, se stane následující akce:

- Windows registrů zařízení k síti vaší organizace, že vám dovolí můžete přístup k prostředkům pomocí osobního účtu. Po registraci zařízení s Windows potom připojí zařízení k síti, tak uživatelské jméno a heslo vaší organizace můžete použít k přihlášení a přístup k prostředkům s omezeným přístupem.

- Volitelně můžete na základě vaší organizace voleb, můžete být požádáni nastavit dvoustupňové ověřování pomocí kteréhokoliv [ověřování službou Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) nebo [bezpečnostní údaje](user-help-security-info-overview.md).

- Volitelně můžete podle volby vaší organizace, vám může být automaticky zaregistroval do správy mobilních zařízení, jako je například Microsoft Intune. Další informace o registraci v Microsoft Intune najdete v tématu [registrace zařízení v Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Přejdete prostřednictvím procesu přihlášení pomocí automatického přihlášení pomocí účtu organizace.

## <a name="to-join-a-brand-new-windows-10-device"></a>Připojit zařízení s Windows 10 zcela nový
Pokud vaše zařízení je zcela nové a nebyla ještě nastavena, můžete projít procesem Windows z pole prostředí (OOBE) připojení zařízení k síti.

1. Spuštění nové zařízení a zahájit proces OOBE.

2. Na **přihlásit se účtem Microsoft** obrazovky, zadejte svou pracovní nebo školní e-mailovou adresu.

    ![Přihlašovací obrazovka s e-mailovou adresu](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Na **zadejte své heslo** zadejte své heslo.

    ![Zadejte heslo obrazovky](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Na vašem mobilním zařízení Schvalte vaše zařízení, můžete přístup k účtu. 

    ![Obrazovka mobilní oznámení](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Dokončete proces OOBE, včetně nastavení nastavení ochrany osobních údajů a nastavení Windows Hello (v případě potřeby).

    Zařízení je teď připojené k síti vaší organizace.

## <a name="to-make-sure-youre-joined"></a>K Ujistěte se, že je připojený
Abyste měli jistotu, že už jste se zapojili podle nastavení.

1. Otevřít **nastavení**a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Vyberte **přístup do práce nebo do školy**a ujistěte se, že se zobrazí text, který říká něco jako, **připojené k *< Vaše_organizace >* Azure AD**.

    ![Přístup k obrazovce pracovním nebo školním účtem připojených contoso](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Připojit už nakonfigurované zařízení s Windows 10
Pokud jste měli zařízení pro nějakou dobu a je již byla nastavená, můžete následujícím postupem připojení zařízení k síti.

1. Otevřít **nastavení**a pak vyberte **účty**.

2. Vyberte **přístup do práce nebo do školy**a pak vyberte **připojit**.

    ![Přístup do práce nebo škola a připojit odkazy](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na **nastavit pracovní nebo školní účet** obrazovky, vyberte **připojit toto zařízení k Azure Active Directory**.

    ![Nastavit pracovní nebo školní účet obrazovky](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na **Pojďme vás přihlásit** zadejte e-mailovou adresu (například alain@contoso.com) a pak vyberte **Další**.

    ![Pojďme vás přihlásit obrazovky](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na **zadejte heslo** obrazovky, zadejte heslo a pak vyberte **přihlášení**.

    ![Zadat heslo](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Na vašem mobilním zařízení Schvalte vaše zařízení, můžete přístup k účtu. 

    ![Obrazovka mobilní oznámení](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na **Ujistěte se, že toto je vaše organizace** obrazovky, zkontrolujte informace a ujistěte se, že je vhodné a pak vyberte **připojit**.

    ![Ujistěte se, že toto je vaše organizace obrazovka ověření](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na **všechno máte nastavené** obrazovce, klikněte na tlačítko **provádí**.

    ![Jste všechny sady obrazovky](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>K Ujistěte se, že je připojený
Abyste měli jistotu, že už jste se zapojili podle nastavení.

1. Otevřít **nastavení**a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Vyberte **přístup do práce nebo do školy**a ujistěte se, že se zobrazí text, který říká něco jako, **připojené k *< Vaše_organizace >* Azure AD**.

    ![Přístup k obrazovce pracovním nebo školním účtem připojených contoso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Další postup
Po připojení zařízení k síti vaší organizace, byste měli mít přístup k všech vašich prostředků pomocí svého pracovního nebo školního účtu.

- Pokud vaše organizace hodlá registraci osobních zařízení, jako je telefon, přečtěte si téma [registraci osobních zařízení v síti vaší organizace](user-help-register-device-on-network.md).

