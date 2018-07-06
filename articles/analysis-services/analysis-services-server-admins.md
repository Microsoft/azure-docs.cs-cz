---
title: Správa správců serveru ve službě Azure Analysis Services | Dokumentace Microsoftu
description: Další informace o správě správce serveru pro server služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866963"
---
# <a name="manage-server-administrators"></a>Správa správců serveru
Správci serverů musí být platný uživatel nebo skupina zabezpečení v Azure Active Directory (Azure AD) pro tenanta, ve kterém se nachází server. Můžete použít **správci Analysis Services** pro váš server webu Azure portal nebo vlastnosti serveru v aplikaci SSMS ke správě správce serveru. 

> [!NOTE]
> Skupiny zabezpečení musí mít `MailEnabled` nastavenou na `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Chcete-li přidat správce serveru pomocí webu Azure portal
1. Na portálu pro váš server, klikněte na tlačítko **správci Analysis Services**.
2. V  **\<servername >-správci Analysis Services**, klikněte na tlačítko **přidat**.
3. V **Správce serverů přidejte**, vyberte uživatelských účtů ze služby Azure AD nebo pozvání externího uživatele podle e-mailovou adresu.

    ![Správce serveru na webu Azure portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Chcete-li přidat správce serveru pomocí SSMS
1. Klikněte pravým tlačítkem na server > **vlastnosti**.
2. V **vlastnosti serveru Analysis Server**, klikněte na tlačítko **zabezpečení**.
3. Klikněte na tlačítko **přidat**a pak zadejte e-mailovou adresu pro uživatele nebo skupinu ve službě Azure AD.
   
    ![Přidat správce serveru v aplikaci SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Další postup 
[Ověřování a uživatelská oprávnění](analysis-services-manage-users.md)  
[Spravovat databázové role a uživatele](analysis-services-database-users.md)  
[Řízení přístupu na základě role](../role-based-access-control/overview.md)  

