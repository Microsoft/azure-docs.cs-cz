---
title: Připojte své pracovní zařízení k síti AD vaší organizace
description: Naučte se připojit své pracovní zařízení k síti vaší organizace.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91536966"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Připojte své pracovní zařízení k síti vaší organizace.
Připojte zařízení s Windows 10 vlastněné do sítě vaší organizace, abyste měli přístup k potenciálně omezeným prostředkům.

## <a name="what-happens-when-you-join-your-device"></a>Co se stane, když se připojíte k zařízení
Když připojujete zařízení s Windows 10 k síti vaší organizace, dojde k následujícím akcím:

- Windows zaregistruje vaše zařízení do sítě vaší organizace a umožní vám přístup k prostředkům pomocí osobního účtu. Po registraci zařízení Windows do sítě připojí vaše zařízení, takže můžete k přihlášení a přístup k prostředkům s omezeným přístupem použít uživatelské jméno a heslo vaší organizace.

- V závislosti na volbách vaší organizace se může zobrazit výzva k nastavení dvoustupňového ověřování prostřednictvím [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) nebo [bezpečnostních údajů](./security-info-setup-signin.md).

- Volitelně můžete v závislosti na volbách vaší organizace automaticky zaregistrované v rámci správy mobilních zařízení, například Microsoft Intune. Další informace o registraci v Microsoft Intune najdete v tématu [registrace zařízení v Intune](/intune-user-help/enroll-your-device-in-intune-all).

- Provedete to pomocí automatického přihlašování pomocí účtu organizace.

## <a name="to-join-a-brand-new-windows-10-device"></a>Připojení ke značce – nové zařízení s Windows 10
Pokud je vaše zařízení zcela nové a ještě není nastavené, můžete k připojení zařízení k síti použít proces počátečního prostředí (OOBE) Windows.

1. Spusťte nové zařízení a spusťte proces OOBE.

2. Na obrazovce **Přihlásit se účtem Microsoft** zadejte svou pracovní nebo školní e-mailovou adresu.

    ![Přihlašovací obrazovka s e-mailovou adresou](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Do obrazovky **Zadejte heslo** zadejte své heslo.

    ![Zadejte obrazovku pro heslo.](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Na svém mobilním zařízení schvalte své zařízení, aby mohl přistupovat k vašemu účtu. 

    ![Snímek obrazovky s oznámením o zadání hesla](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Dokončete proces OOBE, včetně nastavení nastavení ochrany osobních údajů a nastavení Windows Hello (v případě potřeby).

    Vaše zařízení je teď připojené k síti vaší organizace.

## <a name="to-make-sure-youre-joined-new-device"></a>Abyste se ujistili, že jste připojeni (nové zařízení)
Můžete se ujistit, že jste připojeni. Prohlédněte si nastavení.

1. Otevřete **Nastavení** a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Vyberte možnost **přístup do práce nebo do školy** a ujistěte se, že je zobrazený text, který je podobný jako **PŘIPOJENÝ k *\<your_organization>* Azure AD**.

    ![Snímek obrazovky, který zobrazuje okno přístup do práce nebo do školy se zvoleným účtem "připojené k (vaší organizaci)" Azure AD ".](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Připojení již nakonfigurovaného zařízení s Windows 10
Pokud máte zařízení v době a a už je nastavené, můžete pomocí těchto kroků připojit zařízení k síti.

1. Otevřete **Nastavení** a pak vyberte **účty**.

2. Vyberte možnost **přístup do práce nebo do školy** a pak vyberte **připojit**.

    ![Přístup k pracovním nebo školním odkazům a připojením](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na obrazovce **nastavit pracovní nebo školní účet** vyberte **připojit k tomuto zařízení Azure Active Directory**.

    ![Nastavení obrazovky pracovního nebo školního účtu](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na obrazovce **přihlášení, které vám přihlásíte** , zadejte svou e-mailovou adresu (například alain@contoso.com ) a pak vyberte **Další**.

    ![Pojďme vám přihlášený displej](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na obrazovce **Zadejte heslo** zadejte heslo a pak vyberte **Přihlásit** se.

    ![Zadejte heslo](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Na svém mobilním zařízení schvalte své zařízení, aby mohl přistupovat k vašemu účtu. 

    ![Obrazovka s mobilním oznámením](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na obrazovce Ujistěte se,  **že se jedná o vaši organizaci** , zkontrolujte informace a ujistěte se, že jsou správné, a pak vyberte **připojit**.

    ![Ujistěte se, že se jedná o ověřovací obrazovku vaší organizace.](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na obrazovce **vše nastavené** klikněte na **Hotovo**.

    ![Obrazovka vše je nastaveno](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Abyste se ujistili, že jste připojeni
Můžete se ujistit, že jste připojeni. Prohlédněte si nastavení.

1. Otevřete **Nastavení** a pak vyberte **účty**.

    ![Účty na obrazovce nastavení](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Vyberte možnost **přístup do práce nebo do školy** a ujistěte se, že je zobrazený text, který je podobný jako **PŘIPOJENÝ k *\<your_organization>* Azure AD**.

    ![Přístup k pracovní nebo školní obrazovce s připojeným účtem contoso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Další kroky
Po připojení zařízení k síti vaší organizace byste měli mít přístup ke všem svým prostředkům pomocí informací o pracovním nebo školním účtu.

- Pokud vaše organizace chce zaregistrovat svoje osobní zařízení, například váš telefon, přečtěte si téma [registrace osobního zařízení v síti vaší organizace](user-help-register-device-on-network.md).

- Pokud je vaše organizace spravovaná pomocí Microsoft Intune a máte dotazy týkající se registrace, přihlášení nebo jakéhokoli problému souvisejícího s Intune, přečtěte si [obsah uživatelské pomoci Intune](/intune-user-help/use-managed-devices-to-get-work-done).