---
title: Správa serveru admins ve službě Azure Analysis Services | Microsoft Docs
description: Naučte se spravovat správci serveru pro server služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: def09f2853f761f3fefca80f341e6cc0557bac86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-server-administrators"></a>Spravovat správce serveru
Správci serveru musí být platný uživatel nebo skupina ve službě Azure Active Directory (Azure AD) pro klienta, ve kterém se nachází server. Můžete použít **Analysis Services Admins** pro váš server v portálu Azure nebo vlastnosti serveru v aplikaci SSMS ke správě správci serveru. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Přidat správce serveru pomocí portálu Azure
1. V portálu pro váš server, klikněte na tlačítko **Analysis Services Admins**.
2. V  **\<servername >-Analysis Services Admins**, klikněte na tlačítko **přidat**.
3. V **přidat správci serveru**, vyberte uživatelské účty ze služby Azure AD a zvaní externí uživatele podle e-mailovou adresu.

    ![Správci serveru na portálu Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Přidat správce serveru pomocí aplikace SSMS
1. Pravým tlačítkem na server > **vlastnosti**.
2. V **vlastnosti serveru pro analýzu**, klikněte na tlačítko **zabezpečení**.
3. Klikněte na tlačítko **přidat**a potom zadejte e-mailovou adresu pro uživatele nebo skupiny ve službě Azure AD.
   
    ![Přidat správce serveru v aplikaci SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Další postup 
[Ověřování a uživatelská oprávnění](analysis-services-manage-users.md)  
[Spravovat role databáze a uživatele](analysis-services-database-users.md)  
[Řízení přístupu na základě role](../role-based-access-control/overview.md)  

