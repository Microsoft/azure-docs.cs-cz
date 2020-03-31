---
title: Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator – Azure AD
description: Přidejte svůj pracovní nebo školní účet do aplikace Microsoft Authenticator a ověřte svou identitu při použití dvoufaktorového ověření.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063913"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator

Pokud vaše organizace používá dvoufaktorové ověření, můžete si nastavit pracovní nebo školní účet tak, aby jako jedna z metod ověřování používal aplikaci Microsoft Authenticator.

>[!Important]
>Před přidáním účtu je nutné stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle pokynů v článku [Ke stažení a instalaci aplikace.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Přidání pracovního nebo školního účtu

1. Na počítači přejděte na stránku [Další ověření zabezpečení.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

    >[!Note]
    >Pokud stránku Další **ověření zabezpečení** nevidíte, je možné, že správce zapnul bezpečnostní údaje (náhled). V takovém případě byste měli postupovat podle pokynů v části [Nastavení bezpečnostních údajů a použít aplikaci pro ověřování.](security-info-setup-auth-app.md) Pokud tomu tak není, budete muset kontaktovat oddělení technické podpory vaší organizace. Další informace o bezpečnostních údajích naleznete v [tématu Přehled bezpečnostních údajů (preview).](user-help-security-info-overview.md)

2. Zaškrtněte políčko vedle **aplikace Authenticator**a pak vyberte **Konfigurovat**.

    Zobrazí se stránka **Konfigurovat mobilní aplikaci.**

    ![Obrazovka, která poskytuje QR kód](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, **vyberte Přidat účet** z ikony **Přizpůsobit a řídit** v pravém horním bodě a pak vyberte Pracovní nebo **školní účet**.

    >[!Note]
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

4. Pomocí fotoaparátu zařízení naskenujte QR kód z obrazovky **Konfigurace mobilní aplikace** v počítači a pak zvolte **Hotovo**.

    >[!Note]
    >Pokud fotoaparát nedokáže zachytit QR kód, můžete ručně přidat informace o účtu do aplikace Microsoft Authenticator pro dvoufaktorové ověření. Další informace a postup naleznete v [tématu Ruční přidání účtu](user-help-auth-app-add-account-manual.md).

5. Zkontrolujte obrazovku **Účty** aplikace v zařízení, abyste se ujistili, že je váš účet správný a že existuje přidružený šestimístný ověřovací kód. Z důvodu dodatečného zabezpečení se ověřovací kód mění každých 30 sekund, aby někdo mohl kód používat vícekrát.

    ![Obrazovka Účty](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí aplikace Authenticator v zařízení. Další informace najdete [v tématu Přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje k účtu a související nastavení aplikací, jako je pořadí vašich účtů, do cloudu. Další informace naleznete v [tématu Zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
