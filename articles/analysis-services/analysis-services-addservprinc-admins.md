---
title: Přidat zásadu služby do role správce serveru Azure Analysis Services | Microsoft Docs
description: Zjistěte, jak přidat zásadě služby automation k roli správce serveru
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9c6417e069bbed38b1f6e9317636a10834ce7197
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Přidat zásadu služby do role správce serveru 

 K automatizaci úloh bezobslužné prostředí PowerShell, musí mít Princip služby **správce serveru** oprávnění na serveru služby Analysis Services, který je spravován. Tento článek popisuje postup přidání Princip služby do role správce serveru na serveru Azure AS.

## <a name="before-you-begin"></a>Než začnete
Před dokončením tohoto úkolu, musí mít Princip služby registruje v Azure Active Directory.

[Vytvořit zásadu služby - portálu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li tuto úlohu dokončit, musíte mít [správce serveru](analysis-services-server-admins.md) oprávnění na serveru Azure AS. 

## <a name="add-service-principle-to-server-administrators-role"></a>Přidat zásadu služby do role správce serveru

1. V aplikaci SSMS připojení k serveru Azure AS.
2. V **vlastnosti serveru** > **zabezpečení**, klikněte na tlačítko **přidat**.
3. V **vyberte uživatele nebo skupinu**, vyhledejte registrovaná aplikace podle názvu, vyberte a pak klikněte na tlačítko **přidat**.

    ![Vyhledejte účet Princip služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID Princip účtu služby a pak klikněte na tlačítko **OK**.
    
    ![Vyhledejte účet Princip služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Pro operace serveru pomocí rutin AzureRm systémem scheduler Princip služby musí patřit také do **vlastníka** role pro prostředek v [řízení řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell serveru SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


