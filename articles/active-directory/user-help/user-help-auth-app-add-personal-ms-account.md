---
title: Přidání osobního účtu Microsoft do aplikace Microsoft Authenticator – Azure AD
description: Přidejte do aplikace Microsoft Authenticator osobní účty Microsoft, například pro Outlook.com nebo Xbox LIVE, abyste ověřili svou identitu při použití dvoufaktorového ověřování.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984733"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Přidání osobních účtů Microsoft do aplikace Microsoft Authenticator

Přidejte své osobní účty Microsoft, například pro Outlook.com a Xbox LIVE, do aplikace Microsoft Authenticator pro standardní dvoufaktorový proces ověřování i přihlášení telefonu bez hesla.

- **Standardní metoda dvoufaktorového ověřování.** Do zařízení, ke které se přihlašujete, zadejte své uživatelské jméno a heslo a pak zvolte, jestli aplikace Microsoft Authenticator odešle oznámení nebo jestli chcete zkopírovat přidružený ověřovací kód z obrazovky **Účty** aplikace Microsoft Authenticator.

- **Metoda přihlášení bez hesla.** Zadejte své uživatelské jméno do zařízení, ke kterému se přihlašujete, abyste používali svůj osobní účet Microsoft, a pak pomocí mobilního zařízení ověřte, že jste to vy, kteří používáte otisk prstu, obličej nebo PIN kód. Pro tuto metodu není nutné zadávat heslo.

>[!Important]
>Před přidáním účtu je nutné stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle pokynů v článku [Ke stažení a instalaci aplikace.](user-help-auth-app-download-install.md)

Svůj osobní účet Microsoft můžete přidat tak, že nejprve zapnete dvoufaktorové ověření a pak účet přidáte do aplikace. Nemusíte zapínat dvoufaktorové ověřování, abyste pro svůj účet používali pouze přihlašování bez hesla, ale důrazně doporučujeme zapnout dvoufaktorové ověření pro další zabezpečení účtu.

## <a name="turn-on-two-factor-verification"></a>Zapnutí dvoufaktorového ověření

1. Na počítači přejděte na stránku [Základy zabezpečení](https://account.microsoft.com/security) a přihlaste se pomocí svého osobního účtu Microsoft. Například, alain@outlook.com.

2. V dolní části stránky **Základy zabezpečení** zvolte odkaz **další možnosti zabezpečení.**

    ![Stránka Základy zabezpečení se zvýrazněným odkazem "Další možnosti zabezpečení"](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Přejděte do části **Dvoustupňové ověření** a zvolte zapnutí **funkce**. Můžete jej také vypnout zde, pokud již nechcete používat s osobním účtem.

## <a name="add-your-microsoft-account-to-the-app"></a>Přidání účtu Microsoft do aplikace

1. Otevřete aplikaci Microsoft Authenticator na svém mobilním zařízení.

1. V Systému Android vyberte **Přidat účet** z ikony Přizpůsobit **a řídit** v pravém horním seřízení.

    ![Stránky pro výběr účtu Android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    V iOS vyberte ikonu plus v pravém horním.

    ![iOS verze prostředí pro výběr účtu](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na stránce **Přidat účet** zvolte **Osobní účet**.

1. Vyberte **Přihlásit se pomocí Microsoftu** a přidejte svůj účet. QR kód lze použít, pokud je k dispozici, ale vždy můžete přidat svůj účet po přihlášení pomocí uživatelského jména a hesla.

    ![Vyberte účet Microsoft nebo naskenujte QR kód, pokud je k dispozici](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Přihlaste se ke svému osobnímu účtu alain@outlook.compomocí příslušné e-mailové adresy (například) a pak vyberte **Další**.

    >[!Note]
    >Pokud nemáte osobní účet Microsoft, [můžete si ho vytvořit](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Zadejte heslo a pak **vyberte Přihlásit se**. Váš osobní účet se přidá do aplikace Microsoft Authenticator.

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí aplikace Authenticator v zařízení. Další informace najdete [v tématu Přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- Pokud máte potíže se získáním ověřovacího kódu pro svůj osobní účet Microsoft, přečtěte si článek **Problémy s ověřovacím kódem řešení potíží** v článku [bezpečnostní údaje účtu Microsoft & ověřovací kódy.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje k účtu a související nastavení aplikací, jako je pořadí vašich účtů, do cloudu. Další informace naleznete v [tématu Zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
