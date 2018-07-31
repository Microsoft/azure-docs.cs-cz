---
title: Nastavení Azure Active Directory zařízení připojených k | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu Azure Active Directory připojené zařízení.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 511580b95646677a231a68862cae8a6aa57abcb0
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345527"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Nastavení Azure Active Directory zařízení připojených k

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů vašich prostředků. Další informace najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md).

Pokud chcete převést vlastnictví pracovní zařízení s Windows 10 pod kontrolu služby Azure AD, můžete to provést konfigurací zařízení připojených k Azure AD. Toto téma poskytuje související kroky. 


## <a name="prerequisites"></a>Požadavky

Připojit zařízení s Windows 10, musíte nakonfigurovat službu device registration service povolit registraci zařízení. Kromě s oprávněními pro připojení k zařízení ve vašem tenantovi Azure AD, musí mít méně zařízení registrovaná než nakonfigurované maximum. Další informace najdete v tématu [nastavení zařízení](../device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Co byste měli vědět


- Windows připojí zařízení v adresáři organizace ve službě Azure AD.

- Možná budete muset projít výzvu ověřování službou Multi-Factor Authentication. Tento problém je možné konfigurovat vaším správcem IT.

- Azure AD ověří, jestli je zařízení vyžaduje registraci správy mobilních zařízení a zaregistruje ho, pokud je k dispozici.

- Pokud se spravovaný uživatel Windows vás provede na plochu automatické přihlašování.

- Pokud jste federovaného uživatele, budete muset přihlásit pomocí svých přihlašovacích údajů.

- Pokud jsou federované, zprostředkovatele Identity musí podporovat WS-Fed a WS-Trust koncového bodu uživatelského jména a hesla. To může být verze 1.3 nebo 2005. Tato podpora protokolu je potřeba připojit zařízení k Azure AD i Přihlaste se na zařízení s heslem. 




## <a name="joining-a-device"></a>Připojení zařízení

Tato část obsahuje kroky pro připojení zařízení s Windows 10 do služby Azure AD. Úspěšně připojil zařízení se zobrazí jako **připojené k \<služby Azure AD\>**.

![Připojeno](./media/device-management-azuread-joined-devices-setup/13.png)


**Připojit zařízení s Windows 10:**

1. V **Start** nabídky, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/device-management-azuread-joined-devices-setup/01.png)

2. Klikněte na tlačítko **účty**.

    ![Účty](./media/device-management-azuread-joined-devices-setup/02.png)


3. Klikněte na tlačítko **přístup do práce nebo do školy**.

    ![Přístup do práce nebo do školy](./media/device-management-azuread-joined-devices-setup/03.png)

4. Na **přístup do práce nebo do školy** dialogového okna, klikněte na tlačítko **připojit**.

    ![Připojení](./media/device-management-azuread-joined-devices-setup/04.png)


5. Na **nastavit pracovní nebo školní účet** dialogového okna, klikněte na tlačítko **připojit toto zařízení k Azure Active Directory**.

    ![Připojení](./media/device-management-azuread-joined-devices-setup/08.png)


6. Na **Pojďme vás přihlásit** dialogové okno, zadejte název vašeho účtu (například someone@example.com) a potom klikněte na tlačítko **Další**.

    ![Pojďme vás přihlásit](./media/device-management-azuread-joined-devices-setup/10.png)


6. Na **zadejte heslo** dialogové okno, zadejte své heslo a potom klikněte na tlačítko **přihlášení**.

    ![Zadejte heslo.](./media/device-management-azuread-joined-devices-setup/05.png)


7. Na **Ujistěte se, že toto je vaše organizace** dialogového okna, klikněte na tlačítko **připojit**.

    ![Ujistěte se, že toto je vaše organizace](./media/device-management-azuread-joined-devices-setup/11.png)


8. Na **všechno máte nastavené** dialogového okna, klikněte na tlačítko **provádí**.

    ![Nyní je vše připraveno](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Ověření

Chcete-li ověřit, jestli se zařízení připojí ke službě Azure AD, můžete zkontrolovat **přístup do práce nebo do školy** dialogového okna na vašem zařízení.

![Připojeno](./media/device-management-azuread-joined-devices-setup/13.png)

Alternativně můžete spustit následující příkaz: `dsregcmd /status`  
Na úspěšně připojil zařízení **AzureAdJoined** je **Ano**.

![Připojeno](./media/device-management-azuread-joined-devices-setup/14.png)

Můžete také zkontrolovat nastavení zařízení na portálu Azure AD.

![Připojeno](./media/device-management-azuread-joined-devices-setup/15.png)

Další informace najdete v tématu [vyhledání zařízení](../device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu: 

- [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md)
- [Správa zařízení pomocí webu Azure portal](../device-management-azure-portal.md)
- 



