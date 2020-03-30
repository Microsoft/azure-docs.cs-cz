---
title: Připojte se k pracovnímu zařízení k síti vaší organizace – ad
description: Přečtěte si, jak se připojit k pracovnímu zařízení k síti vaší organizace.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 0ff8b85a15d94ded2d702e0df247f9ebc4d3f923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266309"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Připojení pracovního zařízení k síti vaší organizace
Připojte se k zařízení s Windows 10 vlastněné prací do sítě vaší organizace, abyste měli přístup k potenciálně omezeným prostředkům.

## <a name="what-happens-when-you-join-your-device"></a>Co se stane, když se připojíte k zařízení
Když se připojíte k zařízení s Windows 10 k síti vaší organizace, dojde k následujícím akcím:

- Systém Windows zaregistruje zařízení do sítě vaší organizace a umožní vám přístup k prostředkům pomocí osobního účtu. Po registraci zařízení systém Windows připojí vaše zařízení k síti, takže můžete pomocí uživatelského jména a hesla vaší organizace přihlásit se k prostředkům s omezeným přístupem a získat k nim přístup.

- Volitelně můžete být na základě voleb vaší organizace požádáni o nastavení dvoustupňového ověření pomocí [vícefaktorového ověřování](multi-factor-authentication-end-user-first-time.md) nebo [bezpečnostních údajů](user-help-security-info-overview.md).

- Volitelně můžete být na základě možností vaší organizace automaticky zaregistrovaní do správy mobilních zařízení, jako je Microsoft Intune. Další informace o registraci v Microsoft Intune najdete v [tématu Registrace zařízení v Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Budete procházet procesem přihlášení pomocí automatického přihlášení pomocí účtu organizace.

## <a name="to-join-a-brand-new-windows-10-device"></a>Připojení ke zbrusu novému zařízení s Windows 10
Pokud je vaše zařízení zcela nové a ještě nebylo nastaveno, můžete projít procesem Windows Out of Box Experience (OOBE) a připojit zařízení k síti.

1. Spusťte nové zařízení a zahajte proces OOBE.

2. Na obrazovce **Přihlásit se pomocí Microsoftu** zadejte svou pracovní nebo školní e-mailovou adresu.

    ![Přihlašovací obrazovka s e-mailovou adresou](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Na obrazovce **Zadejte heslo** zadejte heslo.

    ![Zadejte obrazovku s heslem](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Na mobilním zařízení schvalte zařízení, aby mělo přístup k vašemu účtu. 

    ![Obrazovka oznámení pro mobilní zařízení](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Dokončete proces OOBE, včetně nastavení ochrany osobních údajů a nastavení Windows Hello (v případě potřeby).

    Vaše zařízení je nyní připojeno k síti vaší organizace.

## <a name="to-make-sure-youre-joined"></a>Chcete-li se ujistit, že jste připojeni
Můžete se ujistit, že jste připojeni při pohledu na vaše nastavení.

1. Otevřete **Nastavení**a vyberte **Účty**.

    ![Účty na obrazovce Nastavení](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Vyberte **Přístup k práci nebo do školy**a ujistěte se, že se zobrazí text, který říká něco jako Připojení k ** * \<your_organization>* Azure AD**.

    ![Přístup k pracovní nebo školní obrazovce s připojeným účtem contoso](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Připojení k již nakonfigurovanému zařízení s Windows 10
Pokud máte zařízení nějakou dobu a už je nastavené, můžete zařízení připojit k síti podle těchto kroků.

1. Otevřete **Nastavení**a vyberte **Účty**.

2. Vyberte **Přístup k práci nebo škole**a pak vyberte **Připojit**.

    ![Přístup k pracovním nebo školním odkazům a připojení](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Na obrazovce **Nastavení pracovního nebo školního účtu** vyberte **Připojit toto zařízení k Azure Active Directory**.

    ![Nastavení obrazovky pracovního nebo školního účtu](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Na obrazovce **Let's get you signed in** zadejte alain@contoso.comsvou e-mailovou adresu (například) a pak vyberte **Další**.

    ![Pojďme si vás podepsal na obrazovce](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Na obrazovce **Zadejte heslo** zadejte heslo a vyberte **Přihlásit se**.

    ![Zadejte heslo](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Na mobilním zařízení schvalte zařízení, aby mělo přístup k vašemu účtu. 

    ![Obrazovka oznámení pro mobilní zařízení](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Na obrazovce **Ujistěte se, že se jedná** o vaši organizaci, zkontrolujte informace, abyste se ujistili, že jsou správné, a pak vyberte **Připojit se**.

    ![Zkontrolujte, zda se jedná o ověřovací obrazovku vaší organizace.](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Na obrazovce **Vše je nastaveno** na **Hotovo**.

    ![Máte nastavenou obrazovku](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Chcete-li se ujistit, že jste připojeni
Můžete se ujistit, že jste připojeni při pohledu na vaše nastavení.

1. Otevřete **Nastavení**a vyberte **Účty**.

    ![Účty na obrazovce Nastavení](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Vyberte **Přístup k práci nebo do školy**a ujistěte se, že se zobrazí text, který říká něco jako Připojení k ** * \<your_organization>* Azure AD**.

    ![Přístup k pracovní nebo školní obrazovce s připojeným účtem contoso](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Další kroky
Po připojení zařízení k síti vaší organizace byste měli mít přístup ke všem prostředkům pomocí informací o pracovním nebo školním účtu.

- Pokud vaše organizace chce, abyste zaregistrovali své osobní zařízení, například telefon, přečtěte si informace [o registraci osobního zařízení v síti organizace](user-help-register-device-on-network.md).

- Pokud se vaše organizace spravuje pomocí Microsoft Intune a máte otázky ohledně registrace, přihlášení nebo jiného problému souvisejícího s Intune, přečtěte si [obsah uživatelské nápovědy intune](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).