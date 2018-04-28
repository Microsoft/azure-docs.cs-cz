---
title: Přidat hlavní název služby do role správce serveru Azure Analysis Services | Microsoft Docs
description: Zjistěte, jak přidat objektu služby automation k roli správce serveru
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1cc563cc13a9102dbdac7bd505b4dd844ff8247
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Přidat hlavní název služby do role správce serveru 

 K automatizaci úloh bezobslužné prostředí PowerShell, musí mít objekt služby **správce serveru** oprávnění na serveru služby Analysis Services, který je spravován. Tento článek popisuje, jak přidat hlavní název služby do role správce serveru na serveru Azure AS.

## <a name="before-you-begin"></a>Než začnete
Před dokončením tohoto úkolu, musí mít objekt služby registruje v Azure Active Directory.

[Vytvoření instančního objektu – portál Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Vytvořit objekt služby – prostředí PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li tuto úlohu dokončit, musíte mít [správce serveru](analysis-services-server-admins.md) oprávnění na serveru Azure AS. 

## <a name="add-service-principal-to-server-administrators-role"></a>Přidání objektu služby do role správce serveru

1. V aplikaci SSMS připojení k serveru Azure AS.
2. V **vlastnosti serveru** > **zabezpečení**, klikněte na tlačítko **přidat**.
3. V **vyberte uživatele nebo skupinu**, vyhledejte registrovaná aplikace podle názvu, vyberte a pak klikněte na tlačítko **přidat**.

    ![Hledání hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID hlavní účet služby a pak klikněte na tlačítko **OK**.
    
    ![Hledání hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Operace serveru pomocí rutin AzureRm, hlavní scheduler spuštěné služby musíte také zařadit do **vlastníka** role pro prostředek v [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell serveru SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


