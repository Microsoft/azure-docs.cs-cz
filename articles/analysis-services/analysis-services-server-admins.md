---
title: Správa serveru admins ve službě Azure Analysis Services | Microsoft Docs
description: Naučte se spravovat správci serveru pro server služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec1630f4de70f77c13e335c68aff16180e524c12
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307804"
---
# <a name="manage-server-administrators"></a>Spravovat správce serveru
Správci serveru musí být platný uživatel nebo novou skupinu zabezpečení ve službě Azure Active Directory (Azure AD) pro klienta, ve kterém se nachází server. Můžete použít **Analysis Services Admins** pro váš server v portálu Azure nebo vlastnosti serveru v aplikaci SSMS ke správě správci serveru. 

> [!NOTE]
> Skupiny zabezpečení musí mít `MailEnabled` vlastnost nastavena na hodnotu `True`.

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

