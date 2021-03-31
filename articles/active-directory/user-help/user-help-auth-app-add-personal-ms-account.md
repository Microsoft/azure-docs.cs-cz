---
title: Přidání osobního účet Microsoft do aplikace Microsoft Authenticator – Azure AD
description: Přidejte osobní účty Microsoft, například pro Outlook.com nebo Xbox LIVE do aplikace Microsoft Authenticator k ověření vaší identity při použití dvojúrovňového ověřování.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83741631"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Přidání osobních účtů Microsoft do aplikace Microsoft Authenticator

Přidejte své osobní účty Microsoft, například pro účty Outlook.com a Xbox LIVE, do aplikace Microsoft Authenticator pro standardní proces ověřování dvou procesorů a pro přihlášení bez hesla pro telefonování.

- **Standardní metoda ověřování dvou faktorů.** Zadejte své uživatelské jméno a heslo do zařízení, ke kterému se přihlašujete, a pak zvolte, jestli Microsoft Authenticator Aplikace pošle oznámení, nebo pokud byste chtěli zkopírovat přidružený ověřovací kód z obrazovky **účtů** aplikace Microsoft Authenticator.

- **Metoda přihlašování v neheslech** Zadejte své uživatelské jméno do zařízení, ke kterému se přihlašujete, pomocí osobního účet Microsoft a pak použijte své mobilní zařízení, abyste ověřili, že používáte otisk prstu, obličeje nebo PIN. Pro tuto metodu není nutné zadávat heslo.

>[!Important]
>Než budete moct přidat svůj účet, musíte si stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle kroků v článku [Stažení a instalace aplikace](user-help-auth-app-download-install.md) .

Vlastní účet Microsoft můžete přidat tak, že nejprve zapnete dvojúrovňové ověřování a potom přidáte účet do aplikace. Nemusíte zapnout dvojúrovňové ověřování, abyste pro svůj účet mohli používat jenom přihlášení pro telefon bez hesla, ale důrazně doporučujeme, abyste pro další zabezpečení účtů zapnuli dvojúrovňové ověřování.

## <a name="turn-on-two-factor-verification"></a>Zapnout dvojúrovňové ověřování

1. Na svém počítači navštivte stránku [Základy zabezpečení](https://account.microsoft.com/security) a přihlaste se pomocí svých osobních účet Microsoft. Například, alain@outlook.com.

2. V dolní části stránky **Základy zabezpečení** klikněte na odkaz **Další možnosti zabezpečení** .

    ![Stránka základy zabezpečení s zvýrazněným odkazem další možnosti zabezpečení](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **V** části **dvoustupňové ověřování** klikněte na možnost pro zapnutí funkce. Můžete ho také vypnout tady, pokud ho už nechcete používat s vaším osobním účtem.

## <a name="add-your-microsoft-account-to-the-app"></a>Přidání účet Microsoft do aplikace

1. Na svém mobilním zařízení otevřete aplikaci Microsoft Authenticator.

1. V Androidu v pravém horním rohu vyberte **Přidat účet** v ikoně **přizpůsobení a řízení** .

    ![Stránky pro výběr účtu Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    V systému iOS vyberte v pravém horním rohu ikonu se symbolem plus.

    ![verze iOS prostředí pro výběr účtu](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na stránce **Přidat účet** vyberte možnost **osobní účet**.

1. Vyberte **Přihlásit se účtem Microsoft** pro přidání svého účtu. Kód QR se dá použít, když je dostupný, ale kdykoli můžete přidat svůj účet tím, že se přihlásíte pomocí uživatelského jména a hesla.

    ![Vyberte buď účet Microsoft, nebo naskenujte kód QR, pokud je k dispozici.](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Přihlaste se k osobnímu účtu pomocí příslušné e-mailové adresy (například alain@outlook.com ) a pak vyberte **Další**.

    >[!Note]
    >Pokud nemáte osobní účet Microsoft, [můžete si ho vytvořit](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Zadejte heslo a pak vyberte **Přihlásit** se. Váš osobní účet se přidá do aplikace Microsoft Authenticator.

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí ověřovací aplikace na svém zařízení. Další informace najdete v tématu [přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- Pokud máte potíže s získáním ověřovacího kódu pro osobní účet Microsoft, přečtěte si část **problémy s ověřovacím kódem pro řešení potíží** v článku [účet Microsoft informace o zabezpečení & ověřovací kódy](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako je například pořadí účtů, do cloudu. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
