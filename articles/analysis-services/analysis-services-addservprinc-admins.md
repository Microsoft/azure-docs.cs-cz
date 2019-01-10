---
title: Přidání instančního objektu k roli správce serveru Azure Analysis Services | Dokumentace Microsoftu
description: Zjistěte, jak přidat do role správce serveru instanční objekt služby automation
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5089083862f243a6a6847a58ac0777c52f978e73
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189724"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Přidání instančního objektu k roli správce serveru 

 K automatizaci bezobslužné úlohy prostředí PowerShell, musí mít instanční objekt služby **správce serveru** oprávnění na serveru Analysis Services, který je spravován. Tento článek popisuje postup přidání hlavního názvu služby do role správce serveru na server služby Azure jako.

## <a name="before-you-begin"></a>Před zahájením
Před dokončením tohoto úkolu, musí mít instanční objekt služby v Azure Active Directory.

[Vytvoření instančního objektu – Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Požadovaná oprávnění
Tento úkol provést, musíte mít [správce serveru](analysis-services-server-admins.md) oprávnění na serveru jako Azure. 

## <a name="add-service-principal-to-server-administrators-role"></a>Přidání instančního objektu k roli správce serveru

1. V aplikaci SSMS připojení k serveru Azure AS.
2. V **vlastnosti serveru** > **zabezpečení**, klikněte na tlačítko **přidat**.
3. V **vyberte uživatele nebo skupiny**, vyhledejte registrované aplikace podle názvu, vyberte a klikněte na **přidat**.

    ![Vyhledejte účet instančního objektu](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID objektu zabezpečení účtu služby a potom klikněte na **OK**.
    
    ![Vyhledejte účet instančního objektu](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Pro operace serveru pomocí rutin AzureRm, spuštěný Plánovač instančního objektu služby musí také patřit do **vlastníka** role pro prostředek v [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Související informace

* [Stáhnout modul prostředí PowerShell pro Server SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


