---
title: Správa správců serveru v Azure Analysis Services | Microsoft Docs
description: Tento článek popisuje, jak spravovat Správce serverů pro Azure Analysis Services Server pomocí Azure Portal, PowerShellu nebo rozhraní REST API.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bc17d27837d5b96f06b5172fb019db873418db94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922944"
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

K určení parametru správce při vytváření nového serveru použijte rutinu [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) . <br>
Pomocí rutiny [set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) můžete změnit parametr správce existujícího serveru.

## <a name="rest-api"></a>REST API

K určení vlastnosti asAdministrator při vytváření nového serveru použijte [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) . <br>
K určení vlastnosti asAdministrator při úpravách stávajícího serveru použijte [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) . <br>



## <a name="next-steps"></a>Další kroky 

[Ověřování a uživatelská oprávnění](analysis-services-manage-users.md)  
[Správa databázových rolí a uživatelů](analysis-services-database-users.md)  
[Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)  
