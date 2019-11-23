---
title: Self-service password reset for Windows - Azure Active Directory
description: How to enable self-service password reset using forgot password at the Windows login screen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e25efcb068fe51f05dbbde50e8a96da492a735
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381227"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>How to: Enable password reset from the Windows login screen

For machines running Windows 7, 8, 8.1, and 10 you can enable users to reset their password at the Windows login screen. Users no longer have to find a device with a web browser to access the [SSPR portal](https://aka.ms/sspr).

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Obecná omezení

- Password reset is not currently supported from a Remote Desktop or from Hyper-V enhanced sessions.
- This feature does not work for networks with 802.1x network authentication deployed and the option “Perform immediately before user logon”. For networks with 802.1x network authentication deployed it is recommended to use machine authentication to enable this feature.
- Hybrid Azure AD joined machines must have network connectivity line of sight to a domain controller to use the new password and update cached credentials.
- If using an image, prior to running sysprep ensure that the web cache is cleared for the built-in Administrator prior to performing the CopyProfile step. More information about this step can be found in the support article [Performance poor when using custom default user profile](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- The following settings are known to interfere with the ability to use and reset passwords on Windows 10 devices
    - If Ctrl+Alt+Del is required by policy in versions of Windows 10 before v1809, **Reset password** will not work.
    - If lock screen notifications are turned off, **Reset password** will not work.
    - HideFastUserSwitching is set to enabled or 1
    - DontDisplayLastUserName is set to enabled or 1
    - NoLockScreen is set to enabled or 1
    - EnableLostMode is set on the device
    - Explorer.exe is replaced with a custom shell
- The combination of the following specific three settings can cause this feature to not work.
    - Interactive logon: Do not require CTRL+ALT+DEL = Disabled
    - DisableLockScreenAppNotifications = 1 or Enabled
    - IsContentDeliveryPolicyEnforced = 1 or True

## <a name="windows-10-password-reset"></a>Windows 10 password reset

### <a name="windows-10-prerequisites"></a>Windows 10 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 10 devices 
       - Port 443 to `passwordreset.microsoftonline.com` and `ajax.aspnetcdn.com`
       - Windows 10 devices only support machine-level proxy configuration
- Run at least Windows 10, version April 2018 Update (v1803), and the devices must be either:
    - Azure AD joined
    - Hybrid Azure AD joined

### <a name="enable-for-windows-10-using-intune"></a>Enable for Windows 10 using Intune

Nasazení změny konfigurace, která umožní resetování hesla z přihlašovací obrazovky, pomocí Intune je nejflexibilnější metodou. Intune vám umožňuje nasadit změnu konfigurace pro vámi zvolenou skupinu počítačů. Tato metoda vyžaduje registraci zařízení v Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Vytvoření zásad konfigurace zařízení v Intune

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na **Intune**.
1. Vytvořte nový profil konfigurace zařízení tak, že přejdete do **Konfigurace zařízení** > **Profily** > **Vytvořit profil**.
   - Zadejte výstižný název profilu.
   - Volitelně zadejte výstižný popis profilu.
   - Jako platformu vyberte **Windows 10 a novější**.
   - Jako typ profilu vyberte **Vlastní**.
1. Nakonfigurujte **Nastavení**.
   - **Přidejte** následující Nastavení identifikátoru OMA-URI, které povolí odkaz na resetování hesla.
      - Zadejte výstižný název vysvětlující, co toto nastavení dělá.
      - Volitelně zadejte výstižný popis nastavení.
      - Identifikátor **OMA-URI** nastavte na `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`.
      - **Datový typ** nastavte na **Integer**.
      - Jako **Hodnota** nastavte **1**.
      - Klikněte na tlačítko **OK**.
   - Klikněte na tlačítko **OK**.
1. Klikněte na **Vytvořit**
1. This policy can be assigned to specific users, devices, or groups. More information can be found in the article [Assign user and device profiles in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Enable for Windows 10 using the Registry

1. Sign in to the Windows PC using administrative credentials
1. Spusťte program **regedit** jako správce.
1. Nastavte následující klíč registru:
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Troubleshooting Windows 10 password reset

Protokol auditu služby Azure AD bude obsahovat informace o IP adrese a typu klienta, kde došlo k resetování hesla.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

When users reset their password from the login screen of a Windows 10 device, a low-privilege temporary account called `defaultuser1` is created. This account is used to keep the password reset process secure. The account itself has a randomly generated password, doesn’t show up for device sign-in, and will automatically be removed after the user resets their password. Multiple `defaultuser` profiles may exist but can be safely ignored.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8, and 8.1 password reset

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8, and 8.1 prerequisites

- An administrator must enable Azure AD self-service password reset from the Azure portal.
- **Users must register for SSPR before using this feature**
- Network proxy requirements
   - Windows 7, 8, and 8.1 devices
       - Port 443 to `passwordreset.microsoftonline.com`
- Patched Windows 7 or Windows 8.1 Operating System.
- TLS 1.2 enabled using the guidance found in [Transport Layer Security (TLS) registry settings](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- If more than one 3rd party credential provider is enabled on your machine, users will see more than one user profile on the login screen.

> [!WARNING]
> TLS 1.2 must be enabled, not just set to auto negotiate

### <a name="install"></a>Instalace

1. Download the appropriate installer for the version of Windows you would like to enable.
   - Software is available on the Microsoft download center at [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Sign in to the machine where you would like to install, and run the installer.
1. After installation, a reboot is highly recommended.
1. After the reboot, at the login screen choose a user and click "Forgot password?" to initiate the password reset workflow.
1. Complete the workflow following the onscreen steps to reset your password.

![Example Windows 7 clicked "Forgot password?" SSPR flow](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Silent installation

- For silent install, use the command “msiexec /i SsprWindowsLogon.PROD.msi /qn”
- For silent uninstall, use the command “msiexec /x SsprWindowsLogon.PROD.msi /qn”

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Troubleshooting Windows 7, 8, and 8.1 password reset

Events will be logged both on the machine and in Azure AD. Azure AD Events will include information about the IP address and ClientType where the password reset occurred.

![Example Windows 7 password reset in the Azure AD Audit log](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

If additional logging is required, a registry key on the machine can be changed to enable verbose logging. Enable verbose logging for troubleshooting purposes only.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- To enable verbose logging, create a `REG_DWORD: “EnableLogging”`, and set it to 1.
- To disable verbose logging, change the `REG_DWORD: “EnableLogging”` to 0.

## <a name="what-do-users-see"></a>Co vidí uživatelé

Now that you have configured password reset for your Windows devices, what changes for the user? Jak se dozví, že své heslo můžou resetovat na přihlašovací obrazovce?

![Example Windows 7 and 10 login screens with SSPR link shown](./media/howto-sspr-windows/windows-reset-password.png)

When users attempt to sign in, they now see a **Reset password** or **Forgot password** link that opens the self-service password reset experience at the login screen. Tato funkce umožňuje uživatelům resetovat své heslo, aniž by museli použít jiné zařízení pro přístup k webovému prohlížeči.

Vaši uživatelé najdou pokyny k použití této funkce v tématu popisujícím [resetování hesla k pracovnímu nebo školnímu účtu](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="next-steps"></a>Další kroky

[Plan authentication methods to allow](concept-authentication-methods.md)

[Configure Windows 10](https://docs.microsoft.com/windows/configuration/)
