---
title: Přidání instančního objektu do role správce serveru Azure Analysis Services | Microsoft Docs
description: Naučte se přidat instanční objekt služby Automation do role správce serveru.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a8fbbdbd647aa3d733a26ceaf72629058ae3a274
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619268"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Přidání instančního objektu k roli správce serveru 

 Aby bylo možné automatizovat bezobslužné úlohy PowerShellu, instanční objekt musí mít oprávnění **Správce serveru** na spravovaném serveru Analysis Services. Tento článek popisuje, jak přidat instanční objekt do role Správci serveru na serveru Azure jako.

## <a name="before-you-begin"></a>Před zahájením
Před dokončením této úlohy musíte mít v Azure Active Directory zaregistrovaný instanční objekt.

[Vytvoření instančního objektu – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Požadovaná oprávnění
K dokončení této úlohy musíte mít oprávnění [Správce serveru](analysis-services-server-admins.md) na serveru Azure as. 

## <a name="add-service-principal-to-server-administrators-role"></a>Přidání instančního objektu k roli správců serveru

1. V SSMS se připojte k Azure AS serveru.
2. V nabídce**zabezpečení** **vlastností** > serveru klikněte na **Přidat**.
3. V nabídce **Vyberte uživatele nebo skupinu**vyhledejte registrovanou aplikaci podle názvu, vyberte a pak klikněte na **Přidat**.

    ![Hledat hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID účtu zabezpečení služby a pak klikněte na **OK**.
    
    ![Hledat hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> U operací se serverem, které používají rutiny Azure PowerShell, musí instanční objekt, který spouští Scheduler, patřit taky do role **vlastníka** pro prostředek v [Access Control na základě role (RBAC) založené na rolích Azure](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Související informace

* [Stáhnout SQL Server modul PowerShellu](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


