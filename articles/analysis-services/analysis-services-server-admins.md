---
title: Správa správců serveru v Azure Analysis Services | Microsoft Docs
description: Tento článek popisuje, jak spravovat Správce serverů pro Azure Analysis Services Server pomocí Azure Portal, PowerShellu nebo rozhraní REST API.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c876e27165eba30b17874eca600ba81be2e9354
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019426"
---
# <a name="manage-server-administrators"></a>Správa správců serveru

Správci serveru musí být platným uživatelem, instančním objektem nebo skupinou zabezpečení v Azure Active Directory (Azure AD) pro tenanta, ve kterém se server nachází. Správce serverů můžete spravovat pomocí **Analysis Services správci** serveru v Azure Portal, vlastností serveru v SSMS, PowerShellu nebo REST API. 

Při přidávání **skupiny zabezpečení**použijte `obj:groupid@tenantid` . Ve skupinách zabezpečení přidaných do role správce serveru nejsou podporované objekty služby.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Přidání správců serveru pomocí Azure Portal

1. Na portálu klikněte pro váš server na **Analysis Services správci**.
2. V ** \<servername> Analysis Services správci**klikněte na **Přidat**.
3. V nástroji **přidat správce serveru**vyberte v Azure AD uživatelské účty nebo Pozvěte externí uživatele podle e-mailové adresy.

    ![Správci serveru v Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Přidání správců serveru pomocí SSMS

1. Klikněte pravým tlačítkem na **vlastnosti**serveru >.
2. V **Analysis Server vlastnosti**klikněte na **zabezpečení**.
3. Klikněte na **Přidat**a potom zadejte e-mailovou adresu uživatele nebo skupiny ve službě Azure AD.
   
    ![Přidat správce serveru v SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K určení parametru správce při vytváření nového serveru použijte rutinu [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) . <br>
Pomocí rutiny [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) můžete změnit parametr správce existujícího serveru.

## <a name="rest-api"></a>REST API

K určení vlastnosti asAdministrator při vytváření nového serveru použijte [Create](/rest/api/analysisservices/servers/create) . <br>
K určení vlastnosti asAdministrator při úpravách stávajícího serveru použijte [Update](/rest/api/analysisservices/servers/update) . <br>



## <a name="next-steps"></a>Další kroky 

[Ověřování a uživatelská oprávnění](analysis-services-manage-users.md)  
[Správa databázových rolí a uživatelů](analysis-services-database-users.md)  
[Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)