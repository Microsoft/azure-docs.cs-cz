---
title: Začínáme s aplikací Microsoft Authenticator – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak upgradovat na nejnovější verzi aplikace Microsoft Authenticator.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 590f9e2a9cf531a1124f9cb132791f2956227d9c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047253"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Začínáme s aplikací Microsoft Authenticator

Aplikace Microsoft Authenticator pomůže zabránit neoprávněnému přístupu k účtům a zastavit podvodné transakce poskytuje další úroveň zabezpečení pro váš pracovní nebo školní účet (třeba alain@contoso.com) nebo osobního účtu Microsoft (pro například alain@outlook.com).

Při použití aplikace pro dvoustupňové ověřování, můžete pracovat v jednom ze dvou způsobů:

- **Oznámení.** Aplikace odešle oznámení do zařízení. Ujistěte se, že oznámení je vpravo a pak vyberte **ověřte**. Pokud není povědomý oznámení, vyberte **Odepřít**.

- **Ověřovací kód.** Po zadání uživatelského jména a hesla, můžete otevřít aplikaci a zkopírujte ověřovací kód, který je k dispozici na **účty** obrazovky k obrazovce přihlášení. Ověřovací kód, který funguje jako druhou podobu ověřování.

## <a name="opt-in-for-two-step-verification"></a>Vyjádřit výslovný souhlas pro dvoustupňové ověřování.

Vaše organizace rozhodne, zda použít dvoustupňové ověření pomocí svého pracovního nebo školního účtu. Správce vám dá vědět, jaké metody ověřování je potřeba nastavit a použít. Další informace najdete v tématu [Azure Multi-Factor Authentication význam pro mě](multi-factor-authentication-end-user.md).

Pro svůj osobní účet Microsoft můžete nastavit dvoustupňové ověřování pro sebe. Podrobnější informace a pokyny najdete v článku [o dvoustupňovém ověřování](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Můžete také použít aplikaci Microsoft Authenticator s účty jiných společností než Microsoft. Tyto účty mohou volat funkci něco jiného než dvoustupňové ověřování, ale by měl být schopen najít v rámci zabezpečení nebo nastavení přihlášení. Další informace o tom, jak nastavit tyto účty jiných společností než Microsoft naleznete v tématu [Zákaznická podpora Microsoftu vás videa](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX).

## <a name="install-the-app"></a>Nainstalujte si aplikaci

Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071). Pokud chcete získat co nejlepších výsledků, byste pak měli nechat aplikací, dostávat oznámení, když budete vyzváni. 

## <a name="add-accounts-to-the-app"></a>Přidat účty do aplikace

Pracovní nebo školní účty nebo osobní účty můžete přidat do aplikace Microsoft Authenticator. 

### <a name="add-a-personal-microsoft-account"></a>Přidat osobního účtu Microsoft

Pro osobní účet Microsoft (ten, který používáte pro přihlášení k Outlook.com, Xbox, Skype, atd.) vše, co musíte udělat, je přihlásit ke svému účtu v aplikaci Microsoft Authenticator.

### <a name="add-a-work-or-school-account"></a>Přidat pracovní nebo školní účet

1. Pokud je to možné, přejděte [dalšího ověření zabezpečení](http://aka.ms/mfasetup) obrazovky na jiný počítač nebo zařízení. Informace o tom, jak na této obrazovce najdete v tématu [změnou nastavení zabezpečení](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) nebo se obraťte na svého správce.

    >[!Note]
    >Pokud správce zapnul prostředí zabezpečení informací ve verzi preview, můžete postupujte podle pokynů [nastavení bezpečnostní údaje pro použití ověřovací aplikaci](security-info-setup-auth-app.md) oddílu.

2. Zaškrtněte políčko vedle položky **aplikace Authenticator**a pak vyberte **konfigurovat**.

    ![Tlačítko Konfigurovat v dialogovém okně Nastavení ověření zabezpečení](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    **Konfigurace mobilní aplikace** obrazovky pomocí kódu QR můžete zkontrolovat pomocí ověřovací aplikace.

    ![Obrazovka, která obsahuje kód QR](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator. Na **účty** obrazovky, vyberte **přidat účet**a pak vyberte **pracovní nebo školní účet**.

4. Použít fotoaparát v zařízení kód QR naskenovat a potom vyberte **provádí** zavřete obrazovky kód QR.

    >[!Note]
    >Pokud vaše kamera nepracuje správně, můžete si [QR kód a adresu URL zadat ručně](#add-an-account-to-the-app-manually).

    **Účty** obrazovka aplikace ukazuje název účtu a ověřovací šestimístný kód. Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund vám zakázala používat stejný kód dvakrát.  

    ![obrazovka s účty](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ručně přidat účet do aplikace

1. Přejděte **dalšího ověření zabezpečení** obrazovky. Informace o tom, jak získat na této obrazovce najdete v tématu [změnou nastavení zabezpečení](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).

2. Zaškrtněte políčko vedle položky **aplikace Authenticator**a pak vyberte **konfigurovat**.

    **Konfigurace mobilní aplikace** obrazovky.

3. Zkopírujte kód a adresa URL informací z **konfigurace mobilní aplikace** obrazovky, takže můžete ručně zadat do skener QR.

4. Otevřete aplikaci Microsoft Authenticator. Na **účty** obrazovky, vyberte **přidat účet**a pak vyberte **pracovní nebo školní účet**.

5. Na obrazovce skener kódů QR, vyberte **zadat kód ručně**.

    ![Obrazovka pro skenování kódu QR](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. Zadejte kód a adresu URL z obrazovky pomocí kódu QR do **přidat účet** obrazovky a pak vyberte **Dokončit**.

    ![Obrazovka pro zadání kód a adresu URL](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    **Účty** obrazovka aplikace ukazuje název účtu a ověřovací šestimístný kód. Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund vám zakázala používat stejný kód dvakrát.

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Použití funkce rozpoznávání obličeje nebo otisků prstů vašeho zařízení

Vaše organizace může vyžadovat kód PIN k dokončení ověření vaší identity. Použití funkce rozpoznávání obličeje nebo otisků prstů vašeho zařízení místo kódu PIN můžete nastavit aplikaci Microsoft Authenticator. Tento parametr můžete nastavit až při prvním použití aplikace authenticator se ověřit svůj účet tak, že vyberete možnost použít vaše možnosti biometrických zařízení jako identifikace místo PIN kódu.

## <a name="use-the-app-when-you-sign-in"></a>Použití aplikace při přihlášení

Poté, co vaše účty přidáte do aplikace, můžete použít aplikaci pro přihlášení k účtům.

Pokud jste se rozhodli použít ověřovací kódy v aplikaci, budete k jejich zobrazení na spuštění **účty** stránky. Kódy změnit každých 30 sekund, takže máte vždycky nový kód, v případě potřeby. Ale není nutné provádět s nimi až po přihlášení a výzva k zadání ověřovacího kódu.

## <a name="next-steps"></a>Další postup

- Pokud máte další obecné dotazy o aplikaci, přečtěte si téma [nejčastější dotazy k aplikaci Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- Pokud chcete získat další informace o dvoustupňovém ověřování, přečtěte si téma [nastavit účtu pro dvoustupňové ověřování.](multi-factor-authentication-end-user-first-time.md)

- Pokud chcete získat další informace o bezpečnostních údajů, přečtěte si téma [spravovat svoje bezpečnostní údaje](security-info-manage-settings.md)
