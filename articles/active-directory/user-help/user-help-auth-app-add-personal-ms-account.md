---
title: Přidání osobního účet Microsoft do aplikace Microsoft Authenticator – Azure AD
description: Přidejte osobní účty Microsoft, například pro Outlook.com nebo Xbox LIVE do aplikace Microsoft Authenticator k ověření vaší identity při použití dvojúrovňového ověřování.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062298"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Přidání osobních účtů Microsoft do aplikace Microsoft Authenticator

Přidejte své osobní účty Microsoft, například pro Outlook.com a Xbox LIVE do aplikace Microsoft Authenticator pro standardní proces ověřování dvou faktorů a pro metodu přihlašování k telefonickému přihlášení.

- **Standardní metoda ověřování dvou faktorů.** Do zařízení, ke kterému se přihlašujete, zadejte svoje uživatelské jméno a heslo a pak zvolte, jestli Microsoft Authenticator Aplikace pošle oznámení, nebo pokud byste chtěli zkopírovat přidružený ověřovací kód z obrazovky **účtů** aplikace Microsoft Authenticator.

- **Metoda přihlašování v neheslech** Zadejte své uživatelské jméno do zařízení, ke kterému se přihlašujete pro osobní účet Microsoft a pak použijte své mobilní zařízení k ověření, že jste použili otisk prstu, obličeje nebo PIN. Pro tuto metodu není nutné zadávat heslo.

>[!Important]
>Než budete moct přidat svůj účet, musíte si stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle kroků v článku [Stažení a instalace aplikace](user-help-auth-app-download-install.md) .

## <a name="add-your-personal-microsoft-account"></a>Přidat osobní účet Microsoft

Vlastní účet Microsoft můžete přidat tak, že nejprve zapnete dvojúrovňové ověřování a potom přidáte účet do aplikace.

>[!Note]
>Pokud máte v úmyslu používat pro osobní účet Microsoft jenom přihlášení pro telefon bez hesla, nemusíte zapnout dvojúrovňové ověřování. Pro zvýšení zabezpečení účtu ale doporučujeme zapnout dvojúrovňové ověřování.

### <a name="turn-on-two-factor-verification"></a>Zapnout dvojúrovňové ověřování

1. Na svém počítači navštivte stránku [Základy zabezpečení](https://account.microsoft.com/security) a přihlaste se pomocí svých osobních účet Microsoft. například alain@outlook.com.

2. V dolní části stránky **Základy zabezpečení** klikněte na odkaz **Další možnosti zabezpečení** .

    ![Stránka základy zabezpečení s zvýrazněným odkazem další možnosti zabezpečení](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **V**části **dvoustupňové ověřování** klikněte na možnost pro zapnutí funkce. Můžete ho také vypnout tady, pokud ho už nechcete používat s vaším osobním účtem.

### <a name="add-your-microsoft-account-to-the-app"></a>Přidání účet Microsoft do aplikace

1. Na svém mobilním zařízení otevřete aplikaci Microsoft Authenticator.

2. V pravém horním rohu vyberte **Přidat účet** v ikoně **vlastní nastavení a ovládací prvek** .

    ![Stránka účty s zvýrazněnou ikonou pro přizpůsobení a řízení](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Na stránce **Přidat účet** vyberte možnost **osobní účet**.

4. Přihlaste se k osobnímu účtu pomocí příslušné e-mailové adresy (například alain@outlook.com) a pak zvolte **Další**.

    >[!Note]
    >Pokud nemáte osobní účet Microsoft, můžete si ho vytvořit tady.

5. Zadejte heslo a pak zvolte **Přihlásit**se.

    Váš osobní účet se přidá do aplikace Microsoft Authenticator.

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí ověřovací aplikace na svém zařízení. Další informace najdete v tématu [přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- Pokud máte potíže s získáním ověřovacího kódu pro osobní účet Microsoft, přečtěte si část **problémy s ověřovacím kódem pro řešení potíží** v článku [účet Microsoft informace o zabezpečení & ověřovací kódy](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako je například pořadí účtů, do cloudu. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
