---
title: Nastavení Azure Active Directory zaregistrované zařízení | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu Azure Active Directory zaregistrované zařízení.
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
ms.openlocfilehash: bf763b08f234145cb81d2cded170348f96e5e478
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343337"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Nastavení Azure Active Directory zaregistrované zařízení s Windows 10

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů vašich prostředků. Další informace najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md).

Pokud chcete povolit **přineste si vlastní zařízení (BYOD)** scénáři, můžete to provést konfigurací zařízení registrováno v Azure AD. Ve službě Azure AD můžete nakonfigurovat zařízení registrováno v Azure AD pro Windows 10, iOS, Android a macOS. Tento článek poskytuje související postup pro zařízení s Windows 10. 


## <a name="before-you-begin"></a>Než začnete

K registraci zařízení s Windows 10, musí nakonfigurovat službu device registration service umožňuje registraci zařízení. Kromě toho musí mít méně zařízení registrovaná než nakonfigurované maximum. Další informace najdete v tématu [nastavení zařízení](../device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Co byste měli vědět

Při registraci zařízení, které by měl mějte následující:

- Windows ve službě Azure AD se zařízení registruje v adresáři organizace.

- Možná budete muset projít výzvu ověřování službou Multi-Factor Authentication. Tento problém je možné konfigurovat vaším správcem IT.

- Azure AD ověří, jestli je zařízení vyžaduje registraci správy mobilních zařízení a zaregistruje ho, pokud je k dispozici.

- Pokud se spravovaný uživatel Windows vás provede na plochu automatické přihlašování.

- Pokud jste federovaného uživatele, budete přesměrováni na přihlašovací obrazovce Windows k zadání přihlašovacích údajů.


## <a name="registering-a-device"></a>Registrace zařízení

Tato část obsahuje kroky pro registraci zařízení s Windows 10 do služby Azure AD. Úspěšně registrovaná zařízení se zobrazí s **pracovní nebo školní účet** položka.

![Registrace](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Postup pro registraci zařízení s Windows 10:**

1. V **Start** nabídky, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Klikněte na tlačítko **účty**.

    ![Účty](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Klikněte na tlačítko **přístup do práce nebo do školy**.

    ![Přístup do práce nebo do školy](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Na **přístup do práce nebo do školy** dialogového okna, klikněte na tlačítko **připojit**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Na **nastavit pracovní nebo školní účet** dialogové okno, zadejte název vašeho účtu (například someone@example.com) a potom klikněte na tlačítko **Další**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Na **zadejte heslo** dialogové okno, zadejte své heslo a potom klikněte na tlačítko **Další**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Na **všechno máte nastavené** dialogového okna, klikněte na tlačítko **provádí**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Ověření

Chcete-li ověřit, jestli se zařízení připojí ke službě Azure AD, můžete zkontrolovat **přístup do práce nebo do školy** dialogového okna na vašem zařízení.

![Registrace](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Alternativně můžete zkontrolovat nastavení zařízení na portálu Azure AD.

![Registrace](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Další postup

- Další informace najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md)

- Další informace o správě zařízení na portálu Azure AD, najdete v článku [Správa zařízení pomocí webu Azure portal ](../device-management-azure-portal.md).




