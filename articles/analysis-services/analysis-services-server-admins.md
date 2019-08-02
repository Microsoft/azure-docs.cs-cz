---
title: Správa správců serveru v Azure Analysis Services | Microsoft Docs
description: Naučte se spravovat správce serveru pro Analysis Services Server v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: eb5248d374fa6212398ad652b4db1496833473ae
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696308"
---
# <a name="manage-server-administrators"></a>Správa správců serveru

Správci serveru musí být platný uživatel nebo skupina zabezpečení v Azure Active Directory (Azure AD) pro tenanta, ve kterém se server nachází. Správce serverů můžete spravovat pomocí **Analysis Services správci** serveru v Azure Portal, vlastností serveru v SSMS, PowerShellu nebo REST API. 

**Skupiny zabezpečení** musí být [povoleny poštou](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) s `MailEnabled` vlastností nastavenou na `True`. Při určování skupiny podle e-mailové `obj:groupid@tenantid`adresy použijte.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Přidání správců serveru pomocí Azure Portal

1. Na portálu klikněte pro váš server na **Analysis Services správci**.
2. **V\<> servername-Analysis Services Admins**klikněte na **Přidat**.
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
[Řízení přístupu na základě role](../role-based-access-control/overview.md)  

