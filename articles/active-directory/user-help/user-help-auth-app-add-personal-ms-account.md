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
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 289c00e28fd82b78b321732628ff85d22c64cabf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062298"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Přidání osobních účtů Microsoft do aplikace Microsoft Authenticator

Přidejte své osobní účty Microsoft, například pro Outlook.com a Xbox LIVE, do aplikace Microsoft Authenticator pro standardní dvoufaktorový proces ověřování i metodu přihlášení k telefonu bez hesla.

- **Standardní metoda dvoufaktorového ověřování.** Do zařízení, ke které se přihlašujete, zadejte své uživatelské jméno a heslo a pak zvolte, jestli aplikace Microsoft Authenticator odešle oznámení nebo jestli chcete zkopírovat přidružený ověřovací kód z obrazovky **Účty** aplikace Microsoft Authenticator.

- **Metoda přihlášení bez hesla.** Do zařízení, ke kterému se přihlašujete pro svůj osobní účet Microsoft, zadejte své uživatelské jméno a potom pomocí mobilního zařízení ověřte, zda jste to vy, pomocí otisku prstu, obličeje nebo KÓDU PIN. Pro tuto metodu není nutné zadávat heslo.

>[!Important]
>Před přidáním účtu je nutné stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle pokynů v článku [Ke stažení a instalaci aplikace.](user-help-auth-app-download-install.md)

## <a name="add-your-personal-microsoft-account"></a>Přidání osobního účtu Microsoft

Svůj osobní účet Microsoft můžete přidat tak, že nejprve zapnete dvoufaktorové ověření a pak účet přidáte do aplikace.

>[!Note]
>Pokud máte v plánu používat pro svůj osobní účet Microsoft telefonní přihlášení bez hesla, nemusíte zapínat dvoufaktorové ověřování. Pro další zabezpečení účtu však doporučujeme zapnout dvoufaktorové ověření.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvoufaktorového ověření

1. Na počítači přejděte na stránku [Základy zabezpečení](https://account.microsoft.com/security) a přihlaste se pomocí svého osobního účtu Microsoft. Například, alain@outlook.com.

2. V dolní části stránky **Základy zabezpečení** zvolte odkaz **další možnosti zabezpečení.**

    ![Stránka Základy zabezpečení se zvýrazněným odkazem "Další možnosti zabezpečení"](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Přejděte do části **Dvoustupňové ověření** a zvolte zapnutí **funkce**. Můžete jej také vypnout zde, pokud již nechcete používat s osobním účtem.

### <a name="add-your-microsoft-account-to-the-app"></a>Přidání účtu Microsoft do aplikace

1. Otevřete aplikaci Microsoft Authenticator na svém mobilním zařízení.

2. V pravém horním mase vyberte **Přidat účet** z ikony Přizpůsobit **a řídit.**

    ![Stránka Účty se zvýrazněnou ikonou Přizpůsobit a řídit](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Na stránce **Přidat účet** zvolte **Osobní účet**.

4. Přihlaste se ke svému osobnímu účtu alain@outlook.compomocí příslušné e-mailové adresy (například) a pak zvolte **Další**.

    >[!Note]
    >Pokud nemáte osobní účet Microsoft, můžete si ho vytvořit zde.

5. Zadejte heslo a pak zvolte **Přihlásit se**.

    Váš osobní účet se přidá do aplikace Microsoft Authenticator.

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí aplikace Authenticator v zařízení. Další informace najdete [v tématu Přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- Pokud máte potíže se získáním ověřovacího kódu pro svůj osobní účet Microsoft, přečtěte si článek **Problémy s ověřovacím kódem řešení potíží** v článku [bezpečnostní údaje účtu Microsoft & ověřovací kódy.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje k účtu a související nastavení aplikací, jako je pořadí vašich účtů, do cloudu. Další informace naleznete v [tématu Zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
