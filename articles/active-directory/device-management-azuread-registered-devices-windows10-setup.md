---
title: Nastavení Azure Active Directory zaregistrované zařízení | Microsoft Docs
description: Zjistěte, jak nastavit zařízení s Azure Active Directory zaregistrován.
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
ms.openlocfilehash: 541f37bb11c604567b356716db282d0e4e2f629b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713520"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Nastavení Azure Active Directory zaregistrované zařízení se systémem Windows 10

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují k prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další informace najdete v tématu [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md).

Pokud chcete povolit **PŘINESTE si vlastní zařízení ()** scénáři, můžete to provést pomocí konfigurace Azure AD, které registrované zařízení. Ve službě Azure AD můžete nakonfigurovat zařízení zaregistrovat Azure AD pro Windows 10, iOS, Android a systému macOS. Tento článek poskytuje související kroky pro zařízení s Windows 10. 


## <a name="before-you-begin"></a>Než začnete

Registrovat zařízení Windows 10, je třeba službu device registration service vám umožní zaregistrovat zařízení. Kromě toho musí mít méně zařízení registrovaná než nakonfigurované maximum. Další informace najdete v tématu [nakonfigurovat nastavení zařízení](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Důležité informace

Při registraci zařízení, byste měli mít na paměti následující:

- Windows se zařízení registruje do adresáře organizace v Azure AD

- Bude pravděpodobně nutné projít výzvu vícefaktorového ověřování. Tento problém je možné konfigurovat váš správce IT.

- Azure AD ověří, zda zařízení vyžaduje registraci správy mobilních zařízení a zaregistruje ho, pokud je k dispozici.

- Pokud jste spravované uživatel, Windows přejdete na ploše prostřednictvím automatického přihlášení.

- Pokud se Federovaný uživatel, můžete k zadání pověření přesměrováni na obrazovce přihlášení systému Windows.


## <a name="registering-a-device"></a>Registrace zařízení

Tato část vám poskytne kroky pro registraci zařízení s Windows 10 do služby Azure AD. Úspěšně registrovaná zařízení se zobrazí s **pracovní nebo školní účet** položku.

![Registrace](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Postup registrace zařízení s Windows 10:**

1. V **spustit** nabídky, klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Klikněte na tlačítko **účty**.

    ![Účty](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Klikněte na tlačítko **přístup k práci nebo ve škole**.

    ![Přístup k práci nebo ve škole](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Na **přístup k práci nebo ve škole** dialogové okno, klikněte na tlačítko **Connect**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Na **nastavit pracovní nebo školní účet** dialogové okno, zadejte název účtu (například someone@example.com) a potom klikněte na **Další**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Na **zadejte heslo** dialogové okno, zadejte své heslo a pak klikněte na tlačítko **Další**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Na **máte všechno nastavené** dialogové okno, klikněte na tlačítko **provádí**.

    ![Připojení](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Ověření

Pokud chcete ověřit, jestli se zařízení připojí ke službě Azure AD, můžete zkontrolovat **přístup k práci nebo ve škole** dialogové okno na vašem zařízení.

![Registrace](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Alternativně můžete zkontrolovat nastavení zařízení na portálu Azure AD.

![Registrace](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Další postup

- Další informace najdete v tématu [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md)

- Další informace o správě zařízení na portálu Azure AD najdete v tématu [Správa zařízení pomocí webu Azure portal ](device-management-azure-portal.md).




