---
title: Správa správců serveru ve službě Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak spravovat správce serveru pro server Azure Analysis Services pomocí azure portálu, PowerShellu nebo REST API.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572612"
---
# <a name="manage-server-administrators"></a>Správa správců serveru

Správci serveru musí být platným uživatelem nebo skupinou zabezpečení ve službě Azure Active Directory (Azure AD) pro klienta, ve kterém je server umístěn. **Správci Analysis Services** pro váš server můžete použít na webu Azure Portal, Vlastnosti serveru v Rozhraní SSMS, PowerShellu nebo ROZHRANÍ REST API ke správě správců serveru. 

**Skupiny zabezpečení** musí mít `MailEnabled` [povolenou poštu](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) s vlastností nastavenou na `True`. Při zadávání skupiny podle `obj:groupid@tenantid`e-mailové adresy použijte .

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Přidání správců serveru pomocí portálu Azure Portal

1. Na portálu klikněte na server na **položku Analysis Services Admins**.
2. V ** \<> název_serveru – Správci služby Analysis Services**klepněte na tlačítko **Přidat**.
3. V **části Přidat správce serveru**vyberte uživatelské účty ze služby Azure AD nebo pozvěte externí uživatele podle e-mailové adresy.

    ![Správci serveru na webu Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Přidání správců serveru pomocí služby SSMS

1. Klepněte pravým tlačítkem myši na server > **vlastnosti**.
2. V **okně Vlastnosti serveru Analysis Server**klepněte na **položku Zabezpečení**.
3. Klikněte na **Přidat**a zadejte e-mailovou adresu uživatele nebo skupiny ve vašem Azure AD.
   
    ![Přidání správců serveru do ssms](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pomocí rutiny [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) určete parametr Administrator při vytváření nového serveru. <br>
Pomocí rutiny [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) upravte parametr Administrator pro existující server.

## <a name="rest-api"></a>REST API

Při [Create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) vytváření nového serveru můžete zadat vlastnost asAdministrator. <br>
Při úpravách existujícího serveru můžete použít [funkci Aktualizovat](https://docs.microsoft.com/rest/api/analysisservices/servers/update) k určení vlastnosti asAdministrator. <br>



## <a name="next-steps"></a>Další kroky 

[Ověřování a uživatelská oprávnění](analysis-services-manage-users.md)  
[Správa databázových rolí a uživatelů](analysis-services-database-users.md)  
[Řízení přístupu na základě rolí](../role-based-access-control/overview.md)  

