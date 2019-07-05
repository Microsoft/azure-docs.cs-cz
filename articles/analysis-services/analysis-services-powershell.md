---
title: Správa služby Azure Analysis Services pomocí Powershellu | Dokumentace Microsoftu
description: Správa Azure Analysis Services pomocí Powershellu.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a958c33e173c881a3ad09a49fe9f71ddb0c9df56
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508942"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Správa služby Azure Analysis Services pomocí Powershellu

Tento článek popisuje rutiny prostředí PowerShell použít k serveru Azure Analysis Services a úlohy správy databází. 

Úlohy správy prostředků serveru, jako je vytvoření nebo odstranění serveru, pozastavení nebo obnovení operací serveru nebo změna úrovně služby (vrstvy) pomocí rutin služby Azure Analysis Services. Další úlohy správy databází, jako jsou přidávání nebo odebírání členů rolí, zpracování nebo dělení používají rutiny v modulu SqlServer jako SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Oprávnění

Většina úkolů prostředí PowerShell vyžaduje, že máte oprávnění správce na serveru Analysis Services, které spravujete. Naplánované úlohy prostředí PowerShell jsou Bezobslužná operace. Účet nebo instanční objekt s plánovači musí mít oprávnění správce na serveru Analysis Services. 

Pro operace serveru pomocí rutin prostředí Azure PowerShell, váš účet nebo účet, který spouští Plánovač musí také patřit do role vlastníka prostředku v [Azure Role-Based řízení přístupu (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operace prostředků a server 

Install module - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Dokumentace ke službě – [Az.AnalysisServices odkaz](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databázové operace

Operace databáze Azure Analysis Services pomocí stejného modulu systému SQL Server jako SQL Server Analysis Services. Ne všechny rutiny jsou však podporovány pro Azure Analysis Services. 

V modulu SqlServer poskytuje rutiny pro správu databáze specifické pro úlohy, jakož i pro obecné účely rutinu Invoke-ASCmd, který přijímá dotazu tabulkový Model skriptování jazyk TMSL () nebo skript. Pro Azure Analysis Services se podporují následující rutiny v modulu SqlServer.

Instalace modulu - [systému SQL Server](https://www.powershellgallery.com/packages/SqlServer)   
Dokumentace ke službě – [referenční dokumentace systému SQL Server](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Podporované rutiny

|Rutina|Popis|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Přidáte člena do role databáze.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Zálohujte databázi služby Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Odebrání člena z role databáze.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Zpracujte databázi.|  
|[Vyvolání ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Zpracování oddílu.| 
|[Vyvolání ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Zpracovat tabulku.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Sloučit oddíl.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Obnovení databáze služby Analysis Services.| 
  

## <a name="related-information"></a>Související informace

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Stáhnout modul prostředí PowerShell pro Server SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stažení aplikace SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modul systému SQL Server v galerii prostředí PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabulkový Model programování pro 1200 úroveň kompatibility a vyšší](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
