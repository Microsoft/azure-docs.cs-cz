---
title: Přidat pracovní nebo školní účet do aplikace Microsoft Authenticator – Azure Active Directory | Dokumentace Microsoftu
description: Jak přidat svůj pracovní nebo školní účet do aplikace Microsoft Authenticator pro dvoufaktorové ověřování.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: f60e115039de3843a6851e0073e853d0581dda32
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904795"
---
# <a name="add-your-work-or-school-account"></a>Přidejte svůj pracovní nebo školní účet
Pokud vaše organizace používá dvojúrovňové ověřování, můžete nastavit svůj pracovní nebo školní účet k používání aplikace Microsoft Authenticator jako jednu z metod ověřování.

>[!Important]
>Než budete moct přidat svůj účet, musíte stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste tak ještě neučinili, postupujte podle kroků v [stáhnout a nainstalovat aplikaci](microsoft-authenticator-app-how-to.md) článku.

## <a name="add-your-work-or-school-account"></a>Přidejte svůj pracovní nebo školní účet

1. Ve vašem počítači, přejděte [dalšího ověření zabezpečení](https://aka.ms/mfasetup) stránky.

    >[!Note]
    >Pokud se nezobrazí **dalšího ověření zabezpečení** stránky, je možné, že správce má zapnuté informace (preview) prostředí zabezpečení. Pokud je to tento případ, postupujte podle pokynů v [nastavení bezpečnostní údaje pro použití ověřovací aplikaci](security-info-setup-auth-app.md) oddílu. Pokud to není tento případ, musíte požádat o pomoc HelpDesk vaší organizace. Další informace o bezpečnostní údaje, najdete v části [spravovat svoje bezpečnostní údaje](security-info-manage-settings.md).

2. Zaškrtněte políčko vedle položky **aplikace Authenticator**a pak vyberte **konfigurovat**.

    **Konfigurace mobilní aplikace** se zobrazí stránka.
    
    ![Obrazovka, která obsahuje kód QR](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, vyberte **přidat účet** z **přizpůsobení a řízení** ikonu v pravém horním rohu a pak vyberte **pracovní nebo školní účet**.

4. Naskenování kódu QR pomocí fotoaparátu v zařízení **konfigurace mobilní aplikace** obrazovky ve vašem počítači a klikněte na tlačítko **provádí**.

    >[!Note]
    >Pokud fotoaparátu nemůže zachytit kód QR, můžete ručně přidat informace o vašem účtu do aplikace Microsoft Authenticator pro dvoufaktorové ověřování. Další informace a jak na to, najdete v části [ručně přidat svůj účet](microsoft-authenticator-app-add-account-manual.md).

5. Zkontrolujte **účty** obrazovce v aplikaci v zařízení, a ujistěte se, že váš účet je práva a je k přidružené ověřovací šestimístný kód. Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund brání pomocí kódu více než jednou.

    ![obrazovka s účty](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

## <a name="next-steps"></a>Další postup

- Poté, co vaše účty přidáte do aplikace, můžete se přihlásit pomocí aplikace Authenticator na vašem zařízení. Další informace najdete v tématu [Přihlaste se pomocí aplikace](microsoft-authenticator-app-phone-signin-faq.md).

- Pro zařízení s Iosem můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako například pořadí z vašich účtů s cloudem. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](microsoft-authenticator-app-backup-and-recovery.md).