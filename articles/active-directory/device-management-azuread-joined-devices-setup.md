---
title: Zařízení připojená k nastavení Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nastavit Azure Active Directory připojené zařízení.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 791181f11af38e4b78743ddb372207bd21893a6b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260925"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Zařízení připojená k nastavení Azure Active Directory

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují k prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další informace najdete v tématu [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md).

Pokud chcete, aby pracovní vlastněná zařízení s Windows 10 řídí služby Azure AD, můžete k tomu lze konfigurace Azure AD, které jsou připojené k zařízení. Toto téma vám poskytne související kroky. 


## <a name="prerequisites"></a>Požadavky

K připojení k zařízením s Windows 10, je třeba službu device registration service vám umožní zaregistrovat zařízení. Kromě nutnosti oprávnění k připojení zařízení v klientovi služby Azure AD, musí mít méně zařízení registrovaná než nakonfigurované maximum. Další informace najdete v tématu [nakonfigurovat nastavení zařízení](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Důležité informace


- Windows připojí zařízení v adresáři organizace ve službě Azure AD.

- Bude pravděpodobně nutné projít výzvu vícefaktorového ověřování. Tento problém je možné konfigurovat váš správce IT.

- Azure AD ověří, zda zařízení vyžaduje registraci správy mobilních zařízení a zaregistruje ho, pokud je k dispozici.

- Pokud jste spravované uživatel, Windows přejdete na ploše prostřednictvím automatického přihlášení.

- Pokud se Federovaný uživatel, budete muset přihlásit pomocí svých přihlašovacích údajů.

- Pokud jste se federovaný, musí podporovat zprostředkovatele Identity WS-Fed a WS-Trust koncový bod uživatelského jména a hesla. To může být verze 1.3 nebo 2005. Tato podpora protokolu je potřeba připojit zařízení k Azure AD i Přihlaste se k zařízení s heslem. 




## <a name="joining-a-device"></a>Připojení zařízení

Tato část vám poskytne kroky pro připojení k zařízení s Windows 10 do služby Azure AD. Úspěšně připojil zařízení se zobrazí jako **připojené k \<služby Azure AD\>**.

![Připojeno](./media/device-management-azuread-joined-devices-setup/13.png)


**Připojení zařízení Windows 10:**

1. V **spustit** nabídky, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/device-management-azuread-joined-devices-setup/01.png)

2. Klikněte na tlačítko **účty**.

    ![Účty](./media/device-management-azuread-joined-devices-setup/02.png)


3. Klikněte na tlačítko **přístup k práci nebo ve škole**.

    ![Přístup k práci nebo ve škole](./media/device-management-azuread-joined-devices-setup/03.png)

4. Na **přístup k práci nebo ve škole** dialogové okno, klikněte na tlačítko **Connect**.

    ![Připojení](./media/device-management-azuread-joined-devices-setup/04.png)


5. Na **nastavit pracovní nebo školní účet** dialogové okno, klikněte na tlačítko **připojit toto zařízení k Azure Active Directory**.

    ![Připojení](./media/device-management-azuread-joined-devices-setup/08.png)


6. Na **Pojďme vás přihlásit** dialogové okno, zadejte název účtu (například someone@example.com) a potom klikněte na **Další**.

    ![Pojďme vás přihlásit](./media/device-management-azuread-joined-devices-setup/10.png)


6. Na **zadejte heslo** dialogové okno, zadejte své heslo a pak klikněte na tlačítko **přihlášení**.

    ![Zadat heslo](./media/device-management-azuread-joined-devices-setup/05.png)


7. Na **zkontrolujte, že toto je vaše organizace** dialogové okno, klikněte na tlačítko **připojení**.

    ![Ujistěte se, že toto je vaše organizace](./media/device-management-azuread-joined-devices-setup/11.png)


8. Na **máte všechno nastavené** dialogové okno, klikněte na tlačítko **provádí**.

    ![Je vše připraveno](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Ověření

Pokud chcete ověřit, jestli se zařízení připojí ke službě Azure AD, můžete zkontrolovat **přístup k práci nebo ve škole** dialogové okno na vašem zařízení.

![Připojeno](./media/device-management-azuread-joined-devices-setup/13.png)

Alternativně můžete spustit následující příkaz: `dsregcmd /status`  
Do zařízení úspěšně připojil **AzureAdJoined** je **Ano**.

![Připojeno](./media/device-management-azuread-joined-devices-setup/14.png)

Můžete také zkontrolovat nastavení zařízení na portálu Azure AD.

![Připojeno](./media/device-management-azuread-joined-devices-setup/15.png)

Další informace najdete v tématu [vyhledávání zařízení](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu: 

- [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)
- [Správa zařízení pomocí portálu Azure](device-management-azure-portal.md)
- 



