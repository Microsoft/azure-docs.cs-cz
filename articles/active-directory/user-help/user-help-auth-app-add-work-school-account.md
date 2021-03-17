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
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359100"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Přidání pracovního nebo školního účtu do aplikace Microsoft Authenticator

Pokud vaše organizace používá dvojúrovňové ověřování, můžete nastavit pracovní nebo školní účet tak, aby používal aplikaci Microsoft Authenticator jako jednu z metod ověřování.

>[!Important]
>Než budete moct přidat svůj účet, musíte si stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle kroků v článku [Stažení a instalace aplikace](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Přidat svůj pracovní nebo školní účet

Svůj pracovní nebo školní účet můžete do aplikace Microsoft Authenticator přidat pomocí jedné z následujících akcí:

- Přihlašovací údaje Přihlaste se přes svůj pracovní nebo školní účet. (Preview)
- Naskenování kódu QR

### <a name="sign-in-with-your-credentials"></a>Přihlaste se pomocí svých přihlašovacích údajů.

>[!Note]
>Tato funkce je použitelná jenom pro uživatele, u kterých správci povolili přihlašování telefonem pomocí ověřovací aplikace.

Pokud chcete přidat účet, přihlaste se k pracovnímu nebo školnímu účtu pomocí svých přihlašovacích údajů:

1. Otevřete aplikaci Microsoft Authenticator a vyberte **+** tlačítko a klepněte na **Přidat pracovní nebo školní účet**. Vyberte **Sign in** (Přihlásit se).

1. Zadejte svoje přihlašovací údaje pracovního nebo školního účtu. Pokud máte dočasný přístup (klepnutím), můžete ho použít k přihlášení. V tuto chvíli můžete potenciálně zablokovat, aby bylo možné pokračovat jedním z následujících podmínek:

   - Pokud nemáte ve svém účtu dostatek metod ověřování pro získání tokenu silného ověřování, nebudete moct přejít k možnosti Přidat účet.

   - Pokud se vám zobrazí zpráva `You might be signing in from a location that is restricted by your admin` , budete zablokován a potřebujete správce k odblokování v [informacích o zabezpečení](https://mysignins.microsoft.com/security-info).

   - Pokud nejste zablokovali přihlášení telefonem pomocí aplikace pro ověřování pomocí ověřovacího programu pro správce, budete moci projít registraci zařízení, abyste mohli nastavit přihlášení k telefonu bez hesla a Azure Multi-Factor Authentication (MFA).

1. V tomto okamžiku můžete být požádáni o skenování kódu QR poskytnutého vaší organizací, abyste v aplikaci nastavili místní účet služby Multi-Factor Authentication. To je potřeba udělat jenom v případě, že vaše organizace používá místní MFA Server.

1. Na zařízení klepněte na účet a ověřte si ho v zobrazení na celé obrazovce, na kterém je váš účet správný a že je k dispozici kód ověřovacího kódu se šesti číslicemi. Pro zvýšení zabezpečení se ověřovací kód každých 30 sekund nebrání někomu, aby používal kód několikrát.

## <a name="sign-in-with-a-qr-code"></a>Přihlášení pomocí kódu QR

Chcete-li přidat účet skenováním kódu QR, postupujte následovně:

1. V počítači přejdete na stránku **Další ověření zabezpečení** .

   >[!Note]
   >Pokud nevidíte stránku **Další ověření zabezpečení** , je možné, že správce zapnul prostředí informace o zabezpečení (Preview). V takovém případě byste měli postupovat podle pokynů v části [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md) . Pokud to tak není, budete muset požádat o pomoc oddělení technické podpory vaší organizace. Další informace o zabezpečení najdete v tématu [Nastavení bezpečnostních údajů z výzvy k přihlášení](security-info-setup-signin.md).

1. Zaškrtněte políčko vedle ověřovací aplikace a pak vyberte **Konfigurovat**. Zobrazí se stránka **Konfigurace mobilní aplikace** .

   ![Obrazovka, která poskytuje kód QR](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Otevřete aplikaci Microsoft Authenticator, vyberte ikonu Plus, ![ Vyberte ikonu Plus na zařízení s iOS nebo Androidem ](media/user-help-auth-app-add-work-school-account/plus-icon.png) a vyberte **Přidat účet**, vyberte **pracovní nebo školní účet** a potom **Naskenujte kód QR**.
   Pokud nemáte nastavený účet v ověřovací aplikaci, zobrazí se velké modré tlačítko, které říká **Přidat účet**.

Pokud se vám nezobrazí výzva k použití kamery k naskenování kódu QR v nastavení vašeho telefonu, ujistěte se, že aplikace ověřovatele má přístup k telefonní kameře.

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí ověřovací aplikace na svém zařízení. Další informace najdete v tématu [přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako je například pořadí účtů, do cloudu. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
