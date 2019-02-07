---
title: Osobní účty Microsoft přidat do aplikace Microsoft Authenticator – Azure Active Directory | Dokumentace Microsoftu
description: Postup přidání osobním účtům Microsoft, jako Outlook.com nebo Xbox LIVE do aplikace Microsoft Authenticator pro dvoufaktorové ověřování.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 9290026c1becc1ae32682fa173aae57dd7c47565
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773276"
---
# <a name="add-your-personal-microsoft-accounts"></a>Přidejte osobní účty Microsoft
Přidáte osobním účtům Microsoft, například Outlook.com a Xbox LIVE do aplikace Microsoft Authenticator pro proces standardní dvojúrovňového ověřování a metoda pro přihlašování k passwordless phone.

- **Metoda standardní dvojúrovňového ověřování.** Zadejte uživatelské jméno a heslo do zařízení, které jste přihlášení k a pak zvolte, jestli odešle oznámení, aplikace Microsoft Authenticator nebo pokud chcete zkopírovat přidružené ověřovací kód z **účty** obrazovce aplikace Microsoft Authenticator.

- **Metoda passwordless přihlašování.** Zadejte svoje uživatelské jméno do zařízení budete přihlášení pro váš osobní účet Microsoft a pak ověřte, že je můžete pomocí otisku prstu, rozpoznávání tváře nebo PIN kód pomocí vašeho mobilního zařízení. Pro tuto metodu není nutné zadat heslo.

>[!Important]
>Než budete moct přidat svůj účet, musíte stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste tak ještě neučinili, postupujte podle kroků v [stáhnout a nainstalovat aplikaci](user-help-auth-app-download-install.md) článku.

## <a name="add-your-personal-microsoft-account"></a>Přidejte svůj osobní účet Microsoft
Váš osobní účet Microsoft můžete přidat podle prvního zapnutí dvojúrovňového ověřování a tak, že přidáte účet do aplikace.

>[!Note]
>Pokud plánujete používat pouze passwordless přihlašování telefonem pro osobní účet Microsoft, nemusíte zapnutí dvojúrovňového ověřování. Pro vyšší zabezpečení, ale doporučujeme zapnout dvoufaktorové ověřování.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvojúrovňového ověřování

1. Na vašem počítači, přejděte na vaše [Základy zabezpečení](https://account.microsoft.com/security) stránky a přihlaste se pomocí osobního účtu Microsoft. Například, alain@outlook.com.

2. V dolní části **Základy zabezpečení** zvolte **další možnosti zabezpečení** odkaz.

    ![Stránka Základy zabezpečení se zvýrazněným odkazem "Další možnosti zabezpečení"](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Přejděte **dvoustupňové ověřování** části a zvolte funkci **na**. Můžete také vypnout ho tady Pokud už nechcete používat s osobním účtem.

### <a name="add-your-microsoft-account-to-the-app"></a>Přidejte do aplikace účtu Microsoft

1. Otevřete aplikaci Microsoft Authenticator na vašem mobilním zařízení.

2. Vyberte **přidat účet** z **přizpůsobení a řízení** ikonu v pravém horním rohu.

    ![Stránka účty s zvýrazněnou ikonou přizpůsobení a řízení](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. V **přidat účet** zvolte **osobní účet**.

4. Přihlaste se ke svému osobnímu účtu pomocí odpovídající e-mailovou adresu (například alain@outlook.com) a klikněte na tlačítko **Další**.

    >[!Note]
    >Pokud nemáte osobního účtu Microsoft, můžete si ho vytvořit tady.

5. Zadejte své heslo a klikněte na tlačítko **přihlášení**.

    Váš osobní účet se přidá do aplikace Microsoft Authenticator.

## <a name="next-steps"></a>Další postup

- Poté, co vaše účty přidáte do aplikace, můžete se přihlásit pomocí aplikace Authenticator na vašem zařízení. Další informace najdete v tématu [Přihlaste se pomocí aplikace](user-help-auth-app-sign-in.md).

- Pro zařízení s Iosem můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako například pořadí z vašich účtů s cloudem. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
