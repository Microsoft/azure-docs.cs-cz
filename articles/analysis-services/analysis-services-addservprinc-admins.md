---
title: "Přidat zásadu služby do role správce serveru Azure Analysis Services | Microsoft Docs"
description: "Zjistěte, jak přidat zásadě služby automation k roli správce serveru"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 8e51b80e184b2b1ff24b1051b55088fbc54c271c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Přidat zásadu služby do role správce serveru 

 K automatizaci úloh bezobslužné prostředí PowerShell, musí mít Princip služby **správce serveru** oprávnění na serveru služby Analysis Services, který je spravován. Tento článek popisuje postup přidání Princip služby do role správce serveru na serveru Azure AS.

## <a name="before-you-begin"></a>Než začnete
Před dokončením tohoto úkolu, musí mít Princip služby registruje v Azure Active Directory.

[Vytvořit zásadu služby - portálu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Vytvořit zásadu služby – prostředí PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

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
> Pro operace serveru pomocí rutin AzureRm systémem scheduler Princip služby musí patřit také do **vlastníka** role pro prostředek v [řízení řízení přístupu (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell serveru SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


