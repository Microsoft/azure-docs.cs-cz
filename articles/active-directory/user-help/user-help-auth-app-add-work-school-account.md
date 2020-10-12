---
title: Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator – Azure AD
description: Přidejte svůj pracovní nebo školní účet do aplikace Microsoft Authenticator, abyste ověřili svoji identitu při použití dvojúrovňového ověřování.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e003c45aa1e7d75b709b7fbf99532fb1302fcbb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797645"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator

Pokud vaše organizace používá dvojúrovňové ověřování, můžete nastavit pracovní nebo školní účet tak, aby používal aplikaci Microsoft Authenticator jako jednu z metod ověřování.

>[!Important]
>Než budete moct přidat svůj účet, musíte si stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle kroků v článku [Stažení a instalace aplikace](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Přidat svůj pracovní nebo školní účet

1. V počítači přejdete na stránku [Další ověření zabezpečení](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) .

    >[!Note]
    >Pokud nevidíte stránku **Další ověření zabezpečení** , je možné, že správce zapnul prostředí informace o zabezpečení (Preview). V takovém případě byste měli postupovat podle pokynů v části [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md) . Pokud to tak není, budete muset požádat o pomoc oddělení technické podpory vaší organizace. Další informace o bezpečnostních informacích najdete v tématu [Přehled informací o zabezpečení (Preview)](./security-info-setup-signin.md).

2. Zaškrtněte políčko u **ověřovací aplikace**a pak vyberte **Konfigurovat**.

    Zobrazí se stránka **Konfigurace mobilní aplikace** .

    ![Obrazovka, která poskytuje kód QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** **a potom** vyberte **pracovní nebo školní účet**.

    >[!Note]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu [Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

4. Pomocí kamery zařízení Naskenujte kód QR z obrazovky **Konfigurace mobilní aplikace** na vašem počítači a pak zvolte **Hotovo**.

    >[!Note]
    >Pokud fotoaparát nedokáže zachytit kód QR, můžete ručně přidat informace o svém účtu do aplikace Microsoft Authenticator pro dvoustupňové ověřování. Další informace a jak to udělat, najdete v tématu [Ruční přidání účtu](user-help-auth-app-add-account-manual.md).

5. Projděte si na zařízení obrazovku **účty** , abyste se ujistili, že je váš účet pravý a že je k němu přidružený kód ověřovacího kódu o šesti číslicích. Pro zvýšení zabezpečení se ověřovací kód každých 30 sekund nebrání někomu, aby používal kód několikrát.

    ![Obrazovka účty](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí ověřovací aplikace na svém zařízení. Další informace najdete v tématu [přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako je například pořadí účtů, do cloudu. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).